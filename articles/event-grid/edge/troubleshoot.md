---
title: Sorun giderme-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid sorun giderme.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171355"
---
# <a name="common-issues"></a>Sık Karşılaşılan Sorunlar

Ortamınızdaki IoT Edge Azure Event Grid kullanarak sorunlarla karşılaşırsanız sorun giderme ve çözümleme için bu makaleyi kılavuz olarak kullanın.

## <a name="view-event-grid-module-logs"></a>Event Grid modül günlüklerini görüntüle

Sorun gidermek için Event Grid modül günlüklerine erişmeniz gerekebilir. Bunu yapmak için, modülün dağıtıldığı VM 'de aşağıdaki komutu çalıştırın:

Windows 'ta,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Linux 'ta,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>HTTPS istekleri yapılamıyor

* İlk olarak Event Grid modülün **gelen: serverAuth: tlsPolicy** **Strict** veya **Enabled** olarak ayarlanmış olduğundan emin olun.

* Modülün modüle iletişimler iletişim varsa, bağlantı noktası **4438** ' de çağrıyı yaptığınızdan ve modülün adı dağıtılan ile eşleştiğinden emin olun. 

  Örneğin, Event Grid modülü **eventgridmodule** adı ile DAĞıTıLMıŞSA, URL 'niz olmalıdır **https://eventgridmodule:4438** . Büyük/küçük harf ve bağlantı noktası numarasının doğru olduğundan emin olun.
    
* IoT olmayan modülden ise, dağıtım sırasında Event Grid bağlantı noktasının konak makineye eşlendiğinden emin olun.

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>HTTP istekleri yapılamıyor

* İlk olarak Event Grid modülün **gelen: serverAuth: tlsPolicy** özelliğinin **etkin** veya **devre dışı** olarak ayarlandığını doğrulayın.

* Modülün modüle iletişimler iletişim varsa, bağlantı noktası **5888** ' de çağrıyı yaptığınızdan ve modülün adı dağıtılan ile eşleştiğinden emin olun. 

  Örneğin, Event Grid modülü **eventgridmodule** adı ile DAĞıTıLMıŞSA, URL 'niz olmalıdır **http://eventgridmodule:5888** . Büyük/küçük harf ve bağlantı noktası numarasının doğru olduğundan emin olun.
    
* IoT olmayan modülden ise, dağıtım sırasında Event Grid bağlantı noktasının konak makineye eşlendiğinden emin olun.

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Sertifika zinciri, güvenilmeyen bir yetkili tarafından verilmiş

Varsayılan olarak, Event Grid modülü, IoT Edge güvenlik arka plan programı tarafından verilen sertifikaları kimlik doğrulamak üzere yapılandırılmıştır. İstemcinin bu zincire kök sertifika sunduğunuzdan emin olun.

İçindeki **ıotsecurity** sınıfı, [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) IoT Edge güvenlik arka plan programından sertifikaların nasıl alınacağını gösterir ve bunu giden çağrıları yapılandırmak için kullanır.

Üretim dışı ortamındaysanız, istemci kimlik doğrulamasını kapatma seçeneğiniz vardır. Bunun nasıl yapılacağı hakkında ayrıntılı bilgi için [güvenlik ve kimlik doğrulama](security-authentication.md) bölümüne bakın.

## <a name="debug-events-not-received-by-subscriber"></a>Abone tarafından alınmayan hata ayıklama olayları

Bunun tipik nedenleri şunlardır:

* Olay hiçbir şekilde başarıyla nakledilmedi. Event Grid modülüne bir olay gönderilirken 200 (Tamam) HTTP StatusCode işlemi alınmalıdır.

* Doğrulanacak olay aboneliğini denetleyin:
    * Uç nokta URL 'SI geçerli
    * Abonelikteki tüm filtreler olayın "bırakılmış" olmasına neden olmaz.

* Abone modülünün çalışıp çalışmadığını doğrulama

* Event Grid modülünün dağıtıldığı VM 'de oturum açın ve günlüklerini görüntüleyin.

* **Aracı: logDeliverySuccess = true** ayarlayarak ve Event Grid modülünü yeniden dağıtarak ve isteği yeniden denemeden teslim günlüğü başına ' yı açın. İşlem başına günlük tutmayı açmak, hata ayıklamanın yeniden dengeleyebilmesi için iş çıkarma ve gecikme süresini etkileyebilir **: logDeliverySuccess = false**  ve Event Grid modülü yeniden dağıtılıyor.

* Ölçümleri ayarlayarak ölçümleri açın **: reportertype = konsol** ve Event Grid modülünü yeniden dağıtın. Bundan sonra herhangi bir işlem, daha fazla hata ayıklamak için kullanılabilen Event Grid modülünün konsolunda, ölçümler günlüğe kaydediliyor. Ölçümleri yalnızca hata ayıklama için ve bir kez tamamladıktan sonra **ölçüm** ayarları yaparak devre dışı bırakmak için ' i etkinleştirmek ve Event Grid modülünü yeniden dağıtmak için önerimiz.

## <a name="next-steps"></a>Sonraki adımlar

Her türlü sorunu bildirin, IoT Edge üzerinde Event Grid kullanma ile ilgili öneriler [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .