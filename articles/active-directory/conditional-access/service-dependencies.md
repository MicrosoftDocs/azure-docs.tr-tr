---
title: Koşullu erişim hizmeti bağımlılıkları-Azure Active Directory
description: İlke tetiklemek üzere koşullu erişim Azure Active Directory koşulların nasıl kullanıldığını öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91258617"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Koşullu erişim Azure Active Directory hizmet bağımlılıkları nelerdir? 

Koşullu erişim ilkeleriyle, Web siteleri ve hizmetleri için erişim gereksinimlerini belirtebilirsiniz. Örneğin, erişim gereksinimleriniz Multi-Factor Authentication (MFA) veya [yönetilen cihazlar](require-managed-devices.md)gerektiriyor olabilir. 

Bir siteye veya hizmete doğrudan eriştiğinizde, ilgili bir ilkenin etkisinden değerlendirmek genellikle kolaydır. Örneğin, SharePoint Online için çok faktörlü kimlik doğrulaması (MFA) gerektiren bir ilkenize sahipseniz, MFA SharePoint Web portalında her oturum açma için zorlanır. Ancak, diğer bulut uygulamalarına yönelik bağımlılıklara sahip bulut uygulamaları olduğundan, bir ilkenin etkisini değerlendirmek her zaman düz bir şekilde ileri doğru kalmaz. Örneğin, Microsoft ekipleri SharePoint Online 'daki kaynaklara erişim sağlayabilir. Bu nedenle, geçerli senaryolarımızda Microsoft ekiplerine eriştiğinizde, SharePoint MFA ilkesine de tabidir. 

> [!TIP]
> Office [365](concept-conditional-access-cloud-apps.md#office-365) uygulamasının kullanılması, Office yığınındaki hizmet bağımlılıklarıyla ilgili sorunları önlemek Için tüm Office uygulamalarını hedefleyecek.

## <a name="policy-enforcement"></a>İlke zorlama 

Yapılandırılmış bir hizmet bağımlılığı varsa, ilke erken veya geç bağlantılı zorlama kullanılarak uygulanabilir. 

- **Erken bağlı ilke zorlaması** , bir kullanıcının çağıran uygulamaya erişmeden önce bağımlı hizmet ilkesini karşılaması gerektiği anlamına gelir. Örneğin, bir kullanıcının MS ekiplerinde oturum açmadan önce SharePoint ilkesini karşılaması gerekir. 
- **Geç bağlantılı ilke zorlaması** , Kullanıcı çağıran uygulamada oturum açtıktan sonra oluşur. Uygulama istekleri çağrılırken, aşağı akış hizmeti için bir belirteç olan zorlaması ertelenir. Örnek olarak, Planner ve Office.com 'e erişen MS ekipleri bulunur. 

Aşağıdaki diyagramda MS takımlar hizmet bağımlılıklarını gösterilmektedir. Kalın oklar erken bağlantılı zorlamayı belirtir Planner için kesik çizgili ok, geç bağlantılı uygulamayı gösterir. 

![MS ekipleri hizmet bağımlılıkları](./media/service-dependencies/01.png)

En iyi uygulama olarak, mümkün olan her durumda ortak ilkeleri ilgili uygulamalar ve hizmetler arasında ayarlamanız gerekir. Tutarlı bir güvenlik duruşunu size en iyi kullanıcı deneyimini sağlar. Örneğin, Exchange Online, SharePoint Online, Microsoft ekipleri ve Skype Kurumsal genelinde ortak bir ilke ayarlamak, akış içi hizmetlere uygulanan farklı ilkelerden kaynaklanan beklenmeyen istekleri önemli ölçüde azaltır. 

Bunu Office yığınındaki uygulamalarla gerçekleştirmenin harika bir yolu, tek uygulamaları hedeflemek yerine [office 365 uygulamasını](concept-conditional-access-cloud-apps.md#office-365) kullanmaktır.

Aşağıdaki tabloda, istemci uygulamalarının karşılaması gereken ek hizmet bağımlılıkları listelenmektedir  

| İstemci uygulamaları         | Aşağı akış hizmeti                          | İşlemlerini |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure yönetimi (portal ve API) | Erken bağlantılı |
| Microsoft sınıf | Exchange                                    | Erken bağlantılı |
|                     | SharePoint                                  | Erken bağlantılı |
| Microsoft Teams     | Exchange                                    | Erken bağlantılı |
|                     | MS planlayıcısı                                  | Geç bağlantılı  |
|                     | Microsoft Stream                            | Geç bağlantılı  |
|                     | SharePoint                                  | Erken bağlantılı |
|                     | Skype Kurumsal Çevrimiçi Sürüm                   | Erken bağlantılı |
| Office portalı       | Exchange                                    | Geç bağlantılı  |
|                     | SharePoint                                  | Geç bağlantılı  |
| Outlook grupları      | Exchange                                    | Erken bağlantılı |
|                     | SharePoint                                  | Erken bağlantılı |
| PowerApps           | Microsoft Azure yönetimi (portal ve API) | Erken bağlantılı |
|                     | Windows Azure Active Directory              | Erken bağlantılı |
| Project             | Dynamics CRM                                | Erken bağlantılı |
| Skype Kurumsal  | Exchange                                    | Erken bağlantılı |
| Visual Studio       | Microsoft Azure yönetimi (portal ve API) | Erken bağlantılı |
| Microsoft Forms     | Exchange                                    | Erken bağlantılı |
|                     | SharePoint                                  | Erken bağlantılı |
| Microsoft To-Do     | Exchange                                    | Erken bağlantılı |

## <a name="next-steps"></a>Sonraki adımlar

Ortamınızda Koşullu erişimin nasıl uygulanacağını öğrenmek için, bkz. [Azure Active Directory Koşullu erişim dağıtımınızı planlayın](plan-conditional-access.md).
