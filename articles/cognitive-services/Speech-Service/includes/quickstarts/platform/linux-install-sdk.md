---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188474"
---
## <a name="install-speech-sdk"></a>Konuşma SDK'sını yükleme

Linux için Konuşma SDK'sı, gerek 64 bit gerekse 32 bit uygulamalar derlemek için kullanılabilir. Gerekli kitaplıklar ve üst bilgi dosyaları, öğesinden bir bataklık dosyası olarak indirilebilir https://aka.ms/csspeech/linuxbinary .

SDK'yı aşağıda gösterildiği gibi indirin ve yükleyin:

1. Konuşma SDK'sı dosyalarının ayıklanacağı bir dizin seçin ve `SPEECHSDK_ROOT` ortam değişkenini bu dizine işaret edecek şekilde ayarlayın. Bu değişken, ileride komutlarda bu dizine başvurmayı kolaylaştırır. Örneğin, giriş dizininizdeki `speechsdk` dizinine başvurmak istiyorsanız, şunun gibi bir komut kullanın:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Henüz yoksa dizini oluşturun.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Konuşma SDK'sı ikili dosyalarını içeren `.tar.gz` arşivini indirin ve açın:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Açılan paketin en üst düzey dizininin içeriğini doğrulayın:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Dizinde üçüncü taraf bildirim ve lisans dosyaları, ayrıca üstbilgi (`.h`) dosyalarını içeren bir `include` dizini ve kitaplıkları içeren bir `lib` dizini olmalıdır.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]