---
title: Release opmerkingen voor Azure Security Center
description: Een beschrijving van wat er nieuw is en gewijzigd in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 6909bcbc67680f9205af8a79782907d4671d668b
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860712"
---
# <a name="whats-new-in-azure-security-center"></a>Wat is er nieuw in Azure Security Center?

Azure-beveiliging is in actieve ontwikkeling en ontvangt voortdurend verbeteringen. Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt deze pagina informatie over:

- Nieuwe functies
- Opgeloste fouten
- Afgeschafte functionaliteit

Deze pagina wordt regel matig bijgewerkt. Ga daarom vaak opnieuw te werk. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in het [Archief voor wat er nieuw is in azure Security Center](release-notes-archive.md).


## <a name="may-2020"></a>Mei 2020


### <a name="alert-suppression-rules-preview"></a>Regels voor waarschuwings onderdrukking (preview-versie)

Met deze nieuwe functie (momenteel in Preview) kunt u de duurzaamheid van waarschuwingen verlagen. Gebruik regels om waarschuwingen automatisch te verbergen waarvan bekend is dat ze worden Innocuous of gerelateerd aan normale activiteiten in uw organisatie. Zo kunt u zich richten op de meest relevante bedreigingen. 

Waarschuwingen die overeenkomen met uw ingeschakelde onderdrukkings regels worden nog steeds gegenereerd, maar de status wordt ingesteld op genegeerd. U kunt de status bekijken in de Azure Portal of u hebt echter toegang tot uw Security Center beveiligings waarschuwingen.

Met onderdrukkings regels worden de criteria gedefinieerd waarvoor waarschuwingen automatisch moeten worden genegeerd. Normaal gesp roken gebruikt u een onderdrukkings regel voor het volgende:

- waarschuwingen onderdrukken die u als foutieve positieven hebt geïdentificeerd

- waarschuwingen onderdrukken die te vaak worden geactiveerd om nuttig te zijn

