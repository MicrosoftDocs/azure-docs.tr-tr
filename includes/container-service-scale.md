---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a4af53e035929a44f74a95b8e9897cb1dc0c6d8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/02/2020
ms.locfileid: "66814856"
---
[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

[Azure Container Service kümesini dağıttıktan](../articles/container-service/dcos-swarm/container-service-deployment.md) sonra aracı düğüm sayısını değiştirmeniz gerekebilir. Örneğin, daha fazla kapsayıcı uygulaması veya örneği çalıştırmak için daha fazla aracı gerekebilir. 

Azure portal veya Azure CLı kullanarak DC/OS, Docker Sısınma veya Kubernetes kümesindeki aracı düğümlerinin sayısını değiştirebilirsiniz. 

## <a name="scale-with-the-azure-portal"></a>Azure Portal’da ölçeklendirme

1. [Azure Portal](https://portal.azure.com)’da, **Kapsayıcı hizmetleri**’ni bulun ve değiştirmek istediğiniz kapsayıcı hizmetine tıklayın.
2. **Kapsayıcı hizmeti** dikey penceresinde **Aracılar**’a tıklayın.
3. **VM Sayısı**’nda istenen aracıları düğümü sayısını girin.

    ![Portalda bir havuzu ölçeklendirme](./media/container-service-scale/container-service-scale-portal.png)

4. Yapılandırmayı kaydetmek için **Kaydet**’e tıklayın.

## <a name="scale-with-the-azure-cli"></a>Azure CLı ile ölçeklendirme

[Azure CLI 'Yı yükleyip](/cli/azure/install-azure-cli) Ile bir Azure hesabında oturum açın `az login` .

### <a name="see-the-current-agent-count"></a>Geçerli aracı sayısını görme
Şu anda kümedeki aracıları sayısını görmek için `az acs show` komutunu çalıştırın. Bunu yaptığınızda küme yapılandırması gösterilir. Örneğin, aşağıdaki komut `myResourceGroup` kaynak grubundaki `containerservice-myACSName` adlı kapsayıcı hizmetinin yapılandırmasını gösterir:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

Komut, `AgentPoolProfiles` altında `Count` değerindeki aracıların sayısını döndürür.

### <a name="use-the-az-acs-scale-command"></a>az acs scale komutunu kullanma
Aracı düğüm sayısını değiştirmek için `az acs scale` komutunu çalıştırın ve **kaynak grubu**, **kapsayıcı hizmeti adı** ve istenen **yeni aracı sayısı** değerlerini girin. Daha küçük veya daha büyük sayılar kullanarak sırasıyla yukarı veya aşağı ölçeklendirme yapabilirsiniz.

Örneğin, önceki kümede aracı sayısını 10 yapmak için aşağıdaki komutu yazın:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Azure CLı, yeni aracı sayısı dahil olmak üzere kapsayıcı hizmetinin yeni yapılandırmasını temsil eden bir JSON dizesi döndürür.

Daha fazla komut seçeneği için `az acs scale --help` komutunu çalıştırın.

## <a name="scaling-considerations"></a>Ölçeklendirme konusunda dikkat edilmesi gerekenler

* Aracı düğüm sayısı 1 ile 100 (dahil) arasında olmalıdır. 

* Çekirdek kotanız bir küme içindeki aracı düğümlerinin sayısını sınırlayabilir.

* Aracı düğümü ölçeklendirme işlemleri, aracı havuzunu içeren bir Azure sanal makine ölçek kümesine uygulanır. DC/OS kümesinde, bu makalede gösterilen işlemlerle yalnızca özel havuzdaki aracı düğümleri ölçeklendirilir.

* Kümenizde dağıttığınız düzenleyiciye bağlı olarak, küme üzerinde çalışan bir kapsayıcının örnek sayısını ayrı olarak ölçeklendirebilirsiniz. Örneğin, bir kapsayıcı uygulamasının örnek sayısını değiştirmek için DC/OS kümesinde [Marathon kullanıcı arabirimini](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) kullanın.


## <a name="next-steps"></a>Sonraki adımlar
* Azure CLı komutlarını Azure Container Service ile kullanmayla ilgili [daha fazla örnek](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) için bkz..
* Azure Container Service’de [DC/OS aracı havuzları](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) hakkında daha fazla bilgi edinin.

