---
title: 'Öğretici: origami ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve origami arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 02c79e8385c7a7e9d60a3dcbed603ca94cb1dc43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522279"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Öğretici: origami ile tümleştirme Azure Active Directory

Bu öğreticide, origami 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Origami Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Origami 'e erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla origami (çoklu oturum açma) ile otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini origami ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Origami çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Origami **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-origami-from-the-gallery"></a>Galeriden origami ekleme

Origami tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden origami yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Galeriden origami eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **origami** yazın, sonuç panelinden **origami** ' yi seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![Sonuç listesinde origami](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre origami ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve origami 'deki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı origami ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[origami çoklu oturum açmayı yapılandırın](#configure-origami-single-sign-on)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. Kullanıcının Azure AD gösterimine bağlı olan origami 'de Britta Simon 'ın bir karşılığı olacak şekilde **[origami test kullanıcısı oluşturun](#create-origami-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı origami ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **origami** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Origami etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [origami istemci destek ekibine](https://wordpress.org/support/theme/origami) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Origami ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-origami-single-sign-on"></a>Origami Single Sign-On yapılandırma

1. Yönetici haklarıyla origami hesabında oturum açın.

2. Üstteki menüden **yönetici**' ye tıklayın.
   
    !["Admin" seçiliyken origami ana sayfasını gösteren ekran görüntüsü.](./media/origami-tutorial/tutorial_origami_51.png)

3. Çoklu oturum açma Kurulumu iletişim sayfasında, aşağıdaki adımları uygulayın:
   
    !["Çoklu oturum açmayı etkinleştir" sayfasının seçili olduğu ve metin kutularının vurgulandığı "çoklu oturum açma kurulumu" sayfasını gösteren ekran görüntüsü.](./media/origami-tutorial/tutorial_origami_531.png)

    a. **Çoklu oturum açmayı etkinleştir '** i seçin.

    b. **Kimlik sağlayıcısının oturum açma sayfası URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Kimlik sağlayıcısının oturum kapatma sayfası URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    d. Azure portal indirdiğiniz sertifikayı karşıya yüklemek için, **Araştır** ' a tıklayın.

    e. **Değişiklikleri Kaydet**’e tıklayın.

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

Bu bölümde, origami 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **origami**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **origami**' yi seçin.

    ![Uygulamalar listesindeki origami bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-origami-test-user"></a>Origami test kullanıcısı oluştur

Bu bölümde, origami içinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. 

1. Yönetici haklarıyla origami hesabında oturum açın.

2. Üstteki menüden **yönetici**' ye tıklayın.
   
    !["Admin" seçiliyken origami hesabı giriş sayfasını gösteren ekran görüntüsü.](./media/origami-tutorial/tutorial_origami_51.png)

3. **Kullanıcılar ve güvenlik** Iletişim kutusunda **Kullanıcılar**' a tıklayın.
   
    !["Kullanıcılar" seçiliyken "kullanıcılar ve güvenlik" iletişim kutusunu gösteren ekran görüntüsü.](./media/origami-tutorial/tutorial_origami_54.png)

4. **Yeni Kullanıcı Ekle**' ye tıklayın.
   
    !["Yeni Kullanıcı Ekle" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/origami-tutorial/tutorial_origami_55.png)

5. Yeni Kullanıcı Ekle iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    !["Kullanıcı adı", "ad" ve "soyadı" metin kutuları vurgulanmış "Yeni Kullanıcı Ekle" iletişim kutusunun gösterildiği ekran görüntüsü.](./media/origami-tutorial/tutorial_origami_56.png)

    a. **Kullanıcı adı** metin kutusuna, **brittasıon \@ contoso.com** gibi kullanıcının e-postasını girin.

    b. **Parola** metin kutusuna bir parola yazın.

    c. **Parolayı Onayla** metin kutusuna parolayı yeniden yazın.

    d. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta** gibi girin.

    e. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını girin.

    f. **Kaydet**’e tıklayın.
   
    !["Kaydet" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/origami-tutorial/tutorial_origami_57.png)

6. Kullanıcıya **Kullanıcı rolleri** ve **istemci erişimi** atayın. 
   
    ![Tek Sign-On yapılandırma](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde origami kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız origami için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)