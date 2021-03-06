---
title: query functie moreLikeThis (preview)
titleSuffix: Azure Cognitive Search
description: Beschrijft de functie moreLikeThis (preview), die beschikbaar is in Preview-versies van de Azure Cognitive Search REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873808"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (preview) in azure Cognitive Search

> [!IMPORTANT] 
> Deze functie is momenteel beschikbaar als openbare preview-versie. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is momenteel geen portal-of .NET SDK-ondersteuning.

`moreLikeThis=[key]`is een query parameter in de [Search Documents-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) waarmee documenten worden gevonden die vergelijkbaar zijn met het document dat is opgegeven door de document sleutel. Wanneer een zoek opdracht wordt gemaakt met `moreLikeThis`, wordt er een query gegenereerd met zoek termen die zijn geëxtraheerd uit het opgegeven document, waarin het document het beste wordt beschreven. De gegenereerde query wordt vervolgens gebruikt voor het maken van de zoek opdracht. Standaard wordt de inhoud van alle Doorzoek bare velden beschouwd als minus alle beperkte velden die u hebt opgegeven met behulp `searchFields` van de para meter. De `moreLikeThis` para meter kan niet worden gebruikt met de zoek `search=[string]`parameter,.

Standaard wordt de inhoud van alle Doorzoek bare velden op het hoogste niveau beschouwd. Als u in plaats daarvan bepaalde velden wilt opgeven, kunt u de `searchFields` para meter gebruiken. 

U kunt niet `MoreLikeThis` gebruiken voor Doorzoek bare subvelden in een [complex type](search-howto-complex-data-types.md).

## <a name="examples"></a>Voorbeelden

In de volgende voor beelden wordt het voor beeld van hotels gebruikt [in Quick Start: Maak een zoek index in de Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Eenvoudige query

De volgende query zoekt naar documenten waarvan de beschrijvings velden het meest overeenkomen met het veld van het bron document `moreLikeThis` , zoals opgegeven door de para meter:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

In dit voor beeld zoekt de aanvraag naar hotels die vergelijkbaar zijn met `HotelId` die van 29.
In plaats van HTTP GET te gebruiken, kunt u `MoreLikeThis` ook gebruikmaken van http post:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Filters toepassen

`MoreLikeThis`kan worden gecombineerd met andere algemene query parameters, `$filter`zoals. Zo kan de query worden beperkt tot alleen hotels waarvan de categorie ' budget ' is, waarbij de classificatie hoger is dan 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Velden selecteren en de resultaten beperken

De `$top` selector kan worden gebruikt om het aantal resultaten te beperken dat in een `MoreLikeThis` query moet worden geretourneerd. Daarnaast kunnen velden worden geselecteerd met `$select`. Hier worden de eerste drie hotels geselecteerd samen met hun ID, naam en classificatie: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Volgende stappen

U kunt elk hulp programma voor webtesting gebruiken om te experimenteren met deze functie.  U kunt het beste postman gebruiken voor deze oefening.

> [!div class="nextstepaction"]
> [Azure Cognitive Search REST-Api's verkennen met behulp van postman](search-get-started-postman.md)
