---
title: 'Hızlı başlangıç: konuşmayı tanıma, Python-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: chlandsi
ms.openlocfilehash: 2222f19ef4459d5ad3ccfa8a39fac6ab804b62e4
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226167"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurun ve boş bir proje oluşturun](../../../../quickstarts/setup-platform.md?tabs=python&pivots=programming-language-python)

## <a name="add-sample-code"></a>Örnek kod ekleme

1. `quickstart.py` dosyasını açın ve tüm kodu aşağıdakiyle değiştirin.

    ````python
    import azure.cognitiveservices.speech as speechsdk

    speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"

    def translate_speech_to_text():

        # Creates an instance of a speech translation config with specified subscription key and service region.
        # Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
        translation_config = speechsdk.translation.SpeechTranslationConfig(subscription=speech_key, region=service_region)

        # Sets source and target languages.
        # Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
        fromLanguage = 'en-US'
        translation_config.speech_recognition_language = fromLanguage
        translation_config.add_target_language('de')
        translation_config.add_target_language('fr')

        # Creates a translation recognizer using and audio file as input.
        recognizer = speechsdk.translation.TranslationRecognizer(translation_config=translation_config)

        # Starts translation, and returns after a single utterance is recognized. The end of a
        # single utterance is determined by listening for silence at the end or until a maximum of 15
        # seconds of audio is processed. It returns the recognized text as well as the translation.
        # Note: Since recognize_once() returns only a single utterance, it is suitable only for single
        # shot recognition like command or query.
        # For long-running multi-utterance recognition, use start_continuous_recognition() instead.
        print("Say something...")
        result = recognizer.recognize_once()

        # Check the result
        if result.reason == speechsdk.ResultReason.TranslatedSpeech:
            print("RECOGNIZED '{}': {}".format(fromLanguage, result.text))
            print("TRANSLATED into {}: {}".format('de', result.translations['de']))
            print("TRANSLATED into {}: {}".format('fr', result.translations['fr']))
        elif result.reason == speechsdk.ResultReason.RecognizedSpeech:
            print("RECOGNIZED: {} (text could not be translated)".format(result.text))
        elif result.reason == speechsdk.ResultReason.NoMatch:
            print("NOMATCH: Speech could not be recognized: {}".format(result.no_match_details))
        elif result.reason == speechsdk.ResultReason.Canceled:
            print("CANCELED: Reason={}".format(result.cancellation_details.reason))
            if result.cancellation_details.reason == speechsdk.CancellationReason.Error:
                print("CANCELED: ErrorDetails={}".format(result.cancellation_details.error_details))

    translate_speech_to_text()
    ````

1. Aynı dosyada, `YourSubscriptionKey` dizesini abonelik anahtarınızla değiştirin.

1. Dizeyi `YourServiceRegion` aboneliğinizle ilişkili [bölge](../../../../regions.md) ile değiştirin.

1. Yaptığınız değişiklikleri kaydedin `quickstart.py` .

## <a name="build-and-run-your-app"></a>Uygulamanızı derleyin ve çalıştırın

1. Örneği konsolundan veya IDE 'ınızdan çalıştırın:

   ```
   python quickstart.py
   ```

1. İngilizce bir deyim ya da cümle söyleyin. Uygulama, konuşmanızı konuşma hizmetine iletir, bu da metin (Bu durumda, Fransızca ve Almanca) olarak metne dönüştürür. Konuşma hizmeti daha sonra metni görüntülenmek üzere uygulamaya geri gönderir.

   ```
   Say something...
   RECOGNIZED 'en-US': What's the weather in Seattle?
   TRANSLATED into 'de': Wie ist das Wetter in Seattle?
   TRANSLATED into 'fr': Quel temps fait-il à Seattle ?
   ```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]
