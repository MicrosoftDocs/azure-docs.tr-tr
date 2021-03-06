---
title: Privileged Identity Management dağıtma (PıM)-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) dağıtımının nasıl planlanacağını açıklar.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036835"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management dağıtma (PıM)

Bu makale, Azure Active Directory (Azure AD) kuruluşunuzda Privileged Identity Management (PıM) dağıtımının nasıl planlanacağını açıklayan bir adım adım kılavuzdur. Yüksek ayrıcalıklı rollerdeki kullanıcıları mümkün olduğunca daha az güçlü yerleşik veya özel rollere yeniden atayabilir ve en ayrıcalıklı rolleriniz için tam zamanında rol atamaları planlayın. Bu makalede, hem dağıtım planlama hem de uygulama için öneriler sunuyoruz.

> [!TIP]
> Bu makalede, şu şekilde işaretlenmiş öğeleri göreceksiniz:
>
> : heavy_check_mark: **Microsoft şunları öneriyor**
>
> Bunlar genel önerilerdir ve yalnızca belirli kurumsal gereksinimleriniz için uygulandıklarında uygulamanız gerekir.

## <a name="licensing-requirements"></a>Lisanslama gereksinimleri

Privileged Identity Management kullanmak için, dizininiz aşağıdaki ücretli veya deneme lisanslarından birine sahip olmalıdır. Daha fazla bilgi için bkz. [Privileged Identity Management kullanılacak lisans gereksinimleri](subscription-requirements.md).

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Eğitim A5
- Microsoft 365 Kurumsal E5

## <a name="how-pim-works"></a>PıM nasıl kullanılır?

Bu bölümde Privileged Identity Management sürecinin ilgili bölümlerinin planlanmasına yönelik bir gözden geçirme sağlanmaktadır. Daha fazla bilgi için bkz. [Azure AD Privileged Identity Management nedir?](pim-configure.md)

1. Kullanıcıların ayrıcalıklı rollere uygun olması için Privileged Identity Management kullanmaya başlayın.
1. Uygun bir kullanıcının ayrıcalıklı rolünü kullanması gerektiğinde, Privileged Identity Management kullanarak rolü etkinleştirir.
1. Kullanıcının ayarlarında şu şekilde gerekli olabilir:

    - Multi-Factor Authentication kullanma
    - Etkinleştirme için onay iste
    - Etkinleştirme için bir iş nedeni sağlayın

1. Kullanıcı, rolünü başarıyla etkinleştirdikten sonra, ayarlanan süre için rol izinlerine sahip olurlar.
1. Yöneticiler denetim günlüğündeki tüm Privileged Identity Management etkinliklerinin geçmişini görüntüleyebilir. Ayrıca, Azure AD kuruluşlarını daha da güvenli hale getirirler ve erişim gözden geçirmeleri ve uyarılar gibi Privileged Identity Management özelliklerini kullanarak uyumluluk sağlayabilir.

## <a name="roles-that-can-be-managed-by-pim"></a>PıM tarafından yönetilebilecek roller

**Azure AD rollerinin** hepsi Azure Active Directory (genel yönetici, Exchange Yöneticisi ve Güvenlik Yöneticisi gibi). [Azure Active Directory Içindeki yönetici rolü izinlerinde](../roles/permissions-reference.md)roller ve bunların işlevleri hakkında daha fazla bilgi edinebilirsiniz. Yöneticilerinize hangi rollerin atanacağını belirlemeye yönelik yardım için bkz. [göreve göre en az ayrıcalıklı roller](../roles/delegate-by-task.md).

**Azure rolleri** , bir Azure kaynağı, kaynak grubu, abonelik veya yönetim grubuyla bağlantılı rollerdir. Yalnızca sahip, Kullanıcı erişimi Yöneticisi ve katkıda bulunan gibi yerleşik Azure rollerine ve ayrıca [özel rollere](../../role-based-access-control/custom-roles.md)tam zamanında erişim sağlamak için PIM 'yi kullanabilirsiniz. Azure rolleri hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi](../../role-based-access-control/overview.md).

Daha fazla bilgi için, [Privileged Identity Management içinde yönetileyemiyorum roller](pim-roles.md)bölümüne bakın.

## <a name="deployment-plan"></a>Dağıtım planı

Kuruluşunuzda Privileged Identity Management dağıtmadan önce, yönergeleri izleyin ve kuruluşunuzun ayrıcalıklı kimlik gereksinimlerine özel bir plan oluşturmanıza yardımcı olması için bu bölümdeki kavramları anlayın.

### <a name="identify-your-stakeholders"></a>Paydaşlarınızı tanımla

Aşağıdaki bölümde, projede yer alan tüm paydaşların belirlenmesi ve oturum açmanız, incelenmesi veya bilinçli olması gerekir. Azure AD rolleri için PıM ve Azure rolleri için PıM dağıtmak üzere ayrı tablolar içerir. Aşağıdaki tabloda bulunan paydaşlara kuruluşunuz için uygun şekilde ekleyin.

- Bu nedenle = bu projede oturumu Kapat
- R = bu projeyi gözden geçirin ve giriş sağlayın
- I = bu projenin bilgilendirilmesi

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Paydaşlar: Azure AD rolleri Için Privileged Identity Management

