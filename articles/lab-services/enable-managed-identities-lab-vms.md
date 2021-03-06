---
title: Beheerde identiteiten inschakelen op uw Lab-Vm's in Azure DevTest Labs
description: In dit artikel wordt beschreven hoe een Lab-eigenaar door de gebruiker toegewezen beheerde identiteiten kan inschakelen op uw virtuele lab-machines.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75692665"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Door de gebruiker toegewezen beheerde identiteiten inschakelen op virtuele lab-machines in Azure DevTest Labs
Als eigenaar van een lab kunt u door de gebruiker toegewezen beheerde identiteiten inschakelen op uw virtuele lab-machines (Vm's) in Azure DevTest Labs.

Een beheerde identiteit kan worden gebruikt om te verifiëren bij elke service die ondersteuning biedt voor Azure Active Directory (AD)-verificatie, met inbegrip van Key Vault, zonder dat er referenties in de code worden door gegeven. Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

Met deze functie kunnen Lab-gebruikers Azure-resources, zoals Azure SQL Database, delen in de context van het lab. De verificatie voor de resource wordt door de identiteit zelf uitgevoerd. Zodra de configuratie is geconfigureerd, wordt elke bestaande/nieuw gemaakte Lab-VM ingeschakeld met deze identiteit. Lab-gebruikers kunnen toegang krijgen tot bronnen wanneer ze zijn aangemeld op hun computers.

> [!NOTE]
> U kunt meerdere door de gebruiker toegewezen beheerde identiteiten toevoegen die moeten worden ingeschakeld op uw Lab-Vm's.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
Voer de volgende stappen uit om een door de gebruiker toegewezen beheerde identiteit voor Lab-Vm's toe te voegen:

1. [Een door de gebruiker toegewezen beheerde identiteit maken in uw abonnement](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navigeer naar de pagina **configuratie en beleid** voor uw Lab.
2. Selecteer **identiteit (preview)** in het menu links.
3. Selecteer het tabblad **virtuele machine** .
4. Selecteer **toevoegen** om een bestaande identiteit te selecteren in een vooraf gevulde vervolg keuzelijst. 

    ![Knop identiteit toevoegen](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Selecteer een bestaande door de **gebruiker beheerde identiteit** in de vervolg keuzelijst en selecteer **OK**. 

    ![Identiteit toevoegen](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API gebruiken

1.  Nadat u een identiteit hebt gemaakt, noteert u de resource-ID van de identiteit. Deze moet eruitzien als in het volgende voor beeld: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Voer een PUT HTTPS-methode uit om een nieuwe **ServiceRunner** -resource toe te voegen aan het lab, zoals wordt weer gegeven in het volgende voor beeld. 

    Resource voor het uitloper van Services is een proxy bron voor het beheer en beheer van beheerde identiteiten in DevTest Labs. De naam van het service loper kan een geldige naam zijn, maar we raden u aan de naam van de beheerde identiteits bron te gebruiken.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Volgende stappen
Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.







