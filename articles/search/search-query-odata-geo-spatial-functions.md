---
title: Verwijzing naar geo-spatiale functie van OData
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van geo-ruimtelijke functies van OData, geo. Distance en geo. intersects, in azure Cognitive Search query's.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113174"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Geo-ruimtelijke functies van OData in azure Cognitive Search `geo.distance` -en`geo.intersects`

Azure Cognitive Search ondersteunt geo-ruimtelijke query's in [OData-filter expressies](query-odata-filter-orderby-syntax.md) via `geo.distance` de `geo.intersects` functies en. De `geo.distance` functie retourneert de afstand in kilo meters tussen twee punten, een veld of bereik variabele en een constante die wordt door gegeven als onderdeel van het filter. De `geo.intersects` functie retourneert `true` of een bepaald punt zich in een bepaalde veelhoek bevindt, waarbij het punt een veld-of bereik variabele is en de veelhoek is opgegeven als een constante die als onderdeel van het filter wordt door gegeven.

De `geo.distance` functie kan ook worden gebruikt in de [para meter **$OrderBy** ](search-query-odata-orderby.md) om Zoek resultaten te sorteren op afstand van een bepaald punt. De syntaxis voor `geo.distance` in **$OrderBy** is hetzelfde als in **$filter**. Wanneer u `geo.distance` in **$OrderBy**gebruikt, moet het veld waarop het van toepassing is, `Edm.GeographyPoint` van het type zijn en moet het ook **sorteerbaar**zijn.

> [!NOTE]
> Wanneer u `geo.distance` gebruikt in de para meter **$OrderBy** , mag het veld dat u aan de functie doorgeeft slechts één geo-Point bevatten. Met andere woorden, deze moeten van het type `Edm.GeographyPoint` en niet `Collection(Edm.GeographyPoint)`. Het is niet mogelijk om te sorteren op verzamelings velden in azure Cognitive Search.

## <a name="syntax"></a>Syntaxis

De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van de `geo.distance` functies `geo.intersects` en, evenals de geo-ruimtelijke waarden waarop ze worden toegepast:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

### <a name="geodistance"></a>geo. distance

De `geo.distance` functie heeft twee para meters `Edm.GeographyPoint` van het type `Edm.Double` en retourneert een waarde die de afstand ertussen in kilo meters. Dit wijkt af van andere services die ondersteuning bieden voor geo-spatiale bewerkingen van OData, die doorgaans afstanden retour neren in meters.

Een van de para meters `geo.distance` moet een geografische punt constante zijn, en de andere moet een veld pad (of een bereik variabele zijn in het geval van een filter iteratie voor een veld van `Collection(Edm.GeographyPoint)`het type). De volg orde van deze para meters is niet van belang.

De geografie punt constante is van het formulier `geography'POINT(<longitude> <latitude>)'`, waarbij de lengte graad en de breedte numeriek constanten zijn.

> [!NOTE]
> Wanneer u `geo.distance` in een filter gebruikt, moet u de afstand die door de functie wordt geretourneerd, vergelijken `lt`met `le`een `gt`constante using `ge`,, of. De Opera `eq` tors `ne` en worden niet ondersteund bij het vergelijken van de afstanden. Dit is bijvoorbeeld een correct gebruik van `geo.distance`:. `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`

### <a name="geointersects"></a>geo. intersects

De `geo.intersects` functie gebruikt een variabele van het `Edm.GeographyPoint` type en een `Edm.GeographyPolygon` constante en retourneert `Edm.Boolean`  --  `true` een als het punt zich binnen de grenzen van de veelhoek bevindt, `false` anders.

De veelhoek is een tweedimensionale Opper vlak dat is opgeslagen als een reeks punten die een gebonden ring definiëren (Zie de [voor beelden](#examples) hieronder). De veelhoek moet worden gesloten, wat betekent dat de eerste en laatste punt sets hetzelfde moeten zijn. [Punten in een veelhoek moeten een aflinksome volg orde hebben](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Georuimtelijke query's en veelhoeken die de 180th-meridiaan bespannen

Voor veel georuimtelijke query bibliotheken waarbij een query wordt ingesteld die de 180th-meridiaan (in de buurt van de grens waarde) bevat, of een tijdelijke oplossing vereist, zoals het splitsen van de veelhoek in twee, één aan beide zijden van de meridiaan.

In azure Cognitive Search werken georuimtelijke query's met een lengte van 180 graden zoals verwacht als de query vorm rechthoekig is en uw coördinaten worden uitgelijnd op een raster indeling langs lengte graad en breedte graad (bijvoorbeeld `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). Als dat niet het geval is, kunt u voor niet-rechthoekige of niet-afgestemde vormen de methode split-veelhoek overwegen.  

### <a name="geo-spatial-functions-and-null"></a>Georuimtelijke functies en`null`

Net als alle andere niet-verzamelings velden in azure Cognitive Search, kunnen `Edm.GeographyPoint` velden van `null` het type waarden bevatten. Wanneer Azure Cognitive Search een veld `geo.intersects` evalueert dat is `null`, is het resultaat altijd `false`. Het gedrag van `geo.distance` in dit geval is afhankelijk van de context:

- In filters `geo.distance` resulteert een `null` veld in. `null` Dit betekent dat het document niet overeenkomt `null` omdat er een andere waarde dan null wordt geëvalueerd `false`.
- Bij het sorteren van resultaten met `geo.distance` behulp `null` van **$OrderBy**, resulteert een veld in de Maxi maal mogelijke afstand. Documenten met een dergelijk veld worden lager gesorteerd dan alle andere wanneer de sorteer richting `asc` wordt gebruikt (de standaard instelling) en hoger dan alle andere wanneer de richting is `desc`.

## <a name="examples"></a>Voorbeelden

### <a name="filter-examples"></a>Voorbeelden van filter

Alle hotels zoeken binnen 10 kilo meter van een gegeven referentie punt (waarbij locatie een veld van het `Edm.GeographyPoint`type is):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Alle hotels in een bepaalde View Port zoeken die worden beschreven als een veelhoek (waarbij locatie een veld `Edm.GeographyPoint`van het type is). Houd er rekening mee dat de veelhoek is gesloten (de eerste en laatste punt sets moeten hetzelfde zijn) en [de punten moeten in de juiste volg orde worden weer gegeven](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Voor beelden per order

Hotels sorteren op `rating`aflopend en vervolgens oplopend op afstand van de opgegeven coördinaten:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sorteer hotels in aflopende Volg `search.score` orde `rating`op en, en vervolgens in oplopende volg orde op afstand van de opgegeven coördinaten, zodat tussen twee hotels met een identieke classificatie, het dichtstbijzijnde item bovenaan wordt weer gegeven:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
