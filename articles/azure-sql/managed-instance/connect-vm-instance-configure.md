---
title: Azure VM-connectiviteit configureren
titleSuffix: Azure SQL Managed Instance
description: Maak verbinding met een met Azure SQL beheerd exemplaar via SQL Server Management Studio van een virtuele machine van Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: b94993a67fadad2c31c8787deab49e62dd457512
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044372"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-managed-instance"></a>Snelstartgids: Azure VM configureren om verbinding te maken met een door Azure SQL beheerd exemplaar
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In deze Quick start ziet u hoe u een virtuele machine van Azure kunt configureren om verbinding te maken met een met Azure SQL beheerd exemplaar met behulp van SQL Server Management Studio (SSMS). 


Zie [een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md) voor een Snelstartgids waarin wordt getoond hoe u verbinding maakt vanaf een on-premises client computer met behulp van een punt-tot-site-verbinding

## <a name="prerequisites"></a>Vereisten

In deze Quick Start worden de resources gebruikt die zijn gemaakt in [een SQL Managed instance maken](instance-create-quickstart.md) als uitgangs punt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Een nieuw subnet VNet maken

Met de volgende stappen maakt u een nieuw subnet in het SQL Managed instance VNet, zodat een virtuele Azure-machine verbinding kan maken met het door SQL beheerde exemplaar. Het subnet voor het beheerde exemplaar van SQL is toegewezen aan SQL Managed instances. U kunt geen andere resources, zoals virtuele machines van Azure, maken in dat subnet.

1. Open de resource groep voor het SQL-beheerde exemplaar dat u hebt gemaakt in de Snelstartgids [een door SQL beheerd exemplaar maken](instance-create-quickstart.md) . Selecteer het virtuele netwerk voor uw SQL Managed instance.

   ![SQL Managed instance-resources](./media/connect-vm-instance-configure/resources.png)

2. Selecteer **subnetten** en selecteer vervolgens **+ subnet** om een nieuw subnet te maken.

   ![Subnetten van SQL Managed instance](./media/connect-vm-instance-configure/subnets.png)

3. Vul het formulier in met behulp van de informatie in deze tabel:

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ---------------- | ----------------- | ----------- |
   | **Naam** | Een geldige naam|Zie [naamgevings regels en beperkingen](/azure/architecture/best-practices/resource-naming)voor geldige namen.|
   | **Adresbereik (CIDR-blok)** | Een geldig bereik | De standaard waarde is geschikt voor deze Quick Start.|
   | **Netwerk beveiligings groep** | Geen | De standaard waarde is geschikt voor deze Quick Start.|
   | **Routetabel** | Geen | De standaard waarde is geschikt voor deze Quick Start.|
   | **Service-eindpunten** | 0 geselecteerd | De standaard waarde is geschikt voor deze Quick Start.|
   | **Delegatie van subnet** | Geen | De standaard waarde is geschikt voor deze Quick Start.|

   ![Nieuw subnet met SQL-beheerde instantie voor client-VM](./media/connect-vm-instance-configure/new-subnet.png)

4. Selecteer **OK** om dit extra subnet in het SQL Managed instance VNet te maken.

## <a name="create-vm-in-new-subnet"></a>Virtuele machine maken in nieuw subnet 

De volgende stappen laten zien hoe u een virtuele machine in het nieuwe subnet maakt om verbinding te maken met het door SQL beheerde exemplaar.

## <a name="prepare-the-azure-virtual-machine"></a>De virtuele machine van Azure voorbereiden

Omdat het beheerde exemplaar van SQL wordt geplaatst in uw privé-Virtual Network, moet u een Azure VM maken met een geïnstalleerd SQL Client-hulp programma, zoals SQL Server Management Studio of Azure Data Studio. Met dit hulp programma kunt u verbinding maken met het SQL Managed instance en query's uitvoeren. In deze snelstart wordt SQL Server Management Studio gebruikt.

De eenvoudigste manier om een virtuele client machine met alle benodigde hulpprogram ma's te maken, is door de Azure Resource Manager-sjablonen te gebruiken.

