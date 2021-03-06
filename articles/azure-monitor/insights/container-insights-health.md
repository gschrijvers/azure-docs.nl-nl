---
title: Kubernetes-cluster status controleren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de status van uw AKS-en niet-AKS-clusters kunt weer geven en analyseren met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649575"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Inzicht in de Kubernetes-cluster status met Azure Monitor voor containers

Met Azure Monitor voor containers wordt de status van de beheerde infrastructuur onderdelen gecontroleerd en gerapporteerd en worden alle knoop punten die worden uitgevoerd op een Kubernetes-cluster ondersteund door Azure Monitor voor containers. Deze ervaring overschrijdt de status van de cluster status die wordt berekend en gerapporteerd in de [weer gave met meerdere clusters](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), waar u nu kunt begrijpen of een of meer knoop punten in het cluster resource beperking zijn, of dat een knoop punt of pod niet beschikbaar is die van invloed kan zijn op een actieve toepassing in het cluster op basis van metrische gegevens.

>[!NOTE]
>Aan het eind van juni 2020 gaat de status functie over naar een persoonlijke preview. Raadpleeg de volgende aankondiging van Azure- [updates](https://azure.microsoft.com/updates/ci-health-limited-preview/)voor meer informatie.
>

Voor informatie over het inschakelen van Azure Monitor voor containers raadpleegt u [Onboarding Azure monitor voor containers](container-insights-onboard.md).

>[!NOTE]
>Als u AKS-engine clusters wilt ondersteunen, controleert u of deze voldoet aan het volgende:
>- De meest recente versie van de helm- [client](https://helm.sh/docs/using_helm/)wordt gebruikt.
>- De versie van de container agent is *micro soft/OMS: ciprod11012019*. Zie de [agent bijwerken in Kubernetes-cluster](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent)om de agent bij te werken.
>

## <a name="overview"></a>Overzicht

In Azure Monitor voor containers biedt de status (preview-versie) proactieve status controles van uw Kubernetes-cluster om u te helpen bij het identificeren en diagnosticeren van problemen. Het biedt u de mogelijkheid om aanzienlijke problemen weer te geven die zijn gedetecteerd. Monitors die de status van uw cluster evalueren, worden uitgevoerd op de container agent in uw cluster en de status gegevens worden naar de tabel **KubeHealth** in uw werk ruimte log Analytics geschreven. 

Kubernetes cluster Health is gebaseerd op een aantal bewakings scenario's die zijn georganiseerd door de volgende Kubernetes-objecten en-samen vattingen:

- Kubernetes-infra structuur: biedt een samen telling van de Kubernetes API-server, ReplicaSets en DaemonSets die worden uitgevoerd op knoop punten die in uw cluster worden geïmplementeerd, door het CPU-en geheugen gebruik te evalueren en een Peul Beschik baarheid

    ![Totaliserings weergave status Kubernetes-infra structuur](./media/container-insights-health/health-view-kube-infra-01.png)

- Knoop punten: voorziet in een samen telling van de knooppunt groepen en de status van afzonderlijke knoop punten in elke pool, door het CPU-en geheugen gebruik te evalueren en de status van een knoop punt, zoals gerapporteerd door Kubernetes.

    ![Totaliserings weergave status van knoop punten](./media/container-insights-health/health-view-nodes-01.png)

Op dit moment wordt alleen de status van een virtuele-kubelet ondersteund. De status voor het CPU-en geheugen gebruik van virtuele-kublet-knoop punten wordt gerapporteerd als **onbekend**, omdat er geen signaal wordt ontvangen.

Alle monitors worden weer gegeven in een hiërarchische indeling in het deel venster status hiërarchie, waarbij een aggregaatmonitor voor het Kubernetes-object of de abstractie (dat wil zeggen, Kubernetes-infra structuur of knoop punten) de bovenste monitor is die de gecombineerde status van alle afhankelijke onderliggende monitors weergeeft. De belangrijkste bewakings scenario's voor het afleiden van de status zijn:

* Het CPU-gebruik van het knoop punt en de container evalueren.
* Het geheugen gebruik van het knoop punt en de container evalueren.
* De status van de peulen en knoop punten op basis van de berekening van de status gereed gerapporteerd door Kubernetes.

Met de volgende pictogrammen wordt de status aangegeven:

|Pictogram|Betekenis|  
|--------|-----------|  
|![Pictogram groen vinkje geeft In orde aan](./media/container-insights-health/healthyicon.png)|Succes, status is in orde (groen)|  
|![Gele driehoek en uitroepteken is waarschuwing](./media/container-insights-health/warningicon.png)|Waarschuwing (geel)|  
|![Rode knop met witte X geeft kritieke status aan](./media/container-insights-health/criticalicon.png)|Kritiek (rood)|  
|![Pictogram lichter gekleurd](./media/container-insights-health/grayicon.png)|Onbekend (grijs)|  

## <a name="monitor-configuration"></a>Configuratie bewaken

Zie [configuratie handleiding Health Monitor](container-insights-health-monitors-config.md)voor meer informatie over het gedrag en de configuratie van elke monitor die Azure monitor voor de status van containers ondersteunt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Status van een AKS-of niet-AKS-cluster weer geven

Toegang tot de Azure Monitor voor de functie status van containers (preview) is rechtstreeks beschikbaar vanuit een AKS-cluster door **inzichten** te selecteren in het linkerdeel venster van de Azure Portal. Selecteer in de sectie **insightss** de optie **containers**. 

Als u de status wilt weer geven van een niet-AKS-cluster, dat wil zeggen een AKS engine-cluster dat lokaal wordt gehost of op Azure Stack, selecteert u **Azure monitor** in het linkerdeel venster van de Azure Portal. Selecteer in de sectie **insightss** de optie **containers**.  Selecteer op de pagina met meerdere clusters het niet-AKS-cluster in de lijst.

Selecteer in Azure Monitor voor containers, op de pagina **cluster** , **status**.

![Dash board voor cluster status](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Cluster status controleren

Wanneer de status pagina wordt geopend, wordt standaard de **Kubernetes-infra structuur** geselecteerd in het raster **status** .  Het raster geeft een overzicht van de huidige status rollup van de Kubernetes-infra structuur en cluster knooppunten. Als u een van beide status waarden selecteert, worden de resultaten in het deel venster status hiërarchie (het middelste deel venster) bijgewerkt en worden alle onderliggende monitors in een hiërarchische indeling weer gegeven met de huidige status. Als u meer informatie over een afhankelijke monitor wilt weer geven, kunt u er een selecteren en wordt er automatisch een eigenschappen venster weer gegeven aan de rechter kant van de pagina. 

![Eigenschappen venster cluster status](./media/container-insights-health/health-view-property-pane.png)

In het eigenschappen venster leert u het volgende:

- Op het tabblad **overzicht** ziet u de huidige status van de monitor die is geselecteerd, wanneer de monitor voor het laatst is berekend en wanneer de laatste status wijziging heeft plaatsgevonden. Aanvullende informatie wordt weer gegeven, afhankelijk van het type monitor dat is geselecteerd in de hiërarchie.

    Als u een aggregaatmonitor selecteert in het deel venster status hiërarchie, wordt op het tabblad **overzicht** in het deel venster eigenschap een Rollup van het totale aantal onderliggende monitors in de hiërarchie weer gegeven en wordt aangegeven hoeveel statistische monitors een kritieke, waarschuwing en een goede status hebben. 

    ![Tabblad Overzicht van status van het eigenschappen venster voor aggregaatmonitor](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Als u een unit-monitor selecteert in het deel venster status hiërarchie, wordt de vorige voor beelden die zijn berekend en gerapporteerd door de container agent in de afgelopen vier uur, weer gegeven onder **laatste status wijziging** . Dit is gebaseerd op de berekening van de unit-monitor voor het vergelijken van verschillende opeenvolgende waarden om de status te bepalen. Als u bijvoorbeeld de monitor status eenheid *pod Ready* hebt geselecteerd, worden de laatste twee steek proeven weer gegeven die worden gecontroleerd door de para meter *ConsecutiveSamplesForStateTransition*. Zie de gedetailleerde beschrijving van [Unit monitors](container-insights-health-monitors-config.md#unit-monitors)voor meer informatie.
    
    ![Tabblad Overzicht van het eigenschappen venster status](./media/container-insights-health/health-overview-unit-monitor.png)

    Als de tijd die wordt gerapporteerd door de **laatste status wijziging** een dag of ouder is, is het resultaat van geen wijzigingen in de status van de monitor. Als het laatste voor beeld dat is ontvangen voor een unit-monitor echter meer dan vier uur oud is, duidt dit erop dat de container agent geen gegevens heeft verzonden. Als de agent weet dat er een bepaalde resource bestaat, bijvoorbeeld een knoop punt, maar er geen gegevens zijn ontvangen van de CPU of het geheugen gebruik van het knoop punt (als voor beeld), wordt de status van de monitor ingesteld op **onbekend**.  

- Op het tabblad**configuratie** worden de standaard instellingen voor configuratie parameters weer gegeven (alleen voor unit monitors, geen geaggregeerde monitors) en hun waarden.
- Op het tabblad **kennis** bevat deze informatie over het gedrag van de monitor en hoe deze worden geëvalueerd voor een onjuiste situatie.

Bewakings gegevens op deze pagina worden niet automatisch vernieuwd en u moet op **vernieuwen** klikken boven aan de pagina om de meest recente status van het cluster te bekijken.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken om te evalueren of aan te passen om uw clusters te waarschuwen, te visualiseren of te analyseren.
