---
title: 'Azure AD Connect Cloud-inrichtings agent: automatische upgrade | Microsoft Docs'
description: In dit artikel wordt de ingebouwde functie voor automatische upgrades in de Azure AD Connect Cloud inrichtings agent beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190310"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud-inrichtings agent: automatische upgrade

Zorg ervoor dat uw Azure Active Directory (Azure AD) Connect-installatie van de Cloud-inrichtings agent altijd up-to-date is met de functie voor automatische upgrades.

De agent wordt hier geïnstalleerd: ' Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe '

Als u uw versie wilt controleren, klikt u met de rechter muisknop op het uitvoer bare bestand en selecteert u eigenschappen en vervolgens Details.

![Versie van agent bestand](media/how-to-automatic-upgrade/agent1.png)

De agent Updater wordt hier geïnstalleerd: ' Program files\Azure AD Connect inrichtings agent Updater\AzureADConnectAgentUpdater.exe '

Als u uw versie wilt controleren, klikt u met de rechter muisknop op het uitvoer bare bestand en selecteert u eigenschappen en vervolgens Details.

![Versie van agent updater](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Agent verwijderen
Als u de agent wilt verwijderen, gaat u naar **een programma verwijderen of wijzigen** en verwijdert u het volgende:

- **Updater Microsoft Azure AD-agent**
- **Inrichtings agent Microsoft Azure AD verbinding maken**
- **Inrichtings agent pakket Microsoft Azure AD koppelen**

![Agent verwijderen](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

