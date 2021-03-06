---
title: Azure Lab Services için güvenlik duvarı ayarları
description: Güvenlik duvarı kurallarına eklenmek üzere bir laboratuvardaki sanal makinelerin genel IP adresini ve bağlantı noktası numarası aralığını belirlemeyi öğrenin.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445857"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure Lab Services için güvenlik duvarı ayarları

Her bir kuruluş veya okul, kendi ağını gereksinimlerine en uygun şekilde ayarlar.  Bazen bu, kendi ağı dışındaki makinelere Uzak Masaüstü Protokolü (RDP) veya Secure Shell (SSH) bağlantılarını engelleyen güvenlik duvarı kurallarını ayarlamayı içerir.  Azure Lab Services ortak bulutta çalıştığından, öğrencilerin kampüs ağdan bağlanırken VM 'lerine erişmesine izin vermek için bazı ek yapılandırmalar gerekebilir.

Her laboratuvar tek genel IP adresi ve birden çok bağlantı noktası kullanır.  Hem şablon VM hem de öğrenci VM 'Leri olan tüm VM 'Ler, bu genel IP adresini kullanacaktır.  Genel IP adresi, laboratuvarın ömrü boyunca değişmeyecektir.  Ancak, her VM farklı bir bağlantı noktası numarasına sahip olur.  Bağlantı noktası numaraları 49152 ile 65535 arasında değişir.  Genel IP adresi ve bağlantı noktası numarası birleşimi, eğitmeni ve öğrencileri doğru VM 'ye bağlamak için kullanılır.  Bu makalede, bir laboratuvar tarafından kullanılan belirli genel IP adresinin nasıl bulunacağı ele alınacaktır.  Bu bilgiler, öğrencilerin VM 'lerine erişebilmesi için gelen ve giden güvenlik duvarı kurallarını güncelleştirmek üzere kullanılabilir.

>[!IMPORTANT]
>Her laboratuvarın farklı bir genel IP adresi olur.

## <a name="find-public-ip-for-a-lab"></a>Laboratuvar için genel IP bulma

Her bir laboratuvarın genel IP adresleri, Laboratuvar Hizmetleri Laboratuvarı hesabının **Tüm laboratuvarları** sayfasında listelenir.  **Tüm Labs** sayfasını bulma hakkında yönergeler için bkz. [laboratuvarları laboratuvar hesabında görüntüleme](manage-labs.md#view-labs-in-a-lab-account).  

> [!div class="mx-imgBorder"]
> ![Tüm Labs sayfası](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Laboratuvarınızın şablon makinesi henüz yayınlanmamışsa genel IP adresini görmezsiniz.

## <a name="conclusion"></a>Sonuç

Artık laboratuvarın genel IP adresini biliyoruz.  Genel IP adresi ve 49152-65535 numaralı bağlantı noktası aralığı için kuruluşun güvenlik duvarı için gelen ve giden kuralları oluşturulabilir.  Kurallar güncelleştirildikten sonra, öğrenciler ağ güvenlik duvarı erişimi engellenmeksizin VM 'lerine erişebilir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Laboratuvar oluşturucunun laboratuvar konumunu seçmesine izin verme](allow-lab-creator-pick-lab-location.md)
- [Laboratuvarınızın ağını eş bir sanal ağla bağlama](how-to-connect-peer-virtual-network.md)
- [Laboratuvara paylaşılan görüntü Galerisi iliştirme](how-to-attach-detach-shared-image-gallery.md)
- [Laboratuvar sahibi olarak Kullanıcı ekleme](how-to-add-user-lab-owner.md)
- [Laboratuvar için güvenlik duvarı ayarlarını görüntüleme](how-to-configure-firewall-settings.md)
- [Laboratuvar için diğer ayarları yapılandırma](how-to-configure-lab-accounts.md)
