---
title: Python ile Azure Data Lake Storage 1. hesabını yönetme
description: Azure Data Lake Storage 1. hesap yönetimi işlemleri için Python SDK 'sını nasıl kullanacağınızı öğrenin.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 35e71b80c6f47bb13f7a2b490b493b0cb42acf04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103518"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Python kullanarak Azure Data Lake Storage 1. hesap yönetimi işlemleri
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Data Lake Storage 1. hesabı oluşturma, Data Lake Storage 1. hesaplarını listeleme vb. gibi temel hesap yönetim işlemlerini gerçekleştirmek için Azure Data Lake Storage 1. için Python SDK 'yı nasıl kullanacağınızı öğrenin. Python kullanarak Data Lake Storage 1. dosya sistemi işlemlerinin nasıl gerçekleştirileceği hakkında yönergeler için bkz. [Python kullanarak Data Lake Storage 1. dosya sistemi işlemleri](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Önkoşullar

* **Python**. Python’u [buradan](https://www.python.org/downloads/) indirebilirsiniz. Bu makalede Python 3.6.2 kullanılmıştır.

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Bir Azure Kaynak grubu**. Talimatlar için bkz. [Azure kaynak grubu oluşturma](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Modülleri yükleme

Python kullanarak Data Lake Storage 1. çalışmak için üç modül yüklemeniz gerekir.

* `azure-mgmt-resource` modülü, Active Directory gibi şeyler için Azure modüllerini içerir.
* `azure-mgmt-datalake-store`Azure Data Lake Storage 1. hesap yönetimi işlemlerini içeren modül. Bu modülle ilgili daha fazla bilgi için bkz. [Azure Data Lake Storage 1. Management Module Reference](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Azure Data Lake Storage 1. dosya sistemi işlemlerini içeren modül. Bu modülle ilgili daha fazla bilgi için bkz. [Azure-datalake-Store FileSystem Module Reference](/python/api/azure-datalake-store/azure.datalake.store.core/).

Modülleri yüklemek için aşağıdaki komutları kullanın.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

1. Tercih ettiğiniz IDE’de yeni bir Python uygulaması (örneğin, **örneğim.py**) oluşturun.

2. Gerekli modülleri içeri aktarmak için aşağıdaki kod parçacığını ekleyin

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Değişiklikleri örneğim.py uygulamasına kaydedin.

## <a name="authentication"></a>Kimlik Doğrulaması

Bu bölümde Azure AD ile gerçekleştirilen farklı kimlik doğrulama yöntemlerinden bahsedeceğiz. Şu seçenekleri kullanabilirsiniz:

* Uygulamanız için son kullanıcı kimlik doğrulaması için bkz. [Python kullanarak Data Lake Storage 1. Ile Son Kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-python.md).
* Uygulamanıza yönelik hizmetten hizmete kimlik doğrulaması için bkz. [Python kullanarak Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>İstemci ve Data Lake Storage 1. hesabı oluşturma

Aşağıdaki kod parçacığı ilk olarak Data Lake Storage 1. hesabı istemcisini oluşturur. Bir Data Lake Storage 1. hesabı oluşturmak için istemci nesnesini kullanır. Kod parçacığı son olarak bir dosya sistemi istemci nesnesi oluşturur.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Data Lake Storage 1. hesaplarını listeleyin

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabını silme

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Sonraki adımlar
* [Python kullanarak Data Lake Storage 1. dosya sistemi işlemleri](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Ayrıca bkz.

* [Azure-datalake-Store Python (FileSystem) başvurusu](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Azure Data Lake Storage 1. uyumlu açık kaynak büyük veri uygulamaları](data-lake-store-compatible-oss-other-applications.md)