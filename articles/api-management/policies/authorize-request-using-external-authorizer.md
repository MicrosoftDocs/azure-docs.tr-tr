---
title: Örnek API yönetimi ilkesi-dış yetkilendirici kullanarak isteği yetkilendir
titleSuffix: Azure API Management
description: Azure API Management ilkesi örneği-yetkilendirmeyi, özel veya eski bir kimlik doğrulama/yetkilendirme mantığını kapsüllemek üzere dış yetkilendirmeyle nasıl kullandığını gösterir.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076241"
---
# <a name="authorize-requests-using-external-authorizer"></a>Dış yetkilendirici kullanarak istekleri yetkilendirme

Bu makalede, bir dış yetkilici özel kimlik doğrulama/yetkilendirme mantığını kullanarak API erişiminin güvenliğini nasıl güvence altına gösteren bir Azure API Management ilkesi örneği gösterilmektedir. Bir ilke kodu ayarlamak veya düzenlemek için, [Ilke ayarlama veya düzenleme](../set-edit-policies.md)bölümünde açıklanan adımları izleyin. Diğer örnekleri görmek için bkz. [ilke örnekleri](../policy-reference.md).

## <a name="policy"></a>İlke

Kodu **gelen** bloğa yapıştırın.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Sonraki adımlar

APıM ilkeleri hakkında daha fazla bilgi edinin:

+ [Erişim kısıtlama ilkeleri](../api-management-access-restriction-policies.md)
+ [İlke örnekleri](../policy-reference.md)