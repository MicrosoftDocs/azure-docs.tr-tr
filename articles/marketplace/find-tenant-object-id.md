---
title: Azure Marketi 'nde kiracı KIMLIĞI, nesne KIMLIĞI ve iş ortağı ilişki ayrıntılarını bulma
description: Azure Marketi 'nde bir abonelik KIMLIĞININ kiracı KIMLIĞINI, nesne KIMLIĞINI ve iş ortağı ilişki ayrıntılarını bulma.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109355"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Kiracı KIMLIĞI, nesne KIMLIĞI ve iş ortağı ilişki ayrıntılarını bulun

Bu makalede, kiracı KIMLIĞI, nesne KIMLIĞI ve iş ortağı ilişki ayrıntılarının ilgili abonelik kimlikleriyle birlikte nasıl bulunacağı açıklanır.

Hata ayıklama yardımı için kullanmak üzere Azure Cloud Shell içindeki bu öğelerin ekran görüntülerini almanız gerekiyorsa, [hata ayıklama Için kiracı, nesne ve Iş ortağı kimliği Ilişkilendirmesini bulun](#find-ids-for-debugging).

>[!Note]
> Yalnızca bir aboneliğin sahibi bu adımları gerçekleştirmek için ayrıcalıklara sahiptir.

## <a name="find-tenant-id"></a>Kiracı KIMLIĞINI bul

1. [Azure Portal](https://ms.portal.azure.com/) gidin.
2. **Azure Active Directory** seçin.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Azure portal Azure Active Directory simgesi.":::

3. **Genel bakış**'ı seçin. Kiracı KIMLIĞINIZ **temel bilgiler** altında görünmelidir.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Azure portal grupları seçin.":::

## <a name="find-subscriptions-and-roles"></a>Abonelikleri ve rolleri bulma

1. Azure portal gidin ve yukarıdaki 1. ve 2. adımlarda belirtildiği gibi **Azure Active Directory** seçin.
2. **Abonelikler**'i seçin.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Azure portal içindeki abonelikler simgesi.":::

3. Abonelikleri ve rolleri görüntüleyin.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Azure portal içindeki abonelikler ekranı.":::

## <a name="find-partner-id"></a>Iş ortağı KIMLIĞINI bul

1. Önceki bölümde açıklandığı gibi Abonelikler sayfasına gidin.
2. Bir abonelik seçin.
3. **Faturalandırma** bölümünde **iş ortağı bilgileri**' ni seçin.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Sol gezinti menüsündeki iş ortağı bilgileri.":::

## <a name="find-user-object-id"></a>Kullanıcı bul (nesne KIMLIĞI)

1. Uygun yönetici haklarıyla, istenen Kiracıdaki bir hesapla [Office 365 Yönetici portalında](https://portal.office.com/adminportal/home) oturum açın.
2. Sol taraftaki menüde, alttaki **Yönetim Merkezleri** bölümünü genişletin ve ardından Azure Active Directory seçeneğini seçerek yönetici konsolunu yeni bir tarayıcı penceresinde başlatın.
3. **Kullanıcılar**’ı seçin.
4. Kullanıcı hesabının profil bilgilerini görüntülemek için, istenen kullanıcıya gidin veya arama yapın, ardından hesap adını seçin.
5. Nesne KIMLIĞI sağ taraftaki kimlik bölümünde bulunur.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory Yönetim Merkezi.":::

6. Sol menüdeki **erişim denetimi (IAM)** öğesini seçip **rol atamaları**' nı seçerek **rol atamaları** bulun.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Azure kaynakları için rol atamaları.":::

## <a name="find-ids-for-debugging"></a>Hata ayıklama için kimlikleri bul

Bu bölümde, hata ayıklama amacıyla kiracı, nesne ve iş ortağı KIMLIĞI ilişkilendirmesini bulma açıklanmaktadır.

1. [Azure Portal](https://ms.portal.azure.com/) gidin.
2. Sağ üst köşedeki PowerShell simgesini seçerek Azure Cloud Shell açın.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Ekranın sağ üst kısmındaki PowerShell simgesi.":::

3. **PowerShell**' i seçin.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="PowerShell bağlantısını seçin.":::

4. İş ortağının bağlandığı birini seçmek için **abonelik** kutusunu seçin ve ardından **depolama oluşturun**. Bu tek seferlik bir eylemdir; zaten depolama ayarladıysanız, sonraki adıma geçin.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Depolama Oluştur düğmesini seçin.":::

5. Depolama oluşturma (veya zaten sahip olma) Azure Cloud Shell penceresini açar.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Azure Cloud Shell penceresi.":::

6. İş ortağı ilişkilendirme ayrıntıları için uzantıyı şu komutla birlikte yüklemelisiniz:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell uzantının zaten yüklü olup olmadığını göreceksiniz:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Uzantıyı gösteren Azure Cloud Shell penceresi zaten yüklü.":::

7. Bu komutu kullanarak iş ortağı ayrıntılarını kontrol edin:<br>`az managementpartner show --partner-id xxxxxx`<br>Örnek: `az managementpartner show --partner-id 4760962`.<br>Komut sonuçlarının bir ekran görüntüsünü yaslanın ve şuna benzer şekilde görünür:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Önceki komutun, taraf kimliğini görüntülemek için sonuçlarını gösteren örnek ekran.":::

>[!NOTE]
>Birden çok abonelik ekran görüntüsü gerektiriyorsa, abonelikler arasında geçiş yapmak için şu komutu kullanın:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Sonraki adımlar

- [Desteklenen ülkeler ve bölgeler](sell-from-countries.md)