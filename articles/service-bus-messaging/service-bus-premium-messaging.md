---
title: Azure Service Bus Premium-en Standard-lagen
description: In dit artikel worden de standaard-en Premium-lagen van Azure Service Bus beschreven. Vergelijkt deze lagen en biedt technische verschillen.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76774571"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Prijscategorieën voor Service Bus Premium en Standard Messaging

Service Bus Messaging, dat entiteiten zoals wachtrijen en onderwerpen omvat, combineert functies voor bedrijfsberichten met krachtige semantiek voor publiceren/abonneren in de cloud. Service Bus Messaging wordt gebruikt als de communicatie-backbone voor veel geavanceerde cloudoplossingen.

De *Premium*-laag van de Service Bus Messaging-service zorgt voor de afhandeling van algemene klantaanvragen met betrekking tot de schaal, prestaties en beschikbaarheid van essentiële toepassingen. Voor productiescenario's wordt de laag Premium aanbevolen. Hoewel de functiesets bijna identiek zijn, zijn deze twee lagen van de Service Bus Messaging-service ontworpen voor verschillende gebruiksscenario’s.

In de volgende tabel worden enkele belangrijke verschillen uitgelicht.

| Premium | Standard |
| --- | --- |
| Hoge doorvoersnelheid |Variabele doorvoersnelheid |
| Voorspelbare prestaties |Variabele latentie |
| Vaste prijzen |Variabel omslagstelsel voor betalen per gebruik |
| Mogelijkheid om de workload omhoog en omlaag te schalen |N.v.t. |
| Berichtformaat tot maximaal 1 MB |Berichtformaat tot maximaal 256 kB |

**Service Bus Premium Messaging** biedt isolatie van resources op het niveau van de CPU en het geheugen, zodat elke workload van een klant geïsoleerd wordt uitgevoerd. Deze resource container wordt een *Messa ging-eenheid*genoemd. Aan elke Premium-naamruimte wordt ten minste één Messaging-eenheid toegewezen. U kunt 1, 2, 4 of 8 Messa ging-eenheden kopen voor elke Service Bus Premium-naam ruimte. Eén werk belasting of entiteit kan meerdere Messa ging-eenheden omvatten en het aantal Messa ging-eenheden kan worden gewijzigd in. Dit resulteert in voorspelbare en herhaalbare prestaties voor uw Service Bus-oplossing.

Niet alleen zijn de prestaties beter voorspelbaar en beschikbaar, ze zijn ook sneller. Service Bus Premium-berichten worden gebaseerd op de opslag engine die is geïntroduceerd in [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/). Met de Premium-laag zijn de piekprestaties veel sneller dan met de Standard-laag.

## <a name="premium-messaging-technical-differences"></a>Technische verschillen Premium Messaging

In de volgende secties wordt een aantal verschillen besproken tussen Premium en Standard Messaging.

### <a name="partitioned-queues-and-topics"></a>Gepartitioneerde wachtrijen en onderwerpen

Gepartitioneerde wachtrijen en onderwerpen worden niet ondersteund in Premium Messaging. Zie [Gepartitioneerde wachtrijen en onderwerpen](service-bus-partitioning.md) voor meer informatie over partitioneren.

### <a name="express-entities"></a>Express-entiteiten

Omdat Premium Messaging wordt uitgevoerd in een volledig geïsoleerde runtime-omgeving, worden Express-entiteiten niet ondersteund in Premium-naamruimten. Zie de eigenschap [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) voor meer informatie over de Express-functie.

Als u code uitvoert onder Standard Messaging en deze wilt overzetten naar de Premium-prijscategorie, moet de eigenschap [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) zijn ingesteld op **false** (de standaardwaarde).

## <a name="premium-messaging-resource-usage"></a>Resource gebruik voor Premium Messa ging
Over het algemeen kan elke bewerking van een entiteit CPU-en geheugen gebruik veroorzaken. Hier volgen enkele van deze bewerkingen: 

