---
title: Automatische versnelling bij het aanmelden configureren met behulp van Home realm Discovery
description: Meer informatie over het configureren van beleid voor het detecteren van basis-Realms voor Azure Active Directory authenticatie voor federatieve gebruikers, met inbegrip van automatische versnelling en domein hints.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 340cf77ae6b4c5677ed91f6a0626b73d259e5fd2
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690507"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Azure Active Directory aanmeldings gedrag configureren voor een toepassing met behulp van een beleid voor het detecteren van een thuis domein

Dit artikel bevat een inleiding tot het configureren van Azure Active Directory verificatie gedrag voor federatieve gebruikers. Het behandelt de configuratie van beperkingen voor automatische versnelling en verificatie voor gebruikers in federatieve domeinen.

## <a name="home-realm-discovery"></a>Home Realm Discovery
Home realm Discovery (HRD) is het proces waarmee Azure Active Directory (Azure AD) kunt bepalen waar een gebruiker zich moet verifiëren tijdens de aanmeldings tijd.  Wanneer een gebruiker zich aanmeldt bij een Azure AD-Tenant om toegang te krijgen tot een resource, of naar de aanmeldings pagina van de Azure AD-aanmelding, typt u een gebruikers naam (UPN). Azure AD gebruikt om te ontdekken waar de gebruiker zich moet aanmelden. 

De gebruiker moet mogelijk worden uitgevoerd op een van de volgende locaties om te worden geverifieerd:

- De thuis Tenant van de gebruiker (kan dezelfde Tenant zijn als de bron waartoe de gebruiker toegang probeert te krijgen). 

- Microsoft-account.  De gebruiker is een gast in de resource-Tenant.

-  Een on-premises ID-provider, zoals Active Directory Federation Services (AD FS).

- Een andere ID-provider die federatief is voor de Azure AD-Tenant.

## <a name="auto-acceleration"></a>Automatische versnelling 
Sommige organisaties configureren domeinen in hun Azure Active Directory Tenant om te communiceren met een andere IdP, zoals AD FS voor gebruikers verificatie.  

Wanneer een gebruiker zich aanmeldt bij een toepassing, worden ze eerst weer gegeven met een aanmeldings pagina voor Azure AD. Nadat ze hun UPN hebben getypt, worden ze in een federatief domein geplaatst op de aanmeldings pagina van de IdP voor dat domein. Onder bepaalde omstandigheden kunnen beheerders gebruikers willen omleiden naar de aanmeldings pagina wanneer ze zich aanmelden bij specifieke toepassingen. 

Als gevolg hiervan kunnen gebruikers de eerste Azure Active Directory-pagina overs Laan. Dit proces wordt ' automatische versnelling ' genoemd.

In gevallen waarbij de Tenant federatief is voor een andere IdP om zich aan te melden, maakt automatische versnelling gebruikers meer gestroomlijnd.  U kunt automatische versnelling voor afzonderlijke toepassingen configureren.

>[!NOTE]
>Als u een toepassing configureert voor automatische versnelling, kunnen gast gebruikers zich niet aanmelden. Als u een gebruiker recht hebt op een federatieve IdP voor authenticatie, is er geen manier om deze te openen om terug te gaan naar de aanmeldings pagina van Azure Active Directory. Gast gebruikers, die mogelijk moeten worden omgeleid naar andere tenants of een externe IdP, zoals een Microsoft-account, kunnen zich niet aanmelden bij die toepassing omdat ze de stap voor het detecteren van de Home-realm overs Laan.  

Er zijn twee manieren om automatische versnelling naar een federatieve IdP te beheren:   

- Gebruik een domein hint op verificatie aanvragen voor een toepassing. 
- Configureer een beleid voor het detecteren van basis-Realms om automatische versnelling in te scha kelen.

### <a name="domain-hints"></a>Domein hints    
Domein hints zijn richt lijnen die zijn opgenomen in de verificatie aanvraag van een toepassing. Ze kunnen worden gebruikt om de gebruiker te versnellen tot hun federatieve IdP-aanmeldings pagina. Of ze kunnen worden gebruikt door een toepassing met meerdere tenants om de gebruiker rechtstreeks naar de aanmeldings pagina van Azure AD te versnellen voor hun Tenant.  

