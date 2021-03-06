---
title: Toegang tot Self-service toepassingen gebruiken | Microsoft Docs
description: De toegang van self-service toepassingen inschakelen om gebruikers toe te staan hun eigen toepassingen te vinden
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55da8731855c8afda496edff33f3fbb7982cd44b
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "65784541"
---
# <a name="how-to-use-self-service-application-access"></a>Toegang tot Self-service toepassingen gebruiken

Voordat uw gebruikers toepassingen zelf kunnen detecteren vanuit hun toegangs venster, moet u de toegang van **selfservice toepassingen** inschakelen voor alle toepassingen waarvoor u gebruikers de mogelijkheid wilt bieden om zichzelf te detecteren en toegang tot te vragen.

Deze functie is een uitstekende manier om tijd en geld te besparen als een IT-groep en wordt ten zeerste aanbevolen als onderdeel van een moderne implementatie van toepassingen met Azure Active Directory.

Met deze functie kunt u het volgende doen:

-   Laat gebruikers zelf toepassingen detecteren vanuit het [toegangs venster](https://myapps.microsoft.com/) van de toepassing zonder dat ze de IT-groep hoeven te gebruiken.

-   Voeg deze gebruikers toe aan een vooraf geconfigureerde groep, zodat u kunt zien wie toegang heeft aangevraagd, de toegang moet verwijderen en de functies die eraan zijn toegewezen, moeten beheren.

-   Een zakelijke goed keurder toestaan om toegangs aanvragen voor toepassingen goed te keuren zodat de IT-groep deze niet hoeft te gebruiken.

-   U kunt Maxi maal 10 personen configureren die de toegang tot deze toepassing goed keuren.

-   Zo kunt u een zakelijke goed keurder toestaan om de wacht woorden in te stellen die gebruikers kunnen gebruiken om zich aan te melden bij de toepassing, rechtstreeks vanuit het [toepassings toegangs paneel](https://myapps.microsoft.com/)van de bedrijfs fiatteur.

-   Automatisch toegewezen Self-Service gebruikers aan een toepassingsrol rechtstreeks toewijzen.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>De toegang van self-service toepassingen inschakelen om gebruikers toe te staan hun eigen toepassingen te vinden

Toegang voor selfservice toepassingen is een uitstekende manier om gebruikers de mogelijkheid te bieden om zelf toepassingen te detecteren, zodat de bedrijfs groep de toegang tot deze toepassingen kan goed keuren. U kunt de bedrijfs groep toestaan om de referenties te beheren die aan deze gebruikers zijn toegewezen voor wacht woord voor eenmalige aanmelding voor toepassingen, direct vanaf hun toegangs Vensters.

Volg de onderstaande stappen om de toegang van selfservice toepassingen voor een toepassing in te scha kelen:

1. Open [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** bovenin het navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory links.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u selfservice toegang wilt inschakelen in de lijst.

7. Zodra de toepassing is geladen, klikt u op **self-service** in het navigatie menu aan de linkerkant van de toepassing.

8. Als u toegang voor selfservice toepassingen voor deze toepassing wilt inschakelen, schakelt u de optie **gebruikers toestaan om toegang te vragen tot deze toepassing?** in op **Ja.**

9. Vervolgens selecteert u de groep waartoe gebruikers die toegang tot deze toepassing vragen moeten worden toegevoegd, klikt u op de selector naast het label **waaraan gebruikers moeten worden toegevoegd** , en selecteert u een groep.

10. **Optioneel:** Als u een zakelijke goed keuring wilt vereisen voordat gebruikers toegang worden verleend, **stelt u de**optie **goed keuring vereisen alvorens toegang tot deze toepassing verlenen** in.

11. **Optioneel: voor toepassingen die alleen gebruikmaken van wacht woord voor eenmalige aanmelding,** als u wilt toestaan dat de goed keurders van deze gebruiker de wacht woorden opgeven die naar deze toepassing worden verzonden voor goedgekeurde gebruikers, stelt u de **goed keurders toestaan om gebruikers wachtwoorden in te stellen voor deze toepassing?** **in-of uitschakelen.**

12. **Optioneel:** Als u de zakelijke goed keurders wilt opgeven die de toegang tot deze toepassing mogen goed keuren, klikt u op de kiezer naast het label **dat de toegang tot deze toepassing mag goed keuren?** om Maxi maal 10 afzonderlijke goed keurders voor bedrijven te selecteren.

    * Groepen worden niet ondersteund.

13. **Optioneel:** **voor toepassingen die rollen**weer geven, kunt u, als u goedgekeurde selfservice gebruikers wilt toewijzen aan een rol, klikken op de selector naast de **rol waaraan gebruikers moeten worden toegewezen in deze toepassing?** om de rol te selecteren waaraan deze gebruikers moeten worden toegewezen.

14. Klik boven aan de Blade op de knop **Opslaan** om te volt ooien.

Zodra u de configuratie van de selfservice toepassing hebt voltooid, kunnen gebruikers naar hun [toepassings toegangs paneel](https://myapps.microsoft.com/) navigeren en op de knop **toevoegen** klikken om de apps te vinden waarvoor u selfservice toegang hebt ingeschakeld. Zakelijke goed keurders zien ook een melding in het [toegangs venster](https://myapps.microsoft.com/)van hun toepassing. U kunt een e-mail bericht ontvangen wanneer een gebruiker toegang tot een toepassing heeft aangevraagd waarvoor de goed keuring is vereist. 

Deze goed keuringen ondersteunen alleen één goedkeurings werk stromen, wat betekent dat als u meerdere goed keurders opgeeft, één fiatteur de toegang tot de toepassing kan goed keuren.

## <a name="next-steps"></a>Volgende stappen
[Azure Active Directory instellen voor groepsbeheer met self-service](../users-groups-roles/groups-self-service-management.md)
