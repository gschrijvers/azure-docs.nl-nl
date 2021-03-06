---
title: On-premises apps integreren met Cloud App Security-Azure AD
description: Configureer een on-premises toepassing in Azure Active Directory om te werken met Microsoft Cloud App Security (MCAS). Gebruik de MCAS-App-beheer voor voorwaardelijke toegang om sessies in realtime te bewaken en te beheren op basis van beleid voor voorwaardelijke toegang. U kunt dit beleid Toep assen op on-premises toepassingen die gebruikmaken van toepassings proxy in Azure Active Directory (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275500"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Realtime toegangs bewaking voor toepassingen configureren met Microsoft Cloud App Security en Azure Active Directory
Configureer een on-premises toepassing in Azure Active Directory (Azure AD) voor het gebruik van Microsoft Cloud App Security (MCAS) voor realtime-bewaking. MCAS maakt gebruik van App-beheer voor voorwaardelijke toegang om sessies in realtime te bewaken en te beheren op basis van beleid voor voorwaardelijke toegang. U kunt dit beleid Toep assen op on-premises toepassingen die gebruikmaken van toepassings proxy in Azure Active Directory (Azure AD).

Hier volgen enkele voor beelden van de typen beleids regels die u kunt maken met MCAS:

- Het downloaden van gevoelige documenten op onbeheerde apparaten blok keren of beveiligen.
- Bewaak wanneer gebruikers met een hoog risico zich aanmelden bij toepassingen en vervolgens hun acties vanuit de sessie registreren. Met deze informatie kunt u het gebruikers gedrag analyseren om te bepalen hoe sessie beleid moet worden toegepast.
- Client certificaten of apparaatcompatibiliteit gebruiken om de toegang tot specifieke toepassingen van niet-beheerde apparaten te blok keren.
- Gebruikers sessies beperken voor niet-bedrijfs netwerken. U kunt beperkte toegang verlenen aan gebruikers die toegang hebben tot een toepassing buiten uw bedrijfs netwerk. Deze beperkte toegang kan bijvoorbeeld voor komen dat de gebruiker gevoelige documenten downloadt.

Zie [apps met Microsoft Cloud App Security app-beheer voor voorwaardelijke toegang beveiligen](/cloud-app-security/proxy-intro-aad)voor meer informatie.

## <a name="requirements"></a>Vereisten

Houders

- EMS E5-licentie of 
- Azure Active Directory Premium P1 en MCAS standalone.

On-premises toepassing:

- De on-premises toepassing moet gebruikmaken van Kerberos-beperkte delegering (KCD)

Toepassings proxy configureren:

- Configureer Azure AD voor het gebruik van toepassings proxy, met inbegrip van het voorbereiden van uw omgeving en het installeren van de connector voor de toepassings proxy. Zie [een on-premises toepassing voor externe toegang toevoegen via toepassings proxy in azure AD](application-proxy-add-on-premises-application.md)voor een zelf studie. 

## <a name="add-on-premises-application-to-azure-ad"></a>Een on-premises toepassing toevoegen aan Azure AD

Voeg een on-premises toepassing toe aan Azure AD. Zie [een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor een Snelstartgids. Wanneer u de toepassing toevoegt, moet u de volgende twee instellingen instellen op de Blade **on-premises toepassing toevoegen** :

- **Pre-authenticatie**: Voer **Azure Active Directory**in.
- **Url's vertalen in de hoofd tekst van de toepassing**: Kies **Ja**.

Deze twee instellingen zijn vereist voor de toepassing om met MCAS te werken.

## <a name="test-the-on-premises-application"></a>De on-premises toepassing testen

Nadat u uw toepassing aan Azure AD hebt toegevoegd, gebruikt u de stappen in [test de toepassing](application-proxy-add-on-premises-application.md#test-the-application) om een gebruiker toe te voegen voor testen en om de aanmelding te testen. 

## <a name="deploy-conditional-access-app-control"></a>App-beheer voor voorwaardelijke toegang implementeren

Als u uw toepassing wilt configureren met het toepassings beheer voor voorwaardelijke toegang, volgt u de instructies in [toepassings beheer voor voorwaardelijke toegang implementeren voor Azure AD-apps](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>App-beheer voor voorwaardelijke toegang testen

Als u de implementatie van Azure AD-toepassingen met toepassings beheer voor voorwaardelijke toegang wilt testen, volgt u de instructies in [de implementatie testen voor Azure AD-apps](/cloud-app-security/proxy-deployment-aad).





