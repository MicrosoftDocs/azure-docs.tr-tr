---
title: Azure Notification Hubs ile kullanıcılara platformlar arası bildirimler gönderme (ASP.NET)
description: Tüm platformları hedefleyen platformdan bağımsız bir bildirim olan tek bir istekte göndermek için Notification Hubs şablonlarını nasıl kullanacağınızı öğrenin.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f8f9357fff4773467dd93046ece8533514eb444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92313897"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Azure Notification Hubs platformlar arası bildirimler gönderin

Bu öğretici, önceki öğreticide derleme [yaparak Azure Notification Hubs kullanarak belirli kullanıcılara bildirim gönderebilir]. Bu öğreticide, belirli bir kimliği doğrulanmış kullanıcıya kayıtlı tüm cihazlara nasıl bildirim gönderinin açıklanmaktadır. Bu yaklaşım, desteklenen her istemci platformuna bildirim göndermek için birden çok istek gerektirdi. Azure Notification Hubs, belirli bir cihazın nasıl bildirim almak istediğini belirtebileceğiniz şablonları destekler. Bu yöntem platformlar arası bildirimlerin gönderilmesini basitleştirir.

Bu makalede, tüm platformları hedefleyen bir bildirim göndermek için şablonlardan nasıl yararlanulacağı gösterilmektedir. Bu makalede, platformdan bağımsız bir bildirim göndermek için tek bir istek kullanılmaktadır. Şablonlar hakkında daha ayrıntılı bilgi için bkz. [Notification Hubs genel bakış][Templates].

> [!IMPORTANT]
> Windows Phone projeler 8,1 ve önceki sürümleri Visual Studio 2019 ' de desteklenmez. Daha fazla bilgi için bkz. [Visual Studio 2019 Platform hedefleme ve uyumluluk](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Notification Hubs, bir cihaz aynı etiketi kullanarak birden çok şablonu kaydedebilir. Bu durumda, etiketi hedefleyen bir gelen ileti, her şablon için bir tane olmak üzere cihaza birden çok bildirim teslim edildiğinde oluşur. Bu işlem, bir Windows Mağazası uygulamasında hem rozet hem de bildirim olarak aynı iletiyi birden çok görsel bildirimde görüntülemenizi sağlar.

## <a name="send-cross-platform-notifications-using-templates"></a>Şablonları kullanarak platformlar arası bildirimler gönderme

Bu bölüm, [Azure Notification Hubs öğreticisini kullanarak belirli kullanıcılara bildirim gönderme] bölümünde oluşturduğunuz örnek kodu kullanır. [GitHub 'dan tüm örneği indirebilirsiniz](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Şablonları kullanarak platformlar arası bildirimler göndermek için aşağıdakileri yapın:

1. **Çözüm Gezgini**'Deki Visual Studio 'Da, **denetleyiciler** klasörünü genişletin ve ardından *registercontroller. cs* dosyasını açın.

1. `Put`Yeni bir kayıt oluşturan yöntemdeki kod bloğunu bulun ve ardından `switch` içeriği şu kodla değiştirin:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Bu kod, yerel kayıt yerine bir şablon kaydı oluşturmak için platforma özgü yöntemi çağırır. Şablon kayıtları yerel kayıtlardan türetiğinden, var olan kayıtları değiştirmenize gerek yoktur.

1. **Çözüm Gezgini**, **denetleyiciler** klasöründe, **notificationscontroller. cs** dosyasını açın. `Post` yöntemini aşağıdaki kod ile değiştirin:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Bu kod, aynı anda tüm platformlara bir bildirim gönderir. Yerel yük belirtmezsiniz. Notification Hubs, kayıtlı şablonlarda belirtildiği gibi, belirtilen etiket değeri ile her cihaza doğru yükü oluşturur ve gönderir.

1. Web API projenizi yeniden yayımlayın.

1. Kaydın başarılı olduğunu doğrulamak için istemci uygulamasını yeniden çalıştırın.

1. İsteğe bağlı olarak, istemci uygulamasını ikinci bir cihaza dağıtın ve uygulamayı çalıştırın. Her cihazda bir bildirim görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticiyi tamamladığınıza göre, bu makalelerde Notification Hubs ve şablonlar hakkında daha fazla bilgi edinebilirsiniz:

* Şablonları kullanmaya yönelik farklı senaryolar için, [Evrensel Windows platformu uygulamaları çalıştıran belirli Windows cihazlarına anında iletme bildirimleri][Use Notification Hubs to send breaking news] öğreticisine bakın.
* Şablonlar hakkında daha ayrıntılı bilgi için bkz. [Notification Hubs genel bakış][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Azure Notification Hubs kullanarak belirli kullanıcılara bildirimler gönderme]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)