---
title: Azure Maliyet Yönetimi verilerine erişim atama
description: Bu makalede farklı erişim kapsamları için Azure Maliyet Yönetimi verilerine izin atama adımları gösterilmektedir.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: cb54c5f7334120f6cd01ed1704939c5c1a55e7c6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645290"
---
# <a name="assign-access-to-cost-management-data"></a>Maliyet Yönetimi verilerine erişim atama

Azure Kurumsal Anlaşmalarına sahip olan kullanıcıların Azure Maliyet Yönetimi verilerine erişim düzeyi, Azure portalında ve Enterprise (EA) portalında verilen izinlerin birleşimiyle belirlenir. Diğer Azure hesabı türlerine sahip olan kullanıcıların Maliyet Yönetimi verilerine erişim düzeyini belirlemek Azure rol tabanlı erişim denetimi (Azure RBAC) sayesinde daha kolaydır. Bu makalede Maliyet Yönetimi verilerine erişim atama adımları açıklanmaktadır. İzinlerin birleşimi atandıktan sonra, kullanıcı Maliyet Yönetimi verilerini kapsamına ve Azure portalında seçtiği kapsama göre görüntüler.

Kullanıcının veri birleştirmesi sağlamak ve maliyet bilgilerine erişimi denetlemek için seçtiği kapsam Maliyet Yönetimi’nin tamamında kullanılır. Kullanıcılar, kapsamları kullanırken çoklu seçim yapmaz. Bunun yerine alt kapsamların birleşerek oluşturduğu daha geniş bir kapsam seçer ve görüntülemek istedikleri verileri filtreler. Bazı kişilerin alt kapsamların toplandığı üst kapsama erişmemesi gerektiğinden veri birleştirme önemlidir.

Azure rol tabanlı erişim denetimi (Azure RBAC) ile maliyetleri ve ücretleri görüntüleme erişimi atama hakkında bilgi edinmek için [Maliyet Yönetimi erişimi denetleme](https://www.youtube.com/watch?v=_uQzQ9puPyM) videosunu izleyin. Diğer videoları izlemek için [Maliyet Yönetimi YouTube kanalını](https://www.youtube.com/c/AzureCostManagement) ziyaret edin.

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Maliyet Yönetimi kapsamları

Maliyet yönetimi farklı Azure hesap türlerini destekler. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](understand-cost-mgt-data.md). Hesap türü, kullanılabilecek kapsamları belirler.

### <a name="azure-ea-subscription-scopes"></a>Azure EA aboneliği kapsamları

Azure EA aboneliklerine ait maliyet verilerini görüntülemek isteyen bir kullanıcının aşağıdaki kapsamlardan birine veya daha fazlasına en azından yazma erişimi olmalıdır.

