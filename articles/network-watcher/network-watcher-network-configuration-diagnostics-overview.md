---
title: Azure ağ Izleyicisi 'nde ağ yapılandırması tanılamalarına giriş | Microsoft Docs
description: Bu sayfa ağ Izleyicisi-ağ yapılandırması tanılama 'ya genel bakış sağlar
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 5feef79a08789ad381b0c93cb938abd9effdfcc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502017"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Azure ağ Izleyicisi 'nde ağ yapılandırması tanılamalarına giriş

Ağ yapılandırması Tanılama Aracı, müşterilerin Azure sanal ağınızda hangi trafik akışına izin verileceğini ve hangilerinin reddedileceğini anlamalarına yardımcı olur. NSG kurallarınızın doğru şekilde yapılandırılıp yapılandırılmadığını anlamak için yardımcı olabilir. 

## <a name="pre-requisites"></a>Ön koşullar
Ağ yapılandırması tanılamayı kullanmak için, aboneliğinizde ağ Izleyicisi etkinleştirilmelidir. Etkinleştirmek için bkz. [Azure Ağ İzleyicisi örneği oluşturma](./network-watcher-create.md) .

## <a name="background"></a>Arka Plan

- Azure 'daki kaynaklarınız sanal ağlar (VNet 'ler) ve alt ağlar aracılığıyla bağlanır. Bu VNET 'lerin ve alt ağların güvenliği bir ağ güvenlik grubu (NSG) kullanılarak yönetilebilir.
- Bir NSG, bağlı olduğu kaynaklara ağ trafiğine izin veren veya reddeden güvenlik kurallarının bir listesini içerir. NSG 'Ler, VM 'lere bağlı olan alt ağlar, tek tek VM 'Ler veya tek ağ arabirimleri (NIC) ile ilişkilendirilebilir. 
- Ağınızdaki tüm trafik akışları, geçerli NSG kuralları kullanılarak değerlendirilir.
- Kurallar en düşükten en yükseğe doğru öncelik sayısına göre değerlendirilir 

## <a name="how-does-network-configuration-diagnostic-work"></a>Ağ yapılandırması tanılaması nasıl çalışır? 

Belirli bir akışta, NCD aracı akışın benzetimini çalıştırır ve akışa izin verme/reddetme kuralları hakkında ayrıntılı bilgi (veya reddedildi) ve daha ayrıntılı bilgiler verir.  Müşterilerin kaynak, hedef, protokol vb. gibi bir akışın ayrıntılarını sağlaması gerekir. Araç, trafiğe izin verilip verilmediğini, belirtilen akış için değerlendirilen NSG kurallarını ve her kural için değerlendirme sonuçlarını döndürür.

## <a name="next-steps"></a>Sonraki adımlar

Diğer arabirimler üzerinden ağ yapılandırması tanılamayı kullanma
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic)
 - [Azure CLI](/cli/azure/network/watcher#az_network_watcher_run_configuration_diagnostic)