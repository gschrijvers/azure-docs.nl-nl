---
title: Technische concepten voor Azure container-aanbiedingen-micro soft Commercial Marketplace
description: Technische bronnen en richt lijnen om u te helpen bij het configureren van een container aanbod op Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700978"
---
# <a name="create-an-azure-container-offer"></a>Een Azure-container aanbieding maken

Dit artikel bevat technische bronnen en aanbevelingen om u te helpen bij het maken van een container aanbod op Azure Marketplace.

## <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de [documentatie van Azure container instances](https://docs.microsoft.com/azure/container-instances)voor Quick starts, zelf studies en voor beelden.

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen.

Naast uw oplossings domein moet uw technische team kennis hebben van de volgende micro soft-technologieën:

- Basis informatie over [Azure-Services](https://azure.microsoft.com/services/)
- Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
- Praktische kennis van [Azure-virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
- Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Werk kennis van [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's

Kies een of beide van de volgende script omgevingen om uw container installatie kopie te beheren:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

U wordt aangeraden deze hulpprogram ma's toe te voegen aan uw ontwikkel omgeving:

- [Azure Opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Bekijk de beschik bare hulpprogram ma's op de pagina [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/) . Als u Visual Studio gebruikt, raadpleegt u de hulpprogram ma's die beschikbaar zijn in de [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>De container installatie kopie maken

Zie de volgende zelf studies voor meer informatie:

- [Zelf studie: een container installatie kopie maken voor implementatie naar Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Zelf studie: container installatie kopieën bouwen en implementeren in de Cloud met Azure container Registry taken](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Volgende stappen

- [Maak uw container aanbod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
