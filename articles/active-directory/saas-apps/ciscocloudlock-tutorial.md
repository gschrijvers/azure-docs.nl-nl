---
title: 'Zelf studie: integratie Azure Active Directory met de Cloud Security Fabric | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de Cloud Security Fabric.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b1471abd7e057af919ed274547daf94d356c2b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "70213601"
---
# <a name="tutorial-integrate-the-cloud-security-fabric-with-azure-active-directory"></a>Zelf studie: de Cloud Security Fabric integreren met Azure Active Directory

In deze zelf studie leert u hoe u de Cloud Security Fabric integreert met Azure Active Directory (Azure AD). Wanneer u de Cloud beveiligings infrastructuur integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Cloud beveiligings infrastructuur.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij de Cloud beveiligings infrastructuur met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Het abonnement voor eenmalige aanmelding voor Cloud Security Fabric (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* De Cloud Security Fabric ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>De Cloud beveiligings infrastructuur toevoegen vanuit de galerie

Als u de integratie van de Cloud beveiligings infrastructuur wilt configureren in azure AD, moet u de Cloud beveiligings infrastructuur vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **de Cloud beveiligings infrastructuur** in het zoekvak.
1. Selecteer **de Cloud beveiligings infrastructuur in het** paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met de Cloud beveiligings infrastructuur met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Cloud beveiligings infrastructuur.

Als u Azure AD SSO wilt configureren en testen met de Cloud Security Fabric, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[Configureer de Cloud Security Fabric SSO](#configure-the-cloud-security-fabric-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak de test gebruiker voor de Cloud beveiligings infrastructuur](#create-the-cloud-security-fabric-test-user)** een equivalent van B. Simon in de Cloud beveiligings infrastructuur die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Cloud Security Fabric** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: 

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: 

    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > De id-waarde is niet echt. Werk de waarde bij met de werkelijke id. Neem contact op met [het ondersteunings team van Cloud Security Fabric-client](mailto:support@cloudlock.com) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

5. Als u de **handtekening** opties wilt wijzigen volgens uw vereiste, klikt u op de knop **bewerken** om het dialoog venster **SAML-handtekening certificaat** te openen.

    ![SAML-antwoord](./media/ciscocloudlock-tutorial/saml.png)

    a. Selecteer de optie **SAML-respons ondertekenen en** de optie bevestiging voor **ondertekening**.

    b. Selecteer de **SHA-256-** optie voor het **handtekening algoritme**.

    c. Klik op **Opslaan**.  

6. Kopieer de gewenste URL ('s) op basis van uw vereiste op de sectie **de Cloud Security Fabric instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-the-cloud-security-fabric-sso"></a>De SSO van Cloud Security Fabric configureren

Als u eenmalige aanmelding wilt configureren voor **de Cloud beveiligings infrastructuur** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [het Cloud Security Fabric-ondersteunings team](mailto:support@cloudlock.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.
### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de Cloud beveiligings infrastructuur.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen **de Cloud beveiligings infrastructuur**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-the-cloud-security-fabric-test-user"></a>De test gebruiker voor de Cloud Security Fabric maken

In deze sectie maakt u een gebruiker met de naam B. Simon in de Cloud Security Fabric. Werk samen met [het ondersteunings team voor Cloud Security Fabric](mailto:support@cloudlock.com) om de gebruikers toe te voegen aan het Cloud platform Security Fabric. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Cloud Security fabric in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Cloud Security Fabric waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
