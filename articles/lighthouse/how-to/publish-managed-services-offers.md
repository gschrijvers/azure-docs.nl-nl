---
title: Een beheerde service aanbieding naar Azure Marketplace publiceren
description: Meer informatie over het publiceren van een Managed Service-aanbod waarmee klanten worden vrijgegeven aan het beheer van de gedelegeerde resources van Azure.
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 04da33d0232371f4175a935bf1e35925376babbc
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788736"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Een beheerde service aanbieding naar Azure Marketplace publiceren

In dit artikel leert u hoe u een aanbieding voor open bare of privé beheerde services publiceert naar [Azure Marketplace](https://azuremarketplace.microsoft.com) met behulp van het [commerciële Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) -programma in Partner Center. Klanten die de aanbieding kopen, kunnen dan de abonnementen en resource groepen voor [Azure delegated resource management](../concepts/azure-delegated-resource-management.md)onboarden.

## <a name="publishing-requirements"></a>Publicatie vereisten

U hebt een geldig [account in het partner centrum](../../marketplace/partner-center-portal/create-account.md) nodig om aanbiedingen te maken en te publiceren. Als u nog geen account hebt, wordt u door het [aanmeldings proces](https://aka.ms/joinmarketplace) geleid door de stappen voor het maken van een account in partner centrum en het inschrijven van het commerciële Marketplace-programma.

Op basis van de [certificerings vereisten voor het beheerde service aanbod](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)moet u beschikken over een [Silver-of Gold-competentie voor het Cloud platform](https://docs.microsoft.com/partner-center/learn-about-competencies) of van een Azure-expert- [MSP](https://partner.microsoft.com/membership/azure-expert-msp) om een beheerd service aanbod te kunnen publiceren.

Uw Microsoft Partner Network-ID (MPN) wordt [automatisch gekoppeld](../../billing/billing-partner-admin-link-started.md) aan de aanbiedingen die u publiceert voor het bijhouden van invloed op de klant afspraken.

> [!NOTE]
> Als u een aanbieding niet wilt publiceren naar Azure Marketplace, kunt u klanten hand matig voorbereiden door Azure Resource Manager sjablonen te gebruiken. Zie voor meer informatie [onboarding van een klant naar Azure gedelegeerd resource beheer](onboard-customer.md).

## <a name="create-your-offer"></a>Uw aanbieding maken

Zie [een beheerde service aanbieding maken](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)voor gedetailleerde instructies voor het maken van uw aanbieding, inclusief alle informatie en assets die u moet opgeven.

Zie de [publicatie handleiding voor Azure Marketplace en AppSource](../../marketplace/marketplace-publishers-guide.md)voor meer informatie over het algemene publicatie proces. U moet ook het [beleid voor commerciële Marketplace-certificerings](https://docs.microsoft.com/legal/marketplace/certification-policies)instanties, met name de sectie [Managed Services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) , bekijken.

Zodra een klant uw aanbieding heeft toegevoegd, kunnen ze een of meer abonnementen of resource groepen delegeren, die vervolgens voor het [beheer van gedelegeerde resources van Azure](#the-customer-onboarding-process)wordt uitgevoerd.

> [!IMPORTANT]
> Elk abonnement in een beheerde service aanbieding bevat een sectie **manifest Details** , waarin u de Azure Active Directory (Azure AD)-entiteiten in uw Tenant definieert die toegang hebben tot de gedelegeerde resource groepen en/of abonnementen voor klanten die dat plan hebben gekocht. Het is belang rijk te weten dat elke groep (of gebruiker of Service-Principal) die u opneemt, dezelfde machtigingen heeft voor elke klant die het plan heeft gekocht. Als u verschillende groepen wilt toewijzen voor gebruik met elke klant, moet u een afzonderlijk [privé plan](../../marketplace/private-offers.md) publiceren dat exclusief is voor elke klant.

## <a name="publish-your-offer"></a>Uw aanbieding publiceren

Zodra u alle secties hebt voltooid, is de volgende stap het publiceren van de aanbieding op Azure Marketplace. Selecteer de knop **publiceren** om het proces van het live-aanbod te initiëren. Meer informatie over dit proces vindt u [hier](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish). 

U kunt op elk gewenst moment [een bijgewerkte versie van uw aanbieding publiceren](../..//marketplace/partner-center-portal/update-existing-offer.md) . U kunt bijvoorbeeld een nieuwe functie definitie toevoegen aan een eerder gepubliceerde aanbieding. Als u dit doet, krijgen klanten die de aanbieding al hebben toegevoegd, een pictogram op de pagina [**service providers**](view-manage-service-providers.md) in de Azure Portal waarmee ze kunnen zien dat er een update beschikbaar is. Elke klant kan [de wijzigingen controleren](view-manage-service-providers.md#update-service-provider-offers) en besluiten of ze willen bijwerken naar de nieuwe versie. 

## <a name="the-customer-onboarding-process"></a>Het onboarding-proces van de klant

Nadat een klant uw aanbieding heeft toegevoegd, kunnen ze [een of meer specifieke abonnementen of resource groepen delegeren](view-manage-service-providers.md#delegate-resources), die vervolgens worden uitgevoerd voor het beheer van de gedelegeerde resources van Azure. Als een klant een aanbieding heeft geaccepteerd, maar nog geen resources heeft gedelegeerd, wordt op de pagina [**service providers**](view-manage-service-providers.md) van de Azure Portal een opmerking weer geven boven aan de **provider** .

> [!IMPORTANT]
> Delegering moet worden uitgevoerd door een niet-gast account in de Tenant van de klant waarvan de [eigenaar ingebouwde rol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) heeft voor het abonnement (of de resource groepen bevat die worden uitgevoerd). Als u alle gebruikers wilt zien die het abonnement kunnen delegeren, kan een gebruiker in de Tenant van de klant het abonnement selecteren in de Azure Portal, **toegangs beheer openen (IAM)** en [alle gebruikers met de rol eigenaar weer geven](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Zodra de klant een abonnement (of een of meer resource groepen binnen een abonnement) delegeert, wordt de resource provider **micro soft. ManagedServices** geregistreerd voor dat abonnement en kunnen gebruikers in uw Tenant toegang krijgen tot de gedelegeerde resources op basis van de autorisaties in uw aanbieding.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [commerciële Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Meer informatie over de [ervaring op het beheer van cross-tenants](../concepts/cross-tenant-management-experience.md).
- [Bekijk en beheer klanten](view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.
