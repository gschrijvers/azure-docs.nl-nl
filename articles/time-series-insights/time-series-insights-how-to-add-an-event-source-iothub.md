---
title: Een IoT hub-gebeurtenis bron toevoegen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het toevoegen van een IoT hub-gebeurtenis bron aan uw Time Series Insights-omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a0a2f703d9224b8b9dd77c80b2b6a7faee70f5bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538100"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Een IoT hub-gebeurtenis bron toevoegen aan uw Time Series Insights omgeving

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om een gebeurtenis bron toe te voegen waarmee gegevens worden gelezen van Azure IoT Hub naar uw Azure Time Series Insights omgeving.

> [!NOTE]
> De instructies in dit artikel zijn van toepassing op Azure Time Series Insights GA en aan Time Series Insights-voorbeeld omgevingen.

## <a name="prerequisites"></a>Vereisten

* Een [Azure time series Insights omgeving](time-series-insights-update-create-environment.md)maken.
* Maak een [IOT-hub met behulp van de Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* Voor de IoT-hub moeten actieve bericht gebeurtenissen worden verzonden in.
* Maak een speciale Consumer groep in de IoT-hub voor de Time Series Insights omgeving van waaruit kan worden verbruikt. Elke Time Series Insights gebeurtenis bron moet een eigen toegewezen consumenten groep hebben die niet met een andere consument wordt gedeeld. Als meerdere lezers gebeurtenissen van dezelfde consumenten groep gebruiken, kunnen alle lezers waarschijnlijk fouten vertonen. Lees de [hand leiding voor Azure IOT hub-ontwikkel aars](../iot-hub/iot-hub-devguide.md)voor meer informatie.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Een Consumer groep toevoegen aan uw IoT-hub

Toepassingen gebruiken consumenten groepen voor het ophalen van gegevens uit Azure IoT Hub. Als u gegevens op een betrouw bare manier van uw IoT-hub wilt lezen, geeft u een speciale consumenten groep op die alleen door deze Time Series Insights omgeving wordt gebruikt.

Een nieuwe consumenten groep toevoegen aan uw IoT-hub:

1. Zoek en open uw IoT-hub in de [Azure Portal](https://portal.azure.com).

1. Selecteer onder **instellingen**de optie **ingebouwde eind punten**en selecteer vervolgens het eind punt **gebeurtenissen** .

   [![Selecteer op de pagina eind punten samen stellen de knop gebeurtenissen](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Voer onder **consumenten groepen**een unieke naam in voor de consumenten groep. Gebruik dezelfde naam in uw Time Series Insights omgeving wanneer u een nieuwe gebeurtenis bron maakt.

1. Selecteer **Opslaan**.

## <a name="add-a-new-event-source"></a>Een nieuwe gebeurtenis bron toevoegen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle resources** in het menu aan de linkerkant. Selecteer uw Time Series Insights-omgeving.

1. Selecteer onder **instellingen**de optie **gebeurtenis bronnen**en selecteer vervolgens **toevoegen**.

   [![Selecteer gebeurtenis bronnen en selecteer vervolgens de knop toevoegen](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. Voer in het deel venster **nieuwe bron van gebeurtenis** de naam van de **gebeurtenis bron**een unieke naam in voor deze time series Insights omgeving. Voer bijvoorbeeld **gebeurtenis-stream**in.

1. Selecteer **IOT hub**voor **bron**.

1. Selecteer een waarde voor de **optie importeren**:

   * Als u al een IoT-hub in een van uw abonnementen hebt, selecteert u **IOT hub gebruiken uit beschik bare abonnementen**. Deze optie is de eenvoudigste benadering.
   
     [![Selecteer opties in het deel venster nieuwe gebeurtenis bron](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * In de volgende tabel worden de eigenschappen beschreven die vereist zijn voor de optie **IOT hub van beschik bare abonnementen gebruiken** :

       [![Nieuw deel venster gebeurtenis Bron: eigenschappen die moeten worden ingesteld in de optie IoT Hub van beschik bare abonnementen gebruiken](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnement | Het abonnement waarvan de gewenste IOT-hub deel uitmaakt. |
       | IoT-hub-naam | De naam van de geselecteerde IOT-hub. |
       | Beleids naam van IoT-hub | Selecteer het beleid voor gedeelde toegang. U kunt het beleid voor gedeelde toegang vinden op het tabblad instellingen van IoT hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Voor het beleid voor gedeelde toegang voor uw gebeurtenis bron *moeten* **service Connect** -machtigingen zijn toegewezen. |
       | Beleids sleutel voor IoT hub | De sleutel is vooraf ingevuld. |

    * Als de IoT-hub zich buiten uw abonnementen bevindt, of als u geavanceerde opties wilt kiezen, selecteert u **IOT hub-instellingen hand matig opgeven**.

      In de volgende tabel worden de vereiste eigenschappen voor de **IOT hub instellingen hand matig opgeven**:

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnements-id | Het abonnement waarvan de gewenste IOT-hub deel uitmaakt. |
       | Resourcegroep | De naam van de resource groep waarin de IoT-hub is gemaakt. |
       | IoT-hub-naam | De naam van uw IoT-hub. Wanneer u uw IoT-hub hebt gemaakt, hebt u een naam opgegeven voor de IoT-hub. |
       | Beleids naam van IoT-hub | Het beleid voor gedeelde toegang. U kunt het beleid voor gedeelde toegang maken op het tabblad instellingen van IoT hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Voor het beleid voor gedeelde toegang voor uw gebeurtenis bron *moeten* **service Connect** -machtigingen zijn toegewezen. |
       | Beleids sleutel voor IoT hub | De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Azure Service Bus. Voer hier de primaire of secundaire sleutel in. |

    * Beide opties delen de volgende configuratie opties:

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Klanten groep voor IoT hub | De Consumer groep die gebeurtenissen van de IoT-hub leest. We raden u ten zeerste aan om een speciale consumenten groep voor uw gebeurtenis bron te gebruiken. |
       | Serialisatie-indeling voor gebeurtenissen | Op dit moment is JSON de enige beschik bare serialisatie-indeling. De gebeurtenis berichten moeten de volgende indeling hebben of er kunnen geen gegevens worden gelezen. |
       | Naam van de timestamp-eigenschap | Als u deze waarde wilt bepalen, moet u weten wat de bericht indeling is van de bericht gegevens die worden verzonden naar de IoT-hub. Deze waarde is de **naam** van de specifieke gebeurtenis eigenschap in de bericht gegevens die u wilt gebruiken als de tijds tempel van de gebeurtenis. De waarde is hoofdletter gevoelig. Als dit veld leeg blijft, wordt de tijd voor het **plaatsen van gebeurtenissen** in de gebeurtenis bron gebruikt als tijds tempel van de gebeurtenis. |


1. Voeg de toegewezen Time Series Insights naam van de Consumer groep toe die u hebt toegevoegd aan uw IoT-hub.

1. Selecteer **Maken**.

1. Nadat u de gebeurtenis bron hebt gemaakt, wordt door Time Series Insights automatisch streamen van gegevens naar uw omgeving gestart.

## <a name="next-steps"></a>Volgende stappen

* [Definieer gegevens toegangs beleid](time-series-insights-data-access.md) voor het beveiligen van de gegevens.

* [Gebeurtenissen verzenden](time-series-insights-send-events.md) naar de bron van de gebeurtenis.

* Toegang tot uw omgeving in de [Time Series Insights Explorer](https://insights.timeseries.azure.com).
