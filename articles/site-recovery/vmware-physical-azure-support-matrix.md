---
title: Ondersteunings matrix voor nood herstel voor VMware/fysiek in Azure Site Recovery
description: Hierin wordt een overzicht gegeven van de ondersteuning voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure met behulp van Azure Site Recovery.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: d8e7b2f8f6483d462f781d95011ef7b972e83b87
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801787"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Ondersteunings matrix voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure

In dit artikel vindt u een overzicht van de ondersteunde onderdelen en instellingen voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure met behulp van [Azure site Recovery](site-recovery-overview.md).

- Meer [informatie](vmware-azure-architecture.md) over de architectuur voor herstel na nood gevallen voor de virtuele VMWare-machine of fysieke server.
- Volg de [zelf studies](tutorial-prepare-azure.md) om herstel na nood gevallen uit te proberen.

## <a name="deployment-scenarios"></a>Implementatiescenario's

**Scenario** | **Details**
--- | ---
Herstel na nood geval voor virtuele VMware-machines | Replicatie van on-premises virtuele VMware-machines naar Azure. U kunt dit scenario implementeren in de Azure Portal of met behulp van [Power shell](vmware-azure-disaster-recovery-powershell.md).
Herstel na nood geval van fysieke servers | Replicatie van on-premises fysieke Windows/Linux-servers naar Azure. U kunt dit scenario implementeren in de Azure Portal.

## <a name="on-premises-virtualization-servers"></a>On-premises virtualisatieservers

