---
title: Overzicht van Azure Operational Security | Microsoft Docs
description: Dit artikel biedt een overzicht van de operationele beveiliging van Azure.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 00a71fec9c0bfc1db45eee7129b7c2a8adaef0fa
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674243"
---
# <a name="azure-operational-security-overview"></a>Overzicht van Azure Operational Security

[Azure Operational Security](/azure/security/fundamentals/operational-security) heeft betrekking op de services, besturings elementen en functies die beschikbaar zijn voor gebruikers voor het beveiligen van hun gegevens, toepassingen en andere assets in Microsoft Azure. Het is een framework dat de kennis bevat die is opgedaan via verschillende mogelijkheden die uniek zijn voor micro soft. Deze mogelijkheden zijn onder andere de micro soft Security Development Lifecycle (SDL), het micro soft Security Response Center-programma en het diepe bewustzijn van de Cyber beveiliging Threat landschap.

## <a name="azure-management-services"></a>Azure-beheer Services

Een IT-team is verantwoordelijk voor het beheer van de infra structuur, toepassingen en gegevens van data centers, met inbegrip van de stabiliteit en beveiliging van deze systemen. Het verkrijgen van beveiligings inzichten in toenemende complexe IT-omgevingen vergt echter vaak dat organisaties gegevens uit meerdere beveiligings-en beheer systemen cobble.

[Microsoft Azure controle logboeken](/azure/operations-management-suite/operations-management-suite-overview) is een op de cloud gebaseerde IT-beheer oplossing waarmee u uw on-premises en Cloud infrastructuur kunt beheren en beveiligen. De kern functionaliteit wordt verschaft door de volgende services die worden uitgevoerd in Azure. Azure bevat meerdere services die u helpen bij het beheren en beveiligen van uw on-premises en Cloud infrastructuur. Elke service biedt een specifieke beheer functie. U kunt Services combi neren om verschillende beheer scenario's te krijgen. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](/azure/azure-monitor/overview) verzamelt gegevens van beheerde bronnen in centrale gegevens archieven. Deze gegevens kunnen gebeurtenissen, prestatie gegevens of aangepaste gegevens bevatten die via de API worden geleverd. Nadat de gegevens zijn verzameld, is deze beschikbaar voor waarschuwingen, analyses en export.

U kunt gegevens uit verschillende bronnen consolideren en gegevens uit uw Azure-Services combi neren met uw bestaande on-premises omgeving. Azure Monitor logboeken maakt ook de verzameling van de gegevens duidelijk gescheiden van de actie die op die gegevens is uitgevoerd, zodat alle acties beschikbaar zijn voor alle soorten gegevens.

### <a name="automation"></a>Automation

[Azure Automation](/azure/automation/automation-intro) biedt u een manier om de hand matige, langlopende, fout gevoelige en regel matig herhaalde taken te automatiseren die vaak worden uitgevoerd in een Cloud en bedrijfs omgeving. Het bespaart tijd en verhoogt de betrouw baarheid van beheer taken. Deze taken worden zelfs zo gepland dat deze automatisch met regel matige tussen pozen worden uitgevoerd. U kunt processen automatiseren door gebruik te maken van runbooks of configuratie beheer automatiseren door gebruik te maken van de desired state Configuration.

### <a name="backup"></a>Backup

[Azure backup](/azure/backup/backup-introduction-to-azure-backup) is de Azure-service die u kunt gebruiken om een back-up te maken van (of te beveiligen) en om uw gegevens in de Microsoft Cloud te herstellen. Azure Backup vervangt uw bestaande on-premises of off-site back-upoplossing met een Cloud oplossing die betrouwbaar, veilig en voordelig is.

Azure Backup biedt onderdelen die u downloadt en implementeert op de juiste computer of server of in de Cloud. Welk onderdeel, of welke agent, u implementeert, is afhankelijk van wat u wilt beveiligen. Alle Azure Backup onderdelen (of u nu gegevens op locatie of in de Cloud beveiligt) kunnen worden gebruikt om een back-up te maken van gegevens in een Azure Recovery Services-kluis in Azure.

