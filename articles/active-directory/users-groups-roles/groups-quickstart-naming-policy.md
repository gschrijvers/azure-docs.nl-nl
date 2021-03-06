---
title: Quick Start van groeps beleidsobjecten-Azure Active Directory | Microsoft Docs
description: Legt uit hoe u nieuwe gebruikers toevoegt of bestaande gebruikers verwijdert in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4253f5bd702abd061cf1cddd4badd68c9cd5d475
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582836"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Snelstart: naambeleid voor groepen in Azure Active Directory

In deze Quick Start stelt u het naamgevings beleid in uw Azure Active Directory-organisatie (Azure AD) in voor door de gebruiker gemaakte Office 365-groepen, om u te helpen bij het sorteren en doorzoeken van de groepen van uw organisatie. U kunt het naambeleid onder meer gebruiken voor:

* Het communiceren van de functie van een groep, het lidmaatschap, de geografische regio of de maker van een groep.
* Het classificeren van groepen in het adresboek.
* Zorgen dat het gebruik van bepaalde woorden voor groepsnamen en -aliassen wordt geblokkeerd.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Het groeps naamgevings beleid configureren in de Azure Portal

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een beheerders account van de gebruiker.
1. Selecteer **groepen**en selecteer vervolgens **naamgevings beleid** om de pagina naamgevings beleid te openen.

    ![Open de pagina naamgevings beleid in het beheer centrum](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Het naamgevings beleid voor voegsel-achtervoegsels weer geven of bewerken

1. Selecteer op de pagina **naamgevings beleid** de optie **groeps naamgevings beleid**.
1. U kunt het huidige naamgevings beleid voor voor voegsels of achtervoegsels afzonderlijk weer geven of bewerken door de kenmerken of teken reeksen te selecteren die u wilt afdwingen als onderdeel van het naamgevings beleid.
1. Als u een voor voegsel of achtervoegsel uit de lijst wilt verwijderen, selecteert u het voor voegsel of achtervoegsel en selecteert u vervolgens **verwijderen**. Meerdere items kunnen tegelijkertijd worden verwijderd.
1. Selecteer **Opslaan** om uw wijzigingen in het beleid van kracht te laten worden.

### <a name="view-or-edit-the-custom-blocked-words"></a>Aangepaste geblokkeerde woorden weer geven of bewerken

1. Selecteer op de pagina **naamgevings beleid** de optie **geblokkeerde woorden**.

    ![de lijst met geblokkeerde woorden voor het naamgevings beleid bewerken en uploaden](./media/groups-naming-policy/blockedwords.png)

1. De huidige lijst met aangepaste geblokkeerde woorden weer geven of bewerken door **downloaden**te selecteren.
1. Upload de nieuwe lijst met aangepaste geblokkeerde woorden door het bestands pictogram te selecteren.
1. Selecteer **Opslaan** om uw wijzigingen in het beleid van kracht te laten worden.

Dat is alles. U hebt het naambeleid ingesteld en uw aangepaste, geblokkeerde woorden toegevoegd.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Verwijder het naamgevings beleid met behulp van Azure Portal

1. Selecteer op de pagina **naamgevings beleid** de optie **beleid verwijderen**.
1. Nadat u het verwijderen hebt bevestigd, wordt het naamgevings beleid verwijderd, met inbegrip van het naamgevings beleid voor voegsel en achtervoegsel en eventuele aangepaste geblokkeerde woorden.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u het naamgevings beleid voor uw Azure AD-organisatie kunt instellen via de Azure Portal.

Ga naar het volgende artikel voor meer informatie, waaronder de Power shell-cmdlets voor het benoemen van naamgevings beleid, technische beperkingen, het toevoegen van een lijst met aangepaste geblokkeerde woorden en de ervaring van eind gebruikers in Office 365-apps.
> [!div class="nextstepaction"]
> [Naamgevings beleid Power shell](groups-naming-policy.md)