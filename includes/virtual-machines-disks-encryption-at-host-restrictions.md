---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231020"
---
- , Ultra diskleri desteklemez.
- , VM 'leriniz/sanal makine ölçek kümelerinde Azure disk şifrelemesi (BitLocker/VM kullanan Konuk VM şifrelemesi) etkin ise etkinleştirilemez.
- Azure disk şifrelemesi, konakta şifreleme etkin olan disklerde etkinleştirilemez.
- Şifreleme, var olan sanal makine ölçek kümesi üzerinde etkinleştirilebilir. Ancak, yalnızca şifrelemeyi etkinleştirdikten sonra oluşturulan yeni VM 'Ler otomatik olarak şifrelenir.
- Mevcut VM 'Lerin şifrelenmesi için serbest bırakılmasının ve yeniden ayrılması gerekir.