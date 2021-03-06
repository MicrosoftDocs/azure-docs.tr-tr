---
title: SAS belirteci ile güvenli bir şekilde şablon dağıtın
description: Bir SAS belirteci ile korunan Azure Resource Manager şablonuyla Azure 'a kaynak dağıtın. Azure PowerShell ve Azure CLı gösterir.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88855667"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>SAS belirteci ile özel ARM şablonu dağıtma

Azure Resource Manager şablonunuz (ARM şablonu) bir depolama hesabında bulunuyorsa, genel kullanıma sunulmamak için şablona erişimi kısıtlayabilirsiniz. Şablon için bir paylaşılan erişim imzası (SAS) belirteci oluşturarak ve dağıtım sırasında bu belirteci sağlayarak güvenli bir şablona erişirsiniz. Bu makalede, bir SAS belirteci ile şablon dağıtmak için Azure PowerShell veya Azure CLı 'nın nasıl kullanılacağı açıklanmaktadır.

> [!IMPORTANT]
> Şablonunuzun bir SAS belirteciyle güvenliğini sağlamak yerine, [şablon](template-specs.md)özelliklerini kullanmayı düşünün. Şablon özellikleri sayesinde şablonlarınızı kuruluşunuzdaki diğer kullanıcılarla paylaşabilir ve Azure RBAC aracılığıyla şablonlara erişimi yönetebilirsiniz.

## <a name="create-storage-account-with-secured-container"></a>Güvenli kapsayıcı ile depolama hesabı oluşturma

Aşağıdaki betik, ortak erişim kapatılmış bir depolama hesabı ve kapsayıcısı oluşturur.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Şablonu depolama hesabına yükle

Şimdi, şablonunuzu depolama hesabına yüklemeye hazırsınız demektir. Kullanmak istediğiniz şablonun yolunu belirtin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Dağıtım sırasında SAS belirteci sağla

Bir depolama hesabında özel bir şablon dağıtmak için bir SAS belirteci oluşturun ve şablon için URI 'sine dahil edin. Dağıtım işleminin tamamlanmasına yetecek sürenin sona ereceği süre sonu süresini ayarlayın.

> [!IMPORTANT]
> Şablonu içeren Blobun yalnızca hesap sahibi tarafından erişilebilir. Ancak, blob için bir SAS belirteci oluşturduğunuzda, blob bu URI 'ye sahip herkes tarafından erişilebilir. Başka bir Kullanıcı URI 'yi kesirse, bu kullanıcı şablona erişebilir. SAS belirteci, şablonlarınıza erişimi sınırlayan iyi bir yoldur, ancak parolalar gibi hassas verileri doğrudan şablonda içermemelidir.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki örnek, Cloud Shell Bash ortamıyla birlikte çalışmaktadır. Diğer ortamlar, SAS belirtecinin sona erme zamanını oluşturmak için farklı bir sözdizimi gerektirebilir.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Bağlı şablonlar içeren bir SAS belirteci kullanmanın bir örneği için bkz. [Azure Resource Manager ile bağlantılı şablonlar kullanma](linked-templates.md).


## <a name="next-steps"></a>Sonraki adımlar
* Şablonları dağıtmaya giriş için bkz. [ARM şablonlarıyla kaynak dağıtma ve Azure PowerShell](deploy-powershell.md).
* Şablonda parametreleri tanımlamak için bkz. [yazma şablonları](template-syntax.md#parameters).
