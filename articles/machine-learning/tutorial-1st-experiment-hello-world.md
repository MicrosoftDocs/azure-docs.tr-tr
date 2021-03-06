---
title: 'Öğretici: "Hello World!" Çalıştır Python betiği'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Başlarken serisinin 2. bölümü, önemsiz bir "Hello World!" öğesinin nasıl gönderileceği gösterilmektedir Buluta Python betiği.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
ms.openlocfilehash: 4f2b01b7a04958c4bd1f97332b54a1ff4fc32356
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522334"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Öğretici: "Hello World!" Çalıştır Python betiği (Bölüm 2/4)

Bu öğreticide, Python için Azure Machine Learning SDK 'sını kullanarak bir Python "Hello World!" öğesini nasıl göndereceğini öğrenirsiniz. SCRIPT.

Bu öğretici, Azure 'da Azure Machine Learning ve iş tabanlı makine öğrenimi görevlerinin temellerini öğrendiğiniz *dört bölümden oluşan bir öğretici serisinin 2. bölümüdür* . Bu öğretici, [1. Bölüm: yerel makinenizi Azure Machine Learning Için ayarlama](tutorial-1st-experiment-sdk-setup-local.md)sırasında tamamladığınız çalışmayı oluşturur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * "Merhaba Dünya!" oluşturma ve çalıştırma Python betiği yerel olarak.
> * "Hello World!" göndermek için bir Python denetim betiği oluşturun Azure Machine Learning.
> * Denetim betiğinin Azure Machine Learning kavramlarını anlayın.
> * "Hello World!" öğesini gönder ve Çalıştır SCRIPT.
> * Kod çıktılarınızı bulutta görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

- Zaten bir Azure Machine Learning çalışma alanınız yoksa [Bölüm 1](tutorial-1st-experiment-sdk-setup-local.md) ' in tamamlanması.

## <a name="create-and-run-a-python-script-locally"></a>Yerel olarak bir Python betiği oluşturma ve çalıştırma

`src` `tutorial` Azure Machine Learning bir işlem kümesi üzerinde çalıştırmak istediğiniz kodu depolamak için dizin altında adlı yeni bir alt dizin oluşturun. `src`Alt dizinde `hello.py` Python betiğini oluşturun:

```python
# src/hello.py
print("Hello world!")
```

Proje dizin yapınız şu şekilde görünür:

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="Dizin yapısı src alt dizininde hello.py gösterir":::


### <a name="test-your-script-locally"></a><a name="test"></a>Betiğinizi yerel olarak test etme

Kodunuzu, en sevdiğiniz IDE 'yi veya bir terminali kullanarak yerel olarak çalıştırabilirsiniz. Kodu yerel olarak çalıştırmak kodun etkileşimli hata ayıklamasının avantajına sahiptir.  Etkinleştirilen *Tutorial1* Conda ortamını içeren pencerede, Python dosyasını çalıştırın:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Yerel olarak](?success=run-local#control-script) [bir sorunla karşılaşdığım](https://www.research.net/r/7C2NTH7?issue=run-local) betiği çalıştırdım

## <a name="create-a-control-script"></a><a name="control-script"></a> Denetim betiği oluşturma

Bir *Denetim betiği* `hello.py` , komut dosyanızı bulutta çalıştırmanızı sağlar. Machine Learning kodunuzun nasıl ve nerede çalıştırılacağını denetlemek için denetim betiğini kullanın.  

Öğretici dizininizde, adlı yeni bir Python dosyası oluşturun `03-run-hello.py` ve aşağıdaki kodu kopyalayın/bu dosyaya yapıştırın:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Kodu anlama

