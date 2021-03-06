---
title: Power shell-voor beelden voor Azure AD-toepassingsproxy
description: Gebruik deze Power shell-voor beelden voor Azure AD-toepassingsproxy om informatie te krijgen over toepassings proxy-apps en-connectors in uw directory, gebruikers en groepen toe te wijzen aan apps en certificaat gegevens op te halen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481259"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD Power shell-voor beelden voor Azure AD-toepassingsproxy

De volgende tabel bevat koppelingen naar Power shell-script voorbeelden voor Azure AD-toepassingsproxy. Voor deze voor beelden is de preview-versie [van de AzureAD v2 Power shell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) of de [AzureAD v2 Power shell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)vereist, tenzij anders vermeld.


Zie toepassings [proxy toepassings beheer](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) en [Application proxy-connector beheer](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)voor meer informatie over de cmdlets die worden gebruikt in deze voor beelden.

| | |
|---|---|
|**Toepassings proxy-apps**||
| [Basis informatie voor alle apps van de toepassings proxy weer geven](scripts/powershell-get-all-app-proxy-apps-basic.md) | Een lijst met basis informatie (AppId, DisplayName, object) over alle toepassings proxy-apps in uw Directory. |
| [Uitgebreide informatie weer geven voor alle toepassings proxy-apps](scripts/powershell-get-all-app-proxy-apps-extended.md) | Een lijst met uitgebreide informatie (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) over alle toepassings proxy-apps in uw Directory.  |
| [Alle toepassings proxy-apps per connector groep weer geven](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Bevat informatie over alle toepassings proxy-apps in uw directory en aan welke connector groepen de apps worden toegewezen. |
| [Alle toepassings proxy-apps met een levens duur beleid van een Token ophalen](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Een lijst met alle apps van de toepassings proxy in uw directory met een levens duur voor tokens en de bijbehorende details. Voor dit voor beeld is de [Preview-versie van de AzureAD v2 Power shell voor Graph-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)vereist. |
|**Connectorgroepen**||
| [Alle connector groepen en connectors in de Directory ophalen](scripts/powershell-get-all-connectors.md) | Een lijst met alle connector groepen en connectors in uw Directory. |
| [Alle apps die zijn toegewezen aan een connector groep verplaatsen naar een andere connector groep](scripts/powershell-move-all-apps-to-connector-group.md) | Hiermee verplaatst u alle toepassingen die momenteel aan een connector groep zijn toegewezen, naar een andere connector groep. |
|**Gebruikers en groepen die zijn toegewezen**||
| [Gebruikers en groepen weer geven die zijn toegewezen aan een toepassings proxy toepassing](scripts/powershell-display-users-group-of-app.md) | Een lijst met de gebruikers en groepen die zijn toegewezen aan een specifieke toepassings proxy toepassing. |
| [Een gebruiker toewijzen aan een toepassing](scripts/powershell-assign-user-to-app.md) | Hiermee wijst u een specifieke gebruiker toe aan een toepassing. |
| [Een groep toewijzen aan een toepassing](scripts/powershell-assign-group-to-app.md) | Hiermee wijst u een specifieke groep toe aan een toepassing. |
|**Configuratie van externe URL**||
| [Alle toepassings proxy-apps ophalen met behulp van standaard domeinen (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Een lijst met alle toepassings proxy toepassingen die gebruikmaken van standaard domeinen (. msappproxy.net). |
| [Alle toepassings proxy-apps ophalen met behulp van joker tekens publiceren](scripts/powershell-get-all-wildcard-apps.md) | Een lijst met alle toepassings proxy-apps met behulp van joker tekens publiceren. |
|**Aangepaste domein configuratie**||
| [Alle toepassings proxy-apps ophalen met aangepaste domeinen en certificaat gegevens](scripts/powershell-get-all-custom-domains-and-certs.md) | Een lijst met alle apps voor toepassings proxy die gebruikmaken van aangepaste domeinen en de certificaat gegevens die aan de aangepaste domeinen zijn gekoppeld. |
| [Alle apps uit de Azure AD-proxy toepassing publiceren zonder dat het certificaat is geüpload](scripts/powershell-get-all-custom-domain-no-cert.md) | Een lijst met alle apps voor toepassings proxy die gebruikmaken van aangepaste domeinen, maar waarvoor geen geldig TLS/SSL-certificaat is geüpload. |
| [Alle Azure AD-proxy toepassings-apps die zijn gepubliceerd met het identieke certificaat ophalen](scripts/powershell-get-custom-domain-identical-cert.md) | Een lijst met alle Azure AD-proxy toepassings-apps die zijn gepubliceerd met het identieke certificaat. |
| [Alle Azure AD-proxy toepassings-apps die zijn gepubliceerd met het identieke certificaat ophalen en vervangen](scripts/powershell-get-custom-domain-replace-cert.md) | Voor Azure AD-proxy toepassings-apps die met een identiek certificaat worden gepubliceerd, kunt u het certificaat in bulk vervangen. |
