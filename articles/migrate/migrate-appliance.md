---
title: Azure Migrate-apparaat
description: Biedt een overzicht van het Azure Migrate apparaat dat in Server evaluatie en-migratie wordt gebruikt.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 98398510acb1eec29ea603d869f1e9ec383cb210
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758942"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

In dit artikel vindt u een overzicht van de vereisten en ondersteuning voor het Azure Migrate apparaat. 

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate apparaat wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Gebruikt voor** 
--- | --- | ---
**VMware VM-evaluatie** | Azure Migrate: Server evaluatie | VMware-Vm's detecteren<br/><br/> Machine-apps en-afhankelijkheden detecteren<br/><br/> Meta gegevens en prestaties van meta gegevens van machines voor evaluaties verzamelen.
**Migratie van VMware VM zonder agent** | Azure Migrate: Server migratie | VMware-Vm's detecteren <br/><br/> Virtuele VMware-machines repliceren met migratie zonder agent.
**Hyper-V-VM-evaluatie** | Azure Migrate: Server evaluatie | Virtuele Hyper-V-machines detecteren<br/><br/> Meta gegevens en prestaties van meta gegevens van machines voor evaluaties verzamelen.
**Beoordeling van fysieke machine** |  Azure Migrate: Server evaluatie |  Detecteer fysieke servers (of Vm's die u als fysieke servers behandelt).<br/><br/> Meta gegevens en prestaties van meta gegevens van machines voor evaluaties verzamelen.

## <a name="deployment-methods"></a>Implementatie methoden

Het apparaat kan worden geïmplementeerd met een aantal methoden:

- Het apparaat kan worden geïmplementeerd met een sjabloon voor virtuele VMware-machines en Hyper-V-Vm's (eicellen-sjabloon voor VMware of VHD voor Hyper-V).
- Als u geen sjabloon wilt gebruiken, kunt u het apparaat voor VMware of Hyper-V implementeren met behulp van een Power shell-script.
- In Azure Government moet u het apparaat implementeren met behulp van een script.
- Voor fysieke servers implementeert u het apparaat altijd met behulp van een script.
- Download koppelingen zijn beschikbaar in de onderstaande tabellen.


## <a name="appliance---vmware"></a>Apparaat-VMware 

De volgende tabel bevat een overzicht van de Azure Migrate vereisten voor VMware.

**Vereiste** | **VMware** 
--- | ---
**Machtigingen** | Als u de Web-App van het apparaat lokaal of op afstand wilt openen, moet u een domein beheerder of lokale beheerder op de apparaatapparaat zijn.
**Toestel onderdelen** | Het apparaat heeft de volgende onderdelen:<br/><br/> - **Beheer-app**: dit is een web-app voor gebruikers invoer tijdens de implementatie van het apparaat. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/> - **Detectie agent**: de agent verzamelt computer configuratie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/>- **Beoordelings agent**: de agent verzamelt prestatie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/>- **Service voor automatische updates**: updates van de onderdelen van het apparaat (elke 24 uur wordt uitgevoerd).<br/>- **DRA-agent**: organiseert de replicatie van de virtuele machine en coördineert de communicatie tussen gerepliceerde machines en Azure. Wordt alleen gebruikt bij het repliceren van virtuele VMware-machines naar Azure met migratie zonder agent.<br/>- **Gateway**: verstuurt gerepliceerde gegevens naar Azure. Wordt alleen gebruikt bij het repliceren van virtuele VMware-machines naar Azure met migratie zonder agent.
**Ondersteunde implementatie** | Implementeren als VMware-VM met behulp van de eicellen-sjabloon.<br/><br/> Implementeren als een VMware-VM of fysieke machine met behulp van een Power shell-installatie script.
**Project ondersteuning** |  Een apparaat kan worden gekoppeld aan een enkel project. <br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectie limieten** | Een apparaat kan tot 10.000 VMware-Vm's detecteren op een vCenter Server.<br/> Een apparaat kan verbinding maken met één vCenter Server.
**EICELLEN-sjabloon** | Downloaden van portal of van https://aka.ms/migrate/appliance/vmware .<br/><br/> De download grootte is 11,2 GB.<br/><br/> De sjabloon voor het gedownloade apparaat wordt geleverd met een Windows Server 2016-evaluatie licentie, die voor 180 dagen geldig is. Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de apparaat-VM activeert.
**PowerShell-script** | [Downloaden](https://go.microsoft.com/fwlink/?linkid=2105112)van script.<br/><br/> 
**Software/hardware** |  Het apparaat moet worden uitgevoerd op de computer met Windows Server 2016, 32-GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch.<br/> Voor het apparaat is toegang tot internet vereist, hetzij rechtstreeks hetzij via een proxy.<br/><br/> Als u het apparaat op een virtuele VMware-machine uitvoert, hebt u voldoende resources op de vCenter Server nodig om een virtuele machine toe te wijzen die aan de vereisten voldoet.<br/><br/> Als u het apparaat op een fysieke computer uitvoert, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten. 
**VMware-vereisten** | Als u het apparaat als een virtuele VMware-machine implementeert, moet dit worden geïmplementeerd op een ESXi-host waarop versie 5,5 of hoger wordt uitgevoerd.<br/><br/> vCenter Server met 5,5, 6,0, 6,5 of 6,7.
**VDDK (migratie zonder agent)** | Als u het apparaat als een virtuele VMware-machine implementeert en u een migratie zonder agent uitvoert, moet de VMware vSphere-VDDK op de apparaat-VM zijn geïnstalleerd.
**Hashwaarde-eicellen** | [Controleer](tutorial-assess-vmware.md#verify-security) de hash-waarden van de eicellen-sjabloon.
**Hash-waarde-Power shell-script** | [Controleer](deploy-appliance-script.md#verify-file-security) de hash-waarden van het Power shell-script.




## <a name="appliance---hyper-v"></a>Apparaat-Hyper-V

**Vereiste** | **Hyper-V** 
--- | ---
**Machtigingen** | Als u de Web-App van het apparaat lokaal of op afstand wilt openen, moet u een domein beheerder of lokale beheerder op de apparaatapparaat zijn.
**Toestel onderdelen** | Het apparaat heeft de volgende onderdelen:<br/><br/>- **Beheer-app**: dit is een web-app voor gebruikers invoer tijdens de implementatie van het apparaat. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/> - **Detectie agent**: de agent verzamelt computer configuratie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/>- **Beoordelings agent**: de agent verzamelt prestatie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/>- **Service voor automatische updates**: updates van de onderdelen van het apparaat (elke 24 uur wordt uitgevoerd).
**Ondersteunde implementatie** | Als Hyper-V-VM implementeren met behulp van een VHD-sjabloon.<br/><br/> Implementeer als een Hyper-V-VM of fysieke machine met behulp van een Power shell-installatie script.
**Project ondersteuning** |  Een apparaat kan worden gekoppeld aan een enkel project. <br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectie limieten** | Een apparaat kan tot 5000 Hyper-V-Vm's detecteren.<br/> Een apparaat kan verbinding maken met Maxi maal 300 Hyper-V-hosts.
**VHD-sjabloon** | Gezipte map inclusief VHD. Downloaden van portal of van https://aka.ms/migrate/appliance/hyperv .<br/><br/> Download grootte is 10 GB.<br/><br/> De sjabloon voor het gedownloade apparaat wordt geleverd met een Windows Server 2016-evaluatie licentie, die voor 180 dagen geldig is. Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de apparaat-VM activeert.
**PowerShell-script** | [Downloaden](https://go.microsoft.com/fwlink/?linkid=2105112)van script.<br/><br/> 
**Software/hardware***   |  Het apparaat moet worden uitgevoerd op de computer met Windows Server 2016, 32-GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en vereist Internet toegang, hetzij rechtstreeks hetzij via een proxy.<br/><br/> Als u het apparaat uitvoert als een Hyper-V-VM, hebt u voldoende resources op de Hyper-V-host nodig om 16 GB RAM-geheugen, 8 Vcpu's, ongeveer 80 GB aan opslag ruimte toe te wijzen en een externe switch voor de toestel-VM.<br/><br/> Als u het apparaat op een fysieke computer uitvoert, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten. 
**Vereisten voor Hyper-V** | Als u het apparaat met de VHD-sjabloon implementeert, is de virtuele machine van het apparaat van Azure Migrate Hyper-V VM versie 5,0.<br/><br/> Op de Hyper-V-host moet Windows Server 2012 R2 of later worden uitgevoerd. 
**Hashwaarde-VHD** | [Controleer](tutorial-assess-hyper-v.md#verify-security) de hash-waarden van de VHD-sjabloon.
**Hash-waarde-Power shell-script** | [Controleer](deploy-appliance-script.md#verify-file-security) de hash-waarden van het Power shell-script.


## <a name="appliance---physical"></a>Apparaat-fysiek

**Vereiste** | **Fysiek** 
--- | ---
**Machtigingen** | Als u de Web-App van het apparaat lokaal of op afstand wilt openen, moet u een domein beheerder of lokale beheerder op de apparaatapparaat zijn.
**Toestel onderdelen** | Het apparaat heeft de volgende onderdelen: <br/><br/> - **Beheer-app**: dit is een web-app voor gebruikers invoer tijdens de implementatie van het apparaat. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/> - **Detectie agent**: de agent verzamelt computer configuratie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/>- **Beoordelings agent**: de agent verzamelt prestatie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.<br/>- **Service voor automatische updates**: updates van de onderdelen van het apparaat (elke 24 uur wordt uitgevoerd).
**Ondersteunde implementatie** | Implementeren als een toegewezen fysieke machine, of een VM, met behulp van een Power shell-installatie script. Het script kan vanuit de portal worden gedownload.
**Project ondersteuning** |  Een apparaat kan worden gekoppeld aan een enkel project. <br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectie limieten** | Een apparaat kan Maxi maal 250 fysieke servers detecteren.
**PowerShell-script** | Down load het script (AzureMigrateInstaller. ps1) in een gezipte map vanuit de portal. [Meer informatie](tutorial-assess-physical.md#set-up-the-appliance). U kunt ook [rechtstreeks downloaden](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> De download grootte is 59,7 MB.
**Software/hardware** |  Het apparaat moet worden uitgevoerd op de computer met Windows Server 2016, 32-GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en vereist Internet toegang, hetzij rechtstreeks hetzij via een proxy.<br/><br/> Als u het apparaat op een fysieke computer uitvoert, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten.<br/> Het apparaat wordt niet ondersteund op een computer met Windows Server 2019.
**Hash-waarde** | [Controleer](deploy-appliance-script.md#verify-file-security) de hash-waarden van het Power shell-script.

## <a name="url-access"></a>URL-toegang

Het Azure Migrate-apparaat heeft verbinding met internet nodig.

- Wanneer u het apparaat implementeert, controleert Azure Migrate een connectiviteits controle op de vereiste Url's.
- U moet toegang tot alle Url's in de lijst toestaan. Als u alleen de evaluatie uitvoert, kunt u de Url's die zijn gemarkeerd als vereist voor VMware-agentloze migratie, overs Laan.
-  Als u een op URL gebaseerde proxy gebruikt om verbinding te maken met internet, moet u ervoor zorgen dat de proxy alle CNAME-records verhelpt die zijn ontvangen tijdens het opzoeken van de Url's.

### <a name="public-cloud-urls"></a>Url's voor open bare Clouds

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Ga naar Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Maak Azure Active Directory (AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.azure.com | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate-service.
*.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault. Opmerking: Zorg ervoor dat computers voor replicatie toegang hebben.
aka.ms/* | Toegang tot ook wel-koppelingen toestaan. Wordt gebruikt voor Azure Migrate apparaat-updates.
download.microsoft.com/download | Down loads van micro soft downloaden toestaan.
*.servicebus.windows.net | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Verbinding maken met Azure Migrate service-Url's.
*.hypervrecoverymanager.windowsazure.com | **Gebruikt voor VMware-migratie zonder agent**<br/><br/> Verbinding maken met Azure Migrate service-Url's.
*.blob.core.windows.net |  **Gebruikt voor VMware-migratie zonder agent**<br/><br/>Gegevens uploaden naar de opslag voor migratie.

### <a name="government-cloud-urls"></a>Cloud-Url's voor de overheid

**URL** | **Details**  
--- | --- |
*. portal.azure.us  | Ga naar Azure Portal.
graph.windows.net | Meld u aan bij uw Azure-abonnement.
login.microsoftonline.us  | Maak Azure Active Directory (AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.usgovcloudapi.net | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate-service.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
*. vault.usgovcloudapi.net | Geheimen beheren in de Azure Key Vault.
aka.ms/* | Toegang tot ook wel-koppelingen toestaan. Wordt gebruikt voor Azure Migrate apparaat-updates.
download.microsoft.com/download | Down loads van micro soft downloaden toestaan.
*. servicebus.usgovcloudapi.net  | Communicatie tussen het apparaat en de Azure Migrate service.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Verbinding maken met Azure Migrate service-Url's.
*. hypervrecoverymanager.windowsazure.us | **Gebruikt voor VMware-migratie zonder agent**<br/><br/> Verbinding maken met Azure Migrate service-Url's.
*. blob.core.usgovcloudapi.net  |  **Gebruikt voor VMware-migratie zonder agent**<br/><br/>Gegevens uploaden naar de opslag voor migratie.
*. applicationinsights.us | App-logboeken uploaden die worden gebruikt voor interne bewaking.





## <a name="collected-data---vmware"></a>Verzamelde gegevens-VMware

Het apparaat verzamelt meta gegevens, prestatie gegevens en informatie over afhankelijkheids analyse (als [afhankelijkheids analyse](concepts-dependency-visualization.md) zonder agent wordt gebruikt).

### <a name="metadata"></a>Metagegevens

Met de meta gegevens die door het Azure Migrate-apparaat worden gedetecteerd, kunt u nagaan of machines en apps gereed zijn voor migratie naar Azure, op de juiste manier computers en apps, kosten plannen en toepassings afhankelijkheden analyseren. Micro soft gebruikt deze gegevens niet in een controle op de naleving van licenties.

Hier ziet u de volledige lijst met virtuele VMware-VM-meta gegevens die door het apparaat worden verzameld en naar Azure worden verzonden.

**GEGEVENS** | **ITEM**
--- | --- 
**Computer Details** | 
VM-id | VM. Config. InstanceUuid 
VM-naam | VM. Config.Name
vCenter Server-ID | VMwareClient. instance. uuid
VM-beschrijving | VM. Summary. config. annotatie
Licentie product naam | VM. Client. ServiceContent. about. LicenseProductName
Besturingssysteemtype | VM. SummaryConfig.GuestFullName
Opstart type | VM. Config. firmware
Aantal kerngeheugens | VM. Config. hardware. NumCPU
Geheugen (MB) | VM. Config. hardware. MemoryMB
Aantal schijven | VM. Config. hardware. device. ToList (). FindAll (x => is VirtualDisk). Count
Lijst met schijf grootte | VM. Config. hardware. device. ToList (). FindAll (x => is VirtualDisk)
Lijst met netwerk adapters | VM. Config. hardware. device. ToList (). FindAll (x => is VirtualEthernet). Count
CPU-gebruik | CPU. usage. Average
Geheugen gebruik |mem. usage. Average
**Details per schijf** | 
Waarde van schijf sleutel | schijf. Prestatie
Dikunit-nummer | schijf. UnitNumber
Sleutel waarde van schijf controller | schijf. ControllerKey. waarde
Gigabytes ingericht | virtualDisk. DeviceInfo. summary
Schijf naam | De waarde die is gegenereerd met de schijf. UnitNumber, schijf. Sleutel, schijf. ControllerKey. waarde
Lees bewerkingen per seconde | virtualDisk. numberReadAveraged. Average
Schrijf bewerkingen per seconde | virtualDisk. numberWriteAveraged. Average
Lees doorvoer (MB per seconde) | virtualDisk. Read. Average
Schrijf doorvoer (MB per seconde) | virtualDisk. write. Average
**Details van de NIC** | 
Naam van netwerk adapter | adapter. Prestatie
MAC-adres | ((VirtualEthernetCard) NIC). MacAddress
IPv4-adressen | VM. Guest.Net
IPv6-adressen | VM. Guest.Net
Lees doorvoer (MB per seconde) | net. received. Average
Schrijf doorvoer (MB per seconde) | net. verzonden. gemiddeld
**Details van configuratiepad** | 
Name | verpakking. GetType (). Naam
Type onderliggend object | verpakking. ChildType
Referentie Details | verpakking. MoRef
Details van bovenliggend item | Container. Parent
Details van map per VM | ((Map) container). ChildEntity. type
Details van Data Center per VM | (Container (Data Center)). VmFolder
Details van Data Center per host-map | (Container (Data Center)). HostFolder
Cluster Details per host | ((ClusterComputeResource) container). Hostsite
Details van host per VM | ((HostSystem) container). VM

### <a name="performance-data"></a>Prestatiegegevens


Dit zijn de prestatie gegevens van de VMware-VM die door het apparaat worden verzameld en naar Azure worden verzonden.

**Gegevens** | **Item** | **Beoordelings impact**
--- | --- | ---
CPU-gebruik | CPU. usage. Average | Aanbevolen VM-grootte/kosten
Geheugen gebruik | mem. usage. Average | Aanbevolen VM-grootte/kosten
Lees doorvoer schijf (MB per seconde) | virtualDisk. Read. Average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Schrijf doorvoer schijf (MB per seconde) | virtualDisk. write. Average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Lees bewerkingen op de schijf per seconde | virtualDisk. numberReadAveraged. Average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Schrijf bewerkingen per seconde van schijf | virtualDisk. numberWriteAveraged. Average  | Berekening voor schijf grootte, opslag kosten, VM-grootte
Lees doorvoer van NIC (MB per seconde) | net. received. Average | Berekening voor VM-grootte
Doorvoer capaciteit van NIC (MB per seconde) | net. verzonden. gemiddeld  |Berekening voor VM-grootte

### <a name="app-dependencies-metadata"></a>Meta gegevens van app-afhankelijkheden

Bij een afhankelijkheids analyse zonder agent worden verbindings-en proces gegevens verzameld.

#### <a name="connection-data"></a>Verbindings gegevens

Dit zijn de verbindings gegevens die het apparaat verzamelt van elke virtuele machine die is ingeschakeld voor een afhankelijkheids analyse zonder agent. Deze gegevens worden naar Azure verzonden.

**Gegevens** | **Gebruikte opdracht** 
--- | --- 
Lokale poort | netstat
Lokaal IP-adres | netstat
Externe poort | netstat
Extern IP-adres | netstat
TCP-verbindings status | netstat
Proces-id | netstat
Aantal actieve verbindingen | netstat

#### <a name="process-data"></a>Gegevens verwerken
Dit zijn de proces gegevens die het apparaat verzamelt van elke virtuele machine die is ingeschakeld voor een afhankelijkheids analyse zonder agent. Deze gegevens worden naar Azure verzonden.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
Procesnaam | Win32_Process | ExecutablePath
Proces argumenten | Win32_Process | CommandLine
De naam van de toepassing | Win32_Process | De para meter VersionInfo. ProductName van de eigenschap ExecutablePath

#### <a name="linux-vm-data"></a>Gegevens van Linux-VM

Dit zijn de verbindings-en proces gegevens die het apparaat verzamelt van elke Linux-VM die is ingeschakeld voor de afhankelijkheids analyse zonder agent. Deze gegevens worden naar Azure verzonden.

**Gegevens** | **Gebruikte opdracht** 
--- | ---
Lokale poort | netstat 
Lokaal IP-adres | netstat 
Externe poort | netstat 
Extern IP-adres | netstat 
TCP-verbindings status | netstat 
Aantal actieve verbindingen | netstat
Proces-id  | netstat 
Procesnaam | PCL
Proces argumenten | PCL
De naam van de toepassing | met dpkg of rpm



## <a name="collected-data---hyper-v"></a>Verzamelde gegevens-Hyper-V

Het apparaat verzamelt meta gegevens, prestatie gegevens en informatie over afhankelijkheids analyse (als [afhankelijkheids analyse](concepts-dependency-visualization.md) zonder agent wordt gebruikt).

### <a name="metadata"></a>Metagegevens
Met de meta gegevens die door het Azure Migrate-apparaat worden gedetecteerd, kunt u nagaan of machines en apps gereed zijn voor migratie naar Azure, op de juiste manier computers en apps, kosten plannen en toepassings afhankelijkheden analyseren. Micro soft gebruikt deze gegevens niet in een controle op de naleving van licenties.

Hier vindt u de volledige lijst met meta gegevens van Hyper-V-VM'S die het apparaat verzamelt en verzendt naar Azure.

**GEGEVENS** | **WMI-KLASSE** | **WMI-KLASSE-EIGENSCHAP**
--- | --- | ---
**Computer Details** | 
Serie nummer van BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM-type (gen 1 of 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Weergave naam van VM | Msvm_VirtualSystemSettingData | ElementName
VM-versie | Msvm_ProcessorSettingData | VirtualQuantity
Geheugen (bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximale hoeveelheid geheugen die kan worden gebruikt door VM | Msvm_MemorySettingData | Limiet
Dynamisch geheugen ingeschakeld | Msvm_MemorySettingData | DynamicMemoryEnabled
Naam/versie/FQDN van het besturings systeem | Msvm_KvpExchangeComponent | Naam gegevens GuestIntrinsicExchangeItems
Energie status van de VM | Msvm_ComputerSystem | EnabledState
**Details per schijf** | 
Schijf-id | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Type virtuele harde schijf | Msvm_VirtualHardDiskSettingData | Type
Grootte van virtuele harde schijf | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Bovenliggende virtuele harde schijf | Msvm_VirtualHardDiskSettingData | ParentPath
**Details van de NIC** | 
IP-adressen (synthetische Nic's) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP ingeschakeld (synthetische Nic's) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (synthetische Nic's) | Msvm_SyntheticEthernetPortSettingData | InstanceID
MAC-adres van NIC (synthetische Nic's) | Msvm_SyntheticEthernetPortSettingData | Adres
NIC-ID (verouderde Nic's) | MsvmEmulatedEthernetPortSetting-gegevens | InstanceID
MAC-ID van NIC (verouderde Nic's) | MsvmEmulatedEthernetPortSetting-gegevens | Adres

### <a name="performance-data"></a>Prestatiegegevens

Dit zijn de prestatie gegevens van de Hyper-VM die het apparaat verzamelt en verzendt naar Azure.

**Klasse prestatie meter** | **Item** | **Beoordelings impact**
--- | --- | ---
Virtuele processor van Hyper-V Hyper Visor | % Gast-uitvoerings tijd | Aanbevolen VM-grootte/kosten
Hyper-V-dynamisch geheugen VM | Huidige belasting (%)<br/> Zichtbaar fysiek geheugen voor gast (MB) | Aanbevolen VM-grootte/kosten
Virtuele Hyper-V-opslag apparaat | Gelezen bytes per seconde | Berekening voor schijf grootte, opslag kosten, VM-grootte
Virtuele Hyper-V-opslag apparaat | Geschreven bytes per seconde | Berekening voor schijf grootte, opslag kosten, VM-grootte
Virtual Network Adapter voor Hyper-V | Ontvangen bytes per seconde | Berekening voor VM-grootte
Virtual Network Adapter voor Hyper-V | Verzonden bytes per seconde | Berekening voor VM-grootte

- CPU-gebruik is de som van alle gebruik voor alle virtuele processors die zijn gekoppeld aan een virtuele machine.
- Geheugen gebruik is (huidige druk * gast zichtbaar fysiek geheugen)/100.
- De waarden voor de schijf-en netwerk gebruik worden verzameld uit de vermelde Hyper-V-prestatie meter items.


## <a name="collected-data---physical"></a>Verzamelde gegevens-fysiek

Het apparaat verzamelt meta gegevens, prestatie gegevens en informatie over afhankelijkheids analyse (als [afhankelijkheids analyse](concepts-dependency-visualization.md) zonder agent wordt gebruikt).

### <a name="windows-metadata"></a>Windows-meta gegevens

Met de meta gegevens die door het Azure Migrate-apparaat worden gedetecteerd, kunt u nagaan of machines en apps gereed zijn voor migratie naar Azure, op de juiste manier computers en apps, kosten plannen en toepassings afhankelijkheden analyseren. Micro soft gebruikt deze gegevens niet in een controle op de naleving van licenties.

Hier volgt de volledige lijst met meta gegevens van Windows Server die door het apparaat worden verzameld en naar Azure worden verzonden.

**GEGEVENS** | **WMI-KLASSE** | **WMI-KLASSE-EIGENSCHAP**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domein, naam, PartOfDomain
Aantal processor kernen | Win32_PRocessor | NumberOfCores
Toegewezen geheugen | Win32_ComputerSystem | TotalPhysicalMemory
BIOS-serie nummer | Win32_ComputerSystemProduct | Nummer
BIOS-GUID | Win32_ComputerSystemProduct | MEE
Opstart type | Win32_DiskPartition | Controleren op partitie met type = **GPT: systeem** voor EFI/BIOS
Naam van besturings systeem | Win32_OperatingSystem | Caption
Besturingssysteemversie |Win32_OperatingSystem | Versie
Architectuur van besturings systeem | Win32_OperatingSystem | OSArchitecture
Aantal schijven | Win32_DiskDrive | Model, grootte, DeviceID, media type, naam
Schijfgrootte | Win32_DiskDrive | Grootte
NIC-lijst | Win32_NetworkAdapterConfiguration | Beschrijving, index
IP-adres van NIC | Win32_NetworkAdapterConfiguration | IPAddress
MAC-adres van NIC | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux-meta gegevens

Hier volgt de volledige lijst met meta gegevens van de Linux-server die het apparaat verzamelt en verzendt naar Azure.

**GEGEVENS** | **SPREEK** 
--- | --- 
FQDN | kat/proc/sys/kernel/hostname, hostnaam-f
Aantal processor kernen |  /proc/cpuinfo \| awk '/^ processor/{print $3} ' \| wc-l
Toegewezen geheugen | kat/proc/meminfo \| grep MemTotal \| awk {printf "%. 0f", $2/1024}
BIOS-serie nummer | lshw \| grep "serieel:" \| Head-N1 \| awk {print $2} " <br/> /usr/sbin/dmidecode-t 1 \| grep ' serie ' \| awk ' {$1 = ""; $2 = ""; afdrukken} '
BIOS-GUID | kat/sys/class/DMI/id/product_uuid
Opstart type | [-d/sys/firmware/EFI]  && ECHO EFI- \| \| echo BIOS
Naam/versie van besturings systeem | We hebben toegang tot deze bestanden voor de versie van het besturings systeem en de naam:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architectuur van besturings systeem | Uname-m
Aantal schijven | fdisk-l \| egrep ' Disk. * bytes ' \| awk ' {Print $2} ' \| knippen-F1-d ': '
Opstart schijf | DF/boot \| sed-n 2p \| awk {print $1}
Schijfgrootte | fdisk-l \| egrep ' Disk. * bytes ' \| egrep $disk: \| awk ' {Print $5} '
NIC-lijst | IP-o-4 adres show \| awk {print $2}
IP-adres van NIC | IP-adres geven $nic \| grep inet \| awk ' {Print $2} ' \| knippen-F1-d '/' 
MAC-adres van NIC | IP-adres geeft $nic \| grep- \| awk ' {Print $2} ' weer

### <a name="windows-performance-data"></a>Prestatie gegevens van Windows

Dit zijn de prestatie gegevens van de Windows-Server die het apparaat verzamelt en verzendt naar Azure.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
CPU-gebruik | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Geheugengebruik | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Aantal NIC'S | Win32_PerfFormattedData_Tcpip_NetworkInterface | Het aantal netwerk apparaten ophalen.
Ontvangen gegevens per NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Gegevens die per NIC worden verzonden | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Aantal schijven | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Aantal schijven
Details van schijf | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux-prestatie gegevens

Dit zijn de prestatie gegevens van de Linux-server die het apparaat verzamelt en verzendt naar Azure.

**Gegevens** | **Linux** 
--- | --- 
CPU-gebruik | kat/proc/stat/| grep ' CPU '/proc/stat
Geheugengebruik | gratis \| grep \| -mem awk ' {Print $3/$ 2 * 100,0} '
Aantal NIC'S | lshw-class Network \| grep ETH [0-60] \| wc-l
Ontvangen gegevens per NIC | kat/sys/class/net/ETH $ NIC/statistieken/rx_bytes
Gegevens die per NIC worden verzonden | kat/sys/class/net/ETH $ NIC/statistieken/tx_bytes
Aantal schijven | fdisk-l \| egrep ' Disk. * bytes ' \| awk ' {Print $2} ' \| knippen-F1-d ': '
Details van schijf | kat/proc/diskstats


## <a name="appliance-upgrades"></a>Toestel-upgrades

Het apparaat wordt bijgewerkt wanneer de Azure Migrate agents die op het apparaat worden uitgevoerd, worden bijgewerkt. Dit gebeurt automatisch, omdat automatisch bijwerken standaard is ingeschakeld op het apparaat. U kunt deze standaard instelling wijzigen om de toestel Services hand matig bij te werken.

### <a name="turn-off-auto-update"></a>Automatisch bijwerken uitschakelen

1. Open de REGI ster-editor op de computer waarop het apparaat wordt uitgevoerd.
2. Ga naar **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**.
3. Als u automatisch bijwerken wilt uitschakelen, maakt u een register sleutel auto **Update** -sleutel met DWORD-waarde 0.

    ![Register sleutel instellen](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Automatisch bijwerken inschakelen

U kunt automatisch bijwerken inschakelen met een van de volgende methoden:

- Door de register sleutel AutoUpdate van HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance. te verwijderen
- Nadat de detectie is voltooid, kunt u in de Configuration Manager van het apparaat.

De register sleutel verwijderen:

1. Open de REGI ster-editor op de computer waarop het apparaat wordt uitgevoerd.
2. Ga naar **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**.
3. Verwijder de register sleutel auto **Update**die eerder is gemaakt om automatisch bijwerken uit te scha kelen.

Als u van apparaat Configuration Manager wilt inschakelen, nadat de detectie is voltooid:

1. Open de Configuration Manager van het apparaat op de apparaats machine.
2. Klik in **toestel Services**  >  **Automatic Update van Azure migrate-onderdelen is uitgeschakeld**op automatische updates inschakelen.

    ![Automatische updates inschakelen](./media/migrate-appliance/turn-on.png)

### <a name="check-the-appliance-services-version"></a>Controleer de versie van het toestel Services

U kunt de versie van het toestel nummer controleren aan de hand van een van de volgende methoden:

- Als de detectie is voltooid, is het apparaat Configuration Manager.
- Op de apparaatapparaat, in **Control Panel**  >  **Program ma's en onderdelen**van het configuratie scherm.

De Configuration Manager van het apparaat inchecken:

1. Nadat de detectie is voltooid, opent u het apparaat Configuration Manager (in de Web-App van het apparaat).
2. Controleer de versies van de services van het apparaat in de **service apparaten**.

    ![Versie controleren](./media/migrate-appliance/version.png)

Controleren in het configuratie scherm:

1. Klik op het apparaat op **Start**  >  **configuratie scherm**  >  **Program ma's en onderdelen** starten.
2. Controleer de versies van de toestel Services in de lijst.

    ![Controleer de versie in het configuratie scherm](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Een oudere versie hand matig bijwerken

Als u een oudere versie voor een van de onderdelen uitvoert, moet u de service verwijderen en hand matig bijwerken naar de nieuwste versie.

1. [Down load](https://aka.ms/latestapplianceservices) het bestand LatestComponents. json om te controleren op de nieuwste versies van de service van het apparaat.
2.    Na het downloaden opent u het bestand LatestComponents. json in Klad blok.
3. Zoek de nieuwste service versie in het bestand en de download koppeling hiervoor. Bijvoorbeeld:

    "Naam": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "versie": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Down load de nieuwste versie van een verouderde service met behulp van de download koppeling in het bestand.
5. Nadat u hebt gedownload, voert u de volgende opdracht uit in een Administrator-opdracht venster om de integriteit van de gedownloade MSI te controleren.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```Bijvoorbeeld: C: \> certutil-HashFile C:\USERS\PUBLIC\DOWNLOADS\MICROSOFTAZUREAPPLIANCECONFIGURATIONMANAGER.msi MD5

5. Controleer of de uitvoer van de opdracht overeenkomt met de invoer van de hashwaarde voor de service in het bestand (bijvoorbeeld de bovenstaande MD5-hash-waarde).
6. Voer nu het MSI-bestand uit om de service te installeren. Het is een stille installatie en het installatie venster wordt gesloten nadat het is uitgevoerd.
7. Nadat de installatie is voltooid, controleert u de versie van de **Control panel**service in de  >  **Program ma's en onderdelen**van het configuratie scherm. De service versie moet nu worden bijgewerkt naar de meest recente weer gave in het JSON-bestand.



## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](how-to-set-up-appliance-vmware.md) het instellen van het apparaat voor VMware.
- [Meer informatie over](how-to-set-up-appliance-hyper-v.md) het instellen van het apparaat voor Hyper-V.
- [Meer informatie over](how-to-set-up-appliance-physical.md) het instellen van het apparaat voor fysieke servers.

