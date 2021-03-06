---
title: Virtuele Azure-machines repliceren die worden uitgevoerd in proximity-plaatsings groepen
description: Meer informatie over het repliceren van virtuele Azure-machines die worden uitgevoerd in proximity placement groups met behulp van Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 204ac3be46ac7ba0e1ea96e50379ca417b1299ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847630"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Virtuele Azure-machines repliceren die worden uitgevoerd in proximity-plaatsings groepen naar een andere regio

In dit artikel wordt beschreven hoe u virtuele machines die worden uitgevoerd in een proximity-plaatsings groep, repliceert naar een secundaire regio.

[Proximity placement groups](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) is een logische groepering van virtuele machines van Azure, die u kunt gebruiken om de netwerk LATENTIE tussen vm's die aan uw toepassingen is gekoppeld, te verlagen. Wanneer de Vm's binnen dezelfde plaatsings groep worden geïmplementeerd, bevinden ze zich fysiek zo dicht mogelijk bij elkaar. Proximity-plaatsings groepen zijn bijzonder nuttig voor het oplossen van de vereisten van latentie gevoelige werk belastingen.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Herstel na nood geval met proximity-plaatsings groepen

In een typisch scenario is het mogelijk dat uw virtuele machines worden uitgevoerd in een proximity-plaatsings groep om te voor komen dat de netwerk latentie tussen de verschillende lagen van uw toepassing. Hoewel dit kan leiden tot een optimale netwerk latentie van uw toepassing, kunt u deze toepassingen beveiligen met Site Recovery voor een storing op regio niveau. Site Recovery repliceert de gegevens van de ene regio naar een andere Azure-regio en brengt de computers in een herstel regio voor nood gevallen in een gebeurtenis van de failover.

## <a name="considerations"></a>Overwegingen

- De beste poging is om de virtuele machines te failoveren/failback uit te stellen in een proximity-plaatsings groep. Als de virtuele machine tijdens de failover/failback echter niet in de nabijheid kan worden gebracht, worden er nog steeds failover/failback uitgevoerd en worden de virtuele machines gemaakt buiten een proximity-plaatsings groep.
-  Als een Beschikbaarheidsset is vastgemaakt aan een proximity-plaatsings groep en tijdens failover/failback-Vm's in de beschikbaarheidsset een toewijzings beperking hebben, worden de virtuele machines gemaakt buiten de beschikbaarheidsset en proximity-plaatsings groep.
-  Site Recovery voor proximity-plaatsings groepen wordt niet ondersteund voor niet-beheerde schijven.

## <a name="prerequisites"></a>Vereisten

