---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "83343363"
---
Bir işlev uygulaması tarafından şu anda kullanılan çalışma zamanı sürümünü görüntülemek ve güncelleştirmek için aşağıdaki yordamı kullanın.

1. [Azure Portal](https://portal.azure.com), işlev uygulamanıza gidin.

1. **Ayarlar** altında **yapılandırma**' yı seçin. **İşlev çalışma zamanı ayarları** sekmesinde, **çalışma zamanı sürümünü** bulun. Belirli çalışma zamanı sürümünü aklınızda edin. Aşağıdaki örnekte, sürümü olarak ayarlanır `~3` .

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Çalışma zamanı sürümünü görüntüleyin." border="true":::

1. İşlev uygulamanızı sürüm 1. x çalışma zamanına sabitlemek için, **çalışma zamanı sürümü** altında **~ 1** seçeneğini belirleyin. Uygulamanızda işlevleriniz olduğunda bu anahtar devre dışıdır.

1. Çalışma zamanı sürümünü değiştirdiğinizde, **genel bakış** sekmesine dönün ve uygulamayı yeniden başlatmak Için **Yeniden Başlat** ' ı seçin.  Sürüm 1. x çalışma zamanı üzerinde çalışan işlev uygulaması yeniden başlatılır ve işlev oluştururken sürüm 1. x şablonları kullanılır.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="İşlev uygulamanızı yeniden başlatın." border="true":::
