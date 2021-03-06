---
title: Beschrijvingen en machtigingen van beheerdersrol-Azure AD | Microsoft Docs
description: Een beheerdersrol kan gebruikers toevoegen, beheerders rollen toewijzen, gebruikers wachtwoorden opnieuw instellen, gebruikers licenties beheren of domeinen beheren.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451d0709a8b0a1c0993ff879d675eb056e721c36
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116638"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Machtigingen voor beheerrol in Azure Active Directory

Met Azure Active Directory (Azure AD) kunt u beperkte beheerders aanwijzen voor het beheren van identiteits taken in functies met minder bevoegdheden. Beheerders kunnen voor dergelijke doel einden worden toegewezen om gebruikers toe te voegen of te wijzigen, beheerders rollen toe te wijzen, gebruikers wachtwoorden opnieuw in te stellen, gebruikers licenties te beheren en domein namen te beheren. De [standaard gebruikers machtigingen](../fundamentals/users-default-permissions.md) kunnen alleen worden gewijzigd in gebruikers instellingen in azure AD.

## <a name="limit-use-of-global-administrator"></a>Het gebruik van een globale beheerder beperken

Gebruikers die zijn toegewezen aan de rol van globale beheerder kunnen elke beheer instelling in uw Azure AD-organisatie lezen en wijzigen. De persoon die zich aanmeldt voor een Azure-abonnement krijgt standaard de rol van globale beheerder voor de Azure AD-organisatie toegewezen. Alleen globale beheerders en bevoegde beheerdersrol kunnen beheerders rollen delegeren. Om het risico voor uw bedrijf te verminderen, wordt u aangeraden deze rol toe te wijzen aan de minste mogelijke personen in uw organisatie.

Als best practice wordt u aangeraden deze rol toe te wijzen aan minder dan vijf personen in uw organisatie. Als er meer dan vijf beheerders zijn toegewezen aan de rol van globale beheerder in uw organisatie, zijn dit een aantal manieren om het gebruik te verminderen.

### <a name="find-the-role-you-need"></a>Zoek de gewenste rol

Als u de functie die u nodig hebt uit een lijst met veel rollen wilt vinden, kunt u in azure AD subsets van de rollen weer geven op basis van Rolgroepen. Bekijk het nieuwe **type** filter voor [Azure AD-rollen en-beheerders](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) om alleen de rollen in het geselecteerde type weer te geven.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Er bestaat nu een rol die niet bestaat tijdens het toewijzen van de rol globale beheerder

Het is mogelijk dat een rol of rollen aan Azure AD zijn toegevoegd met meer gedetailleerde machtigingen die geen optie waren wanneer u een aantal gebruikers hebt uitgebreid naar de globale beheerder. Na verloop van tijd worden er extra rollen geïmplementeerd die taken uitvoeren die alleen de rol van globale beheerder kunnen uitvoeren. U kunt deze weer geven in de volgende [beschik bare rollen](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Beheerders rollen toewijzen of verwijderen

Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](directory-manage-roles-portal.md)voor meer informatie over het toewijzen van beheerders rollen aan een gebruiker in azure Active Directory.

## <a name="available-roles"></a>Beschik bare rollen

De volgende beheerders rollen zijn beschikbaar:

### <a name="application-administrator"></a>[Toepassingsbeheerder](#application-administrator-permissions)

Gebruikers met deze rol kunnen alle aspecten van bedrijfs toepassingen, toepassings registraties en toepassings proxy-instellingen maken en beheren. Gebruikers die aan deze rol zijn toegewezen, worden niet toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.

Toepassings beheerders kunnen toepassings referenties beheren waarmee ze de toepassing kunnen imiteren. Gebruikers die aan deze rol zijn toegewezen, kunnen dus toepassings referenties beheren van alleen de toepassingen die niet zijn toegewezen aan Azure AD-rollen of die zijn toegewezen aan de volgende beheerders rollen:

* Toepassingsbeheerder
* Toepassingsontwikkelaar
* Beheerder van de cloudtoepassing
* Adreslijst lezers

Als een toepassing wordt toegewezen aan een andere functie die hierboven niet wordt vermeld, kan de toepassings beheerder geen referenties van die toepassing beheren.

Deze rol verleent ook de mogelijkheid om _toestemming_ te geven aan gedelegeerde machtigingen en toepassings machtigingen, met uitzonde ring van machtigingen voor de Microsoft Graph-API.

> [!IMPORTANT]
> Deze uitzonde ring betekent dat u nog steeds toestemming kunt geven voor _andere_ apps (bijvoorbeeld niet-micro soft-apps of apps die u hebt geregistreerd), maar niet op machtigingen voor Azure AD zelf. U kunt deze machtigingen nog steeds _aanvragen_ als onderdeel van de app-registratie, maar u moet een Azure AD-beheerder hebben om deze machtigingen te _verlenen_ . Dit betekent dat een kwaadwillende gebruiker de machtigingen niet eenvoudig kan verhogen, bijvoorbeeld door te maken en te verzenden naar een app die naar de hele map kan schrijven en de machtigingen van de app kunnen worden uitgebreid naar een globale beheerder.

### <a name="application-developer"></a>[Toepassingsontwikkelaar](#application-developer-permissions)

Gebruikers met deze rol kunnen toepassings registraties maken wanneer de instelling ' gebruikers kunnen toepassingen registreren ' is ingesteld op Nee. Deze rol verleent ook toestemming om de toestemming te geven aan de hand van een eigen naam wanneer de instelling ' gebruikers kunnen toestemming geven voor het openen van Bedrijfs gegevens namens hun naam ' is ingesteld op Nee. Gebruikers die aan deze rol zijn toegewezen, worden toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.

### <a name="authentication-administrator"></a>[Verificatie beheerder](#authentication-administrator-permissions)

Gebruikers met deze rol kunnen referenties voor niet-wacht woord instellen of opnieuw instellen voor sommige gebruikers en kunnen wacht woorden voor alle gebruikers bijwerken. Verificatie beheerders kunnen vereisen dat gebruikers die niet-beheerders zijn of aan sommige rollen zijn toegewezen, zich opnieuw registreren bij bestaande referenties zonder wacht woord (bijvoorbeeld MFA of FIDO), en kunnen ook **MFA op het apparaat**intrekken, waarbij wordt gevraagd om MFA bij de volgende aanmelding. Deze acties gelden alleen voor gebruikers die niet-beheerders zijn of aan een of meer van de volgende rollen zijn toegewezen:

* Verificatie beheerder
* Adreslijst lezers
* Gast uitnodiging
* Berichten centrum-lezer
* Rapport lezer

