---
title: Şirket içi veri ağ geçidini yükleme
description: Şirket içindeki verilere Azure Logic Apps erişmeden önce şirket içi veri ağ geçidini indirip yükleyin
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 4b2559ad20036870c6df5c0662bb973f35155bfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576807"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps için şirket içi veri ağ geçidi yükleme

[Azure Logic Apps 'den şirket içi veri kaynaklarına bağlanabilmeniz için](../logic-apps/logic-apps-gateway-connection.md), Şirket [içi veri ağ geçidini](https://aka.ms/on-premises-data-gateway-installer) yerel bir bilgisayara indirip yükleyin. Ağ geçidi, şirket içindeki veri kaynaklarıyla mantıksal uygulamalarınız arasında hızlı veri aktarımı ve şifreleme gerçekleştiren bir köprü gibi çalışır. Power otomatikleştir, Power BI, Power Apps ve Azure Analysis Services gibi diğer bulut hizmetleriyle aynı ağ geçidi yüklemesini kullanabilirsiniz. Bu hizmetlerle ağ geçidini kullanma hakkında daha fazla bilgi için şu makalelere bakın:

* [Microsoft Power otomatikleştirir şirket içi veri ağ geçidi](/power-automate/gateway-reference)
* [Microsoft Power BI şirket içi veri ağ geçidi](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps şirket içi veri ağ geçidi](/powerapps/maker/canvas-apps/gateway-reference)
* [Şirket içi veri ağ geçidini Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Bu makalede şirket içi veri ağ geçidinizi indirme, yükleme ve kurma işlemlerinin yanı sıra Azure Logic Apps ' dan şirket içi veri kaynaklarına erişebilirsiniz. Bu konunun ilerleyen kısımlarında [Data Gateway 'in nasıl çalıştığı](#gateway-cloud-service) hakkında daha fazla bilgi edinebilirsiniz. Ağ Geçidi hakkında daha fazla bilgi için bkz. [Şirket içi ağ geçidi](/data-integration/gateway/service-gateway-onprem)nedir? Ağ geçidi yükleme ve yönetim görevlerini otomatikleştirmek için, [veri ağ geçidi PowerShell cmdlet 'leri](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)için PowerShell Galerisi ' ni ziyaret edin.

<a name="requirements"></a>

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Aboneliği olan bir Azure hesabınız yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * Azure hesabınızın, gibi görünen bir iş hesabı veya okul hesabı olması gerekir `username@contoso.com` . Azure B2B (konuk) hesaplarını veya veya gibi kişisel Microsoft hesaplarını kullanamazsınız @hotmail.com @outlook.com .

    > [!NOTE]
    > Bir Microsoft 365 teklifine kaydolduysanız ve iş e-posta adresinizi sağlamadıysanız adresiniz şöyle görünebilir `username@domain.onmicrosoft.com` . Hesabınız bir Azure AD kiracısında depolanıyor. Çoğu durumda, Azure hesabınız için Kullanıcı asıl adı (UPN) e-posta adresiniz ile aynıdır.

    Microsoft hesabı ilişkili bir [Visual Studio standart aboneliğini](https://visualstudio.microsoft.com/vs/pricing/) kullanmak için, önce [bir Azure AD kiracısı oluşturun](../active-directory/develop/quickstart-create-new-tenant.md) veya varsayılan dizini kullanın. Dizine bir parolası olan bir kullanıcı ekleyin ve bu kullanıcıya Azure aboneliğinize erişim izni verin. Daha sonra bu Kullanıcı adı ve parolayla ağ geçidi yüklemesi sırasında oturum açabilirsiniz.

  * Azure hesabınız yalnızca tek bir [Azure Active Directory (Azure AD) kiracısına veya dizine](../active-directory/fundamentals/active-directory-whatis.md#terminology)ait olmalıdır. Yerel bilgisayarınızda ağ geçidini yüklemek ve yönetmek için aynı Azure hesabını kullanmanız gerekir.

  * Ağ geçidini yüklediğinizde, Azure hesabınızla oturum açın ve ağ geçidi yüklemenizi Azure hesabınıza ve yalnızca bu hesaba bağlar. Aynı ağ geçidi yüklemesini birden çok Azure hesabı veya Azure AD kiracısından bağlayamazsınız.

  * Azure portal daha sonra, ağ geçidi yüklemenize bağlanan bir Azure ağ geçidi kaynağı oluşturmak için aynı Azure hesabını kullanmanız gerekir. Yalnızca bir ağ geçidi yüklemesini ve bir Azure Gateway kaynağını birbirlerine bağlayabilirsiniz. Ancak Azure hesabınız, Azure ağ geçidi kaynağıyla ilişkili farklı ağ geçidi yüklemelerine bağlanabilir. Mantıksal uygulamalarınız daha sonra bu ağ geçidi kaynağını, şirket içi veri kaynaklarına erişebilen Tetikleyiciler ve eylemlerde kullanabilir.

* Yerel bilgisayarınız için gereksinimler şunlardır:

  **Minimum gereksinimler**

  *  .NET Framework 4.7.2
  * Windows 7 veya Windows Server 2008 R2 64 bit sürümü (ya da sonraki sürümler)

  **Önerilen gereksinimler**

  * 8 çekirdekli CPU
  * 8 GB bellek
  * Windows Server 2012 R2 veya üzeri 64 bit sürümü
  * Biriktirme için katı hal sürücüsü (SSD) depolaması

  > [!NOTE]
  > Ağ Geçidi, Windows Server çekirdeğini desteklemez.

* **İlgili konular**

  * Şirket içi veri ağ geçidini, etki alanı denetleyicisi değil yalnızca yerel bir bilgisayara yükler. Ağ geçidini, veri kaynağınız ile aynı bilgisayara yüklemenize gerek yoktur. Tüm veri kaynaklarınız için yalnızca bir ağ geçidine ihtiyacınız vardır. bu nedenle, her veri kaynağı için ağ geçidini yüklemeniz gerekmez.

    > [!TIP]
    > Gecikme süresini en aza indirmek için, ağ geçidini veri kaynağınıza veya aynı bilgisayara mümkün olduğunca yakın bir şekilde yükleyebilirsiniz.

  * Ağ geçidini kablolu ağ üzerinde bulunan ve internet 'e bağlı olan yerel bir bilgisayara yükler, her zaman açık ve uyku moduna geçmez. Aksi takdirde, ağ geçidi çalıştırılamaz ve performans kablosuz bir ağdan düşebilir.

  * Windows kimlik doğrulamasını kullanmayı planlıyorsanız, ağ geçidini, veri kaynaklarınızla aynı Active Directory ortamına üye olan bir bilgisayara yüklediğinizden emin olun.

  * Ağ geçidinizin yüklemeniz için seçtiğiniz bölge, daha sonra mantıksal uygulamanız için Azure Gateway kaynağını oluştururken seçmeniz gereken konumdur. Bu bölge, varsayılan olarak Azure kullanıcı hesabınızı yöneten Azure AD kiracınızla aynı konumdadır. Ancak, ağ geçidi yüklemesi sırasında veya daha sonra konumunu değiştirebilirsiniz.

    > [!IMPORTANT]
    > Ağ geçidi kurulumu sırasında, Azure Kamu [bulutundaki](../azure-government/compare-azure-government-global-azure.md)bir Azure Active Directory (Azure AD) kiracısıyla Ilişkili Azure Kamu hesabınızla oturum açtıysanız **bölge Değiştir** komutu kullanılamaz. Ağ Geçidi, Kullanıcı hesabınızın Azure AD kiracısı ile aynı bölgeyi otomatik olarak kullanır.
    > 
    > Azure Kamu hesabınızı kullanmaya devam etmek, ancak bunun yerine genel çok kiracılı Azure ticari bulutunda çalışacak ağ geçidini ayarlamak için, önce Kullanıcı adı ile ağ geçidi yüklemesi sırasında oturum açın `prod@microsoft.com` . Bu çözüm, ağ geçidini genel çok kiracılı Azure bulutunu kullanmaya zorlar, ancak yine de Azure Kamu hesabınızı kullanmaya devam etmenizi sağlar.

  * Ağ Geçidi yüklemenizi güncelleştiriyorsanız, temizleyici bir deneyim için önce geçerli ağ geçidinizi kaldırın.

    En iyi uygulama olarak desteklenen bir sürüm kullandığınızdan emin olun. Microsoft, her ay şirket içi veri ağ geçidinde yeni bir güncelleştirme yayınlar ve şu anda yalnızca şirket içi veri ağ geçidi için son altı yayını desteklemektedir. Kullanmakta olduğunuz sürümle ilgili sorunlarla karşılaşırsanız, sorununuz en son sürümde çözümlenebildiğinden [en son sürüme yükseltmeyi](https://aka.ms/on-premises-data-gateway-installer) deneyin.

  * Ağ geçidinde iki mod vardır: yalnızca Power BI için geçerli olan standart mod ve kişisel mod. Aynı bilgisayarda aynı modda çalışan birden fazla ağ geçidi olamaz.

  * Azure Logic Apps ağ geçidi aracılığıyla okuma ve yazma işlemlerini destekler. Ancak, bu işlemlerin [Yük boyutuyla ilgili limitleri](/data-integration/gateway/service-gateway-onprem#considerations)vardır.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Veri ağ geçidi yükleme

1. [Ağ geçidi yükleyicisini yerel bir bilgisayarda indirip çalıştırın](https://aka.ms/on-premises-data-gateway-installer).

1. En düşük gereksinimleri gözden geçirin, varsayılan yükleme yolunu koruyun, kullanım koşullarını kabul edin ve ardından **yükleme**' yi seçin.

   ![Gereksinimleri gözden geçirin ve kullanım koşullarını kabul edin](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Ağ Geçidi başarıyla yüklendikten sonra, Azure hesabınızın e-posta adresini girin ve **oturum aç**' ı seçin, örneğin:

   ![İş veya okul hesabıyla oturum açın](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Ağ Geçidi yüklemeniz yalnızca bir Azure hesabına bağlanabilir.

1. Sonra **Bu bilgisayarda yeni bir ağ geçidi Kaydet '** i seçin  >  . Bu adım ağ geçidi yükleme cihazınızı [ağ geçidi bulut hizmetine](#gateway-cloud-service)kaydeder.

   ![Ağ geçidini yerel bilgisayara kaydet](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Ağ Geçidi yüklemeniz için şu bilgileri sağlayın:

   * Azure AD kiracınız genelinde benzersiz olan bir ağ geçidi adı
   * Kullanmak istediğiniz en az sekiz karakter olması gereken kurtarma anahtarı
   * Kurtarma anahtarınız için onay

   ![Ağ geçidi yüklemesi için bilgi sağlama](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Kurtarma Anahtarınızı güvenli bir yerde kaydedin ve saklayın. Konumu değiştirmek, taşımak, kurtarmak veya bir ağ geçidi yüklemesini almak istiyorsanız bu anahtara ihtiyacınız vardır.

   [Yüksek kullanılabilirlik senaryoları](#high-availability)için ek ağ geçitleri yüklerken seçtiğiniz **mevcut bir ağ geçidi kümesine ekleme** seçeneğini göz önünde bulabilirsiniz.

1. Ağ Geçidi Bulut hizmeti ve ağ geçidi yüklemeniz tarafından kullanılan [mesajlaşma örneği Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) için bölgeyi denetleyin. Bu bölge, varsayılan olarak Azure hesabınız için Azure AD kiracısı ile aynı konumdadır.

   ![Ağ geçidi hizmeti ve hizmet veri yolu için bölgeyi Onayla](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Varsayılan bölgeyi kabul etmek için **Yapılandır**' ı seçin. Bununla birlikte, varsayılan bölge size en yakın olan bölge değilse, bölgeyi değiştirebilirsiniz.

   *Ağ Geçidi yüklemenizin bölgesi neden değiştirilsin?*

   Örneğin, gecikme süresini azaltmak için ağ geçidinizin bölgenizi mantıksal uygulamanızla aynı bölgeye göre değiştirebilirsiniz. Ya da şirket içi veri kaynağınıza en yakın bölgeyi seçebilirsiniz. *Azure 'daki ağ geçidi kaynağınızın* ve mantıksal uygulamanızın farklı konumları olabilir.

   1. Geçerli bölgenin yanındaki **bölgeyi değiştir**' i seçin.

      ![Geçerli ağ geçidi bölgesini değiştirme](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Sonraki sayfada **Bölge seç** listesini açın, istediğiniz bölgeyi seçin ve **bitti**' yi seçin.

      ![Ağ geçidi hizmeti için başka bir bölge seçin](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Son onay penceresindeki bilgileri gözden geçirin. Bu örnek, Logic Apps, Power BI, Power Apps ve güç otomatikleştirme için aynı hesabı kullanır, bu nedenle ağ geçidi tüm bu hizmetler için kullanılabilir. Hazırsanız, **Kapat**' ı seçin.

   ![Veri ağ geçidi bilgilerini onaylama](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Şimdi [ağ geçidi yüklemeniz Için Azure kaynağını oluşturun](../logic-apps/logic-apps-gateway-connection.md).

<a name="communication-settings"></a>

## <a name="check-or-adjust-communication-settings"></a>İletişim ayarlarını denetle veya ayarla

Şirket içi veri ağ geçidi, bulut bağlantısı için [Azure Service Bus mesajlaşma](../service-bus-messaging/service-bus-messaging-overview.md) 'ya bağlıdır ve ağ geçidinin ilişkili Azure bölgesine karşılık gelen giden bağlantıları kurar. İş ortamınız internet 'e erişmek için bir ara sunucu veya güvenlik duvarından geçtiğinde, bu kısıtlama şirket içi veri ağ geçidinin ağ geçidi bulut hizmetine bağlanmasını ve mesajlaşma Azure Service Bus engelleyebilir. Ağ geçidinde, ayarlayabileceğiniz çeşitli iletişim ayarları vardır.

Örnek senaryo, Azure 'da şirket içi veri ağ geçidi kaynağını kullanarak şirket içi kaynaklara erişen özel bağlayıcılar kullandığınız yerdir. Ayrıca belirli IP adresleriyle trafiği sınırlayan bir güvenlik duvarınız varsa, ilgili *yönetilen bağlayıcılar [giden IP adreslerine](logic-apps-limits-and-config.md#outbound)* erişime izin vermek için ağ geçidi yüklemesini ayarlamanız gerekir. Aynı bölgedeki *Tüm* mantıksal uygulamalar aynı IP adresi aralıklarını kullanır.

Daha fazla bilgi için şu konulara bakın:

* [Şirket içi veri ağ geçidi için iletişim ayarlarını yapılandırma](/data-integration/gateway/service-gateway-communication)
* [Şirket içi veri ağ geçidi için ara sunucu ayarlarını yapılandırma](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Yüksek kullanılabilirlik desteği

Şirket içi veri erişimi için tek hata noktalarından kaçınmak için, farklı bir bilgisayarda birden çok ağ geçidi yüklemesi (yalnızca standart mod) olabilir ve bunları bir küme veya grup olarak ayarlayabilirsiniz. Bu şekilde, birincil ağ geçidi kullanılamıyorsa, veri istekleri ikinci ağ geçidine yönlendirilir ve bu şekilde devam eder. Bir bilgisayara yalnızca bir standart ağ geçidi yükleyebildiğinden, kümedeki her ek ağ geçidini farklı bir bilgisayara yüklemelisiniz. Şirket içi veri ağ geçidiyle çalışan tüm bağlayıcılar yüksek kullanılabilirliği destekler.

* Birincil ağ geçidi ile aynı Azure hesabına sahip en az bir ağ geçidi yüklemeniz ve ilgili yükleme için kurtarma anahtarı olmalıdır.

* Birincil ağ geçidinizin, Kasım 2017 veya sonraki bir sürümünün ağ geçidi güncelleştirmesini çalıştırmalıdır.

Birincil ağ geçidinizi ayarladıktan sonra, başka bir ağ geçidi yüklemeye gittiğinizde, **mevcut bir ağ geçidi kümesine ekle**' yi seçin, yüklediğiniz ilk ağ geçidi olan birincil ağ geçidini seçin ve bu ağ geçidi için kurtarma anahtarını sağlayın. Daha fazla bilgi için bkz. Şirket [içi veri ağ geçidi Için yüksek kullanılabilirlik kümeleri](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Konum değiştirme, geçirme, geri yükleme veya mevcut ağ geçidini alma

Ağ geçidinizin konumunu değiştirmeniz gerekiyorsa, ağ geçidi yüklemenizi yeni bir bilgisayara taşıyın, hasarlı bir ağ geçidini kurtarmanız veya mevcut bir ağ geçidi için sahiplik almanız gerekiyorsa, ağ geçidi yüklemesi sırasında sağlanmış olan kurtarma anahtarına ihtiyacınız vardır.

> [!NOTE]
> Ağ geçidini özgün ağ geçidi yüklemesi olan bilgisayara geri yüklemeden önce, önce o bilgisayardaki ağ geçidini kaldırmanız gerekir. Bu eylem, özgün ağ geçidinin bağlantısını keser.
> Herhangi bir bulut hizmeti için bir ağ geçidi kümesini kaldırır veya silerseniz, bu kümeyi geri alamazsınız.

1. Ağ Geçidi yükleyicisini mevcut ağ geçidine sahip olan bilgisayarda çalıştırın.

1. Yükleyici açıldıktan sonra, ağ geçidini yüklemek için kullanılan Azure hesabıyla oturum açın.

1. **Var olan bir ağ geçidini geçir, geri yükle veya**  >  **İleri**' yi seçin, örneğin:

   !["Var olan bir ağ geçidini geçir, geri yükle veya getir" i seçin](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Kullanılabilir kümeler ve ağ geçitleri arasından seçim yapın ve seçilen ağ geçidi için kurtarma anahtarını girin, örneğin:

   ![Ağ geçidi seçin ve kurtarma anahtarı sağlayın](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Bölgeyi değiştirmek için **bölgeyi değiştir**' i seçin ve yeni bölgeyi seçin.

1. Hazırsanız, görevinizi tamamlayabilmeniz için **Yapılandır** ' ı seçin.

## <a name="tenant-level-administration"></a>Kiracı düzeyinde yönetim

Bir Azure AD kiracısındaki tüm şirket içi veri ağ geçitlerine ilişkin görünürlük almak için, söz konusu Kiracıdaki Genel Yöneticiler, [Power Platform Yönetim merkezinde](https://powerplatform.microsoft.com) kiracı yöneticisi olarak oturum açabilir ve **veri ağ geçitleri** seçeneğini seçebilir. Daha fazla bilgi için bkz. Şirket [içi veri ağ geçidi Için kiracı düzeyinde yönetim](/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Ağ geçidini yeniden başlatma

Varsayılan olarak, yerel bilgisayarınızdaki ağ geçidi yüklemesi "Şirket içi veri ağ geçidi hizmeti" adlı bir Windows hizmet hesabı olarak çalışır. Ancak ağ geçidi yüklemesi, `NT SERVICE\PBIEgwService` "oturum aç" hesabı kimlik bilgileri için adı kullanır ve "hizmet olarak oturum aç" izinlerine sahiptir.

> [!NOTE]
> Windows hizmet hesabınız, şirket içi veri kaynaklarına bağlanmak için kullanılan hesaptan ve bulut hizmetlerinde oturum açarken kullandığınız Azure hesabından farklılık gösterir.

Diğer herhangi bir Windows hizmeti gibi, ağ geçidini çeşitli yollarla başlatabilir ve durdurabilirsiniz. Daha fazla bilgi için bkz. Şirket [içi veri ağ geçidini yeniden başlatma](/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Ağ geçidi nasıl çalışır?

Kuruluşunuzdaki kullanıcılar, erişim izni olan şirket içi verilere erişebilir. Ancak, bu kullanıcıların şirket içi veri kaynağınıza bağlanabilmesi için bir şirket içi veri ağ geçidini yüklemeniz ve ayarlamanız gerekir. Genellikle yönetici, bir ağ geçidini yükleyen ve ayarlayan kişidir. Bu eylemler, Sunucu Yöneticisi izinleri veya şirket içi sunucularınız hakkında özel bilgi gerektirebilir.

Ağ Geçidi, arka planda daha hızlı ve daha güvenli bir iletişim sağlanmasına yardımcı olur. Bu iletişim, buluttaki bir Kullanıcı, ağ geçidi bulut hizmeti ve şirket içi veri kaynağınız arasında akar. Ağ Geçidi bulutu hizmeti, veri kaynağı kimlik bilgilerinizi ve ağ geçidi ayrıntılarını şifreler ve depolar. Hizmet Ayrıca sorguları ve sonuçlarını Kullanıcı, ağ geçidi ve şirket içi veri kaynağınız arasında yönlendirir.

Ağ Geçidi, güvenlik duvarları ile birlikte çalışarak yalnızca giden bağlantıları kullanır. Tüm trafik ağ geçidi aracısından güvenli giden trafik olarak gelir. Ağ Geçidi, verileri [Azure Service Bus mesajlaşma](../service-bus-messaging/service-bus-messaging-overview.md)aracılığıyla şifrelenmiş kanallardaki şirket içi kaynaklardan gönderir. Bu hizmet veri yolu, ağ geçidi ile çağıran hizmet arasında bir kanal oluşturur, ancak herhangi bir veri depolamaz. Ağ Geçidi üzerinden taşınan tüm veriler şifrelenir.

![Şirket içi veri ağ geçidi mimarisi](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Bulut hizmetine bağlı olarak, ağ geçidi için bir veri kaynağı ayarlamanız gerekebilir.

Bu adımlarda, şirket içi veri kaynağına bağlı bir öğeyle etkileşim kurarken ne olacağı açıklanır:

1. Bulut hizmeti, veri kaynağı için şifrelenmiş kimlik bilgileriyle birlikte bir sorgu oluşturur. Hizmet daha sonra işlem için ağ geçidi kuyruğuna sorgu ve kimlik bilgilerini gönderir.

1. Ağ Geçidi Bulut hizmeti sorguyu analiz eder ve isteği Azure Service Bus mesajlaşma 'ya gönderir.

1. Azure Service Bus mesajlaşma, bekleyen istekleri ağ geçidine gönderir.

1. Ağ geçidi sorguyu alır, kimlik bilgilerinin şifresini çözer ve bu kimlik bilgileriyle bir veya daha fazla veri kaynağına bağlanır.

1. Ağ Geçidi, çalıştırmak için sorguyu veri kaynağına gönderir.

1. Sonuçlar veri kaynağından ağ geçidine ve sonra ağ geçidi bulut hizmetine gönderilir. Daha sonra ağ geçidi bulutu hizmeti sonuçları kullanır.

### <a name="authentication-to-on-premises-data-sources"></a>Şirket içi veri kaynaklarına yönelik kimlik doğrulaması

Ağ geçidinden şirket içi veri kaynaklarına bağlanmak için depolanan bir kimlik bilgisi kullanılır. Kullanıcıdan bağımsız olarak ağ geçidi, bağlanmak için depolanan kimlik bilgilerini kullanır. Power BI Analysis Services için DirectQuery ve LiveConnect gibi belirli hizmetler için kimlik doğrulama özel durumları olabilir.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft bulut Hizmetleri, kullanıcıların kimliğini doğrulamak için [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) 'yi kullanır. Bir Azure AD kiracısı, Kullanıcı adları ve güvenlik grupları içerir. Genellikle, oturum açma için kullandığınız e-posta adresi, hesabınız için Kullanıcı asıl adı (UPN) ile aynıdır.

### <a name="what-is-my-upn"></a>UPN nedir?

Bir etki alanı yöneticisi değilseniz, UPN 'nizi bilmiyor olabilirsiniz. Hesabınızın UPN 'sini bulmak için, `whoami /upn` iş istasyonunuzdan komutunu çalıştırın. Sonuç bir e-posta adresi gibi görünse de sonuç, yerel etki alanı hesabınızın UPN 'si olur.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Bir şirket içi Active Directory hesabını Azure AD ile eşitleme

Şirket içi Active Directory hesaplarınız ve Azure AD hesaplarınız için UPN aynı olmalıdır. Bu nedenle, her şirket içi Active Directory hesabının Azure AD hesabınızla eşleştiğinden emin olun. Bulut hizmetleri yalnızca Azure AD içindeki hesaplar hakkında bilgi sahibi. Bu nedenle, şirket içi Active Directory hesap eklemeniz gerekmez. Hesap Azure AD 'de yoksa, bu hesabı kullanamazsınız.

Azure AD ile şirket içi Active Directory hesaplarınızı eşleşmenizin yolları aşağıda verilmiştir.

* Hesapları Azure AD 'ye el ile ekleyin.

  Azure portal veya Microsoft 365 Yönetim merkezinde bir hesap oluşturun. Hesap adının şirket içi Active Directory hesap için UPN ile eşleştiğinden emin olun.

* Azure Active Directory Connect aracını kullanarak yerel hesapları Azure AD kiracınızla eşitler.

  Azure AD Connect Aracı, Dizin eşitleme ve kimlik doğrulama kurulumu için seçenekler sağlar. Bu seçenekler arasında Parola karması eşitleme, geçişli kimlik doğrulama ve Federasyon bulunur. Kiracı Yöneticisi veya yerel etki alanı yöneticisi değilseniz, Azure AD Connect kurmak için BT yöneticinize başvurun. Azure AD Connect, Azure AD UPN 'nizin yerel Active Directory UPN 'nize eşleşmesini sağlar. Bu eşleştirme, Power BI veya çoklu oturum açma (SSO) özellikleri ile Analysis Services canlı bağlantılar kullanıyor olmanıza yardımcı olur.

  > [!NOTE]
  > Azure AD Connect aracı ile hesapların eşitlenmesi, Azure AD kiracınızda yeni hesaplar oluşturur.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>SSS ve sorun giderme

* [Şirket içi veri ağ geçidi hakkında SSS](/data-integration/gateway/service-gateway-onprem-faq)
* [Şirket içi veri ağ geçidi sorunlarını giderme](/data-integration/gateway/service-gateway-tshoot)
* [Ağ geçidi performansını izleme ve en iyi duruma getirme](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps 'ten şirket içi verilere bağlanma](../logic-apps/logic-apps-gateway-connection.md)
* [Kurumsal tümleştirme özellikleri](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps için Bağlayıcılar](../connectors/apis-list.md)
