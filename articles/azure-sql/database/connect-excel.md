---
title: Verbinden met Excel
description: Meer informatie over hoe u micro soft Excel verbindt met Azure SQL Database. Gegevens importeren in Excel voor rapportage en gegevens verkenning.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: 08bf78e6cc548ed717d6bc57946067a16ec13e2b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043448"
---
# <a name="connect-excel-to-azure-sql-database-and-create-a-report"></a>Excel verbinden met Azure SQL Database en een rapport maken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Verbinding maken met Excel om gegevens te Azure SQL Database en te importeren en tabellen en grafieken te maken op basis van waarden in de data base. In deze zelfstudie stelt u de verbinding tussen Excel en een databasetabel in, slaat u het bestand met de gegevens en de verbindingsinformatie voor Excel op en maakt u vervolgens een draaigrafiek uit de databasewaarden.

U hebt een SQL Database nodig om aan de slag te gaan. Als u er nog geen hebt, raadpleegt u [een Azure SQL database maken](single-database-create-quickstart.md) en een [IP-firewall op server niveau maken](firewall-create-server-level-portal-quickstart.md) om een SQL database te krijgen met voorbeeld gegevens die in een paar minuten actief zijn.

In dit artikel importeert u voorbeeld gegevens uit dat artikel in Excel, maar u kunt dezelfde stappen volgen met uw eigen gegevens.

