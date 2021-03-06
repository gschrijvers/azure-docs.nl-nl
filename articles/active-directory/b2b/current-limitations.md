---
title: Beperkingen van B2B-samen werking-Azure Active Directory | Microsoft Docs
description: Huidige beperkingen voor Azure Active Directory B2B-samen werking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263359"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Beperkingen van Azure AD B2B-samen werking
Azure Active Directory (Azure AD) B2B-samen werking is momenteel onderhevig aan de beperkingen die in dit artikel worden beschreven.

## <a name="possible-double-multi-factor-authentication"></a>Mogelijke dubbele multi-factor Authentication
Met Azure AD B2B kunt u multi-factor Authentication afdwingen voor de resource organisatie (de uitnodigende organisatie). De redenen voor deze benadering worden beschreven in [voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md). Als een partner al multi-factor Authentication heeft ingesteld en afgedwongen, moeten hun gebruikers de verificatie mogelijk één keer uitvoeren in hun eigen organisatie en vervolgens opnieuw in uw bedrijf.

## <a name="instant-on"></a>Direct aan
In de B2B-samenwerkings stromen voegen we gebruikers toe aan de Directory en kunnen ze deze dynamisch bijwerken tijdens de inwisseling van de uitnodiging, de toewijzing van apps, enzovoort. De updates en schrijf bewerkingen worden gewoonlijk uitgevoerd in één directory-exemplaar en moeten worden gerepliceerd voor alle exemplaren. De replicatie is voltooid zodra alle exemplaren zijn bijgewerkt. Soms kunnen replicatie latenties optreden wanneer het object wordt geschreven of bijgewerkt in één exemplaar en de aanroep voor het ophalen van dit object naar een ander exemplaar. Als dat gebeurt, moet u vernieuwen of opnieuw proberen om te helpen. Als u een app met behulp van onze API schrijft, probeert u een goede, verdedigings praktijk om dit probleem op te lossen.

## <a name="azure-ad-directories"></a>Azure AD-directory's
Azure AD B2B is onderhevig aan Azure AD-service Directory limieten. Zie [Azure AD-service limieten en-beperkingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)voor meer informatie over het aantal directory's dat een gebruiker kan maken en het aantal directory's waarmee een gebruiker of gast gebruiker kan behoren.

## <a name="national-clouds"></a>Nationale Clouds
[Nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) zijn fysiek geïsoleerde exemplaren van Azure. B2B-samen werking wordt niet ondersteund in nationale Cloud grenzen. Als uw Azure-Tenant zich bijvoorbeeld in de open bare, globale Cloud bevindt, kunt u geen gebruiker uitnodigen waarvan het account zich in een nationale Cloud bevindt. Als u wilt samen werken met de gebruiker, vraagt u deze voor een ander e-mail adres of maakt u een gebruikers account voor de gebruiker in uw Directory.

## <a name="azure-us-government-clouds"></a>Azure Amerikaanse overheids Clouds
Binnen de Azure-Cloud voor de Amerikaanse overheid wordt B2B-samen werking momenteel alleen ondersteund tussen tenants die zich in de cloud van Azure Amerikaanse overheid bevinden en die allebei ondersteuning bieden voor B2B-samen werking. Als u een gebruiker uitnodigt in een Tenant die geen deel uitmaakt van de Azure-Cloud voor de Amerikaanse overheid of die geen ondersteuning biedt voor B2B-samen werking, mislukt de uitnodiging of kan de gebruiker de uitnodiging niet inwisselen. Zie [Azure Active Directory Premium P1-en P2-variaties](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)voor meer informatie over andere beperkingen.

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Hoe kan ik zien of B2B-samen werking beschikbaar is in mijn Azure US Government-Tenant?
Ga als volgt te werk om erachter te komen of uw Azure VS government Cloud Tenant ondersteuning biedt voor B2B-samen werking:

1. Ga in een browser naar de volgende URL en vervang uw Tenant naam voor * &lt;Tenant&gt;*:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Zoeken `"tenant_region_scope"` in het JSON-antwoord:

   - Als `"tenant_region_scope":"USGOV”` dit wordt weer gegeven, wordt B2B ondersteund.
   - Als `"tenant_region_scope":"USG"` dit wordt weer gegeven, wordt B2B niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Uitnodigingen voor B2B-samen werking overdragen](delegate-invitations.md)

