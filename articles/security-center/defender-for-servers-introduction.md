---
title: Sunucular için Azure Defender-avantajlar ve Özellikler
description: Sunucular için Azure Defender 'ın avantajları ve özellikleri hakkında bilgi edinin.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 36c32c4eefdaa1c7604f2b0f19e98cf6a6d4310d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096539"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Sunucular için Azure Defender 'a giriş

Sunucular için Azure Defender, Windows ve Linux makineleriniz için tehdit algılama ve gelişmiş savunmaları ekler.

Windows için Azure Defender, Windows tabanlı makinelerinizi izlemek ve korumak için Azure hizmetleri ile tümleşir. Güvenlik Merkezi, tüm bu hizmetlerden gelen uyarıları ve düzeltme önerilerini kullanımı kolay bir biçimde sunar.

Linux için Azure Defender, en yaygın Linux denetim çerçevelerinden biri olan **auditd**'Yi kullanarak Linux makinelerden gelen denetim kayıtlarını toplar. auditd, ana hat çekirdekte yer yaşar. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Sunucular için Azure Defender 'ın avantajları nelerdir?

Sunucular için Azure Defender ile birlikte sunulan tehdit algılama ve koruma özellikleri şunlardır:

- **Uç nokta Için Microsoft Defender Için tümleşik lisans (yalnızca Windows)** -sunucular Için Azure Defender,  [uç nokta için Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender)içerir. Birlikte, kapsamlı uç nokta algılama ve yanıt (EDR) özellikleri sağlar. Daha fazla bilgi için bkz. [uç noktalarınızı koruma](security-center-wdatp.md).

    Endpoint için Defender bir tehdit algıladığında bir uyarı tetikler. Uyarı, güvenlik merkezi 'nde gösterilir. Güvenlik Merkezi 'nden Ayrıca, uç nokta konsolu için Defender ' ı Özet olarak da kullanabilirsiniz ve saldırının kapsamını açığa çıkarmak için ayrıntılı bir araştırma gerçekleştirebilirsiniz. Uç nokta için Microsoft Defender hakkında daha fazla bilgi edinin.

    > [!IMPORTANT]
    > **Endpoint algılayıcı Için Microsoft Defender** , güvenlik merkezi 'Ni kullanan Windows sunucularında otomatik olarak etkinleştirilir.

- **VM 'ler Için güvenlik açığı değerlendirmesi taraması** -Azure Güvenlik Merkezi 'ne dahil edilen güvenlik açığı tarayıcısı Qualys tarafından desteklenmektedir. 

    Qualys ' tarayıcısı, Azure ve karma sanal makinelerinizdeki güvenlik açıklarının gerçek zamanlı olarak tanımlanması için önde gelen araçlardan biridir. Bir Qualys lisansına veya hatta bir Qualys hesabına ihtiyacınız yoktur. her şey güvenlik merkezi 'nin içinde sorunsuz bir şekilde işlenir. Daha fazla bilgi için bkz. Azure [Defender 'ın tümleşik güvenlik açığı değerlendirme çözümü, Azure ve karma makineler](deploy-vulnerability-assessment-vm.md).

- **Tam zamanında (JIT) sanal makine (VM) erişimi** -tehdit AKTÖRLERI, RDP veya SSH gibi açık yönetim bağlantı noktalarına sahip erişilebilir makineleri etkin bir şekilde araytırabilirler. Tüm sanal makineleriniz bir saldırı için olası hedeflerdir. Bir VM başarıyla tehlikede olduğunda, ortamınızda daha fazla kaynağa saldırmak için giriş noktası olarak kullanılır.

    Sunucular için Azure Defender 'ı etkinleştirdiğinizde, sanal makinelerinize gelen trafiği kilitlemek için tam zamanında VM erişimi 'ni kullanabilir ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalmayı azaltabilirsiniz. Daha fazla bilgi için bkz. [JıT VM erişimini anlama](just-in-time-explained.md).

- **Dosya bütünlüğü izleme (FIM)** -değişiklik izleme olarak da bilinen dosya bütünlüğü Izleme (FIM), işletim sisteminin dosyalarını ve kayıt defterlerini, uygulama yazılımını ve diğer değişiklikleri bir saldırının göstergesi olabilecek değişiklikler açısından inceler. Bir karşılaştırma yöntemi, dosyanın geçerli durumunun dosyanın son taramasından farklı olup olmadığını belirlemede kullanılır. Dosyalarınızda geçerli veya şüpheli değişiklikler yapıldığını öğrenmek için bu karşılaştırmayı kullanabilirsiniz.

    Sunucular için Azure Defender 'ı etkinleştirdiğinizde, Windows dosyalarının, Windows kayıt defterlerinin ve Linux dosyalarınızın bütünlüğünü doğrulamak için FIM 'yi kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde dosya bütünlüğü izleme](security-center-file-integrity-monitoring.md).

