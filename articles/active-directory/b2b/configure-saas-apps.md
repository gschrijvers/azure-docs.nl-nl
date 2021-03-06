---
title: SaaS-apps voor B2B-samen werking configureren-Azure AD
description: Code- en PowerShell-voorbeelden voor Azure Active Directory B2B-samenwerking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c73a14c2a8cd063672bd0998368ca660f52cd5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272953"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>SaaS-toepassingen configureren voor B2B-samenwerking

Azure Active Directory (Azure AD) B2B-samen werking werkt met de meeste apps die zijn geïntegreerd met Azure AD. In deze sectie wordt uitgelegd hoe u enkele populaire SaaS-apps kunt configureren voor gebruik met Azure AD B2B.

Voordat u de app-specifieke instructies bekijkt, zijn hier enkele vuist regels:

* Voor de meeste apps moet gebruikers installatie hand matig plaatsvinden. Dat wil zeggen dat gebruikers ook hand matig in de app moeten worden gemaakt.

* Voor apps die ondersteuning bieden voor automatische installatie, zoals Dropbox, worden afzonderlijke uitnodigingen gemaakt op basis van de apps. Gebruikers moeten elke uitnodiging accepteren.

* Stel in de gebruikers kenmerken de **gebruikers-id** altijd in op **gebruiker. mail**om problemen met een vervormde gebruikers profiel schijf (UPD) te beperken in gast gebruikers.


## <a name="dropbox-business"></a>Dropbox-bedrijven

Als u gebruikers in staat wilt stellen om zich aan te melden met hun organisatie account, moet u de Dropbox-onderneming hand matig configureren voor gebruik van Azure AD als Security Assertion Markup Language (SAML)-ID-provider. Als Dropbox Business niet is geconfigureerd om dit te doen, kan de gebruiker zich niet vragen of op een andere manier toestaan zich aan te melden met Azure AD.

1. Als u de BDC Business-app wilt toevoegen aan Azure AD, selecteert u **bedrijfs toepassingen** in het linkerdeel venster en klikt u vervolgens op **toevoegen**.

   ![De knop toevoegen op de pagina bedrijfs toepassingen](media/configure-saas-apps/add-dropbox.png)

2. In het venster **een toepassing toevoegen** voert u **Dropbox** in het zoekvak in en selecteert u **Dropbox voor bedrijven** in de lijst met resultaten.

   ![Zoeken naar Dropbox op de pagina een toepassing toevoegen](media/configure-saas-apps/add-app-dialog.png)

3. Selecteer op de pagina **eenmalige aanmelding** de optie **eenmalige aanmelding** in het linkerdeel venster en voer vervolgens **gebruiker. mail** in het vak **gebruikers-id** in. (Deze is standaard ingesteld als UPN.)

   ![Eenmalige aanmelding voor de app configureren](media/configure-saas-apps/configure-app-sso.png)

4. Als u het certificaat wilt downloaden om te gebruiken voor de Dropbox-configuratie, selecteert u **Dropbox configureren**en selecteert u **URL voor eenmalige aanmelding voor SAML** in de lijst.

   ![Het certificaat voor de Dropbox-configuratie downloaden](media/configure-saas-apps/download-certificate.png)

5. Meld u aan bij Dropbox met de aanmeldings-URL op de pagina voor **eenmalige aanmelding** .

   ![Scherm afbeelding van de aanmeldings pagina voor Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. Selecteer in het menu de optie **beheer console**.

   ![De koppeling ' beheer console ' in het menu Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. In het dialoog venster **verificatie** selecteert u **meer**, uploadt u het certificaat en voert u in het vak **aanmeldings-URL** de URL voor eenmalige SAML-aanmelding in.

   ![De koppeling ' meer ' in het dialoog venster samengevouwen verificatie](media/configure-saas-apps/dropbox-auth-01.png)

   ![De aanmeldings-URL in het dialoog venster uitgebreide verificatie](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Als u automatische gebruikers instellingen wilt configureren in de Azure Portal, selecteert u **inrichting** in het linkerdeel venster, selecteert u **automatisch** in het vak **inrichtings modus** en selecteert u vervolgens **autoriseren**.

   ![Automatische gebruikers inrichting configureren in de Azure Portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Nadat gasten of gebruikers in de Dropbox-app zijn ingesteld, ontvangen ze een afzonderlijke uitnodiging van Dropbox. Als u eenmalige aanmelding met Dropbox wilt gebruiken, moeten genodigden de uitnodiging accepteren door te klikken op een koppeling hierin.

## <a name="box"></a>Box
U kunt gebruikers in staat stellen gast gebruikers te verifiëren met hun Azure AD-account door Federatie te gebruiken dat is gebaseerd op het SAML-protocol. In deze procedure uploadt u meta gegevens naar Box.com.

1. Voeg de box-app toe vanuit de Enter prise-apps.

2. Eenmalige aanmelding configureren in de volgende volg orde:

   ![Scherm opname met de configuratie-instellingen voor eenmalige aanmelding](media/configure-saas-apps/configure-box-sso.png)

   a. Controleer in het vak **Sign on URL** of de AANMELDINGS-URL juist is ingesteld voor box in het Azure Portal. Deze URL is de URL van uw Box.com-Tenant. Deze moet de naamgevings Conventie *https://.box.com*volgen.  
   De **id** is niet van toepassing op deze app, maar wordt nog steeds weer gegeven als een verplicht veld.

   b. Voer in het vak **gebruikers** -id **gebruiker. mail** in (voor SSO voor gast-accounts).

   c. Klik onder **SAML-handtekening certificaat**op **Nieuw certificaat maken**.

   d. Als u de Box.com-Tenant wilt configureren voor het gebruik van Azure AD als id-provider, downloadt u het meta gegevensbestand en slaat u het op uw lokale station op.

   e. Stuur het meta gegevensbestand door naar het ondersteunings team van het vak, waarmee eenmalige aanmelding voor u wordt geconfigureerd.

3. Selecteer voor automatische gebruikers installatie van Azure AD in het linkerdeel venster de optie **inrichten**en selecteer vervolgens **autoriseren**.

   ![Azure AD toestemming geven om verbinding te maken met Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Net als voor Dropbox-genodigden moeten Box-genodigden hun uitnodiging inwisselen vanuit de box-app.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Dynamische groepen en B2B-samen werking](use-dynamic-groups.md)
- [Toewijzing van gebruikers claims voor B2B-samen werking](claims-mapping.md)
- [Office 365 extern delen](o365-external-user.md)

