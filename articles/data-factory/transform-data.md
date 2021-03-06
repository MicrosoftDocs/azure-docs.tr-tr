---
title: Verileri dönüştürme
description: Hadoop, Azure Machine Learning Studio (klasik) veya Azure Data Lake Analytics kullanarak Azure Data Factory verileri dönüştürün veya verileri işleyin.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 0a1eb593e9f9f15f88aefb2fe06706153a4b74a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361409"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory'de veri dönüştürme

> [!div class="op_single_selector"]
> * [Veri akışını eşleme](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight akışı](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (klasik)](transform-data-using-machine-learning.md) 
> * [Saklı Yordam](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks Not defteri](transform-data-databricks-notebook.md)
> * [Databricks jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET özel](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Genel Bakış
Bu makalede, ham verilerinizi ölçek ve Öngörüler halinde dönüştürmek ve işlemek için kullanabileceğiniz Azure Data Factory veri dönüştürme etkinlikleri açıklanır. Bir dönüştürme etkinliği Azure Databricks veya Azure HDInsight gibi bir bilgi işlem ortamında yürütülür. Her bir dönüştürme etkinliği hakkında ayrıntılı bilgi içeren makalelere bağlantılar sağlar.

Data Factory, işlem hatlarına tek tek veya başka bir etkinlikle zincirleme [olarak eklenebilen](concepts-pipelines-activities.md) aşağıdaki veri dönüştürme etkinliklerini destekler.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Veri akışları ile Azure Data Factory yerel olarak dönüştürme

### <a name="mapping-data-flows"></a>Veri akışlarını eşleme

Veri akışlarını eşleme, Azure Data Factory ' de görsel olarak tasarlanan veri dönüştürmelerdir. Veri akışları, veri mühendislerinin kod yazmadan grafik veri dönüştürme mantığı geliştirmesini sağlar. Elde edilen veri akışları, ölçeklendirilen Spark kümelerini kullanan Azure Data Factory işlem hatları içinde etkinlik olarak yürütülür. Veri akışı etkinlikleri mevcut Data Factory zamanlama, denetim, akış ve izleme özellikleri aracılığıyla çalıştırılabilir. Daha fazla bilgi için bkz. [veri akışlarını eşleme](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Veri denetimi

Azure Data Factory Power Query, bulut ölçeğinde veri hazırlama, bulut ölçeğinde bir şekilde kod içermeyen veri hazırlığı yapmanıza olanak sağlar. Denetimi, [çevrimiçi Power Query](/power-query/) ile tümleşir ve Spark yürütmesi aracılığıyla bulut ölçeğinde veriler için Power Query M işlevlerini kullanılabilir hale getirir. Daha fazla bilgi için bkz. [ADF 'de veri denetimi](wrangling-overview.md).

## <a name="external-transformations"></a>Dış dönüşümler

İsteğe bağlı olarak, dönüştürmeleri kodlayabilirsiniz ve dış işlem ortamını kendiniz yönetebilirsiniz.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive etkinliği
Bir Data Factory işlem hattının HDInsight Hive etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hive sorguları yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [Hive etkinliği](transform-data-using-hadoop-hive.md) makalesi. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig etkinliği
Bir Data Factory işlem hattındaki HDInsight Pig etkinliği, kendi veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizdeki Pig sorgularını yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [Pig etkinlik](transform-data-using-hadoop-pig.md) makalesi. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce etkinliği
Bir Data Factory işlem hattındaki HDInsight MapReduce etkinliği, MapReduce programlarını kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md) makalesi.

### <a name="hdinsight-streaming-activity"></a>HDInsight akış etkinliği
Bir Data Factory işlem hattının HDInsight akış etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hadoop akış programlarını yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [HDInsight akış etkinliği](transform-data-using-hadoop-streaming.md) .

### <a name="hdinsight-spark-activity"></a>HDInsight Spark etkinliği
Bir Data Factory işlem hattının HDInsight Spark etkinliği kendi HDInsight kümenizdeki Spark programlarını yürütür. Ayrıntılar için bkz. [Azure Data Factory Spark programlarını çağırma](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning Studio (klasik) etkinlikleri
Azure Data Factory, tahmine dayalı analiz için yayımlanmış bir Azure Machine Learning Studio (klasik) Web hizmeti kullanan işlem hatlarını kolayca oluşturmanızı sağlar. [Toplu yürütme etkinliğini](transform-data-using-machine-learning.md) bir Azure Data Factory işlem hattında kullanarak, toplu işteki verilerde tahmine dayalı hale getirmek Için bir Studio (klasik) Web hizmeti çağırabilirsiniz.

Zaman içinde, Studio (klasik) Puanlama denemeleri 'in tahmine dayalı modellerinin yeni giriş veri kümeleri kullanılarak geri çekilmesi gerekir. Yeniden eğitim ' i tamamladıktan sonra, Puanlama Web hizmetini geri çekme makinesi öğrenimi modeliyle güncelleştirmek istersiniz. Web hizmetini yeni eğitilen modelle güncelleştirmek için [kaynak güncelleştirme etkinliğini](update-machine-learning-models.md) kullanabilirsiniz.  

Bu Studio (klasik) etkinlikleri hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) etkinliklerini kullanma](transform-data-using-machine-learning.md) . 

