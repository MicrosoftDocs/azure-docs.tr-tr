---
title: Azure Log Analytics verilerini Power BI içine aktarma | Microsoft Docs
description: Power BI, Microsoft 'un, farklı veri kümelerinin analizine yönelik zengin görselleştirmeler ve raporlar sağlayan bulut tabanlı bir iş analizi hizmetidir.  Bu makalede, Power BI Log Analytics verileri yapılandırma ve içeri aktarma ve otomatik olarak yenileme için yapılandırma açıklanmaktadır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 69e953f6d2cb9542a589fb2e282ff166c43fc0a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043618"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Azure Izleyici günlük verilerini Power BI içine aktarın


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) , Microsoft 'un, farklı veri kümelerinin analizine yönelik zengin görselleştirmeler ve raporlar sağlayan bulut tabanlı bir iş analizi hizmetidir.  Azure Izleyici günlük sorgusunun sonuçlarını bir Power BI veri kümesine aktarabilirsiniz, böylece farklı kaynaklardan verileri birleştirme ve Web ve mobil cihazlarda rapor paylaşma gibi özelliklerden yararlanabilirsiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Genel Bakış
Azure Izleyici 'deki bir [Log Analytics çalışma alanındaki](../logs/manage-access.md) verileri Power BI içine aktarmak Için, Azure izleyici 'de bir [günlük sorgusuna](../logs/log-query-overview.md) göre Power BI ' de bir veri kümesi oluşturursunuz.  Sorgu, veri kümesinin her yenilenmesinde çalıştırılır.  Daha sonra veri kümesindeki verileri kullanan Power BI raporlar oluşturabilirsiniz.  Power BI veri kümesini oluşturmak için sorgunuzu Log Analytics [Power Query (e) dile](/powerquery-m/power-query-m-language-specification)dışarı aktarabilirsiniz.  Daha sonra bunu, Power BI Desktop bir sorgu oluşturmak ve sonra bir veri kümesi olarak Power BI yayımlamak için kullanırsınız.  Bu işlemin ayrıntıları aşağıda açıklanmıştır.

![Power BI Log Analytics](media/powerbi/overview.png)

## <a name="export-query"></a>Sorguyu dışarı aktar
Power BI veri kümesini doldurmak istediğiniz verileri döndüren bir [günlük sorgusu](../logs/log-query-overview.md) oluşturarak başlayın.  Daha sonra bu sorguyu, Power BI Desktop tarafından kullanılabilecek [Power Query (e) dile](/powerquery-m/power-query-m-language-specification) dışarı aktarabilirsiniz.

> [!WARNING]
> Sorgunuzu, çalışması uzun sürmemesi veya zaman aşımına uğramaması için [En iyileştirmeden](../logs/query-optimization.md) emin olun. Sorgunun alınacağı verilerin TimeSpan değerini tanımlayan, dışarıya aktarılmış sorgudaki **TimeSpan** değerini unutmayın. Sorgunun döndürdüğü veri miktarını sınırlandırmak için ihtiyaç duyduğunuz en küçük TimeSpan değerini kullanın.

1. Veri kümenizin verilerini ayıklamak için [Log Analytics günlük sorgusu oluşturun](../logs/log-analytics-tutorial.md) .
2. **Dışarı aktarma**  >  **Power BI sorgusunu (d)** seçin.  Bu, sorguyu **PowerBIQuery.txt** adlı bir metin dosyasına dışarı aktarır. 

    ![Günlük aramasını dışarı aktar](media/powerbi/export-analytics.png)

3. Metin dosyasını açın ve içeriğini kopyalayın.

## <a name="import-query-into-power-bi-desktop"></a>Sorguyu Power BI Desktop içeri aktar
Power BI Desktop, Power BI yayımlanmakta olabilecek veri kümeleri ve raporlar oluşturmanıza olanak sağlayan bir masaüstü uygulamasıdır.  Azure Izleyici 'den aktarılmış Power Query dilini kullanarak bir sorgu oluşturmak için de kullanabilirsiniz. 

