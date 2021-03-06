---
title: Werken met zoek resultaten
titleSuffix: Azure Cognitive Search
description: Structuur en sorteer de zoek resultaten, haal een document telling op en voeg inhouds navigatie toe aan Zoek resultaten in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 93f1da7db3962994611f70fc145d0e9b62cd4f26
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167856"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Werken met zoek resultaten in azure Cognitive Search

In dit artikel wordt uitgelegd hoe u een query-antwoord krijgt dat terugkeert met een totaal aantal overeenkomende documenten, gepagineerde resultaten, gesorteerde resultaten en door waarden gemarkeerde termen.

De structuur van een antwoord wordt bepaald door para meters in de query: [document zoeken](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) in de rest API-of [DocumentSearchResult-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) in de .NET SDK.

## <a name="result-composition"></a>Resultaten samen stelling

Hoewel een zoek document kan bestaan uit een groot aantal velden, zijn er meestal slechts enkele van de waarden nodig om elk document in de resultatenset aan te duiden. Voeg op een query aanvraag `$select=<field list>` toe om op te geven welke velden in het antwoord worden weer gegeven. Een veld moet worden toegeschreven als kan worden **opgehaald** in de index om in een resultaat te worden opgenomen. 

Voor velden die het beste werken zijn dit het contrast en het onderscheid tussen documenten, waardoor er voldoende informatie is om een klik-en-antwoord op het deel van de gebruiker uit te nodigen. Op een e-commerce-site kunnen het een product naam, beschrijving, merk, kleur, grootte, prijs en classificatie zijn. Voor de hotels-voor beeld-index ingebouwde steek proef zijn dit mogelijk velden in het volgende voor beeld:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Als u afbeeldings bestanden wilt toevoegen aan een resultaat, zoals een foto van een product of logo, slaat u deze buiten Azure Cognitive Search op, maar voegt u een veld in uw index toe om te verwijzen naar de afbeeldings-URL in het zoek document. Voor beelden van indexen die afbeeldingen in de resultaten ondersteunen, zijn de **realestate-voor beeld-US-** demo, in deze [Snelstartgids](search-create-app-portal.md)en in de [nieuwe demo-app voor taken in Utrecht City](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Resultaten pagineren

Standaard retourneert de zoek machine tot de eerste 50 overeenkomsten, zoals wordt bepaald door de zoek score als de query volledige tekst zoekt, of in een wille keurige volg orde voor exacte overeenkomst query's.

Om een ander aantal overeenkomende documenten te retour neren, `$top` voegt `$skip` u para meters toe aan de query aanvraag. In de volgende lijst wordt de logica uitgelegd.

+ Voeg toe `$count=true` om een telling van het totaal aantal overeenkomende documenten in een index op te halen.

+ De eerste set van 15 overeenkomende documenten plus een telling van het totaal aantal overeenkomsten retour neren:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Retourneert de tweede set, waarbij de eerste 15 wordt overgeslagen om de volgende 15 te verkrijgen: `$top=15&$skip=15` . Doe hetzelfde voor de derde set van 15:`$top=15&$skip=30`

De resultaten van gepagineerde query's zijn niet gegarandeerd stabiel als de onderliggende index wordt gewijzigd. Paginering wijzigt de waarde van `$skip` voor elke pagina, maar elke query is onafhankelijk en werkt op de huidige weer gave van de gegevens in de index op het moment van de query (met andere woorden, er is geen caching of moment opname van de resultaten, zoals de waarden in een Data Base voor algemeen gebruik).
 
Hieronder ziet u een voor beeld van hoe u dubbele waarden kunt krijgen. Aannemen een index met vier documenten:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Stel nu dat er resultaten twee per keer worden geretourneerd, gesorteerd op waardering. U voert deze query uit om de eerste pagina met resultaten op te halen: `$top=2&$skip=0&$orderby=rating desc` de volgende resultaten worden geproduceerd:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
In de service wordt ervan uitgegaan dat er een vijfde document wordt toegevoegd aan de index in tussen query-aanroepen: `{ "id": "5", "rating": 4 }` .  Vervolgens voert u een query uit om de tweede pagina op te halen: `$top=2&$skip=2&$orderby=rating desc` en haalt u de volgende resultaten op:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
U ziet dat document 2 twee keer wordt opgehaald. De reden hiervoor is dat het nieuwe document 5 een hogere waarde voor classificatie heeft, zodat het wordt gesorteerd vóór document 2 en-land op de eerste pagina. Hoewel dit gedrag mogelijk onverwacht is, is het gebruikelijk dat een zoek machine zich gedraagt.

## <a name="ordering-results"></a>Resultaten ordenen

Voor Zoek opdrachten in volledige tekst worden de resultaten automatisch gerangschikt op basis van een zoek Score, berekend aan de hand van term frequentie en nabijheid in een document, met hogere scores naar documenten met meer of betere overeenkomsten voor een zoek term. 

Zoek scores geven algemeen inzicht in de relevantie, waarbij de sterkte van de overeenkomst wordt weer gegeven in vergelijking met andere documenten in dezelfde resultatenset. Scores zijn niet altijd consistent met de ene query naar de volgende, dus wanneer u met query's werkt, kunnen er kleine verschillen optreden in de manier waarop Zoek documenten worden besteld. Er zijn verschillende uitleg waarom dit kan gebeuren.

| Oorzaak | Beschrijving |
|-----------|-------------|
| Gegevens volatiliteit | De inhoud van de index is afhankelijk van het toevoegen, wijzigen of verwijderen van documenten. De term frequenties worden gewijzigd wanneer index updates na verloop van tijd worden verwerkt, waardoor de zoek scores van overeenkomende documenten worden beïnvloed. |
| Meerdere replica's | Voor services die gebruikmaken van meerdere replica's, worden query's op elke replica parallel verleend. De index statistieken die worden gebruikt voor het berekenen van een zoek Score worden berekend per replica, met de resultaten samengevoegd en geordend in het query-antwoord. Replica's zijn voornamelijk Spie gels van elkaar, maar statistieken kunnen verschillen als gevolg van kleine verschillen in de status. Eén replica kan bijvoorbeeld verwijderde documenten hebben die bijdragen aan hun statistieken, die uit andere replica's zijn samengevoegd. Normaal gesp roken zijn verschillen in statistieken per replica duidelijker in kleinere indexen. |
| Identieke scores | Als meerdere documenten dezelfde score hebben, kan het zijn dat deze eerst worden weer gegeven.  |

### <a name="consistent-ordering"></a>Consistente volg orde

Gezien de volg orde van de resultaten van Flex, wilt u mogelijk andere opties verkennen als consistentie een toepassings vereiste is. De eenvoudigste benadering is het sorteren op een veld waarde, zoals classificatie of datum. Voor scenario's waarin u wilt sorteren op een specifiek veld, zoals een classificatie of datum, kunt u expliciet een [ `$orderby` expressie](query-odata-filter-orderby-syntax.md)definiëren die kan worden toegepast op elk veld dat is geïndexeerd als **sorteerbaar**.

Een andere optie is het gebruik van een [aangepast Score profiel](index-add-scoring-profiles.md). Score profielen bieden u meer controle over de rang schikking van items in de zoek resultaten, met de mogelijkheid om overeenkomsten te verhogen die in specifieke velden worden gevonden. De extra waarderings logica kan u helpen kleine verschillen tussen replica's te overschrijven omdat de zoek scores voor elk document verder uit elkaar liggen. U wordt aangeraden het [classificatie algoritme](index-ranking-similarity.md) voor deze benadering te hanteren.

## <a name="hit-highlighting"></a>Markeren

Treffer markeringen verwijst naar tekst opmaak (zoals vette of gele hooglichten) die wordt toegepast op overeenkomende termen in een resultaat, zodat u de overeenkomst eenvoudig kunt herkennen. Instructies voor het markeren van treffers worden vermeld in de [query-aanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Als u het markeren van treffers wilt inschakelen, voegt `highlight=[comma-delimited list of string fields]` u in om op te geven welke velden markeringen zullen gebruiken. Markeren is handig voor meer inhouds velden, zoals een beschrijvings veld, waarbij de overeenkomst niet onmiddellijk duidelijk is. Alleen veld definities die als **doorzoekbaar** zijn gemarkeerd, komen in aanmerking voor treffer markeringen.

Azure Cognitive Search retourneert standaard Maxi maal vijf hooglichten per veld. U kunt dit nummer aanpassen door toe te voegen aan het veld een streepje gevolgd door een geheel getal. `highlight=Description-10`Retourneert bijvoorbeeld Maxi maal 10 hooglichten voor overeenkomende inhoud in het veld Beschrijving.

De opmaak wordt toegepast op volledige term query's. Het type opmaak wordt bepaald door Tags en en `highlightPreTag` `highlightPostTag` uw code verwerkt het antwoord (bijvoorbeeld het Toep assen van een vet letter type of een gele achtergrond).

In het volgende voor beeld zijn de termen "zand", "zand", "stranden", "strand" in het veld Beschrijving gemarkeerd voor markeren. Query's die query-uitbrei ding activeren in de-engine, zoals fuzzy en zoeken met Joker tekens, hebben beperkte ondersteuning voor het markeren van treffers.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nieuw gedrag (vanaf 15 juli)

Services die zijn gemaakt na 15 juli 2020 bieden een andere markerings ervaring. Services die vóór deze datum zijn gemaakt, worden niet gewijzigd in hun markerings gedrag. 

Met het nieuwe gedrag:

* Alleen zinsdelen die overeenkomen met de volledige woordgroepen query worden geretourneerd. De query "Super Bowl" retourneert hooglichten als volgt:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Houd er rekening mee dat de term *Bowlen van chips* geen markeringen heeft omdat deze niet overeenkomt met de volledige woord groep.

Wanneer u client code schrijft waarmee treffer markeringen worden geïmplementeerd, moet u rekening houden met deze wijziging. Houd er rekening mee dat dit geen invloed heeft op u tenzij u een volledig nieuwe zoek service maakt.

## <a name="next-steps"></a>Volgende stappen

Als u snel een zoek pagina voor uw client wilt genereren, kunt u de volgende opties overwegen:

+ Met [toepassings Generator](search-create-app-portal.md)maakt u in de portal een HTML-pagina met een zoek balk, facet navigatie en resultaten gebied met afbeeldingen.
+ [Uw eerste app maken in C#](tutorial-csharp-create-first-app.md) is een zelf studie die een functionele client bouwt. Voorbeeld code toont gepagineerde query's, markeren van treffers en sorteren.

Enkele voor beelden van code zijn een web-front-end-interface, die u hier kunt vinden: [New York City Jobs demo app](https://aka.ms/azjobsdemo), [Java script-voorbeeld code met een live demo site](https://github.com/liamca/azure-search-javascript-samples)en [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
