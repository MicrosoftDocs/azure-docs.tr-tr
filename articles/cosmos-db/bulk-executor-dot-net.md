---
title: Toplu içeri aktarma ve güncelleştirme işlemleri için Azure Cosmos DB içinde toplu yürütücü .NET kitaplığı 'nı kullanın
description: Toplu yürütücü .NET kitaplığını kullanarak Azure Cosmos DB belgelerini toplu olarak içeri aktarın ve güncelleştirin.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34aef5bd880e3ef080676fb9e90e62796d499e7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102429825"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Toplu yürütücü .NET kitaplığı 'nı kullanarak Azure Cosmos DB toplu işlemleri gerçekleştirin
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Bu makalede açıklanan bu toplu yürütücü kitaplığı, .NET SDK 2. x sürümünü kullanan uygulamalar için korunur. Yeni uygulamalar için, [.NET SDK sürüm 3. x](tutorial-sql-api-dotnet-bulk-import.md) ile doğrudan kullanılabilen **toplu desteği** kullanabilir ve herhangi bir dış kitaplık gerektirmez. 

> Şu anda toplu yürütücü kitaplığını kullanıyorsanız ve daha yeni SDK 'da toplu desteğe geçirmeyi planlıyorsanız, uygulamanızı geçirmek için [geçiş kılavuzundaki](how-to-migrate-from-bulk-executor-library.md) adımları kullanın.

Bu öğretici, belgeleri Azure Cosmos kapsayıcısında içeri aktarmak ve güncelleştirmek için toplu yürütücü .NET kitaplığını kullanma yönergelerini sağlar. Toplu yürütücü Kitaplığı hakkında bilgi edinmek ve çok büyük bir işlem ve depolama özelliğinden faydalanmanıza yardımcı olmak için, bkz. [toplu yürütücü kitaplığı genel bakış](bulk-executor-overview.md) makalesi. Bu öğreticide, rastgele oluşturulan belgelerin bir Azure Cosmos kapsayıcısına toplu olarak içe aktardığı örnek bir .NET uygulaması görürsünüz. İçeri aktardıktan sonra, belirli belge alanlarında gerçekleştirilecek işlemler olarak düzeltme eklerini belirterek içeri aktarılan verileri nasıl toplu olarak güncelleşkullanabileceğinizi gösterir.

