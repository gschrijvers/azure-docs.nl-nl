---
title: Verificatie methoden | Microsoft Azure kaarten
description: In dit artikel vindt u meer informatie over Azure Active Directory (Azure AD) en gedeelde sleutel verificatie. Beide worden gebruikt voor Microsoft Azure Maps Services. Meer informatie over het verkrijgen van Azure Maps-abonnements sleutel.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335707"
---
# <a name="authentication-with-azure-maps"></a>Verificatie met Azure Maps

Azure Maps ondersteunt twee manieren om aanvragen te verifiëren: verificatie van gedeelde sleutels en Azure Active Directory-verificatie. In dit artikel wordt uitgelegd hoe u deze verificatie methoden kunt gebruiken om uw implementatie van Azure Maps services te begeleiden.

> [!NOTE]
> Ter verbetering van de veilige communicatie met Azure Maps, ondersteunen we nu Transport Layer Security (TLS) 1,2. de ondersteuning voor TLS 1,0 en 1,1 wordt buiten gebruik gesteld. Om service onderbrekingen te voor komen, moet u **uw servers en toepassingen bijwerken om TLS 1,2 te gebruiken vóór 2 April 2020**.  Als u momenteel TLS 1. x gebruikt, evalueert u uw TLS 1,2-gereedheid en ontwikkelt u een migratie plan met de tests die zijn beschreven in [het oplossen van het TLS 1,0-probleem](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Gedeelde sleutel verificatie

 Primaire en secundaire sleutels worden gegenereerd nadat het Azure Maps-account is gemaakt. U wordt aangeraden de primaire sleutel als abonnements sleutel te gebruiken bij het aanroepen van Azure Maps met behulp van gedeelde sleutel verificatie. Met gedeelde sleutel verificatie wordt een sleutel door een Azure Maps-account door gegeven aan een Azure Maps-service. Voor elke aanvraag voor het Azure Maps van services, voegt u de *abonnements sleutel* als een para meter toe aan de URL. De secundaire sleutel kan worden gebruikt in scenario's zoals wijzigingen in de Rolling sleutel.  

Zie [verificatie beheren](https://aka.ms/amauthdetails)voor meer informatie over het weer geven van uw sleutels in de Azure Portal.

> [!Tip]
> U wordt aangeraden de sleutels regelmatig opnieuw te genereren. U hebt twee sleutels, zodat u verbindingen met één sleutel kunt onderhouden tijdens het opnieuw genereren van de andere. Wanneer u de sleutels opnieuw genereert, moet u alle toepassingen die toegang hebben tot uw account, bijwerken met de nieuwe sleutels.

## <a name="authentication-with-azure-active-directory-preview"></a>Verificatie met Azure Active Directory (preview-versie)

Azure Maps biedt nu verificatie aanvragen voor Azure Maps-Services met behulp van [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD biedt verificatie op basis van een identiteit, inclusief [op rollen gebaseerd toegangs beheer (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC wordt gebruikt om toegang tot Azure Maps resources op gebruikers niveau, op groeps niveau of op toepassings niveau toe te staan. In de volgende secties worden de concepten en onderdelen van Azure Maps integratie met Azure AD besproken.

## <a name="authentication-with-oauth-access-tokens"></a>Verificatie met OAuth-toegangstokens

Azure Maps accepteert **OAuth 2,0** -toegangs tokens voor Azure AD-tenants die zijn gekoppeld aan een Azure-abonnement dat een Azure Maps account bevat. Azure Maps accepteert ook tokens voor:

* Azure AD-gebruikers
* Partner toepassingen die gebruikmaken van machtigingen die worden gedelegeerd door gebruikers
* Beheerde identiteiten voor Azure-resources

Azure Maps genereert een *unieke id (client-id)* voor elk Azure Maps-account. U kunt tokens aanvragen bij Azure AD wanneer u deze client-ID combineert met aanvullende para meters. Als u een token wilt aanvragen, geeft u de waarden in de volgende tabel op op basis van uw Azure-omgeving.

| Azure-omgeving   | Azure AD-token eindpunt |
| --------------------|-------------------------|
| Openbare Azure-peering        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Zie [verificatie beheren in azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)voor meer informatie over het configureren van Azure AD en het aanvragen van tokens voor Azure Maps.

Zie [Wat is verificatie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)voor algemene informatie over het aanvragen van tokens van Azure AD.

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Azure-toewijzings resources aanvragen met OAuth-tokens

Nadat Azure AD een token heeft ontvangen, verzendt Azure Maps een aanvraag met de volgende set vereiste aanvraag headers:

| Aanvraagheader    |    Waarde    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorisatie     | Bearer-token eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`is de Azure Maps op een account gebaseerde GUID die wordt weer gegeven op de Azure Maps-verificatie pagina.

Hier volgt een voor beeld van een Azure Maps route aanvraag die gebruikmaakt van een OAuth-token:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Zie [verificatie details weer geven](https://aka.ms/amauthdetails)voor meer informatie over het weer geven van uw client-id.

## <a name="control-access-with-rbac"></a>Toegang beheren met RBAC

Gebruik in azure AD RBAC om de toegang tot beveiligde bronnen te beheren. Stel uw Azure Maps-account in en registreer uw Azure Maps Azure AD-Tenant. Azure Maps ondersteunt het lezen van toegangs beheer voor afzonderlijke Azure AD-gebruikers,-groepen,-toepassingen, Azure-resources en Azure-Services via beheerde identiteiten voor Azure-resources. Op de pagina Azure Maps Portal kunt u RBAC instellen voor uw gekozen rollen.

![Azure Maps gegevens lezer (preview-versie)](./media/azure-maps-authentication/concept.png)

Zie voor meer informatie over het weer geven van uw RBAC-instellingen [RBAC configureren voor Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Beheerde identiteiten voor Azure-resources en-Azure Maps

[Beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) bieden Azure-Services met een automatisch beheerde identiteit, die kan worden gemachtigd om toegang te krijgen tot Azure Maps Services. Enkele voor beelden van beheerde identiteiten zijn: Azure App Service, Azure Functions en Azure Virtual Machines.

## <a name="next-steps"></a>Volgende stappen

* Zie [verificatie beheren in azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)voor meer informatie over het verifiëren van een toepassing met Azure AD en Azure Maps.

* Zie [de Azure Maps map Control gebruiken](https://aka.ms/amaadmc)voor meer informatie over het verifiëren van de Azure Maps map Control en Azure AD.
