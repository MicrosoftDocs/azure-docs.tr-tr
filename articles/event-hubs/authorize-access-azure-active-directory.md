---
title: Azure Active Directory'ye erişimi yetkilendirme
description: Bu makalede Azure Active Directory kullanarak Event Hubs kaynaklarına erişimi yetkilendirme hakkında bilgi sağlanır.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d794b03fdbb5429983788c74cbb05a7c13bf2d76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910806"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Azure Active Directory kullanarak Event Hubs kaynaklarına erişim yetkisi verme
Azure Event Hubs, istekleri Event Hubs kaynaklara yetkilendirmek için Azure Active Directory (Azure AD) kullanılmasını destekler. Azure AD ile, bir kullanıcı veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusu için izin vermek üzere Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz. Roller ve rol atamaları hakkında daha fazla bilgi edinmek için bkz. [farklı rolleri anlama](../role-based-access-control/overview.md).

## <a name="overview"></a>Genel Bakış
Bir güvenlik sorumlusu (bir kullanıcı veya uygulama) bir Event Hubs kaynağına erişmeyi denediğinde, isteğin yetkilendirilmiş olması gerekir. Azure AD ile bir kaynağa erişim iki adımlı bir işlemdir. 

 1. İlk olarak, güvenlik sorumlusunun kimliği doğrulanır ve bir OAuth 2,0 belirteci döndürülür. Belirteç istemek için kaynak adı, `https://eventhubs.azure.net/` Tüm bulutlar/kiracılar için de aynıdır. Kafka istemcileri için, belirteç isteme kaynağı olur `https://<namespace>.servicebus.windows.net` .
 1. Ardından, belirteç, belirtilen kaynağa erişim yetkisi vermek için Event Hubs hizmetine bir isteğin bir parçası olarak geçirilir.

Kimlik doğrulama adımı, bir uygulama isteğinin çalışma zamanında bir OAuth 2,0 erişim belirteci içermesi gerekir. Bir uygulama bir Azure VM 'si, bir sanal makine ölçek kümesi veya bir Azure Işlev uygulaması gibi bir Azure varlığı içinde çalışıyorsa, kaynaklara erişmek için yönetilen bir kimlik kullanabilir. Yönetilen bir kimlik tarafından Event Hubs hizmetine yapılan isteklerin nasıl doğrulanabilmesi hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler Ile azure Event Hubs kaynaklarına erişim kimlik doğrulaması](authenticate-managed-identity.md). 

