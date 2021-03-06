---
title: 'Het hulp programma voor voorwaardelijke toegang What If: Azure Active Directory'
description: Meer informatie over de impact van uw beleid voor voorwaardelijke toegang in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc87b434664ba12cefeb233972e749f631d8a2b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77620688"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Problemen oplossen met behulp van het hulp programma What If in voorwaardelijke toegang

[Voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) is een functie van Azure Active Directory (Azure AD) waarmee u kunt bepalen hoe geautoriseerde gebruikers toegang hebben tot uw Cloud-apps. Hoe weet u wat u kunt verwachten van het beleid voor voorwaardelijke toegang in uw omgeving? Als u deze vraag wilt beantwoorden, kunt u het **hulp programma voor voorwaardelijke toegang What if**gebruiken.

In dit artikel wordt uitgelegd hoe u dit hulp programma kunt gebruiken om uw beleid voor voorwaardelijke toegang te testen.

## <a name="what-it-is"></a>Wat is het?

Met het **What if beleid voor voorwaardelijke toegang** kunt u inzicht krijgen in de impact van uw beleid voor voorwaardelijke toegang in uw omgeving. In plaats van uw beleid te testen door meerdere aanmeldingen hand matig uit te voeren, kunt u met dit hulp programma een gesimuleerde aanmelding van een gebruiker evalueren. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport. In het rapport worden niet alleen de toegepaste beleids regels voor voorwaardelijke toegang, maar ook het [klassieke beleid](policy-migration.md#classic-policies) weer geven als ze bestaan.    

Het hulp programma **What if** biedt een manier om snel te bepalen welke beleids regels van toepassing zijn op een specifieke gebruiker. U kunt de informatie gebruiken, bijvoorbeeld als u een probleem moet oplossen.    

## <a name="how-it-works"></a>Hoe werkt het?

In het **What if hulp programma voor voorwaardelijke toegang**moet u eerst de instellingen configureren van het aanmeldings scenario dat u wilt simuleren. Deze instellingen omvatten:

- De gebruiker die u wilt testen 
- De Cloud-apps waartoe de gebruiker toegang probeert te krijgen
- De omstandigheden waaronder toegang tot de Configuring Cloud-apps wordt uitgevoerd
     
Als volgende stap kunt u een simulatie uitvoering initiëren waarmee uw instellingen worden geëvalueerd. Alleen beleids regels die zijn ingeschakeld, maken deel uit van een evaluatie-uitvoering.

Wanneer de evaluatie is voltooid, genereert het hulp programma een rapport van het betreffende beleid.

## <a name="running-the-tool"></a>Het hulp programma uitvoeren

U kunt het hulp programma **What if** vinden op de pagina **[voorwaardelijke toegang-beleids regels](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** in de Azure Portal.

Als u het hulp programma wilt starten, klikt u in de werk balk boven aan de lijst met beleids regels op **What if**.

![What If](./media/what-if-tool/01.png)

Voordat u een evaluatie kunt uitvoeren, moet u de instellingen configureren.

## <a name="settings"></a>Instellingen

In deze sectie vindt u informatie over de instellingen van simulatie uitvoering.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Gebruiker

U kunt slechts één gebruiker selecteren. Dit is het enige vereiste veld.

### <a name="cloud-apps"></a>Cloud-apps

De standaard waarde voor deze instelling is **alle Cloud-apps**. De standaard instelling voert een evaluatie uit van alle beschik bare beleids regels in uw omgeving. U kunt het bereik beperken tot beleids regels die van invloed zijn op specifieke Cloud-apps.

### <a name="ip-address"></a>IP-adres

Het IP-adres is een enkel IPv4-adres voor het nabootsen van de [locatie voorwaarde](location-condition.md). Het adres vertegenwoordigt het Internet adres van het apparaat dat door uw gebruiker wordt gebruikt om u aan te melden. U kunt het IP-adres van een apparaat controleren door bijvoorbeeld te navigeren naar [Wat is mijn IP-adres](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Apparaatplatformen

Deze instelling imiteert de [voor waarde apparaat platforms](concept-conditional-access-conditions.md#device-platforms) en vertegenwoordigt het equivalent van **alle platformen (met inbegrip van niet-ondersteunde)**. 

### <a name="client-apps"></a>Client-apps

Deze instelling imiteert de [voor waarde voor client-apps](concept-conditional-access-conditions.md#client-apps-preview).
Deze instelling zorgt er standaard voor dat een evaluatie van alle beleids regels met **browser** -of **mobiele apps en desktop-clients** afzonderlijk of beide is geselecteerd. Er wordt ook een beleid gedetecteerd waarmee **Exchange ActiveSync (EAS)** wordt afgedwongen. U kunt deze instelling beperken door het volgende te selecteren:

- **Browser** voor het evalueren van alle beleids regels waarvoor ten minste een **browser** is geselecteerd. 
- **Mobiele apps en desktop-clients** om alle beleids regels te evalueren waarvoor ten minste **Mobile apps en desktop-clients** zijn geselecteerd. 

### <a name="sign-in-risk"></a>Aanmeldings risico

Deze instelling imiteert de [aanmeldings risico voorwaarde](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Evaluatie 

U start een evaluatie door te klikken op **What if**. Het evaluatie resultaat geeft u een rapport dat bestaat uit: 

![What If](./media/what-if-tool/03.png)

- Een indicator die in uw omgeving een klassiek beleid bevat
- Beleids regels die van toepassing zijn op uw gebruiker
- Beleids regels die niet van toepassing zijn op uw gebruiker

Als er een [klassiek beleid](policy-migration.md#classic-policies) bestaat voor de geselecteerde Cloud-apps, wordt er een indicator aan u gepresenteerd. Als u op de indicator klikt, wordt u omgeleid naar de klassieke beleids pagina. Op de pagina klassiek beleid kunt u een klassiek beleid migreren of alleen uitschakelen. U kunt teruggaan naar het evaluatie resultaat door deze pagina te sluiten.

In de lijst met beleids regels die van toepassing zijn op de geselecteerde gebruiker, kunt u ook een lijst met [besturings elementen voor granting](concept-conditional-access-grant.md) en [sessie besturings elementen](concept-conditional-access-session.md) zoeken die aan uw gebruiker moeten voldoen.

In de lijst met beleids regels die niet van toepassing zijn op uw gebruiker, kunt u ook zoeken naar de redenen waarom dit beleid niet van toepassing is. Voor elke vermelde beleids regel vertegenwoordigt de reden de eerste voor waarde waaraan niet is voldaan. Een mogelijke reden voor een beleid dat niet wordt toegepast, is een uitgeschakeld beleid omdat deze niet verder worden geëvalueerd.   

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md).
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md). 
- Als u klassiek beleid wilt migreren, raadpleegt u [klassiek beleid migreren in de Azure Portal](policy-migration.md)  
