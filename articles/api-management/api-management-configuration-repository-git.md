---
title: Git-Azure kullanarak API Management hizmetinizi yapılandırma | Microsoft Docs
description: Git kullanarak API Management hizmeti yapılandırmanızı kaydetme ve yapılandırma hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89400373"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>API Management hizmeti yapılandırmanızı Git kullanarak kaydetme ve yapılandırma

Her bir API Management hizmet örneği, hizmet örneği için yapılandırma ve meta veriler hakkında bilgi içeren bir yapılandırma veritabanını tutar. Azure portal bir ayar değiştirilerek, bir PowerShell cmdlet 'i kullanılarak veya bir REST API çağrısı yapılarak hizmet örneği üzerinde değişiklik yapılabilir. Bu yöntemlere ek olarak, git kullanarak hizmet örneği yapılandırmanızı yönetebilir ve aşağıdaki gibi hizmet yönetimi senaryolarını etkinleştirebilirsiniz:

* Yapılandırma sürümü oluşturma-hizmet yapılandırmanızın farklı sürümlerini indirin ve depolayın
* Toplu yapılandırma değişiklikleri-yerel deponuzdaki hizmet yapılandırmanızın birden çok bölümünde değişiklik yapın ve değişiklikleri tek bir işlemle sunucuya geri tümleştirin
* Tanıdık git araç zinciri ve iş akışı-zaten bildiğiniz git araçları ve iş akışlarını kullanın

Aşağıdaki diyagramda API Management hizmeti örneğinizi yapılandırmanın farklı yollarına genel bakış gösterilmektedir.

![Git yapılandırması][api-management-git-configure]

Azure portal, PowerShell cmdlet 'lerini veya REST API kullanarak hizmetinize değişiklikler yaptığınızda,, `https://{name}.management.azure-api.net` diyagramın sağ tarafında gösterildiği gibi, hizmet yapılandırma veritabanınızı uç nokta kullanarak yönetiyorsunuz demektir. Diyagramın sol tarafında, hizmet yapılandırmanızı adresinde bulunan hizmetiniz için git ve Git deposunu kullanarak nasıl yönetebileceğinizi gösterir `https://{name}.scm.azure-api.net` .

Aşağıdaki adımlarda, git kullanarak API Management hizmeti örneğinizi yönetmeye ilişkin bir genel bakış sağlanmaktadır.

1. Hizmetinizdeki git yapılandırmasına erişin
2. Hizmet yapılandırma veritabanınızı git deponuza kaydetme
3. Git deposunu yerel makinenize kopyalayın
4. En son depoyu yerel makinenize çekin ve değişiklikleri kaydedin ve depoya geri gönderin
5. Deponuzdaki değişiklikleri hizmet yapılandırma veritabanınıza dağıtın

Bu makalede, hizmet yapılandırmanızı yönetmek için git 'in nasıl etkinleştirileceği ve kullanılacağı açıklanır ve git deposundaki dosya ve klasörler için bir başvuru sağlanmaktadır.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Hizmetinizdeki git yapılandırmasına erişin

Git yapılandırma ayarlarınızı görüntülemek ve yapılandırmak için **dağıtım ve altyapı** menüsüne tıklayıp **Depo** sekmesine gidebilirsiniz.

