---
title: Verileri bir kaynak veri deposundan hedef veri deposuna artımlı olarak kopyalama
description: Bu öğreticiler verilerin, kaynak veri deposundan hedef veri deposuna nasıl artımlı olarak kopyalanacağını gösterir. İlki bir tablodan veri kopyalar.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 7161fb30c8b445681b4cd577d8f8ac9fff5106df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739254"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Bir kaynak veri deposundan hedef veri deposuna artımlı olarak veri yükleme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

İlk veriler yüklendikten sonra verileri artımlı olarak (veya delta) yükleme senaryosu, veri tümleştirme çözümlerinde sıkça kullanılır. Bu bölümdeki öğreticiler, Azure Data Factory kullanarak artımlı veri yüklemenin farklı yollarını gösterir.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Bir filigran kullanarak veritabanından Delta verileri yükleme
Bu durumda, kaynak veritabanında bir filigran tanımlayın. Filigran, en son güncelleştirilen zaman damgası veya artan bir anahtarı olan bir sütundur. Delta yükleme çözümü, eski bir filigran ile yeni bir filigran arasında değiştirilen verileri yükler. Bu yaklaşıma yönelik iş akışı şu diyagramda gösterilmiştir: 

![Filigran kullanmaya yönelik iş akışı](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Adım adım yönergeler için şu öğreticilere bakın: 
- [Azure SQL Veritabanı’ndaki bir tablodan Azure Blob depolama Alanı’na artımlı olarak veri kopyalama](tutorial-incremental-copy-powershell.md)
- [SQL Server örneğindeki birden çok tablodan Azure SQL veritabanı 'na artımlı olarak veri kopyalama](tutorial-incremental-copy-multiple-tables-powershell.md)

Şablonlar için aşağıdakilere bakın:
- [Denetim tablosuyla delta kopyalama](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Değişiklik İzleme teknolojisini kullanarak SQL DB 'den Delta verileri yükleme
Değişiklik İzleme teknolojisi, SQL Server ve Azure SQL Veritabanı’nda bulunan, uygulamalar için verimli bir değişiklik izleme mekanizması sağlayan basit bir çözümdür. Bir uygulamanın eklenen, güncelleştirilen veya silinen verileri kolayca tanımlamasına olanak sağlar. 

Bu yaklaşıma yönelik iş akışı şu diyagramda gösterilmiştir:

![Değişiklik İzleme kullanmaya yönelik iş akışı](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Adım adım yönergeler için şu öğreticilere bakın: <br/>
- [Değişiklik İzleme teknolojisini kullanarak Azure SQL Veritabanı’ndan Azure Blob depolama alanına artımlı olarak veri kopyalama](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Yalnızca LastModifiedDate kullanarak yeni ve değiştirilmiş dosyaları yükleme
Yalnızca LastModifiedDate kullanarak hedef depoya yeni ve değiştirilmiş dosyaları kopyalayabilirsiniz. ADF, kaynak deposundaki tüm dosyaları tarar, dosya filtresini LastModifiedDate göre uygular ve yalnızca hedef depoya son kez bu yana yeni ve güncelleştirilmiş dosyayı kopyalar.  ADF 'nin çok büyük miktarlarda dosya taramasına izin verseniz, ancak yalnızca birkaç dosyayı hedefe kopyalayabiliyorsanız, dosya taramanın de uzun süre sürmesi nedeniyle uzun süre beklemeniz gerekir.   

Adım adım yönergeler için şu öğreticilere bakın: <br/>
- [Azure Blob depolama 'dan Azure Blob depolama 'ya LastModifiedDate göre yeni ve değiştirilmiş dosyaları artımlı olarak kopyalama](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Şablonlar için aşağıdakilere bakın:
- [LastModifiedDate parametresine göre yeni dosyaları kopyalama](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Yeni dosyalar yalnızca bölümlenmiş klasör veya dosya adı kullanılarak yükleniyor.
Dosya veya klasör adının bir parçası olarak (örneğin,/yyyy/mm/dd/file.csv), dosyaların veya klasörlerin zaten timeslice bilgileriyle bölümlenme zamanı olan yeni dosyaları kopyalayabilirsiniz. Yeni dosyaları artımlı olarak yüklemek için en iyi performansa yaklaşımlar. 

Adım adım yönergeler için şu öğreticilere bakın: <br/>
- [Azure Blob depolama 'dan Azure Blob depolama alanı 'na bölümlenmiş bir klasöre veya dosya adına göre yeni dosyaları artımlı olarak kopyalama](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Sonraki adımlar
Şu öğreticiye ilerleyin: 

> [!div class="nextstepaction"]
>[Azure SQL Veritabanı’ndaki bir tablodan Azure Blob depolama Alanı’na artımlı olarak veri kopyalama](tutorial-incremental-copy-powershell.md)