- **Uyarlamalı uygulama denetimleri (AAC)** -Uyarlamalı uygulama denetimleri, makineleriniz için bilinen güvenli uygulamalar listesine izin vermeyi tanımlamaya yönelik akıllı ve otomatikleştirilmiş bir çözümdür.

    Uyarlamalı uygulama denetimlerini etkinleştirdikten ve yapılandırdıktan sonra, güvenli olarak tanımladıklarından başka herhangi bir uygulama çalıştırıyorsa güvenlik uyarıları alırsınız. Daha fazla bilgi için bkz. [makinelerinizin saldırı yüzeylerini azaltmak için uyarlamalı uygulama denetimleri kullanma](security-center-adaptive-application.md).

- **Uyarlamalı ağ sağlamlaştırma (ANH)** -kaynaklara ve kaynaklardan gelen trafiği filtrelemek için ağ güvenlik grupları (NSG) uygulanıyor, ağ güvenliği durgunuzu geliştirir. Ancak, NSG üzerinden akan gerçek trafiğin, tanımlanan NSG kurallarının bir alt kümesi olduğu bazı durumlar da olabilir. Bu durumlarda, güvenlik duruşunu artırmak, gerçek trafik desenlerine bağlı olarak NSG kurallarını sağlamlaştırma yoluyla elde edilebilir.

    Uyarlamalı ağ sağlamlaştırma, NSG kurallarına daha fazla uyum sağlamak için öneriler sağlar. Gerçek trafik, bilinen güvenilen yapılandırma, tehdit bilgileri ve diğer güvenlik açığı göstergelerini gösteren bir makine öğrenimi algoritması kullanır ve yalnızca belirli IP/bağlantı noktası tanımlama bilgilerine giden trafiğe izin vermek için öneriler sağlar. Daha fazla bilgi için bkz. [Uyarlamalı ağ sağlamlaştırma ile ağ güvenlik durunuzu geliştirme](security-center-adaptive-network-hardening.md).

- **Docker ana bilgisayar sağlamlaştırma** -Azure Güvenlik Merkezi, IaaS Linux VM 'lerinde barındırılan yönetilmeyen kapsayıcıları veya Docker Kapsayıcıları çalıştıran diğer Linux makinelerini belirler. Güvenlik Merkezi, bu kapsayıcıların yapılandırmalarının sürekli değerlendirir. Daha sonra bunları Internet güvenliği (CIS) Docker kıyaslaması için merkezi ile karşılaştırır. Güvenlik Merkezi, CIS Docker kıyaslaması 'nın kural kümesinin tamamını içerir ve kapsayıcılarınız denetimlerden herhangi birini karşılamadığı takdirde sizi uyarır. Daha fazla bilgi için bkz. [Docker konaklarınızı sağlamlaştırın](harden-docker-hosts.md).

