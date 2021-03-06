---
title: KEUR Azure Kubernetes-cluster bewaken-Sysdig
description: Kubernetes-cluster bewaken in Azure Container Service met behulp van Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371096"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>KEUR Een Azure Container Service Kubernetes-cluster bewaken met behulp van Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
In dit scenario wordt ervan uitgegaan dat u [een Kubernetes-cluster hebt gemaakt met behulp van Azure container service](container-service-kubernetes-walkthrough.md).

Er wordt ook van uitgegaan dat u de Azure CLI-en kubectl-hulpprogram ma's hebt geïnstalleerd.

U kunt testen of u het hulp `az` programma hebt geïnstalleerd door het volgende uit te voeren:

```azurecli
az --version
```

Als u het `az` hulp programma niet hebt geïnstalleerd, vindt u [hier](https://github.com/azure/azure-cli#installation)instructies.

U kunt testen of u het hulp `kubectl` programma hebt geïnstalleerd door het volgende uit te voeren:

```console
kubectl version
```

Als u niet hebt `kubectl` geïnstalleerd, kunt u het volgende uitvoeren:

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig is een externe bewaking als een service bedrijf dat containers kan bewaken in uw Kubernetes-cluster dat wordt uitgevoerd in Azure. Voor het gebruik van Sysdig is een actief Sysdig-account vereist.
U kunt zich aanmelden voor een account op hun [site](https://app.sysdigcloud.com).

Nadat u bent aangemeld bij de Sysdig-cloudwebsite, klikt u op uw gebruikersnaam. Op de pagina ziet u uw ‘toegangssleutel’. 

![Sysdig API-sleutel](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>De Sysdig-agents worden geïnstalleerd in Kubernetes
Voor het bewaken van uw containers voert Sysdig een proces uit op elke machine `DaemonSet`met behulp van een Kubernetes.
DaemonSets zijn Kubernetes API-objecten die één exemplaar van een container per computer uitvoeren.
Ze zijn perfect voor het installeren van hulpprogram ma's zoals de bewakings agent van Sysdig.

Als u de Sysdig-daemonset wilt installeren, moet u [de sjabloon](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) eerst downloaden van Sysdig. Sla het bestand op `sysdig-daemonset.yaml`als.

In Linux en OS X kunt u het volgende uitvoeren:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

In PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Bewerk vervolgens dat bestand om uw toegangs sleutel in te voegen, die u hebt verkregen via uw Sysdig-account.

Maak ten slotte de Daemonset:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Uw bewaking weer geven
Zodra de agents zijn geïnstalleerd en worden uitgevoerd, moeten ze gegevens weer naar Sysdig.  Ga terug naar het [sysdig-dash board](https://app.sysdigcloud.com) en Bekijk de informatie over uw containers.

U kunt ook Kubernetes-specifieke Dash boards installeren via de [wizard Nieuw dash board](https://app.sysdigcloud.com/#/dashboards/new).
