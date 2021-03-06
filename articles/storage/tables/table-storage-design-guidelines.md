---
title: Azure depolama tablo tasarımı için yönergeler | Microsoft Docs
description: Azure depolama tablo hizmetinizi, okuma ve yazma işlemlerini verimli bir şekilde destekleyecek şekilde tasarlama yönergelerini anlayın.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236327"
---
# <a name="guidelines-for-table-design"></a>Tablo tasarımı için yönergeler

Azure Depolama Tablo hizmeti ile kullanılmak üzere tabloları tasarlamak, ilişkisel bir veritabanı için tasarım açısından önemli bir farklılık vardır. Bu makalede, tablo hizmeti çözümünüzü daha etkili ve yazmaya yönelik olarak tasarlamaya yönelik yönergeler açıklanmaktadır.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Tablo hizmeti çözümünüzü, okuma açısından verimli olacak şekilde tasarlama

* ***Okuma ağır uygulamalarda sorgulama için tasarım.*** Tablolarınızı tasarlarken, varlıklarınızı nasıl güncelleşeceğinize ilişkin düşündüğünüzden önce kullanacağınız sorguları (özellikle gecikme süresine duyarlı olanları) düşünün. Bu, genellikle etkili ve performanslı bir çözüme neden olur.  
* ***Sorgularınızda hem PartitionKey hem de Rowkey belirtin.** _ _Point sorguları * bunlar gibi en verimli tablo hizmeti sorguları vardır.  
* ***Varlıkların yinelenen kopyalarını depolamayı göz önünde bulundurun.*** Tablo depolaması, daha verimli sorgular sağlamak için aynı varlığı birden çok kez depolamayı (farklı anahtarlarla) göz önünde bulundurun.  
* ***Verilerinizi kabul etmeyi düşünün.*** Tablo depolaması, veri izlemeyi düşünün. Örneğin, toplama verileri sorgularının yalnızca tek bir varlığa erişmesi için, Özet varlıkları depolayın.  
* ***Bileşik anahtar değerlerini kullanın.** _ Bu anahtarlar yalnızca _ *partitionkey** ve **rowkey**. Örneğin, varlıklara alternatif anahtarlı erişim yolları sağlamak için bileşik anahtar değerlerini kullanın.  
* ***Sorgu projeksiyonu kullanın.*** Yalnızca ihtiyaç duyduğunuz alanları seçerek ağ üzerinden aktardığınız veri miktarını azaltabilirsiniz.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Tablo hizmeti çözümünüzü yazma etkin olacak şekilde tasarlama  

* ***Etkin bölümler oluşturmayın.*** İsteklerinizi dilediğiniz zaman bir noktada birden çok bölüme yaymaya olanak sağlayan anahtarlar ' ı seçin.  
* ***Trafikte ani artışlar önleyin.*** Trafiği makul bir süre boyunca düzgünleştirmek ve trafikte ani artışlar önleyin.
* ***Her varlık türü için ayrı bir tablo oluşturmanız gerekmez.*** Varlık türleri arasında Atomik işlemler gerektirdiğinde, bu birden çok varlık türünü aynı tablodaki aynı bölümde saklayabilirsiniz.
* ***Elde etmeniz gereken en yüksek aktarım hızını göz önünde bulundurun.*** Tablo hizmeti için ölçeklenebilirlik hedeflerini bilmeniz ve tasarımınızın bunları aşmanıza yol açmayacağından emin olmanız gerekir.  

Bu kılavuzu okurken, tüm bu ilkeleri uygulamaya yerleştirtirecek örnekleri görürsünüz. 

## <a name="next-steps"></a>Sonraki adımlar

- [Tablo tasarımı desenleri](table-storage-design-patterns.md)
- [Sorgulama için tasarım](table-storage-design-for-query.md)
- [Tablo verilerini şifreleme](table-storage-design-encrypt-data.md)
- [Veri değişikliği için tasarım](table-storage-design-for-modification.md)
