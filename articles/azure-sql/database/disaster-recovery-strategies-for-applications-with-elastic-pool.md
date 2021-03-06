---
title: Olağanüstü durum kurtarma çözümlerini tasarlama
description: Doğru yük devretme modelini seçerek, olağanüstü durum kurtarma için bulut çözümünüzü nasıl tasarlayacağınızı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0463d11466859c0f30901a0afd960bdc7b2599a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91357799"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Azure SQL veritabanı elastik havuzlarını kullanan uygulamalar için olağanüstü durum kurtarma stratejileri
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL veritabanı, çok sayıda olay oluşması durumunda uygulamanızın iş sürekliliği için sağlamak üzere çeşitli yetenekler sağlar. [Elastik havuzlar](elastic-pool-overview.md) ve tek veritabanları aynı türden olağanüstü durum kurtarma (Dr) yeteneklerini destekler. Bu makalede, bu Azure SQL veritabanı iş sürekliliği özelliklerinden yararlanan esnek havuzlar için çeşitli DR stratejileri açıklanmaktadır.

Bu makalede aşağıdaki kurallı SaaS ISV uygulama deseninin kullanımı verilmiştir:

Modern bulut tabanlı bir Web uygulaması, her son kullanıcı için bir veritabanı sağlar. ISV 'nin birçok müşterisi vardır ve bu nedenle kiracı veritabanları olarak bilinen birçok veritabanını kullanır. Kiracı veritabanları genellikle öngörülemeyen etkinlik desenleri içerdiğinden, ISV, veritabanı maliyetinin uzun süre boyunca tahmin edilebilir olmasını sağlamak için elastik bir havuz kullanır. Elastik havuz, Kullanıcı etkinliği ani artışlar durumunda performans yönetimini de basitleştirir. Kiracı veritabanlarına ek olarak, uygulama kullanıcı profillerini yönetmek, güvenlik, kullanım düzenlerini toplamak vb. için de çeşitli veritabanları kullanır. Tek tek kiracıların kullanılabilirliği, uygulamanın kullanılabilirliğini tümüyle etkilemez. Ancak, yönetim veritabanlarının kullanılabilirliği ve performansı, uygulamanın işlevi için kritik öneme sahiptir ve yönetim veritabanları çevrimdışı ise uygulamanın tamamı çevrimdışı olur.

Bu makalede, maliyet duyarlı başlangıç uygulamalarından sıkı kullanılabilirlik gereksinimlerine sahip olanlara yönelik bir dizi senaryoyu kapsayan DR stratejileri ele alınmaktadır.

