---
title: Quick Start beleid groeps beleidsobjecten-Azure AD | Microsoft Docs
description: Verloop voor Office 365-groepen - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 616910eda33b3ddc49fa6233ccb3989c5e4214e2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582844"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Snelstart: Office 365-groepen voor verloop instellen in Azure Active Directory

In deze snelstart stelt u het verloopbeleid in voor uw Office 365-groepen. Wanneer gebruikers hun eigen groepen kunnen instellen, kunnen niet-gebruikte groepen zich vermenigvuldigen. Eén manier om niet-gebruikte groepen te beheren, is door verloopbeleid voor deze groepen in te stellen om het handmatig verwijderen van groepen te verminderen.

Verloopbeleid maken is eenvoudig:

- Groepen met gebruikers activiteiten worden automatisch vernieuwd als de verlopen bijna
- Groepseigenaren worden op de hoogte gesteld als een groep moet worden vernieuwd die op het punt staat te verlopen
- Een groep die niet wordt vernieuwd, wordt verwijderd
- Een verwijderde Office 365-groep kan binnen dertig dagen door een groepseigenaar of een Azure AD-beheerder worden hersteld

> [!NOTE]
> Groepen gebruiken nu Azure AD Intelligence om automatisch te worden vernieuwd op basis van het feit of ze al recent zijn gebruikt. Deze vernieuwings beslissing is gebaseerd op gebruikers activiteit in groepen in Office 365-services zoals Outlook, share point, teams, Yammer en anderen.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisite"></a>Vereiste

 De rol die is vereist voor het instellen van de groep met minimale bevoegdheden is gebruikers beheerder in de organisatie.

## <a name="turn-on-user-creation-for-groups"></a>Maken van gebruikers voor groepen inschakelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een beheerders account van de gebruiker.

2. Selecteer **Groepen** en vervolgens **Algemeen**.
  
   ![Pagina instellingen voor selfservice groep](./media/groups-quickstart-expiration/self-service-settings.png)

3. Stel **Users can create Office 365 groups** in op **Ja**.

4. Selecteer **Opslaan** om de groepsinstellingen op te slaan als u klaar bent.

## <a name="set-group-expiration"></a>Verloopdatum van de groep instellen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer **Azure Active Directory** > **groepen** > **verlopen** om de verloop instellingen te openen.
  
   ![Pagina verloop instellingen voor groep](./media/groups-quickstart-expiration/expiration-settings.png)

2. Stel het verloopinterval in. Selecteer een vooraf ingestelde waarde of voer een aangepaste waarde van meer dan 31 dagen in. 

3. Geef een e-mailadres op waar meldingen over verlooptijden naartoe moeten worden gestuurd als een groep geen eigenaar heeft.

4. Stel voor deze snelstart **Enable expiration for these Office 365 groups** in op **Alle**.

5. Selecteer **Opslaan** om de instellingen voor het verloop op te slaan als u klaar bent.

Dat is alles. In deze snelstart hebt u het verloopbeleid ingesteld voor de geselecteerde Office 365-groepen.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="to-remove-the-expiration-policy"></a>Verloopbeleid verwijderen

1. Zorg ervoor dat u bent aangemeld bij de [Azure Portal](https://portal.azure.com) met een account dat de globale beheerder is voor uw Azure AD-organisatie.
2. Selecteer de**verval datum**voor **Azure Active Directory** > **groepen** > .
3. Stel **Enable expiration for these Office 365 groups** in op **Geen**.

### <a name="to-turn-off-user-creation-for-groups"></a>Maken van gebruikers voor groepen uitschakelen

1. Selecteer **Azure Active Directory** > **groepen** > **Algemeen**. 
2. Stel **Users can create Office 365 groups in Azure portals** in op **Nee**.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over de verval datum, inclusief instructies voor Power shell en technische beperkingen:

> [!div class="nextstepaction"]
> [Verloop beleid Power shell](groups-lifecycle.md)