Yetkilendirme adımı, güvenlik sorumlusuna bir veya daha fazla Azure rolünün atanmasını gerektirir. Azure Event Hubs, Event Hubs kaynakları için izin kümelerini çevreleyen Azure rolleri sağlar. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. Azure rolleri hakkında daha fazla bilgi için bkz. Azure ['da azure Event Hubs için yerleşik roller](#azure-built-in-roles-for-azure-event-hubs). 

Event Hubs istek yapan yerel uygulamalar ve Web uygulamaları Azure AD ile de yetki verebilir. Erişim belirteci isteme ve bu uygulamayı Event Hubs kaynaklara yönelik istekleri yetkilendirmek üzere kullanma hakkında bilgi edinmek için bkz. [bir uygulamadan Azure AD Ile azure Event Hubs erişim kimlik doğrulaması](authenticate-application.md). 

## <a name="assign-azure-roles-for-access-rights"></a>Erişim hakları için Azure rolleri atama
Azure Active Directory (Azure AD), [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim hakları verir. Azure Event Hubs, Olay Hub 'ı verilerine erişmek için kullanılan ortak izin kümelerini çevreleyen Azure yerleşik rollerinin bir kümesini tanımlar ve verilere erişmek için özel roller de tanımlayabilir.

Azure AD güvenlik sorumlusuna bir Azure rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, abonelik düzeyi, kaynak grubu, Event Hubs ad alanı veya bunun altındaki herhangi bir kaynak kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir kullanıcı veya bir uygulama hizmeti sorumlusu ya da [Azure kaynakları için yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md)olabilir.

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Azure Event Hubs için Azure yerleşik rolleri
Azure, Azure AD ve OAuth kullanarak Event Hubs verilerine erişim yetkilendirmek için aşağıdaki Azure yerleşik rollerini sağlar:

| Rol | Açıklama | 
| ---- | ----------- | 
| [Azure Event Hubs veri sahibi](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Event Hubs kaynaklarına yönelik tüm erişimi sağlamak için bu rolü kullanın. |
| [Azure Event Hubs veri gönderici](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Event Hubs kaynaklarına gönderme erişimi vermek için bu rolü kullanın. |
| [Azure Event Hubs veri alıcısı](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Event Hubs kaynaklarına erişim/alan erişimi sağlamak için bu rolü kullanın. |

Şema kayıt defteri yerleşik rolleri için bkz. [şema kayıt defteri rolleri](schema-registry-overview.md#azure-role-based-access-control).

## <a name="resource-scope"></a>Kaynak kapsamı 
Güvenlik sorumlusuna bir Azure rolü atamadan önce, güvenlik sorumlusunun sahip olması gereken erişimin kapsamını saptayın. En iyi uygulamalar, yalnızca en dar olası kapsamı sağlamak için her zaman en iyi seçenektir.

Aşağıdaki listede, en dar kapsamdan başlayarak Event Hubs kaynaklarına erişimi kapsamındaki düzeyler açıklanmaktadır:

- **Tüketici grubu**: Bu kapsamda, rol ataması yalnızca bu varlık için geçerlidir. Şu anda Azure portal, bu düzeyde bir güvenlik sorumlusuna Azure rolü atanmasını desteklemez. 
- **Olay Hub 'ı**: rol ataması, Olay Hub 'ı varlığı ve altındaki Tüketici grubu için geçerlidir.
- **Ad alanı**: rol ataması, ad alanı altındaki tüm Event Hubs topolojisine ve onunla ilişkili tüketici grubuna yayılır.
- **Kaynak grubu**: rol atama, kaynak grubu altındaki tüm Event Hubs kaynaklarına uygulanır.
- **Abonelik**: rol ataması, abonelikteki tüm kaynak gruplarındaki tüm Event Hubs kaynaklara uygulanır.

> [!NOTE]
> - Azure rol atamalarının yaymanın beş dakika sürebileceğini aklınızda bulundurun. 
> - Bu içerik, Apache Kafka için hem Event Hubs hem de Event Hubs için geçerlidir. Kafka desteği için Event Hubs hakkında daha fazla bilgi için bkz. [Event Hubs for Kafka-Security and Authentication](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Yerleşik rollerin nasıl tanımlandığı hakkında daha fazla bilgi için bkz. [rol tanımlarını anlama](../role-based-access-control/role-definitions.md#management-and-data-operations). Azure özel rolleri oluşturma hakkında daha fazla bilgi için bkz. [Azure özel roller](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Örnekler
- [Microsoft. Azure. EventHubs örnekleri](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Bu örnekler eski **Microsoft. Azure. EventHubs** kitaplığını kullanır, ancak en son **Azure. Messaging. eventhubs** kitaplığını kullanarak kolayca güncelleştirebilirsiniz. Eski kitaplığı kullanarak örneği yeni bir tane ile taşımak için [Microsoft. Azure. eventhubs 'Den Azure. Messaging. eventhubs 'ye geçiş kılavuzu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)'na bakın.
- [Azure. Messaging. EventHubs örnekleri](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Bu örnek, en son **Azure. Messaging. EventHubs** kitaplığını kullanacak şekilde güncelleştirilmiştir.
- [Kafka-OAuth örnekleri için Event Hubs](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Sonraki adımlar
- Bir güvenlik sorumlusu için Azure yerleşik rolünü atamayı öğrenin, bkz. [Azure Active Directory kullanarak Event Hubs kaynaklarına erişim kimlik doğrulaması](authenticate-application.md).
- [Azure RBAC ile özel roller oluşturmayı](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)öğrenin.
- [Azure ACTIVE DIRECTORY Eh ile kullanmayı](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK) öğrenin

Aşağıdaki ilgili makalelere bakın:

- [Azure Active Directory kullanarak bir uygulamadan Azure Event Hubs istek kimliklerini doğrulama](authenticate-application.md)
- [Event Hubs kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](authenticate-managed-identity.md)
- [Paylaşılan erişim Imzalarını kullanarak Azure Event Hubs istek kimliklerini doğrulama](authenticate-shared-access-signature.md)
- [Paylaşılan erişim Imzalarını kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-shared-access-signature.md)
