---
title: Gecombineerde registratie voor SSPR en MFA - Azure Active Directory
description: Azure AD Multi-Factor Authentication en self-service voor wachtwoord herstel
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4caaf8704f2ee49f8f094ad22065ae462154be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143924"
---
# <a name="combined-security-information-registration-overview"></a>Overzicht van registratie van gecombineerde beveiligings gegevens

Vóór de gecombineerde registratie hebben gebruikers de verificatie methoden voor Azure Multi-Factor Authentication en self-service voor wachtwoord herstel (SSPR) afzonderlijk geregistreerd. Het is bekend dat er soort gelijke methoden werden gebruikt voor Multi-Factor Authentication en SSPR, maar dat ze voor beide functies moesten registreren. Met gecombineerde registratie kunnen gebruikers zich eenmaal registreren en profiteren van de voor delen van Multi-Factor Authentication en SSPR.

In dit artikel vindt u een overzicht van de gecombineerde beveiligings registratie. Raadpleeg het volgende artikel om aan de slag te gaan met de gecombineerde beveiligings registratie:

> [!div class="nextstepaction"]
> [Gecombineerde beveiligings registratie inschakelen](howto-registration-mfa-sspr-combined.md)

![Mijn profiel waarin geregistreerde beveiligings gegevens voor een gebruiker worden weer gegeven](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Voordat u de nieuwe ervaring inschakelt, raadpleegt u deze documentatie met beheerders rechten en de gebruikers gerichte documentatie om te controleren of u de functionaliteit en het effect van deze functie begrijpt. Baseer uw training op de [gebruikers documentatie](../user-help/user-help-security-info-overview.md) om uw gebruikers voor te bereiden op de nieuwe ervaring en om te zorgen voor een geslaagde implementatie.

Registratie van gegevens over gecombineerde beveiliging van Azure AD is momenteel niet beschikbaar voor nationale Clouds, zoals Azure VS government, Azure Duitsland of Azure China 21Vianet.

> [!IMPORTANT]
> Gebruikers die zijn ingeschakeld voor de oorspronkelijke preview-versie en de verbeterde gecombineerde registratie-ervaring, zien het nieuwe gedrag. Gebruikers die zijn ingeschakeld voor beide ervaringen, zien alleen de nieuwe ervaring mijn profiel. Het nieuwe mijn profiel wordt uitgelijnd met het uiterlijk van de gecombineerde registratie en biedt een naadloze ervaring voor gebruikers. Gebruikers kunnen mijn profiel zien door naar te [https://myprofile.microsoft.com](https://myprofile.microsoft.com)gaan.
>
> Er wordt mogelijk een fout bericht weer gegeven wanneer u probeert toegang te krijgen tot de optie beveiligings gegevens. Bijvoorbeeld, we kunnen u niet aanmelden. In dit geval moet u bevestigen dat u geen configuratie-of groeps beleidsobject hebt waarmee cookies van derden in de webbrowser worden geblokkeerd.

Mijn profiel pagina's worden gelokaliseerd op basis van de taal instellingen van de computer die toegang heeft tot de pagina. Micro soft slaat de meest recente taal op die wordt gebruikt in de cache van de browser, zodat de daaropvolgende pogingen om toegang te krijgen tot de pagina's worden weer gegeven in de laatste gebruikte taal. Als u de cache wist, worden de pagina's opnieuw weer gegeven. Als u een specifieke taal wilt forceren, kunt u aan `?lng=<language>` het einde van de URL toevoegen, waarbij `<language>` de code is van de taal die u wilt weer geven.

![SSPR of andere methoden voor beveiligings verificatie instellen](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Methoden die beschikbaar zijn in gecombineerde registratie

Bij gecombineerde registratie worden de volgende verificatie methoden en-acties ondersteund:

|   | Registreren | Wijzigen | Verwijderen |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (Maxi maal 5) | Nee | Ja |
| Andere verificator-app | Ja (Maxi maal 5) | Nee | Ja |
| Hardware-token | Nee | Nee | Ja |
| Telefoon | Ja | Ja | Ja |
| Alternatief telefoon nummer | Ja | Ja | Ja |
| Zakelijke telefoon | Nee | Nee | Nee |
| E-mail | Ja | Ja | Ja |
| Beveiligingsvragen | Ja | Nee | Ja |
| App-wachtwoorden | Ja | Nee | Ja |
| FIDO2-beveiligings sleutels<br />*Beheerde modus alleen via de pagina met [beveiligings gegevens](https://mysignins.microsoft.com/security-info)*| Ja | Ja | Ja |

> [!NOTE]
> App-wacht woorden zijn alleen beschikbaar voor gebruikers die zijn afgedwongen voor Multi-Factor Authentication. App-wacht woorden zijn niet beschikbaar voor gebruikers die zijn ingeschakeld voor Multi-Factor Authentication via een beleid voor voorwaardelijke toegang.

Gebruikers kunnen een van de volgende opties instellen als de standaard Multi-Factor Authentication methode:

- Microsoft Authenticator: melding.
- Verificator-app of-hardware-token-code.
- Telefoon oproep.
- Sms-bericht.

Wanneer we meer authenticatie methoden blijven toevoegen aan Azure AD, zullen deze methoden beschikbaar zijn in gecombineerde registratie.

## <a name="combined-registration-modes"></a>Gecombineerde registratie modi

Er zijn twee modi voor gecombineerde registratie: onderbreken en beheren.

- De **interrupt-modus** is een wizard-achtige ervaring die wordt weer gegeven aan gebruikers wanneer ze hun beveiligings gegevens registreren of vernieuwen bij het aanmelden.
- De **beheer modus** maakt deel uit van het gebruikers profiel en stelt gebruikers in staat om hun beveiligings gegevens te beheren.

Voor beide modi moeten gebruikers die eerder een methode hebben geregistreerd die kan worden gebruikt voor Multi-Factor Authentication, Multi-Factor Authentication uitvoeren voordat ze toegang kunnen krijgen tot hun beveiligings gegevens. Gebruikers moeten hun gegevens bevestigen voordat ze hun eerder geregistreerde methoden blijven gebruiken. 

### <a name="interrupt-mode"></a>Interrupt-modus

Gecombineerde registratie respecteert zowel Multi-Factor Authentication als SSPR-beleid als beide zijn ingeschakeld voor uw Tenant. Deze beleids regels bepalen of een gebruiker wordt onderbroken voor registratie tijdens het aanmelden en welke methoden beschikbaar zijn voor registratie.

Hier volgen enkele scenario's waarin gebruikers mogelijk wordt gevraagd om de beveiligings gegevens te registreren of te vernieuwen:

- Multi-Factor Authentication registratie afgedwongen via identiteits beveiliging: gebruikers worden gevraagd zich te registreren tijdens het aanmelden. Ze registreren Multi-Factor Authentication methoden en SSPR-methoden (als de gebruiker is ingeschakeld voor SSPR).
- Multi-Factor Authentication registratie wordt afgedwongen via Multi-Factor Authentication per gebruiker: gebruikers worden gevraagd zich te registreren tijdens het aanmelden. Ze registreren Multi-Factor Authentication methoden en SSPR-methoden (als de gebruiker is ingeschakeld voor SSPR).
- Multi-Factor Authentication registratie wordt afgedwongen via voorwaardelijke toegang of ander beleid: gebruikers worden gevraagd zich te registreren wanneer ze een resource gebruiken waarvoor Multi-Factor Authentication is vereist. Ze registreren Multi-Factor Authentication methoden en SSPR-methoden (als de gebruiker is ingeschakeld voor SSPR).
- SSPR registratie afgedwongen: gebruikers worden gevraagd zich te registreren tijdens het aanmelden. Ze registreren alleen SSPR-methoden.
- SSPR vernieuwen afgedwongen: gebruikers moeten hun beveiligings gegevens controleren met een interval dat door de beheerder is ingesteld. Gebruikers worden weer gegeven met hun informatie en kunnen de huidige gegevens bevestigen of zo nodig wijzigingen aanbrengen.

Wanneer registratie wordt afgedwongen, worden gebruikers weer gegeven het minimale aantal methoden dat nodig is om te voldoen aan de Multi-Factor Authentication-en SSPR-beleids regels, van de meeste tot minst veilig.

Bijvoorbeeld:

- Een gebruiker is ingeschakeld voor SSPR. Het SSPR-beleid vereist twee methoden om opnieuw te worden ingesteld en heeft mobiele app-code, e-mail en telefoon ingeschakeld.
   - Deze gebruiker is verplicht om twee methoden te registreren.
      - De gebruiker wordt standaard de verificator-app en-telefoon weer gegeven.
      - De gebruiker kan ervoor kiezen om e-mail te registreren in plaats van de verificator-app of-telefoon.

Dit stroom diagram beschrijft welke methoden worden weer gegeven wanneer een gebruiker wordt gestuit tijdens het aanmelden:

![Stroom diagram van gecombineerde beveiligings gegevens](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Als u zowel Multi-Factor Authentication als SSPR hebt ingeschakeld, wordt u aangeraden Multi-Factor Authentication registratie af te dwingen.

Als het SSPR-beleid vereist dat gebruikers hun beveiligings gegevens met regel matige tussen pozen controleren, worden gebruikers onderbroken tijdens het aanmelden en worden alle geregistreerde methoden weer gegeven. Ze kunnen de huidige informatie bevestigen als deze up-to-date is, of ze kunnen wijzigingen aanbrengen als dat nodig is. Gebruikers moeten multi-factor Authentication uitvoeren wanneer ze toegang krijgen tot deze pagina.

### <a name="manage-mode"></a>Beheer modus

Gebruikers hebben toegang tot de beheer modus door [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) te gaan naar of door in mijn profiel **beveiligings gegevens** te selecteren. Van daaruit kunnen gebruikers methoden toevoegen, bestaande methoden verwijderen of wijzigen, de standaard methode wijzigen en nog veel meer.

## <a name="key-usage-scenarios"></a>Scenario's voor het gebruik van sleutels

### <a name="set-up-security-info-during-sign-in"></a>Beveiligingsgegevens instellen tijdens aanmelding

Een beheerder heeft de registratie afgedwongen.

Een gebruiker heeft niet alle vereiste beveiligings gegevens ingesteld en gaat naar het Azure Portal. Nadat de gebruikers naam en het wacht woord zijn ingevoerd, wordt de gebruiker gevraagd om beveiligings gegevens in te stellen. De gebruiker volgt vervolgens de stappen die in de wizard worden weer gegeven om de vereiste beveiligings gegevens in te stellen. Als uw instellingen dit toestaan, kan de gebruiker kiezen om andere methoden dan die standaard worden weer gegeven in te stellen. Nadat de wizard is voltooid, bekijken gebruikers de methoden die ze hebben ingesteld en de standaard methode voor Multi-Factor Authentication. Om het installatie proces te volt ooien, bevestigt de gebruiker de gegevens en gaat het Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Beveiligings gegevens van mijn profiel instellen

Een beheerder heeft de registratie niet afgedwongen.

Een gebruiker die de vereiste beveiligings gegevens nog niet heeft ingesteld, gaat [https://myprofile.microsoft.com](https://myprofile.microsoft.com)naar. De gebruiker selecteert **beveiligings gegevens** in het linkerdeel venster. Van daaruit kiest de gebruiker om een methode toe te voegen, selecteert een van de beschik bare methoden en volgt de stappen om die methode in te stellen. Wanneer u klaar bent, ziet de gebruiker de methode die zojuist is ingesteld op de pagina met beveiligings gegevens.

### <a name="delete-security-info-from-my-profile"></a>Beveiligings gegevens uit mijn profiel verwijderen

Een gebruiker die eerder ten minste één methode heeft ingesteld, gaat naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). De gebruiker kiest ervoor een van de eerder geregistreerde methoden te verwijderen. Als u klaar bent, ziet de gebruiker deze methode niet meer op de pagina beveiligings gegevens.

### <a name="change-the-default-method-from-my-profile"></a>De standaard methode van mijn profiel wijzigen

Een gebruiker die eerder ten minste één methode heeft ingesteld die kan worden gebruikt voor Multi-Factor Authentication naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). De gebruiker wijzigt de huidige standaard methode in een andere standaard methode. Wanneer u klaar bent, ziet de gebruiker de nieuwe standaard methode op de pagina beveiligings gegevens.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de zelf studies om [selfservice voor wachtwoord herstel in te scha kelen](tutorial-enable-sspr.md) en [Azure multi-factor Authentication in te scha kelen](tutorial-enable-azure-mfa.md)om aan de slag te gaan.

Meer informatie over het [inschakelen van gecombineerde registratie in uw Tenant of het](howto-registration-mfa-sspr-combined.md) [afdwingen van verificatie methoden door gebruikers te forceren](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

U kunt ook de [beschik bare methoden voor Azure multi-factor Authentication en SSPR](concept-authentication-methods.md)bekijken.
