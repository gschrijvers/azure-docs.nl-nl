---
title: Onderbreken, hervatten, schalen met REST-Api's
description: Beheer de reken kracht in azure Synapse Analytics Data Warehouse via REST Api's.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 65161915a63b58ad75ff2c01c16c95dec1aad2ff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652613"
---
# <a name="rest-apis-for-azure-synapse-analytics-data-warehouse"></a>REST-Api's voor Azure Synapse Analytics-Data Warehouse

REST-Api's voor het beheren van Compute in azure Synapse Analytics Data Warehouse.

## <a name="scale-compute"></a>De schaal van Compute aanpassen

Als u de Data Warehouse-eenheden wilt wijzigen, gebruikt u de REST API [Data Base maken of bijwerken](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . In het volgende voor beeld worden de Data Warehouse-eenheden ingesteld op DW1000 voor de data base-MySQLDW, die wordt gehost op server mijnserver. De server bevindt zich in een Azure-resource groep met de naam ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Compute onderbreken

Gebruik de [pause data base](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) rest API om een Data Base te onderbreken. In het volgende voor beeld wordt een Data Base met de naam Database02 die wordt gehost op een server met de naam Server01, onderbroken. De server bevindt zich in een Azure-resource groep met de naam ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Compute hervatten

Als u een Data Base wilt starten, gebruikt u de REST API [Data Base hervatten](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . In het volgende voor beeld wordt een Data Base met de naam Database02 gehost op een server met de naam Server01. De server bevindt zich in een Azure-resource groep met de naam ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Database status controleren

> [!NOTE]
> Momenteel kan de status van de data base ONLINE worden geretourneerd terwijl de data base de online werk stroom voltooit, wat resulteert in verbindings fouten. Mogelijk moet u een vertraging van 2 tot 3 minuten toevoegen aan de toepassings code als u deze API-aanroep gebruikt om verbindings pogingen te activeren.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Onderhouds planning ophalen

Controleer het onderhouds schema dat is ingesteld voor een Data Warehouse.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Onderhouds planning instellen

Een onderhouds planning instellen en bijwerken in een bestaand Data Warehouse.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```

## <a name="next-steps"></a>Volgende stappen

Zie [Compute beheren](sql-data-warehouse-manage-compute-overview.md)voor meer informatie.
