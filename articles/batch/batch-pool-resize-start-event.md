---
title: Azure Batch havuzu yeniden boyutlandırma başlangıç olayı
description: Batch havuzu yeniden boyutlandırma başlangıç olayı başvurusu. Örnek, el ile yeniden boyutlandırmayla 0 ' dan 2 ' ye kadar düğüm yeniden boyutlandırılması için havuz yeniden boyutlandırma başlangıç olayının gövdesini gösterir.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be64a2ef30cbe3c404633b29202a4adf1e49ea9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803621"
---
# <a name="pool-resize-start-event"></a>Havuz yeniden boyutlandırma başlangıç olayı

 Bu olay, bir havuz yeniden boyutlandırmaya başladığında yayınlanır. Havuz yeniden boyutlandırılması zaman uyumsuz bir olay olduğundan, yeniden boyutlandırma işlemi tamamlandıktan sonra havuzun yeniden boyutlandırılması tamamlandı olayını bekleyebilir.

 Aşağıdaki örnek, el ile yeniden boyutlandırmayla 0 ' dan 2 ' ye kadar düğüm yeniden boyutlandırılması için bir havuz yeniden boyutlandırma başlangıç olayının gövdesini gösterir.

```
{
   "id": "myPool1",
   "nodeDeallocationOption": "Invalid",
   "currentDedicatedNodes": 0,
   "targetDedicatedNodes": 2,
   "currentLowPriorityNodes": 0,
   "targetLowPriorityNodes": 2,
   "enableAutoScale": false,
   "isAutoPool": false
}
```

|Öğe|Tür|Notlar|
|-------------|----------|-----------|
|`id`|Dize|Havuzun KIMLIĞI.|
|`nodeDeallocationOption`|Dize|Havuz boyutu azaltılmışsa, havuzdan düğümlerin ne zaman kaldırılabileceği belirtir.<br /><br /> Olası değerler şunlardır:<br /><br /> **yeniden kuyruğa alma** – çalışan görevleri sonlandırır ve yeniden kuyruğa alma onları sonlandırın. Görevler, iş etkinleştirildiğinde yeniden çalışacaktır. Görevler sonlandırıldıktan hemen sonra düğümleri kaldırın.<br /><br /> **Sonlandır** – çalışan görevleri sonlandırın. Görevler yeniden çalıştırılmaz. Görevler sonlandırıldıktan hemen sonra düğümleri kaldırın.<br /><br /> **taskcompletion** : Şu anda çalışan görevlerin tamamlanmasına izin verin. Beklerken yeni görev zamanlayın. Tüm görevler tamamlandığında düğümleri kaldırın.<br /><br /> **Retaineddata** -çalışmakta olan görevlerin tamamlanmasına izin verin, ardından tüm görev verilerini bekletme dönemlerinin süre sonu için bekleyin. Beklerken yeni görev zamanlayın. Tüm görev bekletme dönemlerinin süresi dolduğunda düğümleri kaldırın.<br /><br /> Varsayılan değer yeniden kuyruğa alma ' dir.<br /><br /> Havuz boyutu artarak değer **geçersiz** olarak ayarlanır.|
|`currentDedicatedNodes`|Int32|Havuza Şu anda atanmış olan işlem düğümlerinin sayısı.|
|`targetDedicatedNodes`|Int32|Havuz için istenen işlem düğümlerinin sayısı.|
|`currentLowPriorityNodes`|Int32|Havuza Şu anda atanmış olan işlem düğümlerinin sayısı.|
|`targetLowPriorityNodes`|Int32|Havuz için istenen işlem düğümlerinin sayısı.|
|`enableAutoScale`|Bool|Havuz boyutunun zaman içinde otomatik olarak görüntülenip görüntülenmeyeceğini belirtir.|
|`isAutoPool`|Bool|Havuzun bir işin oto havuzu mekanizması aracılığıyla oluşturulup oluşturulmayacağını belirtir.|
