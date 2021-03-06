---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048275"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. **Tüm kaynaklar** ' a gidin ve oluşturduğunuz sanal WAN ' ı seçin, ardından sol taraftaki menüden **Kullanıcı VPN yapılandırması** ' nı seçin.
1. **Kullanıcı VPN yapılandırmaları** sayfasında, sayfanın üst kısmındaki + **Yeni Kullanıcı VPN yapılandırması oluştur** sayfasını açmak için **+ Kullanıcı VPN yapılandırması oluştur** ' u seçin.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Kullanıcı VPN yapılandırması sayfasının ekran görüntüsü.":::

1. **Temel bilgiler** sekmesinde, **örnek ayrıntıları**' nın altında, VPN yapılandırmanıza atamak istediğiniz **adı** girin.
1. **Tünel türü** için, açılan listeden istediğiniz tünel türünü seçin. Tünel türü seçenekleri şunlardır: **ıKEV2 VPN**, **OpenVPN** ve **OpenVPN ve IKEv2**.
1. Seçtiğiniz tünel türüne karşılık gelen aşağıdaki adımları kullanın. Tüm değerler belirtilmişse, yapılandırmayı oluşturmak için **gözden geçir + oluştur**' a ve ardından **Oluştur** ' a tıklayın.

   **IKEv2 VPN**

   * **Gereksinimler:** **Ikev2** tünel türünü seçtiğinizde, bir kimlik doğrulama yöntemi seçmenizi yönlendiren bir ileti görürsünüz. Ikev2 için yalnızca bir kimlik doğrulama yöntemi belirtebilirsiniz. Azure sertifikası, Azure Active Directory veya RADIUS tabanlı kimlik doğrulaması seçebilirsiniz.

   * **IPSec özel parametreleri:** IKE Aşama 1 ve ıKE aşama 2 parametrelerini özelleştirmek için IPsec anahtarını **özel** olarak değiştirin ve parametre değerlerini seçin. Özelleştirilebilir parametreler hakkında daha fazla bilgi için bkz. [Özel IPSec](../articles/virtual-wan/point-to-site-ipsec.md) makalesi.

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="IPSec 'in özel olarak geçiş ekran görüntüsü.":::

   * **Kimlik doğrulama:** Kimlik doğrulama yöntemine ilerlemek için sayfanın alt kısmındaki **İleri** ' ye tıklayarak kullanmak istediğiniz kimlik doğrulama mekanizmasına gidin veya sayfanın en üstündeki uygun sekmeye tıklayın. Yöntemi seçmek için anahtarı **Evet** olarak değiştirin.

     Bu örnekte, RADIUS kimlik doğrulaması seçilidir. RADIUS tabanlı kimlik doğrulaması için, ikincil bir RADIUS sunucusu IP adresi ve sunucu parolası sağlayabilirsiniz.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="IKE 'nin ekran görüntüsü.":::

   **OpenVPN**

   * **Gereksinimler:** **OpenVPN** tüneli türünü seçtiğinizde, bir kimlik doğrulama mekanizması seçmenizi yönlendiren bir ileti görürsünüz. Tünel türü olarak OpenVPN seçilirse, birden çok kimlik doğrulama yöntemi belirtebilirsiniz. Herhangi bir Azure sertifikası, Azure Active Directory veya RADIUS tabanlı kimlik doğrulaması alt kümesini seçebilirsiniz. RADIUS tabanlı kimlik doğrulaması için, ikincil bir RADIUS sunucusu IP adresi ve sunucu parolası sağlayabilirsiniz.

   * **Kimlik doğrulama:** Kimlik doğrulama yöntemine ilerlemek için sayfanın alt kısmındaki **İleri** ' ye tıklayarak kullanmak istediğiniz kimlik doğrulama yöntemine gidin veya sayfanın en üstündeki uygun sekmeye tıklayın.
   Seçmek istediğiniz her yöntem için, anahtarı **Evet** olarak değiştirin ve uygun değerleri girin.

     Bu örnekte Azure Active Directory seçilidir.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="OpenVPN sayfasının ekran görüntüsü.":::
