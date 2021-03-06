---
title: Een cluster implementeren in Beschikbaarheidszones
description: Meer informatie over het maken van een Azure Service Fabric cluster in Beschikbaarheidszones.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75609975"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Een Azure Service Fabric-cluster implementeren via Beschikbaarheidszones
Beschikbaarheidszones in Azure is een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Een beschikbaarheids zone is een unieke fysieke locatie die is voorzien van onafhankelijke voeding, koeling en netwerken binnen een Azure-regio.

Service Fabric ondersteunt clusters die over meerdere Beschikbaarheidszones beschikken door knooppunt typen te implementeren die zijn vastgemaakt aan specifieke zones. Dit zorgt voor een hoge Beschik baarheid van uw toepassingen. Azure-beschikbaarheidszones zijn alleen beschikbaar in bepaalde regio's. Zie [Azure-beschikbaarheidszones Overview](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie.

Er zijn voorbeeld sjablonen beschikbaar: [service Fabric sjabloon voor meerdere beschikbaarheids zones](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Aanbevolen topologie voor het primaire knooppunt type van Azure-Service Fabric clusters voor meerdere Beschikbaarheidszones
Een Service Fabric cluster gedistribueerd over Beschikbaarheidszones zorgt voor een hoge Beschik baarheid van de cluster status. Als u een Service Fabric cluster over zones wilt verdelen, moet u een primair knooppunt type maken in elke beschikbaarheids zone die wordt ondersteund door de regio. Hiermee worden Seed-knoop punten gelijkmatig verdeeld over alle typen van het primaire knoop punt.

De aanbevolen topologie voor het primaire knooppunt type vereist de onderstaande bronnen:

* Het betrouwbaarheids niveau van het cluster is ingesteld op Platinum.
* Drie knooppunt typen die als primair zijn gemarkeerd.
    * Elk knooppunt type moet worden toegewezen aan de eigen virtuele-machine schaalset die zich in verschillende zones bevindt.
    * Elke schaalset voor virtuele machines moet ten minste vijf knoop punten bevatten (Silver duurzaamheid).
* Eén open bare IP-resource met behulp van standaard-SKU.
* Een enkele Load Balancer resource met standaard-SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u de schaal sets voor virtuele machines implementeert.

>[!NOTE]
> De groeps eigenschap voor de schaalset voor virtuele machines moet worden ingesteld op True, omdat Service Fabric geen ondersteuning biedt voor een schaalset voor virtuele machines die zones omspant.

 ![Architectuur van Azure Service Fabric-beschikbaarheids zone][sf-architecture]

## <a name="networking-requirements"></a>Netwerk vereisten
### <a name="public-ip-and-load-balancer-resource"></a>Openbaar IP-adres en Load Balancer bron
Als u de eigenschap zones wilt inschakelen voor een resource met een schaalset voor virtuele machines, moet de load balancer en IP-resource waarnaar wordt verwezen door deze schaalset voor virtuele machines, beide gebruikmaken van een *standaard* -SKU. Als u een load balancer of IP-bron maakt zonder de SKU-eigenschap, wordt een basis-SKU gemaakt die geen ondersteuning biedt voor Beschikbaarheidszones. Een standaard-SKU load balancer blokkeert standaard al het verkeer van de buiten kant. Als u buiten verkeer wilt toestaan, moet er een NSG in het subnet worden geïmplementeerd.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Het is niet mogelijk om een in-place wijziging van de SKU in het open bare IP-adres en load balancer resources uit te voeren. Zie de sectie migratie in dit artikel als u migreert van bestaande resources die een basis-SKU hebben.

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-regels voor schaal sets voor virtuele machines
De load balancer binnenkomende NAT-regels moeten overeenkomen met de NAT-groepen van de virtuele-machine schaalset. Elke schaalset voor virtuele machines moet een unieke binnenkomende NAT-groep hebben.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standaard SKU Load Balancer uitgaande regels
Standard Load Balancer en standaard open bare IP introduceren nieuwe mogelijkheden en verschillende gedragingen voor uitgaande connectiviteit in vergelijking met de basis-Sku's. Als u een uitgaande verbinding wilt gebruiken bij het werken met standaard-Sku's, moet u deze expliciet definiëren met een openbaar IP-adres of standaard open bare Load Balancer. Zie voor meer informatie [uitgaande verbindingen](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) en [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> De standaard sjabloon verwijst naar een NSG waarmee al het uitgaande verkeer standaard wordt toegestaan. Binnenkomend verkeer is beperkt tot de poorten die vereist zijn voor Service Fabric-beheer bewerkingen. De NSG-regels kunnen worden aangepast om te voldoen aan uw vereisten.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zones inschakelen op een schaalset voor virtuele machines
Als u een zone wilt inschakelen, moet u op een schaalset voor virtuele machines de volgende drie waarden in de resource van de virtuele-machine schaalset opgeven.

* De eerste waarde is de eigenschap **zones** , waarmee wordt opgegeven in welke beschikbaarheids zone de schaalset voor virtuele machines wordt geïmplementeerd.
* De tweede waarde is de eigenschap ' singlePlacementGroup ', die moet worden ingesteld op True.
* De derde waarde is de eigenschap ' faultDomainOverride ' in de Service Fabric extensie voor de virtuele-machine schaalset. De waarde voor deze eigenschap moet de regio en zone bevatten waarin deze schaalset voor virtuele machines wordt geplaatst. Voor beeld: "faultDomainOverride": "oostus/Az1" alle resources voor de schaalset van virtuele machines moeten in dezelfde regio worden geplaatst, omdat Azure Service Fabric clusters geen ondersteuning bieden voor meerdere regio's.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Meerdere primaire knooppunt typen inschakelen in de Service Fabric cluster resource
Als u een of meer knooppunt typen als primair wilt instellen in een cluster bron, stelt u de eigenschap ' isPrimary ' in op ' True '. Bij het implementeren van een Service Fabric cluster via Beschikbaarheidszones, moet u drie knooppunt typen hebben in verschillende zones.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migreren naar met behulp van Beschikbaarheidszones van een cluster met behulp van een basis-SKU Load Balancer en een basis-SKU-IP
Als u een cluster wilt migreren dat gebruikmaakt van een Load Balancer en IP met een basis-SKU, moet u eerst een volledig nieuwe Load Balancer en IP-bron maken met behulp van de standaard-SKU. Het is niet mogelijk om deze resources in-place bij te werken.

Er moet naar de nieuwe LB en IP worden verwezen in de nieuwe knooppunt typen voor de zone voor meerdere Beschik baarheid die u wilt gebruiken. In het bovenstaande voor beeld zijn drie nieuwe resources voor virtuele-machine schaal sets toegevoegd in zones 1, 2 en 3. Deze schaal sets voor virtuele machines verwijzen naar de nieuw gemaakte LB en IP en worden gemarkeerd als primaire knooppunt typen in de Service Fabric cluster bron.

U moet de nieuwe resources toevoegen aan uw bestaande resource manager-sjabloon om te beginnen. Deze resources omvatten:
* Een open bare IP-resource met een standaard-SKU.
* Een Load Balancer resource met standaard-SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u de schaal sets voor virtuele machines implementeert.
* Drie knooppunt typen die als primair zijn gemarkeerd.
    * Elk knooppunt type moet worden toegewezen aan de eigen virtuele-machine schaalset die zich in verschillende zones bevindt.
    * Elke schaalset voor virtuele machines moet ten minste vijf knoop punten bevatten (Silver duurzaamheid).

Een voor beeld van deze resources vindt u in de [voorbeeld sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Zodra de implementatie van de resources is voltooid, kunt u beginnen met het uitschakelen van de knoop punten in het primaire knooppunt type van het oorspronkelijke cluster. Wanneer de knoop punten zijn uitgeschakeld, worden de systeem services gemigreerd naar het nieuwe primaire knooppunt type dat in de bovenstaande stap is geïmplementeerd.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Zodra alle knoop punten zijn uitgeschakeld, worden de systeem services uitgevoerd op het primaire knooppunt type, dat over meerdere zones wordt verspreid. U kunt de uitgeschakelde knoop punten vervolgens verwijderen uit het cluster. Nadat de knoop punten zijn verwijderd, kunt u de oorspronkelijke IP-, Load Balancer-en virtuele-machine schaal sets verwijderen.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Vervolgens verwijdert u de verwijzingen naar deze resources uit de Resource Manager-sjabloon die u hebt geïmplementeerd.

Bij de laatste stap moet de DNS-naam en het open bare IP-adres worden bijgewerkt.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
