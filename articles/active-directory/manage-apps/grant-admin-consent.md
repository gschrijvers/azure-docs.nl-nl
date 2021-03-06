---
title: Beheerder van de hele Tenant toestemming geven voor een toepassing-Azure AD
description: Meer informatie over het verlenen van toestemming op Tenant niveau voor een toepassing, zodat eind gebruikers niet om toestemming wordt gevraagd wanneer ze zich aanmelden bij een toepassing.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75480916"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Toestemming van de beheerder voor de hele Tenant verlenen aan een toepassing

Meer informatie over hoe u de gebruikers ervaring kunt vereenvoudigen door de beheerder van de hele Tenant toestemming te verlenen voor een toepassing. In dit artikel worden de verschillende manieren beschreven waarop u dit kunt doen. De methoden zijn van toepassing op alle eind gebruikers in uw Azure Active Directory-Tenant (Azure AD).

Zie [Azure Active Directory instemming-Framework](../develop/consent-framework.md)voor meer informatie over het inzenden van toepassingen.

## <a name="prerequisites"></a>Vereisten

Voor het verlenen van toestemming voor de beheerder van de Tenant moet u zich aanmelden als [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), een [toepassings beheerder](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)of een [Cloud toepassings beheerder](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> Wanneer een toepassing toestemming heeft gekregen voor Tenant beheer, kunnen alle gebruikers zich aanmelden bij de app, tenzij deze is geconfigureerd voor het vereisen van een gebruikers toewijzing. Als u wilt beperken welke gebruikers zich kunnen aanmelden bij een toepassing, moet u de gebruikers toewijzing vereisen en vervolgens gebruikers of groepen toewijzen aan de toepassing. Zie [methoden voor het toewijzen van gebruikers en groepen](methods-for-assigning-users-and-groups.md)voor meer informatie.

> [!WARNING]
> Voor het verlenen van een door de Tenant verleende beheerder toestemming voor een toepassing wordt de app en de uitgever van de app toegang verleend tot de gegevens van uw organisatie. Controleer zorgvuldig de machtigingen die de toepassing aanvraagt voordat u toestemming verleent.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Beheerder toestemming geven van de Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Beheerders toestemming verlenen in bedrijfs-apps

U kunt toestemming van de beheerder voor de hele Tenant verlenen via *bedrijfs toepassingen* als de toepassing al is ingericht in uw Tenant. Een app kan bijvoorbeeld worden ingericht in uw Tenant als ten minste één gebruiker al heeft ingestemd met de toepassing. Zie [hoe en waarom toepassingen worden toegevoegd aan Azure Active Directory](../develop/active-directory-how-applications-are-added.md)voor meer informatie.

Toestemming geven voor de hele Tenant beheerder voor een app die wordt vermeld in **bedrijfs toepassingen**:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), een [toepassings beheerder](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)of een [Cloud toepassings beheerder](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecteer **Azure Active Directory** **bedrijfs toepassingen**.
3. Selecteer de toepassing waaraan u de machtiging voor de hele Tenant beheerder wilt verlenen.
4. Selecteer **machtigingen** en klik vervolgens op **toestemming beheerder verlenen**.
5. Controleer zorgvuldig de machtigingen die nodig zijn voor de toepassing.
6. Als u akkoord gaat met de machtigingen die de toepassing vereist, toestemming verlenen. Als dat niet het geval is, klikt u op **Annuleren** of sluit u het venster.

### <a name="grant-admin-consent-in-app-registrations"></a>Toestemming van beheerder verlenen in App-registraties

Voor toepassingen die uw organisatie heeft ontwikkeld of die rechtstreeks zijn geregistreerd in uw Azure AD-Tenant, kunt u ook toestemming geven voor de beheerder van de hele Tenant via **app-registraties** in de Azure Portal.

Toestemming van de beheerder voor de hele Tenant verlenen van **app-registraties**:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), een [toepassings beheerder](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)of een [Cloud toepassings beheerder](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecteer **Azure Active Directory** vervolgens **app-registraties**.
3. Selecteer de toepassing waaraan u de machtiging voor de hele Tenant beheerder wilt verlenen.
4. Selecteer **API-machtigingen** en klik vervolgens op **toestemming beheerder verlenen**.
5. Controleer zorgvuldig de machtigingen die nodig zijn voor de toepassing.
6. Als u akkoord gaat met de machtigingen die de toepassing vereist, toestemming verlenen. Als dat niet het geval is, klikt u op **Annuleren** of sluit u het venster.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>De URL maken voor het verlenen van toestemming voor de beheerder voor de hele Tenant

Bij het verlenen van beheerders toestemming voor de hele Tenant met behulp van een van de hierboven beschreven methoden, wordt een venster geopend van de Azure Portal om te vragen om toestemming van de beheerder voor de hele Tenant. Als u de client-ID (ook wel de toepassings-ID) van de toepassing kent, kunt u dezelfde URL maken om toestemming te geven voor de beheerder van de hele Tenant.

De URL voor de toestemming van de beheerder voor tenants volgt de volgende indeling:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

Hierbij

* `{client-id}`is de client-ID van de toepassing (ook wel App-ID genoemd).
* `{tenant-id}`is de Tenant-ID van uw organisatie of een geverifieerde domein naam.

Zo altijd moet u de machtigingen voor een toepassings aanvraag zorgvuldig controleren voordat u toestemming verleent.

## <a name="next-steps"></a>Volgende stappen

[Configureren hoe eind gebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)

[Machtigingen en toestemming in het micro soft Identity-platform](../develop/active-directory-v2-scopes.md)

[Azure AD op stack overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