Met de rol [bevoorrechte verificatie beheerder](#privileged-authentication-administrator) kunt u de registratie en multi-factor Authentication afdwingen voor alle gebruikers.

> [!IMPORTANT]
> Gebruikers met deze rol kunnen referenties wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van de referenties van een gebruiker kan betekenen dat de identiteit en machtigingen van de gebruiker worden aangenomen. Bijvoorbeeld:
>
>- Toepassings registratie en eigen aren van bedrijfs toepassingen, die referenties kunnen beheren van apps waarvan ze eigenaar zijn. Deze apps hebben mogelijk privileged-machtigingen in azure AD en elders niet verleend aan verificatie beheerders. Via dit pad kan een verificatie beheerder mogelijk de identiteit van een toepassings eigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigen aars van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie in Azure.
>- Groeps eigenaren van beveiligings groep en Office 365, wie groepslid maatschap kan beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en Human Resources Systems.
>- Niet-beheerders als leidinggevenden, juridisch adviseur en Human Resources-werk nemers die mogelijk toegang tot gevoelige of persoonlijke informatie hebben.

### <a name="azure-devops-administrator"></a>[Azure DevOps-beheerder](#azure-devops-administrator-permissions)

Gebruikers met deze rol kunnen het Azure DevOps-beleid beheren om het maken van nieuwe Azure DevOps-organisaties te beperken tot een set Configureer bare gebruikers of groepen. Gebruikers met deze rol kunnen dit beleid beheren via een Azure DevOps-organisatie die een back-up maakt van de Azure AD-organisatie van het bedrijf.

Alle Azure DevOps-beleids regels voor ondernemingen kunnen worden beheerd door gebruikers met deze rol.

### <a name="azure-information-protection-administrator"></a>[Azure Information Protection beheerder](#azure-information-protection-administrator-permissions)

Gebruikers met deze rol hebben alle machtigingen in de Azure Information Protection-Service. Deze rol staat het configureren van labels toe voor het Azure Information Protection beleid, het beheren van beveiligings sjablonen en het activeren van de beveiliging. Deze rol verleent geen enkele machtiging in Identity Protection Center, Privileged Identity Management, Bewaak Office 365 Service Health of Office 365 Security & compliance Center.

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF sleutelsetcursor](#b2c-ief-keyset-administrator-permissions)

Gebruiker kan beleids sleutels en geheimen maken en beheren voor token versleuteling, token handtekeningen en claim versleuteling/ontsleuteling.Door nieuwe sleutels aan bestaande sleutel containers toe te voegen, kan deze beperkte beheerder geheimen naar behoefte overzetten zonder dat dit van invloed is op bestaande toepassingen.Deze gebruiker kan de volledige inhoud van deze geheimen en de verval datums bekijken, zelfs na het maken ervan.

> [!IMPORTANT]
> Dit is een gevoelige rol.De rol sleutelsetcursor moet zorgvuldig worden gecontroleerd en worden toegewezen tijdens de pre-productie en productie.

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF-beleids beheerder](#b2c-ief-policy-administrator-permissions)

Gebruikers met deze rol kunnen alle aangepaste beleids regels maken, lezen, bijwerken en verwijderen in Azure AD B2C en hebben daarom volledige controle over het Framework voor identiteits ervaring in de relevante Azure AD B2C organisatie. Door beleid te bewerken, kan deze gebruiker directe Federatie tot stand brengen met externe ID-providers, het Directory-schema wijzigen, alle gebruikers inhoud wijzigen (HTML, CSS, java script), de vereisten wijzigen voor het volt ooien van een verificatie, het maken van nieuwe gebruikers, het verzenden van gebruikers gegevens naar externe systemen, inclusief wacht woorden en telefoon nummers. Deze rol kan de versleutelings sleutels daarentegen niet wijzigen of de geheimen bewerken die worden gebruikt voor Federatie in de organisatie.

> [!IMPORTANT]
> De B2 IEF-beleids beheerder is een zeer gevoelige rol die zeer beperkt moet worden toegewezen aan organisaties in de productie omgeving.Activiteiten door deze gebruikers moeten nauw keurig worden gecontroleerd, met name voor organisaties die in productie zijn.

### <a name="billing-administrator"></a>[Facturerings beheerder](#billing-administrator-permissions)

Doet aankopen, beheert abonnementen, beheert ondersteuningstickets en bewaakt de servicestatus.

### <a name="cloud-application-administrator"></a>[Beheerder van de cloudtoepassing](#cloud-application-administrator-permissions)

Gebruikers met deze rol hebben dezelfde machtigingen als de rol toepassings beheerder, met uitzonde ring van de mogelijkheid om toepassings proxy te beheren. Met deze rol kunnen alle aspecten van bedrijfs toepassingen en toepassings registraties worden gemaakt en beheerd. Deze rol verleent ook de mogelijkheid om toestemming te geven aan gedelegeerde machtigingen en toepassings machtigingen met uitzonde ring van de Microsoft Graph-API. Gebruikers die aan deze rol zijn toegewezen, worden niet toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.

Cloud toepassings beheerders kunnen toepassings referenties beheren waarmee ze de toepassing kunnen imiteren. Gebruikers die aan deze rol zijn toegewezen, kunnen dus toepassings referenties beheren van alleen de toepassingen die niet zijn toegewezen aan Azure AD-rollen of die zijn toegewezen aan de volgende beheerders rollen:

* Toepassingsontwikkelaar
* Beheerder van de cloudtoepassing
* Adreslijst lezers

Als een toepassing wordt toegewezen aan een andere rol die hierboven niet wordt vermeld, kan de beheerder van de Cloud geen referenties van die toepassing beheren.

### <a name="cloud-device-administrator"></a>[Beheerder van Cloud apparaat](#cloud-device-administrator-permissions)

Gebruikers met deze rol kunnen apparaten in azure AD inschakelen, uitschakelen en verwijderen en Windows 10 BitLocker-sleutels (indien aanwezig) in de Azure Portal lezen. De rol verleent geen machtigingen voor het beheren van andere eigenschappen op het apparaat.

### <a name="compliance-administrator"></a>[Beheerder voor naleving](#compliance-administrator-permissions)

Gebruikers met deze rol hebben machtigingen voor het beheren van aan naleving gerelateerde functies in het Microsoft 365 compliance Center, Microsoft 365 beheer centrum, Azure en Office 365 Security & compliance Center. Eigen beheer kunnen ook alle functies in het Exchange-beheer centrum en teams beheren & Skype voor bedrijven-beheer centrums en ondersteunings tickets maken voor Azure en Microsoft 365. Meer informatie vindt u op de [Office 365-beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

In | Kan doen
----- | ----------
[Nalevings centrum Microsoft 365](https://protection.office.com) | De gegevens van uw organisatie beveiligen en beheren in Microsoft 365 Services<br>Nalevings waarschuwingen beheren
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | De regelgeving voor naleving van uw organisatie bijhouden, toewijzen en verifiëren
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beheer van gegevens beheren<br>Juridisch en gegevens onderzoek uitvoeren<br>Aanvraag voor gegevens onderwerp beheren<br><br>Deze rol heeft dezelfde machtigingen als de RoleGroup van de [nalevings beheerder](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & compliance op rollen gebaseerd toegangs beheer.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alle intune-controle gegevens weer geven
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Heeft alleen-lezen-machtigingen en kan waarschuwingen beheren<br>Kan bestands beleid maken en wijzigen en bestandsbeheer acties toestaan<br>Kan alle ingebouwde rapporten weer geven onder Gegevensbeheer

### <a name="compliance-data-administrator"></a>[Beheerder voor nalevingsgegevens](#compliance-data-administrator-permissions)

Gebruikers met deze rol hebben machtigingen voor het bijhouden van gegevens in het Microsoft 365 compliance Center, Microsoft 365 beheer centrum en Azure. Gebruikers kunnen ook nalevings gegevens volgen in het Exchange-beheer centrum, nalevings beheer en teams & het beheer centrum van Skype voor bedrijven en ondersteunings tickets maken voor Azure en Microsoft 365.

In | Kan doen
----- | ----------
[Nalevings centrum Microsoft 365](https://protection.office.com) | Nalevings beleid bewaken over Microsoft 365 Services<br>Nalevings waarschuwingen beheren
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | De regelgeving voor naleving van uw organisatie bijhouden, toewijzen en verifiëren
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beheer van gegevens beheren<br>Juridisch en gegevens onderzoek uitvoeren<br>Aanvraag voor gegevens onderwerp beheren<br><br>Deze rol heeft dezelfde machtigingen als de RoleGroup van de [compatibiliteits gegevens beheerder](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & op rollen gebaseerd toegangs beheer.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alle intune-controle gegevens weer geven
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Heeft alleen-lezen-machtigingen en kan waarschuwingen beheren<br>Kan bestands beleid maken en wijzigen en bestandsbeheer acties toestaan<br>Kan alle ingebouwde rapporten weer geven onder Gegevensbeheer

### <a name="conditional-access-administrator"></a>[Beheerder van voorwaardelijke toegang](#conditional-access-administrator-permissions)

Gebruikers met deze rol kunnen Azure Active Directory instellingen voor voorwaardelijke toegang beheren.
> [!NOTE]
> Als u beleid voor voorwaardelijke toegang van Exchange ActiveSync wilt implementeren in azure, moet de gebruiker ook een globale beheerder zijn.

### <a name="customer-lockbox-access-approver"></a>[Klanten-lockbox Access-fiatteur](#customer-lockbox-access-approver-permissions)

Beheert [klanten-lockbox-aanvragen](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) in uw organisatie. Ze ontvangen e-mail meldingen voor Klanten-lockbox aanvragen en kunnen aanvragen goed keuren en weigeren vanuit het Microsoft 365-beheer centrum. Ze kunnen ook de Klanten-lockbox functie in-of uitschakelen. Alleen globale beheerders kunnen de wacht woorden van personen die aan deze rol zijn toegewezen, opnieuw instellen.

### <a name="desktop-analytics-administrator"></a>[Beheerder van Desktop Analytics](#desktop-analytics-administrator-permissions)


Gebruikers met deze rol kunnen de bureau blad Analytics en de aanpassing van Office-&-beleids Services beheren. Voor desktop Analytics is dit onder andere de mogelijkheid om inventarisatie van assets te bekijken, implementatie plannen te maken, implementatie en status weer te geven. Voor Office Customization & Policy service kunnen gebruikers met deze rol Office-beleid beheren.

### <a name="device-administrator"></a>[Apparaat-beheerder](#device-administrators-permissions)

Deze rol is alleen beschikbaar voor toewijzing als extra lokale beheerder in [Apparaatinstellingen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gebruikers met deze rol worden lokale computer beheerders op alle Windows 10-apparaten die lid zijn van Azure Active Directory. Ze kunnen geen apparaten objecten in Azure Active Directory beheren.

### <a name="directory-readers"></a>[Adreslijst lezers](#directory-readers-permissions)

Gebruikers met deze rol kunnen basis informatie over de Directory lezen. Deze rol moet worden gebruikt voor:
* Het verlenen van een specifieke set gast gebruikers lees toegang in plaats van deze aan alle gast gebruikers toe te kennen.
* Het verlenen van een specifieke set gebruikers die geen beheerder zijn, heeft toegang tot Azure Portal wanneer de toegang tot de Azure AD-Portal beperken tot beheerders alleen is ingesteld op Ja.
* Verlenen van service-principals toegang tot Directory waarbij Directory. Read. all geen optie is.

### <a name="directory-synchronization-accounts"></a>[Directory-synchronisatie accounts](#directory-synchronization-accounts-permissions)

Niet gebruiken. Deze rol wordt automatisch toegewezen aan de Azure AD Connect-service en is niet bedoeld of wordt niet ondersteund voor andere gebruik.

### <a name="directory-writers"></a>[Schrijvers van mappen](#directory-writers-permissions)

Dit is een verouderde rol die moet worden toegewezen aan toepassingen die het [toestemmings raamwerk](../develop/quickstart-register-app.md)niet ondersteunen. Het mag niet worden toegewezen aan gebruikers.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365-beheerder/CRM-beheerder](#crm-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen in micro soft Dynamics 365 online, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren. Meer informatie over [het gebruik van de service beheerdersrol om uw Azure AD-organisatie te beheren](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als ' Dynamics 365 service Administrator '. Het is "Dynamics 365-beheerder" in de [Azure Portal](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Exchange-beheerder](#exchange-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen in micro soft Exchange Online, wanneer de service aanwezig is. Daarnaast beschikt u over de mogelijkheid om alle Office 365-groepen te maken en beheren, ondersteunings tickets te beheren en de service status te controleren. Meer informatie [over Office 365-beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als de Exchange-service beheerder. Het is ' Exchange Administrator ' in de [Azure Portal](https://portal.azure.com). Het is ' Exchange Online Administrator ' in het [Exchange-beheer centrum](https://go.microsoft.com/fwlink/p/?LinkID=529144).


### <a name="external-id-user-flow-administrator"></a>[Externe ID gebruikers stroom beheerder](#external-id-user-flow-administrator-permissions)

Gebruikers met deze rol kunnen B2C-Gebruikersstromen (ook wel ingebouwde beleids regels genoemd) maken en beheren in de Azure Portal.Door gebruikers stromen te maken of te bewerken, kunnen deze gebruikers de HTML/CSS/java script-inhoud van de gebruikers ervaring wijzigen, MFA-vereisten per gebruikers stroom wijzigen, claims in het token wijzigen en sessie-instellingen aanpassen voor alle beleids regels in de Azure AD-organisatie. Aan de andere kant omvat deze rol niet de mogelijkheid om gebruikers gegevens te controleren of wijzigingen aan te brengen in de kenmerken die zijn opgenomen in het schema van de organisatie.Wijzigingen in het Framework voor identiteits ervaring (ook wel aangepast) zijn ook buiten het bereik van deze rol.

### <a name="external-id-user-flow-attribute-administrator"></a>[Externe ID gebruikers stroom kenmerk beheerder](#external-id-user-flow-attribute-administrator-permissions)

Gebruikers met deze rol kunnen aangepaste kenmerken die beschikbaar zijn voor alle gebruikers stromen in de Azure AD-organisatie toevoegen of verwijderen.Als zodanig kunnen gebruikers met deze rol nieuwe elementen wijzigen of toevoegen aan het schema van de eind gebruiker en de invloed hebben op de werking van alle gebruikers stromen en indirect als gevolg van wijzigingen in welke gegevens kunnen worden gesteld aan eind gebruikers en uiteindelijk worden verzonden als claims naar toepassingen.Deze rol kan geen gebruikers stromen bewerken.

### <a name="external-identity-provider-administrator"></a>[Beheerder van externe ID-provider](#external-identity-provider-administrator-permissions)

Deze beheerder beheert Federatie tussen Azure AD-organisaties en externe ID-providers.Met deze rol kunnen gebruikers nieuwe id-providers toevoegen en alle beschik bare instellingen configureren (bijvoorbeeld het pad Authentication, Service-ID, toegewezen sleutel containers).Deze gebruiker kan de Azure AD-organisatie in staat stellen verificaties van externe ID-providers te vertrouwen.De impact op de ervaring van de eind gebruiker is afhankelijk van het type organisatie:

* Azure AD-organisaties voor werk nemers en partners: de toevoeging van een Federatie (bijvoorbeeld met Gmail) zal direct invloed hebben op alle uitnodigingen van gasten die nog niet zijn ingewisseld. Zie [Google toevoegen als een id-provider voor B2B-gast gebruikers](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Azure Active Directory B2C organisaties: de toevoeging van een Federatie (bijvoorbeeld met Facebook of met een andere Azure AD-organisatie) is niet direct van invloed op eind gebruikers totdat de ID-provider is toegevoegd als een optie in een gebruikers stroom (ook wel ingebouwd beleid genoemd). Zie [Configure a Microsoft-account als een id-provider](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) voor een voor beeld.Als u de gebruikers stromen wilt wijzigen, is de beperkte rol ' B2C User flow Administrator ' vereist.

### <a name="global-administrator--company-administrator"></a>[Beheerder van globale beheerder/bedrijf](#company-administrator-permissions)

Gebruikers met deze rol hebben toegang tot alle beheer functies in Azure Active Directory en services die gebruikmaken van Azure Active Directory identiteiten, zoals Microsoft 365 Security Center, Microsoft 365 compliance Center, Exchange Online, share point online en Skype voor bedrijven online. De persoon die zich aanmeldt voor de Azure AD-organisatie, wordt een globale beheerder. Uw bedrijf kan meer dan één globale beheerder zijn. Globale beheerders kunnen het wachtwoord voor elke gebruiker en alle andere beheerders opnieuw instellen.

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als ' bedrijfs beheerder '. Het is "globale beheerder" in de [Azure Portal](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Algemene lezer](#global-reader-permissions)

Gebruikers met deze rol kunnen instellingen en beheer informatie lezen over Microsoft 365 Services, maar kunnen geen beheer acties uitvoeren. Algemene lezer is het alleen-lezen equivalent van de globale beheerder. Wijs algemene lezer toe in plaats van globale beheerder voor planning, controles of onderzoek. Gebruik de algemene lezer in combi natie met andere beperkte beheerders rollen, zoals Exchange Administrator, om het werk gemakkelijker te maken zonder de rol van globale beheerder toe te wijzen. De algemene lezer werkt met Microsoft 365 beheer centrum, het Exchange-beheer centrum, het share point-beheer centrum, teams beheer centrum, Security Center, compliance Center, Azure AD-beheer centrum en beheer centrum voor Apparaatbeheer.

> [!NOTE]
> De rol van globale lezer heeft nu enkele beperkingen:
>
>- [Onedrive-beheer centrum](https://admin.onedrive.com/) : het onedrive-beheer centrum biedt geen ondersteuning voor de globale lees functie
>- [M365-beheer centrum](https://admin.microsoft.com/Adminportal/Home#/homepage) : globale lezer kan geen lockbox-aanvragen van klanten lezen. U vindt het tabblad **klant lockbox-aanvragen** niet onder **ondersteuning** in het linkerdeel venster van het M365-beheer centrum.
>- [Office Security & compliance Center](https://sip.protection.office.com/homepage) : globale lezer kan geen SCC-controle logboeken lezen, inhoud zoeken of een beveiligde Score bekijken.
>- [Teams beheer centrum](https://admin.teams.microsoft.com) : wereld wijde lezer kan de **levens cyclus van teams**, **analyses & rapporten**, het **beheer van IP-telefoon apparaten** en de **app-catalogus**niet lezen.
>- [Privileged Access Management (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) biedt geen ondersteuning voor de globale lezer-rol.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) -wereld wijde lezer wordt alleen ondersteund [voor centrale rapportage](https://docs.microsoft.com/azure/information-protection/reports-aip) en wanneer uw Azure AD-organisatie zich niet op het [uniforme label platform](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)bevindt.
>
> Deze functies zijn momenteel in ontwikkeling.
>

### <a name="groups-administrator"></a>[Groeps beheerder](#groups-administrator-permissions)

Gebruikers met deze rol kunnen groepen en de bijbehorende instellingen maken/beheren, zoals het naamgevings-en verloop beleid. Het is belang rijk om te begrijpen dat het toewijzen van een gebruiker aan deze rol de mogelijkheid biedt om alle groepen in de organisatie te beheren in verschillende werk belastingen, zoals teams, share point, Yammer en Outlook. Daarnaast kan de gebruiker de verschillende groeps instellingen beheren voor verschillende beheerders portals, zoals micro soft-beheer centrum, Azure Portal, en werk belasting-specifieke taken als teams en share point-beheer centrums.

### <a name="guest-inviter"></a>[Gast uitnodiging](#guest-inviter-permissions)

Gebruikers met deze rol kunnen uitnodigingen van Azure Active Directory B2B-gast gebruiker beheren wanneer de leden de gebruikers instelling **kunnen uitnodigen** is ingesteld op Nee. Meer informatie over B2B-samen werking bij de [samen werking met Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Het bevat geen andere machtigingen.

### <a name="helpdesk-administrator"></a>[Helpdesk beheerder](#helpdesk-administrator-permissions)

Gebruikers met deze rol kunnen wacht woorden wijzigen, tokens voor vernieuwen ongeldig maken, service aanvragen beheren en de service status controleren. Wanneer een vernieuwings token ongeldig is, wordt de gebruiker gedwongen zich opnieuw aan te melden. Helpdesk beheerders kunnen wacht woorden opnieuw instellen en vernieuwings tokens van andere gebruikers die niet-beheerders zijn, alleen toewijzen aan de volgende rollen:

* Adreslijst lezers
* Gast uitnodiging
* Helpdesk beheerder
* Berichten centrum-lezer
* Rapport lezer

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wacht woorden wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van het wacht woord van een gebruiker kan betekenen dat de identiteit en machtigingen van de gebruiker worden aangenomen. Bijvoorbeeld:
>
>- Toepassings registratie en eigen aren van bedrijfs toepassingen, die referenties kunnen beheren van apps waarvan ze eigenaar zijn. Deze apps hebben mogelijk privileged-machtigingen in azure AD en elders niet verleend aan helpdesk beheerders. Via dit pad kan een helpdesk beheerder de identiteit van een toepassings eigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigen aars van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie in Azure.
>- Groeps eigenaren van beveiligings groep en Office 365, wie groepslid maatschap kan beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en Human Resources Systems.
>- Niet-beheerders als leidinggevenden, juridisch adviseur en Human Resources-werk nemers die mogelijk toegang tot gevoelige of persoonlijke informatie hebben.

Het overdragen van beheerders machtigingen via subsets van gebruikers en het Toep assen van beleid op een subset van gebruikers is mogelijk met [beheer eenheden (nu beschikbaar in open bare preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).

Deze rol heette voorheen ' Wachtwoord beheerder ' in de [Azure Portal](https://portal.azure.com/). De naam van de "helpdesk beheerder" in azure AD komt nu overeen met de naam in azure AD Power shell en de Microsoft Graph-API.

### <a name="hybrid-identity-administrator"></a>[Hybride identiteits beheerder](#hybrid-identity-administrator-permissions)

Gebruikers met deze rol kunnen services en instellingen met betrekking tot het inschakelen van hybride identiteit in azure AD inschakelen, configureren en beheren. Deze rol biedt de mogelijkheid om Azure AD te configureren voor een van de drie ondersteunde verificatie methoden, wacht woord-hash-synchronisatie (PHS), Pass-Through-verificatie (PTA) of Federatie (AD FS of externe Federatie provider), en om gerelateerde on-premises infra structuur te implementeren om deze in te scha kelen. On-premises infra structuur omvat provisioning-en PTA-agents. Deze rol biedt de mogelijkheid om naadloze eenmalige aanmelding (S-SSO) in te scha kelen voor naadloze verificatie op niet-Windows 10-apparaten of niet-Windows Server 2016-computers. Daarnaast biedt deze rol de mogelijkheid om aanmeldings logboeken te bekijken en toegang te krijgen tot de status en analyses voor het bewaken en oplossen van problemen. 

### <a name="intune-administrator"></a>[InTune-beheerder](#intune-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft Intune online, wanneer de service aanwezig is. Daarnaast bevat deze rol de mogelijkheid om gebruikers en apparaten te beheren om het beleid te koppelen, en om groepen te maken en te beheren. Meer informatie over op [rollen gebaseerd beheer beheer (RBAC) met Microsoft intune](https://docs.microsoft.com/intune/role-based-access-control).

Deze rol kan alle beveiligings groepen maken en beheren. De intune-beheerder heeft echter geen beheerders rechten voor Office-groepen. Dit betekent dat de beheerder eigen aren of lidmaatschappen van alle Office-groepen in de organisatie niet kan bijwerken. Hij kan echter de Office-groep die hij maakt, beheren die als onderdeel van de bevoegdheden van de eind gebruiker hoort. Een wille keurige Office-groep (geen beveiligings groep) die hij/zij maakt, moet dus worden geteld voor het quotum van 250.

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als intune-service beheerder. Het is de ' intune-beheerder ' in de [Azure Portal](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Kaizala-beheerder](#kaizala-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen voor het beheren van instellingen in micro soft Kaizala, wanneer de service aanwezig is, en de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren. Daarnaast heeft de gebruiker toegang tot rapporten met betrekking tot de acceptatie & gebruik van Kaizala door organisatie leden en zakelijke rapporten die zijn gegenereerd met behulp van de Kaizala-acties.

### <a name="license-administrator"></a>[Licentie beheerder](#license-administrator-permissions)

Gebruikers met deze rol kunnen licentie toewijzingen toevoegen, verwijderen en bijwerken voor gebruikers, groepen (met behulp van groeps licenties) en de gebruiks locatie van gebruikers beheren. De rol biedt geen mogelijkheid om abonnementen te kopen of te beheren, groepen te maken of te beheren, of gebruikers te maken of te beheren buiten de gebruiks locatie. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

### <a name="message-center-privacy-reader"></a>[Berichten centrum-privacy-lezer](#message-center-privacy-reader-permissions)

Gebruikers met deze rol kunnen alle meldingen in het berichten centrum bewaken, inclusief gegevens privacy-berichten. Berichten centrum privacy lezers ontvangen e-mail meldingen met inbegrip van de privacy van gegevens en ze kunnen zich afmelden met behulp van de voor keuren voor berichten centrum. Alleen de globale beheerder en de privacy-lezer van het berichten centrum kunnen gegevens privacy-berichten lezen. Daarnaast bevat deze rol de mogelijkheid om groepen, domeinen en abonnementen weer te geven. Deze rol heeft geen machtiging om service aanvragen weer te geven, te maken of te beheren.

### <a name="message-center-reader"></a>[Berichten centrum-lezer](#message-center-reader-permissions)

Gebruikers met deze rol kunnen meldingen en advies status updates in [Office 365 Message Center](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) voor hun organisatie controleren op geconfigureerde services zoals Exchange, intune en micro soft teams. Berichten centrum-lezers ontvangen wekelijkse e-mail samenvattingen van berichten, updates en kunnen berichten centrum berichten delen in Office 365. In azure AD hebben gebruikers die aan deze rol zijn toegewezen alleen alleen-lezen toegang tot Azure AD-services zoals gebruikers en groepen. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

### <a name="modern-commerce-administrator"></a>[Moderne commerce-beheerder](#modern-commerce-administrator-permissions)
Niet gebruiken. Deze rol wordt automatisch toegewezen vanuit commerce en is niet bedoeld of wordt niet ondersteund voor andere gebruik. Zie hieronder voor meer informatie.

De rol moderne commerce beheerder geeft bepaalde gebruikers toestemming om toegang te krijgen tot Microsoft 365-beheer centrum en de linkernavigatiebalk te bekijken voor **thuis**, **facturering**en **ondersteuning**. De inhoud die beschikbaar is op deze gebieden wordt beheerd door de [Commerce-specifieke rollen](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-mca-roles) die aan gebruikers zijn toegewezen voor het beheren van producten die ze voor zichzelf of uw organisatie hebben gekocht. Dit kunnen taken zijn zoals het betalen van facturen of voor toegang tot facturerings accounts en facturerings profielen. 

Gebruikers met de rol moderne commerce beheerder hebben doorgaans beheerders machtigingen in andere micro soft-aankoop systemen, maar hebben geen globale beheerder of facturerings beheerders rollen die worden gebruikt voor toegang tot het beheer centrum. 

**Wanneer is de rol moderne commerce-beheerder toegewezen?**
* **Self-service aankopen in Microsoft 365-beheer centrum** : met self-service aankopen kunnen gebruikers nieuwe producten uitproberen door ze te kopen of zich zelf aan te melden. Deze producten worden beheerd in het beheer centrum. Gebruikers die een self-service aankoop doen, krijgen een rol in het commerce-systeem en de rol van moderne commerce-beheerder zodat ze hun aankopen kunnen beheren in het beheer centrum. Beheerders kunnen de inkopen van self-service (voor Power BI, Power apps, energie automatisering) blok keren via [Power shell](https://docs.microsoft.com/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell?view=o365-worldwide). Zie [Veelgestelde vragen over self-service aankopen](https://docs.microsoft.com/microsoft-365/commerce/subscriptions/self-service-purchase-faq?view=o365-worldwide)voor meer informatie.  
* **Aankopen van micro soft Commercial Marketplace** : net als bij self-service aankopen, wanneer een gebruiker een product of service koopt van Microsoft AppSource of Azure Marketplace, wordt de rol van moderne commerce-beheerder toegewezen als ze niet de rol van globale beheerder of facturerings beheerder hebben. In sommige gevallen kunnen gebruikers worden geblokkeerd voor het aanbrengen van deze aankopen. Zie [micro soft Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/marketplace-faq-publisher-guide#what-could-block-a-customer-from-completing-a-purchase)(Engelstalig) voor meer informatie.   
* **Voorst Ellen van micro soft** : een voor stel is een formeel aanbod van micro soft voor uw organisatie om micro soft-producten en-services te kopen. Wanneer de persoon die het voor stel accepteert, geen globale beheerder of facturerings beheerdersrol heeft in azure AD, krijgen ze zowel een bedrijfsspecifieke rol toegewezen om het voor stel als de moderne commerce-beheerdersrol voor toegang tot het beheer centrum te volt ooien. Wanneer ze toegang krijgen tot het beheer centrum, kunnen ze alleen functies gebruiken die zijn geautoriseerd door hun specifieke commerce rol. 
* **Commerce-specifieke rollen** : aan sommige gebruikers worden commerce-specifieke rollen toegewezen. Als een gebruiker geen globale of facturerings beheerder is, krijgen ze de rol van moderne commerce-beheerder, zodat ze toegang hebben tot het beheer centrum.  

Als de rol van moderne commerce-beheerder niet is toegewezen aan een gebruiker, verliest deze toegang tot Microsoft 365-beheer centrum. Als ze producten voor zichzelf of voor uw organisatie beheren, kunnen ze deze niet beheren. Dit kunnen bijvoorbeeld het toewijzen van licenties zijn, het wijzigen van de betalings methoden, het betalen van facturen of andere taken voor het beheren van abonnementen. 

### <a name="network-administrator"></a>[Netwerk beheerder](#network-administrator-permissions)

Gebruikers met deze rol kunnen aanbevelingen van de netwerk architectuur beoordelen van micro soft die zijn gebaseerd op telemetrie van het netwerk vanaf hun gebruikers locaties. De netwerk prestaties voor Office 365 zijn gebaseerd op zorgvuldige netwerk verbindings architectuur van de onderneming, die doorgaans specifiek is voor de gebruiker. Deze rol maakt het mogelijk om de gedetecteerde gebruikers locaties en configuratie van de netwerk parameters voor die locaties te bewerken, zodat de telemetriegegevens en ontwerp aanbevelingen kunnen worden verbeterd. 

### <a name="office-apps-administrator"></a>[Office-Apps beheerder](#office-apps-administrator-permissions)

Gebruikers met deze rol kunnen de Cloud instellingen van Office 365 apps beheren. Dit omvat het beheer van Cloud beleid, self-service Download beheer en de mogelijkheid om aan Office-apps gerelateerde rapporten weer te geven. Deze rol biedt daarnaast de mogelijkheid om ondersteunings tickets te beheren en de service status in het hoofd beheer centrum te bewaken. Gebruikers die aan deze rol zijn toegewezen, kunnen ook de communicatie van nieuwe functies in Office-apps beheren. 

### <a name="partner-tier1-support"></a>[Ondersteuning voor partner Tier1](#partner-tier1-support-permissions)

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst verwijderd uit Azure AD. Deze rol is bedoeld voor gebruik door een klein aantal micro soft-verkoop partners en is niet bedoeld voor algemeen gebruik.

### <a name="partner-tier2-support"></a>[Ondersteuning voor partner Tier2](#partner-tier2-support-permissions)

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst verwijderd uit Azure AD. Deze rol is bedoeld voor gebruik door een klein aantal micro soft-verkoop partners en is niet bedoeld voor algemeen gebruik.

### <a name="password-administrator"></a>[Wachtwoord beheerder](#password-administrator-permissions)

Gebruikers met deze rol hebben beperkte mogelijkheden voor het beheren van wacht woorden. Deze rol biedt geen mogelijkheid om service aanvragen te beheren of de service status te controleren. Wachtwoord beheerders kunnen wacht woorden van andere gebruikers die niet-beheerders zijn of alleen leden van de volgende rollen opnieuw instellen:

* Adreslijst lezers
* Gast uitnodiging
* Wachtwoord beheerder

### <a name="power-bi-administrator"></a>[Power BI beheerder](#power-bi-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen in micro soft Power BI, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren. Meer informatie over [de rol van Power bi-beheerder](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als Power BI-service beheerder. Het is ' Power BI Administrator ' in de [Azure Portal](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Power platform-beheerder](#power-platform-administrator-permissions)

Gebruikers met deze rol kunnen alle aspecten van omgevingen, PowerApps, stromen en beleids regels voor preventie van gegevens verlies maken en beheren. Daarnaast hebben gebruikers met deze rol de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren.

### <a name="printer-administrator"></a>[Printer beheerder](#printer-administrator-permissions)

Gebruikers met deze rol kunnen printers registreren en alle aspecten van alle printer configuraties in de universele afdruk oplossing van micro soft beheren, met inbegrip van de instellingen voor de universele afdruk connector. Ze kunnen toestemming geven voor alle gedelegeerde afdruk machtigings aanvragen. Printer beheerders hebben ook toegang tot het afdrukken van rapporten.

### <a name="printer-technician"></a>[Printer technicus](#printer-technician-permissions)

Gebruikers met deze rol kunnen printers registreren en de printer status beheren in de micro soft Universal Print-oplossing. Ze kunnen ook alle connector gegevens lezen. Sleutel taak een printer technicus kan geen gebruikers machtigingen voor printers instellen en printers delen.

### <a name="privileged-authentication-administrator"></a>[Beheerder voor geprivilegieerde authenticatie](#privileged-authentication-administrator-permissions)

Gebruikers met deze rol kunnen referenties voor niet-wacht woord instellen of opnieuw instellen voor alle gebruikers, met inbegrip van globale beheerders, en kunnen wacht woorden voor alle gebruikers bijwerken. Bevoegde authenticatie beheerders kunnen gebruikers dwingen om zich opnieuw te registreren bij bestaande niet-wachtwoord referenties (zoals MFA of FIDO) en ' MFA onthouden op het apparaat ' in te trekken bij de volgende aanmelding van alle gebruikers. De rol [verificatie beheerder](#authentication-administrator) kan opnieuw registreren en MFA afdwingen voor alleen niet-beheerders en gebruikers die zijn toegewezen aan de volgende Azure AD-rollen:

* Verificatie beheerder
* Adreslijst lezers
* Gast uitnodiging
* Berichten centrum-lezer
* Rapport lezer

### <a name="privileged-role-administrator"></a>[Beheerder van geprivilegieerde rol](#privileged-role-administrator-permissions)

Gebruikers met deze rol kunnen roltoewijzingen in Azure Active Directory beheren, en in Azure AD Privileged Identity Management. Daarnaast kunt u met deze rol alle aspecten van Privileged Identity Management en administratieve eenheden beheren.

> [!IMPORTANT]
> Deze rol biedt de mogelijkheid om toewijzingen te beheren voor alle Azure AD-rollen, inclusief de rol van globale beheerder. Deze rol bevat geen andere geprivilegieerde mogelijkheden in azure AD, zoals het maken of bijwerken van gebruikers. Gebruikers die aan deze rol zijn toegewezen, kunnen zich echter zelf of anderen extra bevoegdheid verlenen door extra rollen toe te wijzen.

### <a name="reports-reader"></a>[Rapport lezer](#reports-reader-permissions)

Gebruikers met deze rol kunnen gegevens over gebruiks rapportage en het dash board rapporten weer geven in Microsoft 365 beheer centrum en het pakket voor de acceptatie context in Power BI. Daarnaast biedt de rol toegang tot aanmeldings rapporten en activiteiten in azure AD en gegevens die zijn geretourneerd door de Microsoft Graph rapportage-API. Een gebruiker die is toegewezen aan de rol van de rapport lezer heeft alleen toegang tot de metrische gegevens over het gebruik en de juiste aanneming. Ze hebben geen beheerders machtigingen om instellingen te configureren of om toegang te krijgen tot de productspecifieke beheer centra zoals Exchange. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

### <a name="search-administrator"></a>[Beheerder zoeken](#search-administrator-permissions)

Gebruikers met deze rol hebben volledige toegang tot alle micro soft Search-beheer functies in het Microsoft 365-beheer centrum. Beheerders van zoeken kunnen de rollen Zoek beheerders en zoek editor delegeren naar gebruikers, en inhoud maken en beheren, zoals blad wijzers, Q&als en locaties. Daarnaast kunnen deze gebruikers het berichten centrum bekijken, service status bewaken en service aanvragen maken.

### <a name="search-editor"></a>[Zoek editor](#search-editor-permissions)

Gebruikers met deze rol kunnen inhoud voor micro soft Search maken, beheren en verwijderen in het beheer centrum van Microsoft 365, waaronder blad wijzers, Q&als en locaties.

### <a name="security-administrator"></a>[Beveiligingsbeheer](#security-administrator-permissions)

Gebruikers met deze rol hebben machtigingen voor het beheren van beveiligings functies in het Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection en Office 365 Security & compliance Center. Meer informatie over machtigingen voor Office 365 is beschikbaar op [machtigingen in het Office 365 Security & compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In | Kan doen
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Beveiligings beleid bewaken over Microsoft 365 Services<br>Beveiligings Risico's en-waarschuwingen beheren<br>Rapporten weergeven
Identity Protection Center | Alle machtigingen van de rol beveiligings lezer<br>Daarnaast is de mogelijkheid om alle bewerkingen voor identiteits beveiliging uit te voeren, met uitzonde ring van het opnieuw instellen van wacht woorden
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alle machtigingen van de rol beveiligings lezer<br>Azure AD-roltoewijzingen of-instellingen **kunnen niet worden** beheerd
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid beheren<br>Beveiligings Risico's weer geven, onderzoeken en hierop reageren<br>Rapporten weergeven
Azure Advanced Threat Protection | Verdachte beveiligings activiteit bewaken en erop reageren
Windows Defender ATP en EDR | Rollen toewijzen<br>Computer groepen beheren<br>Detectie van de Endpoint Threat en automatisch herstel configureren<br>Waarschuwingen weer geven, onderzoeken en erop reageren
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Gebruikers-, apparaat-, registratie-, configuratie-en toepassings gegevens weer geven<br>Kan geen wijzigingen aanbrengen in intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Beheerders toevoegen, beleids regels en instellingen toevoegen, logboeken uploaden en beheer acties uitvoeren
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan beveiligings beleid weer geven, beveiligings statussen bekijken, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren
[Office 365-service status](https://docs.microsoft.com/office365/enterprise/view-service-health) | De status van Office 365-services weer geven
[Slimme vergrendeling](../authentication/howto-password-smart-lockout.md) | Definieer de drempel en duur voor vergren delingen wanneer mislukte aanmeldings gebeurtenissen plaatsvinden.
[Wachtwoord beveiliging](../authentication/concept-password-ban-bad.md) | Aangepaste lijst met verboden wacht woorden of on-premises wachtwoord beveiliging configureren.

### <a name="security-operator"></a>[Beveiligings operator](#security-operator-permissions)

Gebruikers met deze rol kunnen waarschuwingen beheren en algemene alleen-lezen toegang hebben voor beveiligings functies, inclusief alle informatie in Microsoft 365 Security Center, Azure Active Directory, identiteits beveiliging, Privileged Identity Management en Office 365 Security & compliance Center. Meer informatie over machtigingen voor Office 365 is beschikbaar op [machtigingen in het Office 365 Security & compliance Center](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

In | Kan doen
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Alle machtigingen van de rol beveiligings lezer<br>Waarschuwingen voor beveiligings Risico's weer geven, onderzoeken en hierop reageren
Identity Protection Center | Alle machtigingen van de rol beveiligings lezer<br>Daarnaast is de mogelijkheid om alle bewerkingen voor identiteits beveiliging uit te voeren, met uitzonde ring van het opnieuw instellen van wacht woorden
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alle machtigingen van de rol beveiligings lezer
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alle machtigingen van de rol beveiligings lezer<br>Beveiligings waarschuwingen weer geven, onderzoeken en hierop reageren
Windows Defender ATP en EDR | Alle machtigingen van de rol beveiligings lezer<br>Beveiligings waarschuwingen weer geven, onderzoeken en hierop reageren
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alle machtigingen van de rol beveiligings lezer
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Alle machtigingen van de rol beveiligings lezer
[Office 365-service status](https://docs.microsoft.com/office365/enterprise/view-service-health) | De status van Office 365-services weer geven

### <a name="security-reader"></a>[Beveiligingslezer](#security-reader-permissions)

Gebruikers met deze rol hebben algemene alleen-lezen toegang voor de functie met betrekking tot beveiliging, inclusief alle informatie in Microsoft 365 Security Center, Azure Active Directory, identiteits beveiliging, Privileged Identity Management, en de mogelijkheid om Azure Active Directory aanmeld rapporten en controle logboeken te lezen, en in Office 365 Security & compliance Center. Meer informatie over machtigingen voor Office 365 is beschikbaar op [machtigingen in het Office 365 Security & compliance Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In | Kan doen
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Beveiligings beleid weer geven in Microsoft 365 Services<br>Beveiligings Risico's en-waarschuwingen weer geven<br>Rapporten weergeven
Identity Protection Center | Alle beveiligings rapporten en instellingen voor beveiligings functies lezen<br><ul><li>Anti-spam<li>Versleuteling<li>Preventie van gegevens verlies<li>Anti-malware<li>Geavanceerde beveiliging tegen bedreigingen<li>Anti-phishing<li>Mailstroom regels
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Heeft alleen-lezen toegang tot alle informatie die wordt weer gegeven in Azure AD Privileged Identity Management: beleid en rapporten voor Azure AD-Roltoewijzingen en beveiligings Beoordelingen.<br>U **kunt zich niet** aanmelden voor Azure AD privileged Identity Management of wijzigingen aanbrengen. In de Privileged Identity Management Portal of via Power shell kan iemand met deze rol aanvullende rollen activeren (bijvoorbeeld globale beheerder of beheerdersrol), als de gebruiker hiervoor in aanmerking komt.
[Office 365 Security & compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid bekijken<br>Beveiligings Risico's weer geven en onderzoeken<br>Rapporten weergeven
Windows Defender ATP en EDR | Waarschuwingen weer geven en onderzoeken. Wanneer u op rollen gebaseerd toegangs beheer inschakelt in Windows Defender ATP, hebben gebruikers met alleen-lezen machtigingen, zoals de rol Azure AD-beveiligings lezer, geen toegang meer tot ze zijn toegewezen aan een Windows Defender ATP-rol.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | kan alleen gebruikers-, apparaat-, inschrijvings-, configuratie- en toepassingsgegevens weergeven. Kan geen wijzigingen aan Intune aanbrengen.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Heeft alleen-lezen-machtigingen en kan waarschuwingen beheren
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar kan geen wijzigingen aanbrengen
[Office 365-service status](https://docs.microsoft.com/office365/enterprise/view-service-health) | De status van Office 365-services weer geven

### <a name="service-support-administrator"></a>[Ondersteunings beheerder voor service](#service-support-administrator-permissions)

Gebruikers met deze rol kunnen ondersteunings aanvragen openen met micro soft voor Azure en Office 365-Services, en het service dashboard en berichten centrum weer geven in het [Azure Portal](https://portal.azure.com) en [Microsoft 365 beheer centrum](https://admin.microsoft.com). Meer informatie [over beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Voorheen heeft deze rol de naam ' service beheerder ' in [Azure Portal](https://portal.azure.com) en [Microsoft 365 beheer centrum](https://admin.microsoft.com). We hebben de naam gewijzigd in ' service ondersteunings beheerder ' om uit te lijnen met de bestaande-naam in Microsoft Graph-API, Azure AD Graph API en Azure AD Power shell.

### <a name="sharepoint-administrator"></a>[Share point-beheerder](#sharepoint-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen in micro soft share point online, wanneer de service aanwezig is, evenals de mogelijkheid om alle Office 365-groepen te maken en beheren, ondersteunings tickets te beheren en de service status te controleren. Meer informatie [over beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als share point-service beheerder. Het is share point-beheerder in de [Azure Portal](https://portal.azure.com).

### <a name="skype-for-business--lync-administrator"></a>[Skype voor bedrijven/Lync-beheerder](#lync-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen in micro soft Skype voor bedrijven, wanneer de service aanwezig is, en het beheren van Skype-specifieke gebruikers kenmerken in Azure Active Directory. Daarnaast verleent deze rol de mogelijkheid om ondersteunings tickets te beheren en de service status te controleren en om toegang te krijgen tot de teams en het beheer centrum van Skype voor bedrijven. Het account moet ook een licentie hebben voor teams of Power shell-cmdlets kunnen niet worden uitgevoerd. Meer informatie over de licentie gegevens voor [de rol van Skype voor bedrijven-beheerder](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) en teams in [Skype voor bedrijven en micro soft teams-invoeg toepassingen](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> In de Microsoft Graph-API en Azure AD Power shell wordt deze rol aangeduid als Lync-service beheerder. Het is "Skype voor bedrijven-beheerder" in de [Azure Portal](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Teams communicatie beheerder](#teams-communications-administrator-permissions)

Gebruikers met deze rol kunnen aspecten van de werk belasting van micro soft teams beheren die betrekking hebben op spraak & telefonie. Dit omvat de beheer hulpprogramma's voor telefoon nummer toewijzing, spraak-en Vergader beleid en volledige toegang tot de Call Analytics-hulp programmaset.

### <a name="teams-communications-support-engineer"></a>[Mede werker communicatie technicus voor teams](#teams-communications-support-engineer-permissions)

Gebruikers met deze rol kunnen communicatie problemen in micro soft-teams oplossen & Skype voor bedrijven met behulp van de hulp middelen voor het oplossen van problemen met gebruikers aanroepen in het micro soft teams & Skype voor bedrijven-beheer centrum. Gebruikers met deze rol kunnen volledige informatie over de oproep record voor alle betrokken deel nemers weer geven. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

### <a name="teams-communications-support-specialist"></a>[Specialist communicatie ondersteuning teams](#teams-communications-support-specialist-permissions)

Gebruikers met deze rol kunnen communicatie problemen in micro soft-teams oplossen & Skype voor bedrijven met behulp van de hulp middelen voor het oplossen van problemen met gebruikers aanroepen in het micro soft teams & Skype voor bedrijven-beheer centrum. Gebruikers met deze rol kunnen alleen gebruikers details weer geven in de aanroep voor de specifieke gebruiker die ze hebben gezocht. Deze rol heeft geen toegang voor het weer geven, maken of beheren van ondersteunings tickets.

### <a name="teams-service-administrator"></a>[Teams service-beheerder](#teams-service-administrator-permissions)

Gebruikers met deze rol kunnen alle aspecten van de werk belasting van micro soft teams beheren via micro soft teams & het beheer centrum van Skype voor bedrijven en de respectieve Power shell-modules. Dit omvat onder andere alle beheer hulpprogramma's die betrekking hebben op telefonie, berichten, vergaderingen en de teams zelf. Deze rol verleent daarnaast de mogelijkheid om alle Office 365-groepen te maken en beheren, ondersteunings tickets te beheren en de service status te controleren.

### <a name="user-administrator"></a>[Gebruikers beheerder](#user-administrator-permissions)

Gebruikers met deze rol kunnen gebruikers maken en alle aspecten van gebruikers met enkele beperkingen beheren (zie hieronder) en het verloop beleid voor wacht woorden kan bijwerken. Daarnaast kunnen gebruikers met deze rol alle groepen maken en beheren. Deze rol omvat ook de mogelijkheid om gebruikers weergaven te maken en beheren, ondersteunings tickets te beheren en de service status te controleren. Gebruikers beheerders hebben geen machtiging om bepaalde gebruikers eigenschappen voor gebruikers te beheren in de meeste beheerders rollen. Gebruiker met deze rol heeft geen machtigingen voor het beheren van MFA. De functies die uitzonde ringen op deze beperking zijn, worden weer gegeven in de volgende tabel.

| | |
| --- | --- |
|Algemene machtigingen|<p>Gebruikers en groepen maken</p><p>Gebruikersweergaven maken en beheren</p><p>Office-ondersteunings tickets beheren<p>Verloop beleid voor wacht woorden bijwerken|
|<p>Voor alle gebruikers, inclusief alle beheerders</p>|<p>Licenties beheren</p><p>Alle gebruikers eigenschappen beheren, met uitzonde ring van Principal-naam van gebruiker</p>
|Alleen voor gebruikers die niet-beheerders zijn of in een van de volgende beperkte beheerders rollen:<ul><li>Adreslijst lezers<li>Gast uitnodiging<li>Helpdesk beheerder<li>Berichten centrum-lezer<li>Rapport lezer<li>Gebruikers beheerder|<p>Verwijderen en herstellen</p><p>Uitschakelen en inschakelen</p><p>Vernieuwings tokens ongeldig maken</p><p>Alle gebruikers eigenschappen beheren, met inbegrip van Principal-naam van gebruiker</p><p>Wachtwoord opnieuw instellen</p><p>Apparaatinstellingen bijwerken (FIDO)</p>|

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wacht woorden wijzigen voor personen die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van het wacht woord van een gebruiker kan betekenen dat de identiteit en machtigingen van de gebruiker worden aangenomen. Bijvoorbeeld:
>
>- Toepassings registratie en eigen aren van bedrijfs toepassingen, die referenties kunnen beheren van apps waarvan ze eigenaar zijn. Deze apps hebben mogelijk privileged-machtigingen in azure AD en andere personen die niet aan gebruikers beheerders zijn toegekend. Via dit pad kan een gebruikers beheerder mogelijk de identiteit van een toepassings eigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigen aars van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of persoonlijke informatie of een kritieke configuratie in Azure.
>- Groeps eigenaren van beveiligings groep en Office 365, wie groepslid maatschap kan beheren. Deze groepen kunnen toegang verlenen tot gevoelige of persoonlijke informatie of kritieke configuratie in azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en Human Resources Systems.
>- Niet-beheerders als leidinggevenden, juridisch adviseur en Human Resources-werk nemers die mogelijk toegang tot gevoelige of persoonlijke informatie hebben.

## <a name="role-permissions"></a>Rolmachtigingen

In de volgende tabellen worden de specifieke machtigingen in Azure Active Directory beschreven die aan elke rol worden gegeven. Sommige rollen hebben mogelijk extra machtigingen in micro soft-services buiten Azure Active Directory.

### <a name="application-administrator-permissions"></a>Machtigingen voor de toepassings beheerder

Kan alle aspecten van app-registraties en bedrijfs-apps maken en beheren.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/Application/appProxyAuthentication/update | De eigenschappen van de verificatie van de app-proxy voor service-principals in Azure Active Directory bijwerken. |
| micro soft. Directory/Application/appProxyUrlSettings/update | Werk de interne en externe URL'S van de toepassings proxy bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/applicationProxy/lezen | Alle app-proxy-eigenschappen lezen. |
| micro soft. Directory/toepassingen/applicationProxy/update | Alle app-proxy-eigenschappen bijwerken. |
| micro soft. Directory/toepassingen/publiek/update | Werk de eigenschap Applications. Audience bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/verificatie/bijwerken | Werk de eigenschap Applications. Authentication bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen van toepassingen in Azure Active Directory bijwerken. |
| micro soft. Directory/toepassingen/maken | Toepassingen maken in Azure Active Directory. |
| micro soft. Directory/toepassingen/referenties/bijwerken | Werk de eigenschap Applications. credentials bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/verwijderen | Verwijder toepassingen in Azure Active Directory. |
| micro soft. Directory/toepassingen/eigen aren/bijwerken | Werk de eigenschap Applications. Owners bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/machtigingen/bijwerken | Werk de eigenschap Applications. permissions bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/beleid/update | Werk de eigenschap Applications. policies bij in Azure Active Directory. |
| micro soft. map/appRoleAssignments/maken | Maak appRoleAssignments in Azure Active Directory. |
| micro soft. map/appRoleAssignments/lezen | Lees appRoleAssignments in Azure Active Directory. |
| micro soft. Directory/appRoleAssignments/update | AppRoleAssignments bijwerken in Azure Active Directory. |
| micro soft. Directory/appRoleAssignments/verwijderen | Verwijder appRoleAssignments in Azure Active Directory. |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. map/connectorGroups/alle/lezen | Lees de eigenschappen van connector groep voor toepassings proxy in Azure Active Directory. |
| micro soft. map/connectorGroups/alle/update | Werk alle eigenschappen van de connector groep van de toepassings proxy bij in Azure Active Directory. |
| micro soft. map/connectorGroups/maken | Maak Application proxy connector-groepen in Azure Active Directory. |
| micro soft. Directory/connectorGroups/verwijderen | Toepassings proxy connector groepen verwijderen in Azure Active Directory. |
| micro soft. map/connectors/alles/lezen | Lees alle eigenschappen van de toepassings proxy connector in Azure Active Directory. |
| micro soft. map/connectors/maken | Application proxy-connectors maken in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Basic/Read | Lees de eigenschap policies. applicationConfiguration in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Basic/update | Werk de eigenschap policies. applicationConfiguration bij in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Create | Beleids regels maken in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/verwijderen | Beleids regels verwijderen in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/eigen aren/lezen | Lees de eigenschap policies. applicationConfiguration in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Owners/update | Werk de eigenschap policies. applicationConfiguration bij in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/policyAppliedTo/lezen | Lees de eigenschap policies. applicationConfiguration in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/appRoleAssignedTo/update | Werk de eigenschap servicePrincipals. appRoleAssignedTo bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/appRoleAssignments/update | Werk de eigenschap servicePrincipals. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/Audience/update | Werk de eigenschap servicePrincipals. Audience bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/verificatie/bijwerken | Werk de eigenschap servicePrincipals. Authentication bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/Basic/update | Werk de basis eigenschappen van servicePrincipals bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/maken | Maak servicePrincipals in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/credentials/update | Werk de eigenschap servicePrincipals. credentials bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/verwijderen | Verwijder servicePrincipals in Azure Active Directory. |
| micro soft. map/servicePrincipals/eigen aren/bijwerken | Werk de eigenschap servicePrincipals. Owners bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/permissions/update | Werk de eigenschap servicePrincipals. permissions bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals. policies bij in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="application-developer-permissions"></a>Ontwikkelaars machtigingen voor toepassingen

Kan toepassings registraties maken onafhankelijk van de instelling gebruikers kunnen toepassingen registreren.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/toepassingen/createAsOwner | Toepassingen maken in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. Directory/appRoleAssignments/createAsOwner | Maak appRoleAssignments in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. Directory/oAuth2PermissionGrants/createAsOwner | Maak oAuth2PermissionGrants in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. Directory/servicePrincipals/createAsOwner | Maak servicePrincipals in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |

### <a name="authentication-administrator-permissions"></a>Verificatie beheerders machtigingen

Voor het weer geven, instellen en opnieuw instellen van verificatie methode-informatie voor een niet-beheerders gebruiker.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/gebruikers/invalidateAllRefreshTokens | De tokens voor het vernieuwen van alle gebruikers in Azure Active Directory ongeldig te maken. |
| micro soft. Directory/Users/strongAuthentication/update | Geavanceerde verificatie-eigenschappen zoals MFA-referentie gegevens bijwerken. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. map/gebruikers/wacht woord/bijwerken | Update wacht woorden voor alle gebruikers in de Office 365-organisatie. Raadpleeg de online documentatie voor meer informatie. |

### <a name="azure-devops-administrator-permissions"></a>Beheerders machtigingen voor Azure DevOps

Kan het Azure DevOps-organisatie beleid en-instellingen beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie [Beschrijving van rol](#azure-devops-administrator) hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. devOps/allTasks | Lees en configureer Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Beheerders machtigingen Azure Information Protection

Kan alle aspecten van de Azure Information Protection-Service beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie [Beschrijving van rol](#) hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. informationProtection/allTasks | Beheer alle aspecten van Azure Information Protection. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF sleutelsetcursor

Geheimen voor Federatie en versleuteling beheren in het Framework voor identiteits ervaring.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. B2C/trustFramework/Series/allTasks | Lees en configureer sleutel sets in Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF Policy Administrator-machtigingen

Beleid voor vertrouwens relaties maken en beheren in het Framework voor identiteits ervaring.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. B2C/trustFramework/policies/allTasks | Aangepaste beleids regels lezen en configureren in Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Facturerings beheerders machtigingen

Kan algemene taken met betrekking tot facturering uitvoeren, zoals het bijwerken van betalings gegevens.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/organisatie/basis/bijwerken | Basis eigenschappen van de organisatie in Azure Active Directory bijwerken. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. commerce. facturering/toerekeningen/allTasks | Beheer alle aspecten van de facturering. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="cloud-application-administrator-permissions"></a>Beheerders machtigingen voor Cloud toepassingen

Kan alle aspecten van app-registraties en bedrijfs-apps maken en beheren, behalve app proxy.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/toepassingen/publiek/update | Werk de eigenschap Applications. Audience bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/verificatie/bijwerken | Werk de eigenschap Applications. Authentication bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen van toepassingen in Azure Active Directory bijwerken. |
| micro soft. Directory/toepassingen/maken | Toepassingen maken in Azure Active Directory. |
| micro soft. Directory/toepassingen/referenties/bijwerken | Werk de eigenschap Applications. credentials bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/verwijderen | Verwijder toepassingen in Azure Active Directory. |
| micro soft. Directory/toepassingen/eigen aren/bijwerken | Werk de eigenschap Applications. Owners bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/machtigingen/bijwerken | Werk de eigenschap Applications. permissions bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/beleid/update | Werk de eigenschap Applications. policies bij in Azure Active Directory. |
| micro soft. map/appRoleAssignments/maken | Maak appRoleAssignments in Azure Active Directory. |
| micro soft. Directory/appRoleAssignments/update | AppRoleAssignments bijwerken in Azure Active Directory. |
| micro soft. Directory/appRoleAssignments/verwijderen | Verwijder appRoleAssignments in Azure Active Directory. |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Create | Beleids regels maken in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Basic/Read | Lees de eigenschap policies. applicationConfiguration in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Basic/update | Werk de eigenschap policies. applicationConfiguration bij in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/verwijderen | Beleids regels verwijderen in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/eigen aren/lezen | Lees de eigenschap policies. applicationConfiguration in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/Owners/update | Werk de eigenschap policies. applicationConfiguration bij in Azure Active Directory. |
| micro soft. Directory/policies/applicationConfiguration/policyAppliedTo/lezen | Lees de eigenschap policies. applicationConfiguration in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/appRoleAssignedTo/update | Werk de eigenschap servicePrincipals. appRoleAssignedTo bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/appRoleAssignments/update | Werk de eigenschap servicePrincipals. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/Audience/update | Werk de eigenschap servicePrincipals. Audience bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/verificatie/bijwerken | Werk de eigenschap servicePrincipals. Authentication bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/Basic/update | Werk de basis eigenschappen van servicePrincipals bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/maken | Maak servicePrincipals in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/credentials/update | Werk de eigenschap servicePrincipals. credentials bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/verwijderen | Verwijder servicePrincipals in Azure Active Directory. |
| micro soft. map/servicePrincipals/eigen aren/bijwerken | Werk de eigenschap servicePrincipals. Owners bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/permissions/update | Werk de eigenschap servicePrincipals. permissions bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals. policies bij in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="cloud-device-administrator-permissions"></a>Beheerders machtigingen voor Cloud apparaten

Volledige toegang tot het beheren van apparaten in azure AD.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. Directory/apparaten/bitLockerRecoveryKeys/lezen | Lees de eigenschap devices. bitLockerRecoveryKeys in Azure Active Directory. |
| micro soft. Directory/apparaten/verwijderen | Apparaten verwijderen in Azure Active Directory. |
| micro soft. map/apparaten/uitschakelen | Apparaten uitschakelen in Azure Active Directory. |
| micro soft. map/apparaten/inschakelen | Apparaten inschakelen in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |

### <a name="company-administrator-permissions"></a>Beheerders machtigingen voor het bedrijf

Kan alle aspecten beheren van Azure AD en micro soft-services die gebruikmaken van Azure AD-identiteiten. Deze rol wordt ook wel de rol globale beheerder genoemd. 

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. cloudAppSecurity/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. Aad. cloudAppSecurity en werk deze bij. |
| micro soft. Directory/administrativeUnits/allProperties/allTasks | Maak en verwijder administrativeUnits en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/toepassingen/allProperties/allTasks | Maak en verwijder toepassingen en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/appRoleAssignments/allProperties/allTasks | Maak en verwijder appRoleAssignments en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. Directory/Contacts/allProperties/allTasks | Maak en Verwijder contact personen en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/contracten/allProperties/allTasks | Maak en verwijder contracten en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/apparaten/allProperties/allTasks | Maak en Verwijder apparaten en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/directoryRoles/allProperties/allTasks | Maak en verwijder directoryRoles en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/directoryRoleTemplates/allProperties/allTasks | Maak en verwijder directoryRoleTemplates en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/domeinen/allProperties/allTasks | Maak en verwijder domeinen en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/groepen/allProperties/allTasks | Maak en verwijder groepen en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/groupSettings/allProperties/allTasks | Maak en verwijder groupSettings en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/groupSettingTemplates/allProperties/allTasks | Maak en verwijder groupSettingTemplates en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/loginTenantBranding/allProperties/allTasks | Maak en verwijder loginTenantBranding en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Maak en verwijder oAuth2PermissionGrants en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. map/organisatie-allProperties/allTasks | Maak en verwijder organisatie en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/policies/allProperties/allTasks | Maak en verwijder beleids regels en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/roleAssignments/allProperties/allTasks | Maak en verwijder roleAssignments en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/roleDefinitions/allProperties/allTasks | Maak en verwijder roleDefinitions en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/scopedRoleMemberships/allProperties/allTasks | Maak en verwijder scopedRoleMemberships en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. Directory/serviceAction/activateService | Kan de Activateservice-service actie uitvoeren in Azure Active Directory |
| micro soft. Directory/serviceAction/disableDirectoryFeature | Kan de Disabledirectoryfeature-service actie uitvoeren in Azure Active Directory |
| micro soft. Directory/serviceAction/enableDirectoryFeature | Kan de Enabledirectoryfeature-service actie uitvoeren in Azure Active Directory |
| micro soft. Directory/serviceAction/getAvailableExtentionProperties | Kan de Getavailableextentionproperties-service actie uitvoeren in Azure Active Directory |
| micro soft. Directory/servicePrincipals/allProperties/allTasks | Maak en verwijder servicePrincipals en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. Directory/subscribedSkus/allProperties/allTasks | Maak en verwijder subscribedSkus en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. map/users/allProperties/allTasks | Maak en verwijder gebruikers en Lees alle eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. directorySync/-beleeningen/allTasks | Alle acties in Azure AD Connect uitvoeren. |
| micro soft. Aad. identityProtection/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. Aad. identityProtection en werk deze bij. |
| micro soft. Aad. privilegedIdentityManagement/aldaar/Lees | Alle resources in micro soft. Aad. privilegedIdentityManagement lezen. |
| micro soft. Azure. advancedThreatProtection/Alvan de aflezingen/lezen | Lees alle resources in micro soft. Azure. advancedThreatProtection. |
| micro soft. Azure. informationProtection/allTasks | Beheer alle aspecten van Azure Information Protection. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. commerce. facturering/toerekeningen/allTasks | Beheer alle aspecten van de facturering. |
| micro soft. intune/toestemmingen/allTasks | Beheer alle aspecten van intune. |
| micro soft. office365. complianceManager/cons/allTasks | Alle aspecten van Office 365-nalevings beheer beheren |
| micro soft. office365. desktopAnalytics/cons/allTasks | Beheer alle aspecten van Desktop Analytics. |
| micro soft. office365. Exchange/alallTasks | Beheer alle aspecten van Exchange Online. |
| micro soft. office365. lockbox/cons/allTasks | Alle aspecten van Office 365 Klanten-lockbox beheren |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. messageCenter/securityMessages/lezen | Lees securityMessages in micro soft. office365. messageCenter. |
| micro soft. office365. protectionCenter/cons/allTasks | Beheer alle aspecten van Office 365 Protection Center. |
| micro soft. office365. securityComplianceCenter/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. office365. securityComplianceCenter en werk deze bij. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. share point/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. office365. share point en werk deze bij. |
| micro soft. office365. skypeForBusiness/cons/allTasks | Beheer alle aspecten van Skype voor bedrijven online. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. usageReports/de aflezingen/lezen | Lees de gebruiks rapporten van Office 365. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. powerApps. dynamics365/allTasks | Beheer alle aspecten van Dynamics 365. |
| micro soft. powerApps. powerBI/allTasks | Beheer alle aspecten van Power BI. |
| micro soft. Windows. defenderAdvancedThreatProtection/geleenheden/lezen | Alle resources in micro soft. Windows. defenderAdvancedThreatProtection lezen. |

### <a name="compliance-administrator-permissions"></a>Beheerders machtigingen voor naleving

Kan nalevings configuratie en-rapporten in azure AD en Office 365 lezen en beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. complianceManager/cons/allTasks | Alle aspecten van Office 365-nalevings beheer beheren |
| micro soft. office365. Exchange/alallTasks | Beheer alle aspecten van Exchange Online. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. share point/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. office365. share point en werk deze bij. |
| micro soft. office365. skypeForBusiness/cons/allTasks | Beheer alle aspecten van Skype voor bedrijven online. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="compliance-data-administrator-permissions"></a>Beheerders machtigingen voor nalevings gegevens

Hiermee wordt inhoud voor naleving gemaakt en beheerd.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. cloudAppSecurity/allTasks | Microsoft Cloud App Security lezen en configureren. |
| micro soft. Azure. informationProtection/allTasks | Beheer alle aspecten van Azure Information Protection. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. complianceManager/cons/allTasks | Alle aspecten van Office 365-nalevings beheer beheren |
| micro soft. office365. Exchange/alallTasks | Beheer alle aspecten van Exchange Online. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. share point/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. office365. share point en werk deze bij. |
| micro soft. office365. skypeForBusiness/cons/allTasks | Beheer alle aspecten van Skype voor bedrijven online. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="conditional-access-administrator-permissions"></a>Beheerders machtigingen voor voorwaardelijke toegang

Kan mogelijkheden voor voorwaardelijke toegang beheren.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/policies/conditionalAccess/Basic/Read | Lees de eigenschap policies. conditionalAccess in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/Basic/update | Werk de eigenschap policies. conditionalAccess bij in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/Create | Beleids regels maken in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/verwijderen | Beleids regels verwijderen in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/eigen aren/lezen | Lees de eigenschap policies. conditionalAccess in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/Owners/update | Werk de eigenschap policies. conditionalAccess bij in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/policiesAppliedTo/lezen | Lees de eigenschap policies. conditionalAccess in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/tenantDefault/update | Werk de eigenschap policies. conditionalAccess bij in Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Beheerders machtigingen voor CRM-Service

Kan alle aspecten van het product Dynamics 365 beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. powerApps. dynamics365/allTasks | Beheer alle aspecten van Dynamics 365. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="customer-lockbox-access-approver-permissions"></a>Machtigingen voor toegang tot de gebruikers-LockBox

Kan micro soft-ondersteunings aanvragen goed keuren voor toegang tot de organisatie gegevens van de klant.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. lockbox/cons/allTasks | Alle aspecten van Office 365 Klanten-lockbox beheren |

### <a name="desktop-analytics-administrator-permissions"></a>Beheerders machtigingen voor desktop Analytics

Kan de bureau blad Analytics en de aanpassing van Office-&-beleids Services beheren. Voor desktop Analytics is dit onder andere de mogelijkheid om inventarisatie van assets te bekijken, implementatie plannen te maken, implementatie en status weer te geven. Voor Office Customization & Policy service kunnen gebruikers met deze rol Office-beleid beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. desktopAnalytics/cons/allTasks | Beheer alle aspecten van Desktop Analytics. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="device-administrators-permissions"></a>Beheerders machtigingen voor apparaten

Gebruikers die aan deze rol zijn toegewezen, worden toegevoegd aan de lokale groep Administrators op apparaten die lid zijn van Azure AD.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/groupSettings/Basic/lezen | Lees de basis eigenschappen van groupSettings in Azure Active Directory. |
| micro soft. map/groupSettingTemplates/Basic/lezen | Lees de basis eigenschappen van groupSettingTemplates in Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Machtigingen voor Directory lezers
Kan basis informatie over de Directory lezen. Voor het verlenen van toegang tot toepassingen, niet bedoeld voor gebruikers.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/administrativeUnits/Basic/lezen | Lees de basis eigenschappen van administrativeUnits in Azure Active Directory. |
| micro soft. map/administrativeUnits/leden/lezen | Lees de eigenschap administrativeUnits. members in Azure Active Directory. |
| micro soft. Directory/toepassingen/basis/lezen | Lees de basis eigenschappen van toepassingen in Azure Active Directory. |
| micro soft. Directory/toepassingen/eigen aren/lezen | Lees de eigenschap Applications. Owners in Azure Active Directory. |
| micro soft. Directory/toepassingen/beleid/lezen | Lees de eigenschap toepassingen. policies in Azure Active Directory. |
| micro soft. map/Contacts/basis/lezen | Lees de basis eigenschappen van contact personen in Azure Active Directory. |
| micro soft. map/contact personen/memberOf/lezen | Lees de eigenschap Contacts. memberOf in Azure Active Directory. |
| micro soft. map/contracten/basis/lezen | Lees de basis eigenschappen voor contracten in Azure Active Directory. |
| micro soft. map/apparaten/basis/lezen | Lees de basis eigenschappen van apparaten in Azure Active Directory. |
| micro soft. Directory/apparaten/memberOf/lezen | Lees de eigenschap devices. memberOf in Azure Active Directory. |
| micro soft. Directory/apparaten/registeredOwners/lezen | Lees de eigenschap devices. registeredOwners in Azure Active Directory. |
| micro soft. Directory/apparaten/registeredUsers/lezen | Lees de eigenschap devices. registeredUsers in Azure Active Directory. |
| micro soft. map/directoryRoles/Basic/lezen | Lees de basis eigenschappen van directoryRoles in Azure Active Directory. |
| micro soft. map/directoryRoles/eligibleMembers/lezen | Lees de eigenschap directoryRoles. eligibleMembers in Azure Active Directory. |
| micro soft. map/directoryRoles/leden/lezen | Lees de eigenschap directoryRoles. members in Azure Active Directory. |
| micro soft. Directory/domeinen/basis/lezen | Lees de basis eigenschappen van domeinen in Azure Active Directory. |
| micro soft. Directory/groepen/appRoleAssignments/lezen | Lees de eigenschap groups. appRoleAssignments in Azure Active Directory. |
| micro soft. map/groepen/basis/lezen | Lees de basis eigenschappen voor groepen in Azure Active Directory. |
| micro soft. Directory/groepen/memberOf/lezen | Lees de eigenschap groups. memberOf in Azure Active Directory. |
| micro soft. map/groepen/leden/lezen | Lees de eigenschap groups. members in Azure Active Directory. |
| micro soft. map/groepen/eigen aren/lezen | Lees de eigenschap groups. Owners in Azure Active Directory. |
| micro soft. map/groepen/instellingen/lezen | Lees de eigenschap groups. settings in Azure Active Directory. |
| micro soft. map/groupSettings/Basic/lezen | Lees de basis eigenschappen van groupSettings in Azure Active Directory. |
| micro soft. map/groupSettingTemplates/Basic/lezen | Lees de basis eigenschappen van groupSettingTemplates in Azure Active Directory. |
| micro soft. map/oAuth2PermissionGrants/Basic/lezen | Lees de basis eigenschappen van oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/organisatie/basis/lezen | Lees de basis eigenschappen van de organisatie in Azure Active Directory. |
| micro soft. map/organisatie/trustedCAsForPasswordlessAuth/lezen | Lees de eigenschap Organization. trustedCAsForPasswordlessAuth in Azure Active Directory. |
| micro soft. map/roleAssignments/Basic/lezen | Lees de basis eigenschappen van roleAssignments in Azure Active Directory. |
| micro soft. map/roleDefinitions/Basic/lezen | Lees de basis eigenschappen van roleDefinitions in Azure Active Directory. |
| micro soft. map/servicePrincipals/appRoleAssignedTo/lezen | Lees de eigenschap servicePrincipals. appRoleAssignedTo in Azure Active Directory. |
| micro soft. map/servicePrincipals/appRoleAssignments/lezen | Lees de eigenschap servicePrincipals. appRoleAssignments in Azure Active Directory. |
| micro soft. map/servicePrincipals/Basic/lezen | Lees de basis eigenschappen van servicePrincipals in Azure Active Directory. |
| micro soft. map/servicePrincipals/memberOf/lezen | Lees de eigenschap servicePrincipals. memberOf in Azure Active Directory. |
| micro soft. map/servicePrincipals/oAuth2PermissionGrants/Basic/lezen | Lees de eigenschap servicePrincipals. oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/servicePrincipals/ownedObjects/lezen | Lees de eigenschap servicePrincipals. ownedObjects in Azure Active Directory. |
| micro soft. map/servicePrincipals/eigen aren/lezen | Lees de eigenschap servicePrincipals. Owners in Azure Active Directory. |
| micro soft. map/servicePrincipals/beleid/lezen | Lees de eigenschap servicePrincipals. policies in Azure Active Directory. |
| micro soft. map/subscribedSkus/Basic/lezen | Lees de basis eigenschappen van subscribedSkus in Azure Active Directory. |
| micro soft. map/gebruikers/appRoleAssignments/lezen | Lees de eigenschap users. appRoleAssignments in Azure Active Directory. |
| micro soft. map/gebruikers/basis/lezen | Lees de basis eigenschappen van gebruikers in Azure Active Directory. |
| micro soft. map/gebruikers/directReports/lezen | Lees de eigenschap users. directReports in Azure Active Directory. |
| micro soft. map/gebruikers/manager/lezen | Lees de eigenschap users. manager in Azure Active Directory. |
| micro soft. map/gebruikers/memberOf/lezen | Lees de eigenschap users. memberOf in Azure Active Directory. |
| micro soft. map/users/oAuth2PermissionGrants/Basic/lezen | Lees de eigenschap users. oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/gebruikers/ownedDevices/lezen | Lees de eigenschap users. ownedDevices in Azure Active Directory. |
| micro soft. map/gebruikers/ownedObjects/lezen | Lees de eigenschap users. ownedObjects in Azure Active Directory. |
| micro soft. map/gebruikers/registeredDevices/lezen | Lees de eigenschap users. registeredDevices in Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Machtigingen voor Directory synchronisatie accounts

Wordt alleen gebruikt door Azure AD Connect service.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/organisatie/dirSync/update | Werk de eigenschap Organization. dirSync bij in Azure Active Directory. |
| micro soft. Directory/beleid/maken | Beleids regels maken in Azure Active Directory. |
| micro soft. Directory/beleid/verwijderen | Beleids regels verwijderen in Azure Active Directory. |
| micro soft. Directory/beleid/basis/lezen | Lees de basis eigenschappen van het beleid in Azure Active Directory. |
| micro soft. Directory/policies/Basic/update | Basis eigenschappen van beleid in Azure Active Directory bijwerken. |
| micro soft. Directory/policies/eigen aren/lezen | Lees de eigenschap policies. Owners in Azure Active Directory. |
| micro soft. Directory/policies/eigen aren/bijwerken | Werk de eigenschap policies. Owners bij in Azure Active Directory. |
| micro soft. Directory/policies/policiesAppliedTo/lezen | Lees de eigenschap policies. policiesAppliedTo in Azure Active Directory. |
| micro soft. Directory/policies/tenantDefault/update | Werk de eigenschap policies. tenantDefault bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/appRoleAssignedTo/lezen | Lees de eigenschap servicePrincipals. appRoleAssignedTo in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/appRoleAssignedTo/update | Werk de eigenschap servicePrincipals. appRoleAssignedTo bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/appRoleAssignments/lezen | Lees de eigenschap servicePrincipals. appRoleAssignments in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/appRoleAssignments/update | Werk de eigenschap servicePrincipals. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/Audience/update | Werk de eigenschap servicePrincipals. Audience bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/verificatie/bijwerken | Werk de eigenschap servicePrincipals. Authentication bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/Basic/lezen | Lees de basis eigenschappen van servicePrincipals in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/Basic/update | Werk de basis eigenschappen van servicePrincipals bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/maken | Maak servicePrincipals in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/credentials/update | Werk de eigenschap servicePrincipals. credentials bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/memberOf/lezen | Lees de eigenschap servicePrincipals. memberOf in Azure Active Directory. |
| micro soft. map/servicePrincipals/oAuth2PermissionGrants/Basic/lezen | Lees de eigenschap servicePrincipals. oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/servicePrincipals/eigen aren/lezen | Lees de eigenschap servicePrincipals. Owners in Azure Active Directory. |
| micro soft. map/servicePrincipals/eigen aren/bijwerken | Werk de eigenschap servicePrincipals. Owners bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/ownedObjects/lezen | Lees de eigenschap servicePrincipals. ownedObjects in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/permissions/update | Werk de eigenschap servicePrincipals. permissions bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/beleid/lezen | Lees de eigenschap servicePrincipals. policies in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals. policies bij in Azure Active Directory. |
| micro soft. directorySync/-beleeningen/allTasks | Alle acties in Azure AD Connect uitvoeren. |

### <a name="directory-writers-permissions"></a>Machtigingen voor Directory Writers

Kan basis informatie over de Directory lezen & schrijven. Voor het verlenen van toegang tot toepassingen, niet bedoeld voor gebruikers.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/groepen/maken | Groepen maken in Azure Active Directory. |
| micro soft. Directory/groepen/createAsOwner | Groepen maken in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. Directory/groepen/appRoleAssignments/update | Werk de eigenschap groups. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen in Azure Active Directory bijwerken. |
| micro soft. map/groepen/leden/bijwerken | Werk de eigenschap groups. members bij in Azure Active Directory. |
| micro soft. Directory/groepen/eigen aren/bijwerken | Werk de eigenschap groups. Owners bij in Azure Active Directory. |
| micro soft. Directory/groepen/instellingen/bijwerken | Werk de eigenschap groups. settings bij in Azure Active Directory. |
| micro soft. Directory/groupSettings/Basic/update | Werk de basis eigenschappen van groupSettings bij in Azure Active Directory. |
| micro soft. map/groupSettings/maken | Maak groupSettings in Azure Active Directory. |
| micro soft. Directory/groupSettings/verwijderen | Verwijder groupSettings in Azure Active Directory. |
| micro soft. Directory/Users/appRoleAssignments/update | Werk de eigenschap users. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/gebruikers/assignLicense | Licenties op gebruikers beheren in Azure Active Directory. |
| micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers in Azure Active Directory bijwerken. |
| micro soft. Directory/gebruikers/invalidateAllRefreshTokens | De tokens voor het vernieuwen van alle gebruikers in Azure Active Directory ongeldig te maken. |
| micro soft. map/gebruikers/Manager/Update | Werk de eigenschap users. Manager bij in Azure Active Directory. |
| micro soft. map/gebruikers/userPrincipalName/update | Werk de eigenschap users. userPrincipalName bij in Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Beheerders machtigingen voor Exchange-service

Kan alle aspecten van het Exchange-product beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. Directory/groepen/Unified/appRoleAssignments/update | Werk de eigenschap groups. Unified in Azure Active Directory. |
| micro soft. Directory/groepen/Unified/Basic/update | Basis eigenschappen van Office 365-groepen bijwerken. |
| micro soft. Directory/groepen/Unified/Create | Office 365-groepen maken. |
| micro soft. Directory/groepen/Unified/Delete | Verwijder Office 365-groepen. |
| micro soft. map/groepen/Unified/members/update | Update lidmaatschap van Office 365-groepen. |
| micro soft. Directory/groepen/Unified/Owners/update | Het eigendom van Office 365-groepen bijwerken. |
| micro soft. office365. Exchange/alallTasks | Beheer alle aspecten van Exchange Online. |
| micro soft. office365. netwerk/prestaties/allProperties/lezen | Lees de pagina netwerk prestaties in het M365-beheer centrum. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. usageReports/de aflezingen/lezen | Lees de gebruiks rapporten van Office 365. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |

### <a name="external-id-user-flow-administrator-permissions"></a>Beheer machtigingen voor gebruikers stroom externe ID

Alle aspecten van gebruikers stromen maken en beheren.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. B2C/userFlows/allTasks | Gebruikers stromen lezen en configureren in Azure Active Directory B2C. |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>Externe ID gebruikers stroom kenmerk beheer machtigingen

Het kenmerk schema maken en beheren dat beschikbaar is voor alle gebruikers stromen.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. B2C/userAttributes/allTasks | Gebruikers kenmerken lezen en configureren in Azure Active Directory B2C. |

### <a name="external-identity-provider-administrator-permissions"></a>Beheerders machtigingen voor externe ID-providers

Configureer id-providers voor gebruik in directe Federatie.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. B2C/identityProviders/allTasks | Id-providers lezen en configureren in Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Algemene lezers machtigingen
Kan alles lezen dat een globale beheerder wel kan, maar geen bewerkingen kan ondernemen.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie [Beschrijving van rol](#global-reader) hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. commerce. facturering/toerekeningen/lezen    | Lees alle aspecten van de facturering. |
| micro soft. map/administrativeUnits/Basic/lezen    | Lees de basis eigenschappen van administrativeUnits in Azure Active Directory. |
| micro soft. map/administrativeUnits/leden/lezen    | Lees de eigenschap administrativeUnits. members in Azure Active Directory. |
| micro soft. Directory/toepassingen/basis/lezen    | Lees de basis eigenschappen van toepassingen in Azure Active Directory. |
| micro soft. Directory/toepassingen/eigen aren/lezen    | Lees de eigenschap Applications. Owners in Azure Active Directory. |
| micro soft. Directory/toepassingen/beleid/lezen    | Lees de eigenschap toepassingen. policies in Azure Active Directory. |
| micro soft. map/Contacts/basis/lezen    | Lees de basis eigenschappen van contact personen in Azure Active Directory. |
| micro soft. map/contact personen/memberOf/lezen    | Lees de eigenschap Contacts. memberOf in Azure Active Directory. |
| micro soft. map/contracten/basis/lezen    | Lees de basis eigenschappen voor contracten in Azure Active Directory. |
| micro soft. map/apparaten/basis/lezen    | Lees de basis eigenschappen van apparaten in Azure Active Directory. |
| micro soft. Directory/apparaten/memberOf/lezen    | Lees de eigenschap devices. memberOf in Azure Active Directory. |
| micro soft. Directory/apparaten/registeredOwners/lezen    | Lees de eigenschap devices. registeredOwners in Azure Active Directory. |
| micro soft. Directory/apparaten/registeredUsers/lezen    | Lees de eigenschap devices. registeredUsers in Azure Active Directory. |
| micro soft. map/directoryRoles/Basic/lezen    | Lees de basis eigenschappen van directoryRoles in Azure Active Directory. |
| micro soft. map/directoryRoles/eligibleMembers/lezen    | Lees de eigenschap directoryRoles. eligibleMembers in Azure Active Directory. |
| micro soft. map/directoryRoles/leden/lezen    | Lees de eigenschap directoryRoles. members in Azure Active Directory. |
| micro soft. Directory/domeinen/basis/lezen    | Lees de basis eigenschappen van domeinen in Azure Active Directory. |
| micro soft. Directory/groepen/appRoleAssignments/lezen    | Lees de eigenschap groups. appRoleAssignments in Azure Active Directory. |
| micro soft. map/groepen/basis/lezen    | Lees de basis eigenschappen voor groepen in Azure Active Directory. |
| micro soft. Directory/groepen/hiddenMembers/lezen    | Lees de eigenschap groups. hiddenMembers in Azure Active Directory. |
| micro soft. Directory/groepen/memberOf/lezen    | Lees de eigenschap groups. memberOf in Azure Active Directory. |
| micro soft. map/groepen/leden/lezen    | Lees de eigenschap groups. members in Azure Active Directory. |
| micro soft. map/groepen/eigen aren/lezen    | Lees de eigenschap groups. Owners in Azure Active Directory. |
| micro soft. map/groepen/instellingen/lezen    | Lees de eigenschap groups. settings in Azure Active Directory. |
| micro soft. map/groupSettings/Basic/lezen    | Lees de basis eigenschappen van groupSettings in Azure Active Directory. |
| micro soft. map/groupSettingTemplates/Basic/lezen    | Lees de basis eigenschappen van groupSettingTemplates in Azure Active Directory. |
| micro soft. map/oAuth2PermissionGrants/Basic/lezen    | Lees de basis eigenschappen van oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/organisatie/basis/lezen    | Lees de basis eigenschappen van de organisatie in Azure Active Directory. |
| micro soft. map/organisatie/trustedCAsForPasswordlessAuth/lezen    | Lees de eigenschap Organization. trustedCAsForPasswordlessAuth in Azure Active Directory. |
| micro soft. Directory/beleid/standaard/lezen    | Lees het standaard beleid in Azure Active Directory. |
| micro soft. map/roleAssignments/Basic/lezen    | Lees de basis eigenschappen van roleAssignments in Azure Active Directory. |
| micro soft. map/roleDefinitions/Basic/lezen    | Lees de basis eigenschappen van roleDefinitions in Azure Active Directory. |
| micro soft. map/servicePrincipals/appRoleAssignedTo/lezen    | Lees de eigenschap servicePrincipals. appRoleAssignedTo in Azure Active Directory. |
| micro soft. map/servicePrincipals/appRoleAssignments/lezen    | Lees de eigenschap servicePrincipals. appRoleAssignments in Azure Active Directory. |
| micro soft. map/servicePrincipals/Basic/lezen    | Lees de basis eigenschappen van servicePrincipals in Azure Active Directory. |
| micro soft. map/servicePrincipals/memberOf/lezen    | Lees de eigenschap servicePrincipals. memberOf in Azure Active Directory. |
| micro soft. map/servicePrincipals/oAuth2PermissionGrants/Basic/lezen    | Lees de eigenschap servicePrincipals. oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/servicePrincipals/ownedObjects/lezen    | Lees de eigenschap servicePrincipals. ownedObjects in Azure Active Directory. |
| micro soft. map/servicePrincipals/eigen aren/lezen    | Lees de eigenschap servicePrincipals. Owners in Azure Active Directory. |
| micro soft. map/servicePrincipals/beleid/lezen    | Lees de eigenschap servicePrincipals. policies in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen    | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. map/subscribedSkus/Basic/lezen    | Lees de basis eigenschappen van subscribedSkus in Azure Active Directory. |
| micro soft. map/gebruikers/appRoleAssignments/lezen    | Lees de eigenschap users. appRoleAssignments in Azure Active Directory. |
| micro soft. map/gebruikers/basis/lezen    | Lees de basis eigenschappen van gebruikers in Azure Active Directory. |
| micro soft. map/gebruikers/directReports/lezen    | Lees de eigenschap users. directReports in Azure Active Directory. |
| micro soft. map/gebruikers/manager/lezen    | Lees de eigenschap users. manager in Azure Active Directory. |
| micro soft. map/gebruikers/memberOf/lezen    | Lees de eigenschap users. memberOf in Azure Active Directory. |
| micro soft. map/users/oAuth2PermissionGrants/Basic/lezen    | Lees de eigenschap users. oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/gebruikers/ownedDevices/lezen    | Lees de eigenschap users. ownedDevices in Azure Active Directory. |
| micro soft. map/gebruikers/ownedObjects/lezen    | Lees de eigenschap users. ownedObjects in Azure Active Directory. |
| micro soft. map/gebruikers/registeredDevices/lezen    | Lees de eigenschap users. registeredDevices in Azure Active Directory. |
| micro soft. map/gebruikers/strongAuthentication/lezen    | Lees sterke verificatie-eigenschappen, zoals MFA-referentie gegevens. |
| micro soft. office365. Exchange//lezen    | Lees alle aspecten van Exchange Online. |
| micro soft. office365. messageCenter/berichten/lezen    | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. messageCenter/securityMessages/lezen    | Lees securityMessages in micro soft. office365. messageCenter. |
| micro soft. office365. netwerk/prestaties/allProperties/lezen | Lees de pagina netwerk prestaties in het M365-beheer centrum. |
| micro soft. office365. protectionCenter/de aflezingen/lezen    | Lees alle aspecten van Office 365 Protection Center. |
| micro soft. office365. securityComplianceCenter/de aflezingen/lezen    | Lees alle standaard eigenschappen in micro soft. office365. securityComplianceCenter. |
| micro soft. office365. usageReports/de aflezingen/lezen    | Lees de gebruiks rapporten van Office 365. |
| micro soft. office365. webportal/de beleen baarheid/standaard/lezen    | Lees de standaard eigenschappen voor alle resources in micro soft. office365. webportal. |

### <a name="groups-administrator-permissions"></a>Groeps beheerders machtigingen
Kan alle aspecten van groepen en groeps instellingen, zoals naamgeving en verloop beleid, beheren.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/groepen/basis/lezen | Lees de standaard eigenschappen voor groepen in Azure Active Directory.  |
| micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen in Azure Active Directory bijwerken. |
| micro soft. map/groepen/maken | Groepen maken in Azure Active Directory. |
| micro soft. Directory/groepen/createAsOwner | Groepen maken in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. Directory/groepen/verwijderen | Groepen verwijderen in Azure Active Directory. |
| micro soft. Directory/groepen/hiddenMembers/lezen | Lees de eigenschap groups. hiddenMembers in Azure Active Directory. |
| micro soft. map/groepen/leden/bijwerken | Werk de eigenschap groups. members bij in Azure Active Directory. |
| micro soft. Directory/groepen/eigen aren/bijwerken | Werk de eigenschap groups. Owners bij in Azure Active Directory. |
| micro soft. Directory/groepen/herstellen | Groepen herstellen in Azure Active Directory. |
| micro soft. Directory/groepen/instellingen/bijwerken | Werk de eigenschap groups. settings bij in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="guest-inviter-permissions"></a>Machtigingen voor gast Inviter
Kan gast gebruikers uitnodigen onafhankelijk van de instelling leden kunnen gasten uitnodigen.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/gebruikers/appRoleAssignments/lezen | Lees de eigenschap users. appRoleAssignments in Azure Active Directory. |
| micro soft. map/gebruikers/basis/lezen | Lees de basis eigenschappen van gebruikers in Azure Active Directory. |
| micro soft. map/gebruikers/directReports/lezen | Lees de eigenschap users. directReports in Azure Active Directory. |
| micro soft. Directory/gebruikers/inviteGuest | Gast gebruikers uitnodigen in Azure Active Directory. |
| micro soft. map/gebruikers/manager/lezen | Lees de eigenschap users. manager in Azure Active Directory. |
| micro soft. map/gebruikers/memberOf/lezen | Lees de eigenschap users. memberOf in Azure Active Directory. |
| micro soft. map/users/oAuth2PermissionGrants/Basic/lezen | Lees de eigenschap users. oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. map/gebruikers/ownedDevices/lezen | Lees de eigenschap users. ownedDevices in Azure Active Directory. |
| micro soft. map/gebruikers/ownedObjects/lezen | Lees de eigenschap users. ownedObjects in Azure Active Directory. |
| micro soft. map/gebruikers/registeredDevices/lezen | Lees de eigenschap users. registeredDevices in Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Beheerders machtigingen voor helpdesk

Kan wacht woorden voor niet-beheerders en helpdesk beheerders opnieuw instellen.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/apparaten/bitLockerRecoveryKeys/lezen | Lees de eigenschap devices. bitLockerRecoveryKeys in Azure Active Directory. |
| micro soft. Directory/gebruikers/invalidateAllRefreshTokens | De tokens voor het vernieuwen van alle gebruikers in Azure Active Directory ongeldig te maken. |
| micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden bijwerken voor alle gebruikers in Azure Active Directory. Raadpleeg de online documentatie voor meer informatie. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="hybrid-identity-administrator-permissions"></a>Beheerders machtigingen voor hybride identiteit

Cloud inrichting en verificatie services inschakelen, implementeren, configureren, beheren, controleren en problemen oplossen. 

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. Directory/toepassingen/publiek/update  | Werk de eigenschap Applications. Audience bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/verificatie/bijwerken | Werk de eigenschap Applications. Authentication bij in Azure Active Directory.  |
| micro soft. Directory/toepassingen/basis/bijwerken | Basis eigenschappen van toepassingen in Azure Active Directory bijwerken. |
| micro soft. Directory/toepassingen/maken | Toepassingen maken in Azure Active Directory. |
| micro soft. Directory/toepassingen/referenties/bijwerken | Werk de eigenschap Applications. credentials bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/verwijderen | Verwijder toepassingen in Azure Active Directory. |
| micro soft. Directory/toepassingen/eigen aren/bijwerken | Werk de eigenschap Applications. Owners bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/machtigingen/bijwerken | Werk de eigenschap Applications. permissions bij in Azure Active Directory. |
| micro soft. Directory/toepassingen/beleid/update | Werk de eigenschap Applications. policies bij in Azure Active Directory. |
| micro soft. Directory/applicationTemplates/instantiëring | Maak een exemplaar van de galerie toepassingen vanuit toepassings sjablonen. |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. Directory/cloudProvisioning/allProperties/allTasks | Alle eigenschappen van Azure AD Cloud Provisioning Service lezen en configureren. |
| micro soft. Directory/federatedAuthentication/allProperties/allTasks | Beheer alle aspecten van Active Directory federatieve Services (ADFS) of van een Federatie provider van derden in azure AD. |
| micro soft. Directory/organisatie/dirSync/update | Werk de eigenschap Organization. dirSync bij in Azure Active Directory. |
| micro soft. Directory/passwordHashSync/allProperties/allTasks | Beheer alle aspecten van de wachtwoord-hash-synchronisatie (PHS) in azure AD. |
| micro soft. Directory/passThroughAuthentication/allProperties/allTasks | Beheer alle aspecten van Pass-Through-verificatie (PTA) in azure AD. |
| micro soft. Directory/seamlessSSO/allProperties/allTasks | Beheer alle aspecten van naadloze eenmalige aanmelding (SSO) in azure AD. |
| micro soft. Directory/servicePrincipals/Audience/update | Werk de eigenschap servicePrincipals. Audience bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/verificatie/bijwerken | Werk de eigenschap servicePrincipals. Authentication bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/Basic/update | Werk de basis eigenschappen van servicePrincipals bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/maken | Maak servicePrincipals in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/credentials/update | Werk de eigenschap servicePrincipals. credentials bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/verwijderen | Verwijder servicePrincipals in Azure Active Directory. |
| micro soft. map/servicePrincipals/eigen aren/bijwerken | Werk de eigenschap servicePrincipals. Owners bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/permissions/update | Werk de eigenschap servicePrincipals. permissions bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals. policies bij in Azure Active Directory. |
| micro soft. map/servicePrincipals/synchronizationJobs/beheren | Beheer alle aspecten van synchronisatie taken in azure AD. |
| micro soft. map/servicePrincipals/synchronizationSchema/beheren | Beheer alle aspecten van het synchronisatie schema in azure AD. |
| micro soft. map/servicePrincipals/synchronizationCredentials/beheren | Beheer alle aspecten van synchronisatie referenties in azure AD. |
| micro soft. Directory/servicePrincipals/tag/update | Werk de eigenschap servicePrincipals. tag bij in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |


### <a name="intune-service-administrator-permissions"></a>Machtigingen voor de intune-service beheerder

Kan alle aspecten van het intune-product beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen in Azure Active Directory bijwerken. |
| micro soft. map/Contacts/maken | Maak contact personen in Azure Active Directory. |
| micro soft. map/contact personen/verwijderen | Verwijder contact personen in Azure Active Directory. |
| micro soft. map/apparaten/basis/bijwerken | Basis eigenschappen bijwerken op apparaten in Azure Active Directory. |
| micro soft. Directory/apparaten/bitLockerRecoveryKeys/lezen | Lees de eigenschap devices. bitLockerRecoveryKeys in Azure Active Directory. |
| micro soft. map/apparaten/maken | Apparaten maken in Azure Active Directory. |
| micro soft. Directory/apparaten/verwijderen | Apparaten verwijderen in Azure Active Directory. |
| micro soft. Directory/apparaten/registeredOwners/update | Werk de eigenschap devices. registeredOwners bij in Azure Active Directory. |
| micro soft. Directory/apparaten/registeredUsers/update | Werk de eigenschap devices. registeredUsers bij in Azure Active Directory. |
| micro soft. Directory/groepen/appRoleAssignments/update | Werk de eigenschap groups. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen in Azure Active Directory bijwerken. |
| micro soft. map/groepen/maken | Groepen maken in Azure Active Directory. |
| micro soft. Directory/groepen/createAsOwner | Groepen maken in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. Directory/groepen/verwijderen | Groepen verwijderen in Azure Active Directory. |
| micro soft. Directory/groepen/hiddenMembers/lezen | Lees de eigenschap groups. hiddenMembers in Azure Active Directory. |
| micro soft. map/groepen/leden/bijwerken | Werk de eigenschap groups. members bij in Azure Active Directory. |
| micro soft. Directory/groepen/eigen aren/bijwerken | Werk de eigenschap groups. Owners bij in Azure Active Directory. |
| micro soft. Directory/groepen/herstellen | Groepen herstellen in Azure Active Directory. |
| micro soft. Directory/groepen/instellingen/bijwerken | Werk de eigenschap groups. settings bij in Azure Active Directory. |
| micro soft. Directory/Users/appRoleAssignments/update | Werk de eigenschap users. appRoleAssignments bij in Azure Active Directory. |
| micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers in Azure Active Directory bijwerken. |
| micro soft. map/gebruikers/Manager/Update | Werk de eigenschap users. Manager bij in Azure Active Directory. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. intune/toestemmingen/allTasks | Beheer alle aspecten van intune. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |

### <a name="kaizala-administrator-permissions"></a>Kaizala-beheerders machtigingen

Kan instellingen voor micro soft Kaizala beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees het Office 365-beheer centrum. |

### <a name="license-administrator-permissions"></a>Licentie beheerders machtigingen

Kan product licenties voor gebruikers en groepen beheren.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/gebruikers/assignLicense | Licenties op gebruikers beheren in Azure Active Directory. |
| micro soft. Directory/Users/usageLocation/update | Werk de eigenschap users. usageLocation bij in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |

### <a name="lync-service-administrator-permissions"></a>Beheerders machtigingen voor Lync-service

Kan alle aspecten van het product van Skype voor bedrijven beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. skypeForBusiness/cons/allTasks | Beheer alle aspecten van Skype voor bedrijven online. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. usageReports/de aflezingen/lezen    | Lees de gebruiks rapporten van Office 365. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |


### <a name="message-center-privacy-reader-permissions"></a>Machtigingen voor privacy-lezer in Message Center

Kan berichten centrum berichten, gegevens privacy-berichten, groepen, domeinen en abonnementen lezen.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. messageCenter/securityMessages/lezen | Lees securityMessages in micro soft. office365. messageCenter. |

### <a name="message-center-reader-permissions"></a>Machtigingen voor Message Center-lezer
Kan berichten en updates voor hun organisatie alleen in het Office 365-berichten centrum lezen. 

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |

### <a name="modern-commerce-administrator-permissions"></a>Moderne commerce-beheerders machtigingen
Kan commerciële aankopen voor een bedrijf, afdeling of team beheren. 

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. commerce. facturering/partners/lezen | Lees de partner-eigenschap van de O365-facturering. |
| micro soft. commerce. volumeLicenseServiceCenter/allTasks | Beheer alle aspecten van het Volume Licensing-service centrum. |
| micro soft. map/organisatie/basis/bijwerken | Basis eigenschappen van de organisatie in Azure Active Directory bijwerken. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |


### <a name="network-administrator-permissions"></a>Netwerk beheerders machtigingen
Kan netwerk locaties beheren en ontwerp inzichten van het Enter prise-netwerk controleren voor Microsoft 365-software als een service toepassing.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. office365. netwerk/prestaties/allProperties/lezen | Lees de pagina netwerk prestaties in het M365-beheer centrum.  |
| micro soft. office365. Network/locations/allProperties/allTasks | Lees en configureer de eigenschappen van netwerk locaties voor elke locatie. |

### <a name="office-apps-administrator-permissions"></a>Beheerders machtigingen voor Office-apps
Kan de Office-apps Cloud Services, waaronder beleid en instellingen beheer, beheren, en de mogelijkheid om de inhoud van ' what's New ' te selecteren, te selecteren en te publiceren op apparaten van eind gebruikers.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. usageReports/de aflezingen/lezen | Lees de gebruiks rapporten van Office 365. |
| micro soft. office365. userCommunication/cons/allTasks | Lees de zicht baarheid van nieuwe berichten en werk deze bij. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |

### <a name="partner-tier1-support-permissions"></a>Tier1-ondersteunings machtigingen voor partners

Niet gebruiken-niet bedoeld voor algemeen gebruik.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen in Azure Active Directory bijwerken. |
| micro soft. map/Contacts/maken | Maak contact personen in Azure Active Directory. |
| micro soft. map/contact personen/verwijderen | Verwijder contact personen in Azure Active Directory. |
| micro soft. map/groepen/maken | Groepen maken in Azure Active Directory. |
| micro soft. Directory/groepen/createAsOwner | Groepen maken in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. map/groepen/leden/bijwerken | Werk de eigenschap groups. members bij in Azure Active Directory. |
| micro soft. Directory/groepen/eigen aren/bijwerken | Werk de eigenschap groups. Owners bij in Azure Active Directory. |
| micro soft. Directory/Users/appRoleAssignments/update | Werk de eigenschap users. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/gebruikers/assignLicense | Licenties op gebruikers beheren in Azure Active Directory. |
| micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers in Azure Active Directory bijwerken. |
| micro soft. map/gebruikers/verwijderen | Verwijder gebruikers in Azure Active Directory. |
| micro soft. Directory/gebruikers/invalidateAllRefreshTokens | De tokens voor het vernieuwen van alle gebruikers in Azure Active Directory ongeldig te maken. |
| micro soft. map/gebruikers/Manager/Update | Werk de eigenschap users. Manager bij in Azure Active Directory. |
| micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden bijwerken voor alle gebruikers in Azure Active Directory. Raadpleeg de online documentatie voor meer informatie. |
| micro soft. map/gebruikers/herstellen | Verwijderde gebruikers herstellen in Azure Active Directory. |
| micro soft. map/gebruikers/userPrincipalName/update | Werk de eigenschap users. userPrincipalName bij in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="partner-tier2-support-permissions"></a>Tier2-ondersteunings machtigingen voor partners

Niet gebruiken-niet bedoeld voor algemeen gebruik.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen in Azure Active Directory bijwerken. |
| micro soft. map/Contacts/maken | Maak contact personen in Azure Active Directory. |
| micro soft. map/contact personen/verwijderen | Verwijder contact personen in Azure Active Directory. |
| micro soft. Directory/domeinen/allTasks | Maak en verwijder domeinen en lees de standaard eigenschappen in Azure Active Directory en werk deze bij. |
| micro soft. map/groepen/maken | Groepen maken in Azure Active Directory. |
| micro soft. Directory/groepen/verwijderen | Groepen verwijderen in Azure Active Directory. |
| micro soft. map/groepen/leden/bijwerken | Werk de eigenschap groups. members bij in Azure Active Directory. |
| micro soft. Directory/groepen/herstellen | Groepen herstellen in Azure Active Directory. |
| micro soft. map/organisatie/basis/bijwerken | Basis eigenschappen van de organisatie in Azure Active Directory bijwerken. |
| micro soft. Directory/Users/appRoleAssignments/update | Werk de eigenschap users. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/gebruikers/assignLicense | Licenties op gebruikers beheren in Azure Active Directory. |
| micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers in Azure Active Directory bijwerken. |
| micro soft. map/gebruikers/verwijderen | Verwijder gebruikers in Azure Active Directory. |
| micro soft. Directory/gebruikers/invalidateAllRefreshTokens | De tokens voor het vernieuwen van alle gebruikers in Azure Active Directory ongeldig te maken. |
| micro soft. map/gebruikers/Manager/Update | Werk de eigenschap users. Manager bij in Azure Active Directory. |
| micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden bijwerken voor alle gebruikers in Azure Active Directory. Raadpleeg de online documentatie voor meer informatie. |
| micro soft. map/gebruikers/herstellen | Verwijderde gebruikers herstellen in Azure Active Directory. |
| micro soft. map/gebruikers/userPrincipalName/update | Werk de eigenschap users. userPrincipalName bij in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="password-administrator-permissions"></a>Wachtwoord beheerders machtigingen

Kan wacht woorden voor niet-beheerders en wachtwoord beheerders opnieuw instellen.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden bijwerken voor alle gebruikers in Azure Active Directory. Raadpleeg de online documentatie voor meer informatie. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |

### <a name="power-bi-service-administrator-permissions"></a>Power BI-service beheerders machtigingen

Kan alle aspecten van het Power BI product beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. powerApps. powerBI/allTasks | Beheer alle aspecten van Power BI. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |


### <a name="power-platform-administrator-permissions"></a>Beheerders machtigingen voor Power platform

Kan alle aspecten van micro soft Dynamics 365, PowerApps en Microsoft Flow maken en beheren. 

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. dynamics365/-beleeningen/allTasks | Beheer alle aspecten van Dynamics 365. |
| micro soft. flow/allTasks | Beheer alle aspecten van Microsoft Flow. |
| micro soft. powerApps/allTasks | Beheer alle aspecten van PowerApps. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="printer-administrator-permissions"></a>Printer beheerders machtigingen

Kan alle aspecten van printers en printer connectors beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. Print/alallProperties/allTasks | Maak en verwijder printers en connectors en Lees alle eigenschappen in micro soft print en werk deze bij. |

### <a name="printer-technician-permissions"></a>Machtigingen voor printer technicus

Kan printers registreren en de registratie ervan opheffen en de printer status bijwerken.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. Print/connectors/allProperties/lezen | Alle eigenschappen van connectors in micro soft print lezen. |
| micro soft. Azure. Print/printers/allProperties/lezen | Lees alle eigenschappen van printers in micro soft afdrukken. |
| micro soft. Azure. Print/printers/Basic/update | Basis eigenschappen van printers bijwerken in micro soft afdrukken. |
| micro soft. Azure. afdrukken/printers/registreren | Printers registreren bij micro soft afdrukken. |
| micro soft. Azure. afdrukken/printers/registratie opheffen | Hef de registratie van printers op in micro soft afdrukken. |

### <a name="privileged-authentication-administrator-permissions"></a>Beheerders machtigingen voor bevoegde authenticatie

Voor het weer geven, instellen en opnieuw instellen van verificatie methode-informatie voor elke gebruiker (beheerder of niet-beheerder).

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/gebruikers/invalidateAllRefreshTokens | De tokens voor het vernieuwen van alle gebruikers in Azure Active Directory ongeldig te maken. |
| micro soft. Directory/Users/strongAuthentication/update | Geavanceerde verificatie-eigenschappen zoals MFA-referentie gegevens bijwerken. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. map/gebruikers/wacht woord/bijwerken | Update wacht woorden voor alle gebruikers in de Office 365-organisatie. Raadpleeg de online documentatie voor meer informatie. |

### <a name="privileged-role-administrator-permissions"></a>Beheerders machtigingen voor geprivilegieerde rollen

Kan roltoewijzingen in azure AD en alle aspecten van Privileged Identity Management beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. privilegedIdentityManagement/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. Aad. privilegedIdentityManagement en werk deze bij. |
| micro soft. Directory/servicePrincipals/appRoleAssignedTo/allTasks | Lees en configureer de eigenschap servicePrincipals. appRoleAssignedTo in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Lees en configureer de eigenschap servicePrincipals. oAuth2PermissionGrants in Azure Active Directory. |
| micro soft. Directory/administrativeUnits/allProperties/allTasks | Beheer eenheden maken en beheren (inclusief leden) |
| micro soft. Directory/roleAssignments/allProperties/allTasks | Roltoewijzingen maken en beheren. |
| micro soft. Directory/roleDefinitions/allProperties/allTasks | Roldefinities maken en beheren. |

### <a name="reports-reader-permissions"></a>Lees machtigingen voor rapporten

Kan aanmeldings-en controle rapporten lezen.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. office365. usageReports/de aflezingen/lezen | Lees de gebruiks rapporten van Office 365. |

### <a name="search-administrator-permissions"></a>Beheerders machtigingen zoeken

Kan alle aspecten van instellingen voor micro soft Search maken en beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. Search/alallProperties/allTasks | Maak en verwijder alle resources en Lees alle eigenschappen in micro soft. office365. Search en werk deze bij. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |

### <a name="search-editor-permissions"></a>Machtigingen voor de zoek editor

Kan de redactionele inhoud maken en beheren, zoals blad wijzers, Q en as, locaties, floorplan.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. office365. messageCenter/berichten/lezen | Lees berichten in micro soft. office365. messageCenter. |
| micro soft. office365. Search/content/allProperties/allTasks | Maak en verwijder inhoud en Lees alle eigenschappen in micro soft. office365. Search en werk deze bij. |

### <a name="security-administrator-permissions"></a>Beveiligings beheerders machtigingen

Kan beveiligings gegevens en-rapporten lezen en configuratie beheren in azure AD en Office 365.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Directory/toepassingen/beleid/update | Werk de eigenschap Applications. policies bij in Azure Active Directory. |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. Directory/apparaten/bitLockerRecoveryKeys/lezen | Lees de eigenschap devices. bitLockerRecoveryKeys in Azure Active Directory. |
| micro soft. Directory/policies/Basic/update | Basis eigenschappen van beleid in Azure Active Directory bijwerken. |
| micro soft. Directory/beleid/maken | Beleids regels maken in Azure Active Directory. |
| micro soft. Directory/beleid/verwijderen | Beleids regels verwijderen in Azure Active Directory. |
| micro soft. Directory/policies/eigen aren/bijwerken | Werk de eigenschap policies. Owners bij in Azure Active Directory. |
| micro soft. Directory/policies/tenantDefault/update | Werk de eigenschap policies. tenantDefault bij in Azure Active Directory. |
| micro soft. Directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals. policies bij in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. Aad. identityProtection/aldaar/Lees | Alle resources in micro soft. Aad. identityProtection lezen. |
| micro soft. Aad. identityProtection/conacties/update | Alle resources in micro soft. Aad. identityProtection bijwerken. |
| micro soft. Aad. privilegedIdentityManagement/aldaar/Lees | Alle resources in micro soft. Aad. privilegedIdentityManagement lezen. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. protectionCenter/de aflezingen/lezen | Lees alle aspecten van Office 365 Protection Center. |
| micro soft. office365. protectionCenter/cons/update | Werk alle resources in micro soft. office365. protectionCenter bij. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |

### <a name="security-operator-permissions"></a>Machtigingen voor beveiligings operator

Maakt en beheert beveiligings gebeurtenissen.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Aad. cloudAppSecurity/allTasks | Microsoft Cloud App Security lezen en configureren. |
| micro soft. Aad. identityProtection/aldaar/Lees | Alle resources in micro soft. Aad. identityProtection lezen. |
| micro soft. Aad. privilegedIdentityManagement/aldaar/Lees | Alle resources in micro soft. Aad. privilegedIdentityManagement lezen. |
| micro soft. Azure. advancedThreatProtection/Alvan de aflezingen/lezen | Lees en configureer Azure AD Advanced Threat Protection. |
| micro soft. intune/toestemmingen/allTasks | Beheer alle aspecten van intune. |
| micro soft. office365. securityComplianceCenter/cons/allTasks | Lees en configureer beveiliging & compliance Center. |
| micro soft. Windows. defenderAdvancedThreatProtection/geleenheden/lezen | Lees en configureer Windows Defender Advanced Threat Protection. |

### <a name="security-reader-permissions"></a>Machtigingen voor beveiligings lezer

Kan beveiligings gegevens en-rapporten in azure AD en Office 365 lezen.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/audit logs bevat/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op audit logs bevat in Azure Active Directory. |
| micro soft. Directory/apparaten/bitLockerRecoveryKeys/lezen | Lees de eigenschap devices. bitLockerRecoveryKeys in Azure Active Directory. |
| micro soft. Directory/policies/conditionalAccess/Basic/Read | Lees de eigenschap policies. conditionalAccess in Azure Active Directory. |
| micro soft. map/signInReports/allProperties/lezen | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| micro soft. Aad. identityProtection/aldaar/Lees | Alle resources in micro soft. Aad. identityProtection lezen. |
| micro soft. Aad. privilegedIdentityManagement/aldaar/Lees | Alle resources in micro soft. Aad. privilegedIdentityManagement lezen. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. protectionCenter/de aflezingen/lezen | Lees alle aspecten van Office 365 Protection Center. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |

### <a name="service-support-administrator-permissions"></a>Beheerders machtigingen voor service ondersteuning

Kan informatie over de service status lezen en ondersteunings tickets beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

### <a name="sharepoint-service-administrator-permissions"></a>Share point-service beheerders machtigingen

Kan alle aspecten van de share point-service beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. Directory/groepen/Unified/appRoleAssignments/update | Werk de eigenschap groups. Unified in Azure Active Directory. |
| micro soft. Directory/groepen/Unified/Basic/update | Basis eigenschappen van Office 365-groepen bijwerken. |
| micro soft. Directory/groepen/Unified/Create | Office 365-groepen maken. |
| micro soft. Directory/groepen/Unified/Delete | Verwijder Office 365-groepen. |
| micro soft. map/groepen/Unified/members/update | Update lidmaatschap van Office 365-groepen. |
| micro soft. Directory/groepen/Unified/Owners/update | Het eigendom van Office 365-groepen bijwerken. |
| micro soft. office365. netwerk/prestaties/allProperties/lezen | Lees de pagina netwerk prestaties in het M365-beheer centrum. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. share point/cons/allTasks | Maak en verwijder alle resources en lees de standaard eigenschappen in micro soft. office365. share point en werk deze bij. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. usageReports/de aflezingen/lezen    | Lees de gebruiks rapporten van Office 365. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |

### <a name="teams-communications-administrator-permissions"></a>Beheerders machtigingen voor teams communicatie

Kan functies voor bellen en vergaderingen binnen de micro soft teams-service beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. usageReports/de aflezingen/lezen | Lees de gebruiks rapporten van Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Privileges van de team communicatie technicus

Kan communicatie problemen binnen teams oplossen met behulp van geavanceerde hulp middelen.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |

### <a name="teams-communications-support-specialist-permissions"></a>Team communicatie support specialist-machtigingen

Kan communicatie problemen in teams oplossen met behulp van basis hulpprogramma's.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |

### <a name="teams-service-administrator-permissions"></a>Team service-beheerders machtigingen

Kan de micro soft teams-service beheren.

> [!NOTE]
> Deze rol heeft aanvullende machtigingen buiten Azure Active Directory. Zie beschrijving van rol hierboven voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. Directory/groepen/hiddenMembers/lezen | Lees de eigenschap groups. hiddenMembers in Azure Active Directory. |
| micro soft. Directory/groepen/Unified/appRoleAssignments/update | Werk de eigenschap groups. Unified in Azure Active Directory. |
| micro soft. Directory/groepen/Unified/Basic/update | Basis eigenschappen van Office 365-groepen bijwerken. |
| micro soft. Directory/groepen/Unified/Create | Office 365-groepen maken. |
| micro soft. Directory/groepen/Unified/Delete | Verwijder Office 365-groepen. |
| micro soft. map/groepen/Unified/members/update | Update lidmaatschap van Office 365-groepen. |
| micro soft. Directory/groepen/Unified/Owners/update | Het eigendom van Office 365-groepen bijwerken. |
| micro soft. office365. netwerk/prestaties/allProperties/lezen | Lees de pagina netwerk prestaties in het M365-beheer centrum. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |
| micro soft. office365. usageReports/de aflezingen/lezen | Lees de gebruiks rapporten van Office 365. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |

### <a name="user-administrator-permissions"></a>Gebruikers beheerders machtigingen
Kan alle aspecten van gebruikers en groepen beheren, met inbegrip van het opnieuw instellen van wacht woorden voor beperkte beheerders.

| **Acties** | **Beschrijving** |
| --- | --- |
| micro soft. map/appRoleAssignments/maken | Maak appRoleAssignments in Azure Active Directory. |
| micro soft. Directory/appRoleAssignments/verwijderen | Verwijder appRoleAssignments in Azure Active Directory. |
| micro soft. Directory/appRoleAssignments/update | AppRoleAssignments bijwerken in Azure Active Directory. |
| micro soft. map/Contacts/basis/update | Basis eigenschappen van contact personen in Azure Active Directory bijwerken. |
| micro soft. map/Contacts/maken | Maak contact personen in Azure Active Directory. |
| micro soft. map/contact personen/verwijderen | Verwijder contact personen in Azure Active Directory. |
| micro soft. Directory/groepen/appRoleAssignments/update | Werk de eigenschap groups. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/groepen/basis/bijwerken | Basis eigenschappen van groepen in Azure Active Directory bijwerken. |
| micro soft. map/groepen/maken | Groepen maken in Azure Active Directory. |
| micro soft. Directory/groepen/createAsOwner | Groepen maken in Azure Active Directory. De Maker wordt toegevoegd als de eerste eigenaar en het gemaakte object telt op het quotum van 250 gemaakte objecten van de maker. |
| micro soft. Directory/groepen/verwijderen | Groepen verwijderen in Azure Active Directory. |
| micro soft. Directory/groepen/hiddenMembers/lezen | Lees de eigenschap groups. hiddenMembers in Azure Active Directory. |
| micro soft. map/groepen/leden/bijwerken | Werk de eigenschap groups. members bij in Azure Active Directory. |
| micro soft. Directory/groepen/eigen aren/bijwerken | Werk de eigenschap groups. Owners bij in Azure Active Directory. |
| micro soft. Directory/groepen/herstellen | Groepen herstellen in Azure Active Directory. |
| micro soft. Directory/groepen/instellingen/bijwerken | Werk de eigenschap groups. settings bij in Azure Active Directory. |
| micro soft. Directory/Users/appRoleAssignments/update | Werk de eigenschap users. appRoleAssignments bij in Azure Active Directory. |
| micro soft. Directory/gebruikers/assignLicense | Licenties op gebruikers beheren in Azure Active Directory. |
| micro soft. map/gebruikers/basis/bijwerken | Basis eigenschappen van gebruikers in Azure Active Directory bijwerken. |
| micro soft. map/gebruikers/maken | Maak gebruikers in Azure Active Directory. |
| micro soft. map/gebruikers/verwijderen | Verwijder gebruikers in Azure Active Directory. |
| micro soft. Directory/gebruikers/invalidateAllRefreshTokens | De tokens voor het vernieuwen van alle gebruikers in Azure Active Directory ongeldig te maken. |
| micro soft. map/gebruikers/Manager/Update | Werk de eigenschap users. Manager bij in Azure Active Directory. |
| micro soft. map/gebruikers/wacht woord/bijwerken | Wacht woorden bijwerken voor alle gebruikers in Azure Active Directory. Raadpleeg de online documentatie voor meer informatie. |
| micro soft. map/gebruikers/herstellen | Verwijderde gebruikers herstellen in Azure Active Directory. |
| micro soft. map/gebruikers/userPrincipalName/update | Werk de eigenschap users. userPrincipalName bij in Azure Active Directory. |
| micro soft. Azure. serviceHealth/allTasks | Azure Service Health lezen en configureren. |
| micro soft. Azure. supportTickets/allTasks | Ondersteunings tickets voor Azure maken en beheren. |
| micro soft. office365. webportal/de beleen baarheid/basis/lezen | Lees de basis eigenschappen van alle resources in micro soft. office365. webportal. |
| micro soft. office365. serviceHealth/cons/allTasks | Lees en configureer Office 365 Service Health. |
| micro soft. office365. supportTickets/cons/allTasks | Office 365-ondersteunings tickets maken en beheren. |

## <a name="role-template-ids"></a>Id van de functie sjabloon

Rollen sjabloon-Id's worden voornamelijk gebruikt door de Microsoft Graph-API of Power shell-gebruikers.

Grafiek weergave naam | Weergave naam Azure Portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Toepassingsbeheerder | Toepassings beheerder | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Toepassingsontwikkelaar | Toepassings ontwikkelaar | CF1C38E5-3621-4004-A7CB-879624DCED7C
Verificatie beheerder | Verificatie beheerder | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps-beheerder | Azure DevOps-beheerder | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection beheerder | Azure Information Protection beheerder | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C IEF sleutelsetcursor | B2C IEF sleutelsetcursor | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF-beleids beheerder | B2C IEF-beleids beheerder | 3edaf663-341e-4475-9f94-5c398ef6c070
Factureringsbeheerder | Factureringsbeheerder | b0f54661-2d74-4c50-afa3-1ec803f12efe
Beheerder van de cloudtoepassing | Beheerder van de Cloud toepassing | 158c047a-c907-4556-b7ef-446551a6b5f7
Beheerder van Cloud apparaat | Beheerder van Cloud apparaat | 7698a772-787b-4ac8-901f-60d6b08affd2
Company Administratoristrain opr | Globale beheerder | 62e90394-69f5-4237-9190-012177145e10
Beheerder voor naleving | Beheerder voor naleving | 17315797-102d-40b4-93e0-432062caca18
Beheerder voor nalevingsgegevens | Beheerder van nalevings gegevens | e6d1a23a-da11-4be4-9570-befc86d067a7
Beheerder van voorwaardelijke toegang | Beheerder van voorwaardelijke toegang | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM Service-beheerder | Dynamics 365-beheerder | 44367163-eba1-44c3-98af-f5787879f96a
Toegangs fiatteur voor klanten-LockBox | Klanten-lockbox Access-fiatteur | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Beheerder van Desktop Analytics | Beheerder van Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Apparaatbeheerders | Beheerders van apparaten | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Apparaat toevoegen | Afgeschaft | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Apparaatbeheer | Afgeschaft | 2b499bcd-da44-4968-8aec-78e1674fa64d
Gebruikers van het apparaat | Afgeschaft | d405c6df-0af8-4e3b-95e4-4d06e542189e
Adreslijst lezers | Adreslijst lezers | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Directory-synchronisatie accounts | Niet weer gegeven omdat deze niet mag worden gebruikt | d29b2b05-8046-44ba-8758-1e26182fcf32
Schrijvers van mappen | Niet weer gegeven omdat deze niet mag worden gebruikt | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange service-beheerder | Exchange-beheerder | 29232cdf-9323-42fd-ade2-1d097af3e4de
Externe ID gebruikers stroom beheerder | Externe ID gebruikers stroom beheerder | 6e591065-9bad-43ed-90f3-e9424366d2f0
Externe ID gebruikers stroom kenmerk beheerder | Externe ID gebruikers stroom kenmerk beheerder | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Beheerder van externe ID-provider | Beheerder van externe ID-provider | be2f45a1-457d-42af-a067-6ec1fa63bc45
Algemene lezer | Algemene lezer | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Groeps beheerder | Groeps beheerder | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Gast uitnodiging | Gast uitnodiging | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Helpdesk beheerder | Helpdesk beheerder | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Hybride identiteits beheerder | Hybride identiteits beheerder | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Intune-servicebeheerder | Intune-beheerder | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala-beheerder | Kaizala-beheerder | 74ef975b-6605-40af-a5d2-b9539d836353
Licentie beheerder | Licentiebeheerder | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-service beheerder | Skype voor Bedrijven-beheerder | 75941009-915a-4869-abe7-691bff18279e
Berichten centrum-privacy-lezer | Berichten centrum-privacy-lezer | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Berichten centrum-lezer | Berichten centrum-lezer | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Moderne commerce-beheerder | Moderne commerce-beheerder | d24aef57-1500-4070-84db-2666f29cf966
Netwerk beheerder | Netwerk beheerder | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Office-Apps beheerder | Office-Apps beheerder | 2b745bdf-0803-4d80-aa65-822c4493daac
Ondersteuning voor partner Tier1 | Niet weer gegeven omdat deze niet mag worden gebruikt | 4ba39ca4-527c-499a-b93d-d9b492c50246
Ondersteuning voor partner Tier2 | Niet weer gegeven omdat deze niet mag worden gebruikt | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Wachtwoord beheerder | Wachtwoordbeheerder | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI-servicebeheerder | Power BI beheerder | a9ea8996-122f-4c74-9520-8edcd192826c
Power platform-beheerder | Power-platform beheerder | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Printer beheerder | Printer beheerder | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Printer technicus | Printer technicus | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Beheerder voor geprivilegieerde authenticatie | Beheerder voor geprivilegieerde authenticatie | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Beheerder van geprivilegieerde rol | Beheerder van geprivilegieerde rol | e8611ab8-c189-46e8-94e1-60213ab1f814
Rapport lezer | Rapport lezer | 4a5d8f65-41da-4de4-8968-e035b65339cf
Beheerder zoeken | Beheerder zoeken | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Zoek editor | Zoek editor | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Beveiligingsbeheer | Beveiligingsbeheerder | 194ae4cb-b126-40b2-bd5b-6091b380977d
Beveiligingsoperator | Beveiligings operator | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Beveiligingslezer | Beveiligingslezer | 5d6b6bb7-de71-4623-b4af-96380a352509
Ondersteunings beheerder voor service | Serviceondersteuningsbeheerder | f023fd81-a637-4b56-95fd-791ac0226033
Share point-service beheerder | SharePoint-beheerder | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams communicatie beheerder | Teams communicatie beheerder | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Mede werker communicatie technicus voor teams | Mede werker communicatie technicus voor teams | f70938a0-fc10-4177-9e90-2178f8765737
Specialist communicatie ondersteuning teams | Specialist communicatie ondersteuning teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams service-beheerder | Teams service-beheerder | 69091246-20e8-4a56-aa4d-066075b2a7a8
Gebruiker | Niet weer gegeven omdat deze niet kan worden gebruikt | a0b1b346-4d3e-4e8b-98f8-753987be4970
Beheerder van gebruikers account | Gebruikersbeheerder | fe930be7-5e62-47db-91af-98c3a49a38b1
Werkplek apparaat toevoegen | Afgeschaft | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Afgeschafte functies

De volgende rollen mogen niet worden gebruikt. Ze zijn afgeschaft en worden in de toekomst verwijderd uit Azure AD.

* Ad hoc-licentie beheerder
* Apparaat toevoegen
* Apparaatbeheer
* Gebruikers van het apparaat
* Maker van e-mail geverifieerde gebruiker
* Postvak beheerder
* Werkplek apparaat toevoegen

## <a name="roles-not-shown-in-the-portal"></a>Rollen die niet worden weer gegeven in de portal

Niet elke rol die wordt geretourneerd door Power shell of MS Graph API is zichtbaar in Azure Portal. In de volgende tabel worden deze verschillen ingedeeld.

API-naam | Azure Portal naam | Opmerkingen
-------- | ------------------- | -------------
Company Administratoristrain opr | Globale beheerder | [De naam is gewijzigd voor betere duidelijkheid](directory-assign-admin-roles.md#role-template-ids)
CRM Service-beheerder | Dynamics 365-beheerder | [Weerspiegelt huidige product huisstijl](directory-assign-admin-roles.md#role-template-ids)
Apparaat toevoegen | Afgeschaft | [Documentatie over afgeschafte functies](directory-assign-admin-roles.md#deprecated-roles)
Apparaatbeheer | Afgeschaft | [Documentatie over afgeschafte functies](directory-assign-admin-roles.md#deprecated-roles)
Gebruikers van het apparaat | Afgeschaft | [Documentatie over afgeschafte functies](directory-assign-admin-roles.md#deprecated-roles)
Directory-synchronisatie accounts | Niet weer gegeven omdat deze niet mag worden gebruikt | [Documentatie voor Directory Synchronization accounts](directory-assign-admin-roles.md#directory-synchronization-accounts)
Schrijvers van mappen | Niet weer gegeven omdat deze niet mag worden gebruikt | [Documentatie over Directory schrijvers](directory-assign-admin-roles.md#directory-writers)
Gastgebruiker | Niet weer gegeven omdat deze niet kan worden gebruikt  | NA
Lync-service beheerder | Skype voor Bedrijven-beheerder | [Weerspiegelt huidige product huisstijl](directory-assign-admin-roles.md#role-template-ids)
Ondersteuning voor partner Tier 1 | Niet weer gegeven omdat deze niet mag worden gebruikt | [Documentatie voor partner Tier1-ondersteuning](directory-assign-admin-roles.md#partner-tier1-support)
Ondersteuning voor partner Tier 2 | Niet weer gegeven omdat deze niet mag worden gebruikt | [Documentatie voor partner Tier2-ondersteuning](directory-assign-admin-roles.md#partner-tier2-support)
Beperkte gast gebruiker | Niet weer gegeven omdat deze niet kan worden gebruikt | NA
Gebruiker | Niet weer gegeven omdat deze niet kan worden gebruikt | NA
Werkplek apparaat toevoegen | Afgeschaft | [Documentatie over afgeschafte functies](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Volgende stappen

* Zie [toegang beheren met Azure roles (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) voor meer informatie over het toewijzen van een gebruiker als beheerder van een Azure-abonnement.
* Zie [inzicht in de verschillende rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md) voor meer informatie over het beheren van toegang tot resources in Microsoft Azure.
* Zie [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md) voor meer informatie over hoe Azure Active Directory aan uw Azure-abonnement wordt gekoppeld