Denetim betiğinin nasıl çalıştığına ilişkin bir açıklama aşağıda verilmiştir:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Çalışma alanı](/python/api/azureml-core/azureml.core.workspace.workspace) Azure Machine Learning çalışma alanınıza bağlanarak Azure Machine Learning kaynaklarınızla iletişim kurabilirsiniz.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Deneme](/python/api/azureml-core/azureml.core.experiment.experiment) , tek bir ad altında birden çok çalıştırma düzenlemek için basit bir yol sağlar. Daha sonra, denemeleri 'ın onlarca çalışma arasında ölçümleri karşılaştırmayı nasıl kolaylaştıradiğini görebilirsiniz.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) kodunuzu sarmalar `hello.py` ve çalışma alanınıza geçirir. Adından da anlaşılacağı gibi, bu sınıfı kullanarak _betiğinizi_ Azure Machine Learning ' de nasıl _çalıştırmak_ istediğinizi _yapılandırabilirsiniz_ . Ayrıca, betiğin çalışacağı işlem hedefini de belirtir. Bu kodda, hedef, [Kurulum öğreticisinde](tutorial-1st-experiment-sdk-setup-local.md)oluşturduğunuz işlem kümesidir.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Betiğinizi gönderir. Bu gönderim bir [çalıştırma](/python/api/azureml-core/azureml.core.run%28class%29)olarak adlandırılır. Bir çalıştırma, kodunuzun tek bir yürütmesini kapsüller. Betik ilerlemesini izlemek için bir çalıştırma kullanın, çıktıyı yakalayın, sonuçları çözümleyin, ölçümleri görselleştirin ve daha fazlasını yapın.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run`Nesnesi, kodunuzun yürütülmesi üzerinde bir tanıtıcı sağlar. Azure Machine Learning Studio 'dan, Python betiğiyle yazdırılan URL ile ilerleme durumunu izleyin.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C2NTH7?issue=create-control-script) [Denetim betiğini](?success=create-control-script#submit) oluşturdum

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Kodunuzu buluta gönderme ve çalıştırma

Denetim betiğinizi çalıştırın, bu, `hello.py` [Kurulum öğreticisinde](tutorial-1st-experiment-sdk-setup-local.md)oluşturduğunuz işlem kümesinde sırayla çalışır.


```bash
python 03-run-hello.py
```

> [!TIP]
> Bu kodu çalıştırmak, aboneliğe erişiminizin olmadığı bir hata veriyorsa, bkz. kimlik doğrulama seçenekleri hakkında bilgi için bkz. [çalışma alanına bağlanma](how-to-manage-workspace.md?tab=python#connect-multi-tenant) .

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud) [bulutta kod gönderdim](?success=submit-to-cloud#monitor)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Studio 'yu kullanarak kodunuzu bulutta izleyin

Betiğinizdeki çıktı, aşağıdaki gibi görünen bir Studio bağlantısı içerir: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Bağlantıyı izleyin.  İlk olarak, **hazırlama** durumunu görürsünüz.  İlk çalıştırmanın tamamlanması 5-10 dakika sürer. Bunun nedeni aşağıdakiler olur:

* Bir Docker görüntüsü bulutta yerleşiktir
* İşlem kümesi 0 ' dan 1 düğümden yeniden boyutlandırılır
* Docker görüntüsü, işlem için indirilir. 

Sonraki çalıştırmalar, Docker görüntüsü işlem üzerinde önbelleğe alındığı için çok daha hızlıdır (~ 15 saniye). İlk çalıştırma tamamlandıktan sonra kodu aşağıdaki kodu yeniden göndererek test edebilirsiniz.

İş tamamlandıktan sonra **çıktılar + Günlükler** sekmesine gidin. Aşağıdakine benzer bir dosya görebilirsiniz `70_driver_log.txt` :

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

8. satırda "Merhaba Dünya!" öğesini görürsünüz. çıktıların.

`70_driver_log.txt`Dosya, bir çalıştırmanın standart çıktısını içerir. Bu dosya, bulutta uzak çalıştırmaları hata ayıkladığınızda yararlı olabilir.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio) [Studio 'Daki günlüğü gördüm](?success=monitor-in-studio#next-steps)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, basit bir "Hello World!" gerçekleştirdiğimiz betiği oluşturup Azure 'da çalıştırdık. Azure Machine Learning çalışma alanınıza bağlanmayı, deneme oluşturmayı ve `hello.py` kodunuzu buluta göndermeyi gördünüz.

Sonraki öğreticide, ' den daha ilginç bir şey çalıştırarak bu dersleri üzerinde derleyebilirsiniz `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Öğretici: Modeli eğitme](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Öğretici serisini burada bitirebilmeniz ve bir sonraki adımda ilerlemeniz istiyorsanız [kaynaklarınızı temizlemeyi](tutorial-1st-experiment-bring-data.md#clean-up-resources)unutmayın.
