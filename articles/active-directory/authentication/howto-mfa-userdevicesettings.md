---
title: Gebruikers instellingen beheren voor Azure Multi-Factor Authentication-Azure Active Directory
description: Meer informatie over het configureren van Azure Active Directory gebruikers instellingen voor Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309762"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gebruikers instellingen voor Azure Multi-Factor Authentication beheren

Als u de gebruikers van Azure Multi-Factor Authentication wilt beheren, kunt u vereisen dat gebruikers hun wacht woord opnieuw instellen, zich opnieuw registreren voor MFA of bestaande MFA-sessies intrekken. Voor gebruikers die app-wacht woorden hebben opgegeven, kunt u er ook voor kiezen om deze wacht woorden te verwijderen, waardoor verouderde verificatie in deze toepassingen mislukt. Deze acties kunnen nodig zijn als u hulp moet bieden aan een gebruiker of als u de beveiligings status opnieuw wilt instellen.

## <a name="manage-user-authentication-options"></a>Opties voor gebruikers verificatie beheren

Als u de rol *authenticatie beheerder* hebt toegewezen, kunt u vereisen dat gebruikers hun wacht woord opnieuw instellen, zich opnieuw registreren voor MFA of de bestaande MFA-sessies van hun gebruikers object intrekken. Voer de volgende stappen uit om gebruikers instellingen te beheren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory** > **gebruikers** > **alle gebruikers**.
1. Kies de gebruiker waarvoor u een actie wilt uitvoeren en selecteer **verificatie methoden**. Selecteer boven aan het venster een van de volgende opties voor de gebruiker:
   - **Wacht woord opnieuw instellen** stelt het wacht woord van de gebruiker opnieuw in en wijst een tijdelijk wacht woord toe dat moet worden gewijzigd bij de volgende aanmelding.
   - **Vereisen dat het opnieuw registreren van MFA vereist** is, zodat wanneer de gebruiker de volgende keer zich aanmeldt, wordt gevraagd om een nieuwe MFA-verificatie methode in te stellen.
   - Met **MFA-sessies intrekken** worden de MFA-sessies van de gebruiker gewist en moeten ze MFA de volgende keer dat het beleid op het apparaat wordt vereist, worden uitgevoerd.

   ![Verificatie methoden beheren vanuit de Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Bestaande app-wacht woorden van gebruikers verwijderen

Als dat nodig is, kunt u alle app-wacht woorden verwijderen die een gebruiker heeft gemaakt. Niet-browser-apps die zijn gekoppeld aan deze app-wacht woorden, werken niet totdat er een nieuw app-wacht woord is gemaakt. *Globale beheerders* machtigingen zijn vereist om deze actie uit te voeren.

Voer de volgende stappen uit om de app-wacht woorden van een gebruiker te verwijderen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory** > **gebruikers** > **alle gebruikers**.
1. Selecteer **multi-factor Authentication**. Mogelijk moet u naar rechts schuiven om deze menu optie weer te geven. Selecteer de onderstaande scherm afbeelding om het volledige Azure Portal venster en de menu locatie weer te geven:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multi-Factor Authentication selecteren in het venster gebruikers in azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Schakel het selectie vakje in naast de gebruiker of gebruikers die u wilt beheren. Er wordt aan de rechter kant een lijst met opties voor snelle stappen weer gegeven.
1. Selecteer **gebruikers instellingen beheren**en schakel het selectie vakje in om **alle bestaande app-wacht woorden te verwijderen die door de geselecteerde gebruikers zijn gegenereerd**, zoals ![wordt weer gegeven in het volgende voor beeld: alle bestaande app-wacht woorden verwijderen](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecteer **Opslaan**en **sluiten**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de instellingen van afzonderlijke gebruikers geholpen. Zie [instellingen voor Azure-multi-factor Authentication configureren](howto-mfa-mfasettings.md) voor informatie over het configureren van Azure multi-factor Authentication-Service-instellingen

Als uw gebruikers hulp nodig hebben, raadpleegt u de [Gebruikers handleiding voor Azure multi-factor Authentication](../user-help/multi-factor-authentication-end-user.md).
