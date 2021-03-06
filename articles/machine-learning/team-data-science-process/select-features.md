---
title: Takım veri bilimi Işleminde Özellik seçimi
description: Özellik seçiminin amacını açıklar ve Machine Learning 'in veri geliştirme sürecinde rollerinin örneklerini sağlar.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321330"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Team Data Science Process’te (TDSP) özellik seçimi
Bu makalede, özellik seçiminin amaçları açıklanmakta ve makine öğrenimi 'nin veri geliştirme sürecinde rolün örnekleri verilmiştir. Bu örnekler Azure Machine Learning Studio çizilir.

Özelliklerin mühendislik ve seçimi, ekip veri bilimi işleminin (TDSP) [Team Data Science süreci nedir?](overview.md)makalesinde özetlenen bir parçasıdır. Özellik Mühendisliği ve seçimi, TDSP 'nin **Özellikler geliştirme** adımının parçalarından oluşur.

* **özellik Mühendisliği**: Bu işlem, verilerdeki mevcut ham özelliklerden ek ilgili özellikler oluşturmaya çalışır ve öğrenme algoritmasına tahmine dayalı güç düzeyini artırır.
* **Özellik seçimi**: Bu işlem, eğitim sorununun boyutlılık düzeyini azaltmak için bir denemede orijinal veri özelliklerinin anahtar alt kümesini seçer.

Normalde **özellik Mühendisliği** , ek özellikler oluşturmak için önce uygulanır ve ardından **Özellik seçimi** adımı, ilgisiz, yedekli veya çok bağıntılı özellikleri ortadan kaldırmak için gerçekleştirilir.

## <a name="filter-features-from-your-data---feature-selection"></a>Veri özelliği seçiminizden özellikleri filtreleme
Özellik seçimi, sınıflandırma veya regresyon görevleri için kullanılabilir. Amaç, başlangıçtaki veri kümesinden alınan özelliklerin bir alt kümesini seçmek, verilerdeki en fazla varyans miktarını göstermek için minimum bir özellik kümesi kullanarak boyutlarını azaltır. Bu özellik alt kümesi, modeli eğitme için kullanılır. Özellik seçimi iki ana amaca hizmet eder.

* İlk olarak, özellik seçimi genellikle ilgisiz, yedekli veya çok bağıntılı özellikleri ortadan kaldırarak sınıflandırma doğruluğunu artırır.
* İkincisi, model eğitimi sürecini daha verimli hale getiren Özellik sayısını düşürür. Verimlilik, destek vektör makineleri gibi tren açısından pahalı olan öğrenenler için önemlidir.

Özellik seçimi, modeli eğitmek için kullanılan veri kümesindeki özelliklerin sayısını azaltmak için arama yapmaz, ancak "boyutlılık azaltma" terimiyle eşleşmez. Özellik seçimi yöntemleri, verileri değiştirmeden özgün özelliklerin bir alt kümesini ayıklar.  Boyutlılık azaltma yöntemleri, özgün özellikleri dönüştürebildiği ve bu nedenle bunları değiştirebilen, uygulanan özellikler sunar. Boyut azaltma yöntemlerine örnek olarak, sorumlu bileşen analizi, kurallı bağıntı Analizi ve tekil değer ayrıştırma sayılabilir.

Diğer bir deyişle, denetimli bağlamdaki Özellik seçimi yöntemlerinin yaygın olarak uygulanan bir kategorisi, "filtre tabanlı özellik seçimi" olarak adlandırılır. Her özellik ve hedef öznitelik arasındaki bağıntıyı değerlendirerek, bu yöntemler her özelliğe puan atamak için istatistiksel bir ölçü uygular. Özellikler daha sonra puanla derecelendirilir ve belirli bir özelliği tutma veya ortadan kaldırma eşiğini ayarlamaya yardımcı olmak için kullanılabilir. Bu yöntemlerde kullanılan istatistiksel ölçülere örnek olarak kişi bağıntı, karşılıklı bilgi ve kikare test dahildir.

Azure Machine Learning Studio, özellik seçimi için belirtilen modüller vardır. Aşağıdaki şekilde gösterildiği gibi, bu modüller [filtre tabanlı özellik seçimi][filter-based-feature-selection] ve [Fisher doğrusal ayrılmış minant analizini][fisher-linear-discriminant-analysis]içerir.

![Özellik seçimi modülleri](./media/select-features/feature-Selection.png)

Örneğin, [filtre tabanlı özellik seçimi][filter-based-feature-selection] modülünün kullanımını göz önünde bulundurun. Kolaylık sağlaması için metin araştırma örneğini kullanmaya devam edin. [Özellik karma][feature-hashing] modülü aracılığıyla bir dizi 256 Özellik oluşturulduktan sonra bir regresyon modeli oluşturmak istediğinizi ve yanıt değişkeninin 1 ile 5 arasında defter İnceleme derecelendirmeleri Içeren "Sütun1" olduğunu varsayalım. "Özellik Puanlama yöntemi" ni "Pearson bağıntı", "hedef sütun" i "Sütun1" ve "istenen özellik sayısı" olarak 50 olarak ayarlayarak. Ardından modül [filtresi tabanlı özellik seçimi][filter-based-feature-selection] , "Sütun1" hedef özniteliğiyle birlikte 50 özellikleri içeren bir veri kümesi oluşturur. Aşağıdaki şekilde, bu denemenin ve giriş parametrelerinin akışı gösterilmektedir:

![Filter-Based Özellik seçimi modül özellikleri](./media/select-features/feature-Selection1.png)

Aşağıdaki şekilde, elde edilen veri kümeleri gösterilmektedir:

![Filtre tabanlı özellik seçimi modülü için sonuç veri kümesi](./media/select-features/feature-Selection2.png)

Her özellik, kendisini ve "Sütun1" hedef özniteliği arasındaki Pearson bağıntı temelinde puanlanır. En üst puanları olan özellikler tutulur.

Seçilen özelliklerin karşılık gelen puanları aşağıdaki şekilde gösterilmiştir:

![Filtre tabanlı özellik seçimi modülünün puanları](./media/select-features/feature-Selection3.png)

Bu [filtre tabanlı özellik seçimi][filter-based-feature-selection] modülünü uygulayarak, "Pearson bağıntı" Puanlama metoduna bağlı olarak "Sütun1" hedef değişkeni ile en çok bağıntılı özelliklere sahip olduklarından 50 'den 256 tanesi seçilidir.

## <a name="conclusion"></a>Sonuç
Özellik Mühendisliği ve Özellik seçimi, yaygın olarak oluşturulan iki özelliktir ve seçilen özellikler, veride bulunan anahtar bilgilerini çıkarmaya deneyen Eğitim sürecinin verimliliğini artırır. Ayrıca, giriş verilerini doğru şekilde sınıflandırmak ve daha fazla robustly hakkında daha fazla bilgi almak için bu modellerin gücünden de gelişir. Daha ayrıntılı bilgi edinmek için özellik Mühendisliği ve seçimi de birleştirebilir. Bu, bir modeli ayarlamak veya eğitme için gereken özellik sayısını artırır ve azaltır. Matematik olarak konuşma, modeli eğitme için seçilen özellikler, verilerdeki desenleri açıklayan ve sonra sonuçları başarıyla tahmin eden en düşük bağımsız değişkenler kümesidir.

Her zaman özellik Mühendisliği veya özellik seçimi yapmak gerekmez. Gerekli olup olmadığı, toplanan verilere, seçilen algoritmaya ve denemenin hedefine bağlıdır.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis