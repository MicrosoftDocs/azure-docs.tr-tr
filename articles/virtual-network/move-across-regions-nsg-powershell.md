---
title: Azure ağ güvenlik grubu (NSG) Azure PowerShell kullanarak başka bir Azure bölgesine taşıma
description: Azure PowerShell kullanarak Azure ağ güvenlik grubunu bir Azure bölgesinden diğerine taşımak için Azure Resource Manager şablonu kullanın.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934992"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Azure ağ güvenlik grubu (NSG) Azure PowerShell kullanarak başka bir bölgeye taşıma

Mevcut NSG 'lerinizi bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, test için aynı yapılandırma ve güvenlik kurallarına sahip bir NSG oluşturmak isteyebilirsiniz. Ayrıca, olağanüstü durum kurtarma planlamasının bir parçası olarak bir NSG 'yi başka bir bölgeye taşımak isteyebilirsiniz.

Azure güvenlik grupları bir bölgeden diğerine taşınamaz. Bununla birlikte, bir NSG 'nin mevcut yapılandırma ve güvenlik kurallarını dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz.  Daha sonra, NSG 'yi bir şablona dışarı aktararak, parametreleri hedef bölgeyle eşleşecek şekilde değiştirerek ve sonra şablonu yeni bölgeye dağıtabilmeniz için kaynağı başka bir bölgede aşamalandırın.  Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [kaynak gruplarını şablonlara dışarı aktarma](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Önkoşullar

- Azure ağ güvenlik grubunun, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Azure ağ güvenlik grupları bölgeler arasında taşınamaz.  Yeni NSG 'yi hedef bölgedeki kaynaklarla ilişkilendirmeniz gerekir.

- Bir NSG yapılandırmasını dışarı aktarmak ve başka bir bölgede NSG oluşturmak üzere bir şablon dağıtmak için, ağ katılımcısı rolü veya daha yüksek bir sürümü gerekir.
   
- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, genel IP 'Ler ve sanal ağlar dahil değildir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, kullanılan hedef bölgede NSG 'ler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin bu işlem için NSG 'lerin eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun.  Bkz. [Azure aboneliği ve hizmet sınırları, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Hazırlama ve taşıma
Aşağıdaki adımlarda, ağ güvenlik grubunun yapılandırma ve güvenlik kuralı Kaynak Yöneticisi şablonu kullanarak taşınması ve Azure PowerShell kullanarak NSG yapılandırma ve güvenlik kurallarının hedef bölgeye taşınması gösterilmektedir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Şablonu dışarı aktarma ve bir betikten dağıtma

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hedef bölgeye taşımak istediğiniz NSG 'nin kaynak KIMLIĞINI alın ve [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)kullanarak bir değişkene yerleştirin:

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Kaynak NSG 'yi bir. JSON dosyasına dışarı aktarma [-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)komutunu yürütebileceğiniz dizine aktarın:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. İndirilen dosya, kaynağın öğesinden verildikten sonra adı alınacaktır.  **\<resource-group-name> . JSON** adlı komuttan aktarılmış dosyayı bulun ve seçtiğiniz bir düzenleyicide açın:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. NSG adının parametresini düzenlemek için, kaynak NSG adının **DefaultValue** özelliğini hedef NSG adı olarak değiştirin, adın tırnak içinde olduğundan emin olun:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. NSG yapılandırmasının ve güvenlik kurallarının taşınacağı hedef bölgeyi düzenlemek için **kaynaklar** altındaki **Location** özelliğini değiştirin:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Bölge konum kodlarını almak için aşağıdaki komutu çalıştırarak [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 'ini Azure PowerShell kullanabilirsiniz:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Ayrıca, isterseniz **\<resource-group-name> . JSON** içindeki diğer parametreleri değiştirebilirsiniz ve gereksinimlerinize bağlı olarak isteğe bağlıdır:

    * **Güvenlik kuralları** - **\<resource-group-name> . JSON** dosyasındaki **SecurityRules** bölümüne kural ekleyerek veya kaldırarak hedef NSG 'ye dağıtılan kuralları düzenleyebilirsiniz:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Hedef NSG 'deki kuralların eklenmesini veya kaldırılmasını bitirmek için, Ayrıca, **\<resource-group-name> . JSON** dosyasının sonundaki özel kural türlerini aşağıdaki örnekte yer alarak düzenlemeniz gerekir:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. **\<resource-group-name> . JSON** dosyasını kaydedin.

10. Target NSG 'nin [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanılarak dağıtılması için hedef bölgede bir kaynak grubu oluşturun:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Düzenlenen **\<resource-group-name> . JSON** dosyasını önceki adımda oluşturulan kaynak grubuna [New-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)kullanarak dağıtın:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Hedef bölgede kaynakların oluşturulduğunu doğrulamak için [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) ve [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)komutunu kullanın:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Vazgeç 

Dağıtımdan sonra, hedefte bulunan NSG 'yi baştan başlatmak veya atmak istiyorsanız hedefte oluşturulan kaynak grubunu silin ve taşınan NSG silinir.  Kaynak grubunu kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanın:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Temizleme

Değişiklikleri uygulamak ve NSG 'nin taşınmasını tamamlamak için, kaynak NSG veya kaynak grubunu silin, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) veya [Remove-aznetworksecuritygroup](/powershell/module/az.network/remove-aznetworksecuritygroup)komutunu kullanın:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure ağ güvenlik grubunu bir bölgeden diğerine taşıdı ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM’lerini başka bir bölgeye taşıma](../site-recovery/azure-to-azure-tutorial-migrate.md)
