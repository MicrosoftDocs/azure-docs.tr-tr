---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için BitaBIZ yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırma hakkında bilgi edinin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8eccc3be7da201ee1e2af046c6b515871ef05adc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350798"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Bıtabiz yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları devre dışı bırakmak üzere otomatik olarak sağlamak ve yeniden sağlamak üzere yapılandırmak için, Bıtabiz ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir BitaBIZ kiracısı](https://bitabiz.dk/en/price/).
* Yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="assigning-users-to-bitabiz"></a>Kullanıcıları BitaBIZ 'a atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların BitaBIZ 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra, buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Iki grup için de atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Kullanıcıları Bıtabiz 'ya atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısına Iki adet bir Azure AD kullanıcısı atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı BitaBIZ 'e atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-bitabiz-for-provisioning"></a>Sağlama için Bıtabiz ayarlama

Azure AD ile otomatik Kullanıcı sağlama için BitaBIZ 'ı yapılandırmadan önce, Bıtabiz üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Bitabiz Yönetici konsolunuza](https://www.bitabiz.com/login?lang=en)oturum açın. **Kurulum Yöneticisi**' ne tıklayın.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/setup-admin.png" alt-text="Kurulum Yöneticisi vurgulanmış şekilde, Bıtabiz yönetim konsolunun ekran görüntüsü." border="false":::

2.  **Tümleştirme**' e gidin.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/integration.png" alt-text="Tümleştirme vurgulanmış şekilde, Bıtabiz yönetim konsolunun ekran görüntüsü." border="false":::

2.  **Microsoft Azure AD sağlama** bölümüne gidin.  Otomatik Kullanıcı hazırlama ' da **etkin** ' i seçin. **SCIM sağlama uç noktası URL 'si** ve **taşıyıcı belirtecinin** değerlerini kopyalayın. Bu değerler, Azure portal Bıtabiz uygulamanızın sağlama sekmesinde kiracı URL 'SI ve gizli belirteç alanlarına girilir.

    ![Bıtabiz ekleme SCıM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Galeriden BitaBIZ ekleme

Azure AD ile otomatik Kullanıcı sağlama için BitaBIZ 'yi yapılandırmak için Azure AD uygulama galerisindeki yönetilen SaaS uygulamaları listenize Bıtabiz eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden bir Kaynakça eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **bitabiz** yazın, sonuçlar panelinde **bitabiz** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde BitaBIZ](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Otomatik Kullanıcı sağlamayı, Bıtabiz olarak yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> Ayrıca, bitabiz için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi tercih edebilirsiniz. Bu arada, [Bıtabiz çoklu oturum açma öğreticisinde](BitaBIZ-tutorial.md)sunulan talimatları takip edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Azure AD 'de Bıtabiz için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Bitabiz**' ı seçin.

    ![Uygulamalar listesindeki BitaBIZ bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. Yönetici kimlik bilgileri bölümünde, sırasıyla kiracı URL 'SI ve gizli belirteç ' de bulunan **SCIM sağlama uç noktası URL 'sini** ve **taşıyıcı belirteç** değerlerini girin. Azure AD 'nin Bıtabiz 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Bıtabiz hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Bitabiz olarak eşitler**' ı seçin.

    ![Bıtabiz Kullanıcı eşlemeleri](media/bitabiz-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Bıtabiz 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için BitaBIZ içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Bıtabiz Kullanıcı öznitelikleri](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Bıtabiz için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** içindeki Istenen değerleri seçerek, iki veya daha fazla grup için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İşlem ilerlemesini izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından, Bıtabiz üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Bıtabiz **Kullanıcı adı**, **e-posta**, **ad** ve **Soyadı** ' nı zorunlu öznitelikler olarak gerektirir 
* Bıtabiz Şu anda sabit silmeleri desteklemez.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md).
