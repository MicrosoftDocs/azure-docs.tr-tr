---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Officesi yazılımını yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Officesi yazılımına otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: fc67d649e3a7cd094eb2c3d633244077abcab308
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359940"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için Officesi yazılımını yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Officesi yazılımına otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için, officesi yazılımında ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* Bir [Officesi yazılım kiracısı](https://www.officespacesoftware.com/)
* Yönetim izinlerine sahip Officesi yazılımındaki bir kullanıcı hesabı.

## <a name="assigning-users-to-officespace-software"></a>Kullanıcıları Officesi yazılımına atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Officesi yazılımına erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Officesi yazılımına atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Officesi yazılımına Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Officesi yazılımına tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Officesi yazılımına atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-officespace-software-for-provisioning"></a>Sağlama için Officesi yazılımını ayarlama

1. [Officesi Software admin konsolunda](https://support.officespacesoftware.com/hc)oturum açın. **Ayarlar > bağlayıcılar**' a gidin.

    ![Officesi Software Yönetici Konsolu](media/officespace-software-provisioning-tutorial/settings.png)

2.  **Dizin eşitleme > SCıM** bölümüne gidin.

    ![Officesi Software SCıM ekleme](media/officespace-software-provisioning-tutorial/scim.png)

3.  **SCIM kimlik doğrulama belirtecini** kopyalayın. Bu değer, Azure portal Officesi yazılım uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

    ![Officesi yazılımı belirteç oluştur](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Galeriden Officesi yazılımı ekleme

Azure AD ile otomatik Kullanıcı sağlama için Officesi yazılımını yapılandırmadan önce Azure AD uygulama galerisindeki Officesi yazılımını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD uygulama galerisinden Officesi yazılımı eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **officesi yazılımı** girin, sonuçlar panelinde **Officesi yazılımı** ' nı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Officesi yazılımı](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Officesi yazılımına otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Officesi yazılımlarındaki kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> Ayrıca, [Officesi yazılımı çoklu oturum açma öğreticisinde](./officespace-tutorial.md)sunulan yönergeleri Izleyerek officesi yazılımı için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Azure AD 'de Officesi yazılımı için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Officesi yazılımı**' nı seçin.

    ![Uygulamalar listesindeki Officesi yazılımı bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://<subdomain>.officespacesoftware.com/api/scim/v2/` **kiracı URL**'si içinde giriş URL 'si biçimi. Örneğin, `https://contoso.officespacesoftware.com/api/scim/v2/`. **Gizli belirteçte** daha önce alınan **SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin Officesi yazılımına bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Officesi yazılım hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Officesi yazılımıyla eşitler**' ı seçin.

    ![Officesi Software Kullanıcı eşlemeleri](media/officespace-software-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Officesi yazılımına eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Officesi yazılımındaki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Officesi Software Kullanıcı öznitelikleri](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

12. Officesi yazılımı için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

13. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek officesi yazılımına sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

14. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü Izleyip, Azure AD sağlama hizmeti tarafından Officesi yazılımlarında gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunu kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)