De toepassing ' largeapp.com ' kan bijvoorbeeld de klanten in staat stellen om toegang te krijgen tot de toepassing op een aangepaste URL ' contoso.largeapp.com '. De app kan ook een domein Hint bevatten voor contoso.com in de verificatie aanvraag. 

De syntaxis van de domein Hint is afhankelijk van het gebruikte protocol en is doorgaans geconfigureerd in de toepassing.

**WS-Federation**: w/h = contoso. com in de query teken reeks.

**SAML**: een SAML-verificatie aanvraag die een domein Hint bevat of een query reeks w/of contoso. com.

**Open ID Connect**: een query reeks domain_hint = contoso. com. 

Als een domein Hint is opgenomen in de verificatie aanvraag van de toepassing en de Tenant federatief is met dat domein, probeert Azure AD de aanmelding te omleiden naar de IdP die voor dat domein is geconfigureerd. 

Als de domein Hint niet verwijst naar een geverifieerd federatief domein, wordt dit genegeerd en wordt de normale detectie van de start realm geactiveerd.

Zie de [Enterprise Mobility + Security Blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)voor meer informatie over automatische versnelling met behulp van de domein hints die door Azure Active Directory worden ondersteund.

>[!NOTE]
>Als een domein Hint is opgenomen in een verificatie aanvraag, overschrijft de aanwezigheid een automatische versnelling die is ingesteld voor de toepassing in HRD-beleid.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Beleid voor het detecteren van basis-Realms voor automatische versnelling
Sommige toepassingen bieden geen manier om de verificatie aanvraag te configureren die ze verzenden. In dergelijke gevallen is het niet mogelijk domein hints te gebruiken voor het beheren van automatische versnelling. Automatische versnelling kan via het beleid worden geconfigureerd om hetzelfde gedrag te krijgen.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Directe verificatie inschakelen voor oudere toepassingen
De aanbevolen procedure is voor toepassingen om AAD-bibliotheken en interactieve aanmelding te gebruiken om gebruikers te verifiëren. De bibliotheken zorgen voor de federatieve gebruikers stromen.  Soms worden verouderde toepassingen niet geschreven om de Federatie te begrijpen. Ze voeren geen Home realm-detectie uit en werken niet met het juiste federatieve eind punt om een gebruiker te verifiëren. Als u wilt, kunt u HRD-beleid gebruiken om specifieke verouderde toepassingen in te scha kelen waarmee gebruikers naam/wacht woord referenties worden ingediend om rechtstreeks met Azure Active Directory te verifiëren. Wachtwoord hash-synchronisatie moet zijn ingeschakeld. 

> [!IMPORTANT]
> Schakel directe verificatie alleen in als wachtwoord-hash-synchronisatie is ingeschakeld en u weet dat u deze toepassing kunt verifiëren zonder dat er beleids regels worden geïmplementeerd door uw on-premises IdP. Als u wacht woord-hash synchronisatie uitschakelt of Directory synchronisatie met AD Connect uit te scha kelen om welke reden dan ook, moet u dit beleid verwijderen om te voor komen dat u direct verificatie kunt gebruiken met behulp van een verouderde hash van het wacht woord.

## <a name="set-hrd-policy"></a>HRD-beleid instellen
Er zijn drie stappen voor het instellen van HRD-beleid voor een toepassing voor federatieve aanmelding voor automatische versnelling of directe Cloud toepassingen:

1. Maak een HRD-beleid.

2. Zoek de service-principal waaraan het beleid moet worden gekoppeld.

3. Koppel het beleid aan de Service-Principal. 

Beleids regels worden alleen van kracht voor een specifieke toepassing wanneer ze zijn gekoppeld aan een service-principal. 

Er kan slechts één HRD-beleid tegelijk actief zijn op een service-principal.  

U kunt de Azure Active Directory Power shell-cmdlets gebruiken om HRD-beleid te maken en te beheren.

Hier volgt een voor beeld van een HRD-beleids definitie:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

Het beleids type is ' HomeRealmDiscoveryPolicy '.

**AccelerateToFederatedDomain** is optioneel. Als **AccelerateToFederatedDomain** False is, heeft het beleid geen effect op automatische versnelling. Als **AccelerateToFederatedDomain** is ingesteld op True en er slechts één geverifieerd en federatief domein is in de Tenant, worden gebruikers direct naar de federatieve IDP geleid om zich aan te melden. Als dit het geval is en er meer dan één geverifieerd domein in de Tenant is, moet **PreferredDomain** worden opgegeven.

**PreferredDomain** is optioneel. **PreferredDomain** moet een domein aangeven dat moet worden versneld. Deze kan worden wegge laten als de Tenant slechts één federatief domein heeft.  Als de para meter wordt wegge laten en er meer dan één geverifieerd Federatie domein is, heeft het beleid geen effect.

 Als **PreferredDomain** is opgegeven, moet deze overeenkomen met een geverifieerd, federatief domein voor de Tenant. Alle gebruikers van de toepassing moeten zich kunnen aanmelden bij dat domein.

**AllowCloudPasswordValidation** is optioneel. Als **AllowCloudPasswordValidation** is ingesteld op True, kan de toepassing een federatieve gebruiker verifiëren door referenties voor gebruikers naam/wacht woord rechtstreeks te presen teren aan het eind punt van het Azure Active Directory-token. Dit werkt alleen als wachtwoord-hash-synchronisatie is ingeschakeld.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioriteit en evaluatie van HRD-beleid
HRD-beleid kan worden gemaakt en vervolgens worden toegewezen aan specifieke organisaties en service-principals. Dit betekent dat het mogelijk is dat meerdere beleids regels worden toegepast op een bepaalde toepassing. Het HRD-beleid dat van kracht wordt, volgt deze regels:


- Als de verificatie aanvraag een domein Hint bevat, wordt het HRD-beleid voor automatische versnelling genegeerd. Het gedrag dat door de domein Hint wordt opgegeven, wordt gebruikt.

- Als een beleid expliciet is toegewezen aan de Service-Principal, wordt dit afgedwongen. 

- Als er geen domein Hint is en er geen beleid expliciet is toegewezen aan de Service-Principal, wordt een beleid afgedwongen dat expliciet is toegewezen aan de bovenliggende organisatie van de Service-Principal. 

- Als er geen domein Hint is en er geen beleid is toegewezen aan de service-principal of de organisatie, wordt het standaard HRD-gedrag gebruikt.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Zelf studie voor het instellen van HRD-beleid voor een toepassing 
We gebruiken Azure AD Power shell-cmdlets om enkele scenario's uit te voeren, waaronder:


- Instellen van HRD-beleid voor het automatisch versnellen van een toepassing in een Tenant met één federatief domein.

- Instellen van HRD-beleid voor het automatisch versnellen van een toepassing naar een van de domeinen die worden gecontroleerd voor uw Tenant.

- Instellen van HRD-beleid om ervoor te zorgen dat een verouderde toepassing direct gebruikers naam/wacht woord verificatie kan Azure Active Directory voor een federatieve gebruiker.

- De toepassingen weer geven waarvoor een beleid is geconfigureerd.


### <a name="prerequisites"></a>Vereisten
In de volgende voor beelden maakt, bijwerkt, koppelt en verwijdert u beleids regels op Application Service-principals in azure AD.

1.  Down load de nieuwste preview van Azure AD Power shell-cmdlet om te beginnen. 

2.  Nadat u de Azure AD Power shell-cmdlets hebt gedownload, voert u de opdracht Connect uit om u aan te melden bij Azure AD met uw beheerders account:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Voer de volgende opdracht uit om alle beleids regels in uw organisatie weer te geven:

    ``` powershell
    Get-AzureADPolicy
    ```

Als er niets wordt geretourneerd, betekent dit dat er geen beleid is gemaakt in uw Tenant.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Voor beeld: een HRD-beleid instellen voor een toepassing 

In dit voor beeld maakt u een beleid dat wanneer het wordt toegewezen aan een toepassing, hetzij: 
- Gebruikers worden automatisch naar een AD FS aanmeld scherm versneld wanneer ze zich aanmelden bij een toepassing wanneer er één domein in uw Tenant is. 
- Gebruikers worden automatisch naar een AD FS-aanmeld scherm versneld. er is meer dan een federatief domein in uw Tenant.
- Hiermee kunnen niet-interactieve gebruikers naam/wacht woord worden aangemeld bij Azure Active Directory voor federatieve gebruikers voor de toepassingen waaraan het beleid is toegewezen.

#### <a name="step-1-create-an-hrd-policy"></a>Stap 1: een HRD-beleid maken

