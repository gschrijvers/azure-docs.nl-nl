---
title: Opties voor apparaat-naar-cloud van Azure IoT Hub | Microsoft Docs
description: 'Ontwikkelaars handleiding: richt lijnen voor het gebruik van apparaat-naar-Cloud-berichten, gerapporteerde eigenschappen of het uploaden van bestanden voor Cloud-naar-apparaat-communicatie.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b984ee7ed662bf089e7bcb6fc7e948fb61ed1209
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733219"
---
# <a name="device-to-cloud-communications-guidance"></a>Communicatie richtlijnen voor apparaat-naar-Cloud

Bij het verzenden van informatie van de apparaat-app naar de back-end van de oplossing, IoT Hub drie opties beschikbaar:

* [Apparaat-naar-Cloud-berichten](iot-hub-devguide-messages-d2c.md) voor time series-telemetrie en-waarschuwingen.

* De [gerapporteerde eigenschappen van het](iot-hub-devguide-device-twins.md) apparaat worden gerapporteerd voor informatie over de status van het apparaat, zoals de beschik bare mogelijkheden, voor waarden of de status van langlopende werk stromen. Bijvoorbeeld configuratie-en software-updates.

* Uploads van [bestanden](iot-hub-devguide-file-upload.md) voor media bestanden en grote telemetriegegevens worden geüpload door apparaten die op een regel matig zijn aangesloten of die zijn gecomprimeerd om band breedte te besparen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Hier volgt een gedetailleerde vergelijking van de verschillende opties voor apparaat-naar-Cloud communicatie.

|  | Apparaat-naar-cloud-berichten | De gerapporteerde eigenschappen van het apparaat dubbele | Uploads van bestanden |
| ---- | ------- | ---------- | ---- |
| Scenario | Telemetrische time series en waarschuwingen. Bijvoorbeeld: 256-KB-sensor gegevens batches die elke vijf minuten worden verzonden. | Beschik bare mogelijkheden en voor waarden. Bijvoorbeeld de huidige connectiviteits modus voor apparaten, zoals mobiel of WiFi. Het synchroniseren van langlopende werk stromen, zoals configuratie-en software-updates. | Media bestanden. Grote (meestal gecomprimeerde) telemetrie-batches. |
| Opslag en ophalen | Tijdelijk opgeslagen door IoT Hub, Maxi maal 7 dagen. Alleen sequentiële Lees bewerkingen. | Opgeslagen door IoT Hub op het dubbele apparaat. Kan worden opgehaald met behulp van de [IOT hub-query taal](iot-hub-devguide-query-language.md). | Opgeslagen in Azure Storage account van de gebruiker. |
| Grootte | Maxi maal 256 KB-berichten. | De maximale grootte van de gerapporteerde eigenschappen is 32 KB. | Maximale bestands grootte die wordt ondersteund door Azure Blob Storage. |
| Frequency | Hoog. Zie [IOT hub limieten](iot-hub-devguide-quotas-throttling.md)voor meer informatie. | Gemiddeld. Zie [IOT hub limieten](iot-hub-devguide-quotas-throttling.md)voor meer informatie. | Laag. Zie [IOT hub limieten](iot-hub-devguide-quotas-throttling.md)voor meer informatie. |
| Protocol | Beschikbaar op alle protocollen. | Beschikbaar via MQTT of AMQP. | Beschikbaar wanneer u een protocol gebruikt, maar HTTPS vereist op het apparaat. |

Een toepassing moet mogelijk gegevens verzenden als een telemetrie-tijd reeks of-waarschuwing en deze beschikbaar maken op het apparaat dubbele. In dit scenario kunt u een van de volgende opties kiezen:

* De apparaat-app verzendt een apparaat-naar-Cloud bericht en rapporteert een eigenschaps wijziging.
* Met de back-end van de oplossing kunt u de gegevens opslaan in de tags van de dubbele apparaten wanneer het bericht wordt ontvangen.

Omdat apparaat-naar-Cloud-berichten een veel hogere door voer hebben dan dubbele updates van het apparaat, is het soms wenselijk om te voor komen dat het apparaat voor elk apparaat-naar-Cloud bericht wordt bijgewerkt.
