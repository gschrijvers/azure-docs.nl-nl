---
title: Een Azure Linux-virtuele machine coderen
description: Meer informatie over het coderen van een virtuele Azure Linux-machine die is gemaakt in azure met behulp van het Resource Manager-implementatie model.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: fd4a93f4c0b2f052fe5c9890bee01e5da0dcead2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460950"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Een virtuele Linux-machine in azure labelen
In dit artikel worden verschillende manieren beschreven om een virtuele Linux-machine in azure te labelen via het Resource Manager-implementatie model. Tags zijn door de gebruiker gedefinieerde sleutel/waarde-paren die rechtstreeks kunnen worden geplaatst op een resource of resource groep. Azure ondersteunt momenteel Maxi maal 50 Tags per resource en resource groep. Labels kunnen worden geplaatst op een resource op het moment dat ze worden gemaakt of worden toegevoegd aan een bestaande resource. Let op: Tags worden alleen ondersteund voor resources die zijn gemaakt via het Resource Manager-implementatie model.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Labelen met Azure CLI

Als u wilt beginnen, hebt u de nieuwste [Azure cli](/cli/azure/install-azure-cli) geïnstalleerd en aangemeld bij een Azure-account met de opdracht [AZ login](/cli/azure/reference-index#az-login).

U kunt alle eigenschappen van een bepaalde virtuele machine, inclusief de tags, weer geven met behulp van deze opdracht:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Als u een nieuwe VM-tag wilt toevoegen via de Azure CLI, kunt `azure vm update` u de opdracht gebruiken samen met de para meter tag **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Als u tags wilt verwijderen, kunt u de para meter **--Remove** gebruiken in de `azure vm update` opdracht.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Nu we tags hebben toegepast op onze resources Azure CLI en de portal, gaan we de gebruiks gegevens bekijken om de tags in de facturerings portal te bekijken.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Volgende stappen
* Zie [Azure Resource Manager Overview][Azure Resource Manager Overview] en [Tags gebruiken om uw Azure-resources te organiseren][Using Tags to organize your Azure Resources]voor meer informatie over het coderen van uw Azure-resources.
* Als u wilt zien hoe Tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources, raadpleegt u [inzicht in uw Azure-factuur][Understanding your Azure Bill] en [krijgt u inzicht in uw Microsoft Azure Resource verbruik][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
