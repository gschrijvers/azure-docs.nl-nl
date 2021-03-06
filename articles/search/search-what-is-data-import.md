---
title: Import-en gegevens opname in zoek indexen
titleSuffix: Azure Cognitive Search
description: Gegevens vullen en uploaden naar een index in azure Cognitive Search van externe gegevens bronnen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282755"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Overzicht van het importeren van gegevens-Azure Cognitive Search

In azure Cognitive Search query's uitvoeren over uw inhoud geladen en opgeslagen in een [zoek index](search-what-is-an-index.md). In dit artikel worden de twee basis benaderingen besproken voor het vullen van een index: *Push* uw gegevens in de index via een programma, of wijs een [Azure Cognitive Search Indexeer functie](search-indexer-overview.md) op een ondersteunde gegevens bron om de gegevens op te *halen* .

Met beide benaderingen is het doel om gegevens van een externe gegevens bron te *laden* in een Azure Cognitive search-index. Met Azure Cognitive Search kunt u een lege index maken, maar totdat u er gegevens naar wilt pushen of verzamelen, is het niet mogelijk om query's uit te voeren.

## <a name="pushing-data-to-an-index"></a>Gegevens naar een index pushen
Het push-model dat wordt gebruikt om uw gegevens via een programma naar Azure Cognitive Search te verzenden, is de meest flexibele benadering. Ten eerste heeft dit model geen beperkingen met betrekking tot het gegevensbrontype. Een gegevensset die bestaat uit JSON-documenten kan worden gepusht naar een Azure Cognitive Search-index, ervan uitgaande dat elk document in de gegevensset velden bevat die zijn toegewezen aan velden die in uw index schema zijn gedefinieerd. Ten tweede heeft dit model geen beperkingen met betrekking tot de frequentie van de uitvoering. U kunt wijzigingen naar een index pushen zo vaak als u wilt. Voor toepassingen die een zeer lage latentie vereisen (bijvoorbeeld als zoekopdrachten gesynchroniseerd moeten zijn met dynamische inventarisatiedatabases) is het pushmodel de enige mogelijkheid.

Deze aanpak is flexibeler dan het pull-model, omdat u documenten afzonderlijk of in batches kunt uploaden (maximaal 1000 per batch of 16 MB, afhankelijk van waar de limiet ligt). Het push-model biedt u ook de mogelijkheid om documenten te uploaden naar Azure Cognitive Search, ongeacht waar uw gegevens zich bevindt.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Gegevens pushen naar een Azure Cognitive Search-index

U kunt de volgende API's gebruiken om één of meerdere documenten in een index te laden:

