---
title: Problemen met de visualisatie van analyses en afhankelijkheden in Azure Migrate oplossen
description: Krijg hulp bij het oplossen van problemen met de evaluatie en afhankelijkheids visualisatie in Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127681"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Problemen met de evaluatie/afhankelijkheid oplossen

Dit artikel helpt u bij het oplossen van problemen met de visualisatie van evalueren en afhankelijkheden met [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemen met de gereedheids beoordeling

Los problemen met de voorbereidings voorbereiding op als volgt:

**Probleem** | **Fix**
--- | ---
Niet-ondersteund opstart type | Azure biedt geen ondersteuning voor Vm's met een EFI-opstart type. U wordt aangeraden het opstart type te converteren naar BIOS voordat u een migratie uitvoert. <br/><br/>U kunt Azure Migrate server migratie gebruiken om de migratie van dergelijke Vm's te verwerken. Tijdens de migratie wordt het opstart type van de VM naar het BIOS geconverteerd.
Voorwaardelijk ondersteund Windows-besturings systeem | Het besturings systeem heeft de eind datum van de ondersteuning door gegeven en heeft een aangepaste ondersteunings overeenkomst (CSA) nodig voor [ondersteuning in azure](https://aka.ms/WSosstatement). Overweeg om te upgraden voordat u naar Azure migreert.
Niet-ondersteund Windows-besturings systeem | Azure ondersteunt alleen [geselecteerde versies van Windows-besturings systemen](https://aka.ms/WSosstatement). U kunt de machine upgraden voordat u naar Azure migreert.
Voorwaardelijk goedgekeurd Linux-besturings systeem | Azure bevestigt alleen [geselecteerde Linux-besturingssysteem versies](../virtual-machines/linux/endorsed-distros.md). U kunt de machine upgraden voordat u naar Azure migreert.
Niet-goedgekeurd Linux-besturings systeem | De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. U kunt een upgrade uitvoeren naar een [officiële versie van Linux](../virtual-machines/linux/endorsed-distros.md) voordat u naar Azure migreert.
Onbekend besturings systeem | Het besturings systeem van de virtuele machine is opgegeven als ' andere ' in vCenter Server. Dit gedrag blokkeert Azure Migrate van het controleren van de Azure-gereedheid van de virtuele machine. Zorg ervoor dat het besturings systeem wordt [ondersteund](https://aka.ms/azureoslist) door Azure voordat u de computer migreert.
Niet-ondersteunde bits versie | Vm's met een 32-bits besturings systeem kunnen worden opgestart in azure, maar het wordt aanbevolen dat u een upgrade uitvoert naar 64-bits voordat u naar Azure migreert.
Vereist een micro soft Visual Studio-abonnement | Op de computer wordt een Windows client-besturings systeem uitgevoerd, dat alleen wordt ondersteund via een Visual Studio-abonnement.
Er is geen VM gevonden voor de vereiste opslag prestaties | De opslag prestaties (invoer/uitvoer-bewerkingen per seconde [IOPS] en door Voer) die vereist zijn voor de computer, overschrijden de ondersteuning voor Azure-VM'S. Verminder de opslag vereisten voor de machine vóór de migratie.
Er is geen VM gevonden voor de vereiste netwerk prestaties | De netwerk prestaties (in/uit) die vereist zijn voor de computer, overschrijden de ondersteuning voor Azure-VM'S. Verminder de netwerk vereisten voor de computer.
De virtuele machine is niet gevonden op de opgegeven locatie | Gebruik een andere doel locatie vóór de migratie.
Een of meer niet-geschikte schijven | Een of meer schijven die zijn gekoppeld aan de virtuele machine voldoen niet aan de vereisten van Azure. Één<br/><br/> Azure Migrate: Server analyse biedt momenteel geen ondersteuning voor Ultra-SSD schijven en evalueert de schijven op basis van de schijf limieten voor Premium Managed disks (32 TB).<br/><br/> Zorg ervoor dat de grootte van de schijf < 64 TB (ondersteund door Ultra-SSD schijven) voor elke schijf die aan de VM is gekoppeld.<br/><br/> Als dat niet het geval is, vermindert u de schijf grootte voordat u naar Azure migreert, of gebruikt u meerdere schijven in Azure en [stript u deze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslag limieten te krijgen. Zorg ervoor dat de prestaties (IOPS en door Voer) die nodig zijn voor elke schijf worden ondersteund door door Azure [beheerde virtuele-machine schijven](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits).
Een of meer niet-geschikte netwerk adapters. | Verwijder ongebruikte netwerk adapters van de machine voordat de migratie wordt gebruikt.
Aantal schijven overschrijdt de limiet | Verwijder ongebruikte schijven van de machine vóór de migratie.
De schijf grootte overschrijdt de limiet | Azure Migrate: Server analyse biedt momenteel geen ondersteuning voor Ultra-SSD schijven en controleert de schijven op basis van Premium-schijf limieten (32 TB).<br/><br/> Azure ondersteunt echter schijven met een grootte van Maxi maal 64 TB (ondersteund door Ultra-SSD schijven). Verklein schijven tot minder dan 64 TB vóór de migratie of gebruik meerdere schijven in Azure en [strip deze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslag limieten te krijgen.
De schijf is niet beschikbaar op de opgegeven locatie | Zorg ervoor dat de schijf zich op de doel locatie bevindt voordat u migreert.
De schijf is niet beschikbaar voor de opgegeven redundantie | De schijf moet het opslag type redundantie gebruiken dat is gedefinieerd in de instellingen voor evaluatie (standaard LRS).
Kan de schijf geschiktheid niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
VM met vereiste kernen en geheugen niet gevonden | Azure kan geen geschikt VM-type vinden. Verminder het geheugen en het aantal kernen van de on-premises machine voordat u migreert.
Kan de geschiktheid van de virtuele machine niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid van een of meer schijven niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid voor een of meer netwerk adapters niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.

## <a name="linux-vms-are-conditionally-ready"></a>Linux-Vm's zijn ' voorwaardelijk gereed '

Server Assessment markeert Linux Vm's als ' voorwaardelijk gereed ' vanwege een bekende onderbreking in Server evaluatie.

- De tussen ruimte voor komt dat de secundaire versie van het Linux-besturings systeem dat is geïnstalleerd op de on-premises Vm's kan worden gedetecteerd.
- Voor RHEL 6,10 wordt momenteel alleen RHEL 6 als de versie van het besturings systeem gedetecteerd.
-  Omdat Azure alleen specifieke versies van Linux afschrijft, zijn de virtuele Linux-machines momenteel gemarkeerd als voorwaardelijk gereed in Server evaluatie.
- U kunt bepalen of het Linux-besturings systeem dat wordt uitgevoerd op de on-premises VM, wordt goedgekeurd in azure door [Azure Linux-ondersteuning](https://aka.ms/migrate/selfhost/azureendorseddistros)te controleren.
-  Nadat u de geviseerde distributie hebt gecontroleerd, kunt u deze waarschuwing negeren.

## <a name="azure-skus-bigger-than-on-premises"></a>Azure-Sku's die groter zijn dan on-premises

Azure Migrate server-evaluatie kan Azure VM Sku's aanbevelen met meer kernen en geheugen dan de huidige on-premises toewijzing op basis van het type evaluatie:


- De aanbeveling van de VM-SKU is afhankelijk van de evaluatie-eigenschappen.
- Dit wordt beïnvloed door het type beoordeling dat u uitvoert in Server beoordeling: op *basis van prestaties*of *als on-premises*.
- Voor evaluaties op basis van prestaties worden de gebruiks gegevens van de on-premises Vm's (CPU, geheugen, schijf en netwerk gebruik) beschouwd om de juiste doel-VM-SKU voor uw on-premises Vm's te bepalen. Er wordt ook een comfort factor toegevoegd bij het bepalen van effectief gebruik.
- Voor on-premises grootte worden de prestatie gegevens niet in overweging genomen en wordt de doel-SKU aanbevolen op basis van on-premises toewijzing.

Om te laten zien hoe dit kan invloed hebben op aanbevelingen, gaan we een voor beeld doen:

We hebben een on-premises VM met vier kernen en acht GB geheugen, met een CPU-gebruik van 50% en een geheugen gebruik van 50% en een opgegeven comfort factor van 1,3.

-  Als de evaluatie **on-premises**is, wordt een Azure VM-SKU met vier kernen en 8 GB geheugen aanbevolen.
- Als de evaluatie op basis van prestaties is gebaseerd op een effectief CPU-en geheugen gebruik (50% van 4 kernen * 1,3 = 2,6 kernen en 50% van 8 GB geheugen * 1,3 = 5,3-GB geheugen), wordt de goedkoopste VM-SKU van vier kernen (dichtstbijgelegen ondersteunde kernen) en acht GB aan geheugen (dichtstbijgelegen ondersteunde geheugen grootte) aanbevolen.
- Meer [informatie](concepts-assessment-calculation.md#types-of-assessments) over de beoordelings grootte.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure Disk Sku's groter dan on-premises

Azure Migrate server beoordeling kan een grotere schijf aanbevelen op basis van het type evaluatie.
- De schijf grootte in Server analyse is afhankelijk van twee evaluatie-eigenschappen: grootte criteria en opslag type.
- Als de grootte criteria **op basis van prestaties**zijn en het opslag type is ingesteld op **automatisch**, worden de IOPS-en doorvoer waarden van de schijf beschouwd bij het identificeren van het doel schijf type (Standard-HDD, Standard-SSD of Premium). Er wordt vervolgens een schijf-SKU van het schijf type aanbevolen en de aanbeveling houdt rekening met de grootte vereisten van de on-premises schijf.
- Als de grootte criteria **op basis van prestaties**zijn en het opslag type **Premium**is, wordt een Premium-schijf-SKU in azure aanbevolen op basis van de IOPS-, doorvoer-en grootte vereisten van de on-premises schijf. Dezelfde logica wordt gebruikt voor het uitvoeren van schijf grootte wanneer de grootte criteria **op locatie** zijn en het opslag type is **Standard-HDD**, **Standard-SSD**of **Premium**.

Als u bijvoorbeeld een on-premises schijf met 32 GB geheugen hebt, maar de geaggregeerde Lees-en schrijf-IOPS voor de schijf 800 IOPS is, raadt de server bepaling een Premium-schijf aan (vanwege de hogere IOPS-vereisten). vervolgens wordt een schijf-SKU aanbevolen die de vereiste IOPS en grootte kan ondersteunen. In dit voorbeeld komen we dan uit bij P15 (256 GB, 1100 IOPS). Hoewel de grootte die de on-premises schijf vereist, 32 GB was, raadt server evaluatie een grotere schijf aan vanwege de hoge IOPS-vereiste van de on-premises schijf.

## <a name="utilized-corememory-percentage-missing"></a>Het gebruikte kern/geheugen percentage ontbreekt

Server Assessment rapporteert "PercentageOfCoresUtilizedMissing" of "PercentageOfMemoryUtilizedMissing" wanneer het Azure Migrate apparaat geen prestatie gegevens kan verzamelen voor de relevante on-premises Vm's.

- Dit kan gebeuren als de virtuele machines tijdens de evaluatie duur zijn uitgeschakeld. Het apparaat kan geen prestatie gegevens voor een virtuele machine verzamelen wanneer deze is uitgeschakeld.
- Als alleen de geheugen items ontbreken en u probeert virtuele Hyper-V-machines te evalueren, controleert u of u dynamisch geheugen hebt ingeschakeld op deze virtuele machines. Er is alleen een bekend probleem voor virtuele Hyper-V-machines, waarin een Azure Migrate apparaat geen geheugen gebruiks gegevens kan verzamelen voor Vm's waarvoor geen dynamisch geheugen is ingeschakeld.
- Als een van de prestatie meter items ontbreekt, wordt de analyse van Azure Migrate server terugvallen op de toegewezen kernen en het geheugen en wordt een bijbehorende VM-grootte aanbevolen.
- Als alle prestatie meter items ontbreken, controleert u of aan de toegangs vereisten voor de poort voor evaluatie is voldaan. Meer informatie over de toegangs vereisten voor de poort voor [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access), [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) en [fysieke](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) server beoordeling.

## <a name="is-the-operating-system-license-included"></a>Is de licentie voor het besturings systeem opgenomen?

Azure Migrate server-evaluatie beschouwt momenteel alleen de licentie kosten van het besturings systeem voor Windows-computers. De licentie kosten voor Linux-machines worden momenteel niet in overweging genomen.

## <a name="how-does-performance-based-sizing-work"></a>Hoe werkt de grootte op basis van prestaties?

Met Server Assessment worden doorlopend prestatiegegevens van on-premises machines verzameld, die vervolgens worden gebruikt om SKU's voor VM en schijf aan te bevelen in Azure. [Meer informatie over het](concepts-assessment-calculation.md#calculate-sizing-performance-based) verzamelen van gegevens op basis van prestaties.


## <a name="dependency-visualization-in-azure-government"></a>Afhankelijkheids visualisatie in Azure Government

Azure Migrate is afhankelijk van Servicetoewijzing voor de visualisatie functionaliteit van de afhankelijkheid. Omdat Servicetoewijzing momenteel niet beschikbaar is in Azure Government, is deze functionaliteit niet beschikbaar in Azure Government.

## <a name="dependencies-dont-show-after-agent-install"></a>Afhankelijkheden worden niet weer gegeven na installatie van agent

Nadat u de afhankelijkheids visualisatie agenten hebt geïnstalleerd op on-premises Vm's, duurt Azure Migrate doorgaans 15-30 minuten om de afhankelijkheden in de portal weer te geven. Als u langer dan 30 minuten hebt gewacht, moet u ervoor zorgen dat de micro soft Monitoring Agent (MMA) verbinding kan maken met de Log Analytics-werk ruimte.

Voor Windows-Vm's:
1. Start MMA in het configuratie scherm.
2. Controleer > in de **Eigenschappen van micro soft Monitoring Agent****Azure log Analytics (OMS)** of de **status** van de werk ruimte groen is.
3. Als de status niet groen is, probeert u de werk ruimte te verwijderen en toe te voegen aan MMA.

    ![MMA-status](./media/troubleshoot-assessment/mma-properties.png)

Voor virtuele Linux-machines moet u ervoor zorgen dat de installatie opdrachten voor MMA en de afhankelijkheids agent zijn geslaagd.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- **MMS-agent**: Bekijk de ondersteunde [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)-en [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) -besturings systemen.
- **Afhankelijkheids agent**: de ondersteunde [Windows-en Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) -besturings systemen.

## <a name="visualize-dependencies-for--hour"></a>Afhankelijkheden voor > uur visualiseren

Hoewel u met Azure Migrate kunt terugkeren naar een bepaalde datum in de afgelopen maand, is de maximale duur waarvoor u de afhankelijkheden kunt visualiseren één uur.

U kunt bijvoorbeeld de functie tijd duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt ze alleen bekijken voor een periode van één uur.

U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [de afhankelijkheids gegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) op te vragen.

## <a name="visualized-dependencies-for--10-machines"></a>Gevisualiseerde afhankelijkheden voor > 10-computers

In Azure Migrate server-evaluatie kunt u [afhankelijkheden voor groepen](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) met Maxi maal tien vm's visualiseren. Voor grotere groepen wordt u aangeraden de Vm's te splitsen in kleinere groepen om afhankelijkheden te visualiseren.

## <a name="machines-show-install-agent"></a>Computers ' agent installeren ' weer geven

Na de migratie van computers met afhankelijkheids visualisatie ingeschakeld op Azure, kunnen computers de actie ' agent installeren ' in plaats van ' afhankelijkheden weer geven ' weer geven vanwege het volgende gedrag:


- Na de migratie naar Azure zijn on-premises machines uitgeschakeld en zijn er vergelijk bare Vm's in Azure. Deze machines halen een ander MAC-adres op.
- Computers kunnen ook een ander IP-adres hebben, op basis van het feit of u het on-premises IP-adres hebt behouden of niet.
- Als zowel MAC-als IP-adressen verschillen van on-premises, Azure Migrate de on-premises machines niet koppelen aan gegevens van Servicetoewijzing afhankelijkheid. In dit geval wordt de optie voor het installeren van de agent weer gegeven in plaats van het weer geven van afhankelijkheden.
- Na een test migratie naar Azure blijven on-premises machines ingeschakeld zoals verwacht. Gelijkwaardige machines die in Azure worden ingezet, halen een ander MAC-adres op en kunnen verschillende IP-adressen verkrijgen. Tenzij u uitgaand Azure Monitor logboek verkeer van deze computers blokkeert, Azure Migrate de on-premises machines niet koppelen aan gegevens van Servicetoewijzing afhankelijkheden, waardoor de optie voor het installeren van agents wordt weer gegeven in plaats van afhankelijkheden weer te geven.


## <a name="capture-network-traffic"></a>Netwerk verkeer vastleggen

Verzamel logboeken voor netwerk verkeer als volgt:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Druk op F12 om Ontwikkelhulpprogramma's te starten. Als dat nodig is, schakelt u de instelling **vermeldingen wissen bij navigatie** uit.
3. Selecteer het tabblad **netwerk** en begin met het vastleggen van netwerk verkeer:
   - In Chrome selecteert u **logboek behouden**. De opname moet automatisch worden gestart. Een rode cirkel geeft aan dat verkeer wordt vastgelegd. Als de rode cirkel niet wordt weer gegeven, selecteert u de zwarte cirkel om te starten.
   - In micro soft Edge en Internet Explorer moet de opname automatisch worden gestart. Als dat niet het geval is, selecteert u de groene knop afspelen.
4. Probeer de fout te reproduceren.
5. Nadat u de fout hebt aangetroffen tijdens het opnemen, stoppen met opnemen en opslaan van een kopie van de geregistreerde activiteit:
   - Klik in Chrome met de rechter muisknop en selecteer **Opslaan als har met inhoud**. Met deze actie worden de logboeken gecomprimeerd en geëxporteerd als een. har-bestand.
   - Selecteer in micro soft Edge of Internet Explorer de optie **vastgelegd verkeer exporteren** . Met deze actie wordt het logboek gecomprimeerd en geëxporteerd.
6. Selecteer het tabblad **console** om te controleren of er waarschuwingen of fouten zijn. Het console logboek opslaan:
   - Klik in Chrome met de rechter muisknop op een wille keurige plaats in het console logboek. Selecteer **Opslaan als**, om het logboek te exporteren en uit te voeren.
   - Klik in micro soft Edge of Internet Explorer met de rechter muisknop op de fouten en selecteer **Alles kopiëren**.
7. Ontwikkelhulpprogramma's sluiten.

## <a name="next-steps"></a>Volgende stappen

Een evaluatie [maken](how-to-create-assessment.md) of [aanpassen](how-to-modify-assessment.md) .
