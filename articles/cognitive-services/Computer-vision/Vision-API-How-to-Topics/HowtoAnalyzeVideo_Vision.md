---
title: Videoları neredeyse gerçek zamanlı olarak analiz edin-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si kullanarak canlı video akışından alınan çerçeveler üzerinde neredeyse gerçek zamanlı analizler gerçekleştirmeyi öğrenin.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933180"
---
# <a name="analyze-videos-in-near-real-time"></a>Videoları neredeyse gerçek zamanlı olarak analiz edin

Bu makalede, Görüntü İşleme API'si kullanarak canlı video akışından alınan çerçeveler üzerinde neredeyse gerçek zamanlı analizler gerçekleştirme işlemi gösterilmektedir. Bu tür bir analizin temel öğeleri şunlardır:

- Bir video kaynağından çerçeveler alınıyor.
- Hangi çerçevelerin analiz edileceği seçiliyor.
- Bu çerçeveler API 'ye gönderiliyor.
- API çağrısından döndürülen her analiz sonucunu kullanma.

Bu makaledeki örnekler C# dilinde yazılmıştır. Koda erişmek için GitHub 'daki [video Çerçeve Analizi örneği](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) sayfasına gidin.

## <a name="approaches-to-running-near-real-time-analysis"></a>Neredeyse gerçek zamanlı analiz çalıştırmaya yönelik yaklaşımlar

Çeşitli yaklaşımlar kullanarak video akışlarında neredeyse gerçek zamanlı analizler çalıştırma sorununu çözebilirsiniz. Bu makalede, gelişmiş algoritmaların mümkündür seviyeleri arttırılarak bunlardan üçü özetlenmektedir.

### <a name="design-an-infinite-loop"></a>Sonsuz döngü tasarlama

Neredeyse gerçek zamanlı çözümlemenin en basit tasarımı sonsuz bir döngüdür. Bu döngünün her yinelemesinde bir çerçeve alırsınız, analiz eder ve ardından sonucu kullanın:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Analizinizi basit, istemci tarafı algoritmasından oluşması durumunda bu yaklaşım uygun olacaktır. Ancak, analiz bulutta olduğunda, sonuçta elde edilen gecikme süresi bir API çağrısının birkaç saniye sürebileceği anlamına gelir. Bu süre boyunca görüntüleri yakalamadığını ve iş parçacığımız aslında hiçbir şey yapmadığınızı. Maksimum kare oranı, API çağrılarının gecikmesi ile sınırlıdır.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>API çağrılarının paralel olarak çalıştırılmasına izin ver

Basit, tek iş parçacıklı bir döngü basit, istemci tarafı bir algoritma açısından mantıklı olsa da, bir bulut API çağrısının gecikmesi ile iyi bir şekilde uyum sağlar. Bu sorunun çözümü, uzun süreli API çağrısının Frame-yakalayıp ile paralel çalışmasına izin verdir. C# ' de, görev tabanlı paralellik kullanarak bunu yapabilirsiniz. Örneğin, aşağıdaki kodu çalıştırabilirsiniz:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Bu yaklaşımda, her çözümlemeyi ayrı bir görevde başlatın. Görev, yeni çerçeveler yakalayıp devam ederken arka planda çalıştırılabilir. Yaklaşım, API çağrısının döndürülmesini beklerken ana iş parçacığının engellenmesini önler. Ancak yaklaşım belirli dezavantajları sunabilir:
* Bu, sunulan basit sürümün sunduğu garantilerden bazılarını ücretetmektedir. Diğer bir deyişle, birden çok API çağrısı paralel olarak gerçekleşebilir ve sonuçlar yanlış sırada döndürülür. 
* Ayrıca, işlev iş parçacığı açısından güvenli değilse tehlikeli olabilecek çok sayıda iş parçacığının ConsumeResult () işlevini aynı anda girmesine neden olabilir. 
* Son olarak, bu basit kod oluşturulan görevleri izlememez, bu nedenle özel durumlar sessizce kaybolur. Bu nedenle, çözümleme görevlerini izleyen bir "tüketici" iş parçacığı eklemeniz, özel durumlar ortaya çıkar, uzun süreli görevler ve sonuçların doğru sırada tüketilmesini sağlar.

### <a name="design-a-producer-consumer-system"></a>Üretici-tüketici sistemi tasarlama

"Producer-Consumer" sistemi tasarlayan son yaklaşımınız için, önceden belirtilen sonsuz döngüsüyle benzer bir üretici iş parçacığı oluşturursunuz. Ancak, analiz sonuçlarını kullanılabilir duruma geldiğinde kullanmak yerine üretici, görevleri izlemek için bir sıraya yerleştirirler.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

Ayrıca, görevleri kuyruk dışında alan bir tüketici iş parçacığı oluşturun, bunların tamamlanmasını bekler ve sonucu görüntüler ya da oluşturulan özel durumu oluşturur. Kuyruğu kullanarak, sonuçların, sistemin maksimum kare hızını sınırlamadan, doğru sırada bir kez tüketildiğini garanti edebilirsiniz.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>Çözümü uygulama

### <a name="get-started-quickly"></a>Hızlıca kullanmaya başlayın

