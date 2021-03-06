---
title: Tips voor het ontwerp van AI-verrijking
titleSuffix: Azure Cognitive Search
description: Tips en probleem oplossing voor het instellen van AI-verrijkings pijplijnen in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79245484"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tips voor AI-verrijking in azure Cognitive Search

Dit artikel bevat een lijst met tips en trucs om u te laten overstappen terwijl u aan de slag gaat met de uitgebreide mogelijkheden van Azure Cognitive Search. 

Als u dit nog niet hebt gedaan, volgt u de [zelf studie: informatie over het aanroepen van AI-verrijkings api's](cognitive-search-quickstart-blob.md) voor de praktijk bij het Toep assen van AI-verrijkingen naar een BLOB-gegevens bron.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: beginnen met een kleine gegevensset
De beste manier om snel problemen te vinden is door de snelheid te verhogen waarmee u problemen kunt oplossen. De beste manier om de indexerings tijd te verminderen, is door het aantal te indexeren documenten te verminderen. 

Begin met het maken van een gegevens bron met slechts een aantal documenten/records. Uw document voorbeeld moet een goede weer gave zijn van de verschillende documenten die worden geïndexeerd. 

Voer uw document voorbeeld door de end-to-end-pijp lijn uit en controleer of de resultaten voldoen aan uw behoeften. Zodra u tevreden bent met de resultaten, kunt u meer bestanden toevoegen aan de gegevens bron.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Controleer of de referenties voor de gegevens bron juist zijn
De verbinding met de gegevens bron wordt pas gevalideerd als u een Indexeer functie hebt gedefinieerd die gebruikmaakt van deze. Als er fouten worden weer gegeven waarin wordt vermeld dat de Indexeer functie de gegevens niet kan ophalen, moet u ervoor zorgen dat:
- Uw connection string is juist. Als u SAS-tokens maakt, moet u er ook voor zorgen dat u de notatie gebruikt die wordt verwacht door Azure Cognitive Search. Zie de [sectie referenties opgeven](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) voor meer informatie over de verschillende indelingen die worden ondersteund.
- De naam van de container in de Indexeer functie is juist.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: bekijken wat er gebeurt, zelfs als er sprake is van een aantal storingen
Soms stopt een kleine fout een Indexeer functie in de bijbehorende sporen. Dat is prima als u van plan bent om problemen één voor één op te lossen. Het is echter mogelijk dat u een bepaald type fout wilt negeren, waardoor de Indexeer functie kan door gaan, zodat u kunt zien welke stromen werkelijk werken.

In dat geval kunt u de Indexeer functie de fouten laten negeren. Doe dat door *maxFailedItems* en *maxFailedItemsPerBatch* als-1 in te stellen als onderdeel van de definitie van de Indexeer functie.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tip 4: verrijkte documenten op de schermen bekijken 
Verrijkte documenten zijn tijdelijke structuren die tijdens de verrijking zijn gemaakt en vervolgens worden verwijderd wanneer de verwerking is voltooid.

Als u een momentopname van het verrijkte document wilt vastleggen tijdens het indexeren, voegt u een veld met de naam ```enriched``` toe aan uw index. De indexeerfunctie dumpt automatisch een tekenreeksrepresentatie van de verrijkingen voor het document in het veld.

Het veld ```enriched``` bevat dan een tekenreeks die een logische representatie vormt van het verrijkte document in het geheugen in JSON.  De veldwaarde is echter een geldig JSON-document. Aanhalingstekens worden geïdentificeerd met een escape-teken, waardoor u `\"` met `"` moet vervangen als u het document als geformatteerde JSON wilt weergeven. 

Het verrijkte veld is alleen bedoeld voor fout opsporing, zodat u inzicht krijgt in de logische vorm van de inhoud waarvoor expressies worden geëvalueerd. U moet niet afhankelijk zijn van dit veld voor het indexeren van de doel einden.

Voeg een ```enriched``` veld toe als onderdeel van de index definitie voor fout opsporing:

#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Tip 5: verwachte inhoud kan niet worden weer gegeven

Ontbrekende inhoud kan het gevolg zijn van het weghalen van documenten tijdens het indexeren. Voor de lagen gratis en basis is de document grootte beperkt. Bestanden die de limiet overschrijden, worden verwijderd tijdens het indexeren. U kunt controleren op verwijderde documenten in de Azure Portal. Dubbel klik op de tegel Indexeer functies in het dash board van de zoek service. Bekijk de verhouding van geslaagde documenten die zijn geïndexeerd. Als dit niet 100% is, kunt u op de verhouding klikken om meer details te krijgen. 

Als het probleem te maken heeft met de bestands grootte, ziet u mogelijk een fout zoals de volgende: \<' de BLOB-naam> ' heeft de \<grootte van de bestands grootte> bytes, wat de maximale grootte voor document extractie voor uw huidige servicelaag overschrijdt. ' Zie [service limieten](search-limits-quotas-capacity.md)voor meer informatie over de limieten voor de Indexeer functie.

Een tweede reden waarom inhoud niet wordt weer gegeven, kan betrekking hebben op fouten in de i/o-toewijzing. De naam van een uitvoer doel is bijvoorbeeld ' personen ', maar de naam van het index veld is kleine letters ' personen '. Het systeem kan 201 succes berichten retour neren voor de volledige pijp lijn, zodat u kunt zien dat indexeren is geslaagd, wanneer een veld leeg is. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 6: de verwerking uitbreiden na een maximale uitvoerings tijd (24-uurs venster)

Het berekenen van de afbeeldings analyse is voor eenvoudige cases evenredig, dus wanneer de installatie kopieën bijzonder groot of complex zijn, kan de maximale toegestane tijd worden overschreden. 

De maximale uitvoerings tijd varieert per laag: enkele minuten op de laag gratis, 24 uur per dag indexeren op factureer bare lagen. Als de verwerking niet kan worden voltooid binnen een periode van 24 uur voor de verwerking op aanvraag, moet u overschakelen naar een schema om de Indexeer functie uit te voeren op het tijdstip waarop deze bewerking is afgebroken. 

Voor geplande Indexeer functies worden de indexeringen hervat op schema in het laatst bekende goede document. Als u een terugkerend schema gebruikt, kan de Indexeer functie de achterstand van de installatie kopie over een reeks uren of dagen werken totdat alle niet-verwerkte installatie kopieën zijn verwerkt. Zie [stap 3: Create-a-Indexeer functie](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) of Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over de syntaxis van een schema.

> [!NOTE]
> Als een Indexeer functie is ingesteld op een bepaald schema, maar herhaaldelijk meerdere keren een fout optreedt in hetzelfde document, wordt de Indexeer functie gestart op een minder frequent interval (Maxi maal ten minste één keer per 24 uur) totdat de voortgang weer wordt uitgevoerd.  Als u van mening bent dat het probleem dat de Indexeer functie niet op een bepaald moment vastloopt, kunt u een on-demand uitvoering van de Indexeer functie uitvoeren. als dat wel het geval is, keert de Indexeer functie weer terug naar het ingestelde plannings interval.

Voor indexering op basis van een portal (zoals beschreven in de Quick Start) kiest u de optie voor het uitvoeren van de Indexeer functie beperkt`"maxRunTime": "PT1H"`de verwerking tot 1 uur (). Mogelijk wilt u het verwerkings venster uitbreiden naar iets langer.

## <a name="tip-7-increase-indexing-throughput"></a>Tip 7: door Voer van indexering verg Roten

Voor [parallelle indexering](search-howto-large-index.md)plaatst u uw gegevens in meerdere containers of meerdere virtuele mappen binnen dezelfde container. Maak vervolgens meerdere data source-en Indexeer functie paren. Alle Indexeer functies kunnen dezelfde vaardig heden gebruiken en naar dezelfde doel zoek index schrijven, zodat uw zoek-app niet op de hoogte hoeft te zijn van deze partities.
Zie [indexeren van grote gegevens sets](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)voor meer informatie.

## <a name="see-also"></a>Zie ook
+ [Snelstartgids: een AI-verrijkings pijplijn maken in de portal](cognitive-search-quickstart-blob.md)
+ [Zelf studie: REST-Api's voor AI-verrijking leren](cognitive-search-tutorial-blob.md)
+ [Referenties voor de gegevens bron opgeven](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Grote gegevens sets indexeren](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Uitgebreide velden toewijzen aan een index](cognitive-search-output-field-mapping.md)
