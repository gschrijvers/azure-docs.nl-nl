---
title: Een Linux-probleemoplossings-VM gebruiken met de Azure CLI | Microsoft Docs
description: Meer informatie over het oplossen van problemen met Linux-VM'S door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure CLI
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73620564"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Problemen met een virtuele Linux-machine oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met de Azure CLI
Als op de virtuele Linux-machine (VM) een opstart-of schijf fout optreedt, moet u mogelijk de stappen voor probleem oplossing uitvoeren op de virtuele harde schijf zelf. Een voor beeld hiervan is een ongeldige vermelding in `/etc/fstab` die verhindert dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u de Azure CLI gebruikt om de virtuele harde schijf te verbinden met een andere Linux-VM om eventuele fouten op te lossen en vervolgens de oorspronkelijke VM opnieuw te maken. 

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
Het probleemoplossingsproces is als volgt:

1. Stop de betrokken VM.
1. Maak een moment opname van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf.
1. Koppel en koppel de nieuwe besturingssysteem schijf aan een andere virtuele Linux-machine voor het oplossen van problemen.
1. Maak verbinding met de VM voor probleemoplossing. Bewerk bestanden of voer hulpprogram ma's uit om problemen op de nieuwe besturingssysteem schijf op te lossen.
1. Ontkoppel de nieuwe besturingssysteem schijf en ontkoppel deze van de virtuele machine voor probleem oplossing.
1. Wijzig de besturingssysteem schijf voor de betrokken VM.

Als u deze probleemoplossings stappen wilt uitvoeren, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

> [!Important]
> De scripts in dit artikel zijn alleen van toepassing op de virtuele machines die gebruikmaken van [beheerde schijven](../linux/managed-disks-overview.md). 

Vervang in de volgende voor beelden parameter namen door uw eigen waarden, zoals `myResourceGroup` en. `myVM`

## <a name="determine-boot-issues"></a>Opstart problemen vaststellen
Controleer de seriële uitvoer om te bepalen waarom de virtuele machine niet correct kan worden opgestart. Een veelvoorkomend voor beeld is een ongeldige vermelding `/etc/fstab`in, of de onderliggende virtuele harde schijf die wordt verwijderd of verplaatst.

De opstart logboeken ophalen met [AZ VM boot-Diagnostics Get-boot-log](/cli/azure/vm/boot-diagnostics). `myVM` In het volgende voor beeld wordt de seriële uitvoer opgehaald van de virtuele machine met de `myResourceGroup`naam in de resource groep met de naam:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Bekijk de seriële uitvoer om te bepalen waarom de virtuele machine niet kan worden opgestart. Als de seriële uitvoer geen indicatie geeft, moet u mogelijk logboek bestanden weer geven `/var/log` zodra u de virtuele harde schijf hebt verbonden met een VM voor probleem oplossing.

## <a name="stop-the-vm"></a>De virtuele machine stoppen

In het volgende voor beeld wordt de `myVM` VM gestopt met de naam `myResourceGroup`van de resource groep met de naam:

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Een moment opname maken van de besturingssysteem schijf van de betrokken VM

Een moment opname is een volledige, alleen-lezen kopie van een VHD. Deze kan niet worden gekoppeld aan een virtuele machine. In de volgende stap maakt u een schijf van deze moment opname. In het volgende voor beeld wordt een moment `mySnapshot` opname gemaakt met de naam van de besturingssysteem schijf van de virtuele machine met de naam ' myVM '. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de moment opname

Met dit script maakt u een beheerde schijf `myOSDisk` met een naam uit `mySnapshot`de moment opname met de naam.  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Als de resource groep en de bron momentopname zich niet in dezelfde regio bevinden, ontvangt u de fout melding ' resource is niet gevonden ' wanneer u `az disk create`uitvoert. In dit geval moet u opgeven `--location <region>` dat de schijf wordt gemaakt in dezelfde regio als de moment opname van de bron.

U hebt nu een kopie van de oorspronkelijke besturingssysteem schijf. U kunt deze nieuwe schijf koppelen aan een andere Windows-VM voor het oplossen van problemen.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>De nieuwe virtuele harde schijf koppelen aan een andere VM
Voor de volgende stappen gebruikt u een andere virtuele machine voor het oplossen van problemen. U koppelt de schijf aan deze virtuele machine voor probleem oplossing om de schijf inhoud te zoeken en te bewerken. Met dit proces kunt u eventuele configuratie fouten corrigeren of aanvullende toepassings-of systeem logboek bestanden bekijken.