Meer informatie over [het onderdrukken van waarschuwingen van de bedreigings beveiliging van Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>De evaluatie van de beveiligings problemen van de virtuele machine is nu algemeen beschikbaar

De Standard-laag van Security Center bevat nu een geïntegreerde evaluatie van beveiligings problemen voor virtuele machines zonder extra kosten. Deze uitbrei ding wordt mogelijk gemaakt door Qualys, maar rapporteert de resultaten direct terug naar Security Center. U hebt geen Qualys-licentie nodig of zelfs een Qualys-account: alles wordt naadloos verwerkt binnen Security Center.

De nieuwe oplossing kan voortdurend uw virtuele machines scannen om beveiligings problemen op te sporen en de bevindingen in Security Center presen teren. 

Als u de oplossing wilt implementeren, gebruikt u de nieuwe beveiligings aanbeveling:

"De ingebouwde oplossing voor de evaluatie van beveiligings problemen inschakelen op virtuele machines (aangedreven door Qualys)"

Meer informatie over [de evaluatie van het geïntegreerde beveiligings risico van Security Center voor virtuele machines](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Wijzigingen in de just-in-time-toegang van virtuele machines (VM)

Security Center bevat een optionele functie waarmee u de beheer poorten van uw virtuele machines kunt beveiligen. Dit biedt een verdediging tegen de meest voorkomende vorm van beveiligings aanvallen.

Met deze update worden de volgende wijzigingen aangebracht in deze functie:

- De aanbeveling waarmee u wordt geadviseerd om JIT in te scha kelen voor een VM, is hernoemd. Voorheen moet ' just-in-time-netwerk toegangs beheer worden toegepast op virtuele machines ' nu: ' beheer poorten van virtuele machines moeten worden beveiligd met Just-in-time-netwerk toegangs beheer '.

- De aanbeveling is zo ingesteld dat deze alleen wordt geactiveerd als er open beheer poorten zijn.

Meer informatie over [de JIT-toegangs functie](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk beveiligings beheer

Een van de beveiligings besturings elementen die zijn geïntroduceerd in de verbeterde beveiligde Score is ' Implementeer beveiligings best practices '. Aangepaste aanbevelingen die zijn gemaakt voor uw abonnementen, worden automatisch in dat besturings element geplaatst. 

Om u te helpen uw aangepaste aanbevelingen gemakkelijker te vinden, zijn deze naar een speciaal beveiligings beheer, ' aangepaste aanbevelingen ', verplaatst. Dit besturings element heeft geen invloed op uw beveiligde Score.

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Toevoegen/verbergen om aanbevelingen in besturings elementen of als een platte lijst weer te geven

Beveiligings controles zijn logische groepen van gerelateerde beveiligings aanbevelingen. Ze zijn gebaseerd op uw kwets bare aanvals oppervlakken. Een besturings element is een reeks beveiligings aanbevelingen met instructies die u helpen bij het implementeren van deze aanbevelingen.

Als u onmiddellijk wilt zien hoe goed uw organisatie een afzonderlijke kwets baarheid beveiligt, controleert u de scores voor elk beveiligings beheer.

Uw aanbevelingen worden standaard weer gegeven in de beveiligings controles. Vanuit deze update kunt u ze ook weer geven als een lijst. Als u deze wilt weer geven als een eenvoudige lijst, gesorteerd op de status van de betrokken resources, gebruikt u de nieuwe wissel knop Group by Controls. De wissel knop bevindt zich boven de lijst in de portal.

De beveiligings besturings elementen-en deze wissel knop maken deel uit van de nieuwe beveiligde Score ervaring. Vergeet niet om ons uw feedback te sturen vanuit de portal.

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).


### <a name="expanded-security-control-implement-security-best-practices"></a>Uitgebreide beveiliging, aanbevolen procedures voor het implementeren van beveiliging 

Een van de beveiligings mechanismen die zijn geïntroduceerd met de verbeterde beveiligde Score is ' Implementeer beveiligings best practices '. Wanneer een aanbeveling zich in dit besturings element bevindt, heeft dit geen invloed op de beveiligde Score. 

Met deze update zijn drie aanbevelingen verplaatst van de besturings elementen waarin deze oorspronkelijk zijn geplaatst, en in dit best practices-besturings element. We hebben deze stap doorgevoerd omdat er is vastgesteld dat het risico van deze drie aanbevelingen lager is dan oorspronkelijk werd aangenomen.

Daarnaast zijn er twee nieuwe aanbevelingen geïntroduceerd en toegevoegd aan dit besturings element.

De drie aanbevelingen die worden verplaatst zijn:

- **MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement** (oorspronkelijk in het besturings element MFA inschakelen)
- **Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement** (oorspronkelijk in het besturings element toegang en machtigingen beheren)
- **Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement** (oorspronkelijk in het besturings element toegang en machtigingen beheren)

De twee nieuwe aanbevelingen die aan het besturings element worden toegevoegd, zijn:

- **[Preview] gast configuratie agent moet zijn geïnstalleerd** -met behulp van [Azure Policy-gast configuratie](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) kunt u binnen virtuele machines inzicht krijgen in de instellingen van de server en de toepassing (alleen Windows).

- **[Preview] Windows exploit Guard moet zijn ingeschakeld** : Windows Defender exploit Guard maakt gebruik van de Azure Policy-gast configuratie agent. Exploit Guard heeft vier onderdelen die zijn ontworpen voor het vergren delen van apparaten tegen een groot aantal aanvals vectoren en voor het blok keren van gedrag dat vaak wordt gebruikt voor aanvallen op schadelijke software, terwijl ondernemingen hun beveiligings Risico's en productiviteits vereisten kunnen afstemmen (alleen Windows).

Meer informatie over Windows Defender exploit Guard in [een exploit Guard-beleid maken en implementeren](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Aangepaste beleids regels met aangepaste meta gegevens zijn nu algemeen beschikbaar

Aangepaste beleids regels maken nu deel uit van de Security Center aanbevelingen, beveiligde scores en het dash board nalevings normen voor regelgeving. Deze functie is nu algemeen beschikbaar en biedt u de mogelijkheid om de beveiligings beoordelings dekking van uw organisatie in Security Center uit te breiden. 

Maak een aangepaste initiatief in azure Policy, voeg er beleids regels aan toe en bewaart dit voor Azure Security Center, en visualiseer het als aanbevelingen.

We hebben nu ook de optie toegevoegd voor het bewerken van de aangepaste aanbevelings-meta gegevens. Meta gegevens opties zijn ernst, herstel stappen, informatie over bedreigingen en meer.  

Meer informatie over [het verbeteren van uw aangepaste aanbevelingen met gedetailleerde informatie](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Crash dump analyse mogelijkheden migreren naar aanval op bestandsloze detectie 

De detectie mogelijkheden van Windows Crash Dump Analysis (CDA) worden geïntegreerd in de [detectie van aanvalen bestanden](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). Detectie van conflicten met een aanval op een bestand biedt verbeterde versies van de volgende beveiligings waarschuwingen voor Windows-computers: code-injectie gedetecteerd, er is een verwerkte shell code gedetecteerd, gedetecteerde en verdacht code segment gedetecteerd.

Enkele van de voor delen van deze overgang:

- **Proactieve en tijdige detectie van malware** : de CDA-benadering die is betrokken bij het wachten op een crash, en het uitvoeren van analyses om actieve malware te vinden. Bij het gebruik van een aanval zonder bestanden wordt er tijdens de uitvoering een proactieve identificatie van de bedreigingen in het geheugen gebruikt. 

- **Verrijkte waarschuwingen** : de beveiligings waarschuwingen van aanval via bestanden bevatten verrijkingen die niet beschikbaar zijn via CDA, zoals de actieve netwerk verbindings gegevens. 

- **Aggregatie van waarschuwingen** : wanneer CDA meerdere aanvals patronen in één crash dump heeft gedetecteerd, worden er meerdere beveiligings waarschuwingen geactiveerd. Bij het detecteren van een aanval met een bestand worden alle geïdentificeerde aanvals patronen uit hetzelfde proces gecombineerd tot één waarschuwing, waardoor de nood zaak om meerdere waarschuwingen te correleren, wordt verwijderd.

- **Gereduceerde vereisten op uw log Analytics werk ruimte** : crash dumps die mogelijk gevoelige gegevens bevatten, worden niet meer geüpload naar uw log Analytics-werk ruimte.



## <a name="april-2020"></a>April 2020

### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamische nalevings pakketten zijn nu algemeen beschikbaar

Het dash board voor nalevings vereisten van Azure Security Center bevat nu **dynamische nalevings pakketten** (nu algemeen beschikbaar) voor het bijhouden van aanvullende industrie-en regelgevings normen.

Dynamische nalevings pakketten kunnen worden toegevoegd aan uw abonnement of beheer groep op de pagina Security Center beveiligings beleid. Wanneer u een Standard-of Bench Mark hebt voor bereid, wordt de standaard weer gegeven in het dash board nalevings beleid met alle gekoppelde nalevings gegevens die zijn toegewezen als evaluaties. Een samenvattings rapport voor een van de beschik bare standaarden kan worden gedownload.

U kunt nu standaarden toevoegen zoals:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK-officiële en UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nieuw)** (dit is een meer volledige weer gave van Azure CIS 1.1.0)

Daarnaast hebben we onlangs de **Azure Security-Bench Mark**toegevoegd, de door micro soft geschreven specifieke Azure-richt lijnen voor de aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevings kaders. Er worden extra standaarden ondersteund in het dash board, zodra deze beschikbaar komen.  
 
Meer informatie over [het aanpassen van de set normen in uw nalevings dashboard](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Aanbevelingen voor de identiteit nu opgenomen in Azure Security Center gratis laag

Beveiligings aanbevelingen voor identiteit en toegang op de laag Azure Security Center gratis zijn nu algemeen beschikbaar. Dit maakt deel uit van de inspanningen om de CSPM-functies (Cloud Security postuur Management) gratis te maken. Tot nu toe zijn deze aanbevelingen alleen beschikbaar in de prijs categorie Standard.

Voor beelden van aanbevelingen voor identiteits-en toegangs rechten zijn:

- "Multi-factor Authentication moet worden ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement."
- "Maxi maal drie eigen aren moeten worden opgegeven voor uw abonnement."
- "Afgeschafte accounts moeten worden verwijderd uit uw abonnement."

Als u abonnementen hebt op de prijs categorie gratis, worden hun beveiligde scores beïnvloed door deze wijziging omdat ze nooit werden beoordeeld voor hun identiteits-en toegangs beveiliging.

Meer informatie over [aanbevelingen voor identiteit en toegang](recommendations-reference.md#recs-identity).

Meer informatie over het [bewaken van identiteit en toegang](security-center-identity-access.md).


## <a name="march-2020"></a>Maart 2020

### <a name="workflow-automation-is-now-generally-available"></a>Werk stroom automatisering is nu algemeen beschikbaar

De functie werk stroom automatisering van Azure Security Center is nu algemeen beschikbaar. Gebruik deze functie om automatisch Logic Apps te activeren voor beveiligings waarschuwingen en aanbevelingen. Daarnaast zijn er hand matige triggers beschikbaar voor waarschuwingen en worden alle aanbevelingen met de optie snel herstellen beschikbaar.

Elk beveiligings programma bevat meerdere werk stromen voor reactie op incidenten. Deze processen kunnen het melden van relevante belanghebbenden, het starten van een wijzigings beheer proces en het Toep assen van specifieke herbemiddelings stappen zijn. Beveiligings experts raden u aan zo veel mogelijk stappen van deze procedures te automatiseren. Automation vermindert de overhead en kan de beveiliging verbeteren door ervoor te zorgen dat de proces stappen snel, consistent en volgens uw vooraf gedefinieerde vereisten worden uitgevoerd.

Zie [werk stroom automatisering](workflow-automation.md)voor meer informatie over de mogelijkheden voor automatische en hand matige Security Center voor het uitvoeren van uw werk stromen.

Meer informatie over [het maken van Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integratie van Azure Security Center met Windows-beheer centrum

Het is nu mogelijk om uw on-premises Windows-servers rechtstreeks naar de Azure Security Center te verplaatsen vanuit het Windows-beheer centrum. Security Center wordt vervolgens uw enige glas venster om beveiligings gegevens weer te geven voor alle resources van uw Windows-beheer centrum, waaronder on-premises servers, virtuele machines en extra PaaS-workloads.

Nadat u een server hebt verplaatst van het Windows-beheer centrum naar Azure Security Center, kunt u het volgende doen:

- Beveiligings waarschuwingen en aanbevelingen weer geven in de uitbrei ding Security Center van het Windows-beheer centrum.
- Bekijk de beveiligings postuur en haal meer gedetailleerde informatie op over uw door Windows-beheer centrum beheerde servers in de Security Center in het Azure Portal (of via een API).

Meer informatie over [het integreren van Azure Security Center met het Windows-beheer centrum](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Beveiliging voor de Azure Kubernetes-service

Azure Security Center breidt de beveiligings functies van de container uit om Azure Kubernetes service (AKS) te beveiligen.

De populaire open-source platform Kubernetes is zodanig aangenomen dat het nu een industrie standaard is voor container indeling. Ondanks deze wijde implementatie is er nog steeds geen informatie over het beveiligen van een Kubernetes-omgeving. Voor het beschermen van de kwets baarheid van een container toepassing is expertise vereist om te garanderen dat de infra structuur veilig is geconfigureerd en voortdurend wordt bewaakt voor mogelijke dreigingen.

De Security Center verdediging omvat:

- **Detectie en zicht baarheid** : doorlopende detectie van beheerde AKS-instanties binnen de abonnementen die zijn geregistreerd voor Security Center.
- **Aanbevelingen voor beveiliging** -beschik bare aanbevelingen om u te helpen te voldoen aan de aanbevolen procedures voor beveiliging voor AKS. Deze aanbevelingen zijn opgenomen in uw beveiligde Score om ervoor te zorgen dat ze worden weer gegeven als onderdeel van de beveiligings postuur van uw organisatie. Een voor beeld van een AKS-aanbeveling die u kunt zien, is ' op rollen gebaseerd toegangs beheer moet worden gebruikt om de toegang tot een Kubernetes-service cluster te beperken '.
- **Bedreigings beveiliging** : Security Center u wordt gewaarschuwd voor bedreigingen en schadelijke activiteiten die zijn gedetecteerd op het niveau van de host en het AKS-cluster.

Meer informatie over de [integratie van Azure Kubernetes Services met Security Center](azure-kubernetes-service-integration.md).

Meer informatie over [de beveiligings functies van de container in Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Verbeterde just-in-time-ervaring

De functies, bewerking en gebruikers interface voor het Azure Security Center van just-in-time hulpprogram ma's die uw beheer poorten beveiligen, zijn als volgt verbeterd: 

- **Veld motivering** : als u via de just-in-time pagina van de Azure portal toegang tot een virtuele machine (VM) wilt aanvragen, is er een nieuw optioneel veld beschikbaar om een reden voor de aanvraag in te voeren. Gegevens die in dit veld worden ingevoerd, kunnen in het activiteiten logboek worden bijgehouden. 
- **Automatisch opschonen van redundante just-in-time (JIT)-regels** : wanneer u een JIT-beleid bijwerkt, wordt automatisch een opschoon programma uitgevoerd om de geldigheid van de volledige ruleset te controleren. Het hulp programma zoekt naar verschillen tussen regels in uw beleid en regels in het NSG. Als het hulp programma voor opschonen een niet-overeenkomend item detecteert, wordt de oorzaak bepaald en, wanneer het veilig is om dit te doen, verwijdert u ingebouwde regels die niet meer nodig zijn. De verruiming verwijdert nooit regels die u hebt gemaakt. 

Meer informatie over [de JIT-toegangs functie](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Twee beveiligings aanbevelingen voor afgeschafte webtoepassingen

Twee beveiligings aanbevelingen met betrekking tot webtoepassingen worden afgeschaft: 

- De regels voor webtoepassingen op IaaS Nsg's moeten worden gehard.
    (Gerelateerd beleid: de Nsg's-regels voor webtoepassingen op IaaS moeten worden gehard)

- Toegang tot App Services moet worden beperkt.
    (Gerelateerd beleid: toegang tot App Services moet worden beperkt [Preview])

Deze aanbevelingen worden niet meer weer gegeven in de Security Center lijst met aanbevelingen. Het gerelateerde beleid wordt niet meer opgenomen in het initiatief ' Security Center default '.

Meer informatie over [beveiligings aanbevelingen](recommendations-reference.md).



## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Aanvals detectie met een bestand voor Linux (preview-versie)

Als aanvallers stealthier Azure Security Center-methoden gebruiken om detectie te voor komen, wordt in aanvulling op Windows de detectie van de aanval van bestanden voor Linux uitgebreid. Aanvallen waarbij misbruik wordt gemaakt van software, schadelijke nettoladingen in goed aardige systeem processen injecteren en in het geheugen verbergen. Deze technieken:

- traceringen van malware op schijf minimaliseren of elimineren
- de kans op detectie door op schijf gebaseerde scan oplossingen op basis van malware aanzienlijk beperken

Als u deze bedreiging wilt bemeteren, Azure Security Center u de aanvals detectie van bestanden voor Windows in oktober 2018 vrijgegeven en nu ook uitgebreide aanvals detectie op Linux. 


## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score-preview"></a>Verbeterde beveiligde Score (preview-versie)

Een verbeterde versie van de functie beveiligde Score van Azure Security Center is nu beschikbaar als preview. In deze versie zijn meerdere aanbevelingen onderverdeeld in beveiligings controles die beter aansluiten op uw kwets bare aanvallen (bijvoorbeeld de toegang tot beheer poorten beperken).

Lees meer over de wijzigingen in de beveiligde score tijdens de preview-fase en bepaal andere herstel bewerkingen waarmee u uw omgeving verder kunt beveiligen.

Meer informatie over [verbeterde beveiligde Score (preview) in azure Security Center](secure-score-security-controls.md).