| **Kapsam** | **Tanımlanma yeri** | **Verileri görüntülemek için gerekli erişim** | **Önkoşul EA ayarı** | **Verileri şununla birleştirir:** |
| --- | --- | --- | --- | --- |
| Faturalama hesabı<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Kuruluş Yöneticisi | Hiçbiri | Kurumsal sözleşmedeki tüm abonelikler |
| Bölüm | [https://ea.azure.com](https://ea.azure.com/) | Bölüm Yöneticisi | **DA ücretleri görüntüleme** etkinleştirildi | Bölüme bağlı olan kayıt hesabına ait olan tüm abonelikler |
| Kayıt hesabı<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Hesap Sahibi | **AO ücretleri görüntüleme** etkinleştirildi | Kayıt hesabındaki tüm abonelikler |
| Yönetim grubu | [https://portal.azure.com](https://portal.azure.com/) | Maliyet yönetimi okuyucusu (veya katkıda bulunan) | **AO ücretleri görüntüleme** etkinleştirildi | Yönetim grubu altındaki tüm abonelikler |
| Abonelik | [https://portal.azure.com](https://portal.azure.com/) | Maliyet yönetimi okuyucusu (veya katkıda bulunan) | **AO ücretleri görüntüleme** etkinleştirildi | Abonelikteki tüm kaynaklar/kaynak grupları |
| Kaynak grubu | [https://portal.azure.com](https://portal.azure.com/) | Maliyet yönetimi okuyucusu (veya katkıda bulunan) | **AO ücretleri görüntüleme** etkinleştirildi | Kaynak grubundaki tüm kaynaklar |

<sup>1</sup> Ödeme hesabı genellikle Kurumsal Sözleşme veya Kayıt olarak da nitelenir.

<sup>2</sup> Kayıt hesabı, hesap sahibi olarak da nitelenir.


## <a name="other-azure-account-scopes"></a>Diğer Azure hesabı kapsamları

Diğer Azure aboneliklerine ait maliyet verilerini görüntülemek isteyen bir kullanıcının aşağıdaki kapsamlardan birine veya daha fazlasına en azından yazma erişimi olmalıdır:

- Yönetim grubu
- Abonelik
- Kaynak grubu

İş ortakları, müşterileri Microsoft Müşteri Sözleşmesi'ne eklediğinde ek kapsamlar sunulur. CSP müşterileri, CSP iş ortakları tarafından etkinleştirilmesi durumunda Maliyet Yönetimi özelliklerini kullanabilir. Daha fazla bilgi için bkz. [İş ortakları için Azure Maliyet Yönetimi'ni kullanmaya başlama](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Azure portalındaki maliyetlere erişimi etkinleştirme

Bölüm kapsamı için **Bölüm yöneticileri ücretleri görüntüleyebilir** (DA ücretleri görüntüleme) seçeneği **Açık** olarak ayarlanmalıdır. Seçeneği Azure portalında veya EA portalında yapılandırın. Diğer tüm kapsamlar için **Hesap sahipleri ücretleri görüntüleyebilir** (AO ücretleri görüntüleme) seçeneği **Açık** olarak ayarlanmalıdır.

Azure portalında bir seçeneği etkinleştirmek için:

1. Kuruluş yöneticisi hesabıyla https://portal.azure.com adresinde Azure portalında oturum açın.
1. **Maliyet Yönetimi + Faturalama** menü öğesini seçin.
1. Kullanılabilir faturalama kapsamlarının ve ücret hesaplarının listesini görüntülemek için **Faturalama kapsamları**’nı seçin.
1. Kullanılabilir ödeme hesapları listesinden **Ödeme Hesabınızı** seçin.
1. **Ayarlar**’ın altında **İlkeler** menü öğesini seçin ve ardından ayarı yapılandırın.  
    ![Ücretleri görüntüleme seçeneklerini gösteren faturalama kapsamı ilkeleri](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Ücretleri görüntüleme seçenekleri etkinleştirildikten sonra çoğu kapsam için Azure portalından Azure rol tabanlı erişim denetimi (Azure RBAC) izin yapılandırması da gerekir.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>EA portalındaki maliyetlere erişimi etkinleştirme

Departman kapsamı için **DA ücretleri görüntüleme** seçeneğinin EA portalında **Etkin** olması gerekir. Seçeneği Azure portalında veya EA portalında yapılandırın. Diğer tüm kapsamlar için **AO ücretleri görüntüleme** seçeneğinin EA portalında **Etkin** olması gerekir.

EA portalında bir seçeneği etkinleştirmek için:

1. Kuruluş yöneticisi hesabıyla [https://ea.azure.com](https://ea.azure.com) adresinde EA portalında oturum açın.
2. Sol bölmede **Yönet**’i seçin.
3. Erişim sağlamak istediğiniz maliyet yönetimi kapsamları için ücret seçeneğini **DA ücretleri görüntüleme** ve/veya **AO ücretleri görüntüleme** olarak etkinleştirin.  
    ![DA ve AO ücretleri görüntüleme seçeneklerinin göründüğü kayıt sekmesi](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Ücretleri görüntüleme seçenekleri etkinleştirildikten sonra çoğu kapsam için Azure portalından Azure rol tabanlı erişim denetimi (Azure RBAC) izin yapılandırması da gerekir.

## <a name="enterprise-administrator-role"></a>Kuruluş yöneticisi rolü

Kuruluş yöneticisi varsayılan olarak ödeme hesabına (Kurumsal Anlaşma/kayıt) ve diğer tüm alt kapsamlara erişebilir. Kuruluş yöneticisi, diğer kullanıcılar için kapsam erişimlerini atar. İş sürekliliği açısından en iyi yöntemlerden biri, her zaman kuruluş yöneticisi erişimine sahip iki kullanıcı bulundurmaktır. Aşağıdaki bölümlerde kuruluş yöneticisinin diğer kullanıcılara kapsam erişimi atadığı örnekler gösterilmiştir.

## <a name="assign-billing-account-scope-access"></a>Ödeme hesabı kapsamı erişimi atama

Ödeme hesabı kapsamına erişim için EA portalından kuruluş yöneticisi izni gerekir. Kuruluş yöneticisi, EA kaydının tamamındaki veya birden fazla kayıttaki maliyetleri görüntüleyebilir. Ödeme hesabı kapsamı için Azure portalında herhangi bir eylem yapılması gerekmez.

1. Kuruluş yöneticisi hesabıyla [https://ea.azure.com](https://ea.azure.com) adresinde EA portalında oturum açın.
2. Sol bölmede **Yönet**’i seçin.
3. **Kayıt** sekmesinde yönetmek istediğiniz kaydı seçin.  
    ![EA portalında kaydınızı seçin](./media/assign-access-acm-data/ea-portal.png)
4. **+ Yönetici Ekle**'yi seçin.
5. Yönetici Ekle kutusunda kimlik doğrulaması türünü seçin ve kullanıcının e-posta adresini yazın.
6. Kullanıcının maliyet ve kullanım verilerine salt okuma erişimine sahip olması gerekiyorsa **Salt okunur** ayarını **Evet** yapın.  Gerekmiyorsa **Hayır**'ı seçin.
7. Hesabı oluşturmak için **Ekle**'yi seçin.  
    ![Yönetici ekle kutusunda gösterilen örnek bilgiler](./media/assign-access-acm-data/add-admin.png)

Yeni kullanıcının Maliyet Yönetimi verilerine erişmesi 30 dakika kadar sürebilir.

### <a name="assign-department-scope-access"></a>Departman kapsamı erişimi atama

Departman kapsamı erişimi için EA portalında departman yöneticisi (DA ücretleri görüntüleme) erişimi gerekir. Departman yöneticisi, bir veya daha fazla departman ile ilişkilendirilmiş maliyetleri ve kullanım verilerini görüntüleyebilir. Departman verilerine departmana bağlı olan kayıt hesabına ait tüm abonelikler dahildir. Azure portalında herhangi bir eylem gerçekleştirilmesi gerekmez.

1. Kuruluş yöneticisi hesabıyla [https://ea.azure.com](https://ea.azure.com) adresinde EA portalında oturum açın.
2. Sol bölmede **Yönet**’i seçin.
3. **Kayıt** sekmesinde yönetmek istediğiniz kaydı seçin.
4. **Departman** sekmesini ve ardından **Yönetici Ekle**’yi seçin.
5. Departman Yöneticisi Ekle kutusunda kimlik doğrulaması türünü seçip kullanıcının e-posta adresini yazın.
6. Kullanıcının maliyet ve kullanım verilerine salt okuma erişimine sahip olması gerekiyorsa **Salt okunur** ayarını **Evet** yapın.  Gerekmiyorsa **Hayır**'ı seçin.
7. Departman yöneticisi iznini vermek istediğiniz departmanları seçin.
8. Hesabı oluşturmak için **Ekle**'yi seçin.  
    ![Departman yöneticisi ekle kutusuna gerekli bilgileri girin](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Kayıt hesabı kapsamı erişimi atama

Kayıt hesabı kapsamına erişim için EA portalında hesap sahibi (AO ücretleri görüntüleme) erişimi gerekir. Hesap sahibi bu kayıt hesabıyla oluşturulan aboneliklerle ilişkilendirilmiş olan maliyetleri ve kullanım verilerini görüntüleyebilir. Azure portalında herhangi bir eylem gerçekleştirilmesi gerekmez.

1. Kuruluş yöneticisi hesabıyla [https://ea.azure.com](https://ea.azure.com) adresinde EA portalında oturum açın.
2. Sol bölmede **Yönet**’i seçin.
3. **Kayıt** sekmesinde yönetmek istediğiniz kaydı seçin.
4. **Hesap** sekmesini ve ardından **Hesap Ekle**'yi seçin.
5. Hesap Ekle kutusunda, hesabı ile ilişkilendirmek için bir **Departman** seçin veya atanmamış olarak bırakın.
6. Kimlik doğrulama türünü seçin ve hesap adını yazın.
7. Ardından kullanıcının e-posta adresini yazın ve isteğe bağlı olarak maliyet merkezini girin.
8. Hesabı oluşturmak için **Ekle**'yi seçin.  
    ![Kayıt hesabı için Hesap ekle kutusuna gerekli bilgileri girin](./media/assign-access-acm-data/add-account.png)

Yukarıdaki adımları tamamladığınızda kullanıcı hesabı, Enterprise Portal'da bir kayıt hesabı olur ve abonelik oluşturabilir. Kullanıcı, oluşturduğu aboneliklerin maliyet ve kullanım verilerine erişebilir.

## <a name="assign-management-group-scope-access"></a>Yönetim grubu kapsamı erişimi atama

Yönetim grubu kapsamını görüntüleme erişimi için en azından Maliyet Yönetimi Okuyucusu (veya Okuyucu) izni gerekir. Yönetim grubu izinlerini Azure portalından yapılandırabilirsiniz. Başkalarına erişim verebilmek için yönetim grubunda en azından Kullanıcı Erişimi Yöneticisi (veya Sahip) iznine sahip olmanız gerekir. Azure EA hesapları için de EA portalında **AO ücretleri görüntüleme** ayarının etkinleştirilmiş olması gerekir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Kenar çubuğunda **Tüm Hizmetler**'i seçin, _yönetim grupları_ için arama yapın ve **yönetim grupları** girişini seçin.
3. Hiyerarşideki yönetim grubunu seçin.
4. Yönetim grubunuzun adının yanındaki **Ayrıntılar**'ı seçin.
5. Soldaki bölmeden **Erişim Denetimi (IAM)** öğesini seçin.
6. **Add (Ekle)** seçeneğini belirleyin.
7. **Rol**'ün altında **Maliyet Yönetimi Okuyucusu**'nu seçin.
8. **Erişim ata** bölümünde **Azure AD, kullanıcı, grup veya uygulama**'yı seçin.
9. Erişim atamak için kullanıcıyı arayın ve seçin.
10. **Kaydet**’i seçin.  
    ![Yönetim grubu için İzin ekle kutusundaki örnek bilgiler](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Abonelik kapsamı erişimi atama

Abonelik erişimi için en azından Maliyet Yönetimi Okuyucusu (veya Okuyucu) izni gerekir. Abonelik izinlerini Azure portalından yapılandırabilirsiniz. Başkalarına erişim verebilmek için abonelikte en azından Kullanıcı Erişimi Yöneticisi (veya Sahip) iznine sahip olmanız gerekir. Azure EA hesapları için de EA portalında **AO ücretleri görüntüleme** ayarının etkinleştirilmiş olması gerekir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Kenar çubuğunda **Tüm Hizmetler**'i seçin, _abonelikler_ araması yapın ve **Abonelikler**'i seçin.
3. Aboneliğinizi seçin.
4. Soldaki bölmeden **Erişim Denetimi (IAM)** öğesini seçin.
5. **Add (Ekle)** seçeneğini belirleyin.
6. **Rol**'ün altında **Maliyet Yönetimi Okuyucusu**'nu seçin.
7. **Erişim ata** bölümünde **Azure AD, kullanıcı, grup veya uygulama**'yı seçin.
8. Erişim atamak için kullanıcıyı arayın ve seçin.
9. **Kaydet**’i seçin.

## <a name="assign-resource-group-scope-access"></a>Kaynak grubu kapsamı erişimi atama

Kaynak grubu erişimi için en azından Maliyet Yönetimi Okuyucusu (veya Okuyucu) izni gerekir. Kaynak grubu izinlerini Azure portalından yapılandırabilirsiniz. Başkalarına erişim verebilmek için kaynak grubunda en azından Kullanıcı Erişimi Yöneticisi (veya Sahip) iznine sahip olmanız gerekir. Azure EA hesapları için de EA portalında **AO ücretleri görüntüleme** ayarının etkinleştirilmiş olması gerekir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Kenar çubuğunda **Tüm Hizmetler**'i seçin, _kaynak grupları_ için arama yapın ve **Kaynak grupları**'nı seçin.
3. Kaynak grubunuzu seçin.
4. Soldaki bölmeden **Erişim Denetimi (IAM)** öğesini seçin.
5. **Add (Ekle)** seçeneğini belirleyin.
6. **Rol**'ün altında **Maliyet Yönetimi Okuyucusu**'nu seçin.
7. **Erişim ata** bölümünde **Azure AD, kullanıcı, grup veya uygulama**'yı seçin.
8. Erişim atamak için kullanıcıyı arayın ve seçin.
9. **Kaydet**’i seçin.

## <a name="cross-tenant-authentication-issues"></a>Kiracılar arası kimlik doğrulaması sorunları

Azure Maliyet Yönetimi şu anda kiracılar arası kimlik doğrulaması için sınırlı desteğe sahiptir. Bazı durumlarda maliyet analizinde kiracılar arası kimlik doğrulaması gerçekleştirmeye çalıştığınızda **Erişim engellendi** hatasıyla karşılaşabilirsiniz. Bu sorun, başka bir kiracının aboneliğinde Azure rol tabanlı erişim denetimini (Azure RBAC) yapılandırmanız ve ardından maliyet verilerini görüntüleme çalışmanız halinde ortaya çıkabilir.

*Sorunu geçici olarak çözmek için*: Kiracılar arası Azure RBAC'yi yapılandırdıktan sonra bir saat bekleyin. Ardından maliyetleri, maliyet analizinde görüntülemeyi deneyin veya kullanıcılara iki kiracıda da Maliyet Yönetimi erişimi verin.  


## <a name="next-steps"></a>Sonraki adımlar

- Maliyet Yönetimi için ilk hızlı başlangıcı önceden tamamlamadıysanız, [Maliyetleri analiz etmeye başlama](quick-acm-cost-analysis.md) bölümünden bilgi edinin.
