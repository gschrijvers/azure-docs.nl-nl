---
title: Systeem knooppunt groepen gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het maken en beheren van groepen met systeem knooppunten in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 04/28/2020
ms.openlocfilehash: 85cc699d6ef8c632663775e91f2b5cad6ca7a7b6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125244"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Systeem knooppunt groepen beheren in azure Kubernetes service (AKS)

In azure Kubernetes service (AKS) worden knoop punten van dezelfde configuratie samen in *knooppunt groepen*gegroepeerd. Knooppunt groepen bevatten de onderliggende virtuele machines waarop uw toepassingen worden uitgevoerd. Systeem knooppunt groepen en gebruikers knooppunt groepen zijn twee verschillende modi voor de knooppunt groep voor uw AKS-clusters. Systeem knooppunt groepen fungeren het primaire doel van het hosten van essentieel systeem peulen, zoals CoreDNS en tunnelfront. Gebruikers knooppunt groepen gebruiken het primaire doel om uw toepassing te hosten. Toepassing peul kan echter worden gepland op systeem knooppunt groepen als u slechts één groep in uw AKS-cluster wilt hebben. Elk AKS-cluster moet ten minste één systeem knooppunt groep bevatten met ten minste één knoop punt. 

> [!Important]
> Als u één systeem knooppunt groep voor uw AKS-cluster in een productie omgeving uitvoert, raden we u aan om ten minste drie knoop punten voor de knooppunt groep te gebruiken.

## <a name="before-you-begin"></a>Voordat u begint

* U moet de Azure CLI-versie 2.3.1 of later installeren en configureren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor systeem knooppunt groepen.

* Zie [quota's, beperkingen voor de grootte van virtuele machines en beschik baarheid van regio's in azure Kubernetes service (AKS)][quotas-skus-regions].
* Het AKS-cluster moet worden gebouwd met virtuele-machine schaal sets als VM-type.
* De naam van een knooppunt groep mag alleen kleine letters bevatten en moet beginnen met een kleine letter. Voor Linux-knooppunt Pools moet de lengte tussen de 1 en 12 tekens liggen. Voor Windows-knooppunt groepen moet de lengte tussen de 1 en 6 tekens zijn.
* Een API-versie van 2020-03-01 of hoger moet worden gebruikt om een modus voor de knooppunt groep in te stellen.
* De modus van een knooppunt groep is een vereiste eigenschap en moet expliciet worden ingesteld wanneer ARM-sjablonen of directe API-aanroepen worden gebruikt.

## <a name="system-and-user-node-pools"></a>Systeem-en gebruikers knooppunt groepen

De knoop punten van de groep systeem knooppunten hebben elk het label **kubernetes.Azure.com/mode: System**. Elk AKS-cluster bevat ten minste één groep met systeem knooppunten. Systeem knooppunt groepen hebben de volgende beperkingen:

* Systeem groepen osType moet Linux zijn.
* OsType van gebruikers knooppunten kunnen Linux of Windows zijn.
* Systeem groepen moeten ten minste één knoop punt bevatten en gebruikers knooppunt groepen kunnen nul of meer knoop punten bevatten.
* Systeem knooppunt groepen vereisen een VM-SKU van ten minste 2 Vcpu's en 4 GB geheugen.
* Systeem knooppunt groepen moeten ten minste 30 peul ondersteunen, zoals wordt beschreven door de [formule minimale en maximale waarde voor peulen][maximum-pods].
* Voor spot knooppunt Pools zijn gebruikers knooppunt Pools vereist.

U kunt de volgende bewerkingen uitvoeren met knooppunt groepen:

* Wijzig een groep van het systeem knooppunt in een groep met gebruikers knooppunten, tenzij u een andere groep van het systeem knooppunt hebt die u in het AKS-cluster kunt opnemen.
* Wijzig de groep van een gebruikers knooppunt in een groep met systeem knooppunten.
* Gebruikers knooppunt groepen verwijderen.
* U kunt systeem knooppunt groepen verwijderen. u hebt een andere groep van het systeem knooppunt nodig om in het AKS-cluster te plaatsen.
* Een AKS-cluster kan meerdere systeem knooppunt groepen bevatten en moet ten minste één groep systeem knooppunten hebben.
* Als u verschillende onveranderbare instellingen voor bestaande knooppunt groepen wilt wijzigen, kunt u nieuwe knooppunt groepen maken om ze te vervangen. Een voor beeld hiervan is het toevoegen van een nieuwe groep van een knoop punt met een nieuwe maxPods-instelling en het verwijderen van de oude knooppunt groep.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Een nieuw AKS-cluster maken met een systeem knooppunt groep

Wanneer u een nieuw AKS-cluster maakt, maakt u automatisch een systeem knooppunt groep met één knoop punt. De eerste knooppunt groep wordt standaard ingesteld op een modus van het type systeem. Wanneer u nieuwe knooppunt Pools maakt met AZ AKS nodepool add, zijn deze knooppunt groepen gebruikers knooppunt groepen, tenzij u expliciet de modus para meter opgeeft.

In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt in de regio *eastus* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voor beeld wordt een cluster met de naam *myAKSCluster* gemaakt met één systeem groep met één knoop punt. Zorg ervoor dat u voor de werk belasting van uw productie gebruikmaakt van systeem knooppunt groepen met ten minste drie knoop punten. Het volt ooien van deze bewerking kan enkele minuten duren.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Een systeem knooppunt groep toevoegen aan een bestaand AKS-cluster

U kunt een of meer systeem knooppunt groepen toevoegen aan bestaande AKS-clusters. Met de volgende opdracht wordt een knooppunt groep van het type modus systeem toegevoegd met een standaard aantal van drie knoop punten.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Details voor de knooppunt groep weer geven

U kunt de details van de knooppunt groep controleren met de volgende opdracht.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Er is een modus van het type **systeem** gedefinieerd voor systeem knooppunt groepen en een modus van het type **gebruiker** is gedefinieerd voor gebruikers knooppunt groepen.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Systeem-en gebruikers knooppunt groepen bijwerken

U kunt de modus voor zowel systeem-als gebruikers knooppunt groepen wijzigen. U kunt een systeem knooppunt groep alleen wijzigen in een gebruikers groep als er al een andere systeem knooppunt groep bestaat op het AKS-cluster.

Met deze opdracht wordt een systeem knooppunt groep gewijzigd in een groep met gebruikers knooppunten.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Met deze opdracht wordt een groep van een gebruikers knooppunt gewijzigd in een systeem knooppunt groep.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Een systeem knooppunt groep verwijderen

> [!Note]
> Als u systeem knooppunt groepen op AKS-clusters vóór API-versie 2020-03-02 wilt gebruiken, voegt u een nieuwe groep systeem knooppunten toe en verwijdert u de oorspronkelijke standaard knooppunt groep.

U kunt de groep van het systeem knooppunt niet verwijderen. Dit is de eerste standaard knooppunten groep in een AKS-cluster. U hebt nu de flexibiliteit om alle knooppunt groepen uit uw clusters te verwijderen. Aangezien voor AKS-clusters ten minste één groep van systeem knooppunten is vereist, moet u ten minste twee systeem knooppunt groepen hebben op uw AKS-cluster voordat u er een kunt verwijderen.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u systeem knooppunt groepen kunt maken en beheren in een AKS-cluster. Zie [meerdere knooppunt groepen gebruiken][use-multiple-node-pools]voor meer informatie over het gebruik van meerdere knooppunt groepen.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node