---
title: Configuraties maken op basis van bestaande servers voor Azure Automation status configuratie
description: In dit artikel leest u hoe u configuraties maakt op basis van bestaande servers voor Azure Automation status configuratie.
keywords: DSC, Power shell, configuratie, installatie
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 775fae09c4d618551327669362cd28a0ae2cc801
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837005"
---
# <a name="create-configurations-from-existing-servers"></a>Configuraties maken op basis van bestaande servers

> Van toepassing op: Windows Power shell 5,1

Het maken van configuraties van bestaande servers kan een uitdagende taak zijn.
Mogelijk wilt u niet *alle* instellingen, alleen die voor u.
U moet ook weten in welke volg orde de instellingen moeten worden toegepast om de configuratie correct toe te passen.

> [!NOTE]
> In dit artikel wordt verwezen naar een oplossing die wordt onderhouden door de open source-community.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samen werking, niet van micro soft.

## <a name="community-project-reversedsc"></a>Community-Project: ReverseDSC

Een door de Community onderhouden oplossing met de naam [ReverseDSC](https://github.com/microsoft/reversedsc) is gemaakt om in dit gebied te werken vanaf share point.

De oplossing bouwt voort op de [SharePointDSC-resource](https://github.com/powershell/sharepointdsc) en breidt deze uit om het verzamelen van [gegevens](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) van bestaande share Point-servers te organiseren.
De nieuwste versie heeft meerdere [uitpak modi](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) om te bepalen welk niveau van de informatie moet worden toegevoegd.

Het resultaat van het gebruik van de oplossing is het genereren van [configuratie gegevens](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) die moeten worden gebruikt met SharePointDSC-configuratie scripts.

Zodra de gegevens bestanden zijn gegenereerd, kunt u ze gebruiken met [DSC-configuratie scripts](/powershell/scripting/dsc/overview/overview) om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registreer uw servers vervolgens [on-premises](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) of [in azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) om configuraties te halen.

Als u ReverseDSC wilt uitproberen, gaat u naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/ReverseDSC/) en downloadt u de oplossing of klikt u op project site om de [documentatie](https://github.com/Microsoft/sharepointDSC.reverse)weer te geven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor meer informatie over Power shell DSC.
- Meer informatie over Power shell DSC-resources in [DSC-resources](/powershell/scripting/dsc/resources/resources).
- Zie [Configuring the local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)(Engelstalig) voor meer informatie over de configuratie van de lokale Configuration Manager.