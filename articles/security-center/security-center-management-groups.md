---
title: Azure Güvenlik Merkezi için abonelikleri yönetim gruplarında düzenleyin ve kullanıcılara roller atayın
description: Azure Güvenlik Merkezi 'nde Azure aboneliklerinizi yönetim gruplarında düzenleme ve kuruluşunuzdaki kullanıcılara roller atama hakkında bilgi edinin
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099395"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Abonelikleri yönetim gruplarında düzenleyin ve kullanıcılara roller atayın

Bu makalede, Azure Active Directory kiracınızla bağlantılı tüm Azure aboneliklerine güvenlik ilkeleri uygulayarak kuruluşunuzun güvenlik duruşunu nasıl yöneteceğiniz açıklanmaktadır.

Azure AD kiracısında kayıtlı olan tüm aboneliklerin güvenlik duruşunu görünürlüğünü almak için, kök yönetim grubuna yeterli okuma izinleri olan bir Azure rolünün atanması gerekir.


## <a name="organize-your-subscriptions-into-management-groups"></a>Aboneliklerinizi yönetim gruplarında düzenleme

### <a name="introduction-to-management-groups"></a>Yönetim gruplarına giriş

Azure Yönetim grupları, abonelik gruplarında erişimi, ilkeleri ve raporlamayı verimli bir şekilde yönetebilir ve kök yönetim grubu üzerinde eylemler gerçekleştirerek Azure 'un tüm özelliklerini etkin bir şekilde yönetebilme olanağı sağlar. Abonelikleri yönetim gruplarında düzenleyebilir ve idare ilkelerinizi yönetim gruplarına uygulayabilirsiniz. Bir yönetim grubu içindeki aboneliklerin tümü otomatik olarak yönetim grubuna uygulanmış olan ilkeleri devralır. 

Her Azure AD kiracısına **kök yönetim grubu** adlı tek bir en üst düzey yönetim grubu verilir. Diğer tüm yönetim grupları ve abonelikler hiyerarşide en üstte yer alan bu kök yönetim grubunun altındadır. Bu grup, genel ilkelerin ve Azure rol atamalarının dizin düzeyinde uygulanmasını sağlar. 

Kök yönetim grubu, aşağıdaki eylemlerden herhangi birini gerçekleştirdiğinizde otomatik olarak oluşturulur: 
- Azure portal **Yönetim grupları** açın. [](https://portal.azure.com)
- API çağrısıyla bir yönetim grubu oluşturun.
- PowerShell ile bir yönetim grubu oluşturun. PowerShell yönergeleri için bkz. [kaynak ve kuruluş yönetimi için yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md).

Güvenlik Merkezi 'ni eklemek için yönetim grupları gerekmez, ancak kök yönetim grubunun oluşturulması için en az bir tane oluşturmanız önerilir. Grup oluşturulduktan sonra, Azure AD kiracınız kapsamındaki tüm abonelikler buna bağlanır. 


Yönetim gruplarına ayrıntılı bir genel bakış için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](../governance/management-groups/overview.md) makalesi.

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Azure portal Yönetim gruplarını görüntüleyin ve oluşturun

1. [Azure portal](https://portal.azure.com) **Yönetim grupları** bulmak ve açmak için üstteki çubukta arama kutusunu kullanın.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Yönetim gruplarınıza erişme":::

    Yönetim gruplarınızın listesi görüntülenir.

1. Bir yönetim grubu oluşturmak için **Yönetim grubu Ekle**' yi seçin, ilgili ayrıntıları girin ve **Kaydet**' i seçin.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Azure 'a bir yönetim grubu ekleme":::

    - **Yönetim grubu kimliği** , bu yönetim grubundaki komutları göndermek için kullanılan dizin benzersiz tanımlayıcısıdır. Bu tanımlayıcı, bu grubu tanımlamak için Azure sistem genelinde kullanıldığından oluşturulduktan sonra düzenlenebilir değildir. 
    - Görünen ad alanı Azure portal içinde görüntülenen addır. Ayrı bir görünen ad, yönetim grubu oluşturulurken isteğe bağlı bir alandır ve herhangi bir zamanda değiştirilebilir.  


### <a name="add-subscriptions-to-a-management-group"></a>Bir yönetim grubuna abonelik ekleme
Oluşturduğunuz yönetim grubuna abonelikler ekleyebilirsiniz.

1. **Yönetim grupları** altında, aboneliğiniz için yönetim grubunu seçin.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Aboneliğiniz için bir yönetim grubu seçin":::

1. Grubun sayfası açıldığında, **Ayrıntılar** ' ı seçin.

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Yönetim grubunun Ayrıntılar sayfasını açma":::

1. Grubun ayrıntılar sayfasında, **Abonelik Ekle**' yi seçin, ardından aboneliklerinizi seçin ve **Kaydet**' i seçin. Kapsama içindeki tüm abonelikleri ekleyinceye kadar tekrarlayın.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Bir yönetim grubuna abonelik ekleme":::
   > [!IMPORTANT]
   > Yönetim gruplarında hem abonelikler hem de alt yönetim grupları bulunabilir. Bir kullanıcıyı bir Azure rolünü üst yönetim grubuna atadığınızda, erişim alt yönetim grubunun abonelikleri tarafından devralınır. Üst yönetim grubunda ayarlanan ilkeler alt öğeler tarafından da devralınır. 



## <a name="assign-azure-roles-to-other-users"></a>Diğer kullanıcılara Azure rolleri atama

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Azure portal aracılığıyla kullanıcılara Azure rolleri atama: 
1. [Azure portalında](https://portal.azure.com) oturum açın. 
1. Yönetim gruplarını görüntülemek için Azure ana menüsünde **tüm hizmetler** ' i seçin ve **Yönetim grupları**' yi seçin.
1.  Bir yönetim grubu seçin ve **Ayrıntılar**' ı seçin.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Ayrıntılar Yönetim Grupları ekran görüntüsü":::

1. **Erişim denetimi (IAM)** ve **rol atamaları**' nı seçin.
1. **Rol ataması ekle**’yi seçin.
1. Atanacak rolü ve Kullanıcı ' yı seçin ve ardından **Kaydet**' i seçin.  
   
   ![Güvenlik okuyucusu rolü Ekle ekran görüntüsü](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>PowerShell ile kullanıcılara Azure rolleri atama: 
1. [Azure PowerShell](/powershell/azure/install-az-ps)'i yükler.
2. Aşağıdaki komutları çalıştırın: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. İstendiğinde, genel yönetici kimlik bilgileriyle oturum açın. 

    ![Oturum açma istemi ekran görüntüsü](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Aşağıdaki komutu çalıştırarak okuyucu rolü izinleri verin:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Rolü kaldırmak için aşağıdaki komutu kullanın: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Yükseltilmiş erişimi kaldır 

Kullanıcılara Azure rolleri atandıktan sonra, kiracı yöneticisinin kendisini Kullanıcı erişimi yönetici rolünden kaldırması gerekir.

1. [Azure Portal](https://portal.azure.com) veya [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com)oturum açın.

2. Gezinti listesinde **Azure Active Directory** ' yi seçin ve ardından **Özellikler**' i seçin.

3. **Azure kaynakları Için erişim yönetimi** altında, anahtarı **Hayır** olarak ayarlayın.

4. Ayarınızı kaydetmek için **Kaydet**' i seçin.



## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, abonelikleri yönetim gruplarında nasıl düzenleyecağınızı ve kullanıcılara roller atamayı öğrendiniz. İlgili bilgiler için bkz.:

- [Azure Güvenlik Merkezi'nde İzinler](security-center-permissions.md)