| Name | Rol | Eylem |
| --- | --- | --- |
| Ad ve e-posta | **Kimlik mimarı veya Azure genel Yöneticisi**<br/>Kimlik Yönetimi ekibinin, bu değişikliği kuruluşunuzda temel kimlik yönetimi altyapısına nasıl hizalamayı tanımlamaya yönelik bir temsilcisidir. | SO/R/ı |
| Ad ve e-posta | **Hizmet sahibi/satır Yöneticisi**<br/>Bir hizmetin veya bir hizmet grubunun BT sahiplerine bir temsilci. Bunlar, kararlar verirken ve takımlarına yönelik Privileged Identity Management almaya yardımcı olan bir anahtarlıyız. | SO/R/ı |
| Ad ve e-posta | **Güvenlik sahibi**<br/>Güvenlik ekibinden, planın kuruluşunuzun güvenlik gereksinimlerini karşıladığından emin olan bir temsilcisidir. | SO/R |
| Ad ve e-posta | **BT Destek Yöneticisi/Yardım Masası**<br/>BT destek kuruluşundan, bu değişikliğin bir yardım masası perspektifinden desteklenebilirliği hakkında geri bildirim sağlayabilen bir temsilcisidir. | R/ı |
| Pilot kullanıcılar için ad ve e-posta | **Ayrıcalıklı rol kullanıcıları**<br/>Ayrıcalıklı kimlik yönetiminin uygulandığı Kullanıcı grubu. Privileged Identity Management uygulandıktan sonra rollerinin nasıl etkinleştirileyeceğinizi bilmeleri gerekir. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Paydaşlar: Azure rolleri Için Privileged Identity Management

| Name | Rol | Eylem |
| --- | --- | --- |
| Ad ve e-posta | **Abonelik/kaynak sahibi**<br/>Privileged Identity Management dağıtmak istediğiniz her abonelik veya kaynağın BT sahiplerine bir temsilci | SO/R/ı |
| Ad ve e-posta | **Güvenlik sahibi**<br/>Güvenlik ekibinden, planın kuruluşunuzun güvenlik gereksinimlerini karşıladığı oturumu kapatan bir temsilcisidir. | SO/R |
| Ad ve e-posta | **BT Destek Yöneticisi/Yardım Masası**<br/>BT destek kuruluşundan, bu değişikliğin bir yardım masası perspektifinden desteklenebilirliği hakkında geri bildirim sağlayabilen bir temsilcisidir. | R/ı |
| Pilot kullanıcılar için ad ve e-posta | **Azure rolü kullanıcıları**<br/>Ayrıcalıklı kimlik yönetiminin uygulandığı Kullanıcı grubu. Privileged Identity Management uygulandıktan sonra rollerinin nasıl etkinleştirileyeceğinizi bilmeleri gerekir. | I |

### <a name="start-using-privileged-identity-management"></a>Privileged Identity Management'ı kullanmaya başlama

Planlama işleminin bir parçası olarak, [Privileged Identity Management](pim-getting-started.md) makalemizi izleyerek Privileged Identity Management hazırlamanız gerekir. Privileged Identity Management, dağıtımınız konusunda yardımcı olmak için tasarlanan bazı özelliklere erişmenizi sağlar.

