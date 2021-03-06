---
title: Tek başına kümede uygulama yükleme
description: Bu öğreticide, tek başına Service Fabric kümenize bir uygulamanın nasıl yükleneceğini öğreneceksiniz.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae946321b34f12c816a717db4a3d07f57feefe52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96485369"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Öğretici: Service Fabric tek başına kümenize uygulama dağıtma

Service Fabric tek başına kümeleri, kendi ortamınızı seçme ve Service Fabric’in benimsediği "her işletim sistemi, her bulut" yaklaşımının bir parçası olarak bir küme oluşturma seçeneği sunar. Bu öğretici serisinde, AWS üzerinde barındırılan bir tek başına küme oluşturur ve bir uygulamayı buna dağıtırsınız.

Bu öğretici, bir serinin üçüncü bölümüdür.  Tek başına kümeler Service Fabric kendi ortamınızı seçme ve "tüm işletim sistemi, herhangi bir bulut" Service Fabric yaklaşımızın parçası olarak bir küme oluşturma seçeneğini sunar. Bu öğreticide, bu tek başına kümeyi barındırmak için gereken AWS altyapısını oluşturma işlemi gösterilmektedir.

Bu makalede şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Örnek uygulamayı indirme
> * Kümeye dağıtma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* [Visual Studio 2019](https://www.visualstudio.com/) ' i yükleyip **Azure geliştirme** ve **ASP.net ve Web geliştirme** iş yüklerini yüklersiniz.
* [Service Fabric SDK 'sını yükler](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Voting örnek uygulamasını indirme

Komut penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Uygulamayı Service Fabric kümesine dağıtma

Uygulama indirildikten sonra, doğrudan Visual Studio'dan bir kümeye dağıtabilirsiniz.

1. Visual Studio’yu açın

2. **Dosya**  >  **açma** Seç

3. Git deposunu kopyaladığınız klasöre gidin ve Voting.sln dosyasını seçin

4. `Voting`Çözüm Gezgini uygulama projesine sağ tıklayın ve **Yayımla** ' yı seçin.

5. **Bağlantı Uç Noktası** açılır listesini seçin ve kümenizdeki düğümlerden birinin genel DNS Adını girin.  Örneğin, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Azure 'da tam etki alanı adı (FQDN) otomatik olarak verilmez, ancak [VM 'ye Genel Bakış sayfasında kolayca ayarlanabilir.](../virtual-machines/create-fqdn.md)

6. Tercih ettiğiniz tarayıcıyı açın ve küme adresini girin (bu uygulamanın 8080 numaralı bağlantı noktasında dağıttığı bağlantı uç noktası - örneğin, ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Kümeden API Yanıtı](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kümenize bir uygulamayı nasıl dağıtacağınızı öğrendiniz:

> [!div class="checklist"]
> * Örnek uygulamayı indirme
> * Kümeye dağıtma

Kümenizi temizlemek için serinin dördüncü bölümüne ilerleyin.

> [!div class="nextstepaction"]
> [Kaynaklarınızı temizleme](service-fabric-tutorial-standalone-clean-up.md)