Met dit script koppelt `myNewOSDisk` u de schijf `MyTroubleshootVM`aan de VM:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>De gekoppelde gegevens schijf koppelen

> [!NOTE]
> In de volgende voor beelden worden de stappen beschreven die nodig zijn voor een Ubuntu-VM. Als u een andere Linux-distributie gebruikt, zoals Red Hat Enterprise Linux of SUSE, zijn de locaties en `mount` opdrachten van het logboek bestand mogelijk iets anders. Raadpleeg de documentatie voor uw specifieke distributie voor de desbetreffende wijzigingen in de opdrachten.

1. SSH naar uw virtuele machine voor probleem oplossing met de juiste referenties. Als deze schijf de eerste gegevens schijf is die aan uw virtuele machine voor probleem oplossing is gekoppeld, is `/dev/sdc`de schijf waarschijnlijk verbonden met. Gebruiken `dmesg` om gekoppelde schijven weer te geven:

    ```bash
    dmesg | grep SCSI
    ```

    De uitvoer lijkt op die in het volgende voorbeeld:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    In het vorige voor beeld bevindt de besturingssysteem `/dev/sda` schijf zich op en de tijdelijke schijf die voor elke `/dev/sdb`VM is ingesteld, bevindt zich op. Als u meerdere gegevens schijven had, moeten ze zich op `/dev/sdd`, `/dev/sde`,,,, enzovoort.

2. Maak een directory voor het koppelen van uw bestaande virtuele harde schijf. In het volgende voor beeld wordt een `troubleshootingdisk`map gemaakt met de naam:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Als u meerdere partities op de bestaande virtuele harde schijf hebt, koppelt u de vereiste partitie. In het volgende voor beeld wordt de eerste primaire partitie `/dev/sdc1`gekoppeld aan:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Best Practice is het koppelen van gegevens schijven op virtuele machines in azure met behulp van de Universally Unique Identifier (UUID) van de virtuele harde schijf. Voor dit korte probleemoplossings scenario is het niet nodig om de virtuele harde schijf te koppelen met behulp van de UUID. Het bewerken `/etc/fstab` van virtuele harde schijven met de apparaatnaam in plaats van de UUID kan er echter toe leiden dat de VM niet kan worden opgestart onder normaal gebruik.


## <a name="fix-issues-on-the-new-os-disk"></a>Problemen op de nieuwe besturingssysteem schijf oplossen
Als de bestaande virtuele harde schijf is gekoppeld, kunt u nu eventuele onderhouds-en probleemoplossings stappen uitvoeren. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.


## <a name="unmount-and-detach-the-new-os-disk"></a>De nieuwe besturingssysteem schijf ontkoppelen en loskoppelen
Wanneer de fouten zijn opgelost, ontkoppelt u de bestaande virtuele harde schijf en koppelt u deze los van de VM voor probleem oplossing. U kunt de virtuele harde schijf niet met andere virtuele machines gebruiken totdat de lease die de virtuele harde schijf koppelt aan de VM voor probleem oplossing is vrijgegeven.

1. Ontkoppel de bestaande virtuele harde schijf van de SSH-sessie naar uw VM voor probleem oplossing. Wijzig eerst de bovenliggende map voor het koppel punt:

    ```bash
    cd /
    ```

    Ontkoppel nu de bestaande virtuele harde schijf. In het volgende voor beeld wordt het apparaat ontkoppeld op `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Ontkoppel nu de virtuele harde schijf van de VM. Sluit de SSH-sessie af op uw virtuele machine voor probleem oplossing:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>De besturingssysteem schijf voor de betrokken VM wijzigen

U kunt Azure CLI gebruiken om de besturingssysteem schijven te wisselen. U hoeft de virtuele machine niet te verwijderen en opnieuw te maken.

In dit voor beeld wordt de `myVM` virtuele machine met de naam gestopt en wordt de schijf toegewezen als de nieuwe besturingssysteem schijf. `myNewOSDisk`

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen
Zie [problemen met ssh-verbindingen met een Azure VM oplossen](troubleshoot-ssh-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Linux-VM](troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.