Uygulamanızı mümkün olduğunca hızlı bir şekilde çalışmaya ve çalıştırmaya yardımcı olmak için, önceki bölümde açıklanan sistemi uyguladık. Kullanımı kolay olan çok sayıda senaryoya uyum sağlayacak kadar esnek olması amaçlanmıştır. Koda erişmek için GitHub 'daki [video Çerçeve Analizi örneği](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) sayfasına gidin.

Kitaplığı, `FrameGrabber` bir Web kamerasından video çerçevelerini işlemek için daha önce tartışılan üretici tüketicisi sistemini uygulayan sınıfını içerir. Kullanıcılar, API çağrısının tam biçimini belirtebilir ve sınıf, çağıran kodun yeni bir çerçeve elde edildiğinde veya yeni bir analiz sonucu olduğunda bilmesini sağlamak için olayları kullanır.

Bazı olasılıkları göstermek için, kitaplığı kullanan iki örnek uygulama sunuyoruz. 

İlk örnek uygulama, varsayılan Web kamerasından çerçeveler oluşturan basit bir konsol uygulamasıdır ve ardından bunları yüz tanıma algılaması için yüz hizmetine gönderir. Uygulamanın basitleştirilmiş bir sürümü aşağıdaki kodda yeniden oluşturulur:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

İkinci örnek uygulama biraz daha ilgi çekici bir uygulamadır. Video çerçevelerinde hangi API 'nin çağrılacağını seçmenizi sağlar. Sol tarafta, uygulama canlı videonun bir önizlemesini gösterir. Sağ tarafta, ilgili çerçevede en son API sonucunun üzerine yer alır.

Çoğu modda, sol taraftaki canlı video ile sağdaki görselleştirilen analizler arasında görünür bir gecikme vardır. Bu gecikme, API çağrısını yapmak için gereken süredir. "EmotionsWithClientFaceDetect" modunda bir özel durum, Azure bilişsel hizmetler 'e herhangi bir görüntü göndermeden önce OpenCV kullanarak istemci bilgisayarda yüz algılamayı yerel olarak gerçekleştirir. 

Bu yaklaşımı kullanarak, algılanan yüzü hemen görselleştirebilirsiniz. Daha sonra, API çağrısı gerçekleştirildikten sonra, daha sonra bu onayları güncelleştirebilirsiniz. Bu, "karma" yaklaşımın olasılığını gösterir. Diğer bir deyişle, istemci üzerinde bazı basit işlemler gerçekleştirilebilir ve sonra bu işlemi gerektiğinde daha gelişmiş analizler ile artırmak için Bilişsel Hizmetler API'si kullanılabilir.

![Etiketli bir görüntüyü görüntüleyen LiveCameraSample uygulaması](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Örnekleri kod Tabanınızla tümleştirin

Bu örneğe başlamak için aşağıdakileri yapın:

1. [Azure hesabı](https://azure.microsoft.com/free/cognitive-services/) oluşturun. Zaten varsa, bir sonraki adıma geçebilirsiniz.
2. Anahtarınızı ve uç noktanızı almak için Azure portal Görüntü İşleme ve yüz için kaynaklar oluşturun. Kurulum sırasında ücretsiz katmanı (F0) seçtiğinizden emin olun.
   - [Görüntü İşleme](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Yüz](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Kaynaklar dağıtıldıktan sonra, her kaynak için anahtarınızı ve uç noktayı toplamak için **Kaynağa Git** ' e tıklayın. 
3. Bilişsel [örnekler-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub deposunun klonlayın.
4. Visual Studio 2015 veya sonraki sürümlerde örneği açın ve örnek uygulamaları derleyin ve çalıştırın:
    - BasicConsoleSample için yüz tuşu, doğrudan [basicconsolesample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)içinde sabit olarak kodlanmıştır.
    - LiveCameraSample için, uygulamanın **Ayarlar** bölmesine anahtarları girin. Anahtarlar, oturum boyunca Kullanıcı verileri olarak kalıcıdır.

Örnekleri tümleştirmeye hazırsanız, kendi projelerinizden VideoFrameAnalyzer kitaplığı 'na başvurun.

VideoFrameAnalyzer 'ın görüntü, ses, video ve metin anlama özellikleri, Azure bilişsel hizmetler 'i kullanır. Microsoft, karşıya yüklediğiniz görüntüler, ses, video ve diğer verileri (Bu uygulama aracılığıyla) alır ve bunları hizmet geliştirme amacıyla kullanabilir. Uygulamanızın verilerini Azure Bilişsel Hizmetler’e gönderdiği kişilerin korunması için yardımınızı rica ediyoruz.

## <a name="summary"></a>Özet

Bu makalede, yüz ve Görüntü İşleme hizmetlerini kullanarak canlı video akışları üzerinde neredeyse gerçek zamanlı analizler çalıştırmayı öğrendiniz. Ayrıca, kullanmaya başlamak için örnek kodumuzu nasıl kullanabileceğinizi öğrenirsiniz.

[GitHub deposunda](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)geri bildirim ve öneriler sağlamayı ücretsiz olarak hissetmekten çekinmeyin. Daha geniş bir API geri bildirimi sağlamak için [UserVoice sitemiz](https://cognitive.uservoice.com/)sayfasına gidin.

