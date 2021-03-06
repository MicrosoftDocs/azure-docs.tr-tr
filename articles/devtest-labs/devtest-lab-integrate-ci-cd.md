---
title: Azure DevTest Labs Azure Pipelines tümleştirin
description: Azure DevTest Labs Azure Pipelines sürekli tümleştirme ve teslim ardışık düzenine nasıl tümleştirileceğini öğrenin
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 96f99d41d0a7ea07bf3854292f9c3bd6245414b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288935"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure DevTest Labs Azure Pipelines CI/CD işlem hattınızla tümleştirin

Azure Pipelines sürekli tümleştirme ve sürekli teslim (CI/CD) derleme ve yayın işlem hatlarınızı Azure DevTest Labs tümleştirmek için *Azure DevTest Labs görevleri* uzantısını kullanabilirsiniz. Uzantı aşağıdakiler de dahil olmak üzere çeşitli görevleri yüklüyor: 

- Sanal makine (VM) oluşturma
- VM’den özel görüntü oluşturma
- VM silme 

Bu görevler, örneğin belirli bir test görevi için bir *altın görüntü* VM 'sini hızlı bir şekilde dağıtmayı ve test tamamlandığında VM 'yi silmeyi kolaylaştırır.

Bu makalede, bir VM oluşturup dağıtmak, özel bir görüntü oluşturmak ve ardından VM 'yi tek bir yayın işlem hattı olarak silmek için Azure DevTest Labs görevlerinin nasıl kullanılacağı gösterilmektedir. Görevleri genellikle kendi özel derlemeniz, test etmeniz ve dağıtım hatlarında tek tek gerçekleştirirsiniz.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Önkoşullar

