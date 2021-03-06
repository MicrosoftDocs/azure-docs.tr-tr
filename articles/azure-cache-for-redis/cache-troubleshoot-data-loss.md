---
title: Redis için Azure Cache'de veri kaybı sorunlarını giderme
description: Anahtarların kısmi kaybı, anahtar süre sonu veya anahtarların tam kaybı gibi Redsıs için Azure önbelleğiyle ilgili veri kaybı sorunlarını çözmeyi öğrenin.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6db036752bab7b84b72a37b148eaec7aa5765ef3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92538604"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Redis için Azure Cache'de veri kaybı sorunlarını giderme

Bu makalede Redsıs için Azure önbelleğinde oluşabilecek gerçek veya algılanan veri kayıplarının nasıl tanılanması anlatılmaktadır.

> [!NOTE]
> Bu kılavuzdaki sorun giderme adımlarından bazıları Redsıs komutları çalıştırma ve çeşitli performans ölçümlerini izleme yönergelerini içerir. Daha fazla bilgi ve yönergeler için [ek bilgi](#additional-information) bölümündeki makalelere bakın.
>

## <a name="partial-loss-of-keys"></a>Anahtarların kısmi kaybı

Redsıs için Azure önbelleği, bellekte depolandıktan sonra anahtarları rastgele olarak silmez. Ancak, süre sonu veya çıkarma ilkelerine ve açık anahtar silme komutlarına yanıt olarak anahtarları kaldırır. Premium veya standart Azure önbelleğindeki birincil düğüme, redin örneği için yazılmış anahtarlar Ayrıca bir çoğaltmada doğrudan kullanılabilir olmayabilir. Veriler, birincil kopyadan zaman uyumsuz ve engellenmeyen bir biçimde çoğaltılır.

Bu anahtarların önbelleğinizi kaybolduğunu fark ederseniz, aşağıdaki olası nedenleri kontrol edin:

| Nedeni | Description |
|---|---|
| [Anahtar süre sonu](#key-expiration) | Anahtarlar, üzerinde ayarlanan zaman aşımları nedeniyle kaldırılır. |
| [Anahtar çıkarma](#key-eviction) | Anahtarlar bellek baskısı altında kaldırılır. |
| [Anahtar silme](#key-deletion) | Anahtarlar açık silme komutları tarafından kaldırılır. |
| [Zaman uyumsuz çoğaltma](#async-replication) | Veri çoğaltma gecikmeleri nedeniyle anahtarlar çoğaltma üzerinde kullanılamaz. |

### <a name="key-expiration"></a>Anahtar süre sonu

Anahtar bir zaman aşımı atanmışsa ve bu süre geçtiğinde Redsıs için Azure önbelleği bir anahtarı otomatik olarak kaldırır. Redsıs anahtar süre sonu hakkında daha fazla bilgi için, [süre](https://redis.io/commands/expire) sonu komut belgelerine bakın. Ayrıca, zaman aşımı değerleri [set](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GetSet](https://redis.io/commands/getset)ve diğer **\* Mağaza** komutları kullanılarak ayarlanabilir.

Kaç anahtarın dolduğunu gösteren istatistikleri almak için, [Info](https://redis.io/commands/info) komutunu kullanın. `Stats`Bölümünde, süre dolma anahtarlarının toplam sayısı gösterilir. Bu `Keyspace` bölümde, zaman aşımları ve ortalama zaman aşımı değeri olan anahtarların sayısı hakkında daha fazla bilgi sağlanmaktadır.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Ayrıca, anahtarın eksik olduğu zaman arasında bir bağıntı olup olmadığını ve zaman aşımına uğradı anahtarların bir ani bir şekilde olduğunu görmek için önbelleğiniz için tanılama ölçümlerine bakabilirsiniz. Bu tür sorunların hatalarını ayıklamak için anahtar alanı bildirimleri veya **izleyicisini** kullanma hakkında bilgi için [redsıs anahtar alanı isabetsizliği hata ayıklamanın](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) ek başlığına bakın.

### <a name="key-eviction"></a>Anahtar çıkarma

Redin için Azure önbelleği, verileri depolamak için bellek alanı gerektirir. Gerektiğinde kullanılabilir belleği boşaltmak için anahtarları temizler. [Info](https://redis.io/commands/info) komutunda **used_memory** veya **used_memory_rss** değerleri yapılandırılmış **MaxMemory** ayarına yaklaşımında, redsıs için Azure önbelleği, [önbellek ilkesine](https://redis.io/topics/lru-cache)göre bellekten anahtar çıkarma işlemi başlatır.

Çıkarılan anahtarların sayısını [Info](https://redis.io/commands/info) komutunu kullanarak izleyebilirsiniz:

```
# Stats

evicted_keys:13224
```

Ayrıca, anahtarın eksik olduğu zaman arasında bir bağıntı olup olmadığını ve çıkarılan anahtarların bir ani bir şekilde olduğunu görmek için önbelleğiniz için tanılama ölçümlerine bakabilirsiniz. Bu tür sorunların hatalarını ayıklamak için anahtar alanı bildirimleri veya **izleyicisini** kullanma hakkında bilgi için [redsıs anahtar alanı isabetsizliği hata ayıklamanın](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) ek başlığına bakın.

### <a name="key-deletion"></a>Anahtar silme

Redsıs istemcileri Redsıs için Azure Cache 'ten anahtar açıkça kaldırmak üzere [del](https://redis.io/commands/del) veya [hdel](https://redis.io/commands/hdel) komutunu verebilir. [Info](https://redis.io/commands/info) komutunu kullanarak silme işlemlerinin sayısını izleyebilirsiniz. **Del** veya **hdel** komutları çağrılırsa, `Commandstats` bölümünde listelenecektir.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Zaman uyumsuz çoğaltma

Standart veya Premium katmanda Redsıs örneği için herhangi bir Azure önbelleği, birincil düğüm ve en az bir çoğaltma ile yapılandırılır. Veriler, bir arka plan işlemi kullanılarak, birincil bilgisayardan bir kopyaya zaman uyumsuz olarak kopyalanır. [Redis.io](https://redis.io/topics/replication) Web sitesi, redsıs veri çoğaltmasının genel olarak nasıl çalıştığını açıklar. İstemcilerin redde sık olarak yazacağı senaryolarda, bu çoğaltmanın anında olması garanti edilmediği için kısmi veri kaybı oluşabilir. Örneğin *, bir istemci kendisine bir anahtar* yazdığında, ancak arka plan işleminin bu anahtarı çoğaltmaya gönderme şansı *olmadan önce* , çoğaltma yeni birincil olarak geçtiğinde anahtar kaybedilir.

## <a name="major-or-complete-loss-of-keys"></a>Anahtarların büyük veya tamamen kaybolması

En fazla veya tüm anahtarlar önbelleğinizi kaybolduysa, aşağıdaki olası nedenleri kontrol edin:

| Nedeni | Description |
|---|---|
| [Anahtar Temizleme](#key-flushing) | Anahtarlar el ile temizlendi. |
| [Yanlış veritabanı seçimi](#incorrect-database-selection) | Redo için Azure önbelleği varsayılan olmayan bir veritabanı kullanacak şekilde ayarlanmıştır. |
| [Redsıs örneği hatası](#redis-instance-failure) | Redsıs sunucusu kullanılamıyor. |

### <a name="key-flushing"></a>Anahtar Temizleme

İstemciler, *tek* bir veritabanındaki tüm anahtarları kaldırmak Için [flushdb](https://redis.io/commands/flushdb) komutunu çağırabilir veya bir redsıs önbelleğindeki *tüm veritabanlarından tüm* anahtarları kaldırmak için [flushall](https://redis.io/commands/flushall) ' a çağrı yapabilir. Anahtarların temizlenmiş olup olmadığını öğrenmek için, [Info](https://redis.io/commands/info) komutunu kullanın. `Commandstats`Bölümünde, **Temizleme** komutunun adlandırılıp çağrılmayacağı gösterilmektedir:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Yanlış veritabanı seçimi

Redsıs için Azure önbelleği varsayılan olarak **DB0** veritabanını kullanır. Başka bir veritabanına geçiş yaparsanız (örneğin, **DB1**) ve bundan sonra anahtarları okumaya çalışırsanız, redin Için Azure önbelleği bunları orada bulamaz. Her veritabanı mantıksal olarak ayrı bir birimdir ve farklı bir veri kümesi tutar. Diğer kullanılabilir veritabanlarını kullanmak ve bunların her birinde anahtarları aramak için [Seç](https://redis.io/commands/select) komutunu kullanın.

### <a name="redis-instance-failure"></a>Redsıs örneği hatası

Redsıs, bellek içi veri deposudur. Veriler redo önbelleğini barındıran fiziksel veya sanal makinelerde tutulur. Temel katmandaki Redsıs örneği için bir Azure önbelleği yalnızca tek bir sanal makinede (VM) çalışır. Bu VM kapalıysa, önbellekte depoladığınız tüm veriler kaybolur. 

Standart ve Premium katmanlardaki önbellekler, çoğaltılan bir yapılandırmada iki VM kullanarak veri kaybına karşı daha fazla esneklik sunar. Bu tür bir önbellekteki birincil düğüm başarısız olduğunda, çoğaltma düğümü verileri otomatik olarak sunacak şekilde alır. Bu sanal makineler, aynı anda kullanılamaz duruma gelme olasılığını en aza indirmek için hatalar ve güncelleştirmeler için ayrı etki alanlarında bulunur. Ancak büyük bir veri merkezi kesintisi olursa VM 'Ler yine de devam edebilir. Bu nadir durumlarda verileriniz kaybedilir.

Bu altyapı hatalarıyla karşı verilerinizin korunmasını artırmak için [redsıs veri kalıcılığı](https://redis.io/topics/persistence) ve [coğrafi çoğaltma](./cache-how-to-geo-replication.md) kullanmayı göz önünde bulundurun.

## <a name="additional-information"></a>Ek bilgiler

- [Redis için Azure Cache sunucu tarafı sorunlarını giderme](cache-troubleshoot-server.md)
- [Doğru katmanı seçme](cache-overview.md#choosing-the-right-tier)
- [Redis için Azure Cache'i izleme](cache-how-to-monitor.md)
- [Redsıs komutlarını nasıl çalıştırabilirim?](cache-development-faq.md#how-can-i-run-redis-commands)