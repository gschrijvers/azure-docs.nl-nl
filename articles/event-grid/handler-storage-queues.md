---
title: Opslag wachtrij als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u Azure Storage-wacht rijen kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: f62f2b5bc01518af29bd1deb17a38e9fe105a4ed
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800556"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Opslag wachtrij als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure Queue Storage** is een hiervan. 

Gebruik **Queue Storage** om gebeurtenissen te ontvangen die moeten worden opgehaald. U kunt Queue Storage gebruiken wanneer u een langlopend proces hebt dat te lang duurt om te reageren. Door gebeurtenissen te verzenden naar de wachtrij opslag, kan de app op basis van een eigen planning gebeurtenissen ophalen en verwerken.

## <a name="tutorials"></a>Zelfstudies
Raadpleeg de volgende zelf studie voor een voor beeld van het gebruik van wachtrij opslag als een gebeurtenis-handler. 

|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen naar Azure Queue-opslag door sturen met Azure CLI en Event Grid](custom-event-to-queue-storage.md) | Hierin wordt beschreven hoe u aangepaste gebeurtenissen naar een wachtrij opslag verzendt. |

## <a name="rest-examples-for-put"></a>REST-voor beelden (voor PUT)

### <a name="storage-queue-as-the-event-handler"></a>Opslag wachtrij als gebeurtenis-handler

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                "queueName": "<QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler---delivery-with-managed-identity"></a>Opslag wachtrij als gebeurtenis-handler-levering met beheerde identiteit

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "StorageQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                    "queueName": "<QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination"></a>Opslag wachtrij als een deadletter-doel

```json
{
    "name": "",
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterDestination": 
        {
            "endpointType": "StorageBlob",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                "blobContainerName": "test"
            }
        }
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination---managed-identity"></a>Opslag wachtrij als een door een deadletter-doel beheerde identiteit

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "deadLetterDestination": 
            {
                "endpointType": "StorageBlob",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                    "blobContainerName": "test"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
