---
title: Koppelingen en Url's vertalen Azure AD-app proxy | Microsoft Docs
description: Behandelt de basis beginselen van Azure AD-toepassingsproxy-connectors.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "69533696"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Hardcoded koppelingen omleiden voor apps die zijn gepubliceerd met Azure AD-toepassingsproxy

Azure AD-toepassingsproxy maakt uw on-premises apps beschikbaar voor gebruikers die zich op afstand of op hun eigen apparaten bevinden. Sommige apps zijn echter ontwikkeld met lokale koppelingen die zijn Inge sloten in de HTML. Deze koppelingen werken niet naar behoren wanneer de app op afstand wordt gebruikt. Wanneer u meerdere on-premises toepassingen naar elkaar wijst, verwachten uw gebruikers dat de koppelingen blijven werken wanneer ze niet op kantoor zijn. 

De beste manier om ervoor te zorgen dat koppelingen werken die zowel binnen als buiten uw bedrijfs netwerk zijn, is door de externe Url's van uw apps zo te configureren dat deze hetzelfde zijn als de interne Url's. Gebruik [aangepaste domeinen](application-proxy-configure-custom-domain.md) om uw externe url's te configureren voor de naam van uw bedrijfs domein in plaats van het standaard domein voor de toepassings proxy.


Als u geen aangepaste domeinen in uw Tenant kunt gebruiken, zijn er enkele andere opties voor het bieden van deze functionaliteit. Al deze zijn ook compatibel met aangepaste domeinen en elkaar, zodat u zo nodig aangepaste domeinen en andere oplossingen kunt configureren.

> [!NOTE]
> Koppelings vertalingen worden niet ondersteund voor in code vastgelegde interne Url's die via Java script worden gegenereerd.

**Optie 1: gebruik de Managed browser of micro soft Edge** : deze oplossing is alleen van toepassing als u van plan bent om aan te bevelen of gebruikers toegang tot de toepassing te geven via de intune Managed browser of micro soft Edge-browser. Alle gepubliceerde Url's worden verwerkt. 

**Optie 2: de MyApps-extensie gebruiken** : voor deze oplossing moeten gebruikers een browser extensie aan client zijde installeren, maar alle gepubliceerde url's worden verwerkt en werkt met de meeste populaire browsers. 

**Optie 3: gebruik de instelling voor het converteren van koppelingen** : dit is een instelling aan de beheerder zijde die onzichtbaar is voor gebruikers. Er worden echter alleen Url's verwerkt in HTML en CSS.   

