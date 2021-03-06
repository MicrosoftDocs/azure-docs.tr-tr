---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017704"
---
Her zaman [Azure AD güvenlik gruplarını](../articles/active-directory/fundamentals/active-directory-manage-groups.md) bir ACL girişinde atanan sorumlu olarak kullanın. Bireysel kullanıcıları veya hizmet sorumlularını doğrudan atamaya yönelik fırsatı yeniden ölçeklendirin. Bu yapının kullanılması, ACL 'Leri tüm dizin yapısına yeniden uygulama gereksinimi olmadan Kullanıcı veya hizmet sorumluları eklemenize ve kaldırmanıza olanak tanır. Bunun yerine, uygun Azure AD güvenlik grubundan kullanıcıları ve hizmet sorumlularını yalnızca ekleyebilir veya kaldırabilirsiniz. 

Grupları kurmanın birçok farklı yolu vardır. Örneğin, sunucunuz tarafından oluşturulan günlük verilerini tutan, **/logData** adlı bir dizininiz olduğunu düşünün. Verileri bu klasöre Azure Data Factory (ADF). Hizmet Mühendisliği ekibinin belirli kullanıcıları günlükleri karşıya yükler ve bu klasörün diğer kullanıcılarını yönetir ve çeşitli Databricks kümeleri bu klasördeki günlükleri analiz eder. 

Bu etkinlikleri etkinleştirmek için bir `LogsWriter` Grup ve `LogsReader` grup oluşturabilirsiniz. Ardından, izinleri aşağıdaki gibi atayabilirsiniz:

- `LogsWriter`Grubu **/logData** dizininin ACL 'sine `rwx` izinlerle ekleyin.
- `LogsReader`Grubu **/logData** dizininin ACL 'sine `r-x` izinlerle ekleyin.
- ADF için hizmet sorumlusu nesnesini veya Yönetilen Hizmet Kimliği (MSI) öğesini `LogsWriters` gruba ekleyin.
- Hizmet Mühendisliği ekibine kullanıcıları `LogsWriter` gruba ekleyin.
- Databricks için hizmet sorumlusu nesnesini veya MSI 'ı `LogsReader` gruba ekleyin.

Hizmet Mühendisliği ekibinizdeki bir kullanıcı şirketten ayrılırsa, onları gruptan kaldırmanız yeterlidir `LogsWriter` . Bu kullanıcıyı bir gruba eklemediyseniz, bu kullanıcı için adanmış bir ACL girişi eklediyseniz, bu ACL girişini **/logData** dizininden kaldırmanız gerekir. Girişi, **/logData** dizininin tüm dizin hiyerarşisindeki tüm alt dizinlerden ve dosyalardan de kaldırmanız gerekir. 

Bir grup oluşturmak ve üye eklemek için bkz. [Azure Active Directory kullanarak temel Grup oluşturma ve üye ekleme](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md).