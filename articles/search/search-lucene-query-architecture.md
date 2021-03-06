---
title: Tam metin sorgusu ve dizin oluşturma altyapısı mimarisi (Lucene)
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama ilgili olarak, tam metin araması için Lucene sorgu işleme ve belge alımı kavramlarını inceler.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002757"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Azure Bilişsel Arama 'de tam metin araması

Bu makale, Lucene tam metin aramasının Azure Bilişsel Arama nasıl çalıştığını daha ayrıntılı olarak anlayabilmek isteyen geliştiricilere yöneliktir. Metin sorguları için, Azure Bilişsel Arama çoğu senaryoda beklenen sonuçları hiç sorunsuz verecektir ama bazen biraz "konu dışı" görünen sonuçlar alabilirsiniz. Böyle durumlarda Lucene sorgu yürütmesinin dört aşaması (sorgu ayrıştırma, sözcük analizi, belge eşleştirme, puanlama) hakkında arka plan bilgilerine sahip olmak, istenen sonucu verecek belirli sorgu parametresi değişikliklerini ve dizin yapılandırmasını belirlemenize yardımcı olabilir. 

> [!Note] 
> Azure Bilişsel Arama tam metin araması için Lucene kullanır, ancak Lucene tümleştirmesi ayrıntılı değildir. Azure Bilişsel Arama için önemli olan senaryoları etkinleştirmek üzere Lucene işlevselliğini seçmeli olarak kullanıma sunar ve genişlettik. 

## <a name="architecture-overview-and-diagram"></a>Mimariye genel bakış ve diyagram

Tam metin arama sorgusunun işlenmesi, arama terimlerini ayıklamak üzere sorgu metnini ayrıştırmaya başlar. Arama altyapısı, eşleşen koşullara sahip belgeleri almak için bir dizin kullanır. Tek tek sorgu terimleri bazen bölünür ve yeni formlara reconstituted, bu da olası bir eşleşme olarak ele alınabilecek daha geniş bir net. Daha sonra bir sonuç kümesi, eşleşen her bir belgeye atanan bir ilgi puanına göre sıralanır. Derecelendirilen listenin en üstünde bulunanlar çağıran uygulamaya döndürülür.

Yeniden oluşturuldu, sorgu yürütme dört aşamaya sahiptir: 

1. Sorgu ayrıştırma 
2. Sözcük temelli analiz 
3. Belge alımı 
4. Puanlama 

Aşağıdaki diyagramda, bir arama isteğini işlemek için kullanılan bileşenler gösterilmektedir. 

 ![Azure Bilişsel Arama Lucene sorgu mimarisi diyagramı][1]


| Başlıca bileşenler | İşlevsel açıklama | 
|----------------|------------------------|
|**Sorgu Çözümleyicileri** | Sorgu işleçlerinden sorgu koşullarını ayırın ve arama altyapısına gönderilmek üzere sorgu yapısını (bir sorgu ağacı) oluşturun. |
|**Çözümleyiciler** | Sorgu koşullarında sözcük temelli analiz gerçekleştirin. Bu işlem, sorgu terimlerini dönüştürmeyi, kaldırmayı veya genişletmeyi içerebilir. |
|**Dizin oluşturma** | Dizinli belgelerden ayıklanan aranabilir terimleri depolamak ve düzenlemek için kullanılan etkili bir veri yapısı. |
|**Arama altyapısı** | Tersine çevrilmiş dizinin içeriğine göre eşleşen belgeleri alır ve puanlar. |

## <a name="anatomy-of-a-search-request"></a>Arama isteğinin anatomisi

Arama isteği, bir sonuç kümesinde döndürülmelidir öğesinin tüm bir belirtimidir. En basit biçimde, her türlü ölçütü olmayan boş bir sorgudur. Daha gerçekçi bir örnek, muhtemelen bir filtre ifadesi ve sıralama kurallarıyla belirli alanlara kapsamlı parametreler, birkaç sorgu terimi içerir.  

Aşağıdaki örnek, [REST API](/rest/api/searchservice/search-documents)kullanarak Azure bilişsel arama 'e gönderebilecek bir arama isteğidir.  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

Bu istek için arama motoru şunları yapar:

