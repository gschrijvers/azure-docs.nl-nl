---
title: Persoonlijke koppeling-Azure Database for MariaDB
description: Meer informatie over de werking van een persoonlijke koppeling voor Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b05a202537492fe54a76cf40a3b15987e099a7e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79367717"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Persoonlijke koppeling voor Azure Database for MariaDB

Met persoonlijke koppeling kunt u privé-eind punten maken voor Azure Database for MariaDB en dus Azure-Services binnen uw privé Virtual Network (VNet). Met het persoonlijke eind punt wordt een privé-IP-adres weer gegeven dat u kunt gebruiken om verbinding te maken met uw Azure Database for MariaDB database server net als elke andere resource in het VNet.

Raadpleeg de [documentatie](https://docs.microsoft.com/azure/private-link/index)van de privé-koppeling voor een lijst met PaaS-services die ondersteuning bieden voor persoonlijke koppelings functionaliteit. Een persoonlijk eind punt is een privé-IP-adres binnen een specifiek [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) en subnet.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for MariaDB de prijs categorieën voor Algemeen en geoptimaliseerd voor geheugen ondersteunt.

## <a name="data-exfiltration-prevention"></a>Preventie van gegevens exfiltration

Gegevens die worden gefilterd in Azure Database for MariaDB, zijn wanneer een geautoriseerde gebruiker, zoals een database beheerder, gegevens uit het ene systeem kan ophalen en verplaatsen naar een andere locatie of een ander systeem buiten de organisatie. De gebruiker verplaatst bijvoorbeeld de gegevens naar een opslag account dat eigendom is van een derde partij.

Overweeg een scenario met een gebruiker die MariaDB workbench in een Azure-VM gebruikt en verbinding maakt met een Azure Database for MariaDB-exemplaar. Dit MariaDB-exemplaar bevindt zich in het VS-West-Data Center. In het onderstaande voor beeld ziet u hoe u de toegang tot de open bare eind punten op Azure Database for MariaDB met behulp van besturings elementen voor netwerk toegang kunt beperken.

* Schakel alle Azure-service verkeer uit voor Azure Database for MariaDB via het open bare eind punt door de instelling Azure-Services toestaan in te scha kelen. Zorg ervoor dat er geen IP-adressen of bereiken toegang hebben tot de server via [firewall regels](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) of [virtuele netwerk service-eind punten](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet).

* Alleen verkeer naar het Azure Database for MariaDB toestaan met behulp van het privé-IP-adres van de virtuele machine. Zie de artikelen over [service-eind punten](concepts-data-access-security-vnet.md) en VNet- [firewall regels](howto-manage-vnet-portal.md)voor meer informatie.

* Verfijn op de virtuele Azure-machine het bereik van de uitgaande verbinding door netwerk beveiligings groepen (Nsg's) en service tags als volgt te gebruiken:

    * Geef een NSG-regel op om verkeer voor service label = SQL toe te staan. Westus: alleen verbinding toestaan met Azure Database for MariaDB in VS-West
    * Geef een NSG-regel (met een hogere prioriteit) op om verkeer te weigeren voor service Tags = SQL-verbindingen met een MariaDB-data base in alle regio's weigeren</br></br>

Aan het einde van deze installatie kan de virtuele machine van Azure alleen verbinding maken met Azure Database for MariaDB in de regio vs-West. De connectiviteit is echter niet beperkt tot één Azure Database for MariaDB. De virtuele machine kan nog steeds verbinding maken met een Azure Database for MariaDB in de regio vs-West, met inbegrip van de data bases die geen onderdeel zijn van het abonnement. Hoewel we het bereik van de gegevens exfiltration in het bovenstaande scenario naar een bepaalde regio hebben gereduceerd, hebben we het niet geheel verwijderd.</br>

Met persoonlijke koppeling kunt u nu netwerk toegangs beheer instellen, zoals Nsg's, zodat de toegang tot het persoonlijke eind punt wordt beperkt. Afzonderlijke Azure PaaS-resources worden vervolgens toegewezen aan specifieke privé-eind punten. Een kwaadwillende Insider heeft alleen toegang tot de toegewezen PaaS-resource (bijvoorbeeld een Azure Database for MariaDB) en geen andere resource.

## <a name="on-premises-connectivity-over-private-peering"></a>On-premises connectiviteit via persoonlijke peering

Wanneer u verbinding maakt met het open bare eind punt vanaf de on-premises machines, moet uw IP-adres worden toegevoegd aan de op IP gebaseerde firewall met behulp van een firewall regel op server niveau. Hoewel dit model goed werkt voor het toestaan van toegang tot afzonderlijke machines voor ontwikkel-of test werkbelastingen, is het moeilijk te beheren in een productie omgeving.

Met persoonlijke koppeling kunt u cross-premises toegang tot het privé-eind punt inschakelen met behulp van [Express route](https://azure.microsoft.com/services/expressroute/) (er), persoonlijke peering of [VPN-tunnel](https://docs.microsoft.com/azure/vpn-gateway/). Ze kunnen vervolgens alle toegang uitschakelen via het open bare eind punt en de op IP gebaseerde firewall niet gebruiken.

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Persoonlijke koppeling voor Azure Database for MariaDB configureren

### <a name="creation-process"></a>Proces maken

Privé-eind punten zijn vereist om een persoonlijke koppeling in te scha kelen. U kunt dit doen met behulp van de volgende hand leidingen.

* [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Goedkeurings proces

Zodra de netwerk beheerder het persoonlijke eind punt (PE) heeft gemaakt, kan de beheerder de verbinding met het privé-eind punt (PEC) met Azure Database for MariaDB beheren. Deze schei ding van taken tussen de netwerk beheerder en de DBA is handig voor het beheer van de Azure Database for MariaDB-verbinding. 

* Navigeer naar de Azure Database for MariaDB Server-Resource in de Azure Portal. 
    * Selecteer de verbindingen met het privé-eind punt in het linkerdeel venster
    * Geeft een lijst weer van alle privé-eindpunt verbindingen (PECs)
    * Er is een overeenkomend persoonlijk eind punt (PE) gemaakt

![de portal voor het persoonlijke eind punt selecteren](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecteer een individuele PEC uit de lijst door deze te selecteren.

![de goed keuring van het privé-eind punt in behandeling selecteren](media/concepts-data-access-and-security-private-link/select-private-link.png)

* De beheerder van de MariaDB-server kan ervoor kiezen om een PEC goed te keuren of af te wijzen en eventueel een korte tekst reactie toe te voegen.

![het bericht van het privé-eind punt selecteren](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Na goed keuring of weigering wordt in de lijst de juiste staat en de antwoord tekst weer gegeven

![de eind status van het privé-eind punt selecteren](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Cases van een persoonlijke koppeling gebruiken voor Azure Database for MariaDB

Clients kunnen verbinding maken met het persoonlijke eind punt van hetzelfde VNet, gepeerd VNet in dezelfde regio, of via VNet-naar-VNet-verbindingen tussen regio's. Daarnaast kunnen clients via on-premises verbinding maken met behulp van ExpressRoute, persoonlijke peering of VPN-tunneling. Hieronder ziet u een vereenvoudigd diagram waarin de algemene gebruiks voorbeelden worden weer gegeven.

![het overzicht van het persoonlijke eind punt selecteren](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Verbinding maken vanaf een Azure-VM in een peered Virtual Network (VNet)
Configureer [VNet-peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) om verbinding te maken met de Azure database for MariaDB van een Azure-vm in een gekoppeld VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Verbinding maken vanaf een virtuele machine van Azure in VNet-naar-VNet-omgeving
Configureer de [vnet-naar-VNet VPN-gateway verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) om verbinding te maken met een Azure database for MariaDB van een Azure-vm in een andere regio of een ander abonnement.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Verbinding maken vanuit een on-premises omgeving via VPN
Als u verbinding wilt maken vanuit een on-premises omgeving met de Azure Database for MariaDB, kiest en implementeert u een van de volgende opties:

* [Punt-naar-site-verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Site-naar-site-VPN-verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privé koppeling gecombineerd met firewall regels

De volgende situaties en resultaten zijn mogelijk wanneer u een persoonlijke koppeling gebruikt in combi natie met firewall regels:

* Als u geen firewall regels configureert, heeft geen verkeer standaard toegang tot de Azure Database for MariaDB.

* Als u openbaar verkeer of een service-eind punt configureert en u persoonlijke eind punten maakt, worden verschillende soorten binnenkomend verkeer geautoriseerd door het bijbehorende type firewall regel.

* Als u geen openbaar verkeer of service-eind punt configureert en u persoonlijke eind punten maakt, is de Azure Database for MariaDB alleen toegankelijk via de persoonlijke eind punten. Als u geen openbaar verkeer of een service-eind punt configureert, hebben geen verkeer meer toegang tot de Azure Database for MariaDB nadat alle goedgekeurde privé-eind punten zijn afgewezen of verwijderd.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Open bare toegang weigeren voor Azure Database for MariaDB

Als u volledig alleen wilt vertrouwen op privé-eind punten voor toegang tot hun Azure Database for MariaDB, kunt u het instellen van alle open bare eind punten ([firewall regels](concepts-firewall-rules.md) en [VNet-service-eind punten](concepts-data-access-security-vnet.md)) uitschakelen door de configuratie voor het **weigeren van open bare netwerk toegang** op de database server in te stellen. 

Als deze instelling is ingesteld op *Ja*, zijn alleen verbindingen via persoonlijke eind punten toegestaan voor uw Azure database for MariaDB. Als deze instelling is ingesteld op *Nee*, kunnen clients verbinding maken met uw Azure database for MariaDB op basis van de instellingen van uw firewall of VNet-service-eind punten. Wanneer de waarde van de toegang tot het particuliere netwerk eenmaal is ingesteld, kunt u ook bestaande firewall-en VNet-service-eindpunt regels toevoegen en/of bijwerken.

> [!Note]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for PostgreSQL-één server ondersteunt de prijs categorieën voor Algemeen en geoptimaliseerd voor geheugen.
>
> Deze instelling heeft geen invloed op de SSL-en TLS-configuraties voor uw Azure Database for MariaDB.

Zie voor meer informatie over het instellen van de **toegang van open bare netwerk** voor uw Azure Database for MariaDB van Azure portal voor het [configureren van open bare netwerk toegang weigeren](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure Database for MariaDB beveiligings functies:

* Zie [firewall ondersteuning](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)als u een firewall wilt configureren voor Azure database for MariaDB.

* Zie [toegang vanaf virtuele netwerken configureren](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)voor meer informatie over het configureren van een service-eind punt voor een virtueel netwerk voor uw Azure database for MariaDB.

* Zie [Azure database for MariaDB Connectivity Architecture](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture) (Engelstalig) voor een overzicht van Azure database for MariaDB connectiviteit
