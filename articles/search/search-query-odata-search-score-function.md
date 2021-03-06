---
title: Naslag informatie over de functie OData Search. Score
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van de functie Search. Score in azure Cognitive Search query's.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113136"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData `search.score` -functie in azure Cognitive Search

Wanneer u een query verzendt naar Azure Cognitive Search zonder de [para meter **$OrderBy** ](search-query-odata-orderby.md), worden de resultaten die terugkomen in aflopende volg orde gesorteerd op relevantie score. Zelfs wanneer u **$OrderBy**gebruikt, wordt de relevantie score standaard gebruikt om bindingen te verstoren. Soms is het echter handig om de relevantie score te gebruiken als eerste Sorteer criterium en enkele andere criteria als de afbreeker. Met `search.score` de functie kunt u dit doen.

## <a name="syntax"></a>Syntaxis

De syntaxis voor `search.score` in **$OrderBy** is `search.score()`. De functie `search.score` voert geen para meters uit. Deze kan worden gebruikt in combi `asc` natie `desc` met de specificatie of de sorteer volgorde, net als bij elke andere component in de para meter **$OrderBy** . Dit kan ergens in de lijst met Sorteer criteria worden weer gegeven.

## <a name="example"></a>Voorbeeld

Sorteer hotels in aflopende Volg `search.score` orde `rating`op en, en vervolgens in oplopende volg orde op afstand van de opgegeven coördinaten, zodat tussen twee hotels met een identieke classificatie, het dichtstbijzijnde item bovenaan wordt weer gegeven:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Volgende stappen  

- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
