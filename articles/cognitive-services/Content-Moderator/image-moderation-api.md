---
title: Görüntü denetleme-Content Moderator
titleSuffix: Azure Cognitive Services
description: Yetişkin ve kcy içeriği için orta görüntülerde Content Moderator makine yardımlı görüntü denetlemesi ve Kullanıcı döngüsü Inceleme aracını kullanın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85801004"
---
# <a name="learn-image-moderation-concepts"></a>Görüntü denetleme kavramlarını öğrenin

Yetişkin ve kcy içeriği için orta görüntülerde Content Moderator makine destekli görüntü denetleme ve [Gözden geçirme aracını](Review-Tool-User-Guide/human-in-the-loop.md) kullanın. Metin içeriğinin görüntülerini tarayın ve bu metni ayıklayın ve yüzleri algılayın. Görüntüleri özel listelerle eşleştirebilir ve daha fazla işlem gerçekleştirebilirsiniz.

## <a name="evaluating-for-adult-and-racy-content"></a>Yetişkin ve kcy içeriği değerlendirmesi

**Değerlendir** işlemi, 0 ile 1 arasında bir güvenirlik puanı döndürür. Ayrıca, true veya false değerine eşit Boole verileri döndürür. Bu değerler, görüntüde olası yetişkin veya kcy içeriği içerip içermediğini tahmin eder. Bir API 'yi yansımanıza (dosya veya URL) çağırdığınızda döndürülen yanıt aşağıdaki bilgileri içerir:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified`, belirli durumlarda açık saçık veya yetişkinlere yönelik olarak değerlendirilebilecek görüntülerin mevcut olma olasılığını gösterir.
> - `isImageRacyClassified`, belirli durumlarda davetkar veya yetişkinlere yönelik olarak değerlendirilebilecek görüntülerin mevcut olma olasılığını gösterir.
> - Puanlar 0 ile 1 arasındadır. Puan arttıkça, modelin daha yüksek olması kategorinin uygulanabilir olabileceğini tahmin edilir. Bu önizleme el ile kodlanmış sonuçlar yerine istatistiksel bir modeli kullanır. Her kategorinin gereksinimlerinize göre nasıl hizalanacağını öğrenmek için kendi içeriklerinizi test etmenizi öneririz.
> - Boole değerleri, iç puan eşiklerine bağlı olarak doğru ya da yanlış şeklindedir. Müşteriler, bu değerin kullanılıp kullanılmayacağını ya da içerik ilkelerine bağlı olarak özel eşiklere karar vermesini değerlendirmelidir.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Optik karakter tanıma ile metin algılama (OCR)

**Optik karakter tanıma (OCR)** işlemi, bir görüntüdeki metin içeriğinin varlığını tahmin eder ve diğer kullanımlar arasında metin düzenlemesi için ayıklar. Dili belirtebilirsiniz. Bir dil belirtmezseniz, algılama varsayılan olarak Ingilizce 'Dir.

Yanıt aşağıdaki bilgileri içerir:
- Özgün metin.
- Algılanan metin öğeleri, bunların güven puanlarıyla birlikte.

Örnek ayıklama:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>Yüzleri algılama

Yüzeyleri algılama, görüntülerde yüzler gibi kişisel verileri algılamaya yardımcı olur. Olası yüzeyleri ve her görüntüde olası yüz sayısını tespit edersiniz.

Yanıt şu bilgileri içerir:

- Yüz sayısı
- Algılanan yüzlerin konumları listesi

Örnek ayıklama:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>Özel listeler oluşturma ve yönetme

Birçok çevrimiçi topluluk içinde, kullanıcılar görüntüleri veya diğer içerik türlerini karşıya yükledikten sonra, rahatsız edici öğeler şu günlerde, haftalarda ve aylarda birden çok kez paylaşılabilir. Aynı görüntünün ve hatta birden çok yerden görüntünün küçük bir yerinde değiştirilmiş sürümlerinin sürekli taranması ve filtrelenmesi maliyetleri pahalı ve hata durumunda olabilir.

Aynı görüntüyü birden çok kez Moderasyon yapmak yerine, rahatsız edici görüntüleri özel engellenen içerik listenize eklersiniz. Bu şekilde, içerik denetleme sisteminiz gelen görüntüleri özel listelerinizle karşılaştırır ve diğer işlemleri de sonlandırır.

> [!NOTE]
> Liste sayısı üst sınırı, her biri **10.000 görüntüyü aşmamak** kaydıyla **5 görüntü listesidir**.
>

Content Moderator, özel görüntülerin listesini yönetmeye yönelik işlemler içeren, tamamlanmış bir [görüntü listesi yönetim API 'si](try-image-list-api.md) sağlar. [Görüntü LISTELERI API konsolu](try-image-list-api.md) ile başlayın ve REST API kod örneklerini kullanın. Visual Studio ve C# hakkında bilginiz varsa, [görüntü listesi .net hızlı](image-lists-quickstart-dotnet.md) başlangıcı ' nı da inceleyin.

## <a name="matching-against-your-custom-lists"></a>Özel listelerinize karşı eşleme

Eşleştirme işlemi, gelen görüntülerin, liste işlemleri kullanılarak oluşturulup yönetilen özel listelerden herhangi birine karşı belirsiz şekilde eşleşmesini sağlar.

Bir eşleşme bulunursa, işlem eşleşen görüntünün tanımlayıcısını ve denetleme etiketlerini döndürür. Yanıt şu bilgileri içerir:

- Puanı Eşleştir (0 ile 1 arasında)
- Eşleşen resim
- Resim etiketleri (önceki denetleme sırasında atanır)
- Görüntü etiketleri

Örnek ayıklama:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>Gözden geçirme aracı

Daha fazla bilgi sahibi olmak için Content Moderator [Gözden geçirme aracını](Review-Tool-User-Guide/human-in-the-loop.md) ve API 'sini kullanarak insan moderatlarınız için gözden geçirme sonuçlarını ve içeriğini kullanın. Makine tarafından atanan etiketleri gözden geçirir ve son kararları onaylanır.

![İnsan denetimciler için görüntü incelemesi](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Sonraki adımlar

[Görüntü denetleme API konsolunun](try-image-api.md) sürücüsünü test edin ve REST API kod örneklerini kullanın. Ayrıca, insan incelemelerini ayarlamayı öğrenmek için [incelemeleri, iş akışlarını ve işleri](./review-api.md) inceleyin.
