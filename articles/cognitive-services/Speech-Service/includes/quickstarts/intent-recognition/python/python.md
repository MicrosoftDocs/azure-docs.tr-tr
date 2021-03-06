---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f8caa38fa77134ef156360af2fd3d7dc5d84bb3d
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444399"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Geliştirme ortamınız Için konuşma SDK 'Sını yükleyip boş bir örnek proje oluşturun</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Amaç tanıma için bir LUSıS uygulaması oluşturma

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Projenizi açın

1. Tercih ettiğiniz IDE 'yi açın.
2. Yeni bir proje oluşturun ve adlı dosyayı oluşturun `quickstart.py` , ardından açın.

## <a name="start-with-some-boilerplate-code"></a>Bazı demirbaş kodla başlayın

Projemiz için bir çatı olarak çalışacak bir kod ekleyelim.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Konuşma yapılandırması oluşturma

Bir nesneyi başlatabilmeniz için `IntentRecognizer` , lusıs tahmin kaynağınız için anahtar ve konum kullanan bir yapılandırma oluşturmanız gerekir.

Bu kodu içine ekleyin `quickstart.py` . Bu değerleri güncelleştirdiğinizden emin olun:

* `"YourLanguageUnderstandingSubscriptionKey"`Lusıs tahmin anahtarınızla değiştirin.
* `"YourLanguageUnderstandingServiceRegion"`Lusıs konumunuz ile değiştirin. Bölgeden **bölge tanımlayıcısı** kullan [](../../../../regions.md)

>[!TIP]
> Bu değerleri bulmak için yardıma ihtiyacınız varsa bkz. [Amaç tanıma için BIR lusıs uygulaması oluşturma](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Bu örnek, `SpeechConfig` halsıs anahtar ve bölgesini kullanarak nesneyi oluşturur. Kullanılabilir yöntemlerin tam listesi için bkz. [SpeechConfig Class](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Konuşma SDK 'Sı, dil için en-US kullanarak varsayılan olarak tanıma yapılır, kaynak dili seçme hakkında bilgi için bkz. [konuşmayı için kaynak dilini belirtme](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>Bir Yoğunlumtanıyıcıyı başlatma

Şimdi bir oluşturalım `IntentRecognizer` . Konuşma yapılandırmanızın hemen altına bu kodu ekleyin.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel ve amaçlar ekleyin

Bir `LanguageUnderstandingModel` ' i amaç tanıyıcı ile ilişkilendirmeli ve tanınan hedefleri eklemelisiniz. Ana otomasyon için önceden oluşturulmuş etki alanındaki amaçları kullanacağız.

Buraya bu kodu ekleyin `IntentRecognizer` . `"YourLanguageUnderstandingAppId"`Lusıs uygulama kimliğiniz ile değiştirdiğinizden emin olun. 

>[!TIP]
> Bu değeri bulmak için yardıma ihtiyacınız varsa bkz. [Amaç tanıma için BIR lusıs uygulaması oluşturma](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

Bu örnek, `add_intents()` açıkça tanımlanmış bir amaç listesi eklemek için işlevini kullanır. Bir modelden tüm amaçları eklemek istiyorsanız `add_all_intents(model)` modeli kullanın ve geçirin.

> [!NOTE]
> Konuşma SDK 'Sı yalnızca LUG v 2.0 uç noktalarını destekler.
> Bir v 2.0 URL 'SI deseninin kullanılması için örnek sorgu alanında bulunan v 3.0 uç nokta URL 'sini el ile değiştirmeniz gerekir.
> LUSıS v 2.0 uç noktaları şu iki desenden birini her zaman izler:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Amacı tanıma

`IntentRecognizer`Nesnesinden yöntemi çağıracağız `recognize_once()` . Bu yöntem, konuşma hizmetinin tanıma için tek bir tümcecik gönderdiğini ve bu ifadenin konuşmayı tanımayı durdur olarak belirlenmesinin ardından olduğunu bilmesini sağlar.

Bu kodu modelinizin altına ekleyin.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Tanıma sonuçlarını (veya hatalarını) görüntüleme

Tanınma sonucu konuşma hizmeti tarafından döndürüldüğünde, onunla ilgili bir şey yapmak isteyeceksiniz. Bu uygulamayı basit tutmaya ve sonucu konsola yazdıracağız.

Çağrın altına `recognize_once()` Bu kodu ekleyin.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kodunuzu denetleyin

Bu noktada, kodunuz şöyle görünmelidir.

> [!NOTE]
> Bu sürüme bazı açıklamalar ekledik.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Uygulamanızı derleyin ve çalıştırın

Örneği konsolundan veya IDE 'ınızdan çalıştırın:

```
python quickstart.py
```

Mikrofonunuzdan yapılan sonraki 15 saniyelik konuşma girişi tanınır ve konsol penceresinde günlüğe kaydedilir.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]