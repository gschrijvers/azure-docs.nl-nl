---
title: Parquet geneste typen doorzoeken met behulp van SQL op aanvraag (preview)
description: In dit artikel leert u hoe u een query kunt uitvoeren op Parquet-geneste typen.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 82edee84317b5d542bf65e29514286f96c18bbcc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744224"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Parquet geneste typen doorzoeken met behulp van SQL on-demand (preview) in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van SQL on-demand (preview) in azure Synapse Analytics.  Met deze query worden geneste Parquet-typen gelezen.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een Data Base** met een gegevens bron waarnaar wordt verwezen. Initialiseer vervolgens de objecten door [installatie script](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die data base. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="project-nested-or-repeated-data"></a>Geneste of herhaalde gegevens projecteren

Met de volgende query wordt het bestand *justSimpleArray. Parquet* gelezen. Hiermee worden alle kolommen uit het Parquet-bestand, inclusief geneste of herhaalde gegevens, geprojecteerd.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Toegang tot elementen vanuit geneste kolommen

Met de volgende query wordt het bestand *structExample. Parquet* gelezen en wordt getoond hoe u elementen van een geneste kolom laat zien:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Toegang tot elementen van herhaalde kolommen

De volgende query leest het bestand *justSimpleArray. Parquet* en gebruikt [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) om een **scalair** element op te halen in een herhaalde kolom, zoals een matrix of kaart:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Met de volgende query wordt het bestand *mapExample. Parquet* gelezen en wordt [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruikt om een **niet-scalair** element op te halen uit een herhaalde kolom, zoals een matrix of kaart:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel ziet u hoe u een [query uitvoert voor json-bestanden](query-json-files.md).