![GIT 'i etkinleştir][api-management-enable-git]

> [!IMPORTANT]
> Adlandırılmış değerler olarak tanımlanmayan tüm gizli dizileri depoda depolanır ve git erişimini devre dışı bırakıp yeniden etkinleştirene kadar geçmişinde kalır. Adlandırılmış değerler, tüm API yapılandırma ve ilkelerindeki gizli dizileri de içeren sabit dize değerlerini yönetmek için güvenli bir yer sağlar, böylece bunları doğrudan ilke deyimlerinizle depolamanız gerekmez. Daha fazla bilgi için bkz. [Azure API Management Ilkelerinde adlandırılmış değerleri kullanma](api-management-howto-properties.md).
>
>

REST API kullanarak git erişimini etkinleştirme veya devre dışı bırakma hakkında bilgi için, bkz. [REST API kullanarak git erişimini etkinleştirme veya devre dışı bırakma](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Hizmet yapılandırmasını git deposuna kaydetmek için

Depoyu kopyalamaya başlamadan önce ilk adım, hizmet yapılandırmasının geçerli durumunu depoya kaydetmesidir. **Depoya Kaydet ' e** tıklayın.

Onay ekranında istediğiniz değişiklikleri yapın ve kaydetmek için **Kaydet** ' e tıklayın.

Birkaç dakika sonra yapılandırma kaydedildikten sonra, son yapılandırma değişikliğinin tarih ve saati ve hizmet yapılandırması ile depo arasındaki son eşitleme dahil olmak üzere deponun yapılandırma durumu görüntülenir.

Yapılandırma depoya kaydedildikten sonra klonlanır.

REST API kullanarak bu işlemi gerçekleştirme hakkında daha fazla bilgi için, bkz. [REST API kullanarak yapılandırma anlık görüntüsünü işleme](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Depoyu yerel makinenize kopyalamak için

Bir depoyu kopyalamak için, deponuzda URL 'niz, bir Kullanıcı adı ve parola gerekir. Kullanıcı adını ve diğer kimlik bilgilerini almak için sayfanın üst kısmındaki **erişim kimlik bilgilerine** tıklayın.

Parola oluşturmak için, önce **sona** erme tarihinin istenen bitiş tarihi ve saatine ayarlandığından emin olun ve ardından **Oluştur**' a tıklayın.

> [!IMPORTANT]
> Bu parolayı bir yere getirin. Bu sayfadan ayrıldığınızda, parola yeniden görüntülenmeyecektir.
>

Aşağıdaki örneklerde, git Bash aracı [for Windows](https://www.git-scm.com/downloads) 'dan, ancak bildiğiniz herhangi bir git aracını kullanabilirsiniz.

Git aracınızı istediğiniz klasörde açın ve Azure portal tarafından belirtilen komutu kullanarak Git deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```
git clone https://{name}.scm.azure-api.net/
```

İstendiğinde Kullanıcı adı ve parola sağlayın.

Herhangi bir hata alırsanız, `git clone` Aşağıdaki örnekte gösterildiği gibi, komutunu Kullanıcı adı ve parolayı içerecek şekilde değiştirmeyi deneyin.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Bu bir hata sağlıyorsa, komutun parola bölümünü kodlayarak URL 'YI deneyin. Bunu yapmanın hızlı bir yolu, Visual Studio 'yu açmak ve komut **penceresinde** aşağıdaki komutu vermektir. **Hemen penceresini** açmak Için, Visual Studio 'da herhangi bir çözümü veya projeyi açın (veya yeni bir boş konsol uygulaması oluşturun) ve **Hata Ayıkla** menüsünden **Windows** **' u** seçin.

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Git komutunu oluşturmak için Kullanıcı adınız ve depo konumlarınızla birlikte kodlanmış parolayı kullanın.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Depo klonlandığında, bunu yerel dosya sisteminizde görüntüleyebilir ve birlikte çalışabilirsiniz. Daha fazla bilgi için bkz. [Yerel git deposunun dosya ve klasör yapısı başvurusu](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Yerel deponuzu en güncel hizmet örneği yapılandırmasıyla güncelleştirmek için

Azure portal API Management hizmet Örneğinizde veya REST API kullanarak değişiklik yaparsanız, yerel deponuzu en son değişikliklerle güncelleştirebilmeniz için bu değişiklikleri depoya kaydetmeniz gerekir. Bunu yapmak için Azure portal **Depo** sekmesinde **depoya kaydet** ' e tıklayın ve ardından yerel deponuzda aşağıdaki komutu verin.

```
git pull
```

Çalıştırmadan önce, `git pull` yerel deponuzun klasöründe olduğunuzdan emin olun. Komutu az önce tamamladıysanız `git clone` , aşağıdaki gibi bir komut çalıştırarak dizini deponuzda değiştirmeniz gerekir.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Değişiklikleri yerel depoınızdan sunucu deposuna göndermek için
Yerel deponuzdaki değişiklikleri sunucu deposuna göndermek için, değişikliklerinizi uygulamanız ve sonra sunucu deposuna göndermeniz gerekir. Değişikliklerinizi yürütmek için git komut aracınızı açın, yerel deponuzdaki dizine geçin ve aşağıdaki komutları verin.

```
git add --all
git commit -m "Description of your changes"
```

Tüm işlemeleri sunucuya göndermek için aşağıdaki komutu çalıştırın.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Hizmet yapılandırma değişikliklerini API Management hizmeti örneğine dağıtmak için

Yerel değişiklikleriniz kaydedildikten ve sunucu deposuna gönderildikten sonra, bunları API Management hizmet örneğinize dağıtabilirsiniz.

REST API kullanarak bu işlemi gerçekleştirme hakkında daha fazla bilgi için, bkz. [REST API kullanarak git değişikliklerini yapılandırma veritabanına dağıtma](/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Yerel git deposunun dosya ve klasör yapısı başvurusu

Yerel git deposundaki dosya ve klasörler, hizmet örneğiyle ilgili yapılandırma bilgilerini içerir.

| Öğe | Açıklama |
| --- | --- |
| kök API-Yönetim klasörü |Hizmet örneği için en üst düzey yapılandırmayı içerir |
| API 'ler klasörü |Hizmet örneğindeki API 'lerin yapılandırmasını içerir |
| Gruplar klasörü |Hizmet örneğindeki grupların yapılandırmasını içerir |
| ilkeler klasörü |Hizmet örneğindeki ilkeleri içerir |
| portalStyles klasörü |Hizmet örneğindeki geliştirici portalı özelleştirmeleri için yapılandırmayı içerir |
| Ürünler klasörü |Hizmet örneğindeki ürünlerin yapılandırmasını içerir |
| Şablonlar klasörü |Hizmet örneğindeki e-posta şablonlarının yapılandırmasını içerir |

Her klasör bir veya daha fazla dosya içerebilir ve bazı durumlarda bir veya daha fazla klasör, örneğin, her API, ürün veya grup için bir klasör olabilir. Her bir klasördeki dosyalar, klasör adı tarafından tanımlanan varlık türüne özeldir.

| Dosya türü | Amaç |
| --- | --- |
| json |İlgili varlıkla ilgili yapılandırma bilgileri |
| html |Genellikle Geliştirici Portalında görünen varlıkla ilgili açıklamalar |
| xml |İlke deyimleri |
| css |Geliştirici portalı özelleştirmesi için stil sayfaları |

Bu dosyalar yerel dosya sisteminizde oluşturulabilir, silinebilir, düzenlenebilir ve yönetilebilir ve değişiklikler API Management hizmet örneğinize geri dağıtılır.

> [!NOTE]
> Aşağıdaki varlıklar git deposunda yer alamaz ve git kullanılarak yapılandırılamaz.
>
> * [Kullanıcılar](/rest/api/apimanagement/2019-12-01/user)
> * [Abonelikler](/rest/api/apimanagement/2019-12-01/subscription)
> * Görünen Değerler
> * Stil dışındaki geliştirici portalı varlıkları
>

### <a name="root-api-management-folder"></a>Kök API-Yönetim klasörü
Kök `api-management` klasör, `configuration.json` hizmet örneği hakkında aşağıdaki biçimde en üst düzey bilgileri içeren bir dosya içerir.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

İlk dört ayar ( `RegistrationEnabled` , `UserRegistrationTerms` , `UserRegistrationTermsEnabled` ve), `UserRegistrationTermsConsentRequired` **Geliştirici Portalı** bölümündeki **kimlikler** sekmesinde aşağıdaki ayarlarla eşlenir.

| Kimlik ayarı | Eşleme |
| --- | --- |
| RegistrationEnabled |**Kullanıcı adı ve parola** kimlik sağlayıcısı 'nın varlığı |
| UserRegistrationTerms |**Kullanıcı kaydı** metin kutusuna kullanım koşulları |
| UserRegistrationTermsEnabled |**Kaydolma sayfasında kullanım koşullarını göster** onay kutusu |
| UserRegistrationTermsConsentRequired |**Izin iste** onay kutusu |
| Requireusersignınenabled |**Anonim kullanıcıları oturum açma sayfasına yeniden yönlendir** onay kutusu |

Sonraki dört ayar ( `DelegationEnabled` , `DelegationUrl` , `DelegatedSubscriptionEnabled` ve), `DelegationValidationKey` **Geliştirici Portalı** bölümündeki **temsili** sekmesinde aşağıdaki ayarlarla eşlenir.

| Temsili ayarı | Eşleme |
| --- | --- |
| DelegationEnabled |Kaydolma **&** kaydolma onay kutusu |
| DelegationUrl |**Temsili uç nokta URL 'si** metin kutusu |
| DelegatedSubscriptionEnabled |**Temsilci ürün aboneliği** onay kutusu |
| DelegationValidationKey |**Temsilci doğrulama anahtarı** metin kutusu |

Son ayarı, `$ref-policy` hizmet örneği için genel ilke deyimleri dosyası ile eşlenir.

### <a name="apis-folder"></a>API 'ler klasörü
`apis`Klasörü, hizmet örneğindeki her BIR API için aşağıdaki öğeleri içeren bir klasör içerir.

* `apis\<api name>\configuration.json` -Bu, API 'nin yapılandırmadır ve arka uç hizmeti URL 'SI ve işlemleriyle ilgili bilgileri içerir. Bu, [belirli BIR API](/rest/api/apimanagement/2019-12-01/apis/get) 'Yi biçimiyle Al ' i çağırmanız durumunda döndürülecek olan bilgilerden `export=true` de aynıdır `application/json` .
* `apis\<api name>\api.description.html` -Bu, API 'nin açıklamasıdır ve `description` [API varlığının](/java/api/com.microsoft.azure.storage.table.entityproperty)özelliğine karşılık gelir.
* `apis\<api name>\operations\` -Bu klasör `<operation name>.description.html` , API 'deki işlemlerle eşlenen dosyaları içerir. Her dosya API 'deki tek bir işlemin açıklamasını içerir ve bu, `description` REST API [işlem varlığının](/rest/api/visualstudio/operations/list#operationproperties) özelliği ile eşlenir.

### <a name="groups-folder"></a>Gruplar klasörü
`groups`Klasör, hizmet örneğinde tanımlanan her grup için bir klasör içerir.

* `groups\<group name>\configuration.json` -Bu, grubun yapılandırması. Bu, [belirli bir grup al](/rest/api/apimanagement/2019-12-01/group/get) işlemini çağırmanız durumunda döndürülecek olan bilgiler ile aynıdır.
* `groups\<group name>\description.html` -Bu, grubun açıklamasıdır ve `description` [Grup varlığının](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)özelliğine karşılık gelir.

### <a name="policies-folder"></a>ilkeler klasörü
`policies`Klasör, hizmet örneğiniz için ilke deyimlerini içerir.

* `policies\global.xml` -hizmet örneğiniz için genel kapsamda tanımlanan ilkeleri içerir.
* `policies\apis\<api name>\` -API kapsamında tanımlanmış bir ilkelerinize sahipseniz, bunlar bu klasörde bulunur.
* `policies\apis\<api name>\<operation name>\` klasör-işlem kapsamında tanımlanmış herhangi bir ilkeniz varsa, bunlar `<operation name>.xml` her işlem için ilke deyimleriyle eşlenen dosyalardaki bu klasörde bulunur.
* `policies\products\` -Ürün kapsamında tanımlanmış bir ilkeniz varsa, bunlar `<product name>.xml` her bir ürünün ilke deyimleriyle eşlenen dosyaları içeren bu klasörde bulunur.

### <a name="portalstyles-folder"></a>portalStyles klasörü
`portalStyles`Klasörü, hizmet örneği için geliştirici portalı özelleştirmeleri için yapılandırma ve stil sayfaları içerir.

* `portalStyles\configuration.json` -geliştirici portalı tarafından kullanılan stil sayfalarının adlarını içerir
* `portalStyles\<style name>.css` -Her `<style name>.css` Dosya, geliştirici portalı için stiller içerir ( `Preview.css` ve `Production.css` Varsayılan olarak).

### <a name="products-folder"></a>Ürünler klasörü
`products`Klasör, hizmet örneğinde tanımlanan her ürün için bir klasör içerir.

* `products\<product name>\configuration.json` -Bu, ürünün yapılandırmadır. Bu, [belirli bir ürün al](/rest/api/apimanagement/2019-12-01/product/get) işlemini çağırmanız durumunda döndürülecek olan bilgiler ile aynıdır.
* `products\<product name>\product.description.html` -Bu, ürünün açıklamasıdır ve `description` REST API [ürün varlığının](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) özelliğine karşılık gelir.

### <a name="templates"></a>templates
`templates`Klasör, hizmet örneğinin [e-posta şablonlarının](api-management-howto-configure-notifications.md) yapılandırmasını içerir.

* `<template name>\configuration.json` -e-posta şablonunun yapılandırması budur.
* `<template name>\body.html` -e-posta şablonunun gövdesidir.

## <a name="next-steps"></a>Sonraki adımlar
Hizmet örneğinizi yönetmenin diğer yolları hakkında daha fazla bilgi için bkz.:

* Aşağıdaki PowerShell cmdlet 'lerini kullanarak hizmet örneğinizi yönetin
  * [Hizmet dağıtımı PowerShell cmdlet başvurusu](/powershell/module/wds)
  * [Hizmet yönetimi PowerShell cmdlet başvurusu](/powershell/azure/servicemanagement/overview)
* REST API kullanarak hizmet örneğinizi yönetin
  * [API Management REST API başvurusu](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png
