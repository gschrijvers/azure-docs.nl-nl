---
title: Wat is Azure Backup?
description: Biedt een overzicht van de Azure Backup-service en hoe deze bijdraagt aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240294"
---
# <a name="what-is-the-azure-backup-service"></a>Wat is de Azure Backup-Service?

De Azure Backup-service biedt eenvoudige, beveiligde en kosteneffectieve oplossingen om een back-up te maken van uw gegevens en die te herstellen vanuit Microsoft Azure-cloud.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Waarvan kan ik een back-up maken?

- **On-premises** -back-ups maken van bestanden, mappen, systeem status met behulp van de [Microsoft Azure Recovery Services (Mars)-agent](backup-support-matrix-mars-agent.md). Of gebruik de DPM-of Azure Backup Server-Agent (MABS) om on-premises virtuele machines ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) en [VMware](backup-azure-backup-server-vmware.md)) en andere [on-premises werk belastingen](backup-mabs-protection-matrix.md) te beveiligen
- **Azure-vm's** - [maken back-ups van volledige Windows/Linux-vm's](backup-azure-vms-introduction.md) (met back-upextensies) of maken back-ups van bestanden, mappen en systeem status met behulp van de Mars- [agent](backup-azure-manage-mars.md).
- **Azure files-shares** - [maken back-ups van Azure-bestands shares met een opslag account](backup-afs.md)
- **SQL Server in azure vm's** -  [back-ups maken van SQL server data bases die worden uitgevoerd op virtuele Azure-machines](backup-azure-sql-database.md)
- **SAP Hana-data bases in azure vm's** - [back-up SAP Hana data bases die worden uitgevoerd op virtuele machines](backup-azure-sap-hana-database.md)

![Overzicht van Azure Backup](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Waarom Azure Backup gebruiken?

Azure Backup biedt deze belangrijke voordelen:

- **On-premises back-up op locatie**: Azure Backup biedt een eenvoudige oplossing voor het maken van back-ups van uw on-premises resources naar de Cloud. Back-ups op korte en lange termijn zonder de noodzaak om complexe on-premises back-upoplossingen te implementeren.
- **Back-ups maken van Azure IaaS vm's**: Azure Backup biedt onafhankelijke en geïsoleerde back-ups om te beschermen tegen onbedoelde vernietiging van originele gegevens. Back-ups worden opgeslagen in een Recovery Services kluis met ingebouwd beheer van herstel punten. Configuratie en schaal baarheid zijn eenvoudig, back-ups worden geoptimaliseerd en u kunt zo nodig eenvoudig herstellen.
- **Gemakkelijk schalen**: Azure Backup gebruikt de onderliggende kracht en onbeperkte schaal van de Azure-cloud voor hoge beschikbaarheid, zonder overhead voor onderhoud of bewaking.
- **Onbeperkte gegevens overdracht ophalen**: Azure backup beperkt de hoeveelheid inkomende of uitgaande gegevens die u overdraagt of factureert voor de gegevens die worden overgedragen.
  - Uitgaande gegevens zijn gegevens die tijdens een herstelbewerking worden overgebracht uit een Recovery Services-kluis.
  - Als u een offline initiële back-up uitvoert met behulp van de Azure import/export-service om grote hoeveel heden gegevens te importeren, zijn er kosten verbonden aan inkomende gegevens.  [Meer informatie](backup-azure-backup-import-export.md).
- Beveilig **uw gegevens**: Azure Backup biedt oplossingen voor het beveiligen van gegevens die [onderweg en in](backup-azure-security-feature.md) [rust](backup-azure-security-feature-cloud.md)zijn.
- **Gecentraliseerde bewaking en beheer**: Azure Backup biedt [ingebouwde mogelijkheden voor bewaking en waarschuwingen](backup-azure-monitoring-built-in-monitor.md) in een Recovery Services kluis. Deze mogelijkheden zijn beschikbaar zonder enige extra beheer infrastructuur. U kunt de schaal van uw bewaking en rapportage ook verg Roten met [behulp van Azure monitor](backup-azure-monitoring-use-azuremonitor.md).
- **App-consistente back-ups ophalen**: een toepassings consistente back-up betekent dat een herstel punt alle vereiste gegevens heeft om de back-up te herstellen. Azure Backup biedt toepassings consistente back-ups, waardoor er geen extra oplossingen nodig zijn om de gegevens te herstellen. Herstellen van toepassingsconsistente gegevens verkort de hersteltijd, zodat u snel weer normaal aan het werk kunt.
- **Gegevens over korte en lange termijn behouden**: u kunt [Recovery Services kluizen](backup-azure-recovery-services-vault-overview.md) gebruiken voor het bewaren van gegevens op de korte en lange termijn.
- **Automatisch opslagbeheer**: voor hybride omgevingen is vaak heterogene opslag vereist, soms on-premises en soms in de cloud. Met Azure Backup zijn er geen kosten verbonden aan het gebruik van on-premises opslag apparaten. De back-upopslag wordt automatisch door Azure Backup toegewezen en beheerd en u betaalt naar gebruik. U betaalt dus alleen voor de opslag die u gebruikt. [Lees meer](https://azure.microsoft.com/pricing/details/backup) over prijzen.
- **Meerdere opslagopties**: Azure Backup biedt twee typen replicatie om uw opslag/gegevens maximaal beschikbaar te houden.
  - Met [lokaal redundante opslag LRS](../storage/common/storage-redundancy-lrs.md) worden uw gegevens drie keer gerepliceerd (er worden drie kopieën gemaakt van uw gegevens) in een opslagschaaleenheid in een datacenter. Alle kopieën van de gegevens komen binnen dezelfde regio voor. LRS is een goedkope optie voor het beschermen van uw gegevens tegen lokale hardwarefouten.
  - [Geografisch redundante opslag (GRS)](../storage/common/storage-redundancy-grs.md) is de standaardinstelling en is de replicatieoptie die wordt aanbevolen. Met GRS worden uw gegevens gerepliceerd naar een secundaire regio (honderden kilometers verwijderd van de primaire locatie van de brongegevens). GRS kost meer dan LRS, maar GRS biedt een hoger duurzaamheids niveau voor uw gegevens, zelfs als er sprake is van een regionale storing.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk](backup-architecture.md) de architectuur en componenten voor verschillende back-upscenario's.
- [Controleer](backup-support-matrix.md) de ondersteunings vereisten en beperkingen voor back-ups en voor [Azure VM-back-ups](backup-support-matrix-iaas.md).
