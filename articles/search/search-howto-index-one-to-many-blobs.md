---
title: Index-blobs die meerdere documenten bevatten
titleSuffix: Azure Cognitive Search
description: Verken Azure-blobs voor tekst inhoud met behulp van de Azure Congitive Search BLOB-indexer, waarbij elke Blob een of meer documenten in de zoek index kan opleveren.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112265"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blobs indexeren om meerdere zoek documenten te maken
Een BLOB-Indexeer functie behandelt standaard de inhoud van een BLOB als één Zoek document. Bepaalde **parsingMode** -waarden ondersteunen scenario's waarbij een afzonderlijke Blob kan leiden tot meerdere zoek documenten. De verschillende soorten **parsingMode** waarmee een Indexeer functie meer dan één Zoek document uit een BLOB kan extra heren:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Een-op-veel-document sleutel
Elk document dat in een Azure Cognitive Search index wordt weer gegeven, wordt uniek geïdentificeerd door een document sleutel. 

Als er geen parserings modus is opgegeven, en als er geen expliciete toewijzing is voor het sleutel veld in de index, wordt `metadata_storage_path` de [eigenschap automatisch door gegeven als de sleutel](search-indexer-field-mappings.md) in azure Cognitive Search. Deze toewijzing zorgt ervoor dat elke BLOB wordt weer gegeven als een afzonderlijk Zoek document.

Wanneer u een van de hierboven genoemde parserings modi gebruikt, wordt één BLOB toegewezen aan veel Zoek documenten, waardoor een document sleutel alleen op basis van BLOB-meta gegevens ongeschikt wordt gemaakt. Om deze beperking te overwinnen, kan Azure Cognitive Search een ' één-op-veel ' document sleutel genereren voor elke afzonderlijke entiteit die uit een blob is geëxtraheerd. Deze eigenschap krijgt de `AzureSearch_DocumentKey` naam en wordt toegevoegd aan elke afzonderlijke entiteit die is geëxtraheerd uit de blob. De waarde van deze eigenschap is gegarandeerd uniek voor elke afzonderlijke entiteit _in blobs_ en de entiteiten worden weer gegeven als afzonderlijke Zoek documenten.

Standaard, wanneer er geen expliciete veld toewijzingen voor het sleutel index veld worden opgegeven, wordt `AzureSearch_DocumentKey` de toewijzing hieraan toegewezen met behulp `base64Encode` van de functie voor veld toewijzing.

## <a name="example"></a>Voorbeeld
Stel dat u een index definitie hebt met de volgende velden:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

En uw BLOB-container heeft blobs met de volgende structuur:

_Blob1. json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Wanneer u een Indexeer functie maakt en de **parsingMode** instelt op `jsonLines` -zonder expliciete veld toewijzingen voor het sleutel veld op te geven, wordt de volgende toewijzing impliciet toegepast
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Met deze instelling wordt de Azure Cognitive Search-index met de volgende informatie (met base64 versleutelde id afgekort voor de boog)

| id | temperatuur | pressure | tijdstempel |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Aangepaste veld toewijzing voor index sleutel veld

Uitgaande van dezelfde index definitie als in het vorige voor beeld, zeggen uw BLOB-container blobs met de volgende structuur:

_Blob1. json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Wanneer u een Indexeer functie met `delimitedText` **parsingMode**maakt, kan het natuurlijk handig zijn om een veld toewijzings functie als volgt in te stellen op het sleutel veld:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Deze toewijzing resulteert echter _niet_ in vier documenten die in de index worden weer gegeven, omdat het `recordid` veld niet uniek is in _blobs_. Daarom raden wij u aan het gebruik van de impliciete veld toewijzing van de `AzureSearch_DocumentKey` eigenschap toe te passen op het sleutel index veld voor een-op-veel-parserings modus.

Als u een expliciete veld toewijzing wilt instellen, moet u ervoor zorgen dat de _sourceField_ uniek is voor elke afzonderlijke entiteit **in alle blobs**.

> [!NOTE]
> De benadering die wordt `AzureSearch_DocumentKey` gebruikt door het waarborgen van uniekheid per geëxtraheerde entiteit is onderhevig aan wijzigingen en daarom moet u niet vertrouwen op de waarde voor de behoeften van uw toepassing.

## <a name="next-steps"></a>Volgende stappen

Als u nog niet bekend bent met de basis structuur en werk stroom voor het indexeren van blobs, moet u eerst de [indexering van azure Blob Storage met azure Cognitive Search](search-howto-index-json-blobs.md) controleren. Raadpleeg de volgende artikelen voor meer informatie over het parseren van modi voor verschillende typen blob-inhoud.

> [!div class="nextstepaction"]
> [Indexeren van CSV-blobs](search-howto-index-csv-blobs.md)
> voor het[indexeren van JSON-blobs](search-howto-index-json-blobs.md)
