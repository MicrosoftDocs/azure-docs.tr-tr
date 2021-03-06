---
title: UK resmi & UK NHS şema örnek denetimleri
description: UK RESMI ve UK NHS şeması örneklerinin denetim eşlemesi. Her denetim, değerlendirmede yardımcı olan bir veya daha fazla Azure Ilke tanımına eşlenir.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: bf10bcf7064306094664c1ff560187ef77645d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627575"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>UK RESMI ve UK NHS şeması örneklerinin denetim eşlemesi

Aşağıdaki makalede, UK RESMI ve UK NHS şeması örneklerinin UK RESMI ve UK NHS denetimleriyle nasıl eşlendiğini ayrıntılarıyla anlatılmaktadır. Denetimler hakkında daha fazla bilgi için bkz. [UK resmi](https://www.gov.uk/government/publications/government-security-classifications).

Aşağıdaki eşlemeler **UK resmi** ve **UK NHS** denetimlerine göre yapılır. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir denetim eşlemesine atlayın. Eşlenmiş denetimlerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Ardından, **\[ ÖNIZLEME \] denetimi UK resmi ve UK NHS 'ler denetimlerini bulup seçin ve denetim gereksinimleri yerleşik ilke girişimi ' ni desteklemek Için belirli VM uzantılarını dağıtın** .

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ile bir veya daha fazla ilke arasında genellikle bire bir veya tam eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md)bakın.

## <a name="1-data-in-transit-protection"></a>geçiş koruması 1 veri

Şema, depolama hesaplarına Güvenli olmayan bağlantıları denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ve Redis Cache Azure hizmetleri ile bilgi aktarımını güvence altına almanıza yardımcı olur.

- Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir
- Depolama hesaplarına güvenli aktarım etkinleştirilmelidir
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularından denetim sonuçlarını göster
- Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır

## <a name="23-data-at-rest-protection"></a>2,3 veri bekleyen koruma

Bu şema, belirli bir cryptograph denetimi uygulayan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ve zayıf şifreleme ayarlarının kullanımını denetleyerek, ilkenizi cryptograph denetimleri kullanarak zorlamanıza yardımcı olur. Azure kaynaklarınızın en iyi durumda olmayan şifreleme yapılandırmalarının nerede olabileceğini anlamak, kaynakların bilgi güvenliği ilkenize uygun şekilde yapılandırıldığından emin olmak için düzeltici eylemler almanıza yardımcı olabilir. Özellikle, bu şema tarafından atanan ilkeler Data Lake Storage hesapları için şifrelemeyi gerektirir; SQL veritabanlarında saydam veri şifrelemesi gerektir; depolama hesaplarında, SQL veritabanlarında, sanal makine disklerinde ve Otomasyon hesabı değişkenlerinde eksik şifrelemeyi denetleyin; depolama hesaplarına Güvenli olmayan bağlantıları denetleme ve Redis Cache; zayıf sanal makine parola şifrelemesini denetleme; ve şifrelenmemiş Service Fabric iletişimini denetleyin.

- Disk şifrelemesi sanal makinelere uygulanmalıdır
- Otomasyon hesabı değişkenleri şifrelenmelidir
- Service Fabric kümelerinde ClusterProtectionLevel özelliği EncryptAndSign olarak ayarlanmalıdır
- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir
- SQL DB saydam veri şifrelemesini dağıtma
- Data Lake Store hesaplarında şifreleme gerektir
- İzin verilen konumlar ("UK Güney" ve "UK Batı" için sabit olarak kodlanmış)
- Kaynak grupları için izin verilen konumlar ("UK Güney" ve "UK Batı" olarak kodlanmıştır)

## <a name="52-vulnerability-management"></a>5,2 güvenlik açığı yönetimi

Bu şema, eksik Endpoint Protection, eksik sistem güncelleştirmeleri, işletim sistemi güvenlik açıkları, SQL güvenlik açıkları ve sanal makine güvenlik açıklarını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak bilgi sistemi güvenlik açıklarını yönetmenize yardımcı olur. Bu Öngörüler, dağıtılan kaynaklarınızın güvenlik durumu hakkında gerçek zamanlı bilgiler sağlar ve düzeltme eylemlerinin önceliklerini belirlemenize yardımcı olabilir.

- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme
- Makinelerinize sistem güncelleştirmeleri yüklenmelidir
- Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir
- Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir
- SQL yönetilen örneğiniz üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Gelişmiş veri güvenliği SQL yönetilen örneğiniz üzerinde etkinleştirilmelidir
- Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir

## <a name="53-protective-monitoring"></a>5,3 koruyucu Izleme

Bu şema, Kısıtlanmamış erişim, liste etkinliğine izin verme ve tehditlere karşı koruyucu izleme sağlayan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak bilgi sistemi varlıklarını korumanıza yardımcı olur.

- Depolama hesapları, ağ erişimini kısıtlıyor olmalıdır
- Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir
- Olağanüstü durum kurtarma yapılandırması olmadan sanal makineleri denetleme
- Azure DDoS koruma standardı etkinleştirilmelidir
- Gelişmiş tehdit koruması türleri SQL yönetilen örneği gelişmiş veri güvenliği ayarları içinde ' All ' olarak ayarlanmalıdır
- Gelişmiş tehdit koruması türleri SQL Server Gelişmiş veri güvenliği ayarları 'nda ' tümü ' olarak ayarlanmalıdır
- SQL Server 'lar üzerinde tehdit algılamayı dağıtma
- Windows Server için varsayılan Microsoft ıaasantimalware uzantısını dağıt

## <a name="9-secure-user-management"></a>9 güvenli Kullanıcı Yönetimi 

Azure rol tabanlı erişim denetimi (Azure RBAC), Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olur.
Azure portal kullanarak, Azure kaynaklarına kimlerin erişebileceğini ve bunların izinlerini gözden geçirebilirsiniz. Bu şema, sahip ve/veya okuma/yazma izinlerine sahip olan dış hesapları, çok faktörlü kimlik doğrulamasının etkin olmadığı okuma ve/veya yazma izinlerine göre denetlemek için [Azure ilke](../../../policy/overview.md) tanımları atayarak erişim haklarını kısıtlayıp denetlemenize yardımcı olur.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir
- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir

## <a name="10-identity-and-authentication"></a>10 kimlik ve kimlik doğrulama

Bu şema, sahip ve/veya okuma/yazma izinlerine sahip olan dış hesapları, çok faktörlü kimlik doğrulamasının etkin olmadığı okuma ve/veya yazma izinlerine göre denetlemek için [Azure ilke](../../../policy/overview.md) tanımları atayarak erişim haklarını kısıtlayıp denetlemenize yardımcı olur.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir
- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir

Bu şema, SQL Server ve Service Fabric için Azure Active Directory kimlik doğrulamasının kullanımını denetlemek üzere Azure ilke tanımları atar. Azure Active Directory kimlik doğrulaması kullanmak, veritabanı kullanıcıları ve diğer Microsoft Hizmetleri için Basitleştirilmiş izin yönetimi ve merkezi kimlik yönetimine izin verebilir.

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır
- Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır

Bu şema Ayrıca, amortisman hesapları ve dış hesaplar dahil olmak üzere incelenmesi gereken denetim hesaplarına Azure Ilke tanımları atar. Gerektiğinde, hesapların oturum açması (veya kaldırılması) engellenebilir ve bu da Azure kaynaklarına erişim haklarını hemen kaldırır.
Bu şema, kaldırma için göz önünde bulundurmanız gereken amorti edilmiş hesabı denetlemek için iki Azure Ilke tanımı atar.

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir

Bu şema Ayrıca, Linux VM parola dosyası izinlerini yanlış ayarlandıklarında uyarı vermek üzere denetleyen bir Azure ilke tanımı atar. Bu tasarım, kimlik doğrulayıcılar güvenliğinin aşılmadığından emin olmak için düzeltici eylem gerçekleştirmenizi sağlar.

- Passwd dosyası izinleri 0644 olarak ayarlanan Linux VM 'lerinden denetim sonuçlarını göster

Bu şema, en düşük güç ve diğer parola gereksinimlerini zorlayamama Windows VM 'Leri denetleyen Azure Ilke tanımlarını atayarak güçlü parolalar zorlamanıza yardımcı olur. Parola gücü ilkesini ihlal eden VM 'lerin farkında, tüm VM Kullanıcı hesaplarının parolalarının ilkeyle uyumlu olduğundan emin olmak için düzeltici eylemler almanıza yardımcı olur.

