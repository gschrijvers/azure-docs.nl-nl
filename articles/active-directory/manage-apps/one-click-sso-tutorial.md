---
title: Eenmalige aanmelding (SSO) van uw Azure Marketplace-toepassing | Microsoft Docs
description: Stappen voor één klik configuratie van SSO voor uw toepassing vanuit Azure Marketplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67872431"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Eén Klik op app-configuratie van eenmalige aanmelding

 In deze zelf studie leert u hoe u met één klik kunt configureren, eenmalige aanmelding (SSO) voor SAML-ondersteunende, Azure Active Directory-toepassingen (Azure AD) vanuit Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Inleiding tot eenmalige aanmelding met één klik

De functie SSO met één klik is ontworpen voor het configureren van eenmalige aanmelding voor Azure Marketplace-apps die ondersteuning bieden voor het SAML-protocol. Op de pagina Azure AD SSO-configuratie kunt u met deze optie automatisch de Azure AD-meta gegevens op de toepassings zijde configureren. Op deze manier kunt u eenmalige aanmelding snel instellen met minimale hand matige inspanningen.

## <a name="advantages-of-one-click-sso"></a>Voor delen van eenmalige aanmelding met één klik

- Snelle SSO-configuratie van Azure Marketplace-toepassingen die hand matig moeten worden ingesteld aan de kant van de toepassing.
- Efficiënter en nauw keurige SSO-configuratie.
- Er is geen partner communicatie of ondersteuning vereist voor de installatie. De toepassing biedt de gebruikers interface voor SAML-configuratie.

## <a name="prerequisites"></a>Vereisten

- Een actief abonnement van de toepassing om te configureren met SSO. U hebt ook beheerders referenties nodig.
- De **beveiligde aanmeld extensie voor mijn apps** van micro soft die in de browser is geïnstalleerd. Zie voor meer informatie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Configuratie stappen voor eenmalige aanmelding met één klik

1. Voeg de toepassing toe vanuit Azure Marketplace.

2. Selecteer **eenmalige aanmelding**.

3. Selecteer **eenmalige aanmelding inschakelen**.

4. Vul de verplichte configuratie waarden in in het gedeelte **basis configuratie van SAML** .

    > [!NOTE]
    > Als de toepassing aangepaste claims heeft die u moet configureren, kunt u deze afhandelen voordat u eenmalige aanmelding uitvoert.

5. Als de functie voor eenmalige aanmelding met één klik beschikbaar is voor uw Azure Marketplace-toepassing, ziet u het volgende scherm. Mogelijk moet u de **uitbrei ding voor beveiligde aanmeldings van mijn apps** installeren door **de extensie installeren**te selecteren.

   ![De browser uitbreiding mijn apps veilig aanmelden installeren](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Nadat u de uitbrei ding aan de browser hebt toegevoegd, selecteert u **toepassings naam \<\>van installatie**. Nadat u bent omgeleid naar de beheer portal van de toepassing, meldt u zich aan als beheerder.

   ![Toepassings naam van installatie](./media/one-click-sso-tutorial/setup-sso.png)

7. Met de browser extensie wordt SSO automatisch geconfigureerd op de toepassing. Bevestig door **Ja**te selecteren.

   ![De automatisch ingevulde gegevens worden opgeslagen](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Als de SSO-configuratie voor uw toepassing extra stappen vereist, volgt u de aanwijzingen om de stappen uit te voeren.

8. Nadat de configuratie is voltooid, selecteert u **OK** om de wijzigingen op te slaan.

   ![De automatisch ingevulde gegevens opslaan](./media/one-click-sso-tutorial/save-data.png)

9. Er wordt een bevestigings venster weer gegeven om u te laten weten dat de SSO-instellingen zijn geconfigureerd.

   ![SSO geconfigureerd](./media/one-click-sso-tutorial/sso-configured.png)

10. Nadat de configuratie is voltooid, bent u afgemeld bij de toepassing en keert u terug naar de Azure Portal.

11. U kunt **testen** selecteren om eenmalige aanmelding te testen.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Wat is de browser uitbreiding mijn apps veilig aanmelden?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
