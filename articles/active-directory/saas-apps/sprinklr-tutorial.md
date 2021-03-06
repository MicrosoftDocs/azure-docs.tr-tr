---
title: 'Öğretici: Sprınklr ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Sprınklr arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e76386c8c276c6c8c07751474b3718f98af18f4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519461"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Öğretici: Sprınklr ile tümleştirme Azure Active Directory

Bu öğreticide, Sprınklr Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Sprınklr 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'de Sprınklr 'e erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Sprınklr (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Sprınklr ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Sprınklr çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Sprınklr **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-sprinklr-from-the-gallery"></a>Galeriden Sprınklr ekleme

Sprınklr 'in Azure AD 'ye tümleştirilmesini yapılandırmak için, galerisindeki Sprınklr öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden Sprınklr eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **sprınklr** yazın, sonuç panelinden **sprınklr** ' i seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuçlar listesinde sprınklr](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına bağlı olarak Sprınklr Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Sprınklr içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açma 'yı Sprınklr ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Sprınklr çoklu oturum açmayı yapılandırma](#configure-sprinklr-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Sprınklr **[test kullanıcısı oluşturun](#create-sprinklr-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan Sprınklr 'de Britta Simon 'a sahip olacak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı Sprınklr ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **sprınklr** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Sprınklr etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.sprinklr.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Sprınklr istemci destek ekibine](https://www.sprinklr.com/contact-us/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Sprınklr ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-sprinklr-single-sign-on"></a>Sprınklr tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Sprınklr şirket sitenizde yönetici olarak oturum açın.

1. **Yönetim \> ayarları**' na gidin.

    ![Yönetim](./media/sprinklr-tutorial/ic782907.png "Yönetim")

1. Sol bölmeden **Iş ortağı \> Çoklu oturum açmayı Yönet** ' e gidin.

    ![Iş ortağını Yönet](./media/sprinklr-tutorial/ic782908.png "Iş ortağını Yönet")

1. **+ Çoklu oturum açma Ekle** seçeneğine tıklayın.

    ![Ekran görüntüsünde çoklu oturum açma Ekle düğmesi gösterilir.](./media/sprinklr-tutorial/ic782909.png "Tek Sign-Ons")

1. **Çoklu oturum açma** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz çoklu oturum açma sayfasını gösterir.](./media/sprinklr-tutorial/ic782910.png "Tek Sign-Ons")

    a. **Ad** metin kutusuna yapılandırmanız için bir ad yazın (örneğin: *Waadssotest*).

    b. **Etkin**'i seçin.

    c. **Yenı SSO sertifikası kullan**' ı seçin.

    d. Base-64 kodlu sertifikanızı Not defteri 'nde açın, içeriğini panonuza kopyalayın ve ardından **kimlik sağlayıcısı sertifikası** metin kutusuna yapıştırın.

    e. Azure portalından kopyaladığınız **Azure AD tanımlayıcı** değerini **varlık kimliği** metin kutusuna yapıştırın.

    f. Azure portalından kopyaladığınız **oturum açma URL 'si** değerini **kimlik sağlayıcısı oturum açma URL 'si** metin kutusuna yapıştırın.

    örneğin: Azure portalından kopyaladığınız **oturum kapatma URL 'si** değerini **ıDENTITY Provider Logout URL** metin kutusuna yapıştırın.

    h. **SAML Kullanıcı kimliği türü** olarak, onaylama ' nın **kullanıcının Sprinklr.com Kullanıcı adını içerdiğini** seçin.

    i. **SAML Kullanıcı kimliği konumu** olarak, **Konu bildiriminin ad tanımlayıcı öğesinde kullanıcı kimliği**' ni seçin.

    j. **Kaydet**’e tıklayın.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alan türü **brittasimon@yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Sprınklr 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **sprınklr**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Sprınklr**' ı seçin.

    ![Uygulamalar listesindeki Sprınklr bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-sprinklr-test-user"></a>Sprınklr test kullanıcısı oluşturma

1. Sprınklr şirket sitenizde yönetici olarak oturum açın.

1. **Yönetim \> ayarları**' na gidin.

    ![Yönetim](./media/sprinklr-tutorial/ic782907.png "Yönetim")

1. Sol bölmeden **istemci \> kullanıcılarını yönet** ' e gidin.

    ![Ekran görüntüsü ayarlar/kullanıcılar ' da Kullanıcı Ekle düğmesini gösterir.](./media/sprinklr-tutorial/ic782914.png "Ayarlar")

1. **Kullanıcı Ekle**'ye tıklayın.

    ![Ekran görüntüsü, tanımlanan değerleri girebileceğiniz Kullanıcı Düzenle iletişim kutusunu gösterir.](./media/sprinklr-tutorial/ic782915.png "Ayarlar")

1. **Kullanıcı Düzenle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:

    ![Kullanıcıyı Düzenle](./media/sprinklr-tutorial/ic782916.png "Kullanıcıyı Düzenle")

    a. **E-postada** **adı** ve **Soyadı** metın kutularına sağlamak istediğiniz bir Azure AD Kullanıcı hesabının bilgilerini yazın.

    b. **Parola devre dışı** seçeneğini belirleyin.

    c. **Dil** seçin.

    d. **Kullanıcı türünü** seçin.

    e. **Güncelleştir**’e tıklayın.

    > [!IMPORTANT]
    > Kullanıcının bir kimlik sağlayıcısı aracılığıyla oturum açmasını sağlamak için **parolanın devre dışı bırakılması** seçilmelidir. 

1. **Role** gidin ve aşağıdaki adımları gerçekleştirin:

    ![İş ortağı rolleri](./media/sprinklr-tutorial/ic782917.png "İş ortağı rolleri")

    a. **Genel** listesinden **ALL_Permissions**' yi seçin.  

    b. **Güncelleştir**’e tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak üzere Sprınklr tarafından sunulan diğer Sprınklr Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Sprınklr kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Sprınklr ' de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)