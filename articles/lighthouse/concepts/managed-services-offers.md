---
title: Aanbiedingen voor beheerde service in Azure Marketplace
description: Met beheerde services kunnen service providers resource beheer aanbiedingen verkopen aan klanten in azure Marketplace.
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 975c9ab98229626a513404b2092b266a9e284279
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792289"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Aanbiedingen voor beheerde service in Azure Marketplace

In dit artikel wordt het type **beheerde service** aanbieding in [Azure Marketplace](https://azuremarketplace.microsoft.com)beschreven. Met beheerde service aanbiedingen kunt u resource beheer services aan klanten aanbieden via het [beheer van gedelegeerde resources van Azure](azure-delegated-resource-management.md). U kunt deze aanbiedingen beschikbaar stellen aan alle potentiële klanten of alleen aan een of meer specifieke klanten. Omdat u klanten rechtstreeks factureert voor kosten met betrekking tot deze beheerde services, zijn er geen kosten in rekening gebracht door micro soft.

## <a name="understand-managed-service-offers"></a>Meer informatie over beheerde service aanbiedingen

Met managed service kunt u het proces van onboarding van klanten voor Azure delegated resource management stroom lijnen. Wanneer een klant een aanbieding in azure Marketplace koopt, kunnen ze opgeven welke abonnementen en/of resource groepen onboarding moeten worden uitgevoerd.

Daarna kunnen gebruikers in uw organisatie aan deze resources werken vanuit de Tenant van uw organisatie, op basis van de toegang die u hebt gedefinieerd bij het maken van de aanbieding. Dit wordt gedaan via een manifest waarmee de gebruikers, groepen en service-principals van Azure Active Directory (Azure AD) worden opgegeven die toegang hebben tot de resources van de klant, samen met rollen die hun toegangs niveau bepalen. Door machtigingen toe te wijzen aan een Azure AD-groep in plaats van een reeks afzonderlijke gebruikers-of toepassings accounts, kunt u afzonderlijke gebruikers toevoegen of verwijderen wanneer uw toegangs vereisten veranderen.

## <a name="public-and-private-offers"></a>Open bare en persoonlijke aanbiedingen

Elk managed services-aanbod omvat een of meer abonnementen. Plannen kunnen privé of openbaar zijn.

Als u uw aanbieding wilt beperken tot specifieke klanten, kunt u een privé-abonnement publiceren. Wanneer u dit doet, kan het abonnement alleen worden aangeschaft voor de specifieke] abonnements-Id's die u opgeeft. Zie voor meer informatie [persoonlijke aanbiedingen](../../marketplace/private-offers.md).

Met open bare abonnementen kunt u uw services promo veren tot nieuwe klanten. Dit zijn doorgaans handiger wanneer u alleen beperkte toegang tot de Tenant van de klant nodig hebt. Zodra u een relatie met een klant tot stand hebt gebracht, kunt u dit doen door een nieuw privé-abonnement alleen voor die klant te publiceren of door [ze voor verdere toegang te gebruiken met behulp van Azure Resource Manager sjablonen](../how-to/onboard-customer.md).

Indien nodig kunt u zowel open bare als privé-abonnementen in dezelfde aanbieding toevoegen.

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, kunt u het niet wijzigen in persoonlijk. Gebruik een privé abonnement om te bepalen welke klanten uw aanbieding mogen accepteren en resources kunnen delegeren. Met een openbaar abonnement kunt u de beschik baarheid van bepaalde klanten of zelfs voor een bepaald aantal klanten niet beperken (hoewel u ervoor kiest om het abonnement niet volledig te verkopen). U kunt de [toegang tot een overdracht verwijderen](../how-to/remove-delegation.md) nadat een klant alleen een aanbieding heeft geaccepteerd als u een **autorisatie** hebt opgenomen waarbij de **roldefinitie** is ingesteld op de functie voor het verwijderen van de [registratie toewijzing van beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) tijdens het publiceren van de aanbieding. U kunt ook contact opnemen met de klant en vragen om [uw toegang te verwijderen](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Aanbiedingen voor beheerde services publiceren

Zie [een Managed Services-aanbieding naar Azure Marketplace publiceren](../how-to/publish-managed-services-offers.md)voor meer informatie over het publiceren van een managed services-aanbod.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure delegated resource management](azure-delegated-resource-management.md) en [Cross-Tenant beheer ervaring](cross-tenant-management-experience.md).
- [Publiceer Managed Services aanbiedingen](../how-to/publish-managed-services-offers.md) voor Azure Marketplace.
