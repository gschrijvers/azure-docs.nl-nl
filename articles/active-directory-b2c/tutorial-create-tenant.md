---
title: Zelf studie-een Azure Active Directory B2C-Tenant maken
description: Meer informatie over het voorbereiden van het registreren van uw toepassingen door een Azure Active Directory B2C-Tenant te maken met behulp van de Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186400"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Zelfstudie: Een Azure Active Directory B2C-tenant maken

Voordat uw toepassingen kunnen communiceren met Azure Active Directory B2C (Azure AD B2C), moeten ze zijn geregistreerd in een Tenant die u beheert.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw Tenant aan uw abonnement koppelen
> * Ga naar de map met uw Azure AD B2C-Tenant
> * Voeg de Azure AD B2C resource als **favoriet** toe aan de Azure Portal

In de volgende zelf studie leert u hoe u een toepassing registreert.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-azure-ad-b2c-tenant"></a>Een Azure AD B2C-tenant maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). Meld u aan met een Azure-account waaraan ten minste de rol [Inzender](../role-based-access-control/built-in-roles.md) is toegewezen binnen het abonnement of een resource groep binnen het abonnement.

1. Selecteer de map die uw abonnement bevat.

    Selecteer op de werk balk Azure Portal het pictogram voor het adres van de map en het **abonnement** en selecteer vervolgens de map die uw abonnement bevat. Deze map wijkt af van de directory die uw Azure AD B2C-Tenant bevat.

    ![Abonnement-Tenant, adres lijst-en abonnements filter waarvoor de Tenant van het abonnement is geselecteerd](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
1. Zoek naar **Azure Active Directory B2C**en selecteer vervolgens **maken**.
1. Selecteer **Een nieuwe Azure AD B2C-tenant maken**.

    ![Maak een nieuwe Azure AD B2C-Tenant geselecteerd in Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Voer de **naam** van de organisatie en de **initiële domein naam**in. Selecteer het **land of de regio** (deze kan later niet worden gewijzigd) en selecteer vervolgens **maken**.

    De domein naam wordt gebruikt als onderdeel van de domein naam van uw volledige Tenant. In dit voor beeld is de naam van de Tenant *contosob2c.onmicrosoft.com*:

    ![Een Tenant formulier maken in met voorbeeld waarden in Azure Portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Nadat het maken van de Tenant is voltooid, selecteert u de **nieuwe B2C-Tenant maken of een koppeling naar een bestaande Tenant koppeling aan** de bovenkant van de pagina voor het maken van de Tenant.

    ![Koppeling naar de breadcrumbnavigatie van de Tenant is gemarkeerd in Azure Portal](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selecteer **een bestaande Azure AD B2C-Tenant koppelen aan mijn Azure-abonnement**.

   ![Een bestaande abonnements selectie in Azure Portal koppelen](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selecteer de **Azure AD B2C Tenant** die u hebt gemaakt en selecteer vervolgens uw **abonnement**.

    Selecteer voor **Resourcegroep** de optie **Nieuwe maken**. Voer een **naam** in voor de resource groep die de Tenant bevat, selecteer de **locatie van de resource groep**en selecteer vervolgens **maken**.

    ![Formulier abonnements instellingen koppelen in Azure Portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    U kunt meerdere Azure AD B2C-tenants koppelen aan één Azure-abonnement voor facturerings doeleinden.

## <a name="select-your-b2c-tenant-directory"></a>Selecteer uw B2C-Tenant Directory

Als u uw nieuwe Azure AD B2C Tenant wilt gaan gebruiken, moet u overschakelen naar de map die de Tenant bevat.

Selecteer het filter **Directory + abonnement** in het bovenste menu van de Azure Portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.

Als u uw nieuwe Azure B2C-Tenant in de lijst niet ziet, vernieuwt u het browser venster en selecteert u vervolgens het filter voor het adres van de map en het **abonnement** in het bovenste menu.

![B2C-Tenant die de map bevat die is geselecteerd in Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C als favoriet toevoegen (optioneel)

Met deze optionele stap kunt u uw Azure AD B2C-Tenant gemakkelijker selecteren in de volgende en alle volgende zelf studies.

In plaats van elke keer dat u met uw Tenant wilt werken, kunt u in plaats daarvan *Azure AD B2C* in **alle services** zoeken. Vervolgens kunt u deze selecteren in de sectie **Favorieten** van het menu van de portal om snel naar uw Azure AD B2C-Tenant te bladeren.

U hoeft deze bewerking slechts één keer uit te voeren. Voordat u deze stappen uitvoert, moet u ervoor zorgen dat u bent overgeschakeld naar de map met uw Azure AD B2C Tenant, zoals wordt beschreven in de vorige sectie, [Selecteer uw B2C-Tenant Directory](#select-your-b2c-tenant-directory).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in het menu Azure Portal **alle services**.
1. In het zoekvak **alle services** zoekt u naar **Azure AD B2C**, houdt u de muis aanwijzer boven het Zoek resultaat en selecteert u vervolgens het ster pictogram in de knop info. **Azure AD B2C** nu wordt weer gegeven in de Azure portal onder **Favorieten**.
1. Als u de positie van uw nieuwe favoriet wilt wijzigen, gaat u naar het menu Azure Portal, selecteert u **Azure AD B2C**en sleept u het omhoog of omlaag naar de gewenste positie.

    ![Azure AD B2C, menu Favorieten Microsoft Azure-portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw Tenant aan uw abonnement koppelen
> * Ga naar de map met uw Azure AD B2C-Tenant
> * Voeg de Azure AD B2C resource als **favoriet** toe aan de Azure Portal

Vervolgens leert u hoe u een webtoepassing in uw nieuwe Tenant kunt registreren.

> [!div class="nextstepaction"]
> [Uw toepassingen registreren >](tutorial-register-applications.md)
