---
title: Verificatie-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: oplossingen voor bedreigingen die worden blootgesteld aan de Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref
ms.openlocfilehash: 569e8d769d56acbb4c7fb4258952ec19e44b58e4
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607823"
---
# <a name="security-frame-authentication--mitigations"></a>Beveiligings frame: verificatie | Oplossingen

| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing**    | <ul><li>[Overweeg het gebruik van een standaard verificatie mechanisme om te verifiëren bij de webtoepassing](#standard-authn-web-app)</li><li>[Toepassingen moeten mislukte verificatie scenario's veilig afhandelen](#handle-failed-authn)</li><li>[Stapsgewijze of adaptieve verificatie inschakelen](#step-up-adaptive-authn)</li><li>[Zorg ervoor dat de beheer interfaces op de juiste manier worden vergrendeld](#admin-interface-lockdown)</li><li>[Veilig verg eten wachtwoord functionaliteiten implementeren](#forgot-pword-fxn)</li><li>[Zorg ervoor dat wacht woord-en account beleid worden geïmplementeerd](#pword-account-policy)</li><li>[Besturings elementen implementeren om te voor komen dat gebruikers naam inventarisatie](#controls-username-enum)</li></ul> |
| **Enddatabase** | <ul><li>[Gebruik, indien mogelijk, Windows-verificatie om verbinding te maken met SQL Server](#win-authn-sql)</li><li>[Gebruik, indien mogelijk, Azure Active Directory verificatie om verbinding te maken met SQL Database](#aad-authn-sql)</li><li>[Wanneer de SQL-verificatie modus wordt gebruikt, moet u ervoor zorgen dat het account en het wachtwoord beleid worden afgedwongen op SQL Server](#authn-account-pword)</li><li>[Geen SQL-verificatie gebruiken in Inge sloten data bases](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Authenticatie referenties per apparaat gebruiken met SaS-tokens](#authn-sas-tokens)</li></ul> |
| **Azure-vertrouwens grens** | <ul><li>[Azure-Multi-Factor Authentication voor Azure-beheerders inschakelen](#multi-factor-azure-admin)</li></ul> |
| **Grens van Service Fabric vertrouwen** | <ul><li>[Anonieme toegang tot Service Fabric cluster beperken](#anon-access-cluster)</li><li>[Zorg ervoor dat Service Fabric client-naar-knoop punt-certificaat verschilt van knoop punt-naar-knoop punt](#fabric-cn-nn)</li><li>[AAD gebruiken om clients te verifiëren voor service Fabric-clusters](#aad-client-fabric)</li><li>[Zorg ervoor dat service Fabric-certificaten worden verkregen van een goedgekeurde certificerings instantie (CA)](#fabric-cert-ca)</li></ul> |
| **Identiteits server** | <ul><li>[Standaard verificatie scenario's gebruiken die worden ondersteund door de identiteits server](#standard-authn-id)</li><li>[De standaard token cache van de identiteits server overschrijven met een schaalbaar alternatief](#override-token)</li></ul> |
| **Grens van computer vertrouwen** | <ul><li>[Zorg ervoor dat de binaire bestanden van de geïmplementeerde toepassing digitaal zijn ondertekend](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Verificatie inschakelen bij het maken van verbinding met MSMQ-wacht rijen in WCF](#msmq-queues)</li><li>[WCF: Stel Message clientCredentialType niet in op none](#message-none)</li><li>[WCF: Stel Trans Port clientCredentialType niet in op geen](#transport-none)</li></ul> |
| **Web-API** | <ul><li>[Zorg ervoor dat de standaard verificatie technieken worden gebruikt voor het beveiligen van web-Api's](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Standaard verificatie scenario's gebruiken die worden ondersteund door Azure Active Directory](#authn-aad)</li><li>[De standaard ADAL-token cache overschrijven met een schaalbaar alternatief](#adal-scalable)</li><li>[Zorg ervoor dat TokenReplayCache wordt gebruikt om het opnieuw afspelen van ADAL-verificatie tokens te voor komen](#tokenreplaycache-adal)</li><li>[ADAL-bibliotheken gebruiken voor het beheren van token aanvragen van OAuth2-clients naar AAD (of on-premises AD)](#adal-oauth2)</li></ul> |
| **IoT-veld Gateway** | <ul><li>[Apparaten verifiëren die verbinding maken met de veld Gateway](#authn-devices-field)</li></ul> |
| **IoT-Cloud gateway** | <ul><li>[Zorg ervoor dat apparaten die verbinding maken met de Cloud gateway worden geverifieerd](#authn-devices-cloud)</li><li>[Authenticatie referenties per apparaat gebruiken](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Zorg ervoor dat alleen anonieme lees toegang wordt verleend aan de vereiste containers en blobs](#req-containers-anon)</li><li>[Beperkte toegang verlenen tot objecten in azure Storage met SAS of SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Overweeg het gebruik van een standaard verificatie mechanisme om te verifiëren bij de webtoepassing

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Verificatie is het proces waarbij een entiteit zijn identiteit bewijst, meestal via referenties, zoals een gebruikers naam en wacht woord. Er zijn meerdere verificatie protocollen beschikbaar die kunnen worden overwogen. Enkele hiervan worden hieronder weer gegeven:</p><ul><li>Client certificaten</li><li>Op Windows gebaseerd</li><li>Formulieren op basis</li><li>Federatie-ADFS</li><li>Federatie-Azure AD</li><li>Federatie-identiteits server</li></ul><p>Overweeg het gebruik van een standaard verificatie mechanisme om het bron proces te identificeren</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Toepassingen moeten mislukte verificatie scenario's veilig afhandelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Toepassingen die gebruikers expliciet verifiëren, moeten mislukte verificatie scenario's veilig afhandelen. Het verificatie mechanisme moet:</p><ul><li>Toegang weigeren tot geprivilegieerde bronnen wanneer authenticatie mislukt</li><li>Een algemeen fout bericht weer geven na mislukte verificatie en de toegang is geweigerd</li></ul><p>Testen voor:</p><ul><li>Beveiliging van beschermde bronnen na mislukte aanmeldingen</li><li>Er wordt een algemeen fout bericht weer gegeven bij mislukte verificatie en geweigerde toegang (en)</li><li>Accounts zijn uitgeschakeld na een uitzonderlijk aantal mislukte pogingen</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Stapsgewijze of adaptieve verificatie inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Controleer of de toepassing extra autorisatie heeft (zoals een stap omhoog of adaptieve authenticatie via multi-factor Authentication, zoals het verzenden van OTP in SMS, e-mail enz. of het vragen om een nieuwe verificatie), zodat de gebruiker wordt gevraagd voordat toegang tot gevoelige informatie wordt verleend. Deze regel geldt ook voor het aanbrengen van belang rijke wijzigingen aan een account of actie</p><p>Dit betekent ook dat de aanpassing van verificatie moet worden geïmplementeerd op een zodanige manier dat de toepassing context gevoelige autorisatie correct afdwingt, zodat niet-geautoriseerde manipulatie door middel van voor beeld wordt geknoeid.</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Zorg ervoor dat de beheer interfaces op de juiste manier worden vergrendeld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | De eerste oplossing is om alleen toegang te verlenen vanuit een bepaald bron-IP-bereik aan de beheer interface. Als deze oplossing niet mogelijk zou zijn, dan wordt altijd aanbevolen voor het afdwingen van een stapsgewijze of adaptieve verificatie voor het aanmelden bij de beheer interface |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Veilig verg eten wachtwoord functionaliteiten implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Het eerste is om te controleren of verg eten wacht woord en andere herstel paden een koppeling verzenden met een tijds limiet voor activering, in plaats van het wacht woord zelf. Extra authenticatie op basis van zachte tokens (bijvoorbeeld SMS-token, systeem eigen mobiele toepassingen, enzovoort) kan worden vereist, ook voordat de koppeling wordt verzonden. Ten tweede moet u het gebruikers account niet vergren delen terwijl het proces van het ophalen van een nieuw wacht woord wordt uitgevoerd.</p><p>Dit kan leiden tot een denial-of-service-aanval wanneer een aanvaller de gebruikers opzettelijk wilt vergren delen met een geautomatiseerde aanval. Ten derde, wanneer de nieuwe wachtwoord aanvraag wordt ingesteld, moet het bericht dat u weergeeft, worden gegeneraliseerd om de inventarisatie van gebruikers te voor komen. Ten vierde maakt u het gebruik van oude wacht woorden altijd niet toe en implementeert u een sterk wachtwoord beleid.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Zorg ervoor dat wacht woord-en account beleid worden geïmplementeerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Wacht woord-en account beleid in overeenstemming met het beleid van de organisatie en aanbevolen procedures moeten worden geïmplementeerd.</p><p>Om te beschermen tegen brute kracht-en woordenboek woorden: er moet een beleid voor sterke wacht woorden worden geïmplementeerd om ervoor te zorgen dat gebruikers complex wacht woord maken (bijvoorbeeld 12 tekens minimum lengte, alfanumerieke tekens en speciale letters).</p><p>Account vergrendelings beleid kan op de volgende manier worden geïmplementeerd:</p><ul><li>**Zachte uitsluiting:** Dit kan een goede optie zijn om uw gebruikers te beschermen tegen beveiligings aanvallen. Als de gebruiker bijvoorbeeld drie keer een onjuist wacht woord invoert, kan de toepassing het account voor een minuut vergren delen om het proces van het afdwingen van het wacht woord te vertragen, waardoor de aanvaller minder winstgevend wordt. Als u voor dit voor beeld hard lock-out zou implementeren, kunt u een ' DoS ' realiseren door accounts permanent te vergren delen. De toepassing kan ook een OTP (eenmalig wacht woord) genereren en deze out-of-band (via e-mail, SMS enz.) naar de gebruiker verzenden. Een andere benadering is het implementeren van CAPTCHA nadat een drempel waarde voor het aantal mislukte pogingen is bereikt.</li><li>**Harde vergren deling:** Dit type vergren deling moet worden toegepast wanneer u een gebruiker detecteert die zich aanmeldt voor uw toepassing en deze opwaardeert met behulp van het permanent vergren delen van hun account totdat een antwoord team een tijd had om hun forensische te doen. Na dit proces kunt u beslissen of u de gebruiker een eigen account wilt geven of dat u verdere juridische acties wilt ondernemen. Dit type aanpak voor komt dat de aanvaller uw toepassing en infra structuur verder doordringt.</li></ul><p>Om te beschermen tegen aanvallen op standaard-en voorspel bare accounts, controleert u of alle sleutels en wacht woorden opnieuw plaatsbaar zijn en of deze na de installatie worden gegenereerd of vervangen.</p><p>Als de toepassing automatisch wacht woorden moet genereren, moet u ervoor zorgen dat de gegenereerde wacht woorden wille keurig zijn en een hoge entropie hebben.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Besturings elementen implementeren om te voor komen dat gebruikers naam inventarisatie

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Alle fout berichten moeten worden gegeneraliseerd om de inventarisatie van gebruikers te voor komen. Soms is het ook niet mogelijk om te voor komen dat gegevens die in de functies zoals een registratie pagina lekken, worden gelekt. Hier moet u gebruikmaken van methoden voor het beperken van de frequentie zoals CAPTCHA om te voor komen dat een aanvaller een geautomatiseerde aanval doet. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Gebruik, indien mogelijk, Windows-verificatie om verbinding te maken met SQL Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Premises |
| **Kenmerken**              | SQL-versie-alle |
| **Verwijzingen**              | [SQL Server: een verificatie modus kiezen](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Stappen** | Windows-verificatie maakt gebruik van het Kerberos-beveiligings protocol, voorziet in het afdwingen van wachtwoord beleid met betrekking tot complexiteits validatie voor sterke wacht woorden, biedt ondersteuning voor account vergrendeling en ondersteunt het verlopen van wacht woorden.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Gebruik, indien mogelijk, Azure Active Directory verificatie om verbinding te maken met SQL Database

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | SQL Azure |
| **Kenmerken**              | SQL-versie-V12 |
| **Verwijzingen**              | [Verbinding maken met SQL Database met behulp van Azure Active Directory-verificatie](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Stappen** | **Minimum versie:** Azure SQL Database V12 vereist om in te stellen Azure SQL Database AAD-verificatie te gebruiken voor de micro soft-Directory |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Wanneer de SQL-verificatie modus wordt gebruikt, moet u ervoor zorgen dat het account en het wachtwoord beleid worden afgedwongen op SQL Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [SQL Server wachtwoord beleid](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Stappen** | Bij het gebruik van SQL Server-verificatie worden aanmeldingen gemaakt in SQL Server die niet zijn gebaseerd op Windows-gebruikers accounts. De gebruikers naam en het wacht woord worden gemaakt met behulp van SQL Server en opgeslagen in SQL Server. SQL Server kunt gebruikmaken van mechanismen voor Windows-wachtwoord beleid. Het kan hetzelfde complexiteits-en verloop beleid dat wordt gebruikt in Windows Toep assen op wacht woorden die worden gebruikt in SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Geen SQL-verificatie gebruiken in Inge sloten data bases

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Premises, SQL Azure |
| **Kenmerken**              | SQL-versie-MSSQL2012, SQL-versie-V12 |
| **Verwijzingen**              | [Aanbevolen beveiligings procedures met Inge sloten data bases](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Stappen** | Als er geen afgedwongen wachtwoord beleid is ingesteld, kan de kans op een zwakke referentie in een Inge sloten data base toenemen. Gebruik Windows-verificatie. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Authenticatie referenties per apparaat gebruiken met SaS-tokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Event Hub |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Overzicht van Event Hubs verificatie en beveiligings model](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Stappen** | <p>Het beveiligings model van Event Hubs is gebaseerd op een combi natie van Shared Access Signature (SAS)-tokens en gebeurtenis uitgevers. De naam van de uitgever vertegenwoordigt de DeviceID die het token ontvangt. Dit helpt bij het koppelen van de tokens die zijn gegenereerd met de betreffende apparaten.</p><p>Alle berichten worden gelabeld met de maker aan de service zijde die de detectie van in-nettolading vervalste pogingen toestaat. Genereer bij het verifiëren van apparaten een SaS-Token per apparaat binnen het bereik van een unieke Uitgever.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Azure-Multi-Factor Authentication voor Azure-beheerders inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure-vertrouwens grens |
| **SDL-fase**               | Implementatie |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Wat is Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Stappen** | <p>Multi-factor Authentication (MFA) is een verificatie methode waarbij meer dan één verificatie methode is vereist en waarmee een kritieke tweede beveiligingslaag wordt toegevoegd aan gebruikers aanmeldingen en trans acties. Het werkt door twee of meer van de volgende verificatie methoden te vereisen:</p><ul><li>Iets dat u kent (doorgaans een wacht woord)</li><li>Iets dat u hebt (een vertrouwd apparaat dat niet eenvoudig kan worden gedupliceerd, zoals een telefoon)</li><li>Iets dat u bent (biometrie)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Anonieme toegang tot Service Fabric cluster beperken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van Service Fabric vertrouwen |
| **SDL-fase**               | Implementatie |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving-Azure  |
| **Verwijzingen**              | [Beveiligings scenario's voor Service Fabric cluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Stappen** | <p>Clusters moeten altijd worden beveiligd om te voor komen dat onbevoegde gebruikers verbinding maken met uw cluster, met name wanneer er productie werkbelastingen worden uitgevoerd.</p><p>Zorg er tijdens het maken van een service Fabric-cluster voor dat de beveiligings modus is ingesteld op ' beveiligd ' en configureer het vereiste X. 509-server certificaat. Als u een ' onveilige ' cluster maakt, kunt u een anonieme gebruiker verbinding laten maken als het beheer eindpunten voor het open bare internet beschikbaar stelt.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Zorg ervoor dat Service Fabric client-naar-knoop punt-certificaat verschilt van knoop punt-naar-knoop punt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van Service Fabric vertrouwen |
| **SDL-fase**               | Implementatie |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving-Azure, omgeving-zelfstandig |
| **Verwijzingen**              | [Client-naar-knoop punt-certificaat beveiliging service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [verbinding maken met een beveiligd cluster met behulp van client certificaat](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Stappen** | <p>De beveiliging van het client-naar-knoop punt wordt geconfigureerd tijdens het maken van het cluster via de Azure Portal, Resource Manager-sjablonen of een zelfstandige JSON-sjabloon door het opgeven van een admin-client certificaat en/of een gebruikers-client certificaat.</p><p>De client certificaten voor de client en de gebruiker die u opgeeft, moeten verschillen van de primaire en secundaire certificaten die u opgeeft voor de beveiliging van knoop punten.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>AAD gebruiken om clients te verifiëren voor service Fabric-clusters

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van Service Fabric vertrouwen |
| **SDL-fase**               | Implementatie |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving-Azure |
| **Verwijzingen**              | [Scenario's voor cluster beveiliging-aanbevelingen voor beveiliging](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Stappen** | Clusters die op Azure worden uitgevoerd, kunnen ook de toegang tot de beheer eindpunten beveiligen met behulp van Azure Active Directory (AAD), naast client certificaten. Voor Azure-clusters is het raadzaam om AAD-beveiliging te gebruiken voor het verifiëren van clients en certificaten voor beveiliging tussen knoop punten.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Zorg ervoor dat service Fabric-certificaten worden verkregen van een goedgekeurde certificerings instantie (CA)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van Service Fabric vertrouwen |
| **SDL-fase**               | Implementatie |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving-Azure |
| **Verwijzingen**              | [X. 509-certificaten en-Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Stappen** | <p>Service Fabric maakt gebruik van X. 509-server certificaten voor het verifiëren van knoop punten en clients.</p><p>Enkele belang rijke aandachtspunten bij het gebruik van certificaten in service fabrics:</p><ul><li>Certificaten die worden gebruikt in clusters die productie werkbelastingen uitvoeren, moeten worden gemaakt met een correct geconfigureerde Windows Server Certificate-Service of zijn verkregen van een goedgekeurde certificerings instantie (CA). De CA kan een goedgekeurde externe CA of een goed beheerde, open bare-sleutel infrastructuur (PKI) zijn.</li><li>Gebruik nooit tijdelijke of test certificaten in productie die zijn gemaakt met hulpprogram ma's zoals MakeCert. exe</li><li>U kunt een zelfondertekend certificaat gebruiken, maar dit mag alleen voor test clusters zijn en niet voor productie doeleinden</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Standaard verificatie scenario's gebruiken die worden ondersteund door de identiteits server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteits server |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [IdentityServer3-de grote afbeelding](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Stappen** | <p>Hieronder vindt u de typische interacties die worden ondersteund door de identiteits server:</p><ul><li>Browsers communiceren met webtoepassingen</li><li>Webtoepassingen communiceren met Web-Api's (soms namens een gebruiker)</li><li>Toepassingen die zijn gebaseerd op de browser communiceren met Web-Api's</li><li>Systeem eigen toepassingen communiceren met Web-Api's</li><li>Server toepassingen communiceren met Web-Api's</li><li>Web-Api's communiceren met Web-Api's (soms namens een gebruiker)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>De standaard token cache van de identiteits server overschrijven met een schaalbaar alternatief

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteits server |
| **SDL-fase**               | Implementatie |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Identity Server-implementatie-caching](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Stappen** | <p>IdentityServer heeft een eenvoudige ingebouwde in-memory cache. Hoewel dit goed is voor kleinschalige, systeem eigen apps, wordt deze niet geschaald voor de volgende redenen:</p><ul><li>Deze toepassingen zijn toegankelijk voor veel gebruikers tegelijk. Als u alle toegangs tokens in hetzelfde archief opslaat, worden er isolatie problemen gemaakt en worden er uitdagingen weer gegeven bij het uitvoeren van een schaal: veel gebruikers, elk met zoveel tokens als de resources die de app namens u toegang geeft, kunnen grote aantallen en zeer dure opzoek bewerkingen betekenen.</li><li>Deze toepassingen worden doorgaans geïmplementeerd op gedistribueerde topologieën, waarbij meerdere knoop punten toegang moeten hebben tot dezelfde cache</li><li>Tokens in de cache moeten worden gerecycled en processen worden gedeactiveerd</li><li>Tijdens de implementatie van web-apps wordt het aanbevolen om de token cache van de standaard identiteits server te vervangen door een schaalbaar alternatief zoals Azure cache voor redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Zorg ervoor dat de binaire bestanden van de geïmplementeerde toepassing digitaal zijn ondertekend

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van computer vertrouwen |
| **SDL-fase**               | Implementatie |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat de binaire bestanden van de geïmplementeerde toepassing digitaal zijn ondertekend, zodat de integriteit van de binaire bestanden kan worden geverifieerd|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Verificatie inschakelen bij het maken van verbinding met MSMQ-wacht rijen in WCF

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen, NET Framework 3 |
| **Kenmerken**              | N.v.t. |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Stappen** | Het programma kan geen verificatie inschakelen bij het maken van verbinding met MSMQ-wacht rijen, een aanvaller kan anoniem berichten verzenden naar de wachtrij voor verwerking. Als verificatie niet wordt gebruikt om verbinding te maken met een MSMQ-wachtrij die wordt gebruikt om een bericht te verzenden naar een ander programma, kan een aanvaller een schadelijk, anoniem bericht indienen.|

### <a name="example"></a>Voorbeeld
Het `<netMsmqBinding/>` element van het WCF-configuratie bestand hieronder geeft WCF de verificatie uitschakelen wanneer er verbinding wordt gemaakt met een MSMQ-wachtrij voor bericht bezorging.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Configureer MSMQ zodanig dat er altijd Windows-domein-of certificaat authenticatie is vereist voor binnenkomende of uitgaande berichten.

### <a name="example"></a>Voorbeeld
Het `<netMsmqBinding/>` element van het WCF-configuratie bestand hieronder geeft WCF de mogelijkheid om verificatie van certificaten in te scha kelen wanneer er verbinding wordt gemaakt met een MSMQ-wachtrij. De client wordt geverifieerd met X. 509-certificaten. Het client certificaat moet aanwezig zijn in het certificaat archief van de server.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF: Stel Message clientCredentialType niet in op none

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | Type client referentie-geen |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Stappen** | Het ontbreken van verificatie houdt in dat iedereen toegang heeft tot deze service. Een service die de clients niet verifieert, biedt toegang tot alle gebruikers. Configureer de toepassing voor verificatie op basis van client referenties. U kunt dit doen door het bericht clientCredentialType in te stellen op Windows of certificaat. |

### <a name="example"></a>Voorbeeld
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF: Stel Trans Port clientCredentialType niet in op geen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen, .NET Framework 3 |
| **Kenmerken**              | Type client referentie-geen |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Stappen** | Het ontbreken van verificatie houdt in dat iedereen toegang heeft tot deze service. Een service die de clients niet verifieert, stelt alle gebruikers in staat om toegang te krijgen tot de functionaliteit. Configureer de toepassing voor verificatie op basis van client referenties. U kunt dit doen door de transport clientCredentialType in te stellen op Windows of certificaat. |

### <a name="example"></a>Voorbeeld
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Zorg ervoor dat de standaard verificatie technieken worden gebruikt voor het beveiligen van web-Api's

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Verificatie en autorisatie in ASP.net Web-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [externe verificatie services met ASP.net Web API (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Stappen** | <p>Verificatie is het proces waarbij een entiteit zijn identiteit bewijst, meestal via referenties, zoals een gebruikers naam en wacht woord. Er zijn meerdere verificatie protocollen beschikbaar die kunnen worden overwogen. Enkele hiervan worden hieronder weer gegeven:</p><ul><li>Client certificaten</li><li>Op Windows gebaseerd</li><li>Formulieren op basis</li><li>Federatie-ADFS</li><li>Federatie-Azure AD</li><li>Federatie-identiteits server</li></ul><p>Koppelingen in de sectie verwijzingen bieden Details op laag niveau over hoe elk van de verificatie schema's kan worden geïmplementeerd om een web-API te beveiligen.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Standaard verificatie scenario's gebruiken die worden ondersteund door Azure Active Directory

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Verificatie scenario's voor Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory Code voorbeelden](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Azure Active Directory hand leiding voor ontwikkel aars](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Stappen** | <p>Azure Active Directory (Azure AD) vereenvoudigt de verificatie voor ontwikkel aars door identiteit als service te leveren, met ondersteuning voor de industrie standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect. Hieronder vindt u de vijf scenario's voor de primaire toepassing die worden ondersteund door Azure AD:</p><ul><li>Webbrowser voor webtoepassing: een gebruiker moet zich aanmelden bij een webtoepassing die wordt beveiligd door Azure AD</li><li>Toepassing met één pagina (SPA): een gebruiker moet zich aanmelden bij een toepassing met één pagina die is beveiligd met Azure AD</li><li>Systeem eigen toepassing op Web-API: een systeem eigen toepassing die wordt uitgevoerd op een telefoon, tablet of PC moet een gebruiker verifiëren om bronnen te verkrijgen van een web-API die wordt beveiligd door Azure AD</li><li>Web Application to Web API: een webtoepassing moet resources ophalen van een web-API die wordt beveiligd door Azure AD</li><li>Daemon of server toepassing op Web-API: een daemon-toepassing of een server toepassing zonder webinterface moet resources ophalen van een web-API die wordt beveiligd door Azure AD</li></ul><p>Raadpleeg de koppelingen in de sectie met verwijzingen voor details over implementatie op laag niveau</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>De standaard ADAL-token cache overschrijven met een schaalbaar alternatief

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Moderne verificatie met Azure Active Directory voor webtoepassingen](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [met behulp van redis als ADAL-token cache](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Stappen** | <p>De standaard cache die ADAL (Active Directory Authentication Library) gebruikt, is een in-memory cache die afhankelijk is van een statische opslag, beschik bare proces breedte. Hoewel dit werkt voor systeem eigen toepassingen, kan het niet worden geschaald voor de volgende redenen:</p><ul><li>Deze toepassingen zijn toegankelijk voor veel gebruikers tegelijk. Als u alle toegangs tokens in hetzelfde archief opslaat, worden er isolatie problemen gemaakt en worden er uitdagingen weer gegeven bij het uitvoeren van een schaal: veel gebruikers, elk met zoveel tokens als de resources die de app namens u toegang geeft, kunnen grote aantallen en zeer dure opzoek bewerkingen betekenen.</li><li>Deze toepassingen worden doorgaans geïmplementeerd op gedistribueerde topologieën, waarbij meerdere knoop punten toegang moeten hebben tot dezelfde cache</li><li>Tokens in de cache moeten worden gerecycled en processen worden gedeactiveerd</li></ul><p>Om de bovenstaande redenen, tijdens het implementeren van web-apps, wordt aangeraden de standaard ADAL-token cache te vervangen door een schaalbaar alternatief zoals Azure cache voor redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Zorg ervoor dat TokenReplayCache wordt gebruikt om het opnieuw afspelen van ADAL-verificatie tokens te voor komen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Moderne verificatie met Azure Active Directory voor webtoepassingen](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Stappen** | <p>Met de eigenschap TokenReplayCache kunnen ontwikkel aars een replay-cache voor tokens definiëren, een archief dat kan worden gebruikt voor het opslaan van tokens om te controleren of er meerdere keren geen tokens kunnen worden gebruikt.</p><p>Dit is een meting tegen een veelvoorkomende aanval, de aptly-aanval (token replay). een aanvaller die het token onderschept dat bij het aanmelden wordt verzonden, kan proberen dit opnieuw te verzenden naar de app (' replay ') om een nieuwe sessie tot stand te brengen. Bijvoorbeeld, in OIDC-toewijzings stroom, na geslaagde gebruikers verificatie, wordt een aanvraag voor het eind punt '/signin-oidc ' van de Relying Party gemaakt met de para meters ' id_token ', ' code ' en ' state '.</p><p>De Relying Party valideert deze aanvraag en brengt een nieuwe sessie tot stand. Als een kwaadwillende persoon deze aanvraag vastlegt en deze opnieuw afspeelt, kan hij/zij een geslaagde sessie tot stand brengen en de gebruiker vervalsen. De aanwezigheid van de nonce in OpenID Connect Connect kan de omstandigheden waarin de aanval kan worden uitgevoerd, beperken, maar niet volledig elimineren. Ontwikkel aars kunnen hun toepassingen beveiligen door een implementatie van ITokenReplayCache te leveren en een exemplaar toe te wijzen aan TokenReplayCache.</p>|

### <a name="example"></a>Voorbeeld
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Voorbeeld
Hier volgt een voor beeld van de implementatie van de ITokenReplayCache-interface. (U kunt uw project-specifieke cache-Framework aanpassen)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Naar de geïmplementeerde cache moet worden verwezen in OIDC-opties via de eigenschap ' TokenValidationParameters ' als volgt.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Let op: als u de effectiviteit van deze configuratie wilt testen, meldt u zich aan bij uw lokale met OIDC beveiligde `"/signin-oidc"` toepassing en legt u de aanvraag voor eind punt in Fiddler vast. Wanneer de beveiliging niet aanwezig is, wordt door het opnieuw afspelen van deze aanvraag in Fiddler een nieuwe sessie cookie ingesteld. Wanneer de aanvraag opnieuw wordt afgespeeld nadat de TokenReplayCache-beveiliging is toegevoegd, wordt de volgende uitzonde ring gegenereerd door de toepassing:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>ADAL-bibliotheken gebruiken voor het beheren van token aanvragen van OAuth2-clients naar AAD (of on-premises AD)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Stappen** | <p>Met de Azure AD Authentication Library (ADAL) kunnen ontwikkel aars van client toepassingen eenvoudig gebruikers verifiëren voor Cloud-of on-premises Active Directory (AD) en vervolgens toegangs tokens verkrijgen voor het beveiligen van API-aanroepen.</p><p>ADAL heeft een groot aantal functies die de verificatie vereenvoudigen voor ontwikkel aars, zoals asynchrone ondersteuning, een Configureer bare token cache waarmee toegangs tokens worden opgeslagen en tokens worden vernieuwd, automatische token vernieuwing wanneer een toegangs token verloopt en er een vernieuwings token beschikbaar is, en meer.</p><p>Door de meeste complexiteit te verwerken, kan ADAL een ontwikkelaar helpen zich te richten op de bedrijfs logica in hun toepassing en eenvoudig bronnen beveiligen zonder een expert op het niveau van beveiliging. Er zijn afzonderlijke bibliotheken beschikbaar voor .NET, java script (client en node. js), Python, iOS, Android en Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Apparaten verifiëren die verbinding maken met de veld Gateway

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-veld Gateway |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat elk apparaat wordt geverifieerd door de veld Gateway voordat er gegevens worden geaccepteerd en voordat u de communicatie met de Cloud gateway vergemakkelijkt. Zorg er ook voor dat apparaten verbinding maken met een per apparaat referentie zodat afzonderlijke apparaten uniek kunnen worden geïdentificeerd.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Zorg ervoor dat apparaten die verbinding maken met de Cloud gateway worden geverifieerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-Cloud gateway |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen, C#, node. JS,  |
| **Kenmerken**              | N.v.t. gateway keuze-Azure IoT Hub |
| **Verwijzingen**              | N.v.t., [Azure IOT hub met .net](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), aan de [slag met IOT hub en node js](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [IOT beveiligen met SAS en certificaten](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Git-opslag plaats](https://github.com/Azure/azure-iot-sdks/) |
| **Stappen** | <ul><li>**Algemeen:** Verifieer het apparaat met behulp van Transport Layer Security (TLS) of IPSec. Infra structuur moet ondersteuning bieden voor het gebruik van een vooraf gedeelde sleutel (PSK) op de apparaten die geen volledige asymmetrische crypto grafie kunnen verwerken. Gebruik Azure AD, OAuth.</li><li>**C#:** Wanneer u een DeviceClient-exemplaar maakt, maakt de methode Create standaard een DeviceClient-exemplaar dat het AMQP-protocol gebruikt om te communiceren met IoT Hub. Als u het HTTPS-protocol wilt gebruiken, dient u de Create-methode te overschrijven. Zo kunt u zelf het protocol bepalen. Als u het HTTPS-protocol gebruikt, moet u ook het `Microsoft.AspNet.WebApi.Client` NuGet-pakket toevoegen aan uw project om `System.Net.Http.Formatting` de naam ruimte op te kunnen bevatten.</li></ul>|

### <a name="example"></a>Voorbeeld
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Voorbeeld
**Node. JS: verificatie**
#### <a name="symmetric-key"></a>Symmetrische sleutel
* Een IoT-hub maken in azure
* Een item maken in het apparaat-id-REGI ster
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Een gesimuleerd apparaat maken
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS-token
* Wordt intern gegenereerd bij het gebruik van symmetrische sleutels, maar we kunnen deze expliciet genereren en gebruiken
* Een protocol definiëren:`var Http = require('azure-iot-device-http').Http;`
* Een SAS-token maken:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Verbinding maken met SAS-token:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Certificaten
* Genereer een zelfondertekend x509-certificaat met een hulp programma zoals OpenSSL om een. cert-en. key-bestand te genereren om het certificaat en de sleutel respectievelijk op te slaan
* Richt een apparaat in dat beveiligde verbinding met behulp van certificaten accepteert.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Een apparaat verbinden met een certificaat
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Authenticatie referenties per apparaat gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-Cloud gateway  |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Gateway keuze-Azure IoT Hub |
| **Verwijzingen**              | [Beveiligings tokens van Azure IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Stappen** | Gebruik verificatie gegevens per apparaat met SaS-tokens op basis van de apparaatcode of het client certificaat in plaats van IoT Hub beleid voor gedeelde toegang. Zo voor komt u dat verificatie tokens van één apparaat-of veld Gateway door een ander worden gebruikt |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Zorg ervoor dat alleen anonieme lees toegang wordt verleend aan de vereiste containers en blobs

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Para-BLOB |
| **Verwijzingen**              | [Anonieme lees toegang tot containers en blobs beheren](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [hand tekeningen voor gedeelde toegang, deel 1: informatie over het SAS-model](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Stappen** | <p>Standaard kan een container en alle blobs in deze alleen worden geopend door de eigenaar van het opslag account. Om anonieme gebruikers lees machtigingen te geven voor een container en de bijbehorende blobs, kan één de container machtigingen instellen om open bare toegang toe te staan. Anonieme gebruikers kunnen blobs lezen binnen een openbaar toegankelijke container zonder de aanvraag te verifiëren.</p><p>Containers bieden de volgende opties voor het beheren van toegang tot de container:</p><ul><li>Volledige open bare Lees toegang: container-en BLOB-gegevens kunnen worden gelezen via anonieme aanvragen. Clients kunnen blobs in de container opsommen via een anonieme aanvraag, maar kunnen containers in het opslag account niet inventariseren.</li><li>Open bare Lees toegang alleen voor blobs: BLOB-gegevens in deze container kunnen worden gelezen via anonieme aanvragen, maar er zijn geen container gegevens beschikbaar. Clients kunnen geen blobs in de container opsommen via anonieme aanvraag</li><li>Geen open bare Lees toegang: container-en BLOB-gegevens kunnen alleen worden gelezen door de account eigenaar</li></ul><p>Anonieme toegang is het meest geschikt voor scenario's waarbij bepaalde blobs altijd beschikbaar moeten zijn voor anonieme lees toegang. Voor een nauw keurig beheer kunt u een gedeelde toegangs handtekening maken, waarmee u beperkte toegang kunt delegeren met behulp van verschillende machtigingen en een opgegeven tijds interval. Zorg ervoor dat containers en blobs, die mogelijk gevoelige gegevens bevatten, geen anonieme toegang per ongeluk hebben</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Beperkte toegang verlenen tot objecten in azure Storage met SAS of SAP

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage |
| **SDL-fase**               | Ontwikkelen |
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t. |
| **Verwijzingen**              | [Hand tekeningen voor gedeelde toegang, deel 1: uitleg over het SAS-model](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [Shared Access Signatures, deel 2: een SAS maken en gebruiken met Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [toegang tot objecten in uw account delegeren met behulp van hand tekeningen voor gedeelde toegang en opgeslagen toegangs beleid](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Stappen** | <p>Het gebruik van een Shared Access Signature (SAS) is een krachtige manier om beperkte toegang tot objecten in een opslag account toe te kennen aan andere clients, zonder dat de toegangs sleutel voor het account hoeft te worden blootgesteld. De SAS is een URI die in de query parameters omvat alle informatie die nodig is voor geverifieerde toegang tot een opslag resource. Om toegang te krijgen tot opslag bronnen met de SAS, hoeft de client alleen de SA'S door te geven aan de juiste constructor of methode.</p><p>U kunt een SAS gebruiken als u toegang wilt verlenen tot resources in uw opslag account naar een client die niet kan worden vertrouwd met de account sleutel. De sleutels van uw opslag account zijn zowel een primaire als een secundaire sleutel, beide waarmee beheerders toegang tot uw account en alle resources erin worden verleend. Door een van uw account sleutels beschikbaar te maken, wordt uw account geopend met de mogelijkheid om kwaad aardig of onachtzaamheid te gebruiken. Shared Access signatures bieden een veilig alternatief waarmee andere clients gegevens in uw opslag account kunnen lezen, schrijven en verwijderen op basis van de machtigingen die u hebt verleend, en zonder dat de account sleutel nodig is.</p><p>Als u een logische set para meters hebt die vergelijkbaar zijn, is het gebruik van een opgeslagen toegangs beleid (SAP) een beter idee. Omdat het gebruik van een SAS die is afgeleid van een opgeslagen toegangs beleid, u de mogelijkheid biedt om die SA'S onmiddellijk in te trekken, is het de aanbevolen best practice om altijd opgeslagen toegangs beleid te gebruiken wanneer dat mogelijk is.</p>|
