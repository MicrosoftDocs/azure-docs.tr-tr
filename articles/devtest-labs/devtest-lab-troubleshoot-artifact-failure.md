---
title: Azure DevTest Labs bir sanal makinede yapıt başarısızlıklarını tanılama
description: DevTest Labs, bir yapıt hatasını tanılamak için kullanabileceğiniz bilgiler sağlar. Bu makalede, yapıt hatalarının nasıl giderileceği gösterilmektedir.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480822"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Laboratuvardaki yapıt başarısızlıklarını tanılama 
Bir yapıt oluşturduktan sonra, başarılı veya başarısız olup olmadığını kontrol edebilirsiniz. Azure DevTest Labs yapıt kayıtları, bir yapıt hatasını tanılamak için kullanabileceğiniz bilgiler sağlar. Bir Windows VM için yapıt günlük bilgilerini görüntülemek için kullanabileceğiniz birkaç seçenek vardır:

* Azure portalında
* VM 'de

> [!NOTE]
> Hataların doğru şekilde tanımlanmasını ve açıklandığından emin olmak için yapıtın uygun yapıya sahip olması önemlidir. Yapının doğru şekilde oluşturulması hakkında daha fazla bilgi için bkz. [özel yapılar oluşturma](devtest-lab-artifact-author.md). Doğru yapılandırılmış yapıtın bir örneğini görmek için [test parametresi türleri](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) yapıtı ' ne bakın.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Azure portal kullanarak yapıt hatalarında sorun giderme

1. Azure portal, kaynak listenizde laboratuvarınızı seçin.
2. Araştırmak istediğiniz yapıtı içeren Windows sanal makinesini seçin.
3. Sol bölmede, **genel** altında **yapıtlar**' ı seçin. Bu VM ile ilişkili yapıların listesi görüntülenir. Yapıt adı ve yapıt durumu belirtilir.

   ![Yapıt durumu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. **Başarısız** durumu gösteren bir yapıt seçin. Yapıt açılır. Yapıtın başarısızlığı hakkındaki ayrıntıları içeren bir uzantı iletisi görüntülenir.

   ![Yapıt hata iletisi](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Sanal makinenin içinden yapıt hatalarında sorun giderme

1. Tanılama yapmak istediğiniz yapıtı içeren VM 'de oturum açın.
2. C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension \\ *1,9*\ durumuna gidin; burada *1,9* , Azure Özel Betik uzantısı sürüm numarasıdır.

   ![Durum dosyası](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. **Durum** dosyasını açın.

Bir **Linux** sanal makinesinde günlük dosyalarını bulmaya ilişkin yönergeler için aşağıdaki makaleye bakın: [Linux sanal makinelerle Azure Özel Betik uzantısı sürüm 2](../virtual-machines/extensions/custom-script-linux.md#troubleshooting) ' yi kullanma


## <a name="related-blog-posts"></a>İlgili blog gönderileri
* [DevTest Labs 'de bir Kaynak Yöneticisi şablonu kullanarak bir VM 'yi mevcut bir Active Directory etki alanına katma](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Sonraki adımlar
* [Laboratuvara Git deposunu nasıl ekleyeceğinizi](devtest-lab-add-artifact-repo.md)öğrenin.

