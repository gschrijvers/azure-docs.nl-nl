---
title: Veelgestelde vragen over Azure NetApp Files | Microsoft Docs
description: Antwoorden op veelgestelde vragen over Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: b-juche
ms.openlocfilehash: a8c299a6f0e6732d50b40fc29bde07179fc2c412
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185639"
---
# <a name="faqs-about-azure-netapp-files"></a>Veelgestelde vragen over Azure NetApp Files

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure NetApp Files. 

## <a name="networking-faqs"></a>Veelgestelde vragen over netwerken

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Gaat het NFS-gegevenspad over via Internet?  

Nee. Het pad van de NFS-gegevens gaat niet via internet. Azure NetApp Files is een systeem eigen Azure-service die is geïmplementeerd in de Azure-Virtual Network (VNet) waar de service beschikbaar is. Azure NetApp Files maakt gebruik van een overgedragen subnet en richt een netwerk interface rechtstreeks in op het VNet. 

Zie de [richt lijnen voor het plannen van Azure NetApp files-netwerken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) voor meer informatie.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kan ik verbinding maken met een VNet dat ik al in de Azure NetApp Files-service heb gemaakt?

Ja, u kunt VNets verbinding maken die u hebt gemaakt met de service. 

Zie de [richt lijnen voor het plannen van Azure NetApp files-netwerken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) voor meer informatie.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kan ik een NFS-volume van Azure NetApp Files koppelen met behulp van DNS FQDN-naam?

Ja, u kunt, als u de vereiste DNS-vermeldingen maakt. Azure NetApp Files levert het service-IP-adres voor het ingerichte volume. 

> [!NOTE] 
> Azure NetApp Files kunt indien nodig extra IP-adressen voor de service implementeren.  DNS-vermeldingen moeten mogelijk periodiek worden bijgewerkt.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Kan ik mijn eigen IP-adres voor een Azure NetApp Files volume instellen of selecteren?  

Nee. De IP-toewijzing aan Azure NetApp Files volumes is dynamisch. Toewijzing van statisch IP-adres wordt niet ondersteund. 
 
## <a name="security-faqs"></a>Veelgestelde vragen over beveiliging

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kan het netwerk verkeer tussen de virtuele machine van Azure en de opslag worden versleuteld?

Gegevens verkeer (verkeer van de NFSv3-, NFSv 4.1-of SMBv3-client naar Azure NetApp Files volumes) is niet versleuteld. Het verkeer van een Azure-VM (waarop een NFS-of SMB-client wordt uitgevoerd) moet echter worden Azure NetApp Files net zo veilig zijn als andere verkeer van Azure-VM-naar-VM. Dit verkeer is lokaal voor het Azure Data Center-netwerk. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kan de opslag in rust worden versleuteld?

Alle Azure NetApp Files volumes worden versleuteld met behulp van de FIPS 140-2-standaard. Alle sleutels worden beheerd door de Azure NetApp Files-service. 

### <a name="how-are-encryption-keys-managed"></a>Hoe worden versleutelings sleutels beheerd? 

Sleutel beheer voor Azure NetApp Files wordt verwerkt door de service. Voor elk volume wordt een unieke XTS-AES-256-gegevens versleutelings sleutel gegenereerd. Een versleutelings sleutel hiërarchie wordt gebruikt om alle volume sleutels te versleutelen en te beveiligen. Deze versleutelings sleutels worden nooit weer gegeven of gerapporteerd in een niet-versleutelde indeling. Versleutelings sleutels worden direct verwijderd wanneer een volume wordt verwijderd.

Ondersteuning voor door gebruikers beheerde sleutels (neem uw eigen sleutels mee) met behulp van de speciale HSM van Azure is beschikbaar op basis van een gecontroleerde toepassing in de regio's VS-Oost, VS-West2 en VS Zuid-Centraal.  U kunt toegang vragen op **anffeedback@microsoft.com**. Als capaciteit beschikbaar is, worden aanvragen goedgekeurd.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kan ik de NFS-export beleids regels configureren om de toegang tot het Azure NetApp Files service-koppelings doel te beheren?


Ja, u kunt Maxi maal vijf regels in één NFS-export beleid configureren.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Ondersteunt Azure NetApp Files netwerk beveiligings groepen?

Nee, op dit moment kunt u geen netwerk beveiligings groepen Toep assen op het gedelegeerde subnet van Azure NetApp Files of de netwerk interfaces die zijn gemaakt door de service.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Kan ik Azure IAM gebruiken met Azure NetApp Files?

Ja, Azure NetApp Files ondersteunt RBAC-functies met Azure IAM.

## <a name="performance-faqs"></a>Veelgestelde vragen over prestaties

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Wat moet ik doen om Azure NetApp Files prestaties te optimaliseren of af te stemmen?