Zie de [tabel met Azure backup onderdelen](/azure/backup/backup-overview#what-can-i-back-up)voor meer informatie.

### <a name="site-recovery"></a>Siteherstel

[Azure site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) biedt bedrijfs continuïteit door de replicatie van on-premises virtuele en fysieke machines naar Azure of een secundaire site te organiseren. Als uw primaire site niet beschikbaar is, voert u een failover uit naar de secundaire locatie zodat gebruikers kunnen blijven werken. U kunt geen failback uitvoeren wanneer de systemen retour neren naar de werk volgorde. Gebruik Azure Security Center om intelligente en efficiënte detectie van bedreigingen uit te voeren.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) is een uitgebreide identiteits service die:

-   Hiermee schakelt u het gebruik van identiteits-en toegangs beheer (IAM) in als een Cloud service.
-   Biedt centraal toegangs beheer, eenmalige aanmelding (SSO) en rapportage.
-   Biedt ondersteuning voor geïntegreerd toegangs beheer voor [duizenden toepassingen](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) op de Azure Marketplace, waaronder Sales Force, Google Apps, box en concur.

Azure AD omvat ook een volledige reeks [mogelijkheden voor identiteits beheer](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), waaronder:

- [Multi-factor Authentication](/azure/multi-factor-authentication/multi-factor-authentication)
- [Wachtwoordbeheer via selfservice](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Self-service groeps beheer](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Privileged account management](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Op rollen gebaseerd toegangsbeheer](/azure/role-based-access-control/overview)
- [Bewaking van toepassings gebruik](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Uitgebreide controle](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Beveiligings bewaking en waarschuwingen](/azure/operations-management-suite/oms-security-responding-alerts)

Met Azure Active Directory hebben alle toepassingen die u publiceert voor uw partners en klanten (bedrijf of consument) dezelfde mogelijkheden voor identiteits-en toegangs beheer. Zo kunt u uw operationele kosten aanzienlijk verlagen.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](/azure/security-center/security-center-intro) helpt u bedreigingen te voor komen, te detecteren en erop te reageren met verbeterde zicht baarheid van de beveiliging van uw Azure-resources (en controle over). Het biedt geïntegreerde beveiligings bewaking en beleids beheer in uw abonnementen. Het helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt en werkt met een uitgebreid ecosysteem van beveiligings oplossingen.

[Bescherm gegevens van virtuele machines (VM)](/azure/security-center/security-center-linux-virtual-machine) in azure door inzicht te krijgen in de beveiligings instellingen van de virtuele machine en de bewaking van bedreigingen. Security Center kan uw virtuele machines controleren op:

- Beveiligings instellingen van het besturings systeem met de aanbevolen configuratie regels.
- Systeem beveiliging en essentiële updates die ontbreken.
- Aanbevelingen voor Endpoint Protection.
- Validatie van schijf versleuteling.
- Aanvallen op het netwerk.