- [Azure DevOps](https://dev.azure.com) kuruluşunuza kaydolun veya oturum açın ve kuruluşta [bir proje oluşturun](/vsts/organizations/projects/create-project) . 
  
- Visual Studio Market Azure DevTest Labs görevleri uzantısını yükler.
  
  1. [Azure DevTest Labs görevler](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)' e gidin.
  1. **Ücretsiz Al** seçeneğini belirleyin.
  1. Açılan listeden Azure DevOps kuruluşunuzu seçin ve ardından **Install**' ı seçin. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Azure VM oluşturmak için şablon oluşturma 

Bu bölümde, isteğe bağlı olarak bir Azure VM oluşturmak için kullandığınız Azure Resource Manager şablonunun nasıl oluşturulacağı açıklanmaktadır.

1. Aboneliğinizde bir Kaynak Yöneticisi şablonu oluşturmak için, [Kaynak Yöneticisi şablonu kullanma](devtest-lab-use-resource-manager-template.md)bölümündeki yordamı izleyin.
   
1. Kaynak Yöneticisi şablonunu oluşturmadan önce, VM oluşturma kapsamında [WinRM yapıtı](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) ekleyin. *Hedef makinelerde* *Azure dosya kopyalama* ve PowerShell gibi dağıtım görevlerinin WinRM erişimi olmalıdır. WinRM yapıtı, VM 'nin tam etki alanı adı (FQDN) olması gereken bir parametre olarak ana bilgisayar adı gerektirir. 
   
   > [!NOTE]
   > WinRM 'yi paylaşılan bir IP adresi ile birlikte kullandığınızda, bir dış bağlantı noktasını WinRM bağlantı noktasına eşlemek için bir NAT kuralı eklemeniz gerekir. VM 'yi genel bir IP adresi ile oluşturursanız, NAT kuralına gerek kalmaz.
   
   
1. Şablonu bilgisayarınıza *CreateVMTemplate.js* adlı bir dosya olarak kaydedin.
   
1. Şablonu kaynak denetim sisteminize iade edin.

## <a name="create-a-script-to-get-vm-properties"></a>VM özelliklerini almak için betik oluşturma

Sürüm ardışık düzeninde *hedef makinelerde* *Azure dosya kopyası* veya PowerShell gibi görev adımlarını çalıştırdığınızda aşağıdaki betik, bir uygulamayı bir VM 'ye dağıtmanız için gereken değerleri toplar. Bu görevleri genellikle uygulamanızı bir Azure VM 'ye dağıtmak için kullanacaksınız. Görevler, VM kaynak grubu adı, IP adresi ve FQDN gibi değerler gerektirir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Betik dosyasını oluşturmak için:

1. Bir metin düzenleyici açın ve aşağıdaki betiği buna yapıştırın.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Dosyayı *GetLabVMParams.ps1* gibi bir adla kaydedin ve kaynak denetim sisteminize iade edin. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Azure Pipelines’da yayın işlem hattı oluşturma

Yeni bir yayın işlem hattı oluşturmak için:

1. Azure DevOps proje sayfasından sol gezinmede işlem **hattı**  >  **yayınları** ' nı seçin.
1. **Yeni İşlem Hattı**’nı seçin.
1. **Şablon seçin** altında aşağı kaydırın ve **boş iş**' ı seçin ve ardından **Uygula**' yı seçin.

### <a name="add-and-set-variables"></a>Değişkenleri ekleme ve ayarlama

İşlem hattı görevleri, Azure portal Kaynak Yöneticisi şablonu oluşturduğunuzda VM 'ye atadığınız değerleri kullanır. 

Değerler için değişken eklemek için: 

1. İşlem hattı sayfasında, **değişkenler** sekmesini seçin.
   
1. Her değişken için, **Ekle** ' yi seçin ve adı ve değeri girin:
   
   |Name|Değer|
   |---|---|
   |*vmName*|Kaynak Yöneticisi şablonunda atadığınız VM adı|
   |*Nitelen*|VM 'ye erişim için Kullanıcı adı|
   |*parola*|Kullanıcı adı için parola. Parolayı gizlemek ve korumak için kilit simgesini seçin.

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs VM oluşturma

Sonraki adımda, gelecekteki dağıtımlar için kullanılacak altın görüntü sanal makinesi oluşturulur. VM 'yi, *Azure DEVTEST Labs VM oluştur* görevi kullanarak Azure DevTest Labs örneğiniz içinde oluşturursunuz.

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **+** **Aracı işi**' nin yanındaki artı işaretini seçin. 
   
1. **Görev Ekle** altında **VM oluştur Azure DevTest Labs** seçin ve **Ekle**' yi seçin. 
   
1. Sol bölmede **Azure DEVTEST Labs VM oluştur** ' u seçin. 

1. Sağ bölmede, formu aşağıda gösterildiği gibi doldurun:
   
   |Alan|Değer|
   |---|---|
   |**Azure RM aboneliği**|**Kullanılabilir Azure hizmeti bağlantılarından** veya açılan menüdeki **kullanılabilir Azure aboneliklerinden** bir hizmet bağlantısı veya abonelik seçin ve gerekirse **Yetkilendir** ' i seçin.<br /><br />**Note:** Azure aboneliğinize daha kısıtlı bir izin bağlantısı oluşturma hakkında bilgi için bkz. [Azure Resource Manager hizmet uç noktası](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Laboratuvar adı**|Laboratuvar VM 'sinin oluşturulacağı mevcut bir laboratuvarın adını seçin.|
   |**Şablon adı**|Kaynak kodu deponuza kaydettiğiniz şablon dosyasının tam yolunu ve adını girin. Yolu basitleştirmek için yerleşik özellikleri kullanabilirsiniz, örneğin:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Şablon parametreleri**|Daha önce tanımladığınız değişkenlerin parametrelerini girin:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Çıkış değişkenleri**  >  **Laboratuvar VM kimliği**|Oluşturulan laboratuvar VM KIMLIĞI için değişkeni girin. Varsayılan **Labvmıd** kullanırsanız, sonraki görevlerdeki değişkenine *$ (labvmıd)* olarak başvurabilirsiniz.<br /><br />Varsayılan dışında bir ad oluşturabilirsiniz, ancak sonraki görevlerde doğru adı kullanmayı unutmayın. Laboratuvar VM KIMLIĞINI aşağıdaki biçimde yazabilirsiniz:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>DevTest Labs VM 'sinin ayrıntılarını toplayın

Daha önce oluşturduğunuz betiği, DevTest Labs VM 'nin ayrıntılarını toplamak için yürütün. 

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **+** **Aracı işi**' nin yanındaki artı işaretini seçin. 
   
1. **Görev Ekle**' nin altında **Azure PowerShell**' ı seçin ve **Ekle**' yi seçin. 
   
1. Sol bölmedeki **Azure PowerShell betiği: filepath** öğesini seçin. 
   
1. Sağ bölmede, formu aşağıda gösterildiği gibi doldurun:
   
   |Alan|Değer|
   |---|---|
   |**Azure bağlantı türü**|**Azure Resource Manager** seçin.|
   |**Azure Aboneliği**|Hizmet bağlantınızı veya aboneliğinizi seçin.| 
   |**Betik türü**|**Betik dosyası yolunu** seçin.|
   |**Betik yolu**|Kaynak kodu deponuza kaydettiğiniz PowerShell betiğinin tam yolunu ve adını girin. Yolu basitleştirmek için yerleşik özellikleri kullanabilirsiniz, örneğin:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Betik bağımsız değişkenleri**|Önceki görev tarafından doldurulmuş olan *Labvmıd* değişkeninin adını girin, örneğin:<br /><br />`-labVmId '$(labVMId)'`|

Komut dosyası gerekli değerleri toplar ve bunları yayın işlem hattının içindeki ortam değişkenlerine depolar, böylece sonraki adımlarda bunlara kolayca başvurabilirsiniz.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>DevTest Labs VM 'den bir VM görüntüsü oluşturma

Sonraki görev, Azure DevTest Labs Örneğinizde yeni dağıtılan VM 'nin bir görüntüsünü oluşturmaktır. Daha sonra bir geliştirici görevi yürütmek veya bazı testleri çalıştırmak istediğinizde, sanal makinenin bir kopyasını oluşturmak için görüntüsünü kullanabilirsiniz. 

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **+** **Aracı işi**' nin yanındaki artı işaretini seçin. 
   
1. **Görev Ekle**' nin altında **Azure DevTest Labs özel görüntü oluştur**' u seçin ve **Ekle**' yi seçin. 
   
1. Görevi şu şekilde yapılandırın:
   
   |Alan|Değer|
   |---|---|
   |**Azure RM aboneliği**|Hizmet bağlantınızı veya aboneliğinizi seçin.|
   |**Laboratuvar adı**|Görüntünün oluşturulacağı mevcut bir laboratuvarın adını seçin.|
   |**Özel görüntü adı**|Özel görüntü için bir ad girin.|
   |**Açıklama** (isteğe bağlı)|Doğru görüntüyü daha sonra seçmenizi kolaylaştırmak için bir açıklama girin.|
   |**Kaynak laboratuvar VM**  >  'si **Kaynak laboratuvar VM kimliği**|Labvmıd değişkeninin varsayılan adını değiştirdiyseniz buraya girin. Varsayılan değer **$ (Labvmıd)** değeridir.|
   |**Çıkış değişkenleri**  >  **Özel görüntü kimliği**|Gerekirse değişkenin varsayılan adını düzenleyebilirsiniz.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Uygulamanızı DevTest Labs sanal makinesine dağıtma (isteğe bağlı)

Uygulamanızı yeni DevTest Labs sanal makinesine dağıtmak için görev ekleyebilirsiniz. Uygulamayı dağıtmak için genellikle kullandığınız görevler, hedef makinelerde *Azure dosya kopyalama* ve *PowerShell '* tir.

Bu görevlerin parametreleri için ihtiyaç duyduğunuz VM bilgileri, yayın ardışık düzeninde bulunan **Labvmrgname**, **labvmı adresi** ve **labvmfqdn** adlı üç yapılandırma değişkenine depolanır. Yalnızca bir DevTest Labs sanal makinesi ve özel bir görüntü oluşturmak için bir uygulama dağıtmadan denemek istiyorsanız, bu adımı atlayabilirsiniz.

### <a name="delete-the-vm"></a>VM’yi silin

Son görev, Azure DevTest Labs örneğinize dağıttığınız sanal makineyi silmektir. Geliştirme görevlerini yürütmeden veya dağıtılan VM 'de ihtiyaç duyduğunuz testleri çalıştırdıktan sonra normalde VM 'yi silebilirsiniz. 

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **+** **Aracı işi**' nin yanındaki artı işaretini seçin. 
   
1. **Görev Ekle**' nin altında, **VM 'yi Sil Azure DevTest Labs** seçin ve **Ekle**' yi seçin. 
   
1. Görevi şu şekilde yapılandırın:
   
   - **Azure RM aboneliği** altında hizmet bağlantınızı veya aboneliğinizi seçin. 
   - **Laboratuvar VM 'si kimliği** Için, labvmıd değişkeninin varsayılan adını değiştirdiyseniz buraya girin. Varsayılan değer **$ (Labvmıd)** değeridir.
   
### <a name="save-the-release-pipeline"></a>Yayın ardışık düzenini kaydetme

Yeni sürüm ardışık düzenini kaydetmek için:

1. Yayın ardışık düzeni sayfasında **Yeni yayın işlem hattı** adı ' nı seçin ve işlem hattı için yeni bir ad girin. 
   
1. Sağ üst köşedeki **Kaydet** simgesini seçin. 

## <a name="create-and-run-a-release"></a>Yayın oluşturma ve çalıştırma

Yeni işlem hattını kullanarak bir yayın oluşturmak ve çalıştırmak için:

1. Yayın ardışık düzeni sayfasında sağ üstteki **yayın oluştur** ' u seçin. 
   
1. **Yapıtlar** altında, en son yapıyı seçin ve ardından **Oluştur**' u seçin.
   
1. Her sürüm aşamasında, sanal makine oluşturma, görüntü oluşturma ve VM silme işlemini görüntülemek için Azure portal DevTest Labs örneğinizin görünümünü yenileyin.

Özel görüntüyü, ihtiyacınız olduğunda VM 'Ler oluşturmak için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- [Kaynak Yöneticisi şablonlarıyla çoklu VM ortamları oluşturmayı](devtest-lab-create-environment-from-arm.md)öğrenin.
- [Ortak DevTest Labs GitHub deposundan](https://github.com/Azure/azure-quickstart-templates)DevTest Labs otomasyonu için daha fazla hızlı başlangıç Kaynak Yöneticisi şablonları bulun.
- Gerekirse, bkz. [Azure DevOps sorun giderme](/azure/devops/pipelines/troubleshooting) sayfası.
 