1. Fiyatın en az $60 ve $300 ' den küçük olduğu belgeleri filtreler.
2. Sorguyu yürütür. Bu örnekte, arama sorgusu tümcecik ve terimlerden oluşur: `"Spacious, air-condition* +\"Ocean view\""` (kullanıcılar genellikle noktalama işareti girmez, ancak örnek eklemek, çözümleyiciler onu nasıl işleyeceğinizi anlamamızı sağlar). Bu sorgu için arama altyapısı, `searchFields` "okyanus görünümü" ni içeren belgeler için ' de belirtilen Açıklama ve başlık alanlarını ve ek olarak "spacemi" ya da "AIR-Condition" önekiyle başlayan koşulları tarar. `searchMode`Parametresi, bir terimin açıkça gerekli olmadığı durumlarda (varsayılan) veya tümü için herhangi bir dönem (varsayılan) veya hepsi ile eşleştirmek için kullanılır `+` .
3. Elde edilen otel kümesini, belirli bir Coğrafya konumuna yakınlığa göre sıralar ve ardından çağıran uygulamaya geri döner. 

Bu makalenin çoğu, *arama sorgusunun* işlenmesiyle ilgilidir: `"Spacious, air-condition* +\"Ocean view\""` . Filtreleme ve sıralama kapsam dışında. Daha fazla bilgi için bkz. [Arama API başvurusu belgeleri](/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>1. Aşama: sorgu ayrıştırma 

Belirtildiği gibi, sorgu dizesi isteğin ilk satırdır: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

Sorgu ayrıştırıcısı, işleçleri ( `*` `+` Örneğin, ve örneğinde) arama terimlerinden ayırır ve arama sorgusunu desteklenen bir türün alt *sorguları* olarak kaldırır: 

+ tek başına terimler için *terim sorgusu* (spacemlike gibi)
+ alıntı yapılan terimler için *tümcecik sorgusu* (okyanus görünümü gibi)
+ bir önek işleci (Air koşulu gibi) tarafından izlenen terimler için *ön ek sorgusu* `*`

Desteklenen sorgu türlerinin tam listesi için bkz. [Lucene sorgu söz dizimi](/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Bir alt sorgu ile İlişkili işleçler, bir belgenin eşleşme olarak kabul edilmesi için "olması gereken" veya "olması" gerektiğini belirtir. Örneğin, `+"Ocean view"` işleci nedeniyle "gerekir" `+` . 

Sorgu ayrıştırıcısı, arama motoruna geçiş yaptığı bir *sorgu ağacında* (sorguyu temsil eden bir iç yapı) alt sorguları yeniden yapılandırır. Sorgu ayrıştırma işlevinin ilk aşamasında, sorgu ağacı şuna benzer.  

 ![Boolean sorgu searchmode any][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Desteklenen çözümleyiciler: Simple ve Full Lucene 

 Azure Bilişsel Arama, iki farklı sorgu dili sunar, `simple` (varsayılan) ve `full` . `queryType`Parametresini arama isteğinizle birlikte ayarlayarak, sorgu ayrıştırıcısına, işleç ve sözdiziminin nasıl yorumlanacağını anlayabilmesi için hangi sorgu dilini istediğinizi söyleirsiniz. [Basit sorgu dili](/rest/api/searchservice/simple-query-syntax-in-azure-search) sezgisel ve sağlam olduğundan, genellikle kullanıcı girişini istemci tarafı işleme olmadan olduğu gibi yorumlamak için uygundur. Web araması altyapılarından tanıdık gelen sorgu işleçlerini destekler. Ayarla [](/rest/api/searchservice/lucene-query-syntax-in-azure-search), `queryType=full` benzer, Regex ve alan kapsamlı sorgular gibi daha fazla işleç ve sorgu türü için destek ekleyerek varsayılan basit sorgu dilini genişleterek, bu ayarı yaparak alacağınız tam Lucene sorgu dili. Örneğin, basit sorgu sözdiziminde gönderilen normal ifade bir ifade değil sorgu dizesi olarak yorumlanır. Bu makaledeki örnek istek, tam Lucene sorgu dilini kullanır.

### <a name="impact-of-searchmode-on-the-parser"></a>Ayrıştırıcıda searchMode etkisi 

Ayrıştırmayı etkileyen başka bir arama isteği parametresi `searchMode` parametresi. Boolean sorguları için varsayılan işleci denetler: Any (varsayılan) veya ALL.  

Ne zaman `searchMode=any` , varsayılan olarak, spacemli ve hava durumu arasındaki boşluk SıNıRLAYıCıSı veya ( `||` ) olduğunda, örnek sorgu metnini ile eşdeğer hale getirme: 

```
Spacious,||air-condition*+"Ocean view" 
```

İçindeki gibi açık işleçler, `+` `+"Ocean view"` Boole sorgu oluşturma (terimi eşleşmelidir) için net değildir.  Daha az belirgin, kalan koşulları yorumlama: spacve Hava durumu gibi. Arama altyapısının okyanus görünümü *ve* spacemli *ve* Hava durumu ile eşleşmeleri bulması gerekir mi? Ya da okyanus görünümü ve kalan terimlerden *birini* bulmalıdır mi? 

Varsayılan olarak ( `searchMode=any` ), arama motoru daha geniş yorumu kabul eder. Her iki alanın de eşleşmesi, yansıtılırken "veya" semantiğinin olması *gerekir* . Daha önce gösterilen ilk sorgu ağacı, iki "i" işlemi ile, varsayılan olarak gösterilir.  

Şimdi belirlediğimiz hakkında düşünün `searchMode=all` . Bu durumda, alan "ve" işlemi olarak yorumlanır. Kalan koşulların her ikisi de eşleşme olarak nitelendirmek için belgede bulunmalıdır. Elde edilen örnek sorgu şu şekilde yorumlanacaktır: 

```
+Spacious,+air-condition*+"Ocean view"
```

Bu sorgu için değiştirilen bir sorgu ağacı, eşleşen bir belge üç alt sorgunun kesişimi olan aşağıdaki gibi olacaktır: 

 ![Boolean sorgusu searchmode tümü][3]

> [!Note] 
> Üzerinde seçim yapmak `searchMode=any` `searchMode=all` , temsilci sorguları çalıştırarak en iyi şekilde ulaşan bir karardır. İşleçleri içermesi muhtemel olabilecek kullanıcılar (belge depoları aranırken ortak), `searchMode=all` Boole sorgu yapılarını bilgilendirir, sonuçları daha sezgisel bulabilir. Ve işleçleri arasında karşılıklı yürütme hakkında daha fazla bilgi için `searchMode` bkz. [basit sorgu söz dizimi](/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>2. Aşama: sözcük Analizi 

Sözcük temelli çözümleyiciler, sorgu ağacı yapılandırıldıktan sonra *terim sorgularını* ve *tümcecik sorgularını* işler. Çözümleyici, ayrıştırıcının kendisine verilen metin girdilerini kabul eder, metni işler ve sonra, belirteç oluşturma koşullarını sorgu ağacına dahil edilecek şekilde geri gönderir. 

En yaygın sözcük Analizi analizi, sorgu koşullarını belirli bir dile özgü kurallara göre dönüştüren *dilsel analizler* : 

* Bir sorgu terimini bir sözcüğün kök biçiminde azaltma 
* Gerekli olmayan sözcükleri kaldırma (Ingilizce 'de "The" veya "ve" gibi stopwords) 
* Bileşik sözcüğü bileşen bölümlerine bölme 
* Büyük harfli bir sözcüğün küçük harfleri 

Bu işlemlerin hepsi, Kullanıcı tarafından girilen metin girişi ve dizinde depolanan koşullar arasındaki farkları silme eğilimindedir. Bu gibi işlemler metin işlemenin ötesine geçer ve dilin kendisi hakkında ayrıntılı bilgi ister. Bu dil tanıma katmanını eklemek için Azure Bilişsel Arama, hem Lucene hem de Microsoft 'tan gelen [dil Çözümleyicileri](/rest/api/searchservice/language-support) 'nin uzun listesini destekler.

> [!Note]
> Çözümleme gereksinimleri, senaryonuza bağlı olarak en az düzeyde farklılık açabilir. Önceden tanımlanmış çözümleyiciler arasından birini seçerek veya kendi [özel çözümleyicinizi](/rest/api/searchservice/Custom-analyzers-in-Azure-Search)oluşturarak, sözlü çözümlemenin karmaşıklığını denetleyebilirsiniz. Çözümleyiciler aranabilir alanlara kapsamlandırılır ve bir alan tanımının parçası olarak belirtilir. Bu, alan temelinde sözcük temelli analizleri değiştirmenize olanak sağlar. Belirtilmemiş, *Standart* Lucene Çözümleyicisi kullanılır.

Bizim örneğimizde, ilk sorgu ağacı, büyük bir "S" ve sorgu ayrıştırıcısının sorgu teriminin bir parçası olarak yorumladığı bir virgülle (bir virgül sorgu dili işleci olarak kabul edilmez) "Spacmerak" terimini içerir.  

Varsayılan çözümleyici terimi işlediğinde, küçük harfli "okyanus görünümü" ve "spacemleri" olarak değişir ve virgül karakterini kaldırır. Değiştirilen sorgu ağacı şöyle görünür: 

 ![Analiz edilen koşullara sahip Boole sorgusu][4]

### <a name="testing-analyzer-behaviors"></a>Çözümleyici davranışlarını test etme 

Bir çözümleyicinin davranışı [Çözümle API 'si](/rest/api/searchservice/test-analyzer)kullanılarak test edilebilir. Hangi koşulların hangi koşullara göre oluşturulacağını görmek için çözümlemek istediğiniz metni sağlayın. Örneğin, standart çözümleyici 'nin "AIR-Condition" metnini nasıl işleyeceğini görmek için, aşağıdaki isteği verebilirsiniz:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

Standart çözümleyici, giriş metnini aşağıdaki iki belirtece ayırır, bunlara başlangıç ve bitiş uzaklıkları (isabet vurgulama için kullanılır) ve konumlarına (tümcecik eşleştirmesi için kullanılır) gibi özniteliklere açıklama ekleyin:

```json
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Sözcük temelli Analize özel durumlar 

Sözcük temelli analiz yalnızca, bir terim sorgusu veya bir tümcecik sorgusu için yalnızca tüm terimleri gerektiren sorgu türleri için geçerlidir. Eksik terimlere sahip sorgu türleri – ön ek sorgusu, joker karakter sorgusu, Regex sorgusu veya benzer bir sorguya uygulanmaz. Örneğimizde terim içeren önek sorgusu da dahil olmak üzere bu sorgu türleri `air-condition*` doğrudan sorgu ağacına eklenir, analiz aşaması atlanarak yapılır. Bu türlerin sorgu koşullarında gerçekleştirilen tek dönüşüm küçük harfe göre yapılır.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>3. Aşama: belge alımı 

Belge alımı, dizinde eşleşen koşullara sahip belgeleri bulmayı gösterir. Bu aşama bir örnek aracılığıyla en iyi şekilde anlaşıldı. Aşağıdaki basit şemaya sahip bir oteller diziniyle başlayalım: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Bu dizinin aşağıdaki dört belgeyi içerdiğini varsayalım: 

```json
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
```

**Terimlerin dizini oluşturma**

Alımı anlamak için dizin oluşturma hakkında birkaç temel bilgi sağlamaya yardımcı olur. Depolama birimi, her aranabilir alan için bir ters bir dizindir. Ters bir dizin içinde tüm belgelerden alınan tüm koşulların sıralanmış bir listesidir. Her bir terim, aşağıdaki örnekte olduğu gibi, oluştuğu belge listesi ile eşlenir.

Ters bir dizindeki koşulları oluşturmak için, arama motoru, sorgu işleme sırasında ne olduğu gibi, belgelerin içeriği üzerinde sözlü analiz gerçekleştirir:

1. *Metin girişleri* , çözümleyici yapılandırmasına bağlı olarak, bir çözümleyici 'ye, daha düşük noktalama işaretlerine ve bu şekilde çıkarılır. 
2. *Belirteçler* , sözlü çözümlemenin çıktılardır.
3. *Koşullar* dizine eklenir.

Bu, arama ve dizin oluşturma işlemlerinde aynı Çözümleyicileri kullanmak için yaygın, ancak gerekli değildir, bu sayede sorgu terimleri dizin içinde terimler gibi görünür.

> [!Note]
> Azure Bilişsel Arama, dizin oluşturma ve ek `indexAnalyzer` ve alan parametreleri aracılığıyla arama için farklı çözümleyiciler belirtmenize olanak tanır `searchAnalyzer` . Belirtilmemişse, özelliği ile ayarlanan çözümleyici, `analyzer` hem dizin oluşturma hem de arama için kullanılır.  

**Örnek belgeler için ters dizin**

Örneğimize dönerek, **başlık** alanı için ters dizin şöyle görünür:

| Süre | Belge listesi |
|------|---------------|
| atman | 1 |
| unun | 2 |
| Otel | 1, 3 |
| Hint | 4  |
| Playa | 3 |
| çare | 3 |
| çekilme | 4 |

Başlık alanında, yalnızca *otel* iki belgede görünür: 1, 3.

**Açıklama** alanı için dizin aşağıdaki gibidir:

| Süre | Belge listesi |
|------|---------------|
| te | 3
| ve | 4
| unun | 1
| Koşullu | 3
| rahatlıkla | 3
| Uzaklık | 1
| Adası | 2
| Kaua ʻ ı | 2
| kutusunun | 2
| north | 2
| Hint | 1, 2, 3
| / | 2
| on |2
| sess | 4
| Odaları  | 1, 3
| bölümluded | 4
| kısa bir | 2
| spacmerak | 1
| şunu | 1, 2
| kullanıcısı | 1
| görüntüle | 1, 2, 3
| İzlenecek | 1
| örneklerini şununla değiştirin: | 3


**Dizinli koşullara göre sorgu koşullarını eşleştirme**

Yukarıdaki ters dizinler verildiğinde, örnek sorguya geri dönelim ve örnek sorgumuz için eşleşen belgelerin nasıl bulunduğumuz hakkında bilgi verlim. Son sorgu ağacının şöyle göründüğünü hatırlayın: 

 ![Analiz edilen koşullara sahip Boole sorgusu][4]

Sorgu yürütme sırasında, tekil sorgular bağımsız olarak aranabilir alanlara göre yürütülür. 

+ "Spacmerak" TermQuery, belge 1 (otel atman) ile eşleşir. 

+ "AIR-Condition *" PrefixQuery, herhangi bir belgeyle eşleşmez. 

  Bu, bazen geliştiricilerin kullandığı bir davranıştır. Hava terimi belgede mevcut olsa da, varsayılan çözümleyici tarafından iki terime bölünür. Kısmi terimler içeren ön ek sorgularının çözümlenmez olduğunu hatırlayın. Bu nedenle, "AIR-Condition" ön ekine sahip terimler ters çevrilmiş dizinde aranabilir ve bulunamadı.

+ PhraseQuery, "okyanus görünümü", "okyanus" ve "Görünüm" terimlerini arar ve özgün belgedeki koşulların yakınlığını denetler. Belgeler 1, 2 ve 3 ' ü Açıklama alanında bu sorguyla eşleştirin. Bildirim belgesi 4 ' te, başlık içinde okyanus terimi bulunur, ancak tek sözcükler yerine "okyanus görünümü" ifadesini aradığımız için eşleşme olarak kabul edilmez. 

> [!Note]
> Arama sorgusu, parametre ile ayarlanan alanları `searchFields` , örnek arama isteğinde gösterildiği gibi sınırlandırmadığınız sürece Azure bilişsel arama dizinindeki tüm aranabilir alanlara göre bağımsız olarak yürütülür. Seçili alanlardan herhangi biri ile eşleşen belgeler döndürülür. 

Tüm sorgu için, söz konusu sorgu için, eşleşen belgeler 1, 2, 3 ' dir. 

## <a name="stage-4-scoring"></a>4. Aşama: Puanlama  

Bir arama sonuç kümesindeki her belgeye bir ilgi puanı atanır. İlgi puanının işlevi, arama sorgusuna göre ifade edilen bir Kullanıcı sorusuna en iyi şekilde yanıt veren belgelerin daha yüksek bir şekilde derecelendirmesi. Puan, eşleşen koşulların istatistiksel özelliklerine göre hesaplanır. Puanlama formülünün temel tarafında [tf/ıDF (terim sıklığı-ters belge sıklığı)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Nadir ve yaygın terimleri içeren sorgularda, TF/ıDF nadir terimi içeren sonuçları yükseltir. Örneğin, *Başkan* sorgusu ile eşleşen belgelerden, tüm vikipli makalelerdeki bir kuramsal dizinde, *Başkan* ile eşleşen belgeler *,* ile eşleşen belgelerden daha ilgili olarak değerlendirilir.


### <a name="scoring-example"></a>Puanlama örneği

Örnek sorgumız ile eşleşen üç belgeyi geri çekin:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
```

Belge 1, sorgu en iyi şekilde eşleştiğinden, hem *terimi hem de gerekli* tümcecik *görünümü* Açıklama alanında gerçekleştiğinden sorgu en iyi şekilde eşleşti. Sonraki iki belge yalnızca tümcecik *okyanus görünümüyle* eşleşir. Belge 2 ve 3 ' ün ilgi puanı, sorguyla aynı şekilde eşleştirildiği halde farklı olduğunu ortaya çıkarmış olabilir. Bunun nedeni, Puanlama formülünün yalnızca TF/ıDF 'den daha fazla bileşene sahip olmasından kaynaklanır. Bu durumda, açıklama daha kısa olduğundan belge 3 ' te biraz daha yüksek bir puan atandı. Alan uzunluğu ve diğer faktörlerin ilgi Puanını nasıl etkileyebileceğini anlamak için [Lucene 'In pratik Puanlama formülü](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) hakkında bilgi edinin.

Bazı sorgu türleri (joker karakter, ön ek, Regex) her zaman genel belge puanına bir sabit puanı katkıda bulunur. Bu, sorgu genişletmesi aracılığıyla bulunan eşleşmelerin sonuçlara dahil edilmesini sağlar, ancak derecelendirmeyi etkilemeksizin. 

Bunun ne kadar önemli olduğunu gösteren bir örnek. Önek aramaları dahil olmak üzere joker karakter aramaları tanıma göre belirsizdir, çünkü giriş çok büyük sayıda farklı terimlerde olası eşleşmelerin bulunduğu kısmi bir dizedir ("Tur", "touektes" ve "Tourmaline") ile eşleşen bir girişi olan Bu sonuçların doğası göz önüne alındığında, hangi koşulların diğerlerinden daha değerli olduğunu makul bir şekilde çıkarmanın bir yolu yoktur. Bu nedenle, Puanlama joker karakter, ön ek ve normal ifade türündeki sorgularda sonuçladığı zaman terim sıklıklarını yok saydık. Kısmi ve tam koşulları içeren çok parçalı bir arama isteğinde, kısmi girdinin sonuçları, beklenmedik bir şekilde, beklenmeyen eşleşmelerin önüne geçmek için sabit bir puana eklenir.

### <a name="score-tuning"></a>Puan ayarlama

Azure Bilişsel Arama ilgi puanlarını ayarlamaya yönelik iki yol vardır:

1. **Puanlama profilleri** , bir dizi kurala göre dereceli sonuçlar listesindeki belgeleri yükseltir. Örneğimizde, başlık alanında, açıklama alanında eşleşen belgelerden daha alakalı olan belgeleri kabul eteceğiz. Ayrıca, dizinimizin her otel için bir fiyat alanı varsa, belgeleri daha düşük fiyatla yükseltebiliriz. [Arama dizinine Puanlama profilleri ekleme](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) hakkında daha fazla bilgi edinin.
2. **Terim arttırma** (yalnızca tam Lucene sorgu sözdiziminde kullanılabilir) `^` , sorgu ağacının herhangi bir bölümüne uygulanabilen bir artırma işleci sağlar. Örneğimizde, *Hava durumu ön koşulunu* aramak yerine \* bir tane, *Uçak koşulunun* veya ön koşulun tam terimini arayabilir, ancak tam terimle eşleşen belgeler, sorgu teriminin yükselmesine uygulanarak daha yüksek bir şekilde derecelendirilir: * Air-condition ^ 2 | | Hava durumu * *. [Terim artırma](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost)hakkında daha fazla bilgi edinin.


### <a name="scoring-in-a-distributed-index"></a>Dağıtılmış dizindeki Puanlama

Azure Bilişsel Arama 'deki tüm dizinler otomatik olarak birden çok parçaya bölünür ve bu da hizmet ölçeği artırma veya azaltma sırasında dizini birden çok düğüm arasında hızlıca dağıtmamızı sağlar. Bir arama isteği verildiğinde, her parçaya bağımsız olarak verilir. Her parçanın sonuçları daha sonra birleştirilir ve puana göre sıralanır (başka bir sıralama tanımlanmazsa). Puanlama işlevinin sorgu dönemi sıklığını, tüm parçalar arasında değil, parçadaki tüm belgelerde ters belge sıklığıyla karşılaştırdığından emin olmak önemlidir!

Bu, farklı parçalar üzerinde bulunduklarında aynı belgeler için bir *uygunluk puanı farklı* olabilir. Neyse ki, bu tür farklılıklar, daha fazla terim dağıtımı nedeniyle dizindeki belge sayısı büyüdükçe kaybolmaya eğilimlidir. Verilen herhangi bir belgeyi hangi parçadan yerleştirilebileceğini varsaymak mümkün değildir. Ancak, bir belge anahtarının değişmediğini varsayarsak, her zaman aynı parçaya atanır.

Genel olarak, sipariş kararlılığı önemli olursa belge puanı belgeleri sıralamak için en iyi öznitelik değildir. Örneğin, aynı puanı taşıyan iki belge verildiğinde, ilk olarak aynı sorgunun sonraki çalıştırmalarda görünen bir garanti yoktur. Belge puanı, sonuç kümesindeki diğer belgelere göre yalnızca genel bir anlamlı fikir vermelidir.

## <a name="conclusion"></a>Sonuç

Internet arama altyapısının başarısı, özel veriler üzerinde tam metin araması için beklentiler oluşturdu. Neredeyse her türlü arama deneyimi için, şartlar yanlış yazıldığında veya tamamlanmadığında bile altyapının amacımızı anlaması beklenmektedir. Şimdiye kadar hiç belirttiğimiz eşdeğer terimleri veya eş anlamlıları temel alan eşleşmeler de bekleyebilir.

Teknik açıdan, tam metin araması, gelişmiş dil analizi ve ilgili bir sonucu teslim etmek üzere sorgu şartlarını gösteren, genişleterek ve dönüştüren yollarla işlemek için önemli bir yaklaşım gerektiren çok karmaşıktır. Devralınan karmaşıklıklar verildiğinde, bir sorgunun sonucunu etkileyebilecek birçok etken vardır. Bu nedenle, tam metin aramasının mekanizması anlamak için harcanan süreyi, beklenmeyen sonuçlarla çalışmaya çalışırken somut avantajlar sağlar.  

Bu makale, Azure Bilişsel Arama bağlamında tam metin aramasını araştırmakta. Sık karşılaşılan sorgu sorunlarını gidermeye yönelik olası nedenleri ve çözümleri tanımak için size yeterli bir arka plan sunabiliyoruz. 

## <a name="next-steps"></a>Sonraki adımlar

+ Örnek dizini oluşturun, farklı sorgular deneyin ve sonuçları gözden geçirin. Yönergeler için bkz. [portalda dizin oluşturma ve sorgulama](search-get-started-portal.md#query-index).

+ [Belgeleri ara](/rest/api/searchservice/search-documents#bkmk_examples) örnek bölümünde veya portalda arama Gezgini 'ndeki [basit sorgu söz diziminde](/rest/api/searchservice/simple-query-syntax-in-azure-search) ek sorgu söz dizimini deneyin.

+ Arama uygulamanızda derecelendirme ayarlamak istiyorsanız [Puanlama profillerini](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) gözden geçirin.

+ [Dile özgü sözcük Çözümleyicileri çözümleyicilerinin](/rest/api/searchservice/language-support)nasıl uygulanacağını öğrenin.

+ [Özel Çözümleyicileri](/rest/api/searchservice/custom-analyzers-in-azure-search) , belirli alanlarda en az işlem veya özel Işleme için yapılandırın.

## <a name="see-also"></a>Ayrıca bkz.

[Belgelerde Arama REST API'si](/rest/api/searchservice/search-documents) 

[Basit sorgu söz dizimi](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Tam Lucene sorgu söz dizimi](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Arama sonuçlarını işleme](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png