Amacınız Azure kaynakları için Privileged Identity Management dağıtmaktır, [Privileged Identity Management makalesinde yönetmek için bulma Azure kaynaklarını](pim-resource-roles-discover-resources.md) izlemeniz gerekir. Yalnızca aboneliklerin ve yönetim gruplarının sahipleri, Privileged Identity Management tarafından yönetim altına bu kaynakları getirebilir. Yönetim altındayken, PıM işlevselliği yönetim grubu, abonelik, kaynak grubu ve kaynak dahil tüm düzeylerde sahipler için kullanılabilir. Azure kaynaklarınız için Privileged Identity Management dağıtmaya çalışan bir genel yöneticisiyseniz, [tüm Azure aboneliklerini yönetmek için erişimi](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , dizinde bulunan tüm Azure kaynaklarına erişim sağlamak üzere yükseltebilir. Ancak, Privileged Identity Management ile kaynaklarını yönetmeden önce, abonelik sahiplerinizin her birinden onay almanızı tavsiye ederiz.

### <a name="enforce-principle-of-least-privilege"></a>En az ayrıcalık ilkesini zorla

Kuruluşunuzda hem Azure AD hem de Azure rolleriniz için en az ayrıcalık ilkesini zorlediğinizden emin olmak önemlidir.

#### <a name="plan-least-privilege-delegation"></a>En az ayrıcalık temsili planlayın

Azure AD rolleri için, çoğu yöneticinin yalnızca bir veya iki özel ve daha az güçlü yönetici rolüne ihtiyacı olduğunda, kuruluşların genel yönetici rolünü bir dizi yöneticiye ataması yaygındır. Çok sayıda küresel yönetici veya diğer yüksek ayrıcalıklı rol sayesinde, ayrıcalıklı rol atamalarınızı yeterince yakından takip etmek zordur.

Azure AD rolleriniz için en az ayrıcalık ilkesini uygulamak için aşağıdaki adımları izleyin.

1. Kullanılabilir [Azure AD yerleşik rollerini](../roles/permissions-reference.md)okuyup anlayarak rollerin ayrıntı düzeyini anlayın. Siz ve takımınız Ayrıca, belirli görevler için en az ayrıcalıklı rolü açıklayan [Azure AD 'de kimlik görevine göre yönetici rollerine](../roles/delegate-by-task.md)başvurmalıdır.

1. Kuruluşunuzda ayrıcalıklı rollere sahip olan liste. Privileged Identity Management [bulma ve öngörüleri (Önizleme)](pim-security-wizard.md) kullanarak pozlandırmayı azaltabilirsiniz.

    ![Ayrıcalıklı roller aracılığıyla pozlandırmayı azaltmak için bulma ve Öngörüler (Önizleme) sayfası](./media/pim-deployment-plan/new-preview-page.png)

1. Kuruluşunuzdaki tüm genel Yöneticiler için, bu rolün neden gerekli olduğunu öğrenin. Ardından, bunları genel yönetici rolünden kaldırın ve Azure Active Directory içinde daha düşük ayrıcalığa sahip yerleşik roller veya özel roller atayın. BILGI, Microsoft şu anda yalnızca genel yönetici rolüne sahip 10 yöneticiyle ilgilidir. [Microsoft 'un Privileged Identity Management nasıl kullandığı hakkında](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)daha fazla bilgi edinin.

1. Diğer tüm Azure AD rolleri için, atama listesini gözden geçirin, role artık gerek olmayan yöneticileri ve bunları atamalarından kaldırın.

Son iki adımı otomatik hale getirmek için Privileged Identity Management erişim incelemelerini kullanabilirsiniz. [Privileged Identity Management Azure AD rolleri için erişim gözden geçirmesi başlatma](pim-how-to-start-security-review.md)' daki adımları izleyerek, bir veya daha fazla üyeye sahip olan her Azure AD rolü için bir erişim incelemesi ayarlayabilirsiniz.

![Azure AD rolleri için erişim gözden geçirme bölmesi oluşturma](./media/pim-deployment-plan/create-access-review.png)

Gözden geçirenleri **Üyeler (kendi)** olarak ayarlayın. Roldeki tüm kullanıcılar, erişim gereksinimi olduğunu onaylamasını isteyen bir e-posta alır. Ayrıca, kullanıcıların role ihtiyacı olan nedenleri bilmesi için Gelişmiş ayarlar ' da **onay sırasında neden iste** ' yi etkinleştirin. Bu bilgilere bağlı olarak, gereksiz rollerden kullanıcıları kaldırabilir veya bunları daha ayrıntılı yönetici rollerine devredebilir.

Erişim gözden geçirmeleri, kişilerin rollere erişimini gözden geçirmesine bildirmek için e-postalara bağımlıdır. E-postaları bağlantılı olmayan ayrıcalıklı hesaplarınız varsa, bu hesapların ikincil e-posta alanını doldurduğunuzdan emin olun. Daha fazla bilgi için bkz. [Azure AD 'de proxyAddresses özniteliği](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Azure Kaynak rolü temsilcisini planlayın

Azure abonelikleri ve kaynakları için, her bir abonelik veya kaynaktaki rolleri gözden geçirmek üzere benzer bir erişim gözden geçirme işlemi ayarlayabilirsiniz. Bu işlemin hedefi, her bir aboneliğe veya kaynağa bağlı olan sahip ve Kullanıcı erişimi yönetici atamalarının en aza indirmektir ve gereksiz atamaları kaldırır. Ancak kuruluşlar, belirli rolleri daha iyi anlamak (özellikle özel roller) olduğundan, genellikle her bir aboneliğin veya kaynağın sahibine bu tür görevleri devredebilir.

Kuruluşunuzda Azure rolleri için PıM dağıtmayı deneyen genel yönetici rolünüzde, her aboneliğe erişim sağlamak için [tüm Azure aboneliklerini yönetmek üzere erişimi de yükseltebilir](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) . Böylece, her bir abonelik sahibini bulabilir ve bunlarla birlikte çalışarak gereksiz atamaları kaldırabilir ve sahip rolü atamasını en aza indirdirebilirsiniz.

Azure aboneliği için sahip rolüne sahip kullanıcılar, Azure AD rolleri için daha önce açıklanan işleme benzer gereksiz rol atamalarını denetlemek ve kaldırmak için de [Azure kaynakları için erişim gözden geçirmeleri](pim-resource-roles-start-access-review.md) kullanabilir.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Hangi rol atamalarının Privileged Identity Management tarafından korunması gerektiğine karar verin

Kuruluşunuzdaki ayrıcalıklı rol atamalarını kaldırdıktan sonra, Privileged Identity Management hangi rollerin korunacağına karar vermeniz gerekir.

Bir rol Privileged Identity Management tarafından korunuyorsa, kendisine atanan uygun kullanıcılar, rol tarafından verilen ayrıcalıkları kullanmak için yükseltilmelidir. Yükseltme işlemi, çok faktörlü kimlik doğrulaması kullanılarak onay alma ve neden etkinleştirildikleri bir nedeni sağlama de içerebilir. Privileged Identity Management ayrıca bildirimler ve Privileged Identity Management ve Azure AD denetim olay günlükleri aracılığıyla yükseltme gerçekleştirebilir.

Privileged Identity Management ile korunacak rolleri seçme zor olabilir ve her kuruluş için farklı olacaktır. Bu bölümde Azure AD ve Azure rolleri için en iyi uygulama önerileri sunulmaktadır.

#### <a name="azure-ad-roles"></a>Azure AD rolleri

En fazla izne sahip Azure AD rollerinin korunmasını önceliklendirmek önemlidir. Tüm Privileged Identity Management müşteriler arasında kullanım desenlerine bağlı olarak, Privileged Identity Management tarafından yönetilen ilk 10 Azure AD rolü şunlardır:

1. Genel yönetici
1. Güvenlik yöneticisi
1. Kullanıcı yöneticisi
1. Exchange yöneticisi
1. SharePoint yöneticisi
1. Intune yöneticisi
1. Güvenlik okuyucusu
1. Hizmet yöneticisi
1. Faturalama yöneticisi
1. Skype Kurumsal yöneticisi

> [!TIP]
> : heavy_check_mark: **Microsoft** , güvenliği tehlikeye atıldığında en çok zararlı olan kullanıcılar olduklarından, tüm genel yöneticilerinizi ve güvenlik Privileged Identity Management yöneticilerini ilk bir adım olarak yönetmenizi önerir.

Kuruluşunuz için en önemli verileri ve izinleri göz önünde bulundurmanız önemlidir. Örnek olarak, bazı kuruluşlar verilere erişip temel iş akışlarını değiştirebilecekleri Privileged Identity Management kullanarak Power BI yönetici rollerini veya takımlar yönetici rollerini korumak isteyebilir.

Konuk kullanıcıların atandığı roller varsa, saldırılara açıktır.

> [!TIP]
> : heavy_check_mark: **Microsoft** , güvenliği aşılmış Konuk Kullanıcı hesaplarıyla ilişkili riski azaltmak için Privileged Identity Management kullanarak Konuk kullanıcılarla tüm rolleri yönetmenizi önerir.

Dizin okuyucu, Ileti merkezi okuyucu ve güvenlik okuyucusu gibi okuyucu rolleri, bazen yazma iznine sahip olmadıkları için diğer rollerden daha az önemli olarak kabul edilir. Ancak, bu hesaplara erişimi olan saldırganlar kişisel veriler gibi hassas verileri okuyabildiğinden, bu rolleri de koruyan bazı müşterilerimiz sunuyoruz. Kuruluşunuzda okuyucu rollerinin Privileged Identity Management kullanılarak yönetilmesini isteyip istememeye karar verirken bu riski göz önüne alın.

#### <a name="azure-roles"></a>Azure rolleri

Azure kaynağı için Privileged Identity Management kullanılarak hangi rol atamalarının yönetilmesi gerektiğine karar verirken, öncelikle kuruluşunuz için en çok önem taşıyan abonelikleri/kaynakları belirlemeniz gerekir. Bu abonelik/kaynak örnekleri şunlardır:

- En hassas verileri barındıran kaynaklar
- Temel, müşteriye yönelik uygulamaların bağımlı olduğu kaynaklar

Genel yöneticiyseniz, hangi aboneliklerin ve kaynakların en önemli olduğuna karar verirken, her abonelik tarafından yönetilen kaynakların bir listesini toplamak için kuruluşunuzdaki abonelik sahiplerine başvurmalısınız. Daha sonra, kaynakları tehlikeye çıkarak önem düzeyine göre gruplamak için abonelik sahipleriyle birlikte çalışın (düşük, orta, yüksek). Bu önem düzeyine göre Privileged Identity Management ile kaynakları yönetmeyi önceliklendirin.

> [!TIP]
> : heavy_check_mark: **Microsoft** , hassas abonelikler/kaynaklar içindeki tüm roller için Privileged Identity Management iş akışını ayarlamak üzere kritik hizmetlerin abonelik/kaynak sahipleri ile çalışmanızı önerir.

Azure kaynakları için Privileged Identity Management zamana sınırlı hizmet hesaplarını destekler. Hizmet hesaplarını, normal bir kullanıcı hesabını nasıl değerlendikiyle tam olarak aynı şekilde ele almanız gerekir.

Kritik olmayan abonelikler/kaynaklar için, tüm roller için Privileged Identity Management ayarlamanız gerekmez. Ancak, sahip ve Kullanıcı erişimi yönetici rollerini Privileged Identity Management ile korumanız hala gerekir.

> [!TIP]
> : heavy_check_mark: **Microsoft** , tüm aboneliklerdeki/kaynakların sahip rollerini ve Kullanıcı erişimi yönetici rollerini Privileged Identity Management kullanarak yönetmenizi önerir.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Rol atamak için bir grup kullanıp kullanmayacağınızı belirleyin

Tek tek kullanıcılar yerine gruba bir rolün atanması, stratejik bir karardır. Planlarken, rol atamalarını yönetmek için bir gruba bir rol atamayı göz önünde bulundurun:

- Bir role çok sayıda Kullanıcı atandı
- Rol atama yetkisini atamak istiyorsunuz

#### <a name="many-users-are-assigned-to-a-role"></a>Bir role çok sayıda Kullanıcı atandı

Bir role kimin atandığını ve ne zaman ihtiyaç duyduklarında bunlara göre atamalarını yönetmeyi izlemek, el ile yapıldığında zaman alabilir. Bir role bir grup atamak için, önce [bir rol atanabilir grubu oluşturun](../roles/groups-create-eligible.md) ve ardından grubu bir rol için uygun olarak atayın. Bu eylem, gruptaki herkese, rol üzerinde yükseltme için uygun olan bireysel kullanıcılarla aynı etkinleştirme sürecine konu vermez. Grup üyeleri, Privileged Identity Management etkinleştirme isteği ve onay işlemini kullanarak atamaları gruba tek tek etkinleştirir. Grup etkin değil, yalnızca kullanıcının grup üyeliği.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Rol atama yetkisini atamak istiyorsunuz

Grup sahibi, bir grubun üyeliğini yönetebilir. Azure AD rolü atanabilir gruplar için, yalnızca ayrıcalıklı rol yöneticisi, genel yönetici ve Grup sahipleri grup üyeliğini yönetebilir. Gruba yeni üyeler eklendiğinde, üye, atamanın uygun veya etkin olup olmadığı, grubun atandığı rollere erişim sağlar. Gerekli ayrıcalıkların kapsamını azaltmak üzere atanan bir rolün grup üyeliği yönetimine temsilci seçmek için Grup sahiplerini kullanın. Grubu oluştururken bir gruba sahip atama hakkında daha fazla bilgi için bkz. [Azure AD 'de rol atanabilir Grup oluşturma](../roles/groups-create-eligible.md).

> [!TIP]
> : heavy_check_mark: Microsoft, Azure AD rolü atanabilir grupları yönetim altına Privileged Identity Management göre **yapmanızı önerir** . Rol atanabilir bir grup, PıM tarafından yönetim altına alındıktan sonra ayrıcalıklı erişim grubu olarak adlandırılır. Grup üyelerini yönetebilmeniz için Grup sahiplerinin sahip rol atamasını etkinleştirmesini gerektirmek için PıM kullanın. PıM yönetimi altında grupları getirme hakkında daha fazla bilgi için bkz. [Privileged Identity Management ayrıcalıklı erişim grupları (Önizleme) getirme](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Hangi rol atamalarının kalıcı veya uygun olacağını belirleyin

Privileged Identity Management tarafından yönetilecek rol listesine karar verdikten sonra, hangi kullanıcıların uygun rolü ve kalıcı olarak etkin rolü alması gerektiğine karar vermelisiniz. Uygun roller yalnızca Privileged Identity Management atanabileceği için, Azure Active Directory ve Azure kaynakları aracılığıyla atanan normal roller kalıcı olarak etkindir.

> [!TIP]
> : heavy_check_mark: **Microsoft** , Azure AD rolleri ve Azure rolleri için, kalıcı genel yönetici rolüne sahip olması gereken, önerilen [iki kesme camı acil durum erişim hesaplarından](../roles/security-emergency-access.md)farklı şekilde sıfır kalıcı etkin atama yapmanızı önerir.

Yöneticinin azalmasına izin verdiğimiz halde, kuruluşların bu hakkı elde etmelerini bazen zorlaştırıyor. Bu kararı verirken göz önünde bulundurmanız gereken noktalar şunlardır:

- Yükselme sıklığı – Kullanıcı yalnızca ayrıcalıklı atamaya ihtiyaç duyuyorsa, kalıcı atamaya sahip olmamalıdır. Öte yandan, Kullanıcı kendi günlük işleri için role ihtiyaç duyuyorsa ve Privileged Identity Management kullanmak üretkenliği önemli ölçüde azalttıklarında, kalıcı rol için kabul edilebilir.
- Kuruluşunuza özel durumlar – uygun rolü verilen kişi, uzak bir takımdan veya yükseltme işlemini iletişim kuran ve zorlayan noktaya yönelik yüksek öncelikli bir yöneticiye ait ise kalıcı rol için kabul edilebilir.

> [!TIP]
> : heavy_check_mark: **Microsoft** , kalıcı rol atamaları olan kullanıcılar için yinelenen erişim İncelemeleri ayarlamanıza (herhangi birine sahip olmanız gerekir) tavsiye eder. Bu dağıtım planının son bölümünde yinelenen erişim incelemesi hakkında daha fazla bilgi edinin

### <a name="draft-your-privileged-identity-management-settings"></a>Privileged Identity Management ayarlarınızı taslağı

Privileged Identity Management çözümünüzü uygulamadan önce, kuruluşunuzun kullandığı her ayrıcalıklı rol için Privileged Identity Management ayarlarınızı taslağı yapmak iyi bir uygulamadır. Bu bölümde belirli roller için Privileged Identity Management ayarlarına bazı örnekler vardır (yalnızca başvuru amaçlıdır ve kuruluşunuz için farklı olabilir). Bu ayarların her biri, Microsoft 'un tablolarındaki önerilerle ayrıntılı olarak açıklanmıştır.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD rolleri için Privileged Identity Management ayarları

| Rol | MFA gerektirme | Bildirim | Olay bileti | Onay gerektir | Onaylayan | Etkinleştirme süresi | Kalıcı yönetici |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Genel Yönetici | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Diğer genel Yöneticiler | 1 Saat | Acil durum erişim hesapları |
| Exchange Yöneticisi | :heavy_check_mark: | :heavy_check_mark: | sayı | sayı | Yok | 2 saat | Yok |
| Yardım Masası Yöneticisi | sayı | sayı | :heavy_check_mark: | sayı | Yok | 8 saat | Yok |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Azure rolleri için Privileged Identity Management ayarları

| Rol | MFA gerektirme | Bildirim | Onay gerektir | Onaylayan | Etkinleştirme süresi | Etkin yönetici | Etkin süre sonu | Uygun süre sonu |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Kritik aboneliklerin sahibi | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Aboneliğin diğer sahipleri | 1 Saat | Yok | yok | 3 ay |
| Daha az kritik aboneliğin Kullanıcı erişimi Yöneticisi | :heavy_check_mark: | :heavy_check_mark: | sayı | Yok | 1 Saat | Yok | yok | 3 ay |
| Sanal Makine Katılımcısı | sayı | :heavy_check_mark: | sayı | Yok | 3 saat | Yok | yok | 6 ay |

Aşağıdaki tabloda ayarların her biri açıklanmaktadır.

| Ayar | Açıklama |
| --- | --- |
| Rol | Ayarlarını tanımladığınız rolün adı. |
| MFA gerektirme | Rolü etkinleştirmeden önce uygun kullanıcının MFA gerçekleştirmesi gerekip gerekmediği.<br/><br/> : heavy_check_mark: Microsoft, özellikle rollerin Konuk kullanıcıları varsa, tüm yönetici rolleri için MFA 'yı zorunlu **kılmanızı önerir** . |
| Bildirim | True olarak ayarlanırsa, genel yönetici, ayrıcalıklı rol yöneticisi ve uygun bir kullanıcı rolü etkinleştirdiğinde kuruluştaki Güvenlik Yöneticisi bir e-posta bildirimi alır.<br/><br/>**Note:** Bazı kuruluşların yönetici hesaplarına bağlı bir e-posta adresi yoksa, bu e-posta bildirimlerini almak için, yöneticilerin bu e-postaları alabilmesi için alternatif bir e-posta adresi ayarlamanız gerekir. |
| Olay bileti | Uygun kullanıcının rolünü etkinleştirirken bir olay bileti numarası kaydetmesi gerekip gerekmediği. Bu ayar, istenmeyen etkinleştirmeleri azaltmak için bir kuruluşun iç olay numarasıyla her bir etkinleştirmeyi belirlemesine yardımcı olur.<br/><br/> : heavy_check_mark: Microsoft, Privileged Identity Management iç sisteminize bağlamak için olay bilet numaralarının avantajlarından **faydalanmanızı önerir** . Bu yöntem, etkinleştirme için bağlam gerektiren onaylayanlar için yararlı olabilir. |
| Onay gerektir | Rolü etkinleştirmek için uygun kullanıcının onay alması gerekip gerekmediği.<br/><br/> : heavy_check_mark: **Microsoft** , en fazla izne sahip roller için onay ayarlamanızı önerir. Tüm Privileged Identity Management müşterilerin, genel yöneticinin, Kullanıcı yöneticisinin, Exchange yöneticisinin, güvenlik yöneticisinin ve parola yöneticisinin kullanım düzenlerini temel alan, onay kurulumu ile en yaygın rollerdir. |
| Onaylayan | Uygun rolü etkinleştirmek için onay gerekiyorsa, isteği onaylaması gereken kişileri listeleyin. Varsayılan olarak, Privileged Identity Management, onaylayanı kalıcı veya uygun olup olmadıklarında ayrıcalıklı rol yöneticisi olan tüm kullanıcılar olarak ayarlar.<br/><br/>**Note:** Bir kullanıcı hem bir Azure AD rolüne hem de rolün bir onaylayana uygun ise kendilerini onaylayamaz.<br/><br/> : heavy_check_mark: **Microsoft** , bir genel yönetici yerine rol ve sık kullananlar hakkında bilgi sahibi olan kullanıcılar için onaylayanları seçmenizi önerir. |
| Etkinleştirme süresi | Kullanıcı, süresi dolmadan önce rolde etkinleştirilecek zaman uzunluğu. |
| Kalıcı yönetici | Rol için kalıcı yönetici olacak (hiçbir şekilde etkinleştirmesi gerekmez) kullanıcıların listesi.<br/><br/> : heavy_check_mark: **Microsoft** , genel Yöneticiler hariç tüm roller için sıfır sistem yöneticisi olmasını önerir. BT hakkında daha fazla bilgi edinmek isteyen kişiler ve bu planın kalıcı olarak etkin olması gerekir. |
| Etkin yönetici | Azure kaynakları için etkin yönetici, rolünü kullanmak için hiçbir şekilde etkinleştirmesi gereken kullanıcıların listesidir. Bu liste, kullanıcının bu rolü kaybedeceğinizi için bir sona erme saati ayarlayabildiğinden Azure AD rollerinde olduğu gibi kalıcı yönetici olarak adlandırılmaz. |
| Etkin süre sonu | Azure rollerinin etkin rol atamaları, yapılandırılan süreden sonra sona eriyor. 15 gün, 1 ay, 3 ay, 6 ay, 1 yıl veya kalıcı olarak etkin seçeneklerinden birini belirleyebilirsiniz. |
| Uygun süre sonu | Azure rollerinin uygun rol atamaları bu süreden sonra sona eriyor. 15 gün, 1 ay, 3 ay, 6 ay, 1 yıl veya kalıcı olarak uygun bir seçim yapabilirsiniz. |

## <a name="implementation-plan"></a>Uygulama planı

Uygun planlama temeli, Azure Active Directory ile bir uygulamayı başarıyla dağıtabilmeniz için temeldir.  Başarılı dağıtımlar için zamanı azaltırken, ekleme işlemini kolaylaştıran akıllı güvenlik ve tümleştirme sağlar.  Bu birleşim, son kullanıcılarınız için zaman hafifletmek için uygulamanızın kolayca tümleştirilebilmesini sağlar.

### <a name="identify-test-users"></a>Test kullanıcılarını tanımla

Bu bölümü, uygulamayı doğrulamak üzere bir grup kullanıcıyı ve kullanıcı grubunu belirlemek için kullanın. Planlama bölümünde seçtiğiniz ayarlara bağlı olarak, her bir rol için test etmek istediğiniz kullanıcıları belirlersiniz.

> [!TIP]
> : heavy_check_mark: **Microsoft** , her BIR Azure AD rolünün hizmet sahiplerini test kullanıcıları olarak yapmanızı önerir ve bu sayede işleme yönelik bir iç advocator haline gelir.

Bu tabloda, rollerin ayarlarının çalıştığını doğrulayan test kullanıcılarını tanımla.

| Rol adı | Test kullanıcıları |
| --- | --- |
| &lt;Rol adı&gt; | &lt;Rolü test etmek için kullanıcılar&gt; |
| &lt;Rol adı&gt; | &lt;Rolü test etmek için kullanıcılar&gt; |

### <a name="test-implementation"></a>Test uygulama

Test kullanıcılarını tanımladığınıza göre, test kullanıcılarınız için Privileged Identity Management yapılandırmak üzere bu adımı kullanın. Kuruluşunuz Azure portal Privileged Identity Management kullanmak yerine kendi iç uygulamanıza Privileged Identity Management iş akışını eklemek isterse, Privileged Identity Management içindeki tüm işlemler [Graph API 'si](/graph/api/resources/privilegedidentitymanagement-root)aracılığıyla da desteklenir.

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Azure AD rolleri için Privileged Identity Management yapılandırma

1. [Azure AD rol ayarlarını,](pim-how-to-change-default-settings.md) planladıklarınız temelinde yapılandırın.

1. **Azure AD rolleri**' ne gidin, **Roller**' i seçin ve ardından yapılandırdığınız rolü seçin.

1. Test kullanıcıları grubu için, zaten kalıcı bir yöneticse, bunları arayarak ve satır üzerinde üç noktayı seçerek bunları kalıcı durumundan uygun hale getirerek uygun hale getirebilirsiniz. Henüz rol atamaları yoksa, [yeni uygun bir atama](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)yapabilirsiniz.

1. Test etmek istediğiniz tüm roller için 1-3 arasındaki adımları yineleyin.

1. Test kullanıcılarını ayarladıktan sonra, [Azure AD rolünü nasıl etkinleştireceğinize](pim-how-to-activate-role.md)ilişkin bağlantıyı göndermeniz gerekir.

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Azure rolleri için Privileged Identity Management yapılandırma

1. Test etmek istediğiniz bir abonelik veya kaynak içindeki bir rol için [Azure Kaynak rolü ayarlarını yapılandırın](pim-resource-roles-configure-role-settings.md) .

1. Bu abonelik için **Azure kaynakları** ' na gidin ve **Roller**' i seçin, yapılandırdığınız rolü seçin.

1. Test kullanıcıları grubu için, zaten etkin bir yöneticse, bunları arayarak ve [rol atamasını güncelleyerek](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)uygun hale getirebilirsiniz. Henüz rol yoksa, [Yeni bir rol atayabilirsiniz](pim-resource-roles-assign-roles.md#assign-a-role).

1. Test etmek istediğiniz tüm roller için 1-3 arasındaki adımları yineleyin.

1. Test kullanıcılarını ayarladıktan sonra, [Azure Kaynak rolünü nasıl etkinleştireceğinize](pim-resource-roles-activate-your-roles.md)ilişkin bağlantıyı göndermeniz gerekir.

Roller için ayarladığınız tüm yapılandırmanın doğru çalışıp çalışmadığını doğrulamak için bu aşamayı kullanmanız gerekir. Testlerinizi belgelemek için aşağıdaki tabloyu kullanın. Ayrıca, etkilenen kullanıcılarla iletişimi iyileştirmek için bu aşamayı kullanmanız gerekir.

| Rol | Etkinleştirme sırasında beklenen davranış | Gerçek sonuçlar |
| --- | --- | --- |
| Genel Yönetici | (1) MFA gerektir<br/>(2) onay gerektir<br/>(3) onaylayan, bildirimi alır ve onaylayabilir<br/>(4) rolün ön ayar zamanından sonra süresi doluyor |  |
| *X* Aboneliğinin sahibi | (1) MFA gerektir<br/>(2) uygun atama, yapılandırılan süre dolduktan sonra sona erecek |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Privileged Identity Management etkilenen hissedarlarla iletişim kurun

Privileged Identity Management dağıtmak, ayrıcalıklı rol kullanıcıları için ek adımlar sunar. Privileged Identity Management, ayrıcalıklı kimliklerle ilişkili güvenlik sorunlarını büyük ölçüde azaltsa da, kuruluşun genelinde dağıtımdan önce değişikliğin etkin bir şekilde bildirilmesi gerekir. Etkilenen yöneticilerin sayısına bağlı olarak kuruluşlar genellikle dahili bir belge, bir video veya değişiklik hakkında bir e-posta oluşturmayı tercih etmesidir. Bu iletişimlere genellikle şunlar dahildir:

- PıM nedir?
- Kuruluşun avantajı nedir?
- Kimler etkilenecek
- Ne zaman PıM alınacaktır
- Kullanıcıların rolünü etkinleştirmesi için hangi ek adımların gerekli olacağı
    - Belgelerimize bağlantı göndermeniz gerekir:
    - [Azure AD rollerini etkinleştirme](pim-how-to-activate-role.md)
    - [Azure rollerini etkinleştirin](pim-resource-roles-activate-your-roles.md)
- PıM ile ilgili herhangi bir sorun için iletişim bilgileri veya Yardım Masası bağlantısı

> [!TIP]
> : heavy_check_mark: **Microsoft** , yardım masasına/destek ekibinize, bunları Privileged Identity Management iş akışı (kuruluşunuzun BIR dahili BT destek ekibi varsa) üzerinden kılavuzluk etmek için zaman ayarlamanızı önerir. Bunlara uygun belge ve iletişim bilgilerinizi sağlayın.

### <a name="move-to-production"></a>Üretime taşıma

Testiniz tamamlandıktan ve başarılı olduktan sonra, Privileged Identity Management yapılandırmanızda tanımladığınız her rolün tüm kullanıcıları için test aşamalardaki tüm adımları yineleyerek Privileged Identity Management üretime taşıyın. Azure AD rolleri için Privileged Identity Management, kuruluşlar, diğer roller için Privileged Identity Management test etmeden ve kullanıma almadan önce genel Yöneticiler için Privileged Identity Management genellikle test ve kullanıma alınıyor. Azure kaynağı için, kuruluşlar her seferinde bir Azure aboneliği Privileged Identity Management test ve kullanıma alma.

### <a name="if-a-rollback-is-needed"></a>Geri alma gerekirse

Privileged Identity Management, üretim ortamında istendiği şekilde çalışıdığı takdirde, aşağıdaki geri alma adımları Privileged Identity Management ayarlamadan önce bilinen iyi duruma geri dönmenize yardımcı olabilir:

#### <a name="azure-ad-roles"></a>Azure AD rolleri

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. **Azure AD Privileged Identity Management** açın.
1. **Azure AD rolleri** ' ni seçin ve ardından **Roller**' i seçin.
1. Yapılandırdığınız her bir rol için uygun bir atamaya sahip tüm kullanıcılar için üç nokta (**...**) simgesini seçin.
1. Rol atamasını kalıcı hale getirmek için **kalıcı yap** seçeneğini belirleyin.

#### <a name="azure-roles"></a>Azure rolleri

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. **Azure AD Privileged Identity Management** açın.
1. **Azure kaynakları** ' nı seçin ve ardından geri almak istediğiniz bir abonelik veya kaynak seçin.
1. **Rolleri** seçin.
1. Yapılandırdığınız her bir rol için uygun bir atamaya sahip tüm kullanıcılar için üç nokta (**...**) simgesini seçin.
1. Rol atamasını kalıcı hale getirmek için **kalıcı yap** seçeneğini belirleyin.

## <a name="next-steps-after-deploying"></a>Dağıtımdan sonraki adımlar

Üretimde Privileged Identity Management başarıyla dağıtımı, kuruluşunuzun ayrıcalıklı kimliklerinin güvenli hale getirilmesi açısından önemli bir adımdır. Privileged Identity Management dağıtımı ile, güvenlik ve uyumluluk için kullanmanız gereken ek Privileged Identity Management özellikleri gelir.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Ayrıcalıklı erişiminizi korumak için Privileged Identity Management uyarıları kullanın

Kuruluşunuzun güvenliğini sağlamak için Privileged Identity Management yerleşik uyarı işlevlerini kullanma hakkında daha fazla bilgi için bkz. [güvenlik uyarıları](pim-how-to-configure-security-alerts.md#security-alerts). Bu uyarılar şunları içerir: Yöneticiler ayrıcalıklı roller kullanmıyor, roller Privileged Identity Management dışında atanıyor, roller çok sık ve daha fazla etkinleştiriliyor. Kuruluşunuzu tam olarak korumak için, uyarı listenizde düzenli olarak gidip sorunları düzelmelisiniz. Uyarılarınızı aşağıdaki şekilde görüntüleyebilir ve çözebilirsiniz:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. **Azure AD Privileged Identity Management** açın.
1. **Azure AD rolleri** ' ni seçin ve ardından **Uyarılar**' ı seçin.

> [!TIP]
> : heavy_check_mark: **Microsoft** , yüksek önem derecesine sahip olarak işaretlenen tüm uyarılarla doğrudan işlem yapmanızı önerir. Orta ve düşük önem derecesine sahip uyarılar için, bir güvenlik tehdidi olduğunu düşündüğünüz takdirde bilgilendirilmesi ve değişiklikler yapmanız gerekir.

Belirli uyarılardan herhangi biri yararlı değilse veya kuruluşunuz için uygulanmadığından, uyarıları her zaman Uyarılar sayfasında kapatabilirsiniz. Bu ayırtıcı 'yi her zaman Azure AD ayarları sayfasında geri döndürebilirsiniz.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Kuruluşunuzun ayrıcalıklı kimliklerini düzenli olarak denetlemek için yinelenen erişim İncelemeleri ayarlayın

Erişim gözden geçirmeleri, kullanıcıların ayrıcalıklı rollere veya belirli gözden geçirenlere atanmasını, her kullanıcının ayrıcalıklı kimliğe ihtiyacı olup olmadığını sormanız için en iyi yoldur. Saldırı yüzeyini azaltmak ve uyumlu kalmak istiyorsanız erişim gözden geçirmeleri harika. Erişim gözden geçirmesi başlatma hakkında daha fazla bilgi için bkz. [Azure AD rolleri erişim İncelemeleri](pim-how-to-start-security-review.md) ve [Azure rolleri erişim İncelemeleri](pim-resource-roles-start-access-review.md). Bazı kuruluşlarda, bazı kuruluşlar için düzenli erişim incelemesi gerçekleştirmek gerekir, çünkü diğer bir deyişle, diğer bir deyişle, diğer bir deyişle, kuruluşunuz genelinde en az ayrıcalık sorumlusunu zorlamak için en iyi yoldur.

> [!TIP]
> : heavy_check_mark: **Microsoft** , tüm Azure AD ve Azure rolleriniz için üç aylık erişim gözden geçirmeleri ayarlamanızı önerir.

Çoğu durumda, Azure AD rolleri için gözden geçiren, Azure rolleri gözden geçireni, rolün bulunduğu aboneliğin sahibidir. Ancak, çoğu zaman şirketlerin belirli bir kişinin e-posta adresiyle bağlantılı olmayan ayrıcalıklı hesaplara sahip olduğu durumdur. Bu durumlarda, hiç kimse okuma ve erişimi İnceleme.

> [!TIP]
> : heavy_check_mark: **Microsoft** , düzenli olarak denetlenen bir e-posta adresiyle bağlantılı olmayan ayrıcalıklı rol atamalarına sahip tüm hesaplar için ikincil bir e-posta adresi eklemenizi önerir

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Güvenlik ve uyumluluğu geliştirmek için denetim günlüğünden en iyi şekilde yararlanın

Denetim günlüğü, güncel kalabileceğiniz ve yönetmeliklerle uyumlu olabilecek yerdir. Privileged Identity Management Şu anda, tüm kuruluşunuzun geçmişini denetim günlüğü içinde, şunlar da dahil olmak üzere bir 30 günlük geçmişi depolar:

- Uygun rollerin etkinleştirilmesi/devre dışı bırakılması
- Privileged Identity Management içinde ve dışında rol atama etkinlikleri
- Rol ayarlarındaki değişiklikler
- Onay kurulumu ile rol etkinleştirme için etkinlikleri isteme/onaylama/reddetme
- Uyarılara Güncelleştir

Genel yönetici veya ayrıcalıklı rol yöneticisiyseniz denetim günlüklerine erişebilirsiniz. Daha fazla bilgi için bkz. Azure [ad rolleri için denetim geçmişi](pim-how-to-use-audit-log.md) ve [Azure rolleri için denetim geçmişi](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **Microsoft** , en az bir yöneticinin tüm denetim olaylarını haftalık olarak okumasını ve denetim olaylarınızı aylık olarak dışarı aktarmanız önerir.

Denetim olaylarınızı daha uzun bir süre için otomatik olarak depolamak istiyorsanız, Privileged Identity Management Denetim günlüğü otomatik olarak [Azure AD denetim günlükleriyle](../reports-monitoring/concept-audit-logs.md)eşitlenir.

> [!TIP]
> : heavy_check_mark: **Microsoft** , daha fazla güvenlik ve uyumluluk Için bir Azure depolama hesabındaki denetim olaylarını arşivlemek üzere [Azure günlük izlemeyi](../reports-monitoring/concept-activity-logs-azure-monitor.md) ayarlamanızı önerir.
