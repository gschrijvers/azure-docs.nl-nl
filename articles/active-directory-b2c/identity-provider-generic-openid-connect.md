---
title: Registratie instellen en aanmelden met OpenID Connect Connect
titleSuffix: Azure AD B2C
description: Stel registratie in en meld u aan met een OpenID Connect Connect-ID-provider (IdP) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188253"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met OpenID Connect Connect met behulp van Azure Active Directory B2C

[OpenID Connect Connect](openid-connect.md) is een verificatie protocol dat is gebaseerd op OAuth 2,0 en dat kan worden gebruikt voor het aanmelden van beveiligde gebruikers. De meeste id-providers die gebruikmaken van dit protocol worden ondersteund in Azure AD B2C. In dit artikel wordt uitgelegd hoe u aangepaste OpenID Connect Connect-id-providers kunt toevoegen aan uw gebruikers stromen.

## <a name="add-the-identity-provider"></a>De ID-provider toevoegen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C Tenant bevat door te klikken op het filter **Directory + abonnement** in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **nieuwe OpenID Connect Connect-provider**.

## <a name="configure-the-identity-provider"></a>De ID-provider configureren

Elke OpenID Connect Connect-ID-provider beschrijft een meta gegevens document met de meeste informatie die nodig is om de aanmelding uit te voeren. Dit omvat informatie zoals de Url's die moeten worden gebruikt en de locatie van de open bare handtekeningen sleutels van de service. Het OpenID Connect Connect meta data-document bevindt zich altijd op een `.well-known\openid-configuration`eind punt dat eindigt op. Voer de meta gegevens-URL in voor de OpenID Connect Connect-ID-provider die u wilt toevoegen.

## <a name="client-id-and-secret"></a>Client-ID en geheim

Om gebruikers toe te staan zich aan te melden, vereist de ID-provider ontwikkel aars om een toepassing in hun service te registreren. Deze toepassing heeft een ID die de **client-id** en een **client geheim**wordt genoemd. Kopieer deze waarden van de ID-provider en geef ze op in de bijbehorende velden.

> [!NOTE]
> Het client geheim is optioneel. U moet echter een client geheim invoeren als u de [autorisatie code stroom](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)wilt gebruiken, die het geheim gebruikt voor het uitwisselen van de code voor het token.

## <a name="scope"></a>Bereik

Met het bereik worden de informatie en machtigingen gedefinieerd die u wilt verzamelen uit uw aangepaste ID-provider. OpenID Connect Connect-aanvragen moeten de `openid` bereik waarde bevatten om het id-token van de ID-provider te ontvangen. Zonder het ID-token kunnen gebruikers zich niet aanmelden bij Azure AD B2C met behulp van de aangepaste ID-provider. Andere bereiken kunnen worden toegevoegd, gescheiden door een spatie. Raadpleeg de documentatie van de aangepaste ID-provider om te zien welke andere bereiken mogelijk beschikbaar zijn.

## <a name="response-type"></a>Antwoord type

Het antwoord type beschrijft wat voor soort informatie wordt weer gegeven in de eerste aanroep `authorization_endpoint` van de aangepaste ID-provider. De volgende antwoord typen kunnen worden gebruikt:

* `code`: Volgens de [autorisatie code stroom](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)wordt een code geretourneerd naar Azure AD B2C. Azure AD B2C opbrengsten om de code `token_endpoint` aan te roepen voor het token.
* `id_token`: Er is een ID-token geretourneerd naar Azure AD B2C van de aangepaste ID-provider.

## <a name="response-mode"></a>Antwoord modus

De antwoord modus definieert de methode die moet worden gebruikt om de gegevens terug van de aangepaste ID-provider naar Azure AD B2C te verzenden. De volgende antwoord modi kunnen worden gebruikt:

* `form_post`: Deze antwoord modus wordt aanbevolen voor de beste beveiliging. Het antwoord wordt verzonden via de HTTP `POST` -methode, waarbij de code of het token wordt gecodeerd in de hoofd `application/x-www-form-urlencoded` tekst met de-indeling.
* `query`: De code of het token wordt geretourneerd als een query parameter.

## <a name="domain-hint"></a>Domein Hint

De domein hint kan worden gebruikt om rechtstreeks over te slaan op de aanmeldings pagina van de opgegeven id-provider, in plaats van dat de gebruiker een selectie maakt tussen de lijst met beschik bare id-providers. Als u dit gedrag wilt toestaan, voert u een waarde in voor de domein hint. Als u naar de aangepaste ID-provider wilt gaan, `domain_hint=<domain hint value>` voegt u de para meter toe aan het einde van uw aanvraag wanneer u Azure AD B2C aanroept om u aan te melden.

## <a name="claims-mapping"></a>Claim toewijzing

Wanneer de aangepaste ID-provider een ID-token terugstuurt naar Azure AD B2C, moet Azure AD B2C de claims van het ontvangen token kunnen toewijzen aan de claims die Azure AD B2C herkent en gebruikt. Raadpleeg de documentatie van de Custom ID-provider voor elk van de volgende toewijzingen om inzicht te krijgen in de claims die terug worden geretourneerd in de tokens van de identiteits provider:

* **Gebruikers-id**: Geef de claim op die de *unieke id* voor de aangemelde gebruiker levert.
* **Weergave naam**: Geef de claim op die de *weergave naam* of de *volledige naam* voor de gebruiker levert.
* Voor **naam**: Voer de claim in die de *voor naam* van de gebruiker levert.
* **Naam**: Voer de claim in die de *Achternaam* van de gebruiker levert.
* **E-mail**: Voer de claim in die het *e-mail adres* van de gebruiker levert.
