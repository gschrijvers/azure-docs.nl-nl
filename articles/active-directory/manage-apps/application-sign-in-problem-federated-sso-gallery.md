---
title: Problemen bij het aanmelden bij de galerij-app voor eenmalige aanmelding | Microsoft Docs
description: Richt lijnen voor de specifieke fouten bij het aanmelden bij een toepassing die u hebt geconfigureerd voor op SAML gebaseerde federatieve eenmalige aanmelding met Azure AD
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
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874d273e26a728afc0a1dc1a16852016797067ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77367899"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemen bij het aanmelden bij een galerie toepassing die is geconfigureerd voor federatieve eenmalige aanmelding

Als u de onderstaande aanmeldings problemen wilt oplossen, raden we u aan deze suggestie te volgen om betere diagnose te krijgen en de oplossings stappen te automatiseren:

- Installeer de [beveiligde browser uitbreiding mijn apps](access-panel-extension-problem-installing.md) om Azure Active Directory (Azure AD) te helpen bij het verbeteren van de test ervaring in de Azure Portal.
- Reproduceer de fout met behulp van de test ervaring op de pagina app-configuratie in de Azure Portal. Meer informatie over het [opsporen van op SAML gebaseerde toepassingen voor eenmalige aanmelding](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Kan de toepassing niet vinden in de map

*Fout AADSTS70001: de toepassing met de id https\/:/contoso.com is niet gevonden in de map*.

**Mogelijke oorzaak**

Het `Issuer` kenmerk dat vanuit de toepassing naar Azure AD wordt verzonden in de SAML-aanvraag komt niet overeen met de id-waarde die is geconfigureerd voor de toepassing in azure AD.

**Afsluiting**

Zorg ervoor dat `Issuer` het kenmerk in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in azure AD. Als u de [test ervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren.

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

1.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.

1.  Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

1.  Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

1.  Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

1.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

1.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of de waarde in het tekstvak id overeenkomt met de waarde voor de id-waarde die wordt weer gegeven in de fout.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Het antwoord adres komt niet overeen met de antwoord adressen die voor de toepassing zijn geconfigureerd

*Fout AADSTS50011: het antwoord adres ' https:\//contoso.com ' komt niet overeen met de antwoord adressen die zijn geconfigureerd voor de toepassing*

**Mogelijke oorzaak**

De `AssertionConsumerServiceURL` waarde in de SAML-aanvraag komt niet overeen met de antwoord-URL-waarde of het patroon dat is geconfigureerd in azure AD. De `AssertionConsumerServiceURL` waarde in de SAML-aanvraag is de URL die u in de fout ziet.

**Afsluiting**

Zorg ervoor dat `AssertionConsumerServiceURL` de waarde in de SAML-aanvraag overeenkomt met de antwoord-URL-waarde die is geconfigureerd in azure AD. Als u de [test ervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren.

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

1.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.

1.  Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

1.  Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

1.  Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

1.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

1.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of werk de waarde in het tekstvak antwoord-URL in op `AssertionConsumerServiceURL` overeenkomst met de waarde in de SAML-aanvraag.    
    
Nadat u de waarde voor de antwoord-URL in azure AD hebt bijgewerkt en deze overeenkomt met de waarde die door de toepassing in de SAML-aanvraag is verzonden, moet u zich kunnen aanmelden bij de toepassing.

## <a name="user-not-assigned-a-role"></a>Gebruiker heeft geen rol toegewezen

*Fout AADSTS50105: de aangemelde gebruiker ' brian\@contoso.com ' is niet toegewezen aan een rol voor de toepassing*.

**Mogelijke oorzaak**

De gebruiker heeft in Azure AD geen toegang gekregen tot de toepassing.

**Afsluiting**

Volg de onderstaande stappen om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing. Als u de [test ervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren.

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder**.

1.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.

1.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

1.  Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

1.  Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

1.  Selecteer in de lijst met toepassingen het account waaraan u een gebruiker wilt toewijzen.

1.  Zodra de toepassing is geladen, selecteert u **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

1.  Klik op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

1.  Selecteer de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

1. Typ in het zoekvak **Zoeken op naam of e-mailadres** de volledige naam of het e-mailadres van de gebruiker die u wilt toevoegen.

1. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast het profiel foto of logo van de gebruiker om de gebruiker aan de **geselecteerde** lijst toe te voegen.

1. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een volledige naam of e-mail adres in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om de gebruiker toe te voegen aan de **geselecteerde** lijst.

1. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

1. **Optioneel:** Klik op het selectie vakje **rol selecteren** in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

1. Klik op de knop **toewijzen** om de toepassing aan de geselecteerde gebruikers toe te wijzen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die worden beschreven in de sectie Beschrijving van oplossing.

## <a name="not-a-valid-saml-request"></a>Geen geldige SAML-aanvraag

*Fout AADSTS75005: de aanvraag is geen geldig Saml2-protocol bericht.*

**Mogelijke oorzaak**

Azure AD biedt geen ondersteuning voor de SAML-aanvraag die met de toepassing is verzonden voor eenmalige aanmelding. Enkele veelvoorkomende problemen zijn:

-   Ontbrekende vereiste velden in de SAML-aanvraag
-   Gecodeerde methode voor SAML-aanvraag

**Afsluiting**

1. Leg de SAML-aanvraag vast. Volg de zelf studie voor het [opsporen van op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md) voor meer informatie over het vastleggen van de SAML-aanvraag.

1. Neem contact op met de leverancier van de toepassing en verstrek de volgende gegevens:

   -   SAML-aanvraag

   -   [Vereisten voor het SAML-protocol voor eenmalige aanmelding voor Azure AD](../develop/single-sign-on-saml-protocol.md)

De leverancier van de toepassing moet valideren dat de Azure AD SAML-implementatie voor eenmalige aanmelding wordt ondersteund.

## <a name="misconfigured-application"></a>Onjuist geconfigureerde toepassing

*Fout AADSTS650056: onjuist geconfigureerde toepassing. Dit kan een van de volgende oorzaken hebben: de client heeft geen machtigingen vermeld in de aangevraagde machtigingen in de registratie van de client toepassing. Of de beheerder heeft geen toestemming gegeven in de Tenant. Of Controleer de toepassings-id in de aanvraag om er zeker van te zijn dat deze overeenkomt met de geconfigureerde client toepassings-id. Neem contact op met uw beheerder om de configuratie of instemming namens de Tenant op te lossen.*

**Mogelijke oorzaak**

Het `Issuer` kenmerk dat vanuit de toepassing naar Azure AD is verzonden in de SAML-aanvraag komt niet overeen met de id-waarde die is geconfigureerd voor de toepassing in azure AD.

**Afsluiting**

Zorg ervoor dat `Issuer` het kenmerk in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in azure AD. Als u de [test ervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

1.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.

1.  Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

1.  Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

1.  Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

1.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

1.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of de waarde in het tekstvak id overeenkomt met de waarde voor de id-waarde die wordt weer gegeven in de fout.


## <a name="certificate-or-key-not-configured"></a>Het certificaat of de sleutel is niet geconfigureerd

*Fout AADSTS50003: er is geen handtekening sleutel geconfigureerd.*

**Mogelijke oorzaak**

Het toepassings object is beschadigd en het certificaat dat is geconfigureerd voor de toepassing wordt niet herkend door Azure AD.

**Afsluiting**

Voer de volgende stappen uit om een nieuw certificaat te verwijderen en te maken:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

1. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

1. Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

1. Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

1. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

1. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren

1. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

1. Selecteer **Nieuw certificaat maken** onder de sectie **SAML-handtekening certificaat** .

1. Selecteer verval datum en klik vervolgens op **Opslaan**.

1. Schakel het selectie vakje **Nieuw certificaat actief maken** in om het actieve certificaat te onderdrukken. Klik vervolgens op **Opslaan** boven in het deelvenster en accepteer het rollovercertificaat om het te activeren.

1. Klik onder de sectie **SAML-handtekening certificaat** op **verwijderen** om het **ongebruikte** certificaat te verwijderen.

## <a name="saml-request-not-present-in-the-request"></a>SAML-aanvraag is niet aanwezig in de aanvraag

*Fout AADSTS750054: SAMLRequest of SAMLResponse moet aanwezig zijn als query reeks parameters in HTTP-aanvraag voor de binding van SAML-omleiding.*

**Mogelijke oorzaak**

Azure AD kan de SAML-aanvraag niet identificeren binnen de URL-para meters in de HTTP-aanvraag. Dit kan gebeuren als de toepassing geen gebruik maakt van een binding voor HTTP-omleiding bij het verzenden van de SAML-aanvraag naar Azure AD.

**Afsluiting**

De toepassing moet de SAML-aanvraag die is gecodeerd naar de locatie header verzenden met behulp van binding van HTTP-omleiding. Lees de sectie over binding voor HTTP-omleidingen in het [specificatiedocument over het SAML-protocol](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD stuurt het token naar een onjuist eind punt

**Mogelijke oorzaak**

Als de aanmeldings aanvraag bij eenmalige aanmelding geen expliciete antwoord-URL (assertion Consumer Service-URL) bevat, selecteert Azure AD een van de geconfigureerde vertrouwens-Url's voor die toepassing. Zelfs als de toepassing een expliciete antwoord-URL heeft geconfigureerd, kan de gebruiker worden omgeleid https://127.0.0.1:444. 

Toen de toepassing werd toegevoegd als niet-galerie-app, is deze antwoord-URL in Azure Active Directory gemaakt als standaardwaarde. Dit gedrag is gewijzigd en deze URL wordt niet meer standaard toegevoegd in Azure Active Directory. 

**Afsluiting**

Verwijder de ongebruikte antwoord-Url's die voor de toepassing zijn geconfigureerd.

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.

3.  Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

6.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Verwijder in de **antwoord-URL (assertion Consumer Service-URL)** ongebruikte of standaard antwoord-url's die zijn gemaakt door het systeem. Bijvoorbeeld `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probleem bij het aanpassen van de SAML-claims die worden verzonden naar een toepassing

Zie [claims toewijzen in azure Active Directory](../develop/active-directory-claims-mapping.md)voor meer informatie over het aanpassen van de SAML-kenmerk claims die naar uw toepassing worden verzonden.

## <a name="next-steps"></a>Volgende stappen

[Fout opsporing op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
