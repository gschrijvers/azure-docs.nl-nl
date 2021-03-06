---
title: Verkeer routeren voor HA-toepassingen-Azure CLI-Traffic Manager
description: Voor beeld van Azure CLI-script-verkeer routeren voor hoge Beschik baarheid van toepassingen
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: e108fbe54ce67d4eb354319450259fc410462c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460610"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Verkeer routeren voor hoge Beschik baarheid van toepassingen met behulp van Azure CLI

Met dit script maakt u een resource groep, twee app service-abonnementen, twee web-apps, een Traffic Manager-profiel en twee Traffic Manager-eind punten. Traffic Manager stuurt het verkeer naar de toepassing in één regio als de primaire regio, en naar de secundaire regio wanneer de toepassing in de primaire regio niet beschikbaar is. Voordat u het script uitvoert, moet u de waarden voor MyWebApp, MyWebAppL1 en MyWebAppL2 wijzigen in unieke waarden in Azure. Nadat het script is uitgevoerd, hebt u toegang tot de app in de primaire regio met de URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeld script is uitgevoerd, kan de volgende opdracht worden gebruikt om de resource groep, App Service-app en alle gerelateerde resources te verwijderen.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, web-app, Traffic Manager-profiel en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Hiermee maakt u een App Service-plan. Dit is net als een server farm voor uw Azure-web-app. |
| [AZ webapp web create](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Hiermee maakt u een Azure-web-app binnen het App Service-abonnement. |
| [AZ Network Traffic-Manager profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Hiermee maakt u een Azure Traffic Manager-profiel. |
| [AZ Network Traffic-Manager endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Hiermee voegt u een eindpunt toe aan een Azure Traffic Manager-profiel. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende App Service CLI-voorbeeld scripts vindt u in de [documentatie van Azure-netwerken](../cli-samples.md).
