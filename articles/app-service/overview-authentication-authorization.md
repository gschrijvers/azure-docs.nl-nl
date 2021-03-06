---
title: Verificatie en autorisatie
description: Meer informatie over de ingebouwde ondersteuning voor verificatie en autorisatie in Azure App Service en Azure Functions en hoe deze uw app kan beveiligen tegen onbevoegde toegang.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 04/15/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: f51a396e997a9e6392f3e86a6f77e581753d6ada
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196438"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Verificatie en autorisatie in Azure App Service en Azure Functions

> [!NOTE]
> Op dit moment biedt ASP.NET Core momenteel geen ondersteuning voor het vullen van de huidige gebruiker met de functie voor verificatie/autorisatie.
>

Azure App Service biedt ingebouwde ondersteuning voor verificatie en autorisatie, zodat u gebruikers kunt aanmelden en toegang hebt tot gegevens door minimale of geen code te schrijven in uw web-app, de REST-API en de mobiele back-end en ook [Azure functions](../azure-functions/functions-overview.md). In dit artikel wordt beschreven hoe App Service de verificatie en autorisatie voor uw app vereenvoudigt.

Voor beveiligde verificatie en autorisatie is grondige inzichten van beveiliging vereist, waaronder Federatie, versleuteling, [JSON-webtokens (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) -beheer, [toekennings typen](https://oauth.net/2/grant-types/), enzovoort. App Service biedt deze hulpprogram ma's, zodat u meer tijd en energie kunt best Eden aan het leveren van bedrijfs waarde aan uw klant.

> [!IMPORTANT]
> U hoeft deze functie niet te gebruiken voor verificatie en autorisatie. U kunt gebruikmaken van de gebundelde beveiligings functies in uw eigen keuze of u kunt uw eigen hulpprogram ma's schrijven. Het is echter belang rijk dat [Chrome 80 een belang rijke wijziging aanbrengt in de implementatie van SameSite voor cookies](https://www.chromestatus.com/feature/5088147346030592) (release date rond maart 2020), en aangepaste externe verificatie of andere scenario's die afhankelijk zijn van het boeken van een cross-site cookie, kunnen afbreken wanneer client-Chrome-browsers worden bijgewerkt. De tijdelijke oplossing is complex omdat deze moet ondersteuning bieden voor verschillende SameSite-gedragingen voor verschillende browsers. 
>
> De versies van de ASP.NET Core 2,1 en hoger die door App Service worden gehost, worden al bijgewerkt voor deze breuk wijziging en verwerkten gechrome 80 en oudere browsers op de juiste manier. Daarnaast wordt dezelfde patch voor ASP.NET Framework 4.7.2 geïmplementeerd in het App Service-exemplaar gedurende januari 2020. Zie [Azure app service SameSite cookie update](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/)(Engelstalig) voor meer informatie over hoe u weet of uw app de patch heeft ontvangen.
>

Zie [gebruikers verificatie en autorisatie voor mobiele apps met Azure app service](../app-service-mobile/app-service-mobile-auth.md)voor meer informatie over systeem eigen mobiele apps.

## <a name="how-it-works"></a>Hoe het werkt

De module authenticatie en autorisatie wordt uitgevoerd in dezelfde sandbox als de code van uw toepassing. Wanneer deze is ingeschakeld, stuurt elke binnenkomende HTTP-aanvraag deze door voordat deze wordt verwerkt door de code van uw toepassing.

![](media/app-service-authentication-overview/architecture.png)

Deze module verwerkt diverse dingen voor uw app:

- Verifieert gebruikers met de opgegeven provider
- Valideert, slaat en vernieuwt tokens
- Beheert de geverifieerde sessie
- Injecteert identiteits gegevens in aanvraag headers

De module wordt afzonderlijk van uw toepassings code uitgevoerd en wordt geconfigureerd met behulp van app-instellingen. Er zijn geen Sdk's, specifieke talen of wijzigingen in de toepassings code vereist. 

### <a name="userapplication-claims"></a>Gebruikers/toepassings claims

Voor alle taal Frameworks maakt App Service de claims in het inkomende token (of dit van een geverifieerde eind gebruiker of een client toepassing afkomstig is) die beschikbaar zijn voor uw code door ze in de aanvraag headers in te voeren. Voor ASP.NET 4,6-apps vult App Service [claimsprincipal is. current](/dotnet/api/system.security.claims.claimsprincipal.current) in met de claims van de geverifieerde gebruiker, zodat u het standaard .net-code patroon kunt volgen, inclusief het `[Authorize]` kenmerk. Op dezelfde manier wordt voor PHP-apps App Service de `_SERVER['REMOTE_USER']` variabele ingevuld. Voor java-apps zijn de claims [toegankelijk via de Tomcat-servlet](containers/configure-language-java.md#authenticate-users-easy-auth).

Voor [Azure functions](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` is niet ingevuld voor .net-code, maar u kunt wel de gebruikers claims in de aanvraag headers vinden of het `ClaimsPrincipal` object ophalen uit de context van de aanvraag of zelfs via een bindings parameter. Zie [werken met client identiteiten](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) voor meer informatie.

Zie voor meer informatie [gebruikers claims voor toegang](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Tokenarchief

App Service biedt een ingebouwde token opslag. Dit is een opslag plaats van tokens die zijn gekoppeld aan de gebruikers van uw web-apps, Api's of systeem eigen mobiele apps. Wanneer u verificatie met een wille keurige provider inschakelt, is deze token opslag direct beschikbaar voor uw app. Als uw toepassings code toegang moet hebben tot gegevens van deze providers namens de gebruiker, zoals: 

- plaatsen op de Facebook-tijd lijn van de geverifieerde gebruiker
- de zakelijke gegevens van de gebruiker lezen met behulp van de Microsoft Graph-API

Normaal gesp roken moet u code schrijven om deze tokens in uw toepassing te verzamelen, op te slaan en te vernieuwen. Met de token opslag haalt u alleen [de tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) op wanneer u deze nodig hebt en [weet u app service deze te vernieuwen](app-service-authentication-how-to.md#refresh-identity-provider-tokens) wanneer ze ongeldig worden. 

De id-tokens, toegangs tokens en vernieuwings tokens worden in de cache opgeslagen voor de geauthenticeerde sessie en ze zijn alleen toegankelijk voor de bijbehorende gebruiker.  

Als u niet met tokens in uw app hoeft te werken, kunt u de token opslag uitschakelen.

### <a name="logging-and-tracing"></a>Logboek registratie en tracering

Als u [toepassings logboeken inschakelt](troubleshoot-diagnostic-logs.md), worden de verificatie-en autorisatie traceringen rechtstreeks in uw logboek bestanden weer geven. Als er een verificatie fout wordt weer geven die niet werd verwacht, kunt u alle gegevens gemakkelijk vinden door te kijken in uw bestaande toepassings Logboeken. Als u [tracering van mislukte aanvragen](troubleshoot-diagnostic-logs.md)inschakelt, kunt u precies zien welke rol de authenticatie-en autorisatie module mogelijk heeft afgespeeld in een mislukte aanvraag. Zoek in de traceer logboeken naar een module met de naam `EasyAuthModule_32/64` . 

## <a name="identity-providers"></a>Id-providers

App Service maakt gebruik van [federatieve identiteiten](https://en.wikipedia.org/wiki/Federated_identity), waarin een id-provider van derden de gebruikers identiteiten en de verificatie stroom voor u beheert. Er zijn standaard vijf id-providers beschikbaar: 

| Provider | Aanmeldings eindpunt |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-account](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Wanneer u verificatie en autorisatie met een van deze providers inschakelt, is het aanmeldings eindpunt beschikbaar voor gebruikers verificatie en voor validatie van verificatie tokens van de provider. U kunt uw gebruikers een onbeperkt aantal van deze aanmeldings opties voorzien. U kunt ook een andere ID-provider of [uw eigen aangepaste identiteits oplossing][custom-auth]integreren.

## <a name="authentication-flow"></a>Verificatiestroom

De verificatie stroom is hetzelfde voor alle providers, maar verschilt afhankelijk van of u zich wilt aanmelden met de SDK van de provider:

- Zonder provider-SDK: de toepassing delegeert federatieve aanmelding bij App Service. Dit is meestal het geval bij browser-apps, waarmee de aanmeldings pagina van de provider kan worden weer gegeven aan de gebruiker. De server code beheert het aanmeldings proces, zodat dit ook wel _Server gerichte stroom_ of _Server stroom_wordt genoemd. Dit geldt voor browser-apps. Dit geldt ook voor systeem eigen apps waarmee gebruikers worden ondertekend met de Mobile Apps client-SDK, omdat de SDK een webweergave opent om gebruikers met App Service-verificatie te ondertekenen. 
- Met de provider-SDK: de toepassing ondertekent gebruikers hand matig aan de provider en stuurt het verificatie token naar App Service voor validatie. Dit is meestal het geval bij apps zonder browser, waarmee de aanmeldings pagina van de provider niet kan worden weer gegeven aan de gebruiker. De toepassings code beheert het aanmeldings proces, dus ook wel _client gerichte stroom_ of _client stroom_. Dit is van toepassing op REST Api's, [Azure functions](../azure-functions/functions-overview.md)en Java script-clients, evenals browser-apps die meer flexibiliteit in het aanmeldings proces hebben. Dit geldt ook voor systeem eigen mobiele apps waarmee gebruikers worden aangemeld met de SDK van de provider.

> [!NOTE]
> Aanroepen van een vertrouwde browser-app in App Service naar een andere REST API in App Service of [Azure functions](../azure-functions/functions-overview.md) kan worden geverifieerd met behulp van de server gerichte stroom. Zie voor meer informatie [verificatie en autorisatie aanpassen in app service](app-service-authentication-how-to.md).
>

In de volgende tabel ziet u de stappen van de verificatie stroom.

| Stap | Zonder provider-SDK | Met provider-SDK |
| - | - | - |
| 1. gebruiker ondertekenen in | Stuurt de client door naar `/.auth/login/<provider>` . | Client code tekent de gebruiker rechtstreeks met de SDK van de provider en ontvangt een verificatie token. Zie de documentatie van de provider voor meer informatie. |
| 2. na verificatie | Provider stuurt de client om naar `/.auth/login/<provider>/callback` . | Client code [boekt token van provider](app-service-authentication-how-to.md#validate-tokens-from-providers) naar `/.auth/login/<provider>` voor validatie. |
| 3. een geverifieerde sessie tot stand brengen | App Service voegt een geverifieerde cookie toe aan het antwoord. | App Service retourneert een eigen verificatie token naar client code. |
| 4. geauthenticeerde inhoud verwerken | Client bevat verificatie cookie in volgende aanvragen (automatisch verwerkt door browser). | Client code geeft een verificatie token weer in de `X-ZUMO-AUTH` header (automatisch verwerkt door Mobile apps client-sdk's). |

App Service kunt voor client browsers automatisch alle niet-geverifieerde gebruikers door sturen naar `/.auth/login/<provider>` . U kunt gebruikers ook voorzien van een of meer `/.auth/login/<provider>` koppelingen om u aan te melden bij uw app met behulp van de gewenste provider.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Autorisatie gedrag

In de [Azure Portal](https://portal.azure.com)kunt u app service-autorisatie configureren met een aantal gedragingen wanneer een binnenkomende aanvraag niet is geverifieerd.

![](media/app-service-authentication-overview/authorization-flow.png)

In de volgende koppen worden de opties beschreven.

### <a name="allow-anonymous-requests-no-action"></a>Anonieme aanvragen toestaan (geen actie)

Met deze optie wordt de autorisatie van niet-geverifieerde verkeer naar uw toepassings code uitgesteld. App Service worden voor geverifieerde aanvragen ook de verificatie gegevens in de HTTP-headers door gegeven. 

Deze optie biedt meer flexibiliteit bij het verwerken van anonieme aanvragen. U kunt bijvoorbeeld [meerdere aanmeldings providers](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aan uw gebruikers aanbieden. U moet echter code schrijven. 

### <a name="allow-only-authenticated-requests"></a>Alleen geverifieerde aanvragen toestaan

De optie is **Aanmelden met \< provider>**. App Service stuurt alle anonieme aanvragen door naar `/.auth/login/<provider>` de provider die u kiest. Als de anonieme aanvraag afkomstig is van een systeem eigen mobiele app, is het geretourneerde antwoord een `HTTP 401 Unauthorized` .

Met deze optie hoeft u geen verificatie code in uw app te schrijven. Nauw keurige autorisatie, zoals Role-specifieke autorisatie, kan worden verwerkt door de claims van de gebruiker te controleren (Zie [gebruikers claims voor toegang](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina.

> [!NOTE]
> Verificatie/autorisatie was voorheen bekend als Easy auth.
>

## <a name="more-resources"></a>Meer bronnen

[Zelf studie: gebruikers end-to-end verifiëren en autoriseren in Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Zelf studie: gebruikers end-to-end verifiëren en autoriseren in Azure App Service voor Linux](containers/tutorial-auth-aad.md)  
[Verificatie en autorisatie aanpassen in app service](app-service-authentication-how-to.md) 
 [.Net core-integratie van Azure AppService EasyAuth (derde partij)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) 
 [Azure app service-verificatie voor het werken met .net core (derden)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Hand leidingen voor een specifieke provider:

* [Uw app configureren voor aanmelding met Azure Active Directory][AAD]
* [Uw app configureren voor aanmelding met Facebook][Facebook]
* [Uw app configureren voor aanmelding met Google][Google]
* [Uw app configureren voor aanmelding met een Microsoft Account][MSA]
* [Uw app configureren voor aanmelding met Twitter][Twitter]
* [Procedure: aangepaste verificatie gebruiken voor uw toepassing][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