1. Henüz yoksa [Power BI Desktop](https://powerbi.microsoft.com/desktop/) yükleyip uygulamayı açın.
2.   >  Yeni bir sorgu açmak için veri al **boş sorgusu ' nu** seçin.  Sonra **Gelişmiş Düzenleyici** ' yi seçin ve ardından dosyaya aktarılmış dosyanın içeriğini sorguya yapıştırın. **Bitti**’ye tıklayın.

    ![Power BI Desktop sorgu](media/powerbi/desktop-new-query.png)

5. Sorgu çalışır ve sonuçları görüntülenir.  Azure 'a bağlanmak için kimlik bilgileri istenebilir.  
6. Sorgu için açıklayıcı bir ad yazın.  Varsayılan değer **Sorgu1**' dir. Veri kümesini rapora eklemek için **Kapat ve Uygula** ' yı tıklatın.

    ![Power BI Desktop adı](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Power BI'da yayımla
Power BI yayımladığınızda bir veri kümesi ve rapor oluşturulur.  Power BI Desktop bir rapor oluşturursanız, bu, verileriniz ile yayımlanacak.  Aksi takdirde, boş bir rapor oluşturulur.  Power BI raporu değiştirebilir veya veri kümesine göre yeni bir tane oluşturabilirsiniz.

1. Verilerinizi temel alan bir rapor oluşturun.  Bilgi sahibi değilseniz [Power BI Desktop belgeleri](/power-bi/desktop-report-view) kullanın.  
1. Power BI göndermek için hazırsanız **Yayımla**' ya tıklayın.  
1. İstendiğinde Power BI hesabınızda bir hedef seçin.  Aklınızda belirli bir hedef yoksa **çalışma alanım**' ı kullanın.

    ![Power BI Desktop yayımlama](media/powerbi/desktop-publish.png)

1. Yayımlama tamamlandığında, yeni veri kümeniz ile Power BI açmak için **Power BI aç** ' a tıklayın.


### <a name="configure-scheduled-refresh"></a>Zamanlanmış yenileme yapılandırma
Power BI oluşturulan veri kümesi, daha önce Power BI Desktop gördüğünüz verileri aynı olacak.  Sorguyu yeniden çalıştırmak ve Azure Izleyici 'den en güncel verilerle doldurmak için veri kümesini düzenli aralıklarla yenilemeniz gerekir.  

1. Raporunuzu karşıya yüklediğiniz çalışma alanına tıklayın ve **veri kümeleri** menüsünü seçin. 
1. Yeni veri kümenizin yanındaki bağlam menüsünü seçin ve **Ayarlar**' ı seçin. 
1. **Veri kaynağı kimlik bilgileri** altında, kimlik bilgilerinin geçersiz olduğunu belirten bir iletiniz olmalıdır.  Bunun nedeni, verileri yenilediğinde veri kümesinin kullanacağı kimlik bilgilerini henüz belirtmemiştir.  
1. **Kimlik bilgilerini düzenle** ' ye tıklayın ve Azure izleyici 'de Log Analytics çalışma alanına erişimi olan kimlik bilgilerini belirtin. İki öğeli kimlik doğrulaması gerekiyorsa kimlik bilgilerinizle oturum açması istenecek **kimlik doğrulama yönteminin** **OAuth2** öğesini seçin.

    ![Power BI zamanlama](media/powerbi/powerbi-schedule.png)

5. **Zamanlanmış yenileme** altında **verilerinizi güncel tutma** seçeneğini açın.  Yenilemeyi çalıştırmak için isteğe bağlı olarak **yenileme sıklığını** ve bir veya daha fazla belirli bir kez değişiklik yapabilirsiniz.

    ![Power BI Yenile](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Sonraki adımlar
* Power BI 'e verilebileceğinizi sorgular oluşturmak için [günlük aramaları](../logs/log-query-overview.md) hakkında bilgi edinin.
* Azure Izleyici günlük dışarı aktarmaları temelinde görselleştirmeler oluşturmak için [Power BI](https://powerbi.microsoft.com) hakkında daha fazla bilgi edinin.