Security Center maakt gebruik [van op rollen gebaseerde Access Control (RBAC)](/azure/role-based-access-control/role-assignments-portal). RBAC biedt [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Security Center evalueert de configuratie van uw resources om beveiligings problemen en beveiligings lekken te identificeren. In Security Center ziet u alleen informatie met betrekking tot een resource als u de rol van eigenaar, bijdrager of lezer hebt toegewezen aan het abonnement of de resource groep waarvan een resource deel uitmaakt.

>[!Note]
>Zie voor meer informatie over rollen en toegestane acties in Security Center [machtigingen in azure Security Center](/azure/security-center/security-center-permissions).

Security Center maakt gebruik van micro soft monitoring agent. Dit is dezelfde agent die wordt gebruikt door de Azure Monitor-service. Gegevens die worden verzameld van deze agent worden opgeslagen in een bestaande Log Analytics [werk ruimte](/azure/log-analytics/log-analytics-manage-access) die is gekoppeld aan uw Azure-abonnement of een nieuwe werk ruimte, rekening houdend met de geolocatie van de virtuele machine.

## <a name="azure-monitor"></a>Azure Monitor

Prestatie problemen in uw Cloud-app kunnen van invloed zijn op uw bedrijf. Met meerdere onderling verbonden onderdelen en veelvuldige releases kunnen degradatie op elk gewenst moment plaatsvinden. En als u een App ontwikkelt, ontdekken uw gebruikers doorgaans problemen die u niet hebt gevonden tijdens het testen. U moet onmiddellijk op de hoogte zijn van deze problemen en u moet beschikken over hulpprogram ma's voor het vaststellen en oplossen van de problemen.

[Azure monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) is een basis programma voor het controleren van services die worden uitgevoerd op Azure. Het biedt u gegevens op infrastructuur niveau over de door Voer van een service en de omgeving die erop van toepassing is. Als u uw apps in azure beheert en u beslist of u resources omhoog of omlaag wilt schalen, Azure Monitor de plaats van start.

U kunt ook bewakings gegevens gebruiken om grondige inzichten over uw toepassing te krijgen. Deze kennis kan u helpen bij het verbeteren van de prestaties of het onderhoud van toepassingen, of het automatiseren van acties waarvoor anders hand matige interventie nodig zou zijn.

Azure Monitor bevat de volgende onderdelen.

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek

Het [Azure-activiteiten logboek](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) biedt inzicht in de bewerkingen die zijn uitgevoerd voor de resources in uw abonnement. Het was voorheen bekend als ' controle logboek ' of ' operationeel logboek ', omdat het controle-vlak gebeurtenissen voor uw abonnementen rapporteert.

### <a name="azure-diagnostic-logs"></a>Diagnostische logboeken in Azure

[Diagnostische logboeken van Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) worden verzonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die resource. De inhoud van deze logboeken is afhankelijk van het bron type.

Windows-gebeurtenis systeem logboeken zijn een categorie van Diagnostische logboeken voor Vm's. BLOB-, tabel-en wachtrij logboeken zijn categorieën van Diagnostische logboeken voor opslag accounts.

Diagnostische logboeken verschillen van het [activiteiten logboek](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Het activiteiten logboek biedt inzicht in de bewerkingen die zijn uitgevoerd voor de resources in uw abonnement. Diagnostische logboeken bieden inzicht in bewerkingen die door uw resource worden uitgevoerd.

### <a name="metrics"></a>Metrische gegevens

Azure Monitor biedt telemetrie waarmee u inzicht krijgt in de prestaties en status van uw workloads op Azure. Het belangrijkste type Azure-telemetriegegevens is de [metrische](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) gegevens (ook wel prestatie meters genoemd) die worden verzonden door de meeste Azure-resources. Azure Monitor biedt verschillende manieren om deze metrische gegevens te configureren en te gebruiken voor bewaking en probleem oplossing.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure Diagnostics kunt het verzamelen van diagnostische gegevens op een geïmplementeerde toepassing. U kunt de diagnostische uitbrei ding van verschillende bronnen gebruiken. Dit wordt momenteel ondersteund: [Azure-Cloud service rollen](/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuele Azure-machines](/azure/vs-azure-tools-configure-roles-for-cloud-service) met micro soft Windows en [Azure service Fabric](/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Klanten bouwen een end-to-end netwerk in azure door afzonderlijke netwerk bronnen, zoals virtuele netwerken, Azure ExpressRoute, Azure-toepassing gateway en load balancers, te organiseren en samen te stellen. Bewaking is beschikbaar op elk van de netwerk bronnen.

Het end-to-end-netwerk kan complexe configuraties en interacties tussen bronnen hebben. Het resultaat is complexe scenario's waarvoor op scenario's gebaseerde bewaking via [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview)is vereist.

Network Watcher vereenvoudigt het bewaken en onderzoeken van uw Azure-netwerk. U kunt de diagnostische en visualisatie hulpprogramma's in Network Watcher gebruiken om het volgende te doen:

- Neem externe pakket opnames op een virtuele machine van Azure.
- Krijg inzicht in uw netwerk verkeer met behulp van stroom Logboeken.
- Diagnose van Azure VPN Gateway en-verbindingen.

Network Watcher heeft momenteel de volgende mogelijkheden:

- [Topologie](/azure/network-watcher/network-watcher-topology-overview): biedt een overzicht van de verschillende interconnects en koppelingen tussen netwerk bronnen in een resource groep.
- [Variabele pakket opname](/azure/network-watcher/network-watcher-packet-capture-overview): Hiermee worden pakket gegevens in en uit een virtuele machine vastgelegd. Geavanceerde filteropties en verfijnde besturingselementen, zoals de mogelijkheid om tijd- en groottebeperkingen in te stellen, bieden flexibiliteit. De pakket gegevens kunnen worden opgeslagen in een BLOB-archief of op de lokale schijf met de indeling. Cap.
- [IP-stroom controleren](/azure/network-watcher/network-watcher-ip-flow-verify-overview): controleert of een pakket wordt toegestaan of geweigerd op basis van 5-tuple Packet-para meters voor stroom informatie (doel-IP, bron-IP, doel poort, bron poort en Protocol). Als een beveiligings groep het pakket weigert, worden de regel en de groep die het pakket heeft geweigerd, geretourneerd.
- [Volgende hop](/azure/network-watcher/network-watcher-next-hop-overview): bepaalt de volgende hop voor pakketten die worden gerouteerd in de Azure-netwerk infrastructuur, zodat u eventuele onjuist geconfigureerde door de gebruiker gedefinieerde routes kunt vaststellen.
- [Weer gave van beveiligings groep](/azure/network-watcher/network-watcher-security-group-view-overview): Hiermee worden de effectief en toegepaste beveiligings regels opgehaald die op een virtuele machine worden toegepast.
- [NSG-stroom logboeken voor netwerk beveiligings groepen](/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Hiermee kunt u Logboeken vastleggen die betrekking hebben op verkeer dat is toegestaan of geweigerd door de beveiligings regels in de groep. De stroom is gedefinieerd met 5-tuple-informatie: bron-IP, doel-IP, bron poort, doel poort en protocol.
- [Probleem oplossing voor virtuele netwerk gateway en verbinding](/azure/network-watcher/network-watcher-troubleshoot-manage-rest): biedt de mogelijkheid om problemen met virtuele netwerk gateways en verbindingen op te lossen.
- [Limieten voor netwerk abonnementen](/azure/network-watcher/network-watcher-monitoring-overview): Hiermee kunt u het gebruik van netwerk bronnen weer geven op basis van limieten.
- [Diagnostische logboeken](/azure/network-watcher/network-watcher-monitoring-overview): biedt één deel venster om Diagnostische logboeken in of uit te scha kelen voor netwerk bronnen in een resource groep.

Zie [configure Network Watcher](/azure/network-watcher/network-watcher-create)(Engelstalig) voor meer informatie.

## <a name="cloud-service-provider-access-transparency"></a>Transparantie van toegang tot Cloud service provider

[Klanten-lockbox voor Microsoft Azure](customer-lockbox-overview.md) is een service die is geïntegreerd in azure portal die u een expliciete controle geeft in de zeldzame instantie wanneer een Microsoft ondersteuning-Engineer toegang nodig heeft tot uw gegevens om een probleem op te lossen.
Er zijn zeer weinig instanties, zoals een fout bij het oplossen van problemen met externe toegang, waarbij een Microsoft Ondersteuning Engineer verhoogde machtigingen nodig heeft om dit probleem op te lossen. In dergelijke gevallen gebruiken micro soft-technici just-in-time Access-service die beperkte, tijdgebonden autorisatie biedt met beperkte toegang tot de service.  
Terwijl micro soft de klant altijd toestemming heeft gegeven voor toegang, biedt Klanten-lockbox u nu de mogelijkheid om dergelijke aanvragen in azure portal te controleren en goed te keuren of te weigeren. Ondersteunings medewerkers van micro soft krijgen geen toegang tot u de aanvraag goed keuren.

## <a name="standardized-and-compliant-deployments"></a>Gestandaardiseerde en compatibele implementaties

Met [Azure-blauw drukken](/azure/governance/blueprints/overview) kunnen Cloud architecten en centrale informatie technologie groepen een Herhaal bare set Azure-resources definiëren die worden geïmplementeerd en voldoen aan de normen, patronen en vereisten van een organisatie.  
Dit maakt het mogelijk voor DevOps teams om snel nieuwe omgevingen te bouwen en te maken en te vertrouwen dat ze ze bouwen met een infra structuur die de naleving van de organisatie onderhoudt.
Blauw drukken biedt een declaratieve manier om de implementatie van verschillende bron sjablonen en andere artefacten te organiseren, zoals:

- Roltoewijzingen
- Beleidstoewijzingen
- Azure Resource Manager-sjablonen
- Resourcegroepen

## <a name="devops"></a>DevOps

Voordat [ontwikkel aars (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) toepassingen ontwikkelen, waren teams verantwoordelijk voor het verzamelen van bedrijfs vereisten voor een software programma en het schrijven van code. Vervolgens test een afzonderlijk team van QA het programma in een geïsoleerde ontwikkel omgeving. Als aan de vereisten is voldaan, heeft het QA-team de code voor de te implementeren bewerkingen vrijgegeven. De implementatie teams zijn verder gefragmenteerd in groepen zoals netwerken en data bases. Elke keer dat een software programma wordt ' veroorzaakt over de muur ' naar een onafhankelijk team, zijn er knel punten toegevoegd.

DevOps stelt teams in staat om sneller en goed koopere oplossingen van hogere kwaliteit te leveren. Klanten verwachten een dynamische en betrouw bare ervaring bij het gebruik van software en services. Teams moeten snel op software-updates herhalen en de impact van de updates meten. Ze moeten snel reageren met nieuwe ontwikkelings herhalingen om problemen op te lossen of meer waarde te bieden.  

Cloud platforms, zoals Microsoft Azure, hebben traditionele knel punten verwijderd en hebben de commoditize-infra structuur geholpen. Software-Reigns in elk bedrijf als Key onderscheid en factor in bedrijfs resultaten. Geen organisatie, ontwikkelaar of IT-mede werker kan of de DevOps-verplaatsing niet voor komen.

Volwassen DevOps-artsen nemen een aantal van de volgende procedures. Deze procedures [hebben betrekking](https://www.visualstudio.com/learn/what-is-devops-culture/) op het maken van strategieën voor gebruikers op basis van de bedrijfs scenario's. Hulp middelen kunnen helpen bij het automatiseren van de verschillende procedures.

- [Flexibele plannings-en project beheer](https://www.visualstudio.com/learn/what-is-agile/) technieken worden gebruikt voor het plannen en isoleren van werk in sprints, het beheren van de team capaciteit en het snel aanpassen van de veranderende behoeften van uw bedrijf.
- [Versie beheer, meestal met git](https://www.visualstudio.com/learn/what-is-git/), zorgt ervoor dat teams overal ter wereld bronnen kunnen delen en integreren met hulpprogram ma's voor software ontwikkeling om de release pijplijn te automatiseren.
- [Doorlopende integratie](https://www.visualstudio.com/learn/what-is-continuous-integration/) verstuurt het voortdurend samen voegen en testen van code, wat leidt tot een vroeg aantal defecten.  Andere voor delen zijn minder tijd verspild op het bestrijden van samenvoeg problemen en snelle feedback voor ontwikkel teams.
- [Continue levering](https://www.visualstudio.com/learn/what-is-continuous-delivery/) van software oplossingen aan productie-en test omgevingen helpt organisaties om snel fouten op te lossen en te reageren op steeds veranderende bedrijfs vereisten.
- [Bewaking](https://www.visualstudio.com/learn/what-is-monitoring/) van het uitvoeren van toepassingen, met inbegrip van productie omgevingen voor toepassings status, en het gebruik van de klant--helpt organisaties een hypo these te vormen en snel te valideren of te disproveen.  Uitgebreide gegevens worden vastgelegd en opgeslagen in verschillende indelingen voor logboek registratie.
- [Infrastructure as code (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) is een praktijk waarmee de automatisering en validatie van het maken en teardown van netwerken en virtuele machines kan worden geautomatiseerd voor het leveren van veilige, stabiele platformen voor hosting van toepassingen.
- Micro [Services](https://www.visualstudio.com/learn/what-are-microservices/) -architectuur wordt gebruikt voor het isoleren van zakelijke gebruiks cases in kleine herbruikbare Services.  Deze architectuur maakt schaal baarheid en efficiëntie mogelijk.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de Beveiliging en audit oplossing:

- [Beveiliging en naleving](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)
