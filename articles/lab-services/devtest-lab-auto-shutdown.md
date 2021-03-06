---
title: Beleid voor automatisch afsluiten beheren in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het instellen van het beleid voor automatisch afsluiten voor een lab, zodat virtuele machines worden afgesloten wanneer ze niet worden gebruikt.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7cdc9f9a4503c786065b6d514f61fe17eae4ce5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81270907"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Automatisch afsluiten configureren voor Lab-en Compute-virtuele machines in Azure DevTest Labs

In dit artikel wordt uitgelegd hoe u instellingen voor automatisch afsluiten configureert voor Lab-Vm's in DevTest Labs en Compute-Vm's. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Automatisch afsluiten configureren voor Lab-Vm's (DevTest Labs)
Met Azure DevTest Labs kunt u kosten besparen en verspiling in uw Labs minimaliseren door beleid (instellingen) voor elk lab te beheren. In dit artikel leest u hoe u het beleid voor automatisch afsluiten configureert voor een Lab-account en instellingen voor automatisch afsluiten configureert voor een lab in het lab-account. Zie [Lab-beleid in azure DevTest Labs definiëren](devtest-lab-set-lab-policy.md)voor meer informatie over het instellen van elk lab-beleid.  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Beleid voor automatisch afsluiten instellen voor een Lab
Als eigenaar van het lab kunt u een afsluit schema configureren voor alle virtuele machines in uw Lab. Op die manier kunt u kosten besparen van het uitvoeren van machines die niet worden gebruikt (niet-actief). U kunt een afsluit beleid afdwingen op al uw virtuele lab-Vm's, maar ook uw Lab-gebruikers de moeite hebben om een planning in te stellen voor de afzonderlijke machines. Met deze functie kunt u het beleid op uw test schema instellen, te beginnen met het beheer van volledig beheer aan uw Lab-gebruikers. Als eigenaar van het lab kunt u dit beleid configureren door de volgende stappen uit te voeren:

