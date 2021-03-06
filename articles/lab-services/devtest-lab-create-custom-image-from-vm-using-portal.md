---
title: Een Azure DevTest Labs aangepaste installatie kopie maken op basis van een virtuele machine | Microsoft Docs
description: Meer informatie over het maken van een aangepaste installatie kopie in Azure DevTest Labs van een ingerichte VM met behulp van de Azure Portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "60868450"
---
# <a name="create-a-custom-image-from-a-vm"></a>Een aangepaste installatiekopie maken vanaf een virtuele machine

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

U kunt een aangepaste installatie kopie maken van een ingerichte virtuele machine en vervolgens die aangepaste installatie kopie gebruiken om identieke Vm's te maken. De volgende stappen laten zien hoe u een aangepaste installatie kopie maakt op basis van een virtuele machine:

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met Labs het gewenste Lab.  

1. Selecteer **mijn virtuele machines**in het hoofd venster van het lab.
 
1. Selecteer in het deel venster **mijn virtuele machines** de virtuele machine waarvan u de aangepaste installatie kopie wilt maken.

1. Selecteer in het deel venster beheer van de VM de optie **aangepaste installatie kopie maken (VHD)**.

    ![Menu-item voor aangepaste installatie kopie maken](./media/devtest-lab-create-template/create-custom-image.png)

1. Voer in het deel venster **aangepaste installatie kopie** een naam en een beschrijving in voor uw aangepaste installatie kopie. Deze informatie wordt weer gegeven in de lijst met basissen wanneer u een VM maakt. De aangepaste installatie kopie bevat de besturingssysteem schijf en alle gegevens schijven die aan de virtuele machine zijn gekoppeld.

    ![Deel venster aangepaste afbeelding maken](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selecteer of Sysprep is uitgevoerd op de virtuele machine. Als het Sysprep niet is uitgevoerd op de virtuele machine, geeft u op of Sysprep moet worden uitgevoerd op de virtuele machine wanneer de aangepaste installatie kopie wordt gemaakt.

1. Selecteer **OK** wanneer u klaar bent om de aangepaste installatie kopie te maken.

Na een paar minuten wordt de aangepaste installatie kopie gemaakt en opgeslagen in het opslag account van de test omgeving. Wanneer een Lab-gebruiker een nieuwe virtuele machine wil maken, is de installatie kopie beschikbaar in de lijst met basis installatie kopieën.

![Aangepaste afbeelding beschikbaar in lijst met basis installatie kopieën](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blog berichten

- [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Aangepaste installatie kopieën kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een virtuele machine toevoegen aan uw Lab](devtest-lab-add-vm.md)
