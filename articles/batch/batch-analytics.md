---
title: Azure Batch Analizi
description: Batch Analytics 'teki konular, Batch hizmeti kaynakları için kullanılabilen olaylar ve uyarılar için başvuru bilgileri içerir.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91849520"
---
# <a name="batch-analytics"></a>Batch Analizi

Bu bölümdeki konular, Batch hizmeti kaynakları için kullanılabilen olaylar ve uyarılar için başvuru bilgileri içerir.

Batch tanılama günlüklerini etkinleştirme ve kullanma hakkında daha fazla bilgi için bkz. [Azure Batch tanılama günlüğü](./batch-diagnostics.md) .

## <a name="diagnostic-logs"></a>Tanılama günlükleri

Azure Batch hizmeti, belirli toplu kaynakların kullanım ömrü boyunca aşağıdaki tanılama günlüğü olaylarını yayar.

### <a name="service-log-events"></a>Hizmet günlüğü olayları

- [Havuz oluşturma](batch-pool-create-event.md)
- [Havuz silme başlangıcı](batch-pool-delete-start-event.md)
- [Havuz silme Tamam](batch-pool-delete-complete-event.md)
- [Havuz yeniden boyutlandırma başlangıcı](batch-pool-resize-start-event.md)
- [Havuz yeniden boyutlandırma Tamam](batch-pool-resize-complete-event.md)
- [Havuz otomatik ölçeklendirme](batch-pool-autoscale-event.md)
- [Görev başlangıcı](batch-task-start-event.md)
- [Görev tamamlanma](batch-task-complete-event.md)
- [Görev başarısız](batch-task-fail-event.md)
- [Görev zamanlama başarısız](batch-task-schedule-fail-event.md)