- Beheer bewerkingen zoals ruwe (maken, ophalen, bijwerken en verwijderen) voor wacht rijen, onderwerpen en abonnementen.
- Runtime bewerkingen (berichten verzenden en ontvangen)
- Bewakings bewerkingen en-waarschuwingen

Het extra CPU-en geheugen gebruik is ook niet geprijsd. Voor de laag Premium Messa ging is er één prijs voor de bericht eenheid.

Het CPU-en geheugen gebruik worden bijgehouden en weer gegeven om de volgende redenen: 

- Transparantie van het systeem opgeven
- De capaciteit van de aangeschafte resources begrijpen.
- Capaciteits planning waarmee u omhoog/omlaag kunt schalen.

## <a name="messaging-unit---how-many-are-needed"></a>Messa ging-eenheid-hoeveel er nodig zijn?

Bij het inrichten van een Azure Service Bus Premium-naam ruimte moet het aantal toegewezen Messa ging-eenheden worden opgegeven. Deze Messa ging-eenheden zijn toegewezen resources die aan de naam ruimte worden toegewezen.

Het aantal Messa ging-eenheden dat aan de Service Bus Premium-naam ruimte wordt toegewezen, kan **dynamisch worden aangepast** aan de factor van de wijziging (verg Roten of verkleinen) in werk belastingen.

Er zijn een aantal factoren waarmee rekening moet worden gehouden bij het bepalen van het aantal Messa ging-eenheden voor uw architectuur:

- Begin met ***1 of 2 Messa ging-eenheden*** die aan uw naam ruimte zijn toegewezen.
- Onderzoek de metrische gegevens over het CPU-gebruik binnen de [metrische gegevens over het resource gebruik](service-bus-metrics-azure-monitor.md#resource-usage-metrics) voor uw naam ruimte.
    - Als het CPU-gebruik ***lager is dan 20%***, kunt u het aantal Messa ging-eenheden dat aan uw naam ruimte is toegewezen, wellicht ***omlaag schalen*** .
    - Als het CPU-gebruik ***hoger is dan 70%***, zal uw toepassing profiteren van het ***opschalen*** van het aantal Messa ging-eenheden dat aan uw naam ruimte is toegewezen.

Het proces voor het schalen van de resources die zijn toegewezen aan een Service Bus naam ruimten kunnen worden geautomatiseerd met behulp van [Azure Automation Runbooks](../automation/automation-quickstart-create-runbook.md).

> [!NOTE]
> Het **schalen** van de resources die zijn toegewezen aan de naam ruimte kan preventieve of reactiveren zijn.
>
>  * **Preventieve**: als er een extra werk belasting wordt verwacht (vanwege seizoensgebondenheid of trends), kunt u door gaan met het toewijzen van meer Messa ging-eenheden aan de naam ruimte voordat de werk belasting wordt bereikt.
>
>  * Opnieuw **actief**: als er extra werk belastingen worden geïdentificeerd door te studie van de metrische gegevens over het resource gebruik, kunnen er extra resources worden toegewezen aan de naam ruimte om de toenemende vraag op te nemen.
>
> De facturerings meters voor Service Bus zijn per uur. In het geval van omhoog schalen betaalt u alleen voor de extra resources voor het aantal uren dat deze zijn gebruikt.
>

## <a name="get-started-with-premium-messaging"></a>Aan de slag met Premium Messaging

U kunt snel aan de slag met Premium Messaging. Het proces is vergelijkbaar met dat van Standard Messaging. [Maak eerst een naamruimte](service-bus-create-namespace-portal.md) in [Azure Portal](https://portal.azure.com). Zorg ervoor dat bij **Prijscategorie** de optie **Premium** is geselecteerd. Klik op **Volledige prijsgegevens weergeven** voor meer informatie over de verschillende lagen.

![maken-premium-naamruimte][create-premium-namespace]

U kunt ook [Premium-naamruimtes maken met behulp van Azure Resource Manager-sjablonen](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende koppelingen voor meer informatie over de Service Bus Messaging-service:

* [Inleiding tot Azure Service Bus Premium Messa ging (blog bericht)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducing Azure Service Bus Premium messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Overzicht van Service Bus Messaging](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