Şu anda toplu yürütücü kitaplığı yalnızca SQL API ve Gremlin API hesapları Azure Cosmos DB desteklenir. Bu makalede, SQL API hesaplarıyla toplu yürütücü .NET kitaplığı 'nın nasıl kullanılacağı açıklanır. Gremlin API hesaplarıyla toplu yürütücü .NET kitaplığını kullanma hakkında bilgi edinmek için bkz. [Azure Cosmos DB Gremlin API 'de toplu işlemler gerçekleştirme](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio 2019 ' ü henüz yüklemediyseniz [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/)' ı indirip kullanabilirsiniz. Visual Studio Kurulumu sırasında "Azure geliştirme" özelliğini etkinleştirdiğinizden emin olun.

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

* [Azure Cosmos DB’yi ücretsiz olarak](https://azure.microsoft.com/try/cosmosdb/) bir Azure aboneliği olmadan, ücretsiz ve herhangi bir taahhütte bulunmadan deneyebilirsiniz. Ya da [Azure Cosmos DB öykünücüsünü](./local-emulator.md) `https://localhost:8081` uç noktayla kullanabilirsiniz. Birincil Anahtar, [Kimlik doğrulama istekleri](local-emulator.md#authenticate-requests) bölümünde sağlanır.

* .NET hızlı başlangıç makalesinin [veritabanı hesabı oluşturma](create-sql-api-dotnet.md#create-account) bölümünde açıklanan adımları kullanarak Azure Cosmos DB BIR SQL API hesabı oluşturun.

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi, GitHub 'dan örnek bir .NET uygulaması indirerek kodla çalışmaya geçiş yapalım. Bu uygulama, Azure Cosmos hesabınızda depolanan veriler üzerinde toplu işlemler gerçekleştirir. Uygulamayı kopyalamak için, bir komut istemi açın, kopyalamak istediğiniz dizine gidin ve şu komutu çalıştırın:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Kopyalanmış depo "Bulkımportsample" ve "BulkUpdateSample" olmak üzere iki örnek içerir. Örnek uygulamalardan birini açabilir, App.config dosyadaki bağlantı dizelerini Azure Cosmos DB hesabınızın bağlantı dizeleriyle güncelleştirebilir, çözümü oluşturabilir ve çalıştırabilirsiniz.

"Bulkımportsample" uygulaması rastgele belgeler oluşturur ve bunları Azure Cosmos hesabınıza toplu olarak içeri aktarır. "BulkUpdateSample" uygulaması, belirli belge alanlarında gerçekleştirilecek işlemler olarak düzeltme eklerini belirterek, içeri aktarılan belgeleri toplu olarak güncelleştirir. Sonraki bölümlerde, bu örnek uygulamaların her birinde kodu gözden geçiyapacaksınız.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Azure Cosmos hesabına toplu veri aktarma

1. "Bulkımportsample" klasörüne gidin ve "Bulkımportsample. sln" dosyasını açın.  

2. Azure Cosmos DB bağlantı dizeleri aşağıdaki kodda gösterildiği gibi App.config dosyasından alınır:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Toplu içe aktarıcı yeni bir veritabanı ve veritabanı adı, kapsayıcı adı ve App.config dosyasında belirtilen aktarım hızı değerlerini içeren bir kapsayıcı oluşturur.

3. Daha sonra DocumentClient nesnesi doğrudan TCP bağlantı moduyla başlatılır:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Bulkyürütücü nesnesi, bekleme süresi ve kısıtlanmış istekler için yüksek yeniden deneme değeriyle başlatılır. Ardından, yaşam süresi boyunca sıkışıklık denetimini Bulkyürütücü 'e geçirmek için 0 olarak ayarlanır.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Uygulama, Bulkımportasync API 'sini çağırır. .NET kitaplığı, serileştirilmiş JSON belgelerinin bir listesini kabul eden ve diğeri Serisi kaldırılan POCO belgelerinin listesini kabul eden, toplu içeri aktarma API 'sinin iki aşırı yüklemesini sağlar. Bu aşırı yüklenmiş yöntemlerin her birinin tanımları hakkında daha fazla bilgi edinmek için [API belgelerine](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync)bakın.

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Bulkımportasync yöntemi aşağıdaki parametreleri kabul eder:**
   
   |**Parametre**  |**Açıklama** |
   |---------|---------|
   |enableUpsert    |   Belgelerde büyük işlemleri etkinleştiren bir bayrak. Verilen KIMLIĞE sahip bir belge zaten varsa, güncelleştirilir. Varsayılan olarak, false olarak ayarlanır.      |
   |Disableautomaticıdgeneration    |    Otomatik KIMLIK oluşturmayı devre dışı bırakmak için bayrak. Varsayılan olarak, true olarak ayarlanır.     |
   |maxConcurrencyPerPartitionKeyRange    | Bölüm anahtar aralığı başına en fazla eşzamanlılık derecesi, null olarak ayarlanması, kitaplığın varsayılan değer olan 20 kullanmasına neden olur. |
   |Maxınmemorysortingbatchsize     |  Her aşamada API çağrısına geçirilen belge numaralandırıcılarından çekilen maksimum belge sayısı. Toplu içeri aktarma işleminden önce gerçekleşen bellek içi sıralama aşamasında, bu parametrenin null olarak ayarlanması, kitaplığın varsayılan en düşük değeri (Documents. Count, 1000000) kullanmasına neden olur.       |
   |cancellationToken    |    Toplu içeri aktarma işleminden düzgün şekilde çıkmak için iptal belirteci.     |

   **Toplu içeri aktarma yanıtı nesne tanımı** Toplu içeri aktarma API çağrısının sonucu aşağıdaki öznitelikleri içerir:

   |**Parametre**  |**Açıklama**  |
   |---------|---------|
   |NumberOfDocumentsImported (uzun)   |  Toplu içeri aktarma API çağrısına sağlanan Toplam belgeden başarıyla içeri aktarılan toplam belge sayısı.       |
   |Totalrequestunitstüketilen (Double)   |   Toplu içeri aktarma API çağrısı tarafından tüketilen toplam istek birimi (RU).      |
   |Totaltımetaken (TimeSpan)    |   Yürütmeyi tamamlamaya yönelik toplu içeri aktarma API çağrısı tarafından alınan toplam süre.      |
   |BadInputDocuments (liste \<object> )   |     Toplu içeri aktarma API çağrısında başarıyla içeri aktarılmayan hatalı biçimli belgelerin listesi. Döndürülen belgeleri düzeltip içeri aktarmayı yeniden deneyin. Hatalı biçimli belgeler, ID değeri dize olmayan belgeleri içerir (null veya başka bir veri türü geçersiz olarak kabul edilir).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Azure Cosmos hesabınızdaki verileri toplu güncelleştirme

Mevcut belgeleri BulkUpdateAsync API kullanarak güncelleştirebilirsiniz. Bu örnekte, `Name` alanı yeni bir değere ayarlayacaksınız ve `Description` mevcut belgelerden alanı kaldıracaksınız. Desteklenen güncelleştirme işlemlerinin tam kümesi için [API belgelerine](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)bakın.

1. "BulkUpdateSample" klasörüne gidin ve "BulkUpdateSample. sln" dosyasını açın.  

2. Güncelleştirme öğelerini ilgili alan güncelleştirme işlemleriyle birlikte tanımlayın. Bu örnekte, `SetUpdateOperation` `Name` alanı güncelleştirmek ve `UnsetUpdateOperation` `Description` Tüm belgelerden alanı kaldırmak için kullanacaksınız. Ayrıca, belirli bir değere göre bir belge alanını artırma, belirli değerleri bir dizi alanına gönderme veya dizi alanından belirli bir değeri kaldırma gibi başka işlemler de gerçekleştirebilirsiniz. Toplu güncelleştirme API 'SI tarafından sunulan farklı yöntemler hakkında bilgi edinmek için [API belgelerine](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)bakın.

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Uygulama, BulkUpdateAsync API 'sini çağırır. BulkUpdateAsync yönteminin tanımı hakkında bilgi edinmek için [API belgelerine](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync)bakın.  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync yöntemi aşağıdaki parametreleri kabul eder:**

   |**Parametre**  |**Açıklama** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Bölüm anahtar aralığı başına en fazla eşzamanlılık derecesi, bu parametreyi null olarak ayarlamak, kitaplığın varsayılan değeri (20) kullanmasını sağlar.   |
   |Maxınmemorysortingbatchsize    |    Her aşamada API çağrısına geçirilen güncelleştirme öğesi numaralandırıcıdan alınan en fazla güncelleştirme öğesi sayısı. Toplu güncelleştirmeden önce gerçekleşen bellek içi sıralama aşamasında, bu parametrenin null olarak ayarlanması kitaplığın varsayılan en düşük değeri (UpdateItems. Count, 1000000) kullanmasına neden olur.     |
   | cancellationToken|Toplu güncelleştirme işleminden düzgün şekilde çıkmak için iptal belirteci. |

   **Toplu güncelleştirme yanıtı nesne tanımı** Toplu güncelleştirme API 'SI çağrısının sonucu aşağıdaki öznitelikleri içerir:

   |**Parametre**  |**Açıklama** |
   |---------|---------|
   |NumberOfDocumentsUpdated (uzun)    |   Toplu güncelleştirme API çağrısı için sağlanan toplam belgelerden başarıyla güncelleştirilmiş belge sayısı.      |
   |Totalrequestunitstüketilen (Double)   |    Toplu güncelleştirme API çağrısı tarafından tüketilen toplam istek birimi (ru).    |
   |Totaltımetaken (TimeSpan)   | Yürütmeyi tamamlamaya yönelik toplu güncelleştirme API çağrısı tarafından alınan toplam süre. |
    
## <a name="performance-tips"></a>Performans ipuçları 

Toplu yürütücü kitaplığı 'nı kullanırken daha iyi performans için aşağıdaki noktaları göz önünde bulundurun:

* En iyi performansı elde etmek için uygulamanızı Azure Cosmos hesabınızın yazma bölgesiyle aynı bölgedeki bir Azure sanal makinesinden çalıştırın.  

* `BulkExecutor`Belirli bir Azure Cosmos kapsayıcısına karşılık gelen tek bir sanal makine içinde uygulamanın tamamı için tek bir nesne örneği oluşturmanız önerilir.  

* Tek bir toplu işlem API yürütmesi, istemci makinesinin CPU 'SU ve ağ GÇ 'sinin büyük bir öbeğini kullandığından (Bu durum, dahili olarak birden çok görevi oluşturarak gerçekleşir). Toplu işlem API çağrılarını çalıştıran uygulama sürecinizdeki birden çok eş zamanlı görevi oluşturmaktan kaçının. Tek bir sanal makinede çalışan tek bir toplu işlem API çağrısı, kapsayıcının aktarım hızını (kapsayıcının üretilen iş > 1.000.000 RU/sn) tüketmez ve toplu işlem API çağrılarını eşzamanlı olarak yürütmek için ayrı sanal makineler oluşturmak tercih edilir.  

* `InitializeAsync()`Hedef Cosmos kapsayıcısının bölüm haritasını getirmek için bir Bulkyürütücü nesnesi örneği oluşturulduktan sonra yöntemin çağrıldığından emin olun.  

* Uygulamanızın App.Config, daha iyi performans için **gcServer** etkinleştirildiğinden emin olun
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Kitaplık, bir günlük dosyasında ya da konsolunda toplanabilecek izlemeleri yayar. Her ikisini de etkinleştirmek için uygulamanızın App.Config dosyasına aşağıdaki kodu ekleyin.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Sonraki adımlar

* NuGet paketi ayrıntıları ve sürüm notları hakkında bilgi edinmek için bkz. [toplu yürütücü SDK ayrıntıları](sql-api-sdk-bulk-executor-dot-net.md).