U hebt ook een kopie van Excel nodig. Dit artikel gebruikt [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Excel verbinden met een SQL Database en gegevens laden

1. Als u Excel wilt verbinden met een SQL Database, opent u Excel en maakt u een nieuwe werkmap of opent u een bestaande Excel-werkmap.
2. Selecteer in de menu balk boven aan de pagina het tabblad **gegevens** , selecteer **gegevens ophalen**, selecteer uit Azure en selecteer vervolgens **uit Azure SQL database**.

   ![Selecteer een gegevens Bron: verbinden Excel met SQL Database.](./media/connect-excel/excel_data_source.png)

3. Typ in het dialoog venster **SQL Server Data Base** de **naam** van de server waarmee u verbinding wilt maken in de vorm <*servername* > **. database.Windows.net**. Bijvoorbeeld **msftestserver.database.Windows.net**. Voer desgewenst de naam van uw data base in. Selecteer **OK** om het venster referenties te openen.

   ![Het dialoog venster verbinding maken met de database server](./media/connect-excel/server-name.png)

4. Selecteer in het dialoog venster **SQL Server Data Base** de optie **Data Base** aan de linkerkant en voer vervolgens de **gebruikers naam** en het **wacht woord** in voor de server waarmee u verbinding wilt maken. Selecteer **verbinding maken** om de **Navigator**te openen.

   ![Typ de servernaam en aanmeldingsreferenties in](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > Afhankelijk van uw netwerk omgeving kunt u mogelijk geen verbinding maken of kunt u de verbinding verliezen als de server geen verkeer van uw client-IP-adres toestaat. Ga naar de [Azure-portal](https://portal.azure.com/), klik op SQL-servers, klik op uw server, klik op firewall onder instellingen en voeg uw client-IP-adres toe. Zie [Firewallinstellingen configureren](firewall-configure.md) voor meer informatie.

5. Selecteer in de **Navigator**de data base waarmee u wilt werken in de lijst, selecteer de tabellen of weer gaven waarmee u wilt werken (we hebben gekozen voor **vGetAllCategories**). Selecteer vervolgens **laden** om de gegevens uit uw Data Base naar uw Excel-werk blad te verplaatsen.

    ![Selecteer een database en tabel.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importeer de gegevens in Excel en maak een draaigrafiek.

Nu u de verbinding tot stand hebt gebracht, hebt u verschillende opties voor het laden van de gegevens. Met de volgende stappen kunt u bijvoorbeeld een draai grafiek maken op basis van de gegevens die in uw SQL Database zijn gevonden.

1. Volg de stappen in de vorige sectie, maar dit keer, in plaats van **laden**te selecteren, selecteert **u laden naar** in de vervolg keuzelijst **laden** .
2. Selecteer vervolgens hoe u deze gegevens in uw werkmap wilt weer geven. We hebben **draaigrafiek** gekozen. U kunt ook kiezen voor **Nieuw werkblad** of **Deze gegevens toevoegen aan een gegevensmodel**. Zie voor meer informatie over gegevensmodellen [Een gegevensmodel maken in Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B).

    ![De indeling kiezen voor gegevens in Excel](./media/connect-excel/import-data.png)

    Het werkblad heeft nu een lege draaitabel en grafiek.
3. Onder **PivotTable-velden**, selecteert u alle selectievakjes voor de velden die u wilt weergeven.

    ![Databaserapport configureren.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Als u andere Excel-werkmappen en-werk bladen wilt verbinden met de data base, selecteert u het tabblad **gegevens** en selecteert u **recente bronnen** om het dialoog venster **recente bronnen** te openen. Kies vervolgens de verbinding die u hebt gemaakt in de lijst en klik vervolgens op **openen**.
> ![Het dialoog venster recente bronnen](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Een permanente verbinding maken met het. ODC-bestand

Als u de verbindings gegevens permanent wilt opslaan, kunt u een. ODC-bestand maken en deze verbinding een selecteerbaar optie maken in het dialoog venster **bestaande verbindingen** .

1. Selecteer in de menu balk boven aan de pagina het tabblad **gegevens** en selecteer vervolgens **bestaande verbindingen** om het dialoog venster **bestaande verbindingen** te openen.
   1. Selecteer **Bladeren voor meer** om het dialoog venster **gegevens bron selecteren** te openen.
   2. Selecteer het bestand **+ NewSqlServerConnection. ODC** en selecteer vervolgens **openen** om de **wizard gegevens verbinding**te openen.

      ![Dialoog venster nieuwe verbinding](./media/connect-excel/new-connection.png)

2. Typ in de **wizard gegevens verbinding**de naam van uw server en uw SQL database referenties. Selecteer **Next**.
   1. Selecteer de data base die uw gegevens bevat uit de vervolg keuzelijst.
   2. Selecteer de tabel of weer gave waarin u geïnteresseerd bent. We hebben gekozen voor vGetAllCategories.
   3. Selecteer **Next**.

      ![Wizard gegevens verbinding](./media/connect-excel/data-connection-wizard.png)

3. Selecteer de locatie van het bestand, de **Bestands naam**en de **beschrijvende naam** in het volgende scherm van de wizard gegevens verbinding. U kunt er ook voor kiezen om het wacht woord op te slaan in het bestand, maar dit kan leiden tot ongewenste toegang. Selecteer **volt ooien** wanneer u klaar bent.

    ![Gegevens verbinding opslaan](./media/connect-excel/save-data-connection.png)

4. Selecteer hoe u uw gegevens wilt importeren. U hebt ervoor gekozen om een draai tabel te maken. U kunt ook de eigenschappen van de verbinding wijzigen door **Eigenschappen**te selecteren. Selecteer **OK** wanneer u klaar bent. Als u niet hebt gekozen om het wacht woord op te slaan bij het bestand, wordt u gevraagd om uw referenties in te voeren.

    ![Gegevens importeren](./media/connect-excel/import-data2.png)

5. Controleer of de nieuwe verbinding is opgeslagen door het tabblad **gegevens** uit te vouwen en **bestaande verbindingen**te selecteren.

    ![Bestaande verbinding](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Volgende stappen

* Leer hoe u [SQL Database koppelt met SQL Server Management Studio](connect-query-ssms.md) voor geavanceerd uitvoeren van query's en analyses.
* Meer informatie over de voordelen van [elastische groepen](elastic-pool-overview.md).
* Leer hoe u [ een webtoepassing maakt die verbinding maakt met SQL Database op de back-end](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
