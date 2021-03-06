---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d06a6ecd8af16da3e6df21e984fbf6a727fbc27e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105347"
---
### <a name="upload-your-model"></a>Modelinizi karşıya yükleyin

Henüz bir nesne bağlantıları modeliniz yoksa, [model](../articles/object-anchors/quickstarts/get-started-model-conversion.md) oluşturma ' daki yönergeleri izleyerek bir tane oluşturun. Ardından buraya geri dönün.

HoloLens 'i Windows cihaz portalına bağladığınıza göre, uygulamanın kullanması için bir model yüklemek üzere aşağıdaki adımları izleyin:

1. Windows cihaz portalında, **System > dosya Gezgini ' ne > LocalAppData**' a gidin. Burada, uygulamanızı uygulamalar listesinde görmeniz gerekir.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Dosya Gezgini":::

2. Uygulamanızı açın ve `LocalState` klasöre tıklayın.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="LocalState klasörünü açın":::

3. Model dosyasını `LocalState` klasöre yükleyin.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="modeli portala yükleme":::

    Uygulamayı HoloLens 'ten yeniden başlatın. Artık modelle eşleşen nesneleri tespit edebilirsiniz.