- **Dosya daha az saldırı algılama (yalnızca Windows)** -Fileless saldırıları, disk tabanlı tarama teknikleri için algılamayı önlemek üzere kötü amaçlı yükleri belleğe ekler. Saldırganın yükü, daha fazla riskli işlem belleği içinde devam ettirir ve çok çeşitli kötü amaçlı etkinlikler gerçekleştirir.

  Dosya daha az saldırı algılaması sayesinde, otomatik bellek oluşturma teknikleri, dosya daha az saldırı araç takımları, teknikler ve davranışları belirler. Bu çözüm, çalışma zamanında makinenizi düzenli olarak tarar ve öngörüleri doğrudan işlem belleğinden ayıklar. Belirli Öngörüler şunları içerir: 

  - İyi bilinen araç takımları ve şifre araştırma yazılımı 

  - Genellikle bir yazılım güvenlik açığından yararlanma aşamasında yük olarak kullanılan küçük bir kod parçası olan shellcode.

  - İşlem belleğinde kötü amaçlı yürütülebilir dosya eklendi

  Dosya daha az saldırı algılama, ağ etkinliği gibi ek işlem meta verileri ile açıklamaları içeren ayrıntılı güvenlik uyarıları oluşturur. Bu, uyarı önceliklendirme, bağıntı ve aşağı akış yanıt süresini hızlandırır. Bu yaklaşım, olay tabanlı EDR çözümlerini tamamlar ve daha fazla algılama kapsamı sağlar.

  Dosya daha az saldırı algılama uyarılarına ilişkin ayrıntılar için, [Uyarı tablosuna](alerts-reference.md#alerts-windows)bakın.

- **Linux auditd uyarıları ve Log Analytics aracı tümleştirmesi (yalnızca Linux)** -auditd sistemi, sistem çağrılarını izlemekten sorumlu olan çekirdek düzeyindeki bir alt sistemden oluşur. Bu, belirli bir kural kümesi tarafından filtreleyerek filtre uygular ve iletileri bir yuvaya yazar. Güvenlik Merkezi, Log Analytics aracısında sestd paketinden işlevleri tümleştirir. Bu tümleştirme, tüm desteklenen Linux dağıtımlarına hiçbir önkoşul olmadan sestd olaylarının toplanmasını mümkün bir şekilde sunar.

    auditd kayıtları, Linux Aracısı için Log Analytics Aracısı kullanılarak toplanır, zenginleştirir ve olaylara toplanır. Güvenlik Merkezi, bulut ve şirket içi Linux makinelerinde kötü amaçlı davranışları algılamak için Linux sinyalleri kullanan yeni çözümlemeler ekler. Windows özelliklerine benzer şekilde, bu analizler şüpheli işlemlere, dubmerak eden oturum açma girişimlerini, çekirdek modülünü yüklemeyi ve diğer etkinlikleri kapsar. Bu etkinlikler, bir makinenin saldırıya açık ya da ihlal edilmiş olduğunu gösterebilir.  

    Linux uyarılarının bir listesi için, [Uyarı tablosuna](alerts-reference.md#alerts-linux)bakın.


## <a name="simulating-alerts"></a>Uyarıların benzetimini yapma

Aşağıdaki oyun defterlerinden birini indirerek uyarıların benzetimini yapabilirsiniz:

- Windows için: [Azure Güvenlik Merkezi PlayBook: güvenlik uyarıları](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Linux için: [Azure Güvenlik Merkezi PlayBook: Linux algılamaları](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, sunucular için Azure Defender hakkında bilgi edindiniz. 

İlgili malzemeler için aşağıdaki makalelere bakın: 

- Bir uyarının Güvenlik Merkezi tarafından oluşturulup oluşturulmadığı veya Güvenlik Merkezi tarafından farklı bir güvenlik ürününden alınıp alınmayacağı, dışarı aktarabilirsiniz. Uyarılarınızı Azure Sentinel 'e, herhangi bir üçüncü taraf SıEM 'e veya herhangi bir harici araca aktarmak için [uyarıları BIR SıEM 'ye aktarma](continuous-export.md)konusundaki yönergeleri izleyin.

- > [!div class="nextstepaction"]
    > [Azure Defender’ı etkinleştirme](enable-azure-defender.md)