> [!NOTE]
> Premium veya İş Açısından Kritik veritabanları ve elastik havuzlar kullanıyorsanız, bunları bölge yedekli dağıtım yapılandırmasına dönüştürerek bölge kesintilerine dayanıklı hale getirebilirsiniz. Bkz. bölgesel olarak [yedekli veritabanları](high-availability-sla.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Senaryo 1. Maliyet duyarlı başlangıç

Ben bir başlangıç işim ve son derece maliyetlidir.  Uygulamanın dağıtımını ve yönetimini basitleştirmek istiyorum ve bireysel müşteriler için sınırlı bir SLA 'Sı olabilir. Ancak uygulamanın tamamı hiçbir şekilde çevrimdışı olduğundan emin olmak istiyorum.

Basitlik gereksinimini karşılamak için, tüm kiracı veritabanlarını tercih ettiğiniz Azure bölgesinde tek bir elastik havuzda dağıtın ve yönetim veritabanlarını coğrafi olarak çoğaltılan tek veritabanları olarak dağıtın. Kiracıların olağanüstü durum kurtarması için, ek bir ücret ödemeden gelen coğrafi geri yükleme ' yi kullanın. Yönetim veritabanlarının kullanılabilirliğini sağlamak için, bir otomatik yük devretme grubu (1. adım) kullanarak onları başka bir bölgeye çoğaltın. Bu senaryoda olağanüstü durum kurtarma yapılandırması 'nın devam eden maliyeti, ikincil veritabanlarının toplam maliyetine eşittir. Bu yapılandırma bir sonraki diyagramda gösterilmiştir.

![Şekil 1](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Birincil bölgede bir kesinti oluşursa, uygulamanızı çevrimiçi duruma getirmeye yönelik kurtarma adımları sonraki diyagram tarafından gösterilmiştir.

* Yük devretme grubu, yönetim veritabanının DR bölgesine otomatik olarak yük devretmesini başlatır. Uygulama yeni birincil hesaba otomatik olarak yeniden bağlanır ve tüm yeni hesaplar ve kiracı veritabanları DR bölgesinde oluşturulur. Mevcut müşteriler, verilerini geçici olarak kullanılamayan şekilde görür.
* Özgün havuz ile aynı yapılandırmaya sahip elastik havuzu oluşturun (2).
* Kiracı veritabanlarının kopyalarını oluşturmak için coğrafi geri yükleme kullanın (3). Son Kullanıcı bağlantılarında tek tek geri yüklemeler tetiklemeyi veya uygulamaya özgü başka bir öncelik düzeni kullanmayı düşünebilirsiniz.

Bu noktada, uygulamanız DR bölgesinde yeniden çevrimiçi olur, ancak bazı müşteriler verilerine erişirken gecikme yaşar.

![Şekil 2](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Kesinti geçici ise, tüm veritabanı geri yüklemeleri DR bölgesinde tamamlanmadan önce birincil bölgenin Azure tarafından kurtarılması mümkündür. Bu durumda, uygulamayı birincil bölgeye geri taşımayı düzenleyin. İşlem, sonraki diyagramda gösterilen adımları alır.

* Tüm bekleyen coğrafi geri yükleme isteklerini iptal edin.
* Yönetim veritabanlarının yükünü birincil bölgeye devreder (5). Bölgenin kurtarmasından sonra, eski alt öğeler otomatik olarak ikincil haline gelir. Şimdi rolleri yeniden Anahtarlarlar.
* Uygulamanın bağlantı dizesini birincil bölgeye geri işaret etmek üzere değiştirin. Artık tüm yeni hesaplar ve kiracı veritabanları birincil bölgede oluşturulur. Mevcut bazı müşteriler, verilerini geçici olarak kullanılamayan şekilde görür.
* Dr bölgesinde (6) değiştirilemediğinden emin olmak için DR havuzundaki tüm veritabanlarını salt okunurdur.
* Kurtarmadan bu yana değiştirilen DR havuzundaki her bir veritabanı için birincil havuzdaki (7) karşılık gelen veritabanlarını yeniden adlandırın veya silin.
* Güncelleştirilmiş veritabanlarını DR havuzundan Birincil havuza (8) kopyalayın.
* DR havuzunu silme (9)

Bu noktada, uygulamanız birincil bölgede, birincil havuzda bulunan tüm kiracı veritabanları ile çevrimiçi olur.

![Şekil 3](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Avantaj

Bu stratejinin en önemli avantajı, veri katmanı yedekliliği için düşük maliyetlidir. Azure SQL veritabanı, hiçbir ek ücret ödemeden uygulama yeniden yazma olmadan veritabanlarını otomatik olarak yedekler. Maliyet yalnızca elastik veritabanları geri yüklendiğinde tahakkuk edilir. 

### <a name="trade-off"></a>Ticaret-kapalı

Tüm kiracı veritabanlarının tam kurtarmasının önemli bir süre sürme ticareti. Süre uzunluğu, DR bölgesinde ve kiracı veritabanlarının genel boyutunda başlattığınız toplam geri yükleme sayısına bağlıdır. Bazı kiracıların bazı geri yüklemelerini önceliklendirseniz bile, hizmet ile aynı bölgede başlatılan diğer tüm geri yüklemeler ile rekabet altına alınır ve mevcut müşterilerin veritabanlarına ilişkin genel etkiyi en aza indirmenize olanak tanır. Buna ek olarak,, DR bölgesindeki yeni elastik havuz oluşturuluncaya kadar kiracı veritabanlarının kurtarılması başlayamaz.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Senaryo 2. Katmanlı hizmet ile yetişkinlere yönelik uygulama

Katmanlı hizmet teklifleri ve deneme müşterileri için farklı SLA 'Lar ve müşterilere ödeme yapmak için çok oldım bir SaaS uygulamasıdır. Deneme müşterileri için maliyeti olabildiğince fazla azaltmalıyım. Deneme müşterileri kapalı kalma süresi alabilir, ancak olasılığını azaltmak istiyorum. Ödeyen müşteriler için kapalı kalma süresi bir uçuş riskidir. Bu nedenle, ödeyen müşterilerin verilerine her zaman erişebildiğinizden emin olmak istiyorum.

Bu senaryoyu desteklemek için, deneme kiracıları ayrı elastik havuzlara yerleştirerek ücretli kiracılardan ayırın. Deneme müşterilerinin, kiracı başına daha düşük eDTU veya sanal çekirdek ve daha uzun bir kurtarma süresi ile daha düşük SLA 'Sı vardır. Ödeme müşterileri, kiracı başına daha yüksek eDTU veya sanal çekirdekler ve daha yüksek bir SLA olan havuzlardır. En düşük kurtarma süresini güvence altına almak için, ödeyen müşterilerin kiracı veritabanları coğrafi olarak çoğaltılır. Bu yapılandırma bir sonraki diyagramda gösterilmiştir.

![Diyagram, Yönetim veritabanı ve ücretli müşteriler birincil havuzu ve deneme müşterileri havuzu için çoğaltma olmadan ikincil havuz arasında coğrafi çoğaltma uygulayan bir birincil bölge ve D R bölgesi gösterir.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

İlk senaryoda olduğu gibi, yönetim veritabanları oldukça etkindir, bu sayede tek bir coğrafi çoğaltılan veritabanı (1) kullanılır. Bu, yeni müşteri abonelikleri, profil güncelleştirmeleri ve diğer yönetim işlemleri için öngörülebilir performansı sağlar. Yönetim veritabanlarının temel aldığı bölge, birincil bölgedir ve yönetim veritabanlarının ikincilerin bulunduğu bölge, DR bölgesidir.

Ödeme müşterilerinin kiracı veritabanlarında, birincil bölgede sağlanan "ücretli" havuzda etkin veritabanları vardır. DR bölgesinde aynı ada sahip bir ikincil havuz sağlayın. Her kiracı, ikincil havuza coğrafi olarak çoğaltılır (2). Bu, yük devretme kullanılarak tüm kiracı veritabanlarının hızlı kurtarılmasına izin verebilir.

Birincil bölgede bir kesinti oluşursa, uygulamanızı çevrimiçi duruma getirmek için kurtarma adımları sonraki diyagramda gösterilmektedir:

![Diyagram, birincil bölge için yönetim veritabanına yük devretme, ücretli müşteri ikincil havuzu ve deneme müşterileri için oluşturma ve geri yükleme ile ilgili bir kesinti gösterir.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Yönetim veritabanlarının yükünü hemen DR bölgesine (3) devreder.
* Uygulamanın bağlantı dizesini DR bölgesini işaret etmek üzere değiştirin. Artık tüm yeni hesaplar ve kiracı veritabanları DR bölgesinde oluşturulur. Mevcut deneme müşterilerine, verileri geçici olarak kullanım dışı görüntülenir.
* Kullanılabilirliğini hemen geri yüklemek için, ücretli kiracının veritabanlarını DR bölgesindeki havuza devreder (4). Yük devretme hızlı bir meta veri düzeyi değişikliği olduğundan, tek tek yük devretmeler, Son Kullanıcı bağlantılarına göre isteğe bağlı olarak tetiklendikleri bir iyileştirmesi göz önünde bulundurun.
* İkincil havuzlarınızın eDTU boyutu veya vCore değeri birincilken değişiklik günlüklerini işlemek için kapasiteyi gerektirdiğinden, tüm kiracıların (5) tam iş yüküne uyum sağlamak için artık havuz kapasitesini hemen artırın.
* Deneme müşterilerinin veritabanları (6) için DR bölgesindeki aynı ada ve aynı yapılandırmaya sahip yeni bir elastik havuz oluşturun.
* Deneme müşterilerinin havuzu oluşturulduktan sonra, tek tek deneme kiracı veritabanlarını yeni havuza (7) geri yüklemek için coğrafi geri yükleme ' yi kullanın. Son Kullanıcı bağlantıları tarafından tek tek geri yüklemeleri tetiklemeyi veya uygulamaya özgü diğer öncelik düzenlerini kullanmayı düşünün.

Bu noktada, uygulamanız DR bölgesinde yeniden çevrimiçi olur. Deneme müşterileri verilerine erişirken gecikme yaşlarken, tüm ödeyen müşterilerin verilerine erişimi vardır.

Uygulamayı DR bölgesindeki geri *yükledikten sonra* birincil bölge Azure tarafından kurtarılırken uygulamayı o bölgede çalıştırmaya devam edebilir veya birincil bölgeye yeniden bağlanma kararı verebilirsiniz. Yük devretme işlemi tamamlanmadan *önce* birincil bölge kurtarılırsa, hemen geri dönme işlemini göz önünde bulundurun. Yeniden çalışma, sonraki diyagramda gösterilen adımları alır:

![Diyagramda, birincil bölge geri yüklendikten sonra uygulanacak yeniden çalışma adımları gösterilmektedir.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Tüm bekleyen coğrafi geri yükleme isteklerini iptal edin.
* Yönetim veritabanlarının yükünü devreder (8). Bölgenin kurtarmasından sonra, eski birincil otomatik olarak ikincil haline gelir. Şimdi birincil olur.  
* Ücretli kiracı veritabanlarının yükünü devreder (9). Benzer şekilde, bölgenin kurtarmasından sonra, eski alt öğeler otomatik olarak ikincil haline gelir. Artık bu, bir veya daha fazla hale gelir.
* DR bölgesinde değiştirilen geri yüklenen deneme veritabanlarını salt okunurdur (10) olarak ayarlayın.
* Deneme müşterileri DR havuzundaki kurtarmadan bu yana değiştirilen her bir veritabanı için, deneme müşterileri birincil havuzunda (11) karşılık gelen veritabanını yeniden adlandırın veya silin.
* Güncelleştirilmiş veritabanlarını DR havuzundan Birincil havuza (12) kopyalayın.
* DR havuzunu (13) silin.

> [!NOTE]
> Yük devretme işlemi zaman uyumsuzdur. Kurtarma süresini en aza indirmek için, kiracı veritabanlarının yük devretme komutunu en az 20 veritabanı yığınlarında yürütmeniz önemlidir.

### <a name="benefit"></a>Avantaj

Bu stratejinin başlıca avantajı, ödeyen müşterileri için en yüksek SLA 'yı sunmamaktadır. Ayrıca, deneme DR havuzu oluşturulduktan hemen sonra yeni denemelerin engellenmesini güvence altına alır. 

### <a name="trade-off"></a>Ticaret-kapalı

Bu, kurulumun, kiracı veritabanlarının toplam maliyetini, ücretli müşterilere yönelik ikincil DR havuzunun maliyetine göre artırdığı bir denge vardır. Ayrıca, ikincil havuzun farklı boyutta olması halinde, DR bölgesindeki havuz yükseltmesi tamamlanana kadar, ödeyen müşteriler yük devretmeden sonra daha düşük performans yaşar.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Senaryo 3. Katmanlı hizmet ile coğrafi olarak dağıtılmış uygulama

Katmanlı hizmet tekliflerle yetişkinlere yönelik bir SaaS uygulaması kullanıyorum. Ücretli müşterilerimiz için çok ısrarlı bir SLA sunmak istiyorum ve kesintiler meydana geldiğinde etkisi riskini en aza indirmek istiyorum, hatta kısa süreli bir müşteri memnuniyetine neden olabilir. Ödeyen müşterilerin verilerine her zaman erişebilmesini çok önemlidir. Denemeler ücretsizdir ve deneme süresi boyunca bir SLA sunulmamaktadır.

Bu senaryoyu desteklemek için, üç ayrı elastik havuz kullanın. Ücretli müşterilerin kiracı veritabanlarını içerecek şekilde iki farklı bölgede yüksek eDTU 'lar veya veritabanı başına sanal çekirdekler içeren iki eşit boyut havuzu sağlayın. Deneme kiracılarını içeren üçüncü havuz, veritabanı başına daha düşük eDTU 'ları veya sanal çekirdekleri içerebilir ve iki bölgeden birinde sağlanabilir.

Kesintiler sırasında en düşük kurtarma süresini garantilemek için, ödeyen müşterilerin kiracı veritabanları, iki bölgenin her birinde birincil veritabanlarının %50 ' i ile coğrafi olarak çoğaltılır. Benzer şekilde, her bölge ikincil veritabanlarının %50 ' i içerir. Bu şekilde, bir bölge çevrimdışı ise, yalnızca ücretli müşterilerin veritabanlarının %50 ' inden etkilenmekte ve yük devri yapmanız gerekir. Diğer veritabanları değişmeden kalır. Bu yapılandırma aşağıdaki diyagramda gösterilmiştir:

![Diyagramda, A bölgesi adlı birincil bölge ve Yönetim veritabanı ile ücretli müşteriler birincil havuzu ile ikincil havuz ile deneme müşterileri havuzu için çoğaltma olmadan coğrafi çoğaltma uygulayan bölge B adlı ikincil bölge gösterilmektedir.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Önceki senaryolarda olduğu gibi, yönetim veritabanları oldukça etkin olduğundan, bunları tek coğrafi çoğaltılan veritabanları (1) olarak yapılandırın. Bu, yeni müşteri aboneliklerinin, profil güncelleştirmelerinin ve diğer yönetim işlemlerinin öngörülebilir performansını sağlar. A bölgesi yönetim veritabanlarının birincil bölgesidir ve B bölgesi yönetim veritabanlarını kurtarmak için kullanılır.

Ödeyerek ilgili müşterilerin kiracı veritabanları da coğrafi olarak çoğaltılır, ancak alt ve bölge B (2) arasında bölünen alt öğeler ve ikinciler ile ayrılır. Bu şekilde, kesinti tarafından etkilenen kiracı birincil veritabanları diğer bölgeye yük devreder ve kullanılabilir hale gelir. Kiracı veritabanlarının diğer yarısı de etkilenmiyor.

Sonraki diyagramda, A bölgesinde bir kesinti oluşursa gerçekleştirilecek kurtarma adımları gösterilmektedir.

![Diyagramda birincil bölge için bir kesinti, yönetim veritabanına yük devretme, ücretli müşteri ikincil havuzu ve deneme müşterileri için B bölgesi oluşturma ve geri yükleme işlemi gösterilir.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Yönetim veritabanlarının yükünü B bölgesine hemen devreder (3).
* Uygulamanın bağlantı dizesini B bölgesindeki yönetim veritabanlarına işaret etmek üzere değiştirin. yönetim veritabanlarını değiştirerek yeni hesapların ve kiracı veritabanlarının B bölgesinde oluşturulduğundan ve mevcut kiracı veritabanlarının da burada bulunduğundan emin olun. Mevcut deneme müşterilerine, verileri geçici olarak kullanım dışı görüntülenir.
* Kullanılabilirliği (4) hemen geri yüklemek için, ücretli kiracının veritabanlarının B bölgesindeki havuz 2 ' ye yük devreder. Yük devretme hızlı bir meta veri düzeyi değişikliği olduğundan, tek tek yük devretmeler, Son Kullanıcı bağlantılarına göre isteğe bağlı olarak tetiklendikleri bir iyileştirmesi göz önünde bulundurmayabilirsiniz.
* Artık havuz 2 yalnızca birincil veritabanlarını içerdiğinden, havuzdaki toplam iş yükü artar ve eDTU boyutunu (5) veya sanal çekirdek sayısını hemen artırabilir.
* Deneme müşterilerinin veritabanları (6) için B bölgesinde aynı ada ve aynı yapılandırmaya sahip yeni elastik havuz oluşturun.
* Havuz oluşturulduktan sonra tek bir deneme kiracı veritabanını havuza (7) geri yüklemek için coğrafi geri yükleme kullanın. Son Kullanıcı bağlantılarında tek tek geri yüklemeler tetiklemeyi veya uygulamaya özgü başka bir öncelik düzeni kullanmayı düşünebilirsiniz.

> [!NOTE]
> Yük devretme işlemi zaman uyumsuzdur. Kurtarma süresini en aza indirmek için, kiracı veritabanlarının yük devretme komutunu en az 20 veritabanı toplu olarak yürütmeniz önemlidir.

Bu noktada, uygulamanız B bölgesinde yeniden çevrimiçi olur. Deneme müşterileri verilerine erişirken gecikme yaşlarken, tüm ödeyen müşterilerin verilerine erişimi vardır.

A bölgesi kurtarılırken deneme müşterileri için B bölgesini kullanmak mı yoksa A bölgesindeki deneme müşterileri havuzunu kullanarak yeniden çalışma mi istediğinize karar vermeniz gerekir. Kurtarma işleminden sonra değiştirilen deneme kiracı veritabanlarının yüzdesi tek bir ölçütte olabilir. Bu karardan bağımsız olarak, iki havuz arasında ücretli kiracılar yeniden dengelenmesi gerekir. sonraki diyagramda, deneme kiracısı veritabanları A bölgesine geri döndüğünüzde işlem gösterilmektedir.  

![Diyagram A bölgesini geri yükledikten sonra uygulanacak yeniden çalışma adımlarını gösterir.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Deneme DR havuzuna tüm bekleyen coğrafi geri yükleme isteklerini iptal edin.
* Yönetim veritabanının yükünü devreder (8). Bölgenin kurtarmasından sonra, eski birincil otomatik olarak ikincil haline gelmiştir. Şimdi birincil olur.  
* Hangi ücretli kiracı veritabanlarının havuz 1 ' e geri dönmesini ve ikincil değerlerine (9) yük devretmeyi başlatmasını seçin. Bölgenin kurtarmasından sonra, havuz 1 ' deki tüm veritabanları otomatik olarak ikincil haline gelmiştir. Şimdi %50, daha fazla mali hale gelir.
* Havuz 2 ' nin boyutunu orijinal eDTU (10) veya Vçekirdekler sayısıyla küçültün.
* B bölgesindeki tüm geri yüklenen deneme veritabanlarını salt okunurdur (11) olarak ayarlayın.
* Kurtarmaya bu yana değiştirilen deneme DR havuzundaki her bir veritabanı için, deneme birincil havuzunda karşılık gelen veritabanını yeniden adlandırın veya silin (12).
* Güncelleştirilmiş veritabanlarını DR havuzundan Birincil havuza (13) kopyalayın.
* DR havuzunu (14) silin.

### <a name="benefit"></a>Avantaj

Bu stratejinin başlıca avantajları şunlardır:

* Bir kesinti, kiracı veritabanlarının %50 ' inden fazlasını etkilememesini sağladığından, ödeyen müşterilerinin en agresif SLA 'sını destekler.
* Kurtarma sırasında DR havuzu oluşturulduktan hemen sonra yeni denemeler engellenmesini güvence altına alır.
* Havuz kapasitesinin daha verimli bir şekilde kullanılmasını sağlar. havuz 1 ' deki ikincil veritabanlarının %50 ' i ve havuz 2 ' nin birincil veritabanlarından daha az etkin olduğu garanti edilir.

### <a name="trade-offs"></a>Dengelemeler

Ana denge:

* Yönetim veritabanlarına yönelik CRUD işlemlerinde, yönetim veritabanlarının birincil kısmında yürütüldüğü üzere B bölgesine bağlı son kullanıcılar için A bölgesine bağlı son kullanıcılar için gecikme süresi daha düşüktür.
* Yönetim veritabanının daha karmaşık tasarımını gerektirir. Örneğin, her kiracı kaydının yük devretme ve yeniden çalışma sırasında değiştirilmesi gereken bir konum etiketi vardır.  
* B bölgesindeki havuz yükseltmesi tamamlanana kadar, ödeyen müşteriler normalden düşük performansla karşılaşabilir.

## <a name="summary"></a>Özet

Bu makale SaaS ISV çok kiracılı bir uygulama tarafından kullanılan veritabanı katmanının olağanüstü durum kurtarma stratejilerine odaklanır. Seçtiğiniz strateji, iş modeli, müşterilerinize sunmak istediğiniz SLA, bütçe kısıtlaması vb. gibi uygulamanın ihtiyaçlarını temel alır. Açıklanan her strateji avantajları ve ticareti özetler ve bu sayede bilinçli bir karar elde edebilirsiniz. Ayrıca, özel uygulamanız büyük olasılıkla diğer Azure bileşenlerini içerir. Bu nedenle, iş sürekliliği kılavuzunu gözden geçirin ve veritabanı katmanının kurtarılmasına göre onları düzenleyin. Azure 'da veritabanı uygulamalarının kurtarılmasını yönetme hakkında daha fazla bilgi edinmek için bkz. [olağanüstü durum kurtarma için bulut çözümleri tasarlama](designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Sonraki adımlar

* Azure SQL veritabanı otomatik yedeklemeleri hakkında bilgi edinmek için bkz. [Azure SQL veritabanı otomatik yedeklemeleri](automated-backups-overview.md).
* İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Kurtarmaya yönelik otomatik yedeklemeleri kullanma hakkında bilgi edinmek için bkz. [hizmet tarafından başlatılan yedeklemelerden bir veritabanını geri yükleme](recovery-using-backups.md).
* Daha hızlı kurtarma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [etkin coğrafi çoğaltma](active-geo-replication-overview.md) ve [otomatik yük devretme grupları](auto-failover-group-overview.md).
* Arşivleme için otomatik yedeklemeleri kullanma hakkında bilgi edinmek için bkz. [veritabanı kopyası](database-copy.md).