U kunt de volgende acties uitvoeren op basis van de prestatie vereisten: 
- Controleer of de grootte van de virtuele machine juist is.
- Versneld netwerken inschakelen voor de virtuele machine.
- Selecteer het gewenste service niveau en de grootte voor de capaciteits groep.
- Maak een volume met de gewenste quotum grootte voor de capaciteit en prestaties.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hoe kan ik op doorvoer gebaseerde service niveaus van Azure NetApp Files naar IOPS converteren?

U kunt MB/s converteren naar IOPS met behulp van de volgende formule:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hoe kan ik het service niveau van een volume wijzigen?

Het wijzigen van het service niveau van een volume wordt momenteel niet ondersteund.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp Files prestaties Hoe kan ik controleren?

Azure NetApp Files biedt meet waarden voor de volume prestaties. U kunt Azure Monitor ook gebruiken voor het bewaken van metrische gegevens over gebruik voor Azure NetApp Files.  Zie de [metrische gegevens voor Azure NetApp files](azure-netapp-files-metrics.md) voor de lijst met metrische gegevens over prestaties voor Azure NetApp files.

## <a name="nfs-faqs"></a>Veelgestelde vragen over NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Ik wil een volume automatisch koppelen wanneer een virtuele machine van Azure wordt gestart of opnieuw wordt opgestart.  Hoe kan ik mijn host configureren voor permanente NFS-volumes?

Als een NFS-volume automatisch moet worden gekoppeld bij het starten of opnieuw opstarten van de VM `/etc/fstab` , voegt u een vermelding toe aan het bestand op de host. 

Zie [een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) voor meer informatie.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Waarom wordt de ingerichte grootte van het volume niet weer gegeven met de DF-opdracht op de NFS-client?

De grootte van het volume dat in VG wordt gerapporteerd, is de maximale grootte van het Azure NetApp Files volume kan toenemen. De grootte van het Azure NetApp Files volume in de DF-opdracht komt niet overeen met het quotum of de grootte van het volume.  U kunt de Azure NetApp Files volume grootte of-quota verkrijgen via de Azure Portal of de API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Wat is de NFS-versie Azure NetApp Files ondersteunen?

Azure NetApp Files ondersteunt NFSv3 en NFSv 4.1. U kunt [een volume maken](azure-netapp-files-create-volumes.md) met behulp van de NFS-versie. 

### <a name="how-do-i-enable-root-squashing"></a>Hoe kan ik root Squashing inschakelen?

De hoofdmap Squashing wordt momenteel niet ondersteund.

## <a name="smb-faqs"></a>Veelgestelde vragen over SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Is een Active Directory verbinding vereist voor SMB-toegang? 

Ja, u moet een Active Directory verbinding maken voordat u een SMB-volume implementeert. De opgegeven domein controllers moeten toegankelijk zijn voor het gedelegeerde subnet van Azure NetApp Files voor een geslaagde verbinding.  Zie [een SMB-volume maken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) voor meer informatie. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hoeveel Active Directory verbindingen worden ondersteund?

Azure NetApp Files biedt geen ondersteuning voor meerdere Active Directory (AD)-verbindingen in één *regio*, zelfs als de AD-verbindingen zich in verschillende NetApp-accounts bevinden. U kunt echter meerdere AD-verbindingen hebben in één *abonnement*, zolang de AD-verbindingen zich in verschillende regio's bevinden. Als u meerdere AD-verbindingen in één regio nodig hebt, kunt u hiervoor afzonderlijke abonnementen gebruiken. 

Een AD-verbinding wordt geconfigureerd per NetApp-account; de AD-verbinding wordt alleen weer gegeven via het NetApp-account dat wordt gemaakt in.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Ondersteunt Azure NetApp Files Azure Active Directory? 

Zowel [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) en [Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) worden ondersteund. U kunt bestaande Active Directory domein controllers gebruiken met Azure NetApp Files. Domein controllers kunnen zich in azure bevinden als virtuele machines, of on-premises via ExpressRoute of S2S VPN. Azure NetApp Files biedt op dit moment geen ondersteuning voor AD-deelname voor [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) .

Als u Azure NetApp Files gebruikt met Azure Active Directory Domain Services, is `OU=AADDC Computers` het pad voor de organisatie-eenheid wanneer u Active Directory configureert voor uw NetApp-account.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Welke versies van Windows Server Active Directory worden ondersteund?

Azure NetApp Files ondersteunt Windows Server 2008r2SP1-2019-versies van Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Waarom wordt de ingerichte grootte niet weer gegeven in de beschik bare ruimte op mijn SMB-client?

De grootte van het volume dat door de SMB-client wordt gerapporteerd, is de maximale grootte waarmee het Azure NetApp Files volume kan worden uitgebreid. De grootte van het Azure NetApp Files volume, zoals weer gegeven op de SMB-client, komt niet overeen met het quotum of de grootte van het volume. U kunt de Azure NetApp Files volume grootte of-quota verkrijgen via de Azure Portal of de API.

<!--
### Does Azure NetApp Files support Kerberos encryption?

