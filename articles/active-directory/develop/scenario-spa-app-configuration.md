---
title: Een app met één pagina configureren-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een toepassing met één pagina (de code configuratie van de app)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882332"
---
# <a name="single-page-application-code-configuration"></a>Toepassing met één pagina: code configuratie

Meer informatie over het configureren van de code voor een toepassing met één pagina (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>MSAL-bibliotheken die ondersteuning bieden voor impliciete stroom

Het micro soft Identity-platform biedt de volgende MSAL-bibliotheken (micro soft Authentication Library) ter ondersteuning van impliciete stroom door gebruik te maken van door de branche aanbevolen beveiligings procedures:

| MSAL-bibliotheek | Beschrijving |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Een gewone Java script-bibliotheek voor gebruik in een webtoepassing aan de client zijde die is gebouwd via Java script-of SPA-frameworks, zoals hoek, vue. js en reageren. js. |
| ![MSAL hoek](media/sample-v2-code/logo_angular.png) <br/> [MSAL hoek](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper van de core MSAL. JS-bibliotheek om gebruik te vereenvoudigen in apps met één pagina die zijn gebouwd met behulp van het hoek kader. |

## <a name="application-code-configuration"></a>Configuratie van de toepassings code

In een MSAL-bibliotheek worden de registratie gegevens van de toepassing door gegeven als configuratie tijdens de initialisatie van de bibliotheek.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Zie voor meer informatie over de Configureer bare opties [toepassing initialiseren met MSAL. js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](scenario-spa-sign-in.md)
