---
title: Grote gegevensset indexeren met ingebouwde Indexeer functies
titleSuffix: Azure Cognitive Search
description: Strategieën voor het indexeren van grote gegevens of reken kundige, intensieve indexering via de batch modus, het opruimen en technieken voor geplande, parallelle en gedistribueerde indexeringen.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 915243fb4dbc6bb274e26261bc5741811ef24592
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82925980"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Grote gegevens sets indexeren in azure Cognitive Search

Azure Cognitive Search ondersteunt [twee basis benaderingen](search-what-is-data-import.md) voor het importeren van gegevens in een zoek index: het *pushen* van uw gegevens in de index via een programma of het aanwijzen van een [Azure Cognitive Search indexer](search-indexer-overview.md) bij een ondersteunde gegevens bron om de gegevens op te *halen* .

Naarmate gegevens volumes groeien of verwerkings behoeften veranderen, is het mogelijk dat eenvoudige of standaard indexerings strategieën niet meer praktisch zijn. Voor Azure Cognitive Search zijn er verschillende benaderingen voor het maken van grotere gegevens sets, variërend van de manier waarop u een aanvraag voor het uploaden van gegevens structureert, voor het gebruik van een bron-specifieke Indexeer functie voor geplande en gedistribueerde werk belastingen.

Dezelfde technieken zijn ook van toepassing op langlopende processen. Met name de stappen die worden beschreven in [parallelle indexering](#parallel-indexing) zijn handig voor reken kundige indexen, zoals afbeeldings analyses of natuurlijke taal verwerking in een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md).

In de volgende secties worden technieken besproken voor het indexeren van grote hoeveel heden gegevens met behulp van de Push-API en Indexeer functies.

## <a name="push-api"></a>Push-API

Bij het pushen van gegevens naar een index, zijn er verschillende belang rijke overwegingen die van invloed zijn op indexerings snelheden voor de Push-API. Deze factoren worden beschreven in de volgende sectie. 

Naast de informatie in dit artikel, kunt u ook gebruikmaken van de code voorbeelden in de [zelf studie indexerings snelheden optimaliseren](tutorial-optimize-indexing-push-api.md) voor meer informatie.

### <a name="service-tier-and-number-of-partitionsreplicas"></a>Servicelaag en aantal partities/replica's

Door partities toe te voegen of de laag van uw zoek service te verhogen, worden de indexerings snelheden verhoogd.

Het toevoegen van extra replica's kan ook leiden tot hogere indexerings snelheden, maar dit is niet gegarandeerd. Aan de andere kant verhogen extra replica's het query volume dat door de zoek service kan worden verwerkt. Replica's zijn ook een belang rijk onderdeel voor het ophalen van een [Sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Voordat u partitie/replica's toevoegt of een upgrade naar een hogere laag uitvoert, moet u rekening houden met de monetaire kosten en toewijzings tijd. Het toevoegen van partities kan de indexerings snelheid aanzienlijk verhogen, maar het toevoegen/verwijderen ervan kan vijf tien minuten tot enkele uren duren. Zie de documentatie over het [aanpassen van capaciteit](search-capacity-planning.md)voor meer informatie.

### <a name="index-schema"></a>Index schema

Het schema van uw index speelt een belang rijke rol bij het indexeren van gegevens. Het is mogelijk om velden toe te voegen en aanvullende eigenschappen toe te voegen aan deze velden (zoals *doorzoekbaar*, *facetbaar*of *filterbaar*) beide de indexerings snelheden verlagen.

Over het algemeen raden we u aan om extra eigenschappen toe te voegen aan velden als u ze wilt gebruiken.

> [!NOTE]
> Voorkom dat de document grootte kleiner wordt door niet-query bare gegevens toe te voegen aan een index. Afbeeldingen en andere binaire gegevens kunnen niet rechtstreeks worden doorzocht en mogen niet worden opgeslagen in de index. Als u niet-query bare gegevens wilt integreren in Zoek resultaten, moet u een niet-doorzoekbaar veld definiëren waarin een URL-verwijzing naar de resource wordt opgeslagen.

### <a name="batch-size"></a>Batch grootte

Een van de eenvoudigste mechanismen voor het indexeren van een grotere gegevensset is het indienen van meerdere documenten of records in één aanvraag. Zolang de volledige Payload minder is dan 16 MB, kan een aanvraag tot 1000 documenten in een bulk upload bewerking verwerken. Deze limieten zijn van toepassing, ongeacht of u gebruikmaakt van de [rest API documenten toevoegen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) of de [methode index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) in de .NET SDK. Voor beide API'S, pakt u 1000-documenten in de hoofd tekst van elke aanvraag.

Door batches te gebruiken om documenten te indexeren, worden de index prestaties aanzienlijk verbeterd. Het bepalen van de optimale Batch grootte voor uw gegevens is een belang rijk onderdeel van het optimaliseren van de index snelheid. De twee primaire factoren die van invloed zijn op de optimale Batch grootte zijn:
+ Het schema van uw index
+ De grootte van uw gegevens

Omdat de optimale Batch grootte afhankelijk is van uw index en uw gegevens, is de beste manier om verschillende batch grootten te testen om te bepalen wat resulteert in de snelste indexerings snelheden voor uw scenario. Deze [zelf studie](tutorial-optimize-indexing-push-api.md) bevat voorbeeld code voor het testen van batch groottes met behulp van de .NET SDK. 

### <a name="number-of-threadsworkers"></a>Aantal threads/werk rollen

Als u optimaal gebruik wilt maken van de indexerings snelheden van Azure Cognitive Search, moet u waarschijnlijk meerdere threads gebruiken voor het gelijktijdig verzenden van batch-indexerings aanvragen naar de service.  

Het optimale aantal threads wordt bepaald door:

+ De laag van uw zoek service
+ Het aantal partities
+ De grootte van uw batches
+ Het schema van uw index

U kunt dit voor beeld wijzigen en testen met verschillende thread aantallen om het optimale thread aantal voor uw scenario te bepalen. Als er echter meerdere threads gelijktijdig worden uitgevoerd, kunt u profiteren van de meeste efficiëntie-winsten van. 

> [!NOTE]
> Wanneer u de laag van uw zoek service verhoogt of de partities verg root, moet u ook het aantal gelijktijdige threads verg Roten.

Wanneer u de aanvragen van de zoek service aanpast, kunnen er [HTTP-status codes](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) worden weer gegeven die aangeven dat de aanvraag niet volledig is voltooid. Tijdens het indexeren zijn twee veelvoorkomende HTTP-status codes:

+ **503-Service niet beschikbaar** : deze fout betekent dat het systeem zwaar wordt belast en dat de aanvraag op dit moment niet kan worden verwerkt.
+ **207 multi-status** : deze fout betekent dat sommige documenten zijn geslaagd, maar dat er ten minste één is mislukt.

### <a name="retry-strategy"></a>Strategie voor opnieuw proberen 

Als er een fout optreedt, moeten aanvragen opnieuw worden geprobeerd met een [exponentiële uitstel-strategie voor opnieuw proberen](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Met de .NET SDK van Azure Cognitive Search worden automatisch 503s en andere mislukte aanvragen opnieuw geprobeerd, maar u moet uw eigen logica implementeren om 207s opnieuw uit te voeren. Open source-hulpprogram ma's, zoals [Polly](https://github.com/App-vNext/Polly) , kunnen ook worden gebruikt voor het implementeren van een strategie voor opnieuw proberen.

### <a name="network-data-transfer-speeds"></a>Netwerk gegevens overdrachts snelheden

Netwerk gegevens overdrachts snelheden kunnen een beperkende factor zijn bij het indexeren van gegevens. Het indexeren van gegevens vanuit uw Azure-omgeving is een eenvoudige manier om indexeren sneller te maken.

## <a name="indexers"></a>Indexeerfuncties

[Indexeer functies](search-indexer-overview.md) worden gebruikt voor het verkennen van ondersteunde Azure-gegevens bronnen voor Doorzoek bare inhoud. Hoewel het niet specifiek is bedoeld voor grootschalig indexeren, zijn verschillende indexerings mogelijkheden bijzonder nuttig voor grotere gegevens sets:

+ Met planners kunt u indexeren op regel matige intervallen uitdelen, zodat u deze in de loop van de tijd kan verdelen.
+ Geplande indexering kan worden hervat op het laatste bekende stop punt. Als een gegevens bron niet volledig in een 24-uurs venster wordt verkend, wordt de indexering hervat op dag twee op elke locatie waar deze is gebleven.
+ Het partitioneren van gegevens in kleinere afzonderlijke gegevens bronnen maakt parallelle verwerking mogelijk. U kunt bron gegevens opsplitsen in kleinere onderdelen, zoals in meerdere containers in Azure Blob Storage, en vervolgens bijbehorende, meerdere [gegevens bron objecten](https://docs.microsoft.com/rest/api/searchservice/create-data-source) maken in azure Cognitive Search die parallel kunnen worden geïndexeerd.

> [!NOTE]
> Indexeer functies zijn gegevens bron-specifiek, dus het gebruik van een indexerings benadering is alleen haalbaar voor geselecteerde gegevens bronnen op Azure: [SQL database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="batch-size"></a>Batch grootte

Net als bij de Push-API kunt u met Indexeer functies het aantal items per batch configureren. Voor Indexeer functies op basis van de [rest API Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer), kunt u het `batchSize` argument instellen om deze instelling aan te passen, zodat deze beter overeenkomt met de kenmerken van uw gegevens. 

De standaard batch grootten zijn gegevens bronnen die specifiek zijn. Azure SQL Database en Azure Cosmos DB hebben een standaard Batch grootte van 1000. Met Azure Blob-indexering wordt daarentegen de Batch grootte bij 10 documenten ingesteld op het herkennen van de grotere gemiddelde document grootte. 

### <a name="scheduled-indexing"></a>Geplande indexering

De planning van de Indexeer functie is een belang rijk mechanisme voor het verwerken van grote gegevens sets, evenals trage processen zoals het analyseren van afbeeldingen in een cognitieve Zoek pijplijn. De verwerking van de Indexeer functie werkt binnen een 24-uurs venster. Als de verwerking niet binnen 24 uur wordt voltooid, kan het gedrag van het plannen van de Indexeer functie worden gebruikt voor uw voor deel. 

Geplande indexering begint op specifieke intervallen, met een taak die doorgaans wordt voltooid voordat het volgende geplande interval wordt hervat. Als de verwerking echter niet binnen het interval wordt voltooid, stopt de indexer (omdat deze verouderd is). Bij het volgende interval wordt de verwerking hervat, waar deze zich de laatste keer bevindt, waarbij het systeem bijhoudt waar dit gebeurt. 

In de praktijk is het mogelijk om de Indexeer functie op een 24-uurs schema in te stellen. Wanneer het indexeren voor de volgende 24 uur wordt hervat, wordt het opnieuw gestart bij het laatst bekende goede document. Op deze manier kan een Indexeer functie worden gebruikt tijdens een document achterstand over een reeks dagen tot alle niet-verwerkte documenten worden verwerkt. Zie voor meer informatie over deze aanpak [grote gegevens sets indexeren in Azure Blob-opslag](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Zie voor meer informatie over het instellen van planningen in het algemeen [Indexeer functie maken rest API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) of Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Parallelle indexering

Een strategie voor parallelle indexering is gebaseerd op het indexeren van meerdere gegevens bronnen in gelijktijdig, waarbij elke definitie van de gegevens bron een subset van de gegevens bevat. 

Voor niet-routine matige, computerintensieve, reken vereisten, zoals OCR op gescande documenten in een cognitieve Zoek pijplijn, afbeeldings analyse of natuurlijke taal verwerking, is de juiste benadering voor het volt ooien van een langlopend proces in de kortste periode. Als u query aanvragen kunt elimineren of beperken, is parallelle indexering van een service die niet tegelijkertijd query's afhandelt, uw beste strategie optie voor het werken met een grote inhoud van trage verwerking. 

Parallelle verwerking heeft de volgende elementen:

+ Verdeel bron gegevens over meerdere containers of meerdere virtuele mappen binnen dezelfde container. 
+ Wijs elke mini maal gegevensset toe aan een eigen [gegevens bron](https://docs.microsoft.com/rest/api/searchservice/create-data-source), gekoppeld aan een eigen [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Voor cognitieve Zoek opdrachten verwijzen we naar dezelfde [vaardig heden](https://docs.microsoft.com/rest/api/searchservice/create-skillset) in elke Indexeer functie definitie.
+ Schrijf naar dezelfde doel zoek index. 
+ Plannen dat alle Indexeer functies op hetzelfde moment worden uitgevoerd.

> [!NOTE]
> In azure Cognitive Search kunt u geen afzonderlijke replica's of partities toewijzen aan indexering of query verwerking. Het systeem bepaalt hoe bronnen worden gebruikt. Als u wilt weten wat het effect is op de prestaties van query's, kunt u parallelle indexering uitproberen in een test omgeving voordat u deze naar productie rolt.  

### <a name="how-to-configure-parallel-indexing"></a>Parallelle indexering configureren

Voor Indexeer functies is de verwerkings capaciteit soepel gebaseerd op één indexerings subsysteem voor elke service-eenheid (SU) die wordt gebruikt door uw zoek service. Er zijn meerdere gelijktijdige Indexeer functies mogelijk op Azure Cognitive Search Services die zijn ingericht op basis-of standaard lagen met ten minste twee replica's. 

1. Controleer in de [Azure Portal](https://portal.azure.com)op de pagina **overzicht** van het zoek service dashboard de **prijs categorie** om te bevestigen dat deze parallelle indexering kan bevatten. De lagen basis en standaard bieden meerdere replica's.

2. Verhoog in **instellingen** > **schaal** [replica's](search-capacity-planning.md) voor parallelle verwerking: een extra replica voor elke Indexeer functie-workload. Geef een voldoende waarde voor het bestaande query volume. Het voldoet niet aan query werkbelastingen voor indexering is geen goede balans.

3. Distribueer gegevens in meerdere containers op een niveau dat door Azure Cognitive Search Indexeer functies kan worden bereikt. Dit kan meerdere tabellen zijn in Azure SQL Database, meerdere containers in Azure Blob Storage of meerdere verzamelingen. Definieer één gegevens bron object voor elke tabel of container.

4. Meerdere Indexeer functies maken en plannen om parallel uit te voeren:

   + Ga ervan uit dat een service met zes replica's. U kunt zes Indexeer functies configureren, die zijn toegewezen aan een gegevens bron die een-zesde van de gegevensset bevat voor een 6 richtings splitsing van de gehele gegevensset. 

   + Wijs elke Indexeer functie aan dezelfde index toe. Voor cognitieve Zoek werkbelastingen wijst u elke Indexeer functie aan dezelfde vaardig heden toe.

   + In elke indexers definitie kunt u hetzelfde run-time uitvoerings patroon plannen. `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` Maakt bijvoorbeeld een planning voor 2018-05-15 op alle Indexeer functies, die worden uitgevoerd op een interval van acht uur.

Op de geplande tijd starten alle Indexeer functies, het laden van gegevens, het Toep assen van verrijkingen (als u een cognitieve Zoek pijplijn hebt geconfigureerd) en het schrijven naar de index. De index voor updates wordt niet vergrendeld door Azure Cognitive Search. Gelijktijdige schrijf bewerkingen worden beheerd met een nieuwe poging als een bepaalde schrijf bewerking niet kan worden uitgevoerd bij de eerste poging.

> [!Note]
> Bij het verg Roten van replica's, overweeg dan het aantal partities te verhogen als de index grootte wordt geprojecteerd om aanzienlijk te verg Roten. Partities bevatten segmenten van geïndexeerde inhoud; Hoe meer partities u hebt, des te kleiner het segment dat elke partitie bevat om op te slaan.

## <a name="see-also"></a>Zie ook

+ [Overzicht van de indexeerfunctie](search-indexer-overview.md)
+ [Indexeren in de portal](search-import-data-portal.md)
+ [Indexeer functie Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexeerfunctie voor Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexeerfunctie voor Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexeerfunctie voor Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Beveiliging in azure Cognitive Search](search-security-overview.md)
