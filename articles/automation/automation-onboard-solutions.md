---
title: Azure Automation Updatebeheer inschakelen vanuit runbook
description: Dit artikel beschrijft hoe u Updatebeheer kunt inschakelen vanuit een runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 54adcb6c6da62406dda5df738dde06dee5d6b1e0
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167380"
---
# <a name="enable-update-management-from-a-runbook"></a>Updatebeheer inschakelen vanuit een runbook

Dit artikel beschrijft hoe u een runbook kunt gebruiken om de [Updatebeheer](automation-update-management.md)-functie voor VM's in uw omgeving in te schakelen. Om Azure VM's in te schakelen op schaal, moet u een bestaande VM inschakelen met behulp van Updatebeheer. 

> [!NOTE]
> Bij het inschakelen van Updatebeheer worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Selecteer de Log Analytics-werkruimte en klik op **Inschakelen**. Er wordt een blauwe banner weergegeven terwijl Updatebeheer wordt ingeschakeld. 

    ![Updatebeheer inschakelen](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Te beheren Azure-VM selecteren

Als Updatebeheer is ingeschakeld, kunt u een Azure-VM toevoegen om updates te ontvangen.

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Selecteer **Azure-VM's toevoegen** om uw VM toe te voegen.

3. Kies de VM in de lijst en klik op **Inschakelen** om de VM in te stellen voor updates. 

   ![Updatebeheer inschakelen voor VM](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Als u probeert een andere functie in te schakelen voordat de installatie van Updatebeheer is voltooid, ontvangt u dit bericht: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Modules installeren en bijwerken

U moet een update uitvoeren naar de nieuwste Azure-modules en de module [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) importeren om Updatebeheer te kunnen inschakelen voor uw VM's.

1. Selecteer **Modules** onder **Gedeelde resources** in uw Automation-account. 
2. Selecteer **Azure-modules bijwerken** om de Azure-modules bij te werken naar de nieuwste versie. 
3. Klik op **Ja** om alle bestaande Azure-modules bij te werken naar de nieuwste versie.

    ![Modules bijwerken](media/automation-onboard-solutions/update-modules.png)

4. Ga terug naar **Modules** onder **Gedeelde resources**. 
5. Selecteer **Bladeren in galerie** om de modulegalerie te openen. 
6. Zoek naar `Az.OperationalInsights` en importeer deze module in uw Automation-account.

    ![De module OperationalInsights importeren](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Een runbook importeren om Updatebeheer in te schakelen

1. Selecteer **Runbooks** onder **Procesautomatisering** in uw Automation-account.
2. Selecteer **Bladeren in galerie**.
3. Zoeken naar `update and change tracking`.
4. Selecteer het runbook en klik op **Importeren** op de pagina Bron weergeven. 
5. Klik op **OK** om het runbook in het Automation-account te importeren.

   ![Runbook importeren voor installatie](media/automation-onboard-solutions/import-from-gallery.png)

6. Klik op de pagina Runbook op **Bewerken** en selecteer **Publiceren**. 
7. Klik in het deelvenster Runbook publiceren op **Ja** om het runbook te publiceren.

## <a name="start-the-runbook"></a>Het runbook starten

U moet Updatebeheer op een Azure-VM hebben ingeschakeld om dit runbook te starten. Het vereist een bestaande VM en resourcegroep met de functie ingeschakeld als parameters.

1. Open het runbook **Enable-MultipleSolution**.

   ![Runbook voor meerdere oplossingen](media/automation-onboard-solutions/runbook-overview.png)

2. Klik op de startknop en voer in de volgende velden parameterwaarden in:

   * **VMNAME**: de naam van een bestaande VM om aan Updatebeheer toe te voegen. Laat dit veld leeg als uy alle VM's in de resourcegroep wilt toevoegen.
   * **VMRESOURCEGROUP**: de naam van de resourcegroep voor de in te schakelen VM's.
   * **SUBSCRIPTIONID**: de abonnement-id van de nieuwe in te schakelen VM. Laat dit veld leeg om het abonnement van de werkruimte te gebruiken. Wanneer u een andere abonnement-id gebruikt, voegt u het Uitvoeren als-account voor uw Automation-account toe als inzender voor het abonnement.
   * **ALREADYONBOARDEDVM**: de naam van de VM waarop updates al handmatig zijn ingeschakeld.
   * **ALREADYONBOARDEDVMRESOURCEGROUP**: de naam van de resourcegroep waartoe de VM behoort.
   * **SOLUTIONTYPE**: voer **Updates** in.

   ![Parameters voor het runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

3. Selecteer **OK** om de runbooktaak te starten.
4. Bewaak de voortgang en eventuele fouten op de pagina van de runbooktaak.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Planningen beheren in Azure Automation](shared-resources/schedules.md) om een runbook te plannen.
* Zie [Updates en patches voor uw Azure-VM's beheren](automation-tutorial-update-management.md) voor het gebruiken van Updatebeheer voor VM's.
* Zie [Problemen met Updatebeheer oplossen](troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
* Zie [Problemen met Windows-updateagent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van problemen met de Windows Update-agent.
* Zie [problemen met Linux Update-agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met de Linux Update-agent.