---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673255"
---
## <a name="update-the-tests"></a>Testleri güncelleştirme

Ayrıca, aynı zamanda bir test kümesi oluşturduğundan, yöntem imzasında yeni parametreyi işlemek için bu testleri güncelleştirmeniz gerekir `msg` `run` .  

_Src/test/Java_ altındaki test kodunuzun konumuna gidin, *function. Java* proje dosyasını açın ve altındaki kod satırını `//Invoke` aşağıdaki kodla değiştirin.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::