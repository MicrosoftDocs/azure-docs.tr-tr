---
title: Azure Dayanıklı İşlevler işlev türleri
description: Azure Işlevlerinde bir Dayanıklı İşlevler düzenleme bölümünde işlev-işlev iletişimini destekleyen işlev ve rol türleri hakkında bilgi edinin.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697716"
---
# <a name="durable-functions-types-and-features"></a>Türler ve Özellikler Dayanıklı İşlevler

Dayanıklı İşlevler, [Azure işlevleri](../functions-overview.md)'nin bir uzantısıdır. İşlev yürütmenin durum bilgisi düzenlemesi için Dayanıklı İşlevler kullanabilirsiniz. Dayanıklı bir işlev uygulaması, farklı Azure işlevlerinden oluşan bir çözümdür. İşlevler, dayanıklı bir işlev düzenlemesi içinde farklı roller oynayabilir. 

Şu anda Azure Işlevlerinde dört dayanıklı işlev türü vardır: etkinlik, Orchestrator, varlık ve istemci. Bu bölümün geri kalanı, bir düzenleme işleminde yer alan işlevlerin türleri hakkında daha fazla ayrıntıya gider.

## <a name="orchestrator-functions"></a>Orchestrator işlevleri

Orchestrator işlevleri, eylemlerin nasıl yürütüleceğini ve eylemlerin yürütülme sırasını açıklamaktadır. Orchestrator işlevleri [dayanıklı işlevler uygulama desenlerinde](durable-functions-overview.md#application-patterns)gösterildiği gibi kodda düzenleme (C# veya JavaScript) açıklanır. Bir düzenleme, [etkinlik işlevleri](#activity-functions), [alt](durable-functions-orchestrations.md#sub-orchestrations)düzenlemeler dahil olmak üzere çok sayıda farklı eylem türüne sahip olabilir, [dış olaylar](durable-functions-orchestrations.md#external-events), [http](durable-functions-http-features.md)ve [zamanlayıcılar](durable-functions-orchestrations.md#durable-timers)bekleniyor. Orchestrator işlevleri de [varlık işlevleriyle](#entity-functions)etkileşime geçebilir.

> [!NOTE]
> Orchestrator işlevleri sıradan kod kullanılarak yazılır, ancak kodun nasıl yazılacağı konusunda katı gereksinimler vardır. Özellikle, Orchestrator işlev kodu *belirleyici* olmalıdır. Bu belirleyici gereksinimlerin izlenmesi başarısız olursa, Orchestrator işlevlerinin düzgün şekilde çalışmamasına neden olabilir. Bu gereksinimlerle ilgili ayrıntılı bilgiler ve bunların çevresinde nasıl çalışılacağı [kod kısıtlamaları](durable-functions-code-constraints.md) konusunda bulunabilir.

Orchestrator işlevleri ve özellikleri hakkında daha ayrıntılı bilgi için bkz. [dayanıklı](durable-functions-orchestrations.md) düzenleme makalesi.

## <a name="activity-functions"></a>Etkinlik işlevleri

Etkinlik işlevleri, dayanıklı bir işlev düzenleme içinde temel iş birimidir. Etkinlik işlevleri, işlemde düzenlenmiş işlevler ve görevlerdir. Örneğin, bir siparişi işlemek için bir Orchestrator işlevi oluşturabilirsiniz. Görevler, envanteri denetlemeyi, müşteriyi borçlandırmayı ve bir sevkiyat oluşturmayı içerir. Her görev ayrı bir etkinlik işlevi olacaktır. Bu etkinlik işlevleri, paralel olarak veya her ikisinin birleşimini de çalıştırılabilir.

Orchestrator işlevlerinin aksine, etkinlik işlevleri, bunlarda yapabileceğiniz iş türünde sınırlı değildir. Etkinlik işlevleri genellikle ağ aramaları yapmak veya CPU yoğun işlemler çalıştırmak için kullanılır. Etkinlik işlevi, verileri Orchestrator işlevine geri de döndürebilir. Dayanıklı görev çerçevesi, her çağrılan etkinlik işlevinin Orchestration yürütmesi sırasında *en az bir kez* yürütüleceğini garanti eder.

> [!NOTE]
> Etkinlik işlevleri yalnızca *en az bir kez* yürütmeyi garanti ettiğinden, mümkün olduğunda Etkinlik işlevi mantığınızı *ıdempotent* yapmanızı öneririz.

Etkinlik işlevi tanımlamak için bir [etkinlik tetikleyicisi](durable-functions-bindings.md#activity-trigger) kullanın. .NET işlevleri bir `DurableActivityContext` parametresi olarak bir alır. Ayrıca, bu tetikleyiciyi işleve geçirilecek diğer JSON-serializlenebilir nesnesine da bağlayabilirsiniz. JavaScript 'te, `<activity trigger binding name>` [ `context.bindings` nesne](../functions-reference-node.md#bindings)üzerindeki özelliği aracılığıyla bir girişe erişebilirsiniz. Etkinlik işlevlerine yalnızca tek bir değer geçirilmiş olabilir. Birden çok değer geçirmek için, tanımlama grupları, diziler veya karmaşık türler kullanmanız gerekir.

> [!NOTE]
> Bir etkinlik işlevini yalnızca bir Orchestrator işlevinden tetikleyebilirsiniz.

## <a name="entity-functions"></a>Varlık işlevleri

Varlık işlevleri, küçük durum parçalarını okumak ve güncelleştirmek için işlemleri tanımlar. Genellikle bu durum bilgisi olan varlıklara *dayanıklı varlıklar* olarak başvurduk. Orchestrator işlevleri gibi, varlık işlevleri de özel tetikleyici türü, *varlık tetikleyicisi* olan işlevlerdir. Bunlar, istemci işlevlerinden veya Orchestrator işlevlerinden de çağrılabilir. Orchestrator işlevlerinin aksine, varlık işlevlerinin belirli kod kısıtlamaları yoktur. Varlık işlevleri, durumu denetim akışı aracılığıyla örtük olarak temsil etmek yerine, durumu açıkça da yönetir.

> [!NOTE]
> Varlık işlevleri ve ilgili işlevler yalnızca Dayanıklı İşlevler 2,0 ve üzeri sürümlerde kullanılabilir.

Varlık işlevleri hakkında daha fazla bilgi için bkz. [dayanıklı varlıklar](durable-functions-entities.md) makalesi.

## <a name="client-functions"></a>İstemci işlevleri

Orchestrator işlevleri bir [düzenleme tetikleyicisi bağlaması](durable-functions-bindings.md#orchestration-trigger) tarafından tetiklenir ve varlık işlevleri bir [varlık tetikleyicisi bağlaması](durable-functions-bindings.md#entity-trigger)tarafından tetiklenir. Bu tetikleyicilerin her ikisi de bir [görev hub 'ında](durable-functions-task-hubs.md)sıraya alınmış iletilere tepki vererek çalışır. Bu iletileri sunmanýn birincil yolu, bir [Orchestrator istemci bağlaması](durable-functions-bindings.md#orchestration-client) veya bir *istemci işlevinin* içinden bir [varlık istemci bağlaması](durable-functions-bindings.md#entity-client) kullanmaktır. Orchestrator olmayan herhangi bir işlev bir *istemci işlevi* olabilir. Örneğin, bir HTTP ile tetiklenen bir işlevden Orchestrator 'ı tetikleyebilirsiniz, bir Azure Olay Hub 'ı tetiklenen işlev vb. Bir işlevi bir *istemci işlevi* yapan, dayanıklı istemci çıkış bağlamasının kullanımı.

> [!NOTE]
> Diğer işlev türlerinden farklı olarak, Orchestrator ve varlık işlevleri doğrudan Azure portalındaki düğmeler kullanılarak tetiklenemez. Azure portalında bir Orchestrator veya varlık işlevini test etmek istiyorsanız, bunun yerine uygulamasının bir Orchestrator veya Entity işlevi Başlatan bir *istemci işlevi* çalıştırmalısınız. En basit test deneyimi için *el ile tetikleyici* işlevi önerilir.

Orchestrator veya Entity işlevlerinin tetiklenmesi ek olarak, *sürekli istemci* bağlama, çalışma düzenlemeleri ve varlıklarıyla etkileşim kurmak için kullanılabilir. Örneğin, düzenlemeler sorgulanabilir, sonlandırılabilir ve bunlara oluşturulan olaylar olabilir. Düzenlemeleri ve varlıkları yönetme hakkında daha fazla bilgi için bkz. [örnek yönetimi](durable-functions-instance-management.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için, [C#](durable-functions-create-first-csharp.md) veya [JavaScript](quickstart-js-vscode.md)'te ilk dayanıklı işlevinizi oluşturun.

> [!div class="nextstepaction"]
> [Dayanıklı İşlevler düzenlemeleri hakkında daha fazla bilgi edinin](durable-functions-orchestrations.md)