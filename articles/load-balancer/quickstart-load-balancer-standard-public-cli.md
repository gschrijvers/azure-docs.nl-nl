---
title: 'Snelstartgids: een open bare Load Balancer maken-Azure CLI'
titleSuffix: Azure Load Balancer
description: In deze snelstart vindt u meer informatie over het maken van een openbare load balancer met behulp van Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1f6a05fdfc28adf412ffbd1402e37b69d1c51634
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79477762"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Snelstartgids: een Standard Load Balancer maken om taken te verdelen over Vm's met behulp van Azure CLI

In deze Quick start ziet u hoe u een open bare Load Balancer maakt. U test de load balancer door twee virtuele machines (VM's) te implementeren waarop een Ubuntu-server wordt uitgevoerd en waarbij de taken van een web-app gelijkelijk over twee VM's worden verdeeld.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupSLB* gemaakt op de locatie *VS Oost*:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. Gebruik [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) om een redundante standaard zone met de naam *myPublicIP* in *myResourceGroupSLB*te maken.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Een openbaar IP-adres voor zonegebonden maken in zone 1 gebruiken:

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Gebruiken `-SKU Basic` voor het maken van een open bare basis-IP. Algemene open bare Ip's zijn niet compatibel met **standaard** Load Balancer. Micro soft raadt aan om **standaard** te gebruiken voor werk belastingen voor de productie.

> [!IMPORTANT]
> In de rest van deze Snelstartgids wordt ervan uitgegaan dat **standaard** -SKU is gekozen tijdens het bovenstaande SKU-selectie proces.

## <a name="create-azure-load-balancer"></a>Een Azure Load Balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:
  - een front-end IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  - een back-end-IP-pool waar de front-end-pool het netwerkverkeer op de load balancer naartoe stuurt.
  - een statustest die de status van de back-end-VM-exemplaren vaststelt.
  - een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer"></a>Load balancer maken

Maak met [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) een openbare Azure Load Balancer met de naam **myLoadBalancer** die een front-endgroep bevat met de naam **myFrontEnd**, een back-endgroep met de naam **myBackEndPool**, die wordt gekoppeld aan het openbare IP-adres **myPublicIP** dat u in de vorige stap hebt gemaakt. Gebruiken `--sku basic` voor het maken van een open bare basis-IP. Micro soft raadt standaard-SKU aan voor productie werkbelastingen.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

> [!IMPORTANT]
> In de rest van deze Snelstartgids wordt ervan uitgegaan dat **standaard** -SKU is gekozen tijdens het bovenstaande SKU-selectie proces.

### <a name="create-the-health-probe"></a>Statustest maken

Een statuscontrole controleert alle exemplaren van de virtuele machines om ervoor te zorgen dat deze netwerkverkeer kunnen verzenden. Het exemplaar van een virtuele machine met mislukte testcontroles wordt uit de load balancer verwijderd totdat deze weer online komt en een testcontrole bepaalt of deze in orde is. Maak met [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) een statustest om de status van de virtuele machines te bewaken. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Een load balancer-regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back-end-IP-pool om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak met [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) de regel *myLoadBalancerRuleWeb* voor het luisteren naar poort 80 in de front-endpool *myFrontEnd* en het verzenden van netwerkverkeer met gelijke taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u enkele VM's implementeert om uw load balancer te testen, maakt u de ondersteunende virtuele-netwerkbronnen.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) in *myResourceGroup* een virtueel netwerk met de naam *myVnet* met een subnet met de naam *mySubnet*.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Voor Standard Load Balancer moeten de VM's in het back-endadres NIC's bevatten die tot een netwerkbeveiligingsgroep behoren. Maak een netwerkbeveiligingsgroep om de binnenkomende verbindingen met uw virtuele netwerk te definiëren.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een regel voor de netwerkbeveiligingsgroep om binnenkomende verbindingen via poort 80 toe te staan.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-nics"></a>NIC's maken

Maak met [az network nic create](/cli/azure/network/nic#az-network-nic-create) drie netwerkinterfaces en koppel deze aan het openbare IP-adres en de netwerkbeveiligingsgroep. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Back-endservers maken

In dit voorbeeld maakt u drie virtuele machines die worden gebruikt als back-endservers voor de load balancer. Installeer NGINX op de virtuele machines om te controleren of de load balancer is gemaakt.

Als u een basis Load Balancer maakt met een standaard openbaar IP-adres, moet u een Beschikbaarheidsset maken met ([AZ VM availabilityset maken](/cli/azure/network/nic) ) om uw virtuele machines aan toe te voegen. Voor standaard load balancers is deze extra stap niet vereist. Micro soft raadt u aan om standaard te gebruiken.

### <a name="create-three-virtual-machines"></a>Drie virtuele machines maken

U kunt een cloud-init-configuratiebestand maken om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. Maak in uw huidige shell een bestand met de naam cloud-init.txt en plak de volgende configuratie in de shell. Zorg ervoor dat u het hele cloud-init-bestand correct kopieert, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Maak de virtuele machines met [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```

Het kan enkele minuten duren voordat de VM's zijn geïmplementeerd.

## <a name="test-the-load-balancer"></a>Load balancer testen

Gebruik [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) om het openbare IP-adres van de load balancer op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

   ![Load balancer testen](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
  az group delete --name myResourceGroupSLB
```

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u een Standard Load Balancer, gekoppelde Vm's aan de app gemaakt, de regel voor het Load Balancer verkeer geconfigureerd, de status test en vervolgens de Load Balancer getest. Ga door naar [Azure Load Balancer zelf studies](tutorial-load-balancer-standard-public-zone-redundant-portal.md)voor meer informatie over Azure Load Balancer.

Meer informatie over [Load Balancer-en beschikbaarheids zones](load-balancer-standard-availability-zones.md).