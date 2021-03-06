---
title: Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt
description: Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4c48a2fad927812cc45543243b48a2df81acf73b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846950"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Privé-eind punten gebruiken voor Azure-web-app (preview-versie)

> [!Note]
> Met de preview-versie van het vernieuwen hebben we de functie gegevens exfiltration Protection uitgebracht.
> Het voor beeld is beschikbaar in de regio's VS-Oost en VS-West 2 voor alle PremiumV2 van Windows en Linux Web Apps-en elastische Premium-functies. 

U kunt een privé-eind punt voor uw Azure-web-app gebruiken om clients die zich in uw particuliere netwerk bevinden, veilig toegang te geven tot de app via een persoonlijke koppeling. Het persoonlijke eind punt gebruikt een IP-adres uit uw Azure VNet-adres ruimte. Netwerk verkeer tussen een client op uw particuliere netwerk en de web-app passeren over het VNet en een persoonlijke koppeling in het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

Als u een persoonlijk eind punt gebruikt voor uw web-app, kunt u het volgende doen:

- Beveilig uw web-app door het persoonlijke eind punt te configureren, waardoor de open bare bloot stelling wordt geëlimineerd.
- Maak veilig verbinding met de web-app vanuit on-premises netwerken die verbinding maken met het VNet met behulp van een VPN-of ExpressRoute privé-peering.
- Vermijd gegevens exfiltration uit uw VNet. 

Als u alleen een beveiligde verbinding tussen uw VNet en uw web-app nodig hebt, is een service-eind punt de eenvoudigste oplossing. Als u de web-app ook van on-premises moet bereiken via een Azure-gateway, een regionaal gepeerd VNet of een globaal peered VNet, is de oplossing.  

Zie [service-eind punten][serviceendpoint]voor meer informatie.

## <a name="conceptual-overview"></a>Conceptueel overzicht

Een persoonlijk eind punt is een speciale netwerk interface (NIC) voor uw Azure-web-app in een subnet in uw Virtual Network (VNet).
Wanneer u een persoonlijk eind punt voor uw web-app maakt, biedt het een beveiligde verbinding tussen clients in uw particuliere netwerk en uw web-app. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw VNet.
De verbinding tussen het persoonlijke eind punt en de web-app maakt gebruik van een beveiligde [persoonlijke koppeling][privatelink]. Privé-eind punt wordt alleen gebruikt voor binnenkomende stromen naar uw web-app. Uitgaande stromen gebruiken dit persoonlijke eind punt niet, maar u kunt via de [VNet-integratie functie][vnetintegrationfeature]uitgaande stromen naar uw netwerk injecteren in een ander subnet.

Het subnet waar u het persoonlijke eind punt aansluit, kan andere resources bevatten, u hebt geen toegewezen leeg subnet nodig.
U kunt het persoonlijke eind punt ook implementeren in een andere regio dan de web-app. 

> [!Note]
>De VNet-integratie functie kan niet hetzelfde subnet gebruiken dan het persoonlijke eind punt. Dit is een beperking van de VNet-integratie functie.

Vanuit een beveiligings perspectief:

- Wanneer u privé-eind punten op uw web-app inschakelt, schakelt u alle open bare toegang uit.
- U kunt meerdere privé-eind punten inschakelen in andere VNets en subnetten, waaronder VNets in andere regio's.
- Het IP-adres van de NIC van het persoonlijke eind punt moet dynamisch zijn, maar blijft hetzelfde totdat u het persoonlijke eind punt verwijdert.
- Aan de NIC van het persoonlijke eind punt kan geen NSG zijn gekoppeld.
- Aan het subnet waarop het persoonlijke eind punt wordt gehost, kan een NSG zijn gekoppeld, maar u moet het afdwingen van het netwerk beleid uitschakelen voor het persoonlijke eind punt: Zie [netwerk beleid uitschakelen voor privé-eind punten][disablesecuritype]. Als gevolg hiervan kunt u niet filteren op alle NSG die toegang hebben tot uw persoonlijke eind punt.
- Wanneer u een persoonlijk eind punt op uw web-app inschakelt, wordt de configuratie van de [toegangs beperkingen][accessrestrictions] van de web-app niet geëvalueerd.
- U kunt het risico op gegevens exfiltration uit het VNet elimineren door alle NSG-regels te verwijderen waarbij de bestemming Internet of Azure-Services labelt. Wanneer u een persoonlijk eind punt voor een web-app implementeert, kunt u deze specifieke Web-app alleen bereiken via het persoonlijke eind punt. Als u een andere web-app hebt, moet u een ander speciaal privé-eind punt voor deze andere web-app implementeren.

In de web-HTTP-logboeken van uw web-app vindt u het bron-IP-adres van de client. Dit wordt geïmplementeerd met behulp van het TCP-proxy protocol, waarbij de IP-eigenschap van de client wordt doorgestuurd naar de web-app. Zie voor meer informatie [verbindings gegevens ophalen met TCP proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Globaal overzicht van het persoonlijke eind punt voor web-apps](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Omdat deze functie in preview is, wordt de DNS-vermelding niet gewijzigd tijdens de preview-versie. U moet de DNS-vermelding in uw privé-DNS-server of Azure DNS privé zone zelf beheren.
Als u een aangepaste DNS-naam moet gebruiken, moet u de aangepaste naam toevoegen in uw web-app. Tijdens de preview moet de aangepaste naam worden gevalideerd, zoals een aangepaste naam, met behulp van een open bare DNS-omzetting. Zie [Custom DNS Validation][dnsvalidation](Engelstalig) voor meer informatie.

Als u de kudu-console wilt gebruiken, of kudu REST API (bijvoorbeeld implementatie met Azure DevOps self-hosted agenten), moet u twee records maken in uw Azure DNS persoonlijke zone of uw aangepaste DNS-server. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>Prijzen

Zie [prijzen voor persoonlijke Azure-koppelingen][pricing]voor prijs informatie.

## <a name="limitations"></a>Beperkingen

Wanneer u de functie Azure gebruikt in een elastisch Premium-abonnement met een privé-eind punt, moet u directe toegang tot het netwerk hebben of u een HTTP 403-fout ontvangen om de functies in azure Web Portal uit te voeren of uit te voeren. Met andere woorden, uw browser moet het persoonlijke eind punt kunnen bereiken om de functie uit te voeren vanuit de Azure-webportal. 

Tijdens de preview-periode wordt alleen de productie sleuf weer gegeven achter het persoonlijke eind punt, moeten andere sleuven worden bereikt via een openbaar eind punt.

De functie voor persoonlijke koppelingen en het persoonlijke eind punt worden regel matig verbeterd. Raadpleeg [dit artikel][pllimitations] voor actuele informatie over beperkingen.

## <a name="next-steps"></a>Volgende stappen

Voor het implementeren van een persoonlijk eind punt voor uw web-app via de portal Zie [hoe u een persoonlijke verbinding maakt met een web-app][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
