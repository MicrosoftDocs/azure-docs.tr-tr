---
title: 'Kümeye veri ata: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Kümeleme modeline puan vermek için Azure Machine Learning ' de verileri kümeye ata modülünü kullanmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898867"
---
# <a name="module-assign-data-to-clusters"></a>Modül: kümelere veri atama

Bu makalede Azure Machine Learning tasarımcısında *kümelere veri atama* modülünün nasıl kullanılacağı açıklanır. Modül, *K-anlamı kümeleme* algoritması ile eğitilen bir kümeleme modeli aracılığıyla tahmin oluşturur.

Kümelere veri ata modülü, her yeni veri noktası için olası atamaları içeren bir veri kümesi döndürür. 

## <a name="how-to-use-assign-data-to-clusters"></a>Kümelere veri atama kullanımı
  
1. Azure Machine Learning tasarımcısında, daha önce eğitilen bir kümeleme modeli bulun. Aşağıdaki yöntemlerden birini kullanarak bir kümeleme modeli oluşturabilir ve eğitebilirsiniz:  
  
    - K- [ortalamalar kümeleme modülünü kullanarak](k-means-clustering.md) k-bit kümeleme algoritmasını yapılandırın ve bir veri kümesi ve bir veri kümesi (Bu makale) Ile eğitme modeli kullanarak modeli eğitme.  
  
    - Ayrıca, çalışma alanınızdaki **kaydedilmiş modeller** grubundan mevcut bir eğitimli kümeleme modeli ekleyebilirsiniz.

2. Eğitilen modeli, **kümeler Için veri ata**'nın sol giriş bağlantı noktasına ekleyin.  

3. Giriş olarak yeni bir veri kümesi iliştirin. 

   Bu veri kümesinde, Etiketler isteğe bağlıdır. Genellikle kümeleme, denetimli bir öğrenme yöntemidir. Kategorileri önceden bilmeniz beklenmez. Ancak, giriş sütunlarının, kümeleme modeliyle eğitiminde kullanılan sütunlarla aynı olması gerekir veya bir hata oluşur.

    > [!TIP]
    > Küme tahminlerinin tasarımcıya yazılan sütunların sayısını azaltmak için, [veri kümesindeki sütunları seç](select-columns-in-dataset.md)' i kullanın ve sütunların bir alt kümesini seçin. 
    
4. Sonuçların, sonuçları görüntüleyen bir sütun da dahil olmak üzere tam giriş veri kümesini içermesini istiyorsanız **yalnızca sonuç için ekleme veya onay işaretini kaldır** onay kutusunu seçili bırakın.
  
    Bu onay kutusunu temizlerseniz yalnızca sonuçlar döndürülür. Bu seçenek, bir Web hizmetinin parçası olarak tahmin oluştururken yararlı olabilir.
  
5.  İşlem hattını gönderme.  
  
### <a name="results"></a>Sonuçlar

+  Veri kümesindeki değerleri görüntülemek için, modüle sağ tıklayın ve ardından **Görselleştir**' i seçin. Ya da modülü seçip sağ paneldeki **çıktılar** sekmesine geçiş yapın ve sonucu görselleştirmek Için **bağlantı noktası çıktılarında** histogram simgesine tıklayın.