- Parola karmaşıklığı ayarı etkin olmayan Windows VM 'lerinden denetim sonuçlarını göster
- Maksimum parola yaşı 70 gün olmayan Windows VM 'lerinden denetim sonuçlarını göster
- En az 1 günlük parola yaşı olmayan Windows VM 'lerinden denetim sonuçlarını göster
- Minimum parola uzunluğunu 14 karakter olarak kısıtlayan Windows VM 'lerinden denetim sonuçlarını göster
- Önceki 24 parolanın yeniden kullanılmasına izin veren Windows VM 'lerinden denetim sonuçlarını göster

Bu şema ayrıca Azure ilke tanımlarını atayarak Azure kaynaklarına erişimi denetlemenize yardımcı olur. Bu ilkeler, kaynaklara daha fazla izin vermeyi sağlayan kaynak türlerinin ve yapılandırmaların kullanımını denetler. Bu ilkeleri ihlal eden kaynakları anlamak, Azure kaynaklarının yetkili kullanıcılarla sınırlı olduğundan emin olmak için düzeltici eylemler almanıza yardımcı olabilir.

- Parolası olmayan hesaplara sahip Linux VM 'lerinden denetim sonuçlarını göster
- Parolasız uzak bağlantılara izin veren Linux VM 'lerinden denetim sonuçlarını göster
- Depolama hesaplarının yeni Azure Resource Manager kaynaklarına geçirilmesi gerekir
- Sanal makinelerin yeni Azure Resource Manager kaynaklara geçirilmesi gerekir
- Yönetilen diskleri kullanmayan VM 'Leri denetleme

## <a name="11-external-interface-protection"></a>11 harici arabirim koruması

Uygun güvenli Kullanıcı yönetimi için 25 ' ten fazla ilke kullanmaktan başka, bu şema, kısıtlanmamış depolama hesaplarını izleyen bir [Azure ilke](../../../policy/overview.md) tanımı atayarak hizmet arabirimlerini yetkisiz erişimden korumanıza yardımcı olur.
Sınırsız erişimi olan depolama hesapları, bilgi sisteminde bulunan bilgilere istenmeden erişime izin verebilir. Bu şema ayrıca sanal makinelerde Uyarlamalı uygulama denetimleri sağlayan bir ilke atar.

- Depolama hesapları, ağ erişimini kısıtlıyor olmalıdır
- Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir
- Internet 'e yönelik uç nokta ile erişim kısıtlı olmalıdır
- Uyarlamalı ağ sağlamlaştırma önerileri internet 'e yönelik sanal makinelere uygulanmalıdır
- Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir
- Sanal makinelerin yönetim bağlantı noktaları, tam zamanında ağ erişim denetimiyle korunmalıdır
- Işlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır
- Web uygulamaları için uzaktan hata ayıklama kapatılmalıdır
- API Apps için uzaktan hata ayıklama kapatılmalıdır

## <a name="13-audit-information-for-users"></a>Kullanıcılar için 13 denetim bilgileri

Bu şema, Azure kaynaklarında günlük ayarlarını denetleyen [Azure ilke](../../../policy/overview.md) tanımları atanarak sistem olaylarının günlüğe kaydedildiğinden emin olmanıza yardımcı olur.
Atanan bir ilke, sanal makinelerin belirli bir Log Analytics çalışma alanına günlük gönderip göndermemişse de denetler.

- Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir
- Tanılama ayarını denetle
- \[Önizleme \] : Linux VM 'ler için Log Analytics aracısı dağıtma
- \[Önizleme \] : Windows VM 'leri için Log Analytics aracısı dağıtma
- Sanal ağlar oluşturulduğunda Ağ İzleyicisi dağıt


## <a name="next-steps"></a>Sonraki adımlar

UK RESMI ve UK NHS şemaları denetim eşlemesini gözden geçirdiğinize göre, genel bakış ve bu örneği dağıtma hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [UK resmi ve UK NHS şemaları-genel bakış](./index.md) 
>  [UK resmi ve UK NHS şemaları-dağıtım adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
