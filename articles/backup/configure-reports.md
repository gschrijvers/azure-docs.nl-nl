---
title: Azure Backup-rapporten configureren
description: Rapporten voor Azure Backup configureren en weer geven met behulp van Log Analytics en Azure-werkmappen
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e0c7418d7141a3b12f367f1b12ee740eaac64703
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797530"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

Een veelvoorkomende vereiste voor back-upbeheerders is het verkrijgen van inzichten over back-ups op basis van gegevens die een lange periode bevallen. Voor deze oplossing gelden de volgende cases:

- Het toewijzen en voors pellen van verbruikte Cloud opslag.
- Controle van back-ups en herstel bewerkingen.
- De belangrijkste trends op verschillende niveaus nauw keurig te identificeren.

Momenteel biedt Azure Backup een rapportage oplossing die gebruikmaakt van [Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) en [Azure-werkmappen](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview). Met deze resources krijgt u inzicht in uw back-ups over uw hele back-ups. In dit artikel wordt uitgelegd hoe u Azure Backup-rapporten configureert en weergeeft.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

- Back-uprapporten worden ondersteund voor virtuele Azure-machines, SQL in azure Vm's, SAP HANA in azure Vm's, Microsoft Azure Recovery Services (MARS) agent, Microsoft Azure Backup Server (MABS) en System Center Data Protection Manager (DPM). De gegevens voor back-up van Azure-bestands shares zijn momenteel niet zichtbaar in back-uprapporten.
- Voor DPM-workloads worden back-uprapporten ondersteund voor de DPM-versie 5.1.363.0 en hoger en de agent versie 2.0.9127.0 en hoger.
- Voor MABS-werk belastingen worden back-uprapporten ondersteund voor de MABS-versie 13.0.415.0 en hoger en de agent versie 2.0.9170.0 en hoger.
- Back-uprapporten kunnen worden weer gegeven voor alle back-upitems, kluizen, abonnementen en regio's, zolang hun gegevens worden verzonden naar een Log Analytics werk ruimte waartoe de gebruiker toegang heeft. Als u rapporten voor een set kluizen wilt weer geven, hoeft u alleen lezers toegang te hebben tot de Log Analytics werk ruimte waarnaar de kluizen hun gegevens verzenden. U hebt geen toegang tot de afzonderlijke kluizen.
- Als u een [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -gebruiker bent met gedelegeerde toegang tot de abonnementen van uw klanten, kunt u deze rapporten gebruiken met Azure Lighthouse om rapporten weer te geven over al uw tenants.
- Op dit moment kunnen gegevens worden weer gegeven in back-uprapporten over Maxi maal 100 Log Analytics werk ruimten (tussen tenants).
- Gegevens voor logboek back-uptaken worden momenteel niet weer gegeven in de rapporten.

## <a name="get-started"></a>Aan de slag

Volg deze stappen om de rapporten te gaan gebruiken.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. een Log Analytics-werk ruimte maken of een bestaande gebruiken

Stel een of meer Log Analytics-werk ruimten in om de gegevens van uw back-uprapport op te slaan. De locatie en het abonnement waar deze Log Analytics werk ruimte kunnen worden gemaakt, zijn onafhankelijk van de locatie en het abonnement waar uw kluizen bestaan.

Zie [een log Analytics-werk ruimte maken in de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)om een log Analytics-werk ruimte in te stellen.

De gegevens in een Log Analytics-werk ruimte worden standaard 30 dagen bewaard. Als u gegevens voor een langere periode wilt zien, wijzigt u de Bewaar periode van de Log Analytics werk ruimte. Als u de Bewaar periode wilt wijzigen, raadpleegt u [gebruik en kosten beheren met Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Configureer de diagnostische instellingen voor uw kluizen

Azure Resource Manager resources, zoals Recovery Services kluizen, registreert u informatie over geplande bewerkingen en door de gebruiker geactiveerde bewerkingen als diagnostische gegevens.

Selecteer in de sectie bewaking van uw Recovery Services kluis **Diagnostische instellingen** en geef het doel op voor de diagnostische gegevens van de Recovery Services kluis. Zie [Diagnostische instellingen voor Recovery Services kluizen gebruiken voor](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)meer informatie over het gebruik van diagnostische gebeurtenissen.

![Het deelvenster Diagnostische instellingen](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup biedt ook een ingebouwde Azure Policy definitie, waarmee de configuratie van diagnostische instellingen voor alle kluizen in een bepaald bereik wordt geautomatiseerd. Zie voor meer informatie over het gebruik van dit beleid de [instellingen voor diagnostische gegevens van de kluis configureren op schaal](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> Nadat u de diagnostische gegevens hebt geconfigureerd, kan het tot 24 uur duren voordat de eerste push bewerking is voltooid. Nadat de gegevens in de werk ruimte Log Analytics zijn gestart, worden de gegevens in de rapporten mogelijk niet onmiddellijk weer gegeven omdat de gegevens voor de huidige gedeeltelijke dag niet in de rapporten zijn opgenomen. Zie [conventies die worden gebruikt in back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)voor meer informatie. We raden u aan om de rapporten twee dagen na het configureren van uw kluizen te bekijken om gegevens naar Log Analytics te verzenden.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. rapporten weer geven in de Azure Portal

Nadat u uw kluizen hebt geconfigureerd om gegevens naar Log Analytics te verzenden, kunt u de back-uprapporten bekijken door naar het deel venster van de kluis te gaan en **back-uprapporten**te selecteren.

![Kluis dashboard](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecteer deze koppeling om de werkmap van het back-uprapport te openen.

> [!NOTE]
>
> - De aanvankelijke belasting van het rapport kan tot wel 1 minuut duren.
> - De Recovery Services kluis is slechts een ingangs punt voor back-uprapporten. Nadat de werkmap van het back-uprapport wordt geopend vanuit een kluis deel venster, selecteert u de juiste set Log Analytics-werk ruimten om gegevens te bekijken die zijn samengevoegd in al uw kluizen.

Het rapport bevat verschillende tabbladen:

- **Samen vatting**: gebruik dit tabblad om een overzicht te krijgen van uw back-ups op hoog niveau. U kunt een kort overzicht krijgen van het totale aantal back-upitems, het totale verbruikte Cloud opslag, het aantal beveiligde instanties en het percentage van de taak geslaagd per type werk belasting. Voor meer informatie over een specifiek type back-upartefact gaat u naar de desbetreffende tabbladen.

   ![Tabblad Samenvatting](./media/backup-azure-configure-backup-reports/summary.png)

- **Back-upitems**: gebruik dit tabblad om informatie en trends te bekijken over Cloud opslag die wordt gebruikt op een back-upitemniveau. Als u bijvoorbeeld SQL gebruikt in een back-up van een Azure-VM, ziet u de gebruikte Cloud opslag voor elke SQL database waarvan een back-up wordt gemaakt. U kunt er ook voor kiezen om gegevens te bekijken voor back-upitems van een bepaalde beveiligings status. Als u bijvoorbeeld de tegel **beveiliging gestopt** selecteert boven aan het tabblad, filtert u alle onderliggende objecten zodanig dat alleen gegevens worden weer gegeven voor back-upitems met de status gestopt voor beveiliging.

   ![Tabblad Back-upitems](./media/backup-azure-configure-backup-reports/backup-items.png)

- **Gebruik**: gebruik dit tabblad om de para meters voor de sleutel facturering voor uw back-ups weer te geven. De informatie die op dit tabblad wordt weer gegeven, bevindt zich op het niveau van een facturerings entiteit (beveiligde container). Als er bijvoorbeeld een back-up van een DPM-server wordt gemaakt naar Azure, kunt u de trend van beveiligde exemplaren en Cloud opslag die is verbruikt voor de DPM-server weer geven. Op dezelfde manier kunt u, als u SQL gebruikt in Azure Backup of SAP HANA in Azure Backup, op dit tabblad informatie over het gebruik weer geven op het niveau van de virtuele machine waarin deze data bases zich bevinden.

   ![Tabblad gebruik](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> Voor DPM-werk belastingen zien gebruikers mogelijk een enigszins verschil (van de volg orde van 20 MB per DPM-server) tussen de gebruiks waarden die worden weer gegeven in de rapporten, vergeleken met de waarde voor aggregatie gebruik zoals weer gegeven op het tabblad Overzicht van Recovery Services-kluis. Dit verschil wordt verwerkt door het feit dat elke DPM-server die voor back-up wordt geregistreerd, een gekoppelde meta gegevens bron heeft die niet wordt opgehaald als een artefact voor rapportage.

- **Taken**: gebruik dit tabblad om langlopende trends voor taken weer te geven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van een mislukte taak. U kunt deze informatie zowel op het niveau van een geaggregeerde als op een back-upitem weer geven. Selecteer een bepaald back-upitem in een raster om gedetailleerde informatie weer te geven over elke taak die is geactiveerd op het back-upitem in het geselecteerde tijds bereik.

   ![Tabblad taken](./media/backup-azure-configure-backup-reports/jobs.png)

- **Beleid**: gebruik dit tabblad om informatie weer te geven over al uw actieve beleids regels, zoals het aantal gekoppelde items en de totale Cloud opslag die wordt gebruikt door items waarvan een back-up is gemaakt onder een bepaald beleid. Selecteer een bepaald beleid om informatie weer te geven over elk van de bijbehorende back-upitems.

   ![Tabblad beleid](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exporteren naar Excel

Selecteer de pijl-omlaag in de rechter bovenhoek van een wille keurige widget, zoals een tabel of grafiek, om de inhoud van die widget te exporteren als een Excel-werk blad, waarbij bestaande filters worden toegepast. Als u meer rijen van een tabel wilt exporteren naar Excel, kunt u het aantal rijen dat wordt weer gegeven op de pagina verhogen met behulp van de vervolg keuze pijl voor **rijen per pagina** boven aan elk raster.

## <a name="pin-to-dashboard"></a>Vastmaken aan dashboard

Selecteer de knop vastmaken aan de bovenkant van elke widget om de widget aan uw Azure Portal dash board vast te maken. Deze functie helpt u bij het maken van aangepaste Dash boards die zijn aangepast om de belangrijkste informatie die u nodig hebt, weer te geven.

## <a name="cross-tenant-reports"></a>Rapporten over meerdere tenants

Als u [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) gebruikt met gedelegeerde toegang tot abonnementen in meerdere Tenant omgevingen, kunt u het standaard abonnements filter gebruiken. Selecteer de filter knop in de rechter bovenhoek van de Azure Portal om alle abonnementen te kiezen waarvoor u gegevens wilt weer geven. Als u dit doet, kunt u Log Analytics werk ruimten in uw tenants selecteren om multi tenant rapporten weer te geven.

## <a name="conventions-used-in-backup-reports"></a>Conventies die in back-uprapporten worden gebruikt

- Filters werken van links naar rechts en van boven naar beneden op elk tabblad. Dat wil zeggen dat alle filters alleen van toepassing zijn op alle widgets die aan de rechter kant van het filter of onder dat filter worden geplaatst.
- Als u een gekleurde tegel selecteert, worden de objecten onder de tegel gefilterd op records die betrekking hebben op de waarde van die tegel. Als u bijvoorbeeld de tegel **beveiliging gestopt** selecteert op het tabblad **Back-upitems** , worden de rasters en de onderstaande grafieken gefilterd om gegevens weer te geven voor back-upitems met de status gestopt voor beveiliging.
- Tegels die niet zijn gekleurd, worden niet geklikt.
- De gegevens voor de huidige gedeeltelijke dag worden niet weer gegeven in de rapporten. Dus als de geselecteerde waarde van het **tijds bereik** de **laatste 7 dagen**is, worden in het rapport records weer gegeven voor de afgelopen zeven voltooide dagen. De huidige dag is niet opgenomen.
- Het rapport bevat details van taken (naast logboek taken) die zijn *geactiveerd* in het geselecteerde tijds bereik.
- De waarden die worden weer gegeven voor **Cloud opslag** en **beveiligde instanties** bevinden zich aan het *einde* van het geselecteerde tijds bereik.
- De back-upitems die in de rapporten worden weer gegeven, zijn de items die aan het *einde* van het geselecteerde tijds bereik bestaan. Back-upitems die in het midden van het geselecteerde tijds bereik zijn verwijderd, worden niet weer gegeven. Dezelfde Conventie geldt ook voor back-upbeleid.

## <a name="query-load-times"></a>Laad tijden query

De widgets in het back-uprapport worden aangedreven door Kusto-query's, die worden uitgevoerd op de Log Analytics-werk ruimten van de gebruiker. Deze query's omvatten doorgaans de verwerking van grote hoeveel heden gegevens, met meerdere samen voegingen om uitgebreid inzicht mogelijk te maken. Als gevolg hiervan worden de widgets mogelijk niet direct geladen wanneer de gebruiker rapporten weergeeft over een grote back-up. Deze tabel bevat een ruwe schatting van de tijd die het laden van verschillende widgets kan uitvoeren, op basis van het aantal back-upitems en het tijds bereik waarvoor het rapport wordt weer gegeven.

| **Aantal gegevens bronnen**                         | **Tijd horizon** | **Geschatte laad tijden**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 maand          | Tegels: 5-10 seconden <br> Rasters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapport niveau: 5-10 seconden|
| ~ 5 K                       | 3 maanden          | Tegels: 5-10 seconden <br> Rasters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapport niveau: 5-10 seconden|
| ~ 10 K                       | 3 maanden          | Tegels: 15-20 seconden <br> Rasters: 15-20 seconden <br> Grafieken: 1-2 minuten <br> Filters op rapport niveau: 25-30 seconden|
| ~ 15 K                       | 1 maand          | Tegels: 15-20 seconden <br> Rasters: 15-20 seconden <br> Grafieken: 50-60 seconden <br> Filters op rapport niveau: 20-25 seconden|
| ~ 15 K                       | 3 maanden          | Tegels: 20-30 seconden <br> Rasters: 20-30 seconden <br> Grafieken: 2-3 minuten <br> Filters op rapport niveau: 50-60 seconden |

## <a name="what-happened-to-the-power-bi-reports"></a> Wat is er gebeurd met de Power BI-rapporten? 

- De eerdere Power BI sjabloon-app voor rapportage, waarvan de bron gegevens afkomstig zijn uit een Azure-opslag account, bevindt zich op een afschaffing-pad. U kunt het beste de diagnostische gegevens van de kluis naar Log Analytics verzenden om rapporten weer te geven.

- Daarnaast bevindt het [v1-schema](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema) voor het verzenden van diagnostische gegevens naar een opslag account of een La-werk ruimte zich ook op een afschaffing pad. Dit betekent dat als u aangepaste query's of Automatiseringen hebt geschreven op basis van het v1-schema, u wordt geadviseerd om deze query's bij te werken om het momenteel ondersteunde v2-schema te gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over bewaking en rapportage met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
