---
title: OData ifade söz dizimi başvurusu
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgularında OData ifadeleri için biçimsel dilbilgisi ve sözdizimi belirtimi.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 83e9ae4aa68bc0c819d02a0cc6c39758549811cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88928862"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Azure Bilişsel Arama için OData ifadesi söz dizimi başvurusu

Azure Bilişsel Arama, API 'de [OData ifadelerini](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) parametre olarak kullanır. En yaygın olarak, OData ifadeleri `$orderby` ve parametreleri için kullanılır `$filter` . Bu ifadeler, birden çok yan tümce, işlev ve işleç içeren karmaşık olabilir. Ancak, Azure Bilişsel Arama REST API birçok bölümünde Özellik yolları gibi basit OData ifadeleri de kullanılır. Örneğin, yol ifadeleri, API 'de alt [alanları, bir](index-add-suggesters.md) [Puanlama işlevini](index-add-scoring-profiles.md), parametreyi, veya/1 & gt `$select` [sorgularında ara](query-lucene-syntax.md)alanları listelemek gibi karmaşık alanların alt alanlarına başvurmak için kullanılır.

Bu makalede, bir biçimsel dilbilgisi kullanılarak bu OData ifadelerinin tüm biçimleri açıklanmaktadır. Ayrıca, dilbilgisi görsel olarak araştırmanıza yardımcı olacak [etkileşimli bir diyagram](#syntax-diagram) de vardır.

## <a name="formal-grammar"></a>Biçimsel dilbilgisi

Azure Bilişsel Arama tarafından desteklenen bir EBNF ([genişletilmiş Backus-Naur formu](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) dilbilgisi kullanarak desteklenen OData dilinin alt kümesini açıklayabilir. Kurallar, en karmaşık ifadelerle başlayarak "yukarıdan aşağı" listelenir ve bunları daha basit ifadelere bölmek için kullanılır. En üstte Azure Bilişsel Arama REST API özgü parametrelere karşılık gelen dilbilgisi kuralları:

- [`$filter`](search-query-odata-filter.md), kural tarafından tanımlanır `filter_expression` .
- [`$orderby`](search-query-odata-orderby.md), kural tarafından tanımlanır `order_by_expression` .
- [`$select`](search-query-odata-select.md), kural tarafından tanımlanır `select_expression` .
- Kural tarafından tanımlanan alan yolları `field_path` . Alan yolları API 'nin tamamında kullanılır. Bir dizinin en üst düzey alanlarına veya bir ya da daha fazla [karmaşık alan](search-howto-complex-data-types.md) öncülerine sahip alt alanlara başvurabilirler.

EBNF, dilbilgisi ve kuralları arasındaki ilişkileri etkileşimli bir şekilde keşfetmenizi sağlayan gözatılabilir bir [sözdizimi diyagramıdır](https://en.wikipedia.org/wiki/Syntax_diagram) .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Sözdizimi diyagramı

Azure Bilişsel Arama tarafından desteklenen OData dil dilbilgisini görsel olarak araştırmak için etkileşimli sözdizimi diyagramını deneyin:

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama için OData sözdizimi diyagramı](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Ayrıca bkz.  

- [Azure Bilişsel Arama filtreler](search-filters.md)
- [Azure Bilişsel Arama REST API &#40;belgelerde arama yapın&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene sorgu söz dizimi](query-lucene-syntax.md)
- [Azure Bilişsel Arama basit sorgu söz dizimi](query-simple-syntax.md)