### <a name="stored-procedure-activity"></a>Saklı yordam etkinliği
Aşağıdaki veri depolarından birinde saklı yordam çağırmak için bir Data Factory Işlem hattındaki SQL Server saklı yordam etkinliğini kullanabilirsiniz: Azure SQL veritabanı, Azure SYNAPSE Analytics, kuruluşunuzda veya bir Azure VM 'de SQL Server veritabanı. Ayrıntılar için bkz. [saklı yordam etkinliği](transform-data-using-stored-procedure.md) makalesi.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL etkinliği
Data Lake Analytics U-SQL etkinliği Azure Data Lake Analytics kümesinde bir U-SQL betiği çalıştırır. Ayrıntılar için bkz. [Data Analytics U-SQL etkinliği](transform-data-using-data-lake-analytics.md) makalesi. 

### <a name="databricks-notebook-activity"></a>Databricks Not defteri etkinliği

Bir Data Factory işlem hattındaki Azure Databricks Not defteri etkinliği Azure Databricks çalışma alanınızda bir Databricks Not defteri çalıştırır. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur. Bkz. [Databricks Not defteri çalıştırarak verileri dönüştürme](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks jar etkinliği

Bir Data Factory işlem hattındaki Azure Databricks jar etkinliği Azure Databricks kümenizde Spark jar 'i çalıştırır. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur. Bkz. [Azure Databricks bir jar etkinliği çalıştırarak verileri dönüştürme](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks Python etkinliği

Bir Data Factory işlem hattının Azure Databricks Python etkinliği Azure Databricks kümenizdeki bir Python dosyası çalıştırır. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur. Bkz. [Azure Databricks bir Python etkinliği çalıştırarak verileri dönüştürme](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Özel etkinlik
Data Factory tarafından desteklenmeyen bir şekilde veri dönüştürmeniz gerekiyorsa, kendi veri işleme mantığınızla özel bir etkinlik oluşturabilir ve etkinliği ardışık düzende kullanabilirsiniz. Özel .NET etkinliğini bir Azure Batch hizmeti veya Azure HDInsight kümesi kullanarak çalışacak şekilde yapılandırabilirsiniz. Ayrıntılar için bkz. [özel etkinlikleri kullanma](transform-data-using-dotnet-custom-activity.md) makalesi. 

R yüklü HDInsight kümenizde R betiklerini çalıştırmak için özel bir etkinlik oluşturabilirsiniz. Bkz. [Azure Data Factory kullanarak R Betiği çalıştırma](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>İşlem ortamları
İşlem ortamı için bağlı bir hizmet oluşturun ve ardından bir dönüştürme etkinliği tanımlarken bağlı hizmeti kullanın. Data Factory tarafından desteklenen iki tür işlem ortamı vardır. 

- **İsteğe** bağlı: Bu durumda, bilgi işlem ortamı Data Factory tarafından tam olarak yönetilir. Bir iş, verileri işlemek ve iş tamamlandığında kaldırılmadan önce Data Factory hizmeti tarafından otomatik olarak oluşturulur. İş yürütme, küme yönetimi ve önyükleme eylemleri için isteğe bağlı işlem ortamının ayrıntılı ayarlarını yapılandırabilir ve kontrol edebilirsiniz. 
- **Kendinizinkini getir**: Bu durumda, kendi bilgi işlem ortamınızı (örneğin HDInsight kümesi) Data Factory bağlı bir hizmet olarak kaydedebilirsiniz. Bilgi işlem ortamı sizin tarafınızdan yönetilir ve Data Factory hizmeti tarafından etkinlikleri yürütmek için kullanılır. 

Data Factory tarafından desteklenen işlem hizmetleri hakkında bilgi edinmek için bkz. [Işlem bağlantılı hizmetleri](compute-linked-services.md) makalesi. 

## <a name="next-steps"></a>Sonraki adımlar
Dönüştürme etkinliği kullanma örneği için aşağıdaki öğreticiye bakın: [öğretici: Spark kullanarak veri dönüştürme](tutorial-transform-data-spark-powershell.md)
