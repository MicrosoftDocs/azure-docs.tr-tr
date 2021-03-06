---
title: Azure Stack Edge Pro sıralaması sorunlarını gidermek için Azure portal kullanma | Microsoft Docs
description: Azure Stack Edge Pro sıralaması sorunlarını nasıl giderebileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e074043cb685f60027d3c09ae7ad8dc17dded0df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443412"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>Azure Stack Edge Pro sipariş sorunlarını giderme

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede Azure Stack Edge Pro sıralaması sorunlarını giderme işleminin nasıl giderileceği açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Sorunları sıralama sorunlarını giderme

## <a name="unsupported-subscription-or-region"></a>Desteklenmeyen abonelik veya bölge

**Hata açıklaması:** Azure portal, şu hatayı alırsanız:

*Seçilen abonelik veya bölge desteklenmiyor. Farklı bir abonelik veya bölge seçin.*

![Desteklenmeyen abonelik veya bölge](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Önerilen çözüm:**  [Microsoft kurumsal anlaşma (EA)](https://azure.microsoft.com/overview/sales-number/), [bulut çözümü sağlayıcısı (CSP)](/partner-center/azure-plan-lp)veya [Microsoft Azure sponsorluğu](https://azure.microsoft.com/offers/ms-azr-0036p/)gibi desteklenen bir abonelik kullandığınızdan emin olun. Kullandıkça Öde abonelikleri desteklenmez. Daha fazla bilgi için [Azure Stack Edge kaynak önkoşulları](azure-stack-edge-deploy-prep.md#prerequisites)bölümüne bakın.

Microsoft 'un, büyük/küçük harfe göre abonelik türü yükseltmesine izin verme ihtimali vardır. İhtiyaçlarınızı anlayabilmeleri ve bu limitleri uygun şekilde ayarlayabilmeleri için [Microsoft desteğine](https://azure.microsoft.com/support/options/) başvurun.

## <a name="selected-subscription-type-not-supported"></a>Seçilen abonelik türü desteklenmiyor

**Hata:** Bir EA, CSP veya sponsorlu aboneliğiniz var ve şu hatayı alırsınız:

*Seçilen abonelik türü desteklenmiyor. Desteklenen bir abonelik kullandığınızdan emin olun. [Daha fazla bilgi edinin](azure-stack-edge-deploy-prep.md#prerequisites). Desteklenen bir abonelik türü kullanıyorsanız, sağlayıcının kayıtlı olduğundan emin olun `Microsoft.DataBoxEdge` . Nasıl kaydedileceği hakkında bilgi için bkz. [kayıt kaynak sağlayıcısı](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Önerilen çözüm:** Azure Stack Edge kaynak sağlayıcınızı kaydetmek için aşağıdaki adımları izleyin:

1. Azure Portal, **giriş**  >  **abonelikleri**' ne gidin.

2. Cihazınızı sıralamak için kullanacağınız aboneliği seçin.

3. **Kaynak sağlayıcıları** ' nı seçin ve ardından **Microsoft. databoxedge** için arama yapın.

    ![Kaynak sağlayıcısını kaydetme](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Kaynak sağlayıcısını kaydetmek için sahip veya katkıda bulunan erişiminiz yoksa, şu hatayı görürsünüz: *Abonelik &lt; aboneliği adının &gt; kaynak sağlayıcıları kaydetme Izni yok: Microsoft. databoxedge.*

Daha fazla bilgi için bkz. [kaynak sağlayıcılarını kaydetme](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Abonelik için Microsoft. DataBoxEdge kaydedilmedi

**Hata:** Azure portal, Azure Stack Edge Pro veya Data Box Gateway için kullanılacak bir abonelik seçersiniz ve şu hatayı alırsınız:

*Kaynak sağlayıcıları: Microsoft. DataBoxEdge abonelik abonelik adı için kayıtlı değil &lt; &gt; ve abonelik &lt; abonelik adı &gt; için bir kaynak sağlayıcısı kaydetme izniniz* yok.

**Önerilen çözüm:** Abonelik erişiminizi yükseltin veya kaynak sağlayıcısını kaydetmek için sahip veya katkıda bulunan erişimi olan birini bulun.

## <a name="resource-disallowed-by-policy"></a>İlke tarafından izin verilmeyen kaynak

**Hata:** Azure portal, bir kaynak sağlayıcısı kaydetmeyi ve aşağıdaki hatayı almanızı deneirsiniz:

*Kaynak &lt; Kaynak adına &gt; ilke tarafından izin verilmedi. (Code: RequestDisallowedByPolicy). Girişim: genel istenmeyen kaynak türlerini reddetme. İlke: izin verilmeyen kaynak türleri.*

**Önerilen çözüm:** Bu hata, kaynak oluşturmayı engelleyen mevcut bir Azure ilkesi nedeniyle oluşur. Azure ilkeleri, Azure kaynaklarını kullanırken veya oluştururken uyumluluk sağlamak için bir kuruluşun sistem yöneticisi tarafından ayarlanır. Böyle bir ilke Azure Stack uç kaynağı oluşturmayı engelliyorsa, Azure ilkenizi düzenlemek için sistem yöneticinize başvurun.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stack Edge Pro sorunlarınızı giderme](azure-stack-edge-troubleshoot.md)hakkında daha fazla bilgi edinin.