---
title: Gegevens van Windows-beveiligings gebeurtenissen verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Windows-beveiligings gebeurtenis gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124972"
---
# <a name="connect-windows-security-events"></a>Verbinding maken met Windows-beveiligingsgebeurtenissen 

Met de connector voor beveiligings gebeurtenissen kunt u alle beveiligings gebeurtenissen van uw Windows-systemen (servers en werk stations, fysieke en virtuele) streamen naar uw Azure-Sentinel-werk ruimte. Zo kunt u Windows-beveiligings gebeurtenissen in uw Dash boards weer geven, om ze te gebruiken bij het maken van aangepaste waarschuwingen en deze te vertrouwen om uw onderzoek te verbeteren, zodat u meer inzicht hebt in het netwerk van uw organisatie en uw beveiligings mogelijkheden kunt uitbreiden. U kunt selecteren welke gebeurtenissen uit de volgende sets worden gestreamd:<a name="event-sets"></a>

- **Alle gebeurtenissen** : alle Windows-beveiligings-en AppLocker-gebeurtenissen.
- **Algemeen** : een standaardset gebeurtenissen voor controle doeleinden. Deze set bevat een volledige controle spoor voor gebruikers. Het bevat bijvoorbeeld zowel aanmeldings gebeurtenissen voor gebruikers als gebruikers afmeldingen (gebeurtenis-Id's 4624, 4634). Er zijn ook controle acties zoals wijzigingen in de beveiligings groep, Kerberos-bewerkingen van de Key-domein controller en andere typen gebeurtenissen in overeenstemming met aanbevolen procedures.

    De **algemene** gebeurtenissenset kan enkele typen gebeurtenissen bevatten die niet zo vaak worden gebruikt.  Dit komt omdat het belangrijkste punt van de **gemeen schappelijke** set het verminderen van het volume van de gebeurtenissen naar een meer beheersbaar niveau, terwijl de volledige controle-Trail-mogelijkheid behouden blijft.

- **Mini maal** : een kleine set gebeurtenissen die mogelijke dreigingen kunnen aanduiden. Deze set bevat geen volledige audittrail. Dit geldt alleen voor gebeurtenissen die kunnen wijzen op een geslaagde schending en andere belang rijke gebeurtenissen met een zeer lage frequentie van de gebeurtenis. Het bevat bijvoorbeeld geslaagde en mislukte gebruikers aanmeldingen (gebeurtenis-Id's 4624, 4625), maar bevat geen informatie over afmeldingen (4634), wat belang rijk is voor controle en niet relevant is voor detectie van inbreuken en relatief hoog volume. Het meren deel van het gegevens volume van deze set bestaat uit aanmeldings gebeurtenissen en gebeurtenissen voor het maken van processen (gebeurtenis-ID 4688).

- **Geen** : er zijn geen beveiligings-of AppLocker-gebeurtenissen. (Deze instelling wordt gebruikt om de connector uit te scha kelen.)

    De volgende lijst bevat een volledige uitsplitsing van de gebeurtenis-Id's van de beveiligings-en app-kluis voor elke set:

    | Gebeurtenissen instellen | Verzamelde gebeurtenis-Id's |
    | --- | --- |
    | **Minimaal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Algemeen** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> De verzameling van beveiligings gebeurtenissen in de context van één werk ruimte kan worden geconfigureerd vanuit Azure Security Center of Azure Sentinel, maar niet voor beide. Als u de Azure-Sentinel onboardt in een werk ruimte die al wordt uitgevoerd Azure Security Center, en is ingesteld op het verzamelen van beveiligings gebeurtenissen, hebt u twee opties:
> - Zorg ervoor dat de verzameling beveiligings gebeurtenissen in Azure Security Center ongewijzigd blijft. U kunt deze gebeurtenissen opvragen en analyseren in azure Sentinel en in Azure Security Center. U kunt de verbindings status van de connector echter niet controleren of de configuratie wijzigen in azure Sentinel. Als dit belang rijk voor u is, kunt u de tweede optie overwegen.
>
> - [Schakel de verzameling van beveiligings gebeurtenissen](../security-center/security-center-enable-data-collection.md) in azure Security Center uit en voeg alleen de beveiligings gebeurtenissen connector toe aan Azure Sentinel. Net als bij de eerste optie kunt u gebeurtenissen in zowel Azure Sentinel als Azure Security Center opvragen en analyseren, maar u kunt nu de verbindings status van de connector bewaken of de configuratie wijzigen in-en alleen in-azure-Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>De Windows-beveiligings gebeurtenissen connector instellen

Uw Windows-beveiligings gebeurtenissen in azure-Sentinel verzamelen:

1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**. Klik in de lijst met connectors op **beveiligings gebeurtenissen**en klik vervolgens op de knop **pagina connector openen** op de rechter benedenhoek. Volg vervolgens de instructies op het scherm op het tabblad **instructies** , zoals wordt beschreven in de rest van deze sectie.

1. Controleer of u de juiste machtigingen hebt, zoals wordt beschreven onder **vereisten**.

1. Down load en installeer de [log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md) (ook wel bekend als micro soft monitoring agent of MMA) op de computers waarvoor u beveiligings gebeurtenissen in azure Sentinel wilt streamen.

    Voor Azure Virtual Machines:
    
    1. Klik op **agent installeren op de virtuele machine van Azure Windows**en klik vervolgens op de koppeling die hieronder wordt weer gegeven.
    1. Voor elke virtuele machine die u wilt verbinden, klikt u op de naam in de lijst die aan de rechter kant wordt weer gegeven en klikt u vervolgens op **verbinding maken**.

    Voor niet-Azure Windows-machines (fysiek, Virtual on-premises of virtueel in een andere Cloud):

    1. Klik op **agent installeren op niet-Azure Windows-computers**en klik vervolgens op de koppeling die hieronder wordt weer gegeven.
    1. Klik op de juiste download koppelingen die aan de rechter kant onder **Windows-computers**worden weer gegeven.
    1. Installeer de agent op de Windows-systemen van uw keuze met behulp van het gedownloade uitvoer bare bestand en configureer dit met de **werk ruimte-id en de sleutels** die worden weer gegeven onder de hierboven vermelde Download koppelingen.

    > [!NOTE]
    >
    > Als u wilt toestaan dat Windows-systemen zonder de benodigde Internet verbinding nog steeds gebeurtenissen streamen naar Azure Sentinel, downloadt en installeert u de **OMS-gateway** op een afzonderlijke computer, met behulp van de koppeling rechtsonder, om als proxy te fungeren.  U moet de Log Analytics agent nog steeds installeren op elk Windows-systeem waarvan u de gebeurtenissen wilt verzamelen.
    >
    > Zie de documentatie over [ **log Analytics gateway** ](../azure-monitor/platform/gateway.md)voor meer informatie over dit scenario.

    Raadpleeg de documentatie van de [ **log Analytics-agent** ](../azure-monitor/platform/agent-windows.md)voor aanvullende installatie opties en meer informatie.

1. Selecteer welke gebeurtenissenset ([alles, algemeen of mini maal](#event-sets)) u wilt streamen.

1. Klik op **Update**.

1. Als u het relevante schema in Log Analytics voor Windows-beveiligings gebeurtenissen wilt `SecurityEvent` gebruiken, typt u in het query venster.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan ongeveer 20 minuten duren totdat uw logboeken in Log Analytics worden weer gegeven. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Windows-beveiligings gebeurtenissen verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.