**Server** | **Vereisten** | **Details**
--- | --- | ---
vCenter Server | Versie 6,7, 6,5, 6,0 of 5,5 | U wordt aangeraden een vCenter-Server te gebruiken in uw implementatie voor herstel na nood gevallen.
vSphere-hosts | Versie 6,7, 6,5, 6,0 of 5,5 | We raden aan dat vSphere-hosts en vCenter-servers zich in hetzelfde netwerk bevinden als de proces server. Standaard wordt de proces server uitgevoerd op de configuratie server. [Meer informatie](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Configuratie Server Site Recovery

De configuratie server is een on-premises machine waarop Site Recovery-onderdelen worden uitgevoerd, met inbegrip van de configuratie server, de proces server en de hoofddoel server.

- Voor virtuele VMware-machines stelt u de configuratie server in door een OVF-sjabloon te downloaden om een virtuele VMware-machine te maken.
- Voor fysieke servers stelt u de computer van de configuratie server hand matig in.

**Component** | **Vereisten**
--- |---
CPU-kernen | 8
RAM | 16 GB
Aantal schijven | 3 schijven<br/><br/> Schijven bevatten de besturingssysteem schijf, de cache schijf van de proces server en het Bewaar station voor failback.
Beschik bare schijf ruimte | 600 GB aan ruimte voor de cache van de proces server.
Beschik bare schijf ruimte | 600 GB aan ruimte voor het Bewaar station.
Besturingssysteem  | Windows Server 2012 R2 of Windows Server 2016 met bureaublad ervaring <br/><br> Als u van plan bent het ingebouwde hoofd doel van dit apparaat te gebruiken voor failback, moet u ervoor zorgen dat de versie van het besturings systeem gelijk is aan of hoger is dan de gerepliceerde items.|
Landinstelling van het besturingssysteem | Engels (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Niet nodig voor de configuratie Server versie [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) of hoger.
Windows Server-functies | Schakel Active Directory Domain Services niet in. Internet Information Services (IIS) of Hyper-V.
Groeps beleid| -Toegang tot de opdracht prompt voor komen. <br/> -Toegang tot register bewerkings Programma's verhinderen. <br/> -Logica vertrouwen voor bestands bijlagen. <br/> -Schakel uitvoering van script in. <br/> - [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Zorg ervoor dat:<br/><br/> -Geen vooraf bestaande standaard website hebben <br/> - [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br/> - [Fastcgi](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -instelling inschakelen  <br/> -Geen vooraf bestaande website/app Luis teren op poort 443<br/>
NIC-type | VMXNET3 (wanneer geïmplementeerd als een VMware-VM)
Type IP-adres | Statisch
Poorten | 443 gebruikt voor het beheren van de kanaal indeling<br/>9443 voor gegevens transport

## <a name="replicated-machines"></a>Gerepliceerde machines

Site Recovery ondersteunt replicatie van elke werk belasting die wordt uitgevoerd op een ondersteunde computer.

> [!Note]
> De volgende tabel geeft een overzicht van de ondersteuning voor computers met BIOS-opstart. Raadpleeg de sectie [opslag](#storage) voor ondersteuning op UEFI-machines.

**Component** | **Details**
--- | ---
Computer instellingen | Machines die naar Azure repliceren, moeten voldoen aan de [vereisten van Azure](#azure-vm-requirements).
Machine workload | Site Recovery ondersteunt replicatie van elke werk belasting die wordt uitgevoerd op een ondersteunde computer. [Meer informatie](https://aka.ms/asr_workload).
Computer naam | Zorg ervoor dat de weergave naam van de computer niet in door [Azure gereserveerde resource namen](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name) valt<br/><br/> Namen van logische volumes zijn niet hoofdletter gevoelig. Zorg ervoor dat er geen twee volumes op een apparaat dezelfde naam hebben. Bijvoorbeeld: volumes met de naam ' voLUME1 ', ' voLUME1 ' kunnen niet worden beveiligd via Azure Site Recovery.
Windows Server 2019 | Ondersteund door [Update pakket 34](https://support.microsoft.com/help/4490016) (versie 9,22 van de Mobility-service).
Windows Server 2016 64-bits | Ondersteund voor Server Core, server met bureaublad ervaring.
Windows Server 2012 R2/Windows Server 2012 | Ondersteund.
Windows Server 2008 R2 met SP1 en hoger. | Ondersteund.<br/><br/> Van versie [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility Service-agent hebt u de [onderhouds stack update (Ssu)](https://support.microsoft.com/help/4490628) en de [SHA-2-update](https://support.microsoft.com/help/4474419) geïnstalleerd op computers met Windows 2008 R2 met SP1 of hoger. SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://aka.ms/SHA-2KB).
Windows Server 2008 met SP2 of hoger (64-bits/32-bits) |  Alleen ondersteund voor migratie. [Meer informatie](migrate-tutorial-windows-server-2008.md).<br/><br/> Van versie [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility Service-agent hebt u de [onderhouds stack update (Ssu)](https://support.microsoft.com/help/4493730) en de [SHA-2-update](https://support.microsoft.com/help/4474419) geïnstalleerd op computers met Windows 2008 SP2. ISHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Windows 10, Windows 8.1, Windows 8 | Ondersteund.
Windows 7 met SP1 64-bits | Ondersteund door [Update pakket 36](https://support.microsoft.com/help/4503156) (versie 9,22 van de Mobility-service). </br></br> Van [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility Service-agent hebt u de [onderhouds stack update (Ssu)](https://support.microsoft.com/help/4490628) en de [SHA-2-update](https://support.microsoft.com/help/4474419) geïnstalleerd op Windows 7 SP1-computers.  SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Linux | Alleen 64-bits systeem wordt ondersteund. 32-bits systeem wordt niet ondersteund.<br/><br/>Op elke Linux-server moeten [Lis-onderdelen (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) zijn geïnstalleerd. Het is vereist om de server op te starten in azure na een testfailover of failover. Als ingebouwde LIS-onderdelen ontbreken, moet u ervoor zorgen dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat u replicatie inschakelt om de computers op te starten in Azure. <br/><br/> Site Recovery organiseert de failover voor het uitvoeren van Linux-servers in Azure. Linux-leveranciers kunnen echter wel de ondersteuning beperken tot distributie versies die geen einde van de levens duur hebben bereikt.<br/><br/> In Linux-distributies worden alleen de voorraad kernels ondersteund die deel uitmaken van de distributie secundaire versie/update.<br/><br/> Het bijwerken van beveiligde machines in grote Linux-distributie versies wordt niet ondersteund. Als u een upgrade wilt uitvoeren, schakelt u replicatie uit, voert u een upgrade van het besturings systeem uit en schakelt u de replicatie opnieuw in<br/><br/> Meer [informatie](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) over ondersteuning voor Linux en open-source technologie in Azure.
Linux Red Hat Enter prise | 5,2 tot 5,11</b><br/> 6,1 tot 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1 <br/> Enkele oudere kernels op servers met Red Hat Enterprise Linux 5.2-5,11 & 6.1-6.10 hebben geen [Lis-onderdelen (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) vooraf geïnstalleerd. Als ingebouwde LIS-onderdelen ontbreken, moet u ervoor zorgen dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat u replicatie inschakelt om de computers op te starten in Azure.
Linux: CentOS | 5,2 tot 5,11</b><br/> 6,1 tot 6,10</b><br/> 7,0 tot 7,6<br/> <br/> 8,0 tot 8,1<br/><br/> Enkele oudere kernels op servers met CentOS 5.2-5,11 & 6.1-6.10 hebben geen [Lis-onderdelen (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) vooraf geïnstalleerd. Als ingebouwde LIS-onderdelen ontbreken, moet u ervoor zorgen dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat u replicatie inschakelt om de computers op te starten in Azure.
Ubuntu | Ubuntu 14,04 LTS-server [(ondersteunde kernel-versies controleren)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16,04 LTS-server [(ondersteunde kernel-versies controleren)](#ubuntu-kernel-versions) </br> Ubuntu 18,04 LTS-server [(ondersteunde kernel-versies controleren)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(ondersteunde kernel-versies controleren)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(ondersteunde kernel-versies controleren)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(ondersteunde kernel-versies controleren)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Het upgraden van gerepliceerde machines van SUSE Linux Enterprise Server 11 SP3 naar SP4 wordt niet ondersteund. Als u een upgrade wilt uitvoeren, schakelt u replicatie uit en schakelt u na de upgrade opnieuw in.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Met de Red Hat compatibele kernel of een onherstelbare versie van de Enter prise kernel van 3, 4 & 5 (UEK3, UEK4, UEK5)

> [!Note]
> Voor elk van de Windows-versies biedt Azure Site Recovery alleen ondersteuning voor [LTSC-builds (Long-term Servicing Channel)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) .  [Semi-Annual-kanaal](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) releases worden op dit moment niet ondersteund.

### <a name="ubuntu-kernel-versions"></a>Ubuntu-kernel-versies

**Ondersteunde versie** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
14,04 LTS | [9,32][9.32 UR] | 3.13.0-24-generic naar 3.13.0-170-generic,<br/>3.16.0-25-generic naar 3.16.0-77-generic,<br/>3.19.0-18-generic naar 3.19.0-80-generic,<br/>4.2.0-18-generic naar 4.2.0-42-generic,<br/>4.4.0-21-generic naar 4.4.0-148-generic,<br/>4.15.0-1023-Azure naar 4.15.0-1045-Azure |
14,04 LTS | [9,31][9.31 UR] | 3.13.0-24-generic naar 3.13.0-170-generic,<br/>3.16.0-25-generic naar 3.16.0-77-generic,<br/>3.19.0-18-generic naar 3.19.0-80-generic,<br/>4.2.0-18-generic naar 4.2.0-42-generic,<br/>4.4.0-21-generic naar 4.4.0-148-generic,<br/>4.15.0-1023-Azure naar 4.15.0-1045-Azure |
14,04 LTS | [9,30][9.30 UR] | 3.13.0-24-generic naar 3.13.0-170-generic,<br/>3.16.0-25-generic naar 3.16.0-77-generic,<br/>3.19.0-18-generic naar 3.19.0-80-generic,<br/>4.2.0-18-generic naar 4.2.0-42-generic,<br/>4.4.0-21-generic naar 4.4.0-148-generic,<br/>4.15.0-1023-Azure naar 4.15.0-1045-Azure |
14,04 LTS | [9,29][9.29 UR]| 3.13.0-24-generic naar 3.13.0-170-generic,<br/>3.16.0-25-generic naar 3.16.0-77-generic,<br/>3.19.0-18-generic naar 3.19.0-80-generic,<br/>4.2.0-18-generic naar 4.2.0-42-generic,<br/>4.4.0-21-generic naar 4.4.0-148-generic,<br/>4.15.0-1023-Azure naar 4.15.0-1045-Azure |
|||
16,04 LTS | [9,32][9.32 UR] | 4.4.0-21-algemeen naar 4.4.0-171-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-74-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1.066-Azure|
16,04 LTS | [9,31][9.31 UR] | 4.4.0-21-generic naar 4.4.0-170-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-72-algemeen<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1063-Azure|
16,04 LTS | [9,30][9.30 UR] | 4.4.0-21-generic naar 4.4.0-166-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-66-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1061-Azure|
16,04 LTS | [9,29][9.29 UR] | 4.4.0-21-generic naar 4.4.0-164-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-64-algemeen<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1059-Azure|
|||
18,04 LTS | [9,32][9.32 UR]| 4.15.0-20-algemeen naar 4.15.0-74-algemeen </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-algemeen naar 5.0.0-37-generic </br> 5.3.0-19-generic tot 5.3.0-24-algemeen </br> 4.15.0-1009-Azure naar 4.15.0-1037-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1028-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1009-Azure|
18,04 LTS | [9,31][9.31 UR]| 4.15.0-20-algemeen naar 4.15.0-72-algemeen </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-algemeen naar 5.0.0-37-generic </br> 5.3.0-19-generic tot 5.3.0-24-algemeen </br> 4.15.0-1009-Azure naar 4.15.0-1037-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1025-Azure </br> 5.3.0-1007-Azure|
18,04 LTS | [9,30][9.30 UR] | 4.15.0-20-algemeen naar 4.15.0-66-algemeen </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-algemeen naar 5.0.0-32-algemeen </br> 4.15.0-1009-Azure naar 4.15.0-1037-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1023-Azure|
18,04 LTS | [9,29][9.29 UR] | 4.15.0-20-algemeen naar 4.15.0-62-algemeen </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-algemeen naar 5.0.0-27-generic </br> 4.15.0-1009-Azure naar 4.15.0-1037-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1018-Azure|

### <a name="debian-kernel-versions"></a>Debian-kernel-versies


**Ondersteunde versie** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
Debian 7 | [9,29][9.29 UR], [9,30][9.30 UR], [9,31][9.31 UR], [9,32][9.32 UR]| 3.2.0-4-amd64 tot 3.2.0-6-amd64, 3.16.0 -0. bpo. 4-amd64 |
|||
Debian 8 | [9,30][9.30 UR], [9,31][9.31 UR], [9,32][9.32 UR] | 3.16.0-4-amd64 tot 3.16.0-10-amd64, 4.9.0 -0. bpo. 4-amd64 tot 4.9.0 -0. bpo. 11-amd64 |
Debian 8 | [9,29][9.29 UR] | 3.16.0-4-amd64 tot 3.16.0-10-amd64, 4.9.0 -0. bpo. 4-amd64 tot 4.9.0 -0. bpo. 9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 ondersteunde kernel-versies

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,28][9.28 UR] | SP1 3.12.49-11: standaard ingesteld op 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-standaard ingesteld op 3.12.74-60.64.118-standaard</br></br> SP2 4.4.21-69-standaard ingesteld op 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-standaard voor 4.4.121-92.117-default</br></br>SP3 4.4.73-5-standaard ingesteld op 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-Azure-naar-4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-standaard ingesteld op 4.12.14-95.29-default</br>SP4 4.12.14-6.3-Azure naar 4.12.14-6.23-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,27][9.27 UR] | SP1 3.12.49-11: standaard ingesteld op 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-standaard ingesteld op 3.12.74-60.64.115-standaard</br></br> SP2 4.4.21-69-standaard ingesteld op 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-standaard voor 4.4.121-92.114-default</br></br>SP3 4.4.73-5-standaard ingesteld op 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-Azure-naar-4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-standaard ingesteld op 4.12.14-95.19-default</br>SP4 4.12.14-6.3-Azure naar 4.12.14-6.15-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,26][9.26 UR] | SP1 3.12.49-11: standaard ingesteld op 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-standaard ingesteld op 3.12.74-60.64.110-standaard</br></br> SP2 4.4.21-69-standaard ingesteld op 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-standaard voor 4.4.121-92.109-default</br></br>SP3 4.4.73-5-standaard ingesteld op 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-Azure naar 4.4.178-4.28-Azure</br></br>SP4 4.12.14-94.41-standaard ingesteld op 4.12.14-95.16-default</br>SP4 4.12.14-6.3-Azure naar 4.12.14-6,9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,25][9.25 UR] | SP1 3.12.49-11: standaard ingesteld op 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-standaard ingesteld op 3.12.74-60.64.107-standaard</br></br> SP2 4.4.21-69-standaard ingesteld op 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-standaard voor 4.4.121-92.104-default</br></br>SP3 4.4.73-5-standaard ingesteld op 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-Azure naar 4.4.176-4,25-Azure</br></br>SP4 4.12.14-94.41-standaard ingesteld op 4.12.14-95.13-default</br>SP4 4.12.14-6.3-Azure naar 4.12.14-6,9-Azure |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 ondersteunde kernel-versies

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 en 15 SP1 | [9,32](https://support.microsoft.com/help/4550047/) | Standaard worden alle [Stock-SuSE 15-en 15-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund. </br></br> 4.12.14-5,5-Azure naar 4.12.14-8.22-Azure



## <a name="linux-file-systemsguest-storage"></a>Linux-bestands systemen/gast opslag

**Component** | **Geboden**
--- | ---
Bestandssystemen | ext3, ext4, XFS, BTRFS (voor waarden die van toepassing zijn volgens deze tabel)
Inrichting van Logical Volume Management (LVM)| Brede inrichting-Ja <br></br> Thin Provisioning-no
Volume manager | -LVM wordt ondersteund.<br/> -/boot op LVM wordt ondersteund door [Update pakket 31](https://support.microsoft.com/help/4478871/) (versie 9,20 van de Mobility-service). Het wordt niet ondersteund in eerdere versies van de Mobility-service.<br/> -Meerdere besturingssysteem schijven worden niet ondersteund.
Geparavirtualiseerde-opslag apparaten | Apparaten die zijn geëxporteerd door geparavirtualiseerde stuurprogramma's worden niet ondersteund.
Meerdere wacht rijen voor blok-i/o-apparaten | Niet ondersteund.
Fysieke servers met de HP CCISS-opslag controller | Niet ondersteund.
Naamgevings regels voor apparaten en koppel punten | De naam van het apparaat of het koppel punt moet uniek zijn.<br/> Zorg ervoor dat er geen twee apparaten/koppel punten bestaan uit hoofdletter gevoelige namen. Het benoemen van apparaten voor dezelfde VM als *device1* en *device1* wordt niet ondersteund.
Mappen | Als u een versie van de Mobility-service hebt die ouder is dan versie 9,20 (uitgebracht in [Update pakket 31](https://support.microsoft.com/help/4478871/)), gelden de volgende beperkingen:<br/><br/> -Deze directory's (indien ingesteld als afzonderlijke partities/bestands systemen) moeten zich op dezelfde besturingssysteem schijf op de bron server bevindt:/(root),/boot,/usr,/usr/local,/var,/etc.</br> -De/boot-map moet zich op een schijf partitie benemen en geen LVM-volume zijn.<br/><br/> Vanaf versie 9,20 worden deze beperkingen niet toegepast. 
Opstartmap | -Opstart schijven mag zich in GPT-partitie-indeling. Dit is een beperking van Azure-architectuur. GPT-schijven worden ondersteund als gegevens schijven.<br/><br/> Meerdere opstart schijven op een virtuele machine worden niet ondersteund<br/><br/> -/boot op een LVM-volume op meer dan één schijf wordt niet ondersteund.<br/> -Een machine zonder een opstart schijf kan niet worden gerepliceerd.
Vereisten voor beschik bare ruimte| 2 GB op de/root-partitie <br/><br/> 250 MB in de installatiemap
XFSv5 | XFSv5-functies op XFS-bestands systemen, zoals controlesom van de meta gegevens, worden ondersteund (de Mobility Service-versie 9,10 en hoger).<br/> Gebruik het hulp programma xfs_info om de XFS-superblok kering voor de partitie te controleren. Als `ftype` is ingesteld op 1, worden XFSv5-functies in gebruik.
BTRFS | BTRFS wordt ondersteund door [Update pakket 34](https://support.microsoft.com/help/4490016) (versie 9,22 van de Mobility-service). BTRFS wordt niet ondersteund als:<br/><br/> -Het subvolume van het BTRFS-bestands systeem wordt gewijzigd nadat de beveiliging is ingeschakeld.</br> -Het BTRFS-bestands systeem is verdeeld over meerdere schijven.</br> -Het BTRFS-bestands systeem ondersteunt RAID.

## <a name="vmdisk-management"></a>VM/schijf beheer

**Actie** | **Details**
--- | ---
Grootte van schijf op een gerepliceerde VM wijzigen | Wordt op de bron-VM vóór de failover direct in de VM-eigenschappen ondersteund. U hoeft replicatie niet uit te scha kelen of opnieuw in te scha kelen.<br/><br/> Als u de bron-VM na een failover wijzigt, worden de wijzigingen niet vastgelegd.<br/><br/> Als u na een failover de schijf grootte op de Azure-VM wijzigt, maakt Site Recovery een nieuwe virtuele machine met de updates.
Schijf toevoegen op een gerepliceerde VM | Niet ondersteund.<br/> Schakel de replicatie voor de virtuele machine uit, voeg de schijf toe en schakel de replicatie opnieuw in.

## <a name="network"></a>Netwerk

**Component** | **Geboden**
--- | ---
NIC-koppeling host netwerk | Ondersteund voor VMware-Vm's. <br/><br/>Niet ondersteund voor replicatie van de fysieke machine.
VLAN host netwerk | Ja.
Host-netwerk IPv4 | Ja.
Host netwerk IPv6 | Nee.
NIC-koppeling voor gast/Server netwerk | Nee.
Gast/Server netwerk IPv4 | Ja.
Gast/Server netwerk IPv6 | Nee.
Statisch IP-adres van gast/Server netwerk (Windows) | Ja.
Statisch IP-adres van gast/Server netwerk (Linux) | Ja. <br/><br/>Vm's zijn geconfigureerd voor het gebruik van DHCP bij failback.
Gast/Server netwerk meerdere Nic's | Ja.


## <a name="azure-vm-network-after-failover"></a>Azure VM-netwerk (na failover)

**Component** | **Geboden**
--- | ---
Azure ExpressRoute | Ja
ILB | Ja
ELB | Ja
Azure Traffic Manager | Ja
Multi-NIC | Ja
Gereserveerd IP adres | Ja
IPv4 | Ja
Bron-IP-adres behouden | Ja
Service-eindpunten voor een virtueel Azure-netwerk<br/> | Ja
Versneld netwerken | Nee

## <a name="storage"></a>Storage
**Component** | **Geboden**
--- | ---
Dynamische schijf | De besturingssysteem schijf moet een standaard schijf zijn. <br/><br/>Gegevens schijven kunnen dynamische schijven zijn
Configuratie van docker-schijf | Nee
Host-NFS | Ja voor VMware<br/><br/> Nee voor fysieke servers
SAN van host (iSCSI/FC) | Ja
VSAN hosten | Ja voor VMware<br/><br/> N.v.t. voor fysieke servers
Multipath (MPIO) hosten | Ja, getest met micro soft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM voor CLARiiON
Virtuele volumes hosten (VVols) | Ja voor VMware<br/><br/> N.v.t. voor fysieke servers
VMDK van gast/server | Ja
Gedeelde gast/server-cluster schijf | Nee
Door gast/server versleutelde schijf | Nee
Gast/server-NFS | Nee
ISCSI voor gast/server | Voor migratie-Ja<br/>Voor herstel na nood gevallen-Nee, iSCSI zal failback als een gekoppelde schijf aan de VM
Het SMB 3,0 van de gast/server | Nee
RDM/server | Ja<br/><br/> N.v.t. voor fysieke servers
Gast/server schijf > 1 TB | Ja, schijf moet groter zijn dan 1024 MB<br/><br/>Maxi maal 8.192 GB bij het repliceren naar Managed disks (9,26-versie en hoger)<br></br> Maxi maal 4.095 GB bij het repliceren naar opslag accounts
Gast/server-schijf met 4 KB logische en 4.000 fysieke sector grootte | Nee
Gast/server schijf met 4 KB logische en 512-bytes fysieke sector grootte | Nee
Volume van gast/server met gestripte schijf >4 TB | Ja
Beheer van logische volumes (LVM)| Dik inrichten-Ja <br></br> Thin Provisioning-Nee
Gast/Server-opslag ruimten | Nee
Hot-of-Remove-schijf voor gast/server | Nee
Gast/server-schijf uitsluiten | Ja
Meerdere paden gast/server (MPIO) | Nee
GPT/server-GUID-partities | Er worden vijf partities ondersteund van [Update pakket 37](https://support.microsoft.com/help/4508614/) (versie 9,25 van de Mobility-service). Eerder vier werden ondersteund.
ReFS | Flexibel bestands systeem wordt ondersteund met Mobility Service versie 9,23 of hoger
EFI/UEFI-opstart procedure voor gast/server | -Ondersteund voor Windows Server 2012 of hoger, SLES 12 SP4 en RHEL 8,0 met mobiliteits agent versie 9,30<br/> -Secure UEFI-opstart type wordt niet ondersteund.

## <a name="replication-channels"></a>Replicatie kanalen

|**Type replicatie**   |**Geboden**  |
|---------|---------|
|Offloaded data transfers (ODX)    |       Nee  |
|Offline seeding        |   Nee      |
| Azure Data Box | Nee

## <a name="azure-storage"></a>Azure Storage

**Component** | **Geboden**
--- | ---
Lokaal redundante opslag | Ja
Geografisch redundante opslag | Ja
Geografisch redundante opslag met leestoegang | Ja
Cool Storage | Nee
Hot Storage| Nee
Blok-blobs | Nee
Versleuteling-at-rest (SSE)| Ja
Versleuteling-at-rest (CMK)| Ja (via Power shell AZ 3.3.0 module)
Premium Storage | Ja
Import/export-service | Nee
Firewalls voor VNets Azure Storage | Ja.<br/> Geconfigureerd op het doel opslag/cache-opslag account (wordt gebruikt voor het opslaan van replicatie gegevens).
V2-opslag accounts voor algemeen gebruik (warme en coole lagen) | Ja (de transactie kosten zijn aanzienlijk hoger voor v2 vergeleken met v1)

## <a name="azure-compute"></a>Azure compute

**Onderdeel** | **Geboden**
--- | ---
Beschikbaarheidssets | Ja
Beschikbaarheidszones | Nee
HUB | Ja
Managed Disks | Ja

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

On-premises Vm's die naar Azure worden gerepliceerd, moeten voldoen aan de vereisten van de Azure-VM die in deze tabel worden samenvatten. Wanneer Site Recovery een controle op vereisten uitvoert voor replicatie, mislukt de controle als aan sommige vereisten niet wordt voldaan.

**Component** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Controleer de [ondersteunde besturings systemen](#replicated-machines) voor gerepliceerde machines. | De controle is mislukt als dit niet wordt ondersteund.
Architectuur van gast besturingssysteem | 64-bits. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de besturingssysteemschijf | Maxi maal 2.048 GB. | De controle is mislukt als dit niet wordt ondersteund.
Aantal besturingssysteemschijven | 1 | De controle is mislukt als dit niet wordt ondersteund.
Aantal gegevensschijven | 64 of minder. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de gegevens schijf | Maxi maal 8.192 GB bij het repliceren naar Managed disk (9,26-versie)<br></br>Maxi maal 4.095 GB bij het repliceren naar het opslag account| De controle is mislukt als dit niet wordt ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
FC-schijf | Niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
BitLocker | Niet ondersteund. | BitLocker moet worden uitgeschakeld voordat u replicatie voor een machine inschakelt. |
VM-naam | Van 1 tot 63 tekens.<br/><br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de computer moet beginnen en eindigen met een letter of cijfer. |  Werk de waarde in de computer eigenschappen in Site Recovery bij.

## <a name="resource-group-limits"></a>Limieten voor resource groep

Raadpleeg het artikel over de [limieten en quota voor abonnementen](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)voor meer informatie over het aantal virtuele machines dat kan worden beveiligd onder één resource groep.

## <a name="churn-limits"></a>Verloop limieten

De volgende tabel bevat de Azure Site Recovery-limieten.
- Deze limieten zijn gebaseerd op onze tests, maar dekken niet alle mogelijke app-I/O-combi Naties.
- De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie.
- Voor de beste resultaten wordt u ten zeerste aangeraden het [Deployment planner-hulp programma](site-recovery-deployment-planner.md)uit te voeren en uitgebreide toepassings tests uit te voeren met testfailover om de ware prestatie afbeelding voor uw app op te halen.

**Replicatie doel** | **Gemiddelde I/O-grootte van bronschijf** |**Gemiddeld gegevens verloop van bron schijf** | **Totale gegevens verloop van bron schijf per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf


**brongegevensverloop** | **Maximumaantal**
---|---
Piekgegevensverloop over alle schijven op een VM | 54 MB/s
Maximumgegevensverloop per dag dat wordt ondersteund door een processerver | 2 TB

- Dit zijn gemiddelden uitgaande van een I/O-overlapping van 30%.
- Site Recovery kan een hogere doorvoer verwerken op basis van overlappingsverhouding, grotere schrijfgrootten en daadwerkelijk workload-I/O-gedrag.
- Deze nummers nemen een typische achterstand van ongeveer vijf minuten. Dat wil zeggen dat de gegevens na het uploaden binnen vijf minuten worden verwerkt en er een herstelpunt is gemaakt.

## <a name="vault-tasks"></a>Kluis taken

**Actie** | **Geboden**
--- | ---
De kluis verplaatsen tussen resource groepen | Nee
De kluis verplaatsen binnen en tussen abonnementen | Nee
Opslag, netwerk, Azure-Vm's verplaatsen tussen resource groepen | Nee
Verplaats opslag-, netwerk-, Azure-Vm's binnen en tussen abonnementen. | Nee


## <a name="obtain-latest-components"></a>Nieuwste onderdelen ophalen

**Naam** | **Beschrijving** | **Details**
--- | --- | ---
Configuratieserver | On-premises geïnstalleerd.<br/> Coördineert de communicatie tussen on-premises VMware-servers of fysieke machines en Azure. | - [Meer informatie over](vmware-physical-azure-config-process-server-overview.md) de configuratie server.<br/> - [Meer informatie over](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) het upgraden naar de nieuwste versie.<br/> - [Meer informatie over](vmware-azure-deploy-configuration-server.md) het instellen van de configuratie server.
Processerver | standaard geïnstalleerd op de configuratieserver.<br/> Hiermee ontvangt u replicatie gegevens, optimaliseert u deze met caching, compressie en versleuteling, en verzendt u deze naar Azure.<br/> Naarmate uw implementatie groeit, kunt u extra proces servers toevoegen om grotere volumes van replicatie verkeer af te handelen. | - [Meer informatie over](vmware-physical-azure-config-process-server-overview.md) de proces server.<br/> - [Meer informatie over](vmware-azure-manage-process-server.md#upgrade-a-process-server) het upgraden naar de nieuwste versie.<br/> - [Meer informatie over](vmware-physical-large-deployment.md#set-up-a-process-server) het instellen van scale-out proces servers.
Mobility-service | Geïnstalleerd op virtuele VMware-machines of fysieke servers die u wilt repliceren.<br/> Coördineert de replicatie tussen on-premises VMware-servers/fysieke servers en Azure.| - [Meer informatie over](vmware-physical-mobility-service-overview.md) de Mobility-service.<br/> - [Meer informatie over](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) het upgraden naar de nieuwste versie.<br/>



## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het](tutorial-prepare-azure.md) voorbereiden van Azure voor herstel na nood gevallen van virtuele VMware-machines.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
