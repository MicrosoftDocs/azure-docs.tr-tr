---
title: Team Data Science Işlem yaşam döngüsü
description: Ekip veri bilimi Işlemi (TDSP), veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz, önerilen bir yaşam döngüsü sağlar.
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
ms.openlocfilehash: ed54fda744978ac43bbffc6a70d6b331e85dc5e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305605"
---
# <a name="the-team-data-science-process-lifecycle"></a>Team Data Science Işlem yaşam döngüsü

Ekip veri bilimi Işlemi (TDSP), veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz, önerilen bir yaşam döngüsü sağlar. Yaşam döngüsü, başarılı projelerin izlediği adımların tamamını özetler. Veri madenciliği için platformlar arası standart Işlem [(net DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), veritabanlarında bilgi bulma [(kdd)](https://wikipedia.org/wiki/Data_mining#Process)veya kuruluşunuzun kendi özel süreci gibi başka bir veri bilimi yaşam döngüsü kullanıyorsanız, görev tabanlı TDSP 'yi kullanmaya devam edebilirsiniz. 

Bu yaşam döngüsü, akıllı uygulamaların bir parçası olarak teslim etmek üzere tasarlanan veri bilimi projeleri için tasarlanmıştır. Bu uygulamalar, tahmine dayalı analiz için makine öğrenimi veya yapay zeka modelleri dağıtır. Araştırmacı veri bilimi projeleri ve improvised Analytics projeleri, bu işlemin kullanımıyla de faydalanabilir. Ancak bu projeler için burada açıklanan adımlardan bazıları gerekmeyebilir. 

## <a name="five-lifecycle-stages"></a>Beş yaşam döngüsü aşaması

TDSP yaşam döngüsü tekrarlayarak yürütülen beş ana aşamadan oluşur. Bu aşamalar şunları içerir:

   1. [Kurumsal yaklaşım](lifecycle-business-understanding.md)
   2. [Veri edinme ve anlama](lifecycle-data.md)
   3. [Modelleme](lifecycle-modeling.md)
   4. [Dağıtım](lifecycle-deployment.md)
   5. [Müşteri kabulü](lifecycle-acceptance.md)

TDSP yaşam döngüsünün görsel bir gösterimi aşağıda verilmiştir: 

![TDSP yaşam döngüsü](./media/lifecycle/tdsp-lifecycle2.png) 


TDSP yaşam döngüsü, tahmine dayalı modeller kullanmak için gereken görevlere rehberlik sağlayan bir yinelenmemiş adım dizisi olarak modellenir. Tahmine dayalı modelleri, akıllı uygulamalar oluşturmak için kullanmayı planladığınız üretim ortamında dağıtırsınız. Bu işlem yaşam döngüsünün amacı, bir veri bilimi projesini açık bir katılım uç noktasına taşımaya devam olmaktır. Veri bilimi, araştırma ve bulma konusunda bir uygulamadır. Standartlaştırılmış şablonlar kullanan iyi tanımlanmış bir yapıt kümesi kullanarak, görevleri takımınızla ve müşterilerinizle iletişim kurma özelliği, yanlış anlayışları önlemeye yardımcı olur. Bu şablonların kullanılması, karmaşık bir veri bilimi projesinin başarıyla tamamlanmasının olasılığını da artırır.

Her aşamada aşağıdaki bilgileri sağlıyoruz:

   * **Hedefler**: belirli amaçlar.
   * **Nasıl yapılır**: belirli görevlerin bir özeti ve bunların nasıl tamamlanacağı hakkında rehberlik.
   * **Yapıtlar**: teslim edilebilirler ve bunları oluşturmak için destek.

## <a name="next-steps"></a>Sonraki adımlar

Belirli senaryolar için işlemdeki tüm adımları gösteren eksiksiz bir uçtan uca izlenecek yol sunuyoruz. [Örnek yönergeler](walkthroughs.md) makalesi, bağlantılar ve küçük resim açıklamaları olan senaryoların bir listesini sağlar. İzlenecek yollar, akıllı bir uygulama oluşturmak için bulut, şirket içi araçların ve hizmetlerin bir iş akışı veya işlem hattına nasıl birleştirileceğini gösterir. 

Azure Machine Learning Studio kullanan TDSPs 'de adımların nasıl yürütüleceği hakkında örnekler için, bkz. [Azure Machine Learning Ile TDSP kullanma](./index.yml).