1. Zorg ervoor dat u bent aangemeld bij de Azure Portal op een ander browser tabblad. Selecteer vervolgens de volgende knop om een virtuele client machine te maken en Installeer SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Vul het formulier in met behulp van de informatie in de volgende tabel:

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ---------------- | ----------------- | ----------- |
   | **Abonnement** | Een geldig abonnement | Moet een abonnement zijn waarin u gemachtigd bent om nieuwe resources te maken. |
   | **Resource groep** |De resource groep die u hebt opgegeven in de Snelstartgids voor het [maken van SQL Managed instance](instance-create-quickstart.md) .|Deze resource groep moet het VNet zijn.|
   | **Locatie** | De locatie voor de resource groep | Deze waarde wordt ingevuld op basis van de geselecteerde resource groep. |
   | **Naam van de virtuele machine**  | Een geldige naam | Zie [naamgevings regels en beperkingen](/azure/architecture/best-practices/resource-naming)voor geldige namen.|
   |**Gebruikers naam beheerder**|Een geldige gebruikers naam|Zie [naamgevings regels en beperkingen](/azure/architecture/best-practices/resource-naming)voor geldige namen. Maak geen gebruik van 'serverbeheerder' aangezien dit een rol is die op serverniveau is gereserveerd.<br>U gebruikt deze gebruikers naam telkens wanneer u [verbinding maakt met de virtuele machine](#connect-to-virtual-machine).|
   |**Wachtwoord**|Een geldig wachtwoord|Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>U gebruikt dit wacht woord telkens wanneer u [verbinding maakt met de virtuele machine](#connect-to-virtual-machine).|
   | **Grootte van virtuele machine** | Een geldige grootte | De standaard instelling in deze sjabloon van **Standard_B2s** is voldoende voor deze Quick Start. |
   | **Locatie**|[resourceGroup (). locatie].| Wijzig deze waarde niet. |
   | **Virtual Network naam**|Het virtuele netwerk waarin u het door SQL beheerde exemplaar hebt gemaakt.|
   | **Subnetnaam**|De naam van het subnet dat u in de vorige procedure hebt gemaakt| Kies niet het subnet waarin u het door SQL beheerde exemplaar hebt gemaakt.|
   | **Locatie van artefacten** | [implementatie (). Properties. templateLink. URI] | Wijzig deze waarde niet. |
   | **SAS-token voor locatie van artefacten** | leeg laten | Wijzig deze waarde niet. |

   ![client-vm maken](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Als u de voorgestelde VNet-naam en het standaard subnet bij het [maken van uw SQL Managed instance](instance-create-quickstart.md)hebt gebruikt, hoeft u de laatste twee para meters niet te wijzigen. Anders moet u deze waarden wijzigen in de waarden die u hebt ingevoerd bij het instellen van de netwerk omgeving.

3. Schakel het selectie vakje **Ik ga akkoord met de bovenstaande voor waarden in** .
4. Selecteer **aanschaffen** om de virtuele Azure-machine in uw netwerk te implementeren.
5. Selecteer het pictogram voor **Meldingen** om de status van de implementatie te bekijken.

> [!IMPORTANT]
> Ga niet verder tot ongeveer 15 minuten nadat de virtuele machine is gemaakt om tijd te geven voor het installeren van SQL Server Management Studio.

## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

In de volgende stappen wordt uitgelegd hoe u verbinding maakt met uw nieuwe virtuele machine via verbinding met een extern bureaublad.

1. Nadat de implementatie is voltooid, gaat u naar de virtuele machine-resource.

    ![VM](./media/connect-vm-instance-configure/vm.png)  

2. Selecteer **Verbinding maken**.

   Een Remote Desktop Protocol bestand (RDP-bestand) wordt weer gegeven met het open bare IP-adres en het poort nummer voor de virtuele machine.

   ![RDP-formulier](./media/connect-vm-instance-configure/rdp.png)  

3. Selecteer **RDP-bestand downloaden**.

   > [!NOTE]
   > U kunt SSH ook gebruiken om verbinding te maken met uw virtuele machine.

4. Sluit het formulier **verbinding maken met virtuele machine** .
5. Open het gedownloade RDP-bestand om verbinding met de VM te maken.
6. Selecteer **verbinding maken**wanneer u hierom wordt gevraagd. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) uit de Mac App Store.

7. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine en klik vervolgens op **OK**.

8. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Kies **Ja** of **door gaan** om door te gaan met de verbinding.

U bent verbonden met uw virtuele machine in het dash board van Serverbeheer.

## <a name="connect-to-sql-managed-instance"></a>Verbinding maken met beheerd SQL-exemplaar 

1. Open SQL Server Management Studio (SSMS) op de virtuele machine.

   Het duurt enkele minuten voordat de configuratie is voltooid, omdat dit de eerste keer is dat SSMS is gestart.
2. Voer in het dialoog venster **verbinding maken met server** de volledig gekwalificeerde **HOSTNAAM** voor uw SQL Managed instance in het vak **Server naam** in. Selecteer **SQL Server verificatie**, geef uw gebruikers naam en wacht woord op en selecteer vervolgens **verbinding maken**.

    ![ssms verbinden](./media/connect-vm-instance-configure/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, kunt u uw systeem en gebruikersdatabases bekijken in het knooppunt Databases. Daarnaast kunt u diverse objecten bekijken in de knooppunten Beveiliging, Server-objecten, Replicatie, Beheer, SQL Server Agent en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md)voor een Snelstartgids waarin wordt getoond hoe u verbinding maakt met een on-premises client computer met behulp van een punt-naar-site-verbinding.
- Zie [Connect your applications to SQL Managed instance](connect-application-instance.md)(Engelstalig) voor een overzicht van de verbindings opties voor toepassingen.
- Als u een bestaande SQL Server-Data Base van on-premises wilt herstellen naar een SQL-beheerd exemplaar, kunt u de [Azure database Migration service (DMS) voor migratie](../../dms/tutorial-sql-server-to-managed-instance.md) of de [T-SQL-opdracht](restore-sample-database-quickstart.md) voor terugzetten gebruiken om een back-upbestand van een Data Base te herstellen.
