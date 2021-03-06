---
title: Extensie-app in Azure Active Directory B2C | Microsoft Docs
description: De B2C-extensies-app worden hersteld.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 547b625996a65999c32c1b73699e3b408be01de3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188593"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: extensies-app

Wanneer een Azure AD B2C Directory wordt gemaakt, wordt er automatisch `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` een app met de naam gemaakt in de nieuwe map. Deze app, waarnaar wordt verwezen als de **B2C-Extensions-app**, is zichtbaar in *app-registraties*. Deze wordt door de Azure AD B2C-service gebruikt om informatie over gebruikers en aangepaste kenmerken op te slaan. Als de app wordt verwijderd, werkt Azure AD B2C niet naar behoren en wordt uw productie omgeving beïnvloed.

> [!IMPORTANT]
> Verwijder de B2C-uitbrei dingen-app alleen als u van plan bent om uw Tenant onmiddellijk te verwijderen. Als de app langer dan 30 dagen verwijderd blijft, zullen de gebruikers gegevens definitief verloren gaan.

## <a name="verifying-that-the-extensions-app-is-present"></a>Controleren of de App Extensions aanwezig is

Controleren of de B2C-extensies-app aanwezig is:

1. Klik in de Azure AD B2C-Tenant op **alle services** in het navigatie menu aan de linkerkant.
1. **App-registraties**zoeken en openen.
1. Zoeken naar een app die begint met **B2C-Extensions-app**

## <a name="recover-the-extensions-app"></a>De extensie-app herstellen

Als u de B2C-uitbrei dingen per ongeluk hebt verwijderd, hebt u 30 dagen om de app te herstellen. U kunt de app herstellen met behulp van de Graph API:

1. Blader naar [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Meld u aan bij de site als globale beheerder voor de Azure AD B2C directory waarvan u de verwijderde app wilt herstellen. Deze globale beheerder moet een e-mail adres hebben dat lijkt op `username@{yourTenant}.onmicrosoft.com`het volgende:.
1. Geef een HTTP-GET op voor `https://graph.windows.net/myorganization/deletedApplications` de URL met API-Version = 1.6. Met deze bewerking worden alle toepassingen weer geven die in de afgelopen 30 dagen zijn verwijderd.
1. Zoek de toepassing in de lijst waarin de naam begint met ' B2C-extension-app ' en kopieer de `objectid` eigenschaps waarde.
1. Geef een HTTP-POST op voor `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`de URL. Vervang het `{OBJECTID}` gedeelte van de URL door de `objectid` van de vorige stap.

U zou nu [de herstelde app kunnen zien](#verifying-that-the-extensions-app-is-present) in de Azure Portal.

> [!NOTE]
> Een toepassing kan alleen worden hersteld als deze in de afgelopen 30 dagen is verwijderd. Als er meer dan 30 dagen zijn, gaan de gegevens permanent verloren. Voor meer hulp moet u een ondersteunings ticket indienen.
