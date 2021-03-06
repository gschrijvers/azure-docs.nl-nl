---
title: 'Zelf studie: NetSuite OneWorld configureren voor automatische gebruikers inrichting met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en NetSuite OneWorld.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9a823e6515c2bfe09e1ab7bcef471eb8169e75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063292"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Zelf studie: een NetSuite configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in OneWorld en Azure AD voor het automatisch inrichten en inleveren van gebruikers accounts van Azure AD naar Netsuite.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

*   Een Azure Active Directory-Tenant.
*   Een OneWorld-abonnement van NetSuite. Houd er rekening mee dat automatische gebruikers inrichting momenteel alleen wordt ondersteund met NetSuite OneWorld.
*   Een gebruikers account in NetSuite met beheerders machtigingen.

## <a name="assigning-users-to-netsuite-oneworld"></a>Gebruikers toewijzen aan NetSuite OneWorld

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts, worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw NetSuite-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw NetSuite-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Belang rijke tips voor het toewijzen van gebruikers aan NetSuite OneWorld

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan NetSuite om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan NetSuite, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="enable-user-provisioning"></a>Gebruikers inrichten inschakelen

In deze sectie wordt u begeleid bij het koppelen van de API voor het inrichten van de gebruikers account van Azure AD naar het internet en het configureren van de inrichtings service om toegewezen gebruikers accounts in NetSuite te maken, bij te werken en uit te scha kelen in azure AD.

> [!TIP] 
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor NetSuite in te scha kelen, gevolgd door de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-user-account-provisioning"></a>Het inrichten van een gebruikers account configureren:

Het doel van deze sectie is het maken van een overzicht van het inschakelen van de gebruikers inrichting van Active Directory gebruikers accounts voor Netsuite.

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

1. Als u NetSuite al hebt geconfigureerd voor eenmalige aanmelding, zoekt u in het zoek veld naar uw exemplaar van NetSuite. Selecteer anders **toevoegen** en zoeken naar **NetSuite** in de toepassings galerie. Selecteer NetSuite in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van NetSuite en selecteer vervolgens het tabblad **inrichten** .

1. Stel de **inrichtings modus** in op **automatisch**. 

    ![inrichtings](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Geef onder de sectie **beheerders referenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak naam van de **beheerder** de naam van een NetSuite-account waaraan het **systeem beheerders** profiel in Netsuite.com is toegewezen.
   
    b. Typ in het tekstvak **beheerders wachtwoord** het wacht woord voor dit account.
      
1. Klik in de Azure Portal op **verbinding testen** om te controleren of Azure AD verbinding kan maken met uw NetSuite-app.

1. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in.

1. Klik op **opslaan.**

1. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met Netsuite.**

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Netsuite. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om te voldoen aan de gebruikers accounts in NetSuite voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Als u de Azure AD-inrichtings service voor NetSuite **wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte instellingen

1. Klik op **opslaan.**

Hiermee start u de initiële synchronisatie van gebruikers en/of groepen die zijn toegewezen aan NetSuite in de sectie gebruikers en groepen. Houd er rekening mee dat het uitvoeren van de eerste synchronisatie langer duurt dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar de inrichtings activiteiten logboeken te volgen. hierin worden alle acties beschreven die worden uitgevoerd door de inrichtings service in de NetSuite-app.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](tutorial-list.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eenmalige aanmelding configureren](netsuite-tutorial.md)
