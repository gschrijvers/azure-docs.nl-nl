---
title: Gebeurtenis-handlers en doelen-Azure Event Grid IoT Edge | Microsoft Docs
description: Gebeurtenis-handlers en bestemmingen in Event Grid aan de rand
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849745"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Gebeurtenis-handlers en bestemmingen in Event Grid aan de rand

Een gebeurtenis-handler is de plaats waar de gebeurtenis voor verdere actie wordt uitgevoerd of voor het verwerken van de gebeurtenis. Met de Event Grid on edge-module kan de gebeurtenis-handler zich op hetzelfde edge-apparaat, op een ander apparaat of in de Cloud bevindt. U kunt elke webhook gebruiken voor het verwerken van gebeurtenissen of het verzenden van gebeurtenissen naar een van de systeem eigen handlers, zoals Azure Event Grid.

In dit artikel vindt u informatie over het configureren van elke.

## <a name="webhook"></a>WebHook

Als u wilt publiceren naar een webhook-eind `endpointType` punt `WebHook` , stelt u de in en geeft u het volgende op:

* endpointUrl: de URL van het webhook-eind punt

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Als u wilt publiceren naar een Azure Event Grid Cloud-eind `endpointType` punt `eventGrid` , stelt u de in en geeft u het volgende op:

* endpointUrl: de URL van het Event Grid onderwerp in de Cloud
* sasKey: de SAS-sleutel van het Event Grid-onderwerp
* Topic: naam voor het stempelen van alle uitgaande gebeurtenissen die moeten worden Event Grid. Onderwerpnaam is handig wanneer u naar een onderwerp van een Event Grid domein boekt.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT Edge hub

Als u wilt publiceren naar een Edge hub-module `endpointType` , `edgeHub` stelt u de in en geeft u het volgende op:

* Uitvoermap: de uitvoer waarop de Event Grid-module gebeurtenissen stuurt die overeenkomen met dit abonnement op edgeHub. Gebeurtenissen die overeenkomen met het onderstaande abonnement worden bijvoorbeeld geschreven naar/messages/modules/eventgridmodule/outputs/sampleSub4.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Event Hubs

Als u wilt publiceren naar een event hub, `endpointType` stelt `eventHub` u de aan en geeft u het volgende op:

* Connections Tring: verbindings reeks voor de specifieke Event hub die u als doel hebt gegenereerd via een gedeeld toegangs beleid.

    >[!NOTE]
    > De connection string moet entiteits-specifiek zijn. Het is niet mogelijk om een naam ruimte connection string te gebruiken. U kunt een specifieke connection string genereren door te navigeren naar de specifieke Event hub waarnaar u wilt publiceren in azure Portal en te klikken op **gedeeld toegangs beleid** voor het genereren van een nieuwe entiteit specifieke connecection teken reeks.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus-wachtrijen

Als u wilt publiceren naar een Service Bus wachtrij, `endpointType` stelt `serviceBusQueue` u de in en geeft u het volgende op:

* Connections Tring: een verbindings reeks voor de specifieke Service Bus wachtrij waarvan u de doel groep hebt gegenereerd via een gedeeld toegangs beleid.

    >[!NOTE]
    > De connection string moet entiteits-specifiek zijn. Het is niet mogelijk om een naam ruimte connection string te gebruiken. Genereer een specifieke entiteit connection string door te navigeren naar de specifieke Service Bus wachtrij waarnaar u wilt publiceren in azure Portal en klik op **beleid voor gedeelde toegang** om een nieuwe entiteit specifieke connecection teken reeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus-onderwerpen

Als u wilt publiceren naar een Service Bus onderwerp, `endpointType` stelt `serviceBusTopic` u de in en geeft u het volgende op:

* Connections Tring: een verbindings reeks voor het specifieke Service Bus onderwerp dat wordt gegenereerd via een gedeeld toegangs beleid.

    >[!NOTE]
    > De connection string moet entiteits-specifiek zijn. Het is niet mogelijk om een naam ruimte connection string te gebruiken. Genereer een specifieke entiteit connection string door te navigeren naar het specifieke Service Bus onderwerp waarnaar u wilt publiceren in azure Portal en klik op **beleid voor gedeelde toegang** om een nieuwe entiteit specifieke connecection teken reeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Opslagwachtrijen

Als u wilt publiceren naar een opslag wachtrij, `endpointType` stelt `storageQueue` u de in en geeft u het volgende op:

* wachtrijmap: de naam van de opslag wachtrij waarnaar u publiceert.
* Connections Tring: de verbindings reeks voor het opslag account waarin de opslag wachtrij zich bevindt.

    >[!NOTE]
    > Event Hubs, Service Bus wacht rijen en Service Bus onderwerpen, de connection string die voor opslag wachtrijen wordt gebruikt, is niet specifiek voor de entiteit. In plaats daarvan moet de connection string voor het opslag account.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```