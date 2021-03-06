---
title: Overzicht van de verbonden computer Windows-agent
description: Dit artikel bevat een gedetailleerd overzicht van de Azure-Arc voor Server Agent die beschikbaar is voor het bewaken van virtuele machines die worden gehost in hybride omgevingen.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 05/18/2020
ms.topic: conceptual
ms.openlocfilehash: 4dbc559e62523a1ea17236a9e8c9666ef48bba33
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664149"
---
# <a name="overview-of-azure-arc-for-servers-agent"></a>Overzicht van Azure Arc voor servers agent

Met de Azure-Arc voor servers verbonden machine-agent kunt u uw Windows-en Linux-machines beheren die buiten Azure worden gehost op uw bedrijfs netwerk of een andere Cloud provider. Dit artikel bevat een gedetailleerd overzicht van de agent-, systeem-en netwerk vereisten en de verschillende implementatie methoden.

## <a name="download-agents"></a>Agents downloaden

U kunt het Azure Connected machine agent-pakket voor Windows en Linux downloaden van de hieronder vermelde locaties.

- [Windows agent Windows Installer-pakket](https://aka.ms/AzureConnectedMachineAgent) van het micro soft Download centrum.
- Linux-agent pakket wordt gedistribueerd vanuit de [pakket opslagplaats](https://packages.microsoft.com/) van micro soft met behulp van de voorkeurs indeling van het pakket voor de distributie (. RPM of. DEB).

>[!NOTE]
>Tijdens deze preview-versie is slechts één pakket vrijgegeven, dat geschikt is voor Ubuntu 16,04 of 18,04.

De Azure Connected machine-agent voor Windows en Linux kan hand matig worden bijgewerkt naar de meest recente versie of automatisch afhankelijk van uw vereisten. Zie [hier](manage-agent.md)voor meer informatie.

## <a name="windows-agent-installation-details"></a>Details van de installatie van Windows agent

De verbonden machine agent voor Windows kan worden geïnstalleerd met behulp van een van de volgende drie methoden:

* Dubbel klik op het bestand `AzureConnectedMachineAgent.msi` .
* Hand matig door het Windows Installer-pakket uit te voeren `AzureConnectedMachineAgent.msi` vanuit de opdracht shell.
* Vanuit een Power shell-sessie met behulp van een script methode.

Na de installatie van de verbonden machine-agent voor Windows, worden de volgende aanvullende configuratie wijzigingen van het hele systeem toegepast.

* De volgende installatie mappen worden tijdens de installatie gemaakt.

    |Map |Beschrijving |
    |-------|------------|
    |C:\Program Files\AzureConnectedMachineAgent |Standaardpad met de agent ondersteunings bestanden.|
    |%ProgramData%\AzureConnectedMachineAgent |Bevat de configuratie bestanden voor de agent.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Bevat de verkregen tokens.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Bevat het configuratie bestand van de agent waarin de `agentconfig.json` registratie gegevens van de service worden vastgelegd.|
    |%ProgramData%\GuestConfig |Bevat de aan Azure-beleids regels gerelateerde bestanden (toegepast).|

* De volgende Windows-Services worden tijdens de installatie van de agent gemaakt op de doel machine.

    |Servicenaam |Weergavenaam |Procesnaam |Beschrijving |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds. exe |Deze service implementeert de Azure instance meta data service (IMDS) voor het bijhouden van de machine.|
    |DscService |Gast configuratie service |dsc_service. exe |Dit is de configuratie code van de desired state Configuration (DSC v2) die in azure wordt gebruikt voor het implementeren van beleid in de gast.|

* De volgende omgevings variabelen worden tijdens de installatie van de agent gemaakt.

    |Name |Standaardwaarde |Beschrijving |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||
    
* Er zijn vier logboek bestanden beschikbaar voor het oplossen van problemen. Deze worden beschreven in de volgende tabel.

    |Logboek |Beschrijving |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Registreert gegevens van de agents (himds) en de interactie met Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Bevat de uitvoer van de azcmagent-hulp programma-opdrachten wanneer het argument uitgebreid (-v) wordt gebruikt.|
    |%ProgramData%\GuestConfig\ gc_agent_logs \ gc_agent. log |Registreert gegevens van de DSC-service activiteit,<br> met name de connectiviteit tussen de himds-service en Azure Policy.|
    |%ProgramData%\GuestConfig\ gc_agent_logs \ gc_agent_telemetry. txt |Registreert gegevens over de telemetrie van DSC-service en uitgebreide logboek registratie.|

* De lokale beveiligings groep **hybride agent extensie toepassingen** wordt gemaakt. 

* Tijdens het verwijderen van de agent worden de volgende artefacten niet verwijderd.

    * C:\Program Files\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent en submappen
    * %ProgramData%\GuestConfig

## <a name="linux-agent-installation-details"></a>Details van installatie van Linux-agent

De verbonden machine agent voor Linux is opgenomen in de voorkeurs pakket indeling voor de distributie (. RPM of. DEB) die wordt gehost in de micro soft [package-opslag plaats](https://packages.microsoft.com/). De agent wordt geïnstalleerd en geconfigureerd met de shell-script bundel [Install_linux_azcmagent. sh](https://aka.ms/azcmagent). 

Na de installatie van de verbonden machine agent voor Linux worden de volgende aanvullende configuratie wijzigingen van het hele systeem toegepast.

* De volgende installatie mappen worden tijdens de installatie gemaakt.

    |Map |Beschrijving |
    |-------|------------|
    |/var/opt/azcmagent/ |Standaardpad met de agent ondersteunings bestanden.|
    |/opt/azcmagent/ |
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Bevat de verkregen tokens.|
    |/var/lib/GuestConfig |Bevat de aan Azure-beleids regels gerelateerde bestanden (toegepast).|

* De volgende daemons worden tijdens de installatie van de agent gemaakt op de doel machine.

    |Servicenaam |Weergavenaam |Procesnaam |Beschrijving |
    |-------------|-------------|-------------|------------|
    |himdsd. service |Azure Hybrid Instance Metadata Service |/opt/azcmagent/bin/himds |Deze service implementeert de Azure instance meta data service (IMDS) voor het bijhouden van de machine.|
    |dscd. service |Gast configuratie service |/opt/DSC/dsc_linux_service |Dit is de configuratie code van de desired state Configuration (DSC v2) die in azure wordt gebruikt voor het implementeren van beleid in de gast.|

* Er zijn vier logboek bestanden beschikbaar voor het oplossen van problemen. Deze worden beschreven in de volgende tabel.

    |Logboek |Beschrijving |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Registreert gegevens van de agents (himds) en de interactie met Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Bevat de uitvoer van de azcmagent-hulp programma-opdrachten wanneer het argument uitgebreid (-v) wordt gebruikt.|
    |/opt/logs/dsc.log |Registreert gegevens van de DSC-service activiteit,<br> met name de connectiviteit tussen de himds-service en Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |Registreert gegevens over de telemetrie van DSC-service en uitgebreide logboek registratie.|

* De volgende omgevings variabelen worden tijdens de installatie van de agent gemaakt. Deze variabelen worden ingesteld in `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Name |Standaardwaarde |Beschrijving |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Tijdens het verwijderen van de agent worden de volgende artefacten niet verwijderd.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="prerequisites"></a>Vereisten

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende versies van het Windows-en Linux-besturings systeem worden officieel ondersteund voor de Azure Connected machine agent: 

- Windows Server 2012 R2 en hoger (inclusief Windows Server Core)
- Ubuntu 16,04 en 18,04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 2

>[!NOTE]
>Deze preview-versie van de verbonden machine-agent voor Windows ondersteunt alleen Windows Server die is geconfigureerd voor gebruik van de Engelse taal.
>

### <a name="required-permissions"></a>Vereiste machtigingen

- Voor de onboarding van machines bent u lid van de rol **Azure Connected machine** .

- Als u een machine wilt lezen, wijzigen, opnieuw wilt indelen en verwijderen, bent u lid van de **Azure Connected machine resource Administrator** -rol. 

### <a name="azure-subscription-and-service-limits"></a>Azure-abonnement en service limieten

Voordat u uw computers met Azure-Arc voor servers (preview) configureert, moet u de limieten voor het Azure Resource Manager- [abonnement](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) en de [limieten van de resource groep](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) controleren om het aantal machines te plannen dat moet worden verbonden.

## <a name="tls-12-protocol"></a>TLS 1,2-protocol

Om te zorgen voor de beveiliging van gegevens die onderweg zijn naar Azure, raden we u aan om de machine te configureren voor het gebruik van Transport Layer Security (TLS) 1,2. Er zijn oudere versies van TLS/Secure Sockets Layer (SSL) gevonden die kwetsbaar zijn en terwijl ze nog steeds werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen**. 

|Platform/taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn vaak afhankelijk van [openssl](https://www.openssl.org) voor TLS 1,2-ondersteuning. | Controleer de [openssl wijzigingen logboek](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van openssl wordt ondersteund.|
| Windows Server 2012 R2 en hoger | Wordt ondersteund en is standaard ingeschakeld. | Om te bevestigen dat u nog steeds de [standaard instellingen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)gebruikt.|

### <a name="networking-configuration"></a>Netwerk configuratie

De verbonden machine agent voor Linux en Windows communiceert veilig uitgaande naar Azure Arc via TCP-poort 443. Als de computer verbinding maakt via een firewall of proxy server om via internet te communiceren, raadpleegt u de vereisten hieronder om inzicht te krijgen in de vereisten voor de netwerk configuratie.

Als de uitgaande connectiviteit wordt beperkt door uw firewall of proxy server, controleert u of de Url's die hieronder worden weer gegeven niet zijn geblokkeerd. Als u alleen de IP-bereiken of domein namen toestaat die vereist zijn om de agent te laten communiceren met de-service, moet u ook toegang tot de volgende service tags en Url's toestaan.

Service Tags:

- AzureActiveDirectory
- AzureTrafficManager

Adres

| Agentresource | Beschrijving |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Gastconfiguratie|
|*-agentservice-prod-1.azure-automation.net|Gastconfiguratie|
|*. his.arc.azure.com|Hybride identiteits service|

Zie voor een lijst met IP-adressen voor elke servicetag/regio het JSON-bestand- [Azure IP-bereiken en de service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Micro soft publiceert wekelijkse updates met elke Azure-service en de IP-bereiken die worden gebruikt. Bekijk [service Tags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)voor meer informatie.

De Url's in de vorige tabel zijn vereist naast de IP-adres bereik gegevens van de service label, omdat de meeste services momenteel geen servicetag registratie hebben. Zo kunnen de IP-adressen worden gewijzigd. Als IP-adresbereiken vereist zijn voor de configuratie van de firewall, moet de **Cloud** -servicetag worden gebruikt om toegang tot alle Azure-Services toe te staan. Schakel de beveiligings controle of inspectie van deze Url's niet uit, en sta ze toe als andere Internet verkeer.

### <a name="register-azure-resource-providers"></a>Azure-resource providers registreren

Azure Arc voor servers (preview) is afhankelijk van de volgende Azure-resource providers in uw abonnement om deze service te kunnen gebruiken:

- **Micro soft. HybridCompute**
- **Micro soft. GuestConfiguration**

Als ze niet zijn geregistreerd, kunt u ze registreren met de volgende opdrachten:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

U kunt de resource providers ook registreren in de Azure Portal door de stappen onder [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)te volgen.


## <a name="installation-and-configuration"></a>Installatie en configuratie

Het koppelen van machines in uw hybride omgeving met Azure kan worden uitgevoerd met behulp van verschillende methoden, afhankelijk van uw vereisten. In de volgende tabel ziet u elke methode om te bepalen welke functie het beste werkt voor uw organisatie.

| Methode | Beschrijving |
|--------|-------------|
| Interactief | Installeer de agent hand matig op een enkele of een beperkt aantal machines Volg de stappen in [computers verbinden met Azure Portal](onboard-portal.md).<br> Vanuit het Azure Portal kunt u een script genereren en uitvoeren op de computer om de installatie-en configuratie stappen van de agent te automatiseren.|
| Op schaal | Installeer en configureer de agent voor meerdere machines die de [Connect-computers volgen met behulp van een Service-Principal](onboard-service-principal.md).<br> Met deze methode maakt u een Service-Principal om machines niet-interactief te verbinden.|
| Op schaal | Installeer en configureer de agent voor meerdere machines [met behulp van Windows Power shell DSC](onboard-dsc.md).<br> Deze methode maakt gebruik van een Service-Principal om machines niet-interactief met Power shell DSC te verbinden. |

## <a name="next-steps"></a>Volgende stappen

Als u Azure-Arc voor servers (preview) wilt gaan evalueren, volgt u het artikel [verbinding maken met hybride computers met Azure via de Azure Portal](onboard-portal.md).