1. Op de start pagina van uw Lab selecteert u **configuratie en beleid**.
2. Selecteer **beleid voor automatisch afsluiten** in de sectie **schema's** van het menu links.
3. Selecteer een van de opties. In de volgende secties vindt u meer informatie over deze opties: het set-beleid is alleen van toepassing op nieuwe virtuele machines die zijn gemaakt in het lab en niet op de bestaande virtuele machines. 

    ![Opties voor automatisch afsluiten van beleid](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Instellingen voor automatisch afsluiten configureren
Het beleid voor automatisch afsluiten helpt Lab-afval te minimaliseren door de tijd op te geven waarop de Vm's van deze Lab worden afgesloten.

Voer de volgende stappen uit om de beleids regels voor een Lab weer te geven (en te wijzigen):

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab.   
4. Selecteer **configuratie en beleid**.

    ![Deel venster beleids instellingen](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Selecteer in het deel venster **configuratie en beleid** van de test **automatisch afsluiten** onder **planningen**.
   
    ![Automatisch afsluiten](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecteer aan om dit beleid **in te scha** kelen en **uit** te scha kelen.
7. Als u dit beleid inschakelt, geeft u de tijd (en tijd zone) op om alle virtuele machines in het huidige Lab af te sluiten.
8. Geef **Ja** of **Nee** op voor de optie om een melding te verzenden 30 minuten vóór de opgegeven tijd voor automatisch afsluiten. Als u **Ja**kiest, voert u het eind punt van de webhook-URL of het e-mail adres in waarmee u wilt dat de melding wordt gepost of verzonden. De gebruiker ontvangt een melding en krijgt de mogelijkheid om het afsluiten te vertragen. Zie de sectie [meldingen](#notifications) voor meer informatie. 
9. Selecteer **Opslaan**.

    Wanneer dit beleid is ingeschakeld, wordt standaard toegepast op alle virtuele machines in het huidige lab. Als u deze instelling van een specifieke virtuele machine wilt verwijderen, opent u het deel venster beheer van de virtuele machine en wijzigt u de instelling voor automatisch **Afsluiten** .
    
> [!NOTE]
> Als u de planning voor automatisch afsluiten voor uw Lab of een specifieke virtuele machine van het lab bijwerkt binnen 30 minuten van de huidige geplande tijd, wordt de bijgewerkte afsluit tijd van toepassing op de planning van de volgende dag. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Gebruiker stelt een schema in en kan zich afmelden
Als u uw Lab instelt op dit beleid, kunnen de Lab-gebruikers het lab-schema overschrijven of afmelden. Met deze optie krijgen gebruikers van het lab volledig beheer over het automatische afsluit schema van hun Vm's. Lab-gebruikers zien geen wijziging op de pagina planning voor automatisch afsluiten van de VM.

![Optie voor automatisch afsluiten van beleid-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Gebruiker stelt een schema in en kan niet afmelden
Als u uw Lab instelt op dit beleid, kunnen gebruikers met een lab het lab-schema overschrijven. Het beleid voor automatisch afsluiten kan echter niet worden afgemeld. Deze optie zorgt ervoor dat elke machine in uw Lab een schema voor automatisch afsluiten heeft. Gebruikers met een Lab kunnen het schema voor automatisch afsluiten van hun Vm's bijwerken en meldingen voor afsluiten instellen.

![Optie voor automatisch afsluiten van beleid-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>De gebruiker heeft geen controle over de planning die is ingesteld door de Lab-beheerder
Als u uw Lab instelt op dit beleid, kunnen Lab-gebruikers het lab-schema niet overschrijven of afmelden. Deze optie biedt Lab-beheerder de volledige controle over de planning voor elke machine in het lab. Gebruikers met een Lab kunnen alleen meldingen voor automatisch afsluiten instellen voor hun virtuele machines.

![Optie voor automatisch afsluiten van beleid-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Meldingen
Zodra automatisch afsluiten is ingesteld door de eigenaar van het lab, worden er 30 minuten meldingen verzonden naar de Lab-gebruikers als een van de Vm's wordt geactiveerd. Met deze optie kunnen gebruikers van het lab hun werk opslaan voordat ze worden afgesloten. De melding bevat ook koppelingen voor elke VM voor de volgende acties:

- Automatisch afsluiten voor deze tijd overs Laan
- Het automatisch afsluiten gedurende een uur of 2 uur uitstellen, zodat ze op de VM kunnen blijven werken.

Er wordt een melding verzonden via het geconfigureerde webhook-eind punt of een e-mail adres dat is opgegeven door de Lab-eigen aren in de instellingen voor automatisch afsluiten. Met webhooks kunt u integraties maken of instellen voor het abonneren op bepaalde gebeurtenissen. Wanneer een van deze gebeurtenissen wordt geactiveerd, stuurt DevTest Labs een HTTP POST-Payload naar de geconfigureerde URL van de webhook. Zie [een webhook of API Azure function maken](../azure-functions/functions-create-a-web-hook-or-api-function.md)voor meer informatie over webhooks. 

We raden u aan om webhooks te gebruiken, omdat deze uitgebreid worden ondersteund door verschillende apps (bijvoorbeeld toegestane vertraging, Azure Logic Apps enzovoort) en u uw eigen manier voor het verzenden van meldingen kunt implementeren. In dit artikel wordt bijvoorbeeld uitgelegd hoe u automatisch afsluiten van e-mail berichten kunt ontvangen met behulp van Azure Logic Apps. Eerst gaan we snel de basis stappen door lopen om automatisch afsluiten in uw Lab in te scha kelen.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Een logische app maken die e-mail meldingen ontvangt
[Azure Logic apps](../logic-apps/logic-apps-overview.md) biedt veel kant-en-klare connectors waarmee u eenvoudig een service kunt integreren met andere clients, zoals Office 365 en Twitter. Op hoog niveau kunnen de stappen voor het instellen van een logische app voor e-mail meldingen worden onderverdeeld in vier fasen: 

- Maak een logische app. 
- Configureer de ingebouwde sjabloon.
- Integreren met uw e-mailclient
- Haal de webhook-URL op.

### <a name="create-a-logic-app"></a>Een logische app maken
Als u aan de slag wilt gaan, maakt u een logische app in uw Azure-abonnement met behulp van de volgende stappen:

1. Selecteer **+ een resource maken** in het menu links, selecteer **integratie**en selecteer **logische app**. 

    ![Menu nieuwe logische app](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Voer de volgende stappen uit op de pagina **logische app-maken** : 
    1. Voer een **naam** in voor de logische app.
    2. Selecteer uw Azure- **abonnement**.
    3. Maak een nieuwe **resource groep** of selecteer een bestaande resource groep. 
    4. Selecteer een **locatie** voor de logische app. 

        ![Nieuwe logische app-instellingen](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Selecteer in de **meldingen** **Ga naar resource** in het bericht. 

    ![Ga naar resource](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecteer **Logic app Designer** onder **implementatie hulpprogramma's** categorie.

    ![HTTP-aanvraag/antwoord selecteren](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Selecteer op de pagina **HTTP-aanvraag-antwoord** de optie **deze sjabloon gebruiken**. 

    ![Selecteer deze sjabloon optie gebruiken](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Kopieer de volgende JSON in de sectie **JSON-schema van aanvraag tekst** : 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![JSON-schema van aanvraag tekst](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecteer **+ nieuwe stap** in de ontwerp functie en voer de volgende stappen uit:
    1. Zoeken naar **Office 365 Outlook-een E-mail verzenden**. 
    2. Selecteer **een E-mail verzenden** van **acties**. 
    
        ![E-mail optie verzenden](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecteer **Aanmelden** om u aan te melden bij uw e-mail account. 
    4. Selecteer **aan** veld en kies eigenaar.
    5. Selecteer **onderwerp**en voer een onderwerp in voor de e-mail melding. Bijvoorbeeld: "shutdown van de machine vmName voor Lab: labName."
    6. Selecteer **hoofd tekst**en definieer de inhoud van de hoofd tekst voor e-mail meldingen. Bijvoorbeeld: "vmName is gepland om in 15 minuten af te sluiten. Sla deze afsluiting over door te klikken op: URL. Afsluiten uitstellen gedurende een uur: delayUrl60. Afsluiten uitstellen gedurende 2 uur: delayUrl120. "

        ![JSON-schema van aanvraag tekst](./media/devtest-lab-auto-shutdown/email-options.png)
1. Selecteer **Opslaan** op de werkbalk. Nu kunt u de URL voor **http post**kopiëren. Selecteer de knop kopiëren om de URL naar het klem bord te kopiëren. 

    ![Webhook-URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Automatisch afsluiten configureren voor Compute-Vm's

1. Selecteer op de pagina **virtuele machine** **automatisch afsluiten** in het menu links in het gedeelte **bewerkingen** . 
2. Selecteer op de pagina **automatisch afsluiten** de optie **aan om** dit beleid in te scha kelen en **uit** te scha kelen.
3. Als u dit beleid inschakelt, geeft u de **tijd** (en **tijd zone**) op waarop de virtuele machine moet worden afgesloten.
4. Geef **Ja** of **Nee** op voor de optie om een melding te verzenden 30 minuten vóór de opgegeven tijd voor automatisch afsluiten. Als u **Ja**kiest, voert u het eind punt van de webhook-URL of het e-mail adres in waarmee u wilt dat de melding wordt gepost of verzonden. De gebruiker ontvangt een melding en krijgt de mogelijkheid om het afsluiten te vertragen. Zie de sectie [meldingen](#notifications) voor meer informatie. 
9. Selecteer **Opslaan**.

    ![Automatisch afsluiten configureren voor een compute-VM](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Activiteiten logboeken weer geven voor het automatisch afsluiten van updates
Wanneer u de instelling voor automatisch afsluiten bijwerkt, wordt de activiteit weer geven die is geregistreerd in het activiteiten logboek voor de virtuele machine. 

1. Ga in het [Azure Portal](https://portal.azure.com)naar de start pagina voor uw VM.
2. Selecteer **activiteiten logboek** in het menu links. 
3. **Resource verwijderen: mycomputevm** uit filters.
3. Bevestig dat u de bewerking voor het **toevoegen of wijzigen van schema's** in het activiteiten logboek ziet. Als u deze niet ziet, moet u even wachten en het activiteiten logboek vernieuwen.

    ![Vermelding in het activiteiten logboek](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Selecteer de bewerking **Schema's toevoegen of wijzigen** om de volgende informatie op de pagina **samen vatting** te bekijken:

    - Naam van de bewerking (schema's toevoegen of wijzigen)
    - De datum en tijd waarop de instelling voor automatisch afsluiten is bijgewerkt.
    - Het e-mail adres van de gebruiker die de instelling heeft bijgewerkt. 

        ![Overzicht van activiteiten logboek vermelding](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Ga naar het tabblad **wijzigings overzicht** op de pagina **schema's toevoegen of wijzigen** , maar u ziet de wijzigings geschiedenis voor de instelling. In het volgende voor beeld is de tijd voor afsluiten gewijzigd van 7 uur in 6 uur op 10 april 2020 om 15:18:47 EST. En de instelling is uitgeschakeld op 15:25:09 EST. 

    ![Activiteiten logboek-wijzigings overzicht](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Als u meer informatie over de bewerking wilt zien, gaat u naar het tabblad **JSON** op de pagina **schema's toevoegen of wijzigen** .

## <a name="next-steps"></a>Volgende stappen
Zie [Lab-beleid in azure DevTest Labs definiëren](devtest-lab-set-lab-policy.md)voor meer informatie over het instellen van alle beleids regels.