Yes, by default, Azure NetApp Files supports both AES-128 and AES-256 encryption for traffic between the service and the targeted Active Directory domain controllers. See [Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) for requirements. 
-->

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Veelgestelde vragen over capaciteits beheer

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hoe kan ik het gebruik van de capaciteits groep en het volume van Azure NetApp Files controleren? 

Azure NetApp Files biedt gegevens over de capaciteits pool en het volume gebruik. U kunt Azure Monitor ook gebruiken om het gebruik van Azure NetApp Files te bewaken. Zie de [metrische gegevens voor Azure NetApp files](azure-netapp-files-metrics.md) voor meer informatie. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kan ik Azure NetApp Files beheren via Azure Storage Explorer?

Nee. Azure NetApp Files wordt niet ondersteund door Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hoe kan ik bepalen of een map de limiet grootte nadert?

U kunt de `stat` opdracht van een client gebruiken om te zien of een map de maximum grootte bereikt (320 MB).

Voor een map van 320 MB is het aantal blokken 655360, waarbij elke blok grootte 512 bytes is.  (Dat wil zeggen 320x1024x1024/512.)  

Voorbeelden:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Veelgestelde vragen over gegevens migratie en-beveiliging

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hoe kan ik gegevens naar Azure NetApp Files migreren?
Azure NetApp Files biedt NFS-en SMB-volumes.  U kunt elk op bestanden gebaseerd Kopieer programma gebruiken om gegevens te migreren naar de service. 

NetApp biedt een op SaaS gebaseerde oplossing, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Met deze oplossing kunt u NFS-of SMB-gegevens repliceren naar Azure NetApp Files NFS-export of SMB-shares. 

U kunt ook een breed scala aan gratis hulpprogram ma's gebruiken om gegevens te kopiëren. Voor NFS kunt u hulpprogram ma's voor werk belastingen, zoals [rsync](https://rsync.samba.org/examples.html) , gebruiken om bron gegevens te kopiëren en te synchroniseren naar een Azure NetApp files volume. Voor SMB kunt u gebruikmaken van werk belastingen [op dezelfde](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) manier.  Deze hulpprogram ma's kunnen ook machtigingen voor bestanden of mappen repliceren. 

De vereisten voor gegevens migratie van on-premises naar Azure NetApp Files zijn als volgt: 

- Zorg ervoor dat Azure NetApp Files beschikbaar is in de Azure-doel regio.
- Valideer de netwerk verbinding tussen de bron en het IP-adres van het Azure NetApp Files doel volume. Gegevens overdracht tussen on-premises en de Azure NetApp Files-service wordt ondersteund via ExpressRoute.
- Maak het doel Azure NetApp Files volume.
- Zet de bron gegevens over naar het doel volume met behulp van het hulp programma voor het kopiëren van bestanden.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hoe kan ik een kopie maken van een Azure NetApp Files-volume in een andere Azure-regio?
    
Azure NetApp Files biedt NFS-en SMB-volumes.  Elk hulp programma voor het kopiëren van bestanden kan worden gebruikt om gegevens tussen Azure-regio's te repliceren. 

NetApp biedt een op SaaS gebaseerde oplossing, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Met deze oplossing kunt u NFS-of SMB-gegevens repliceren naar Azure NetApp Files NFS-export of SMB-shares. 

U kunt ook een breed scala aan gratis hulpprogram ma's gebruiken om gegevens te kopiëren. Voor NFS kunt u hulpprogram ma's voor werk belastingen, zoals [rsync](https://rsync.samba.org/examples.html) , gebruiken om bron gegevens te kopiëren en te synchroniseren naar een Azure NetApp files volume. Voor SMB kunt u gebruikmaken van werk belastingen [op dezelfde](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) manier.  Deze hulpprogram ma's kunnen ook machtigingen voor bestanden of mappen repliceren. 

De vereisten voor het repliceren van een Azure NetApp Files-volume naar een andere Azure-regio zijn als volgt: 
- Zorg ervoor dat Azure NetApp Files beschikbaar is in de Azure-doel regio.
- Valideer de netwerk verbinding tussen VNets in elke regio. Globale peering tussen VNets wordt momenteel niet ondersteund.  U kunt verbinding maken tussen VNets door te koppelen met een ExpressRoute-circuit of een S2S-VPN-verbinding te gebruiken. 
- Maak het doel Azure NetApp Files volume.
- Zet de bron gegevens over naar het doel volume met behulp van het hulp programma voor het kopiëren van bestanden.

### <a name="is-migration-with-azure-data-box-supported"></a>Wordt de migratie met Azure Data Box ondersteund?

Nee. Azure Data Box biedt momenteel geen ondersteuning voor Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Wordt migratie met Azure import/export-service ondersteund?

Nee. De Azure import/export-service biedt momenteel geen ondersteuning voor Azure NetApp Files.

## <a name="next-steps"></a>Volgende stappen  

- [Veelgestelde vragen over Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Veelgestelde vragen over Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files](azure-netapp-files-smb-performance.md)