1. Zorg ervoor dat u beschikt over de Azure PowerShell AZ-module. Als u Azure PowerShell moet installeren of upgraden, volgt u deze [hand leiding voor het installeren en configureren van Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Site Recovery instellen voor Virtual Machines in proximity-plaatsings groep

### <a name="azure-to-azure"></a>Azure naar Azure

1. [Meld](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription) u aan bij uw account en stel uw abonnement in.
2. Bekijk de details van de virtuele machine die u van plan bent te repliceren, zoals [hier](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated)wordt beschreven.
3. [Maak](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault) de Recovery Services-kluis en [Stel](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context) de kluis context in.
4. Bereid de kluis voor op het starten van de virtuele replicatie machine. Hiervoor moet u een [service Fabric-object](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) maken voor zowel primaire als herstel regio's.
5. [Maak](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric) een site Recovery-beveiligings container voor zowel de primaire als de herstel infrastructuur.
6. Een replicatie beleid [maken](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy) .
7. Maak een beveiligings container toewijzing tussen de primaire en herstel beveiligings container met behulp van [deze](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) stappen en een beveiligings container toewijzing voor failback, zoals [hier](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)wordt beschreven.
8. Maak een cache-opslag account door [de](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account) volgende stappen uit te voeren.
9. Maak de vereiste netwerk toewijzingen zoals [hier](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings)wordt vermeld.
10. Als u de virtuele machine van Azure wilt repliceren met Managed disks, gebruikt u de onderstaande Power shell-cmdlet: 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType -RecoveryProximityPlacementGroupId $recPpg.Id

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
Zodra de bewerking voor het starten van de replicatie is voltooid, worden de gegevens van de virtuele machine gerepliceerd naar de herstel regio.

Het replicatie proces wordt gestart door eerst een kopie van de replicerende schijven van de virtuele machine in het herstel gebied te seeden. Deze fase wordt de initiële replicatie fase genoemd.

Wanneer de initiële replicatie is voltooid, wordt de replicatie verplaatst naar de differentiële synchronisatie fase. Op dit moment wordt de virtuele machine beveiligd en kan er een testfailover worden uitgevoerd. De replicatie status van het gerepliceerde item dat de virtuele machine vertegenwoordigt, wordt naar de beveiligde status verplaatst nadat de initiële replicatie is voltooid.

Controleer de replicatie status en de replicatie status voor de virtuele machine door Details van het met replicatie beveiligde item corresponderende items op te halen. 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Als u een testfailover wilt uitvoeren, voert u [de volgende](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover) stappen uit om de testfailover te valideren en op te schonen.
12. Volg de stappen die [hier](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure)worden beschreven om failover uit te voeren.
13. Als u de bron regio opnieuw wilt beveiligen en failback, gebruikt u de onderstaande Power shell-cmdlet:

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. Volg [de stappen om](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication)replicatie uit te scha kelen.

### <a name="vmware-to-azure"></a>VMware naar Azure

1. Zorg ervoor dat u [de on-premises VMware-servers voorbereidt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises) op herstel na nood geval naar Azure.
2. Meld u aan bij uw account en stel uw abonnement in zoals [hier](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure)is opgegeven.
3. [Stel](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault) een Recovery Services kluis in en [Stel de kluis context](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context)in.
4. [Valideer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration) uw kluis registratie.
5. Een replicatie beleid [maken](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy) .
6. [Voeg](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms) een vCenter-Server toe en Detecteer virtuele machines en [Maak](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication) opslag accounts voor replicatie.
7. Als u VMware Virtual Machines wilt repliceren, controleert u de details hier en volgt u de onderstaande Power shell-cmdlet:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. U kunt de replicatie status en replicatie status van de virtuele machine controleren met de cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. Configureer de failover-instellingen door de stappen [hier](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings)te volgen.
10. [Voer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover) een testfailover uit. 
11. Failover naar Azure met behulp van [deze](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure) stappen.

### <a name="hyper-v-to-azure"></a>Hyper-V naar Azure

1. Zorg ervoor dat u [de on-premises Hyper-V-servers voorbereidt](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) op herstel na nood geval naar Azure.
2. [Meld](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account) u aan bij Azure.
3. [Stel](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault) uw kluis in en [Stel](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context) de Recovery Services kluis context in.
4. [Maak](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site) een Hyper-V-site.
5. [Installeer](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent) de provider en de agent.
6. Een replicatie beleid [maken](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy) .
7. Schakel replicatie in met behulp van de onderstaande stappen: 
    
    a. Haal als volgt het Beveilig bare item op dat overeenkomt met de virtuele machine die u wilt beveiligen:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Beveilig de virtuele machine. Als er meer dan één schijf aan de virtuele machine is gekoppeld, geeft u de besturingssysteem schijf op met behulp van de para meter OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. Wacht totdat de virtuele machines een beveiligde status hebben bereikt na de initiële replicatie. Dit kan enige tijd duren, afhankelijk van factoren zoals de hoeveelheid gegevens die moeten worden gerepliceerd en de beschik bare upstream-band breedte naar Azure. Wanneer een beveiligde status is ingesteld, worden de taak status en StateDescription als volgt bijgewerkt: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Herstel eigenschappen van de virtuele machine (zoals de grootte van de VM-rol) en het Azure-netwerk waarmee de VM-NIC na een failover moet worden gekoppeld.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Voer [een testfailover uit.](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover)


## <a name="next-steps"></a>Volgende stappen

Als u het opnieuw beveiligen en failback voor VMware naar Azure wilt uitvoeren, volgt u de stappen die [hier](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback)worden beschreven.

Als u een failover wilt uitvoeren voor Hyper-V naar Azure, volgt u de stappen die [hier](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) worden beschreven en voert u de volgende stappen uit om een failback uit te [voeren.](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback)

Zie [failover in site Recovery](site-recovery-failover.md)voor meer informatie.