Het volgende beleid versnelt gebruikers automatisch naar een AD FS aanmeld scherm wanneer ze zich aanmelden bij een toepassing wanneer er één domein in uw Tenant is.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Het volgende beleid versnelt gebruikers automatisch naar een AD FS aanmeld scherm. er is meer dan een federatief domein in uw Tenant. Als u meer dan één federatief domein hebt dat gebruikers verifieert voor toepassingen, moet u het domein opgeven om automatisch te versnellen.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Voer de volgende opdracht uit om een beleid te maken om gebruikers naam/wacht woord voor federatieve gebruikers rechtstreeks met Azure Active Directory voor specifieke toepassingen in te scha kelen:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Voer de volgende opdracht uit om het nieuwe beleid weer te geven en de **ObjectID**te verkrijgen:

``` powershell
Get-AzureADPolicy
```


Als u het HRD-beleid wilt Toep assen nadat u het hebt gemaakt, kunt u dit toewijzen aan meerdere Application Service-principals.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Stap 2: de Service-Principal zoeken waaraan het beleid moet worden toegewezen  
U hebt de **ObjectID** nodig van de service-principals waaraan u het beleid wilt toewijzen. Er zijn verschillende manieren om de **ObjectID** van service-principals te vinden.    

U kunt de portal gebruiken of u kunt een query uitvoeren op [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). U kunt ook naar het [hulp programma Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) gaan en u aanmelden bij uw Azure ad-account om alle service-principals van uw organisatie weer te geven. 

Omdat u Power shell gebruikt, kunt u de volgende cmdlet gebruiken om de service-principals en hun Id's weer te geven.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Stap 3: wijs het beleid toe aan uw Service-Principal  
Nadat u het **ObjectID** hebt van de service-principal van de toepassing waarvoor u automatische versnelling wilt configureren, voert u de volgende opdracht uit. Met deze opdracht wordt het HRD-beleid dat u in stap 1 hebt gemaakt, gekoppeld aan de service-principal die u in stap 2 hebt gevonden.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

U kunt deze opdracht herhalen voor elke service-principal waaraan u het beleid wilt toevoegen.

In het geval dat er al een HomeRealmDiscovery-beleid aan een toepassing is toegewezen, kunt u geen tweede toevoegen.  In dat geval wijzigt u de definitie van het beleid voor thuis-realm-detectie dat is toegewezen aan de toepassing om aanvullende para meters toe te voegen.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Stap 4: controleren aan welke Application Service-principals uw HRD-beleid is toegewezen
Gebruik de cmdlet **Get-AzureADPolicyAppliedObject** om te controleren voor welke toepassingen hrd-beleid is geconfigureerd. Geef de **ObjectID** door aan het beleid dat u wilt controleren.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Stap 5: u bent klaar.
Voer de toepassing uit om te controleren of het nieuwe beleid werkt.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Voor beeld: de toepassingen weer geven waarvoor het HRD-beleid is geconfigureerd

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Stap 1: alle beleids regels weer geven die in uw organisatie zijn gemaakt 

``` powershell
Get-AzureADPolicy
```

Let op de **ObjectID** van het beleid waarvan u de toewijzingen wilt weer geven.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Stap 2: de service-principals weer geven waaraan het beleid is toegewezen  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Voor beeld: een HRD-beleid uit een toepassing verwijderen
#### <a name="step-1-get-the-objectid"></a>Stap 1: de ObjectID ophalen
Gebruik het vorige voor beeld om de **ObjectID** van het beleid op te halen en van de toepassing Service-Principal waaruit u wilt verwijderen. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Stap 2: de beleids toewijzing van de service-principal van de toepassing verwijderen  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Stap 3: het verwijderen controleren door de service-principals weer te geven waaraan het beleid is toegewezen 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Volgende stappen
- Zie [verificatie scenario's voor Azure AD](../develop/authentication-scenarios.md)voor meer informatie over hoe verificatie werkt in azure AD.
- Zie voor meer informatie over eenmalige aanmelding [voor gebruikers eenmalige aanmelding bij toepassingen in azure Active Directory](what-is-single-sign-on.md).
- Ga naar het [micro soft-identiteits platform](../develop/v2-overview.md) voor een overzicht van alle inhoud die betrekking heeft op ontwikkel aars.
