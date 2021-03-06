---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Iş ekibini yapılandırma | Microsoft Docs'
description: Iş ekibine Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: f465438a6be80a1b487a4df7d3ee8bd38be9c102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94354237"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Iş ekibini yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Iş ekibine otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için, Workteam ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir Iş ekibi kiracısı](https://workte.am/pricing.html)
* Yönetici izinlerine sahip Workteam 'teki bir kullanıcı hesabı.

## <a name="assigning-users-to-workteam"></a>Kullanıcıları Iş ekibine atama 

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Iş ekibine erişimine ihtiyacı olduğuna karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Iş ekibine atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Kullanıcıları Iş ekibine atamaya yönelik önemli ipuçları 

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, Iş ekibine tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Iş ekibine atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-workteam--for-provisioning"></a>Sağlama için Iş ekibini ayarla

Azure AD ile otomatik Kullanıcı sağlama için Iş ekibini yapılandırmadan önce, Workteam üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Iş ekibine](https://app.workte.am/account/signin)oturum açın. **Kuruluş ayarları**  >  **ayarları**' na tıklayın.

    ![Iş takımının U adlı kuruluş ayarları ve Ayarlar seçeneklerini içeren ekran görüntüsü.](media/workteam-provisioning-tutorial/settings.png)

2. En Alta kaydırın ve Iş ekibinin sağlama yeteneklerini etkinleştirin.

    ![S C I M Kullanıcı sağlama dişli simgesiyle Ayarlar bölümünün en altında yer aldığı ekran görüntüsü.](media/workteam-provisioning-tutorial/icon.png)

3. **Temel URL 'yi** ve **taşıyıcı belirtecini** kopyalayın. Bu değerler, Azure portal Iş ekibi uygulamanızın sağlama sekmesinde **kiracı URL 'si** ve **gizli belirteç** alanına girilir.

    ![Ana U R L ve TAŞıYıCı BELIRTECI metin kutuları ile adlandırılan S C I M ayarları iletişim kutusunun ekran görüntüsü.](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Galeriden Iş ekibi ekleyin

Azure AD ile otomatik Kullanıcı sağlaması için Iş ekibini yapılandırmak üzere, Azure AD uygulama galerisindeki Iş ekibini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD uygulama galerisinden Iş ekibi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **çalışma ekibi**' ni girin, sonuçlar panelinde **iş ekibi** ' ni seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde iş ekibi](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Otomatik Kullanıcı sağlamasını Iş ekibine yapılandırma  

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Iş ekibinizdeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> Ayrıca, [Workteam çoklu oturum açma öğreticisinde](workteam-tutorial.md)belirtilen yönergeleri Izleyerek workteam için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>Azure AD 'de Iş ekibine yönelik otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **çalışma ekibi**' ni seçin.

    ![Uygulamalar listesindeki Workteam bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. Yönetici kimlik bilgileri bölümünde, önceki **kiracı URL 'si** ve **parola belirteci** sırasıyla alınan **temel URL** 'yi ve **taşıyıcı belirteç** değerlerini girin. Azure AD 'nin Iş ekibine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Iş ekibi hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları iş ekibine eşitler**' ı seçin.

    ![İş ekibi Kullanıcı eşlemeleri](media/workteam-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den iş ekibine eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için workteam 'teki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![İş ekibi Kullanıcı öznitelikleri](media/workteam-provisioning-tutorial/userattribute.png)

11. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

12. Iş ekibine yönelik Azure AD sağlama hizmetini etkinleştirmek için, **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

13. **Ayarlar** bölümünde **kapsam** içindeki Istenen değerleri seçerek, iş ekibine sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

14. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin sonraki eşitlemeler daha uzun sürer. Kullanıcıların ve/veya grupların sağlaması için ne kadar süreceğine ilişkin daha fazla bilgi için bkz. [kullanıcıları sağlamak için ne kadar sürer](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

İlerlemeyi izlemek için **geçerli durum** bölümünü kullanabilir ve iş ekibinizdeki Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunuzun bağlantılarını izleyebilirsiniz. Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Azure AD sağlama günlüklerini okumak için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