Deze drie functies zorgen ervoor dat uw koppelingen werken, ongeacht waar uw gebruikers zich bevinden. Wanneer u apps hebt die rechtstreeks naar interne eind punten of poorten verwijzen, kunt u deze interne Url's toewijzen aan de Url's van de gepubliceerde externe toepassings proxy. 

 
> [!NOTE]
> De laatste optie is alleen voor tenants die om welke reden dan ook geen aangepaste domeinen kunnen gebruiken om dezelfde interne en externe Url's voor hun apps te hebben. Voordat u deze functie inschakelt, moet u controleren of [aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md) voor u kunnen werken. 
> 
> Als de toepassing die u wilt configureren met koppelings omzetting share point is, raadpleegt u [alternatieve toegangs toewijzingen voor share point 2013 configureren](https://technet.microsoft.com/library/cc263208.aspx) voor een andere benadering voor het toewijzen van koppelingen. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Optie 1: integratie van Intune Managed Browser en micro soft Edge 

U kunt de Intune Managed Browser of micro soft Edge gebruiken om uw toepassing en inhoud verder te beveiligen. Als u deze oplossing wilt gebruiken, moet u gebruikers verplichten om toegang te krijgen tot de toepassing via de Intune Managed Browser. Alle interne Url's die zijn gepubliceerd met toepassings proxy, worden herkend door de Managed Browser en omgeleid naar de bijbehorende externe URL. Dit zorgt ervoor dat alle hardcoded interne Url's werken, en als een gebruiker naar de browser gaat en rechtstreeks de interne URL typt, werkt deze zelfs als de gebruiker extern is.  

Raadpleeg de documentatie van [Managed browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) voor meer informatie over het configureren van deze optie.  

### <a name="option-2-myapps-browser-extension"></a>Optie 2: browser uitbreiding MyApps 

Met de browser uitbreiding MyApps worden alle interne Url's die zijn gepubliceerd met toepassings proxy, herkend door de extensie en omgeleid naar de bijbehorende externe URL. Dit zorgt ervoor dat alle hardcoded interne Url's werken en als een gebruiker naar de adres balk van de browser gaat en de interne URL rechtstreeks typt, werkt deze zelfs als de gebruiker extern is.  

Als u deze functie wilt gebruiken, moet de gebruiker de extensie downloaden en zijn aangemeld. Er is geen andere configuratie nodig voor beheerders of gebruikers. 

Raadpleeg de documentatie van de [MyApps-browser uitbreiding](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) voor meer informatie over het configureren van deze optie.

### <a name="option-3-link-translation-setting"></a>Optie 3: instelling voor het omzetten van koppelingen 

Wanneer koppelings vertalingen is ingeschakeld, zoekt de Application proxy-service in HTML en CSS naar gepubliceerde interne koppelingen en worden ze vertaald zodat uw gebruikers een ononderbroken ervaring krijgen. Het gebruik van de MyApps-browser uitbreiding verdient de voor keur voor de instelling voor de conversie van koppelingen, omdat het een betere ervaring voor gebruikers biedt.

> [!NOTE]
> Als u optie 2 of 3 gebruikt, moet slechts één van deze tegelijk worden ingeschakeld.

## <a name="how-link-translation-works"></a>De werking van koppelings vertalingen

Na verificatie, wanneer de proxy server de toepassings gegevens door geven aan de gebruiker, scant de toepassings proxy de toepassing op vastgelegde koppelingen en worden deze vervangen door hun respectieve, gepubliceerde externe Url's.

Toepassings proxy gaat ervan uit dat toepassingen zijn gecodeerd in UTF-8. Als dat niet het geval is, geeft u het type code ring op in een http- `Content-Type:text/html;charset=utf-8`antwoord header, zoals.

### <a name="which-links-are-affected"></a>Welke koppelingen worden getroffen?

De functie voor het omzetten van koppelingen zoekt alleen naar koppelingen in code tags in de hoofd tekst van een app. Toepassings proxy heeft een afzonderlijke functie voor het omzetten van cookies of Url's in kopteksten. 

Er zijn twee algemene typen interne koppelingen in on-premises toepassingen:

- **Relatieve interne koppelingen** die verwijzen naar een gedeelde bron in een lokale bestands structuur, `/claims/claims.html`zoals. Deze koppelingen werken automatisch in apps die zijn gepubliceerd via toepassings proxy en blijven werken met of zonder koppelings conversie. 
- **Hardcoded interne koppelingen** naar andere on-premises apps `http://expenses` zoals of gepubliceerde bestanden `http://expenses/logo.jpg`zoals. De functie voor het vertalen van koppelingen werkt op hardcoded interne koppelingen en wijzigt deze zodat ze verwijzen naar de externe Url's die externe gebruikers nodig hebben om door te gaan.

De volledige lijst met HTML-code tags die toepassings proxy ondersteunt koppelings vertalingen voor zijn onder andere:
* a
* audio
* base
* knop
* div
* Insluiten
* formulieren
* kader
* horen
* html
* iframe
* afbeelding
* input
* koppelen
* formulier
* metatag
* object
* uit
* source
* bijhouden
* video

Daarnaast wordt het URL-kenmerk ook omgezet in CSS.

### <a name="how-do-apps-link-to-each-other"></a>Hoe maken apps verbinding met elkaar?

Koppelings vertalingen zijn ingeschakeld voor elke toepassing, zodat u de gebruikers ervaring op het niveau per app kunt controleren. Schakel koppelings vertalingen voor een app in als u wilt dat de koppelingen *van* die app worden vertaald, en niet *naar die app* . 

Stel bijvoorbeeld dat u drie toepassingen hebt gepubliceerd via een toepassings proxy die helemaal aan elkaar is gekoppeld: voor delen, kosten en reizen. Er is een vierde app, feedback die niet is gepubliceerd via de toepassings proxy.

Wanneer u koppelings vertalingen inschakelt voor de app voor delen, worden de koppelingen naar onkosten en reizen omgeleid naar de externe Url's voor die apps, maar de koppeling naar de feedback wordt niet omgeleid omdat er geen externe URL is. Koppelingen van onkosten en reizen terug naar voor delen werken niet, omdat koppelings omzetting niet is ingeschakeld voor deze twee apps.

![Koppelingen van voor delen naar andere apps wanneer koppelings vertalingen is ingeschakeld](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Welke koppelingen worden niet vertaald?

Sommige koppelingen worden niet vertaald om de prestaties en beveiliging te verbeteren:

- Koppelingen niet in code tags. 
- Koppelingen niet in HTML of CSS. 
- Koppelingen in URL-gecodeerde indeling.
- Interne koppelingen die vanuit andere Program ma's worden geopend. Koppelingen die worden verzonden via e-mail of chat bericht, of die zijn opgenomen in andere documenten, worden niet vertaald. De gebruikers moeten weten dat ze naar de externe URL moeten gaan.

Als u een van deze twee scenario's wilt ondersteunen, moet u dezelfde interne en externe Url's gebruiken in plaats van koppelings vertalingen.  

## <a name="enable-link-translation"></a>Koppelings vertalingen inschakelen

Aan de slag met koppelings vertalingen is net zo eenvoudig als het klikken op een knop:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
2. Ga naar **Azure Active Directory** > **Enter prise-toepassingen** > **alle toepassingen** > Selecteer de app die u wilt beheren > **toepassings proxy**.
3. Zet **url's in de hoofd tekst van de toepassing om in** **Ja**.

   ![Selecteer Ja om de Url's in de hoofd tekst van de toepassing te vertalen](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Klik op **Opslaan** om uw wijzigingen toe te passen.

Wanneer uw gebruikers nu toegang tot deze toepassing hebben, scant de proxy automatisch op interne Url's die zijn gepubliceerd via de toepassings proxy van uw Tenant.

## <a name="send-feedback"></a>Feedback verzenden

We willen dat deze functie geschikt is voor al uw apps. We zoeken meer dan 30 Tags in HTML en CSS. Als u een voor beeld hebt van gegenereerde koppelingen die niet worden vertaald, verzendt u een code fragment naar de feedback van de [toepassings proxy](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Volgende stappen
[Aangepaste domeinen met Azure AD-toepassingsproxy gebruiken](application-proxy-configure-custom-domain.md) om dezelfde interne en externe URL te hebben

[Alternatieve toegangs toewijzingen configureren voor share point 2013](https://technet.microsoft.com/library/cc263208.aspx)
