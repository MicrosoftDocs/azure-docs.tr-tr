---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: fb2ecd9ab63345954356d18164a2afe94a5aae24
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027164"
---
## <a name="register-an-azure-ad-app"></a>Azure AD uygulamasını kaydetme

Kullanıcıların belirli bir Azure AD kuruluştan oturum açmasını etkinleştirmek için, uygulamayı kurumsal Azure AD kiracısında kaydetmeniz gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Kuruluşunuzun Azure AD kiracınızı içeren dizini kullandığınızdan emin olun (örneğin, contoso.com). Üst menüden **Dizin + abonelik filtresi** ' ni seçin ve ardından Azure AD kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Yeni kayıt** seçeneğini belirleyin.
1. Uygulamanız için bir **ad** girin. Örneğin, `Azure AD B2C App`.
1. Bu **kuruluş dizinindeki hesapların** varsayılan seçimini yalnızca bu uygulama için kabul edin.
1. **Yeniden yönlendirme URI 'si** Için, **Web**'in değerini kabul edin ve aşağıdaki URL 'yi tüm küçük harflerle, burada `your-B2C-tenant-name` Azure AD B2C kiracınızın adıyla birlikte girin.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Örneğin, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. **Kaydet**’i seçin. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.
1. **Sertifikalar & sertifikalar**' ı seçin ve ardından **yeni istemci parolası**' nı seçin.
1. Gizli dizi için bir **Açıklama** girin, bir süre sonu seçin ve ardından **Ekle**' yi seçin. Daha sonraki bir adımda kullanmak için gizli dizi **değerini** kaydedin.

### <a name="configuring-optional-claims"></a>İsteğe bağlı talepler yapılandırılıyor

`family_name` `given_name` Azure AD 'den ve taleplerini almak istiyorsanız, Azure Portal Kullanıcı arabirimi veya uygulama bildiriminde uygulamanız için isteğe bağlı talepler yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure AD uygulamanıza isteğe bağlı talepler sağlama](../articles/active-directory/develop/active-directory-optional-claims.md).

1. [Azure portalında](https://portal.azure.com) oturum açın. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** bölümünden **uygulama kayıtları**' yi seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı seçin.
1. **Yönet** bölümünde **belirteç yapılandırması**' nı seçin.
1. **İsteğe bağlı talep Ekle**' yi seçin.
1. **Belirteç türü** için **kimlik**' i seçin.
1. Eklemek için isteğe bağlı talepler ' i `family_name` seçin `given_name` .
1. **Ekle**'ye tıklayın.