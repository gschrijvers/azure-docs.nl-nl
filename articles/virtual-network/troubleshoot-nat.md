---
title: Problemen met Azure Virtual Network NAT-verbinding oplossen
titleSuffix: Azure Virtual Network
description: Problemen met Virtual Network NAT oplossen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2020
ms.author: allensu
ms.openlocfilehash: c9b5aaefeb8ab21eed850f5bf291d38981239aab
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508425"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Problemen met Azure Virtual Network NAT-verbinding oplossen

Dit artikel helpt beheerders bij het vaststellen en oplossen van verbindings problemen bij het gebruik van Virtual Network NAT.

## <a name="problems"></a>Treden

* [SNAT-uitputting](#snat-exhaustion)
* [ICMP-ping is mislukt](#icmp-ping-is-failing)
* [Connectiviteits fouten](#connectivity-failures)
* [IPv6-samen werking](#ipv6-coexistence)

Volg de stappen in de volgende sectie om deze problemen op te lossen.

## <a name="resolution"></a>Oplossing

### <a name="snat-exhaustion"></a>SNAT-uitputting

Eén [NAT-gateway resource](nat-gateway-resource.md) ondersteunt van 64.000 tot 1.000.000 gelijktijdige stromen.  Elk IP-adres levert een 64.000 SNAT-poort aan de beschik bare inventaris. U kunt Maxi maal 16 IP-adressen per NAT-gateway Resource gebruiken.  Het SNAT-mechanisme wordt [hier](nat-gateway-resource.md#source-network-address-translation) uitgebreid beschreven.

De belangrijkste oorzaak van de SNAT-uitputting is vaak een anti patroon voor de manier waarop uitgaande connectiviteit tot stand is gebracht, beheerde of configureer bare timers zijn gewijzigd ten opzichte van hun standaard waarden.  Lees deze sectie aandachtig.

#### <a name="steps"></a>Stappen

1. Controleer of u de standaard time-out voor inactiviteit hebt gewijzigd in een waarde die hoger is dan 4 minuten.
2. Onderzoek hoe uw toepassing uitgaande connectiviteit maakt (bijvoorbeeld code controle of pakket opname). 
3. Bepaal of deze activiteit verwacht gedrag is of of de toepassing niet goed werkt.  Gebruik [metrische gegevens](nat-metrics.md) in azure monitor om uw bevindingen te staven. Gebruik de categorie ' mislukt ' voor de metrische gegevens voor de SNAT-verbinding.
4. Evalueren of de juiste patronen worden gevolgd.
5. Evalueren of de SNAT-poort uitputting moet worden verminderd met extra IP-adressen die zijn toegewezen aan de NAT gateway-resource.

#### <a name="design-patterns"></a>Ontwerppatronen

Maak waar mogelijk altijd gebruik van verbindings hergebruik en verbindings Pools.  Deze patronen voor komen problemen met bron uitputting en leiden tot voorspelbaar gedrag. Primitieven voor deze patronen vindt u in veel ontwikkel bibliotheken en frameworks.

_**Oplossing:**_ Gebruik geschikte patronen en aanbevolen procedures

- NAT gateway-resources hebben een standaard-time-out voor TCP-inactiviteit van 4 minuten.  Als deze instelling wordt gewijzigd in een hogere waarde, houdt NAT over naar stromen en kan dit leiden tot [overbodige druk op de SNAT-poort inventaris](nat-gateway-resource.md#timers).
- Atomische aanvragen (één aanvraag per verbinding) zijn een slecht ontwerp keuze. Dergelijke Anti-patroon limieten schalen, verminderen de prestaties en verminderen de betrouw baarheid. Gebruik in plaats daarvan HTTP/S-verbindingen om het aantal verbindingen en de bijbehorende SNAT-poorten te verminderen. De schaal van de toepassing neemt toe en verbetert de prestaties als gevolg van lagere Handshakes, overhead en cryptografische bewerkings kosten bij gebruik van TLS.
- DNS kan veel afzonderlijke stromen op het volume introduceren wanneer de client het resultaat van de DNS-resolver niet in de cache opslaat. Gebruik caching.
- UDP-stromen (bijvoorbeeld DNS-zoek acties) wijzen SNAT-poorten toe voor de duur van de time-out voor inactiviteit. Hoe langer de time-out voor inactiviteit, hoe hoger de druk op de SNAT-poorten. Gebruik korte time-out voor inactiviteit (bijvoorbeeld 4 minuten).
- Gebruik verbindings Pools om uw verbindings volume te vorm geven.
- Verstrek nooit een TCP-stroom en vertrouw op TCP-timers om de stroom op te schonen. Als u TCP de verbinding niet expliciet laat sluiten, blijft de status toegewezen bij tussenliggende systemen en eind punten en worden er geen SNAT-poorten beschikbaar voor andere verbindingen. Hierdoor kunnen toepassings fouten en SNAT-uitputting worden geactiveerd. 
- Wijzig TCP close-gerelateerde timer waarden op besturingssysteem niveau niet zonder kennis van impact. Terwijl de TCP-stack wordt hersteld, kunnen de prestaties van uw toepassing negatief worden beïnvloed wanneer de eind punten van een verbinding niet overeenkomen met de verwachtingen. De wensen voor het wijzigen van timers zijn doorgaans een teken van een onderliggend ontwerp probleem. Bekijk de volgende aanbevelingen.

Vaak is het mogelijk dat SNAT-uitputting ook kan worden versterkt met andere anti-patronen in de onderliggende toepassing. Bekijk deze aanvullende patronen en aanbevolen procedures voor het verbeteren van de schaal en betrouw baarheid van uw service.

- Ontdek de gevolgen van het verminderen van de [time-out voor inactiviteit van TCP](nat-gateway-resource.md#timers) naar lagere waarden, inclusief de standaard time-out voor inactiviteit van 4 minuten om eerdere SNAT-poort inventaris vrij
- Denk aan [asynchrone polling-patronen](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) voor langlopende bewerkingen om verbindings bronnen vrij te maken voor andere bewerkingen.
- Lange levens stromen (bijvoorbeeld hergebruikte TCP-verbindingen) moeten TCP-keepalives of keepalives van de toepassingslaag gebruiken om time-out van tussenliggende systemen te voor komen. Het verg Roten van de time-out voor inactiviteit is een laatste redmiddel en kan de hoofd oorzaak mogelijk niet oplossen. Een lange time-out kan problemen met lage frequentie veroorzaken wanneer de time-out is verlopen en vertragings-en overbodige storingen veroorzaken.
- Geslaagde [patronen voor opnieuw proberen](https://docs.microsoft.com/azure/architecture/patterns/retry) moeten worden gebruikt om agressieve pogingen/bursts te voor komen tijdens tijdelijke storingen of herstel na storingen.
Het maken van een nieuwe TCP-verbinding voor elke HTTP-bewerking (ook wel ' atomische verbindingen ' genoemd) is een anti-patroon.  Met atomische verbindingen wordt voor komen dat uw toepassing goed kan worden geschaald en verspilt resources.  Meerdere bewerkingen in een pijp lijn altijd in dezelfde verbinding.  Uw toepassing zal profiteren van de transactie snelheid en resource kosten.  Als uw toepassing gebruikmaakt van trans port Layer encryption (bijvoorbeeld TLS), zijn er aanzienlijke kosten verbonden aan de verwerking van nieuwe verbindingen.  Bekijk de [ontwerp patronen voor Azure-Clouds](https://docs.microsoft.com/azure/architecture/patterns/) voor aanvullende best practice patronen.

#### <a name="additional-possible-mitigations"></a>Aanvullende mogelijke oplossingen

_**Oplossing:**_ Schaal de uitgaande verbinding als volgt:

| Scenario | Voorgelegd |Oplossing |
|---|---|---|
| U ondervindt conflicten voor SNAT-poorten en SNAT-poort uitputting tijdens peri Oden met een hoog gebruik. | De categorie ' mislukt ' voor de [Azure monitor van de SNAT-verbindingen bevat](nat-metrics.md) tijdelijke of permanente storingen in de loop van de tijd en het hoge verbindings volume.  | Bepaal of u meer open bare IP-adres bronnen of open bare IP-prefix bronnen kunt toevoegen. Met deze toevoeging kunnen Maxi maal 16 IP-adressen in totaal naar uw NAT-gateway worden toegevoegd. Deze toevoeging biedt meer inventarisatie voor de beschik bare SNAT-poorten (64.000 per IP-adres) en stelt u in staat om uw scenario verder te schalen.|
| U hebt al 16 IP-adressen opgegeven en er is nog steeds een SNAT-poort uitgeput. | Poging om extra IP-adres toe te voegen is mislukt. Het totale aantal IP-adressen van open bare IP-adres bronnen of open bare IP-voor voegsel resources overschrijdt het totaal van 16. | Distribueer uw toepassings omgeving over meerdere subnetten en geef een NAT-gateway bron op voor elk subnet.  Evalueer uw ontwerp patroon (en) opnieuw om te optimaliseren op basis van voor gaande [richt lijnen](#design-patterns). |

>[!NOTE]
>Het is belang rijk om te begrijpen waarom de uitputting van de SNAT optreedt. Zorg ervoor dat u de juiste patronen gebruikt voor schaal bare en betrouw bare scenario's.  Het toevoegen van meer SNAT-poorten aan een scenario zonder dat de oorzaak van de vraag een laatste redmiddel moet zijn. Als u geen inzicht krijgt in waarom uw scenario druk op de SNAT-poort inventariseert, kunt u meer SNAT-poorten toevoegen aan de inventaris door meer IP-adressen toe te voegen, waardoor er slechts één uitputtings fout optreedt wanneer uw toepassing wordt geschaald.  U kunt andere inefficiënties en anti-patronen maskeren.

### <a name="icmp-ping-is-failing"></a>ICMP-ping is mislukt

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP-en TCP-protocollen. ICMP wordt niet ondersteund en wordt naar verwachting mislukt.  

_**Oplossing:**_ Gebruik in plaats daarvan TCP-verbindings tests (bijvoorbeeld ' TCP ping ') en UDP-specifieke Application Layer-tests om end-to-end-verbindingen te valideren.

De volgende tabel kan worden gebruikt als uitgangs punt voor de hulpprogram ma's die u wilt gebruiken om tests te starten.

| Besturingssysteem | Algemene TCP-verbindings test | Test van de TCP-toepassingslaag | UDP |
|---|---|---|---|
| Linux | NC (algemene verbindings test) | krul (test voor TCP-toepassingslaag) | toepassingsspecifieke |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Power shell [invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | toepassingsspecifieke |

### <a name="connectivity-failures"></a>Connectiviteits fouten

Verbindings problemen met [Virtual Network NAT](nat-overview.md) kunnen worden veroorzaakt door verschillende problemen:

* permanente fouten vanwege configuratie fouten.
* tijdelijke of permanente [SNAT-uitputting](#snat-exhaustion) van de NAT-gateway,
* tijdelijke fouten in de Azure-infra structuur, 
* tijdelijke fouten in het pad tussen Azure en de open bare Internet bestemming 
* tijdelijke of blijvende storingen op de open bare Internet bestemming.

Gebruik hulpprogram ma's zoals het volgende voor het valideren van de verbinding. [ICMP ping wordt niet ondersteund](#icmp-ping-is-failing).

| Besturingssysteem | Algemene TCP-verbindings test | Test van de TCP-toepassingslaag | UDP |
|---|---|---|---|
| Linux | NC (algemene verbindings test) | krul (test voor TCP-toepassingslaag) | toepassingsspecifieke |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Power shell [invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | toepassingsspecifieke |

#### <a name="configuration"></a>Configuratie

Controleer het volgende:
1. Heeft de NAT-gateway bron ten minste één open bare IP-bron of een open bare IP-prefix resource? Er moet ten minste één IP-adres zijn gekoppeld aan de NAT-gateway om uitgaande connectiviteit mogelijk te maken.
2. Is het subnet van het virtuele netwerk geconfigureerd voor het gebruik van de NAT-gateway?
3. Gebruikt u UDR (door de gebruiker gedefinieerde route) en wordt de bestemming overschreven?  NAT-gateway bronnen worden de standaard route (0/0) op geconfigureerde subnetten.

#### <a name="snat-exhaustion"></a>SNAT-uitputting

Raadpleeg de sectie over de [SNAT-uitputting](#snat-exhaustion) in dit artikel.

#### <a name="azure-infrastructure"></a>Azure-infrastructuur

Azure bewaakt de infra structuur en werkt deze met geweldige zorg. Tijdelijke fouten kunnen optreden, er is geen garantie dat de verzen ding zonder verlies is.  Gebruik ontwerp patronen waarmee SYN-herverzendingen voor TCP-toepassingen worden toegestaan. Gebruik verbindingstime-outs groot genoeg om het opnieuw verzenden van TCP SYN toe te staan om tijdelijke gevolgen te beperken die worden veroorzaakt door een verloren SYN-pakket.

_**Oplossen**_

* Controleer op [SNAT-uitputting](#snat-exhaustion).
* De configuratie parameter in een TCP-stack waarmee het gedrag van SYN-verzen ding wordt beheerd, wordt RTO ([time-out voor herverzending](https://tools.ietf.org/html/rfc793)) genoemd. De RTO-waarde is aanpasbaar maar meestal 1 seconde of hoger, standaard met exponentiële back-out.  Als de verbindingstime-out van uw toepassing te kort is (bijvoorbeeld 1 seconde), ziet u mogelijk sporadische verbindingstime-outs.  Verhoog de time-out voor de verbinding van de toepassing.
* Als u meer wilt weten, onverwachte time-outs met standaard gedrag van toepassingen, opent u een ondersteunings aanvraag voor verdere probleem oplossing.

Het wordt niet aanbevolen om de time-out van de TCP-verbinding kunst matig te verlagen of de para meter RTO af te stemmen.

#### <a name="public-internet-transit"></a>Openbaar Internet doorvoer

De kans op tijdelijke fouten neemt toe met een langer pad naar het doel en meer tussenliggende systemen. Er wordt verwacht dat tijdelijke fouten de frequentie van Azure- [infra structuur](#azure-infrastructure)kunnen verhogen. 

Volg dezelfde richt lijnen als voor gaande [Azure-infrastructuur](#azure-infrastructure) sectie.

#### <a name="internet-endpoint"></a>Internet-eind punt

De vorige secties zijn van toepassing, samen met het Internet-eind punt waarmee communicatie tot stand is gebracht. Andere factoren die invloed kunnen hebben op succes van de verbinding zijn:

* verkeers beheer aan de kant van de bestemming, inclusief
- Beperking van API-frequentie die is opgelegd door de doel zijde
- Volumetrische DDoS-oplossingen of trans port Layer Traffic Shaping
* firewall of andere onderdelen op het doel 

Doorgaans worden pakket opnames bij de bron en de bestemming (indien beschikbaar) vereist om te bepalen wat er gebeurt.

_**Oplossen**_

* Controleer op [SNAT-uitputting](#snat-exhaustion). 
* Valideer de verbinding met een eind punt in dezelfde regio of elders ter vergelijking.  
* Als u een hoog volume-of transactie verwerkings tempo maakt, moet u verkennen als het aantal fouten vermindert door te verminderen.
* Als het wijzigen van de frequentie invloed heeft op de frequentie van fouten, controleert u of de API-frequentie limieten of andere beperkingen voor de doel zijde mogelijk zijn bereikt.
* Als uw onderzoek onduidelijk is, opent u een ondersteunings aanvraag voor verdere probleem oplossing.

#### <a name="tcp-resets-received"></a>TCP-opnieuw instellen ontvangen

De NAT-gateway genereert TCP-resets op de bron-VM voor verkeer dat niet wordt herkend als in uitvoering.

Een mogelijke reden is dat er een time-out is opgetreden voor de TCP-verbinding.  U kunt de time-out voor inactiviteit aanpassen van 4 minuten tot 120 minuten.

TCP-resets worden niet gegenereerd aan de open bare kant van NAT-gateway bronnen. TCP-resets aan de doel zijde worden gegenereerd door de bron-VM, niet via de gateway bron van de NAT.

_**Oplossen**_

* Bekijk aanbevelingen voor [ontwerp patronen](#design-patterns) .  
* Open een ondersteunings aanvraag voor het oplossen van problemen, indien nodig.

### <a name="ipv6-coexistence"></a>IPv6-samen werking

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP-en TCP-protocollen en implementatie op een [subnet met een IPv6-voor voegsel wordt niet ondersteund](nat-overview.md#limitations).

_**Oplossing:**_ Implementeer NAT gateway op een subnet zonder IPv6-voor voegsel.

U kunt interesses aanduiden in aanvullende mogelijkheden via [Virtual Network NAT UserVoice](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Virtual Network NAT](nat-overview.md)
* Meer informatie over de [NAT gateway-resource](nat-gateway-resource.md)
* Meer informatie over [metrische gegevens en waarschuwingen voor NAT-gateway bronnen](nat-metrics.md).
* [Vertel ons wat u nu kunt bouwen voor Virtual Network nat in UserVoice](https://aka.ms/natuservoice).

