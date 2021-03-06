---
title: Wat is voorwaardelijke toegang in Azure Active Directory?
description: Meer informatie over hoe voorwaardelijke toegang de kern is van het nieuwe besturings element op basis van identiteiten.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b044a4fd4e29bfe35abff7a4b36e5bae783328b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240910"
---
# <a name="what-is-conditional-access"></a>Wat is voorwaardelijke toegang?

De moderne beveiligings omtrek gaat nu verder dan het netwerk van een organisatie voor het toevoegen van de identiteit van de gebruiker en het apparaat. Organisaties kunnen deze identiteits signalen gebruiken als onderdeel van hun beslissingen voor toegangs beheer. 

Voorwaardelijke toegang is het hulp programma dat wordt gebruikt door Azure Active Directory om signalen samen te brengen, beslissingen te nemen en organisatie beleid af te dwingen. Voorwaardelijke toegang bevindt zich op het hart van het nieuwe op identiteits gerichte besturings vlak.

![Conceptuele voorwaardelijke signalen plus beslissing voor het afdwingen van afdwinging](./media/overview/conditional-access-signal-decision-enforcement.png)

Beleids regels voor voorwaardelijke toegang op hun eenvoudigste manier zijn als-then-instructies, als een gebruiker toegang wil tot een resource, dan moeten ze een actie volt ooien. Voor beeld: een salaris beheerder wil toegang tot de salaris toepassing en is vereist voor het uitvoeren van multi-factor Authentication voor toegang.

Beheerders worden geconfronteerd met twee primaire doel stellingen:

- Geef gebruikers de mogelijkheid overal en altijd productief te zijn
- De bedrijfs middelen beveiligen

Door gebruik te maken van beleids regels voor voorwaardelijke toegang kunt u de juiste toegangs beheer Toep assen als dat nodig is om uw organisatie veilig te houden en op de wijze van de gebruiker te blijven wanneer deze niet nodig is.

![Voortgangs proces stroom voor voorwaardelijke toegang](./media/overview/conditional-access-overview-how-it-works.png)

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de eerste-factor Authentication is voltooid. Voorwaardelijke toegang is niet bedoeld als de eerste verdedigings linie van een organisatie voor scenario's als denial-of-service-aanvallen, maar kan signalen van deze gebeurtenissen gebruiken om toegang te bepalen.

## <a name="common-signals"></a>Algemene signalen

Veelvoorkomende signalen die bij het maken van een beleids beslissing kunnen worden gebruikt om rekening mee te houden, zijn de volgende signalen:

- Gebruiker of groepslid maatschap
   - Beleids regels kunnen worden gericht op specifieke gebruikers en groepen die beheerders een nauw keurige controle over toegang geven.
- IP-locatie gegevens
   - Organisaties kunnen vertrouwde IP-adresbereiken maken die kunnen worden gebruikt bij het maken van beleids beslissingen. 
   - Beheerders kunnen het gehele IP-bereik van landen opgeven om verkeer van te blok keren of toe te staan.
- Apparaat
   - Gebruikers met apparaten van specifieke platformen of gemarkeerd met een specifieke status kunnen worden gebruikt bij het afdwingen van beleid voor voorwaardelijke toegang.
- Toepassing
   - Gebruikers die toegang proberen te krijgen tot specifieke toepassingen, kunnen verschillende beleids regels voor voorwaardelijke toegang activeren. 
- Real-time en berekende risico detectie
   - Hiermee wordt de integratie met Azure AD Identity Protection toegestaan beleids regels voor voorwaardelijke toegang om Risk ante aanmeldings gedrag te identificeren. Beleid kan gebruikers vervolgens dwingen om wachtwoord wijzigingen of multi-factor Authentication uit te voeren om het risico niveau te verminderen of om toegang te blok keren totdat een beheerder hand matige actie onderneemt.
- Microsoft Cloud App Security (MCAS)
   - Hiermee kunnen toegang en sessies van gebruikers toepassingen in realtime worden bewaakt en beheerd, waardoor de zicht baarheid en controle van de toegang tot en activiteiten in uw cloud omgeving worden verhoogd.

## <a name="common-decisions"></a>Algemene beslissingen

- Toegang blokkeren
   - Meest beperkend besluit
- Toegang verlenen
   - Er kunnen nog steeds een of meer van de volgende opties worden vereist:
      - Multi-factor Authentication vereisen
      - Vereisen dat het apparaat wordt gemarkeerd als compatibel
      - Hybride Azure AD-aangesloten apparaat vereisen
      - Goedgekeurde client-app vereisen
      - Beveiligings beleid voor apps vereisen (preview-versie)

## <a name="commonly-applied-policies"></a>Algemeen toegepast beleid

Veel organisaties hebben algemene toegangs problemen die het beleid voor voorwaardelijke toegang kunnen helpen bij het volgende:

- Multi-factor Authentication vereisen voor gebruikers met beheerders rollen
- Multi-factor Authentication vereisen voor Azure-beheer taken
- Aanmeldingen blok keren voor gebruikers die verouderde verificatie protocollen willen gebruiken
- Vertrouwde locaties voor registratie van Azure-Multi-Factor Authentication vereisen
- Toegang vanaf specifieke locaties blok keren of verlenen
- Risk ante aanmeldingen blok keren
- Door de organisatie beheerde apparaten vereisen voor specifieke toepassingen

## <a name="customer-case-studies"></a>Casestudy's van klanten

Ontdek hoe andere organisaties voorwaardelijke toegang van Azure AD gebruiken voor het definiëren en implementeren van geautomatiseerde beslissingen voor toegangs beheer. De volgende verhalen laten zien hoe deze klant aan deze behoeften voldoet.

* [Wipro verstuurt mobiele productiviteit met micro soft Cloud security tools om klant afspraken te verbeteren.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Het beleid voor voorwaardelijke toegang in azure AD heeft het bedrijf in staat gesteld om documenten, resources en toepassingen te delen met behulp van vertrouwde externe entiteiten---die hun eigen referenties kunnen gebruiken---met behoud van de controle over de eigen bedrijfs gegevens.
* [Aramex Delivery Limited-wereld wijd logistiek en transport bedrijf maakt met de Cloud verbonden Office met identiteits-en toegangs beheer oplossing](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Het garanderen van beveiligde toegang is met name lastig bij de externe werk nemers van Aramex. Het bedrijf past nu voorwaardelijke toegang toe om deze externe werk nemers toegang te geven tot hun SaaS-toepassingen van buiten het netwerk. De regel voor voorwaardelijke toegang bepaalt of Multi-Factor Authentication moet worden afgedwongen, zodat alleen de juiste personen de juiste toegang geven.

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Klanten met [Microsoft 365 Business licenties](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) hebben ook toegang tot functies voor voorwaardelijke toegang. 

## <a name="next-steps"></a>Volgende stappen

[Een beleid voor voorwaardelijke toegang op stuk bouwen](concept-conditional-access-policies.md)

Zie [de implementatie van voorwaardelijke toegang plannen in azure Active Directory](plan-conditional-access.md)voor meer informatie over het implementeren van voorwaardelijke toegang in uw omgeving.

[Meer informatie over identiteits beveiliging](../identity-protection/overview-v2.md)

[Meer informatie over Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)

[Meer informatie over Microsoft Intune](/intune/index)