+ [Documenten toevoegen, bijwerken of verwijderen (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) of [indexBatch-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Er is momenteel geen ondersteuning voor het pushen van gegevens via de portal.

Voor een inleiding tot elke methodologie raadpleegt u [Quick Start: een azure Cognitive search-index maken met behulp van Power shell](search-create-index-rest-api.md) of [C# Quick Start: een Azure Cognitive search-index maken met behulp van .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexerings acties: uploaden, samen voegen, mergeOrUpload, verwijderen

U kunt het type indexerings actie per document bepalen, waarbij u opgeeft of het document volledig moet worden geüpload, wordt samengevoegd met de bestaande document inhoud of verwijderd.

Geef in het REST API HTTP POST-aanvragen met JSON-aanvraag instanties op de eind punt-URL van uw Azure Cognitive Search-index. Elk JSON-object in de matrix ' value ' bevat de sleutel van het document en geeft aan of een indexerings actie document inhoud toevoegt, bijwerkt of verwijdert. Zie [documenten laden](search-get-started-dotnet.md#load-documents)voor een voor beeld van een code.

In de .NET SDK kunt u uw gegevens inpakken in `IndexBatch` een-object. Een `IndexBatch` verzameling `IndexAction` objecten die elk een document bevat en een eigenschap die de Azure-Cognitive Search vertelt welke actie moet worden uitgevoerd op dat document. Zie voor een code voorbeeld de [C#-Snelstartgids](search-get-started-dotnet.md).


| @search.action | Beschrijving | Vereiste velden voor elk document | Opmerkingen |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Een `upload`-actie is vergelijkbaar met een "upsert", waarbij het document wordt ingevoegd als het nieuw is en wordt bijgewerkt/vervangen als het al bestaat. |sleutel, plus andere velden die u wilt definiëren |Tijdens het bijwerken/vervangen van een bestaand document wordt elk veld dat niet is opgegeven in de aanvraag ingesteld op `null`. Dit gebeurt zelfs als het veld eerder is ingesteld op een niet-null-waarde. |
| `merge` |Een bestaand document wordt bijgewerkt met de opgegeven velden. Als het document niet in de index bestaat, mislukt de samenvoeging. |sleutel, plus andere velden die u wilt definiëren |Alle velden die u in een samenvoeging opgeeft, vervangen de bestaande velden in het document, In de .NET SDK bevat dit velden van het type `DataType.Collection(DataType.String)`. In de REST API omvat dit velden van het type `Collection(Edm.String)`. Als het document bijvoorbeeld een veld `tags` bevat met de waarde `["budget"]` en u een samenvoeging doet met de waarde `["economy", "pool"]` voor `tags`, wordt de uiteindelijke waarde van het veld `tags``["economy", "pool"]`. Het wordt dus niet `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Deze bewerking gedraagt zich als `merge` wanneer een document met de opgegeven sleutel al in de index bestaat. Als het document niet bestaat, gedraagt deze bewerking zich als `upload` met een nieuw document. |sleutel, plus andere velden die u wilt definiëren |- |
| `delete` |Het opgegeven document wordt uit de index verwijderd. |alleen sleutel |Alle andere velden worden genegeerd. Als u een afzonderlijk veld uit een document wilt verwijderen, gebruikt u `merge` en stelt u het veld expliciet in op null. |

## <a name="decide-which-indexing-action-to-use"></a>Bepalen welke indexeerbewerking u moet gebruiken
Gegevens importeren met behulp van de .NET SDK, (uploaden, samen voegen, verwijderen en mergeOrUpload). Afhankelijk van welke van de onderstaande bewerkingen u kiest, moet u slechts bepaalde velden voor elk document opnemen:


### <a name="formulate-your-query"></a>Uw query formuleren
Er zijn twee manieren om [in de index te zoeken met behulp van de REST-API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). De ene manier is om een HTTP POST-aanvraag uit te geven waarbij uw queryparameters worden gedefinieerd in een JSON-object in de aanvraagtekst. De andere manier is om een HTTP GET-aanvraag uit te geven waarbij uw queryparameters worden gedefinieerd in de aanvraag-URL. POST heeft [soepelere limieten](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) met betrekking tot de grootte van queryparameters dan GET. Daarom wordt u aangeraden POST te gebruiken, tenzij er speciale omstandigheden zijn waarin het gebruik van GET beter zou zijn.

Voor zowel POST als GET moet u in de aanvraag-URL de *servicenaam*, de *indexnaam* en de juiste *API-versie* (de huidige API-versie is `2019-05-06` op het moment van publicatie van dit document) opgeven. Voor GET geeft u in de *querytekenreeks* aan het einde van de URL de queryparameters op. Hieronder vindt u de URL-indeling:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

De indeling voor POST is hetzelfde, maar met alleen de api-versie in de queryreeksparameters.


## <a name="pulling-data-into-an-index"></a>Gegevens in een index ophalen
Het pullmodel verkent een ondersteunde gegevensbron en uploadt de gegevens automatisch naar uw index. In azure Cognitive Search is deze mogelijkheid geïmplementeerd via *Indexeer functies*, die momenteel beschikbaar zijn voor deze platformen:

+ [Blob-opslag](search-howto-indexing-azure-blob-storage.md)
+ [Tabel opslag](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database en SQL Server op Azure-VM's](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexeerfuncties verbinden een index met een gegevensbron (meestal een tabel, weergave of equivalente structuur) en wijzen bronvelden toe aan equivalente velden in de index. Tijdens de uitvoering wordt de rijenset automatisch omgezet naar JSON en in de opgegeven index geladen. Alle indexeerfuncties ondersteunen planning. U kunt dus opgeven hoe vaak de gegevens moeten worden vernieuwd. Met de meeste indexeerfuncties kunt u wijzigingen bijhouden als dit door de gegevensbron wordt ondersteund. Als u naast het herkennen van nieuwe documenten, wijzigingen en verwijderingen in een bestaand document bijhoudt, hoeft u de gegevens in een index via indexeerfuncties niet actief bij te houden. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Gegevens ophalen in een Azure Cognitive Search-index

Functionaliteit van de indexeerfunctie wordt weergegeven in [Azure Portal](search-import-data-portal.md), de [REST API](/rest/api/searchservice/Indexer-operations) en de [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Een voor deel van het gebruik van de portal is dat Azure Cognitive Search meestal een standaard index schema voor u kan genereren door de meta gegevens van de bron-gegevensset te lezen. U kunt de gegenereerde index wijzigen totdat de index wordt verwerkt. Daarna zijn alleen schemabewerkingen toegestaan waarvoor de index niet opnieuw gegenereerd hoeft te worden. Als de wijzigingen die u doorvoert, rechtstreeks invloed hebben op het schema, moet u de index opnieuw opbouwen. 

## <a name="verify-data-import-with-search-explorer"></a>Gegevens import controleren met Search Explorer

Een snelle manier om een voor spelling controle uit te voeren op het uploaden van documenten is het gebruik van **Search Explorer** in de portal. Met de explorer kunt u gegevens uit een index opvragen zonder code te schrijven. De zoek ervaring is gebaseerd op standaard instellingen, zoals de [eenvoudige syntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) en de standaard [Search mode-query parameter](/rest/api/searchservice/search-documents). Resultaten worden geretourneerd in JSON, zodat u het hele document kunt inspecteren.

> [!TIP]
> Talloze [Azure Cognitive Search-code voorbeelden](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) zijn Inge sloten of gemakkelijk beschik bare gegevens sets, zodat u snel aan de slag kunt gaan. De portal bevat ook een voorbeeldindexeerfunctie en een gegevensbron, bestaande uit een kleine vastgoedgegevensset ('realestate-us-sample'). Wanneer u de vooraf geconfigureerde Indexeer functie uitvoert op de voorbeeld gegevens bron, wordt er een index gemaakt en geladen met documenten die vervolgens kunnen worden opgevraagd in Search Explorer of door de code die u schrijft.

## <a name="see-also"></a>Zie ook

+ [Overzicht van de indexeerfunctie](search-indexer-overview.md)
+ [Portaloverzicht: een index maken, gegevens laden, een query in een index uitvoeren](search-get-started-portal.md)
