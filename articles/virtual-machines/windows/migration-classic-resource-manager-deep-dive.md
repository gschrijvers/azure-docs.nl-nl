---
title: Klassieke naar Azure Resource Manager migratie technische diepe duik
description: Technische deep dive over platformondersteunde migratie van resources van het klassieke implementatiemodel naar Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ebd67bdf34bce1d90057ca402b4e3be243b7ec6c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866179"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager

> [!IMPORTANT]
> Vandaag de dag gebruikt ongeveer 90% van de IaaS VM's [Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Vanaf 28 februari 2020 zijn klassieke VM's afgeschaft en op 1 maart 2023 volledig met pensioen gegaan. [Meer informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe het u beïnvloedt.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Laten we een deep-duik nemen bij het migreren van het klassieke Azure-implementatiemodel naar het Azure Resource Manager-implementatiemodel. We bekijken resources op resource- en functieniveau om u te helpen begrijpen hoe het Azure-platform resources migreert tussen de twee implementatiemodellen. Voor meer informatie, lees het artikel over de aankondiging van de service: [Door het platform ondersteunde migratie van IaaS-bronnen van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway klassieker naar Resource Manager migratie](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [ExpressRoute-circuits en bijbehorende virtuele netwerken migreren van de klassieker naar het implementatiemodel resourcebeheer](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Communitytools voor het helpen bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest gestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
