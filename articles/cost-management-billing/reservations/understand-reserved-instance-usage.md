---
title: Bireysel abonelik için Azure rezervasyon kullanımı
description: Kullandıkça öde fiyatlarına tabi bireysel aboneliğiniz için Azure rezervasyonunun nasıl uygulandığını anlamak için kullanım bilgilerinizi okuma hakkında bilgi edinin.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f6aad15d70157dda110fce233fbdafb7f3d896ad
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055068"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Kullandıkça öde fiyatlarına tabi bireysel aboneliğiniz için Azure rezervasyon kullanımını anlama

Rezervasyon kullanımınızı değerlendirmek için [rezervasyon sayfasından](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) Rezervationıd ve [Azure Portal](https://portal.azure.com) kullanım dosyasını kullanın.

Kurumsal Anlaşması olan bir müşteriyseniz bkz. [Kurumsal kaydınız için rezervasyon kullanımını anlama](understand-reserved-instance-usage-ea.md).

Bu makalede, rezervasyonun tek bir aboneliğe uygulandığı varsayılmaktadır. Rezervasyon birden fazla aboneliğe uygulandıysa rezervasyon avantajınız kullanım bilgilerini içeren birden çok CSV dosyasına yayılabilir.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Ayrılmış Sanal Makine Örnekleri için kullanım

Aşağıdaki bölümlerde, Doğu ABD bölgesinde bir Standard_DS1_v2 Windows sanal makinesi çalıştırdığınızı ve ayrılmış sanal makine örneği bilgilerinizin aşağıdaki tablo gibi göründüğünü varsayın:

| Alan | Değer |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Miktar |1|
|SKU | Standard_DS1_v2|
|Bölge | eastus |

Dağıtılan sanal makine, rezervasyon öznitelikleriyle eşleştiğinden, sanal makinenin donanım kısmı ele alınmıştır. Hangi Windows yazılımının ayrılmış sanal makine örneği kapsamında olmadığını görmek için bkz. [Azure Ayrılmış Sanal Makine Örnekleri Windows yazılımı maliyetleri](reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Sanal makineler için CSV dosyasının bildirim bölümü

CSV dosyanızın bu bölümünde, rezervasyonunuzun toplam kullanımı gösterilmektedir. **"Rezervasyon-"** ifadesini içeren **Ölçüm Kategorisi** alanına filtreyi uygulayın. Aşağıdaki ekran görüntüsüne benzer bir sonuç görürsünüz:

![Filtrelenmiş rezervasyon kullanımı ayrıntıları ve ücretlerinin ekran görüntüsü](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

**Ayırma - Temel VM** satırında, rezervasyon kapsamındaki toplam saat sayısı yer alır. Bu satır, rezervasyon kapsamında olduğundan 0,00 ABD dolarıdır. **Ayırma - Windows Svr (1 Çekirdek)** satırı, Windows yazılımının maliyetlerini kapsar.

### <a name="daily-usage-section-of-csv-file"></a>CSV dosyasının günlük kullanım bölümü

**Ek Bilgiler** bölümünü filtreleyin ve **Rezervasyon Kimliğinizi** yazın. Aşağıdaki ekran görüntüsünde, rezervasyonla ilgili alanlar gösterilmektedir.

![Günlük kullanım ayrıntıları ve ücretlerinin ekran görüntüsü](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **Ek Bilgiler** alanındaki **RezervasyonKimliği**, sanal makineye uygulanan rezervasyondur.
2. **ConsumptionMeter**, sanal makinenin ölçüm kimliğidir.
3. **Ayırma - Temel VM** **Ölçüm Alt Kategorisi** satırı, bildirim bölümünde 0 ABD doları maliyeti temsil eder. Bu sanal makineyi çalıştırmanın maliyeti zaten rezervasyon tarafından ödenmiştir.
4. **Ölçüm Kimliği**, rezervasyonun ölçüm kimliğidir. Bu ölçümün maliyeti 0 ABD dolarıdır. Bu ölçüm kimliği, rezervasyon indirimi için uygun nitelikte olan tüm sanal makineler için görüntülenir.
5. Standard_DS1_v2 bir vCPU sanal makinesidir ve sanal makine, Azure Hibrit Avantajı olmadan dağıtılır. Bu nedenle bu ölçüm, Windows yazılımının ek ücretini kapsar. D serisi 1 çekirdekli sanal makineye karşılık gelen ölçümü bulmak için bkz. [Azure Ayrılmış Sanal Makine Örnekleri Windows yazılım maliyetleri](reserved-instance-windows-software-costs.md). Azure Hibrit Avantajı’na sahipseniz bu ek ücret uygulanmaz.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>SQL Veritabanı ve Cosmos DB rezervasyonları için kullanım

Aşağıdaki bölümlerde, kullanım raporunu açıklamak için örnek olarak Azure SQL Veritabanı kullanılmaktadır. Azure Cosmos DB kullanım bilgilerini almak için de aynı adımları kullanabilirsiniz.

Doğu ABD bölgesinde bir SQL Veritabanı 4. Nesil çalıştırdığınızı ve rezervasyon bilgilerinizin aşağıdaki tabloya benzer şekilde göründüğünü varsayın:

| Alan | Değer |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Miktar |2|
|Ürün| SQL Veritabanı 4. Nesil (2 Çekirdek)|
|Bölge | eastus |

### <a name="statement-section-of-csv-file"></a>CSV dosyasının bildirim bölümü

**Ayrılmış Örnek Kullanımı** ölçüm adını filtreleyin ve Azure SQL Veritabanı veya Azure Cosmos DB olarak gerekli **Ölçüm Kategorisi**’ni seçin. Aşağıdaki ekran görüntüsüne benzer bir sonuç görürsünüz:

![Ekran görüntüsü Ölçüm Kategorisi girdisini gösterir.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

**Ayrılmış Örnek Kullanımı** satırında, rezervasyon kapsamındaki toplam çekirdek saati sayısı yer alır. Rezervasyon maliyeti kapsadığından bu satır için fiyat 0 ABD dolarıdır.

### <a name="detail-section-of-csv-file"></a>CSV dosyasının ayrıntı bölümü

**Ek Bilgiler** bölümünü filtreleyin ve **Rezervasyon Kimliğinizi** yazın. Aşağıdaki ekran görüntüsünde, SQL Veritabanı ayrılmış kapasite rezervasyonu ile ilgili alanlar gösterilmektedir.

![Ekran görüntüsü ayrılmış kapasite için C S V dosyasının ayrıntılarını gösterir.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **Ek Bilgiler** alanındaki **ReservationId**, SQL Veritabanı kaynağına uygulanan SQL Veritabanı ayrılmış kapasite rezervasyonudur.
2. **ConsumptionMeter**, SQL Veritabanı kaynağının ölçüm kimliğidir.
3. **Ölçüm Kimliği**, rezervasyon ölçümüdür. Bu ölçümün maliyeti 0 ABD dolarıdır. Rezervasyon indirimi için uygun nitelikte olan SQL Veritabanı kaynakları, CSV dosyasında bu ölçüm kimliğini gösterir.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](save-compute-costs-reservations.md)
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Azure SQL Veritabanı ayrılmış kapasitesi ile SQL Veritabanı işlem kaynakları için ön ödeme yapma](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Ayırmalarını yönetme](manage-reserved-vm-instance.md)
- [Rezervasyon indiriminin nasıl uygulandığını anlama](../manage/understand-vm-reservation-charges.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](understand-reserved-instance-usage-ea.md)
- [Rezervasyonlara dahil olmayan Windows yazılımı maliyetleri](reserved-instance-windows-software-costs.md)