---
title: Een Azure SQL Database een BACPAC-bestand exporteren (Azure Portal)
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Een Azure-SQL database exporteren naar een BACPAC-bestand met behulp van de Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 27e5bee041ca6be2f179a50732f58396b317e179
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045282"
---
# <a name="export-to-a-bacpac-file---azure-sql-database--sql-managed-instance"></a>Exporteren naar een BACPAC-bestand-Azure SQL Database & SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Wanneer u een Data Base voor archivering wilt exporteren of als u wilt overstappen op een ander platform, kunt u het database schema en de gegevens exporteren naar een [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -bestand. Een BACPAC-bestand is een ZIP-bestand met een uitbrei ding van BACPAC die de meta gegevens en gegevens uit de Data Base bevat. Een BACPAC-bestand kan worden opgeslagen in Azure Blob-opslag of in lokale opslag op een on-premises locatie en later opnieuw worden geïmporteerd in Azure SQL Database, Azure SQL Managed instance of een SQL Server on-premises installatie.

## <a name="considerations"></a>Overwegingen

- Als u een transactioneel exemplaar wilt exporteren, moet u ervoor zorgen dat er geen schrijf activiteit plaatsvindt tijdens het exporteren of dat u exporteert vanuit een [Transactioneel consistente kopie](database-copy.md) van uw data base.
- Als u exporteert naar Blob Storage, is de maximale grootte van een BACPAC-bestand 200 GB. Als u een groter BACPAC-bestand wilt archiveren, exporteert u het naar lokale opslag.
- Het exporteren van een BACPAC-bestand naar Azure Premium Storage met behulp van de methoden die in dit artikel worden beschreven, wordt niet ondersteund.
- Opslag achter een firewall wordt momenteel niet ondersteund.
- Als de export bewerking 20 uur overschrijdt, kan deze worden geannuleerd. Als u de prestaties tijdens het exporteren wilt verbeteren, kunt u het volgende doen:

  - Uw reken grootte tijdelijk verhogen.
  - Stopt alle Lees-en schrijf activiteiten tijdens het exporteren.
  - Gebruik een [geclusterde index](https://msdn.microsoft.com/library/ms190457.aspx) met niet-Null-waarden voor alle grote tabellen. Zonder geclusterde indexen kan een export mislukken als deze langer duurt dan 6-12 uur. Dit komt doordat de export service een tabel scan moet volt ooien om een volledige tabel te kunnen exporteren. Een goede manier om te bepalen of uw tabellen zijn geoptimaliseerd voor exporteren, is door **DBCC SHOW_STATISTICS** uit te voeren en ervoor te zorgen dat de *RANGE_HI_KEY* niet null is en dat de waarde van de tabel goed is gedistribueerd. Zie [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)voor meer informatie.

> [!NOTE]
> BACPACs zijn niet bedoeld om te worden gebruikt voor back-up-en herstel bewerkingen. Azure maakt automatisch back-ups voor elke gebruikers database. Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) en [SQL database back-ups](automated-backups-overview.md)voor meer informatie.

## <a name="azure-portal"></a>Azure Portal

Het exporteren van een BACPAC van een Data Base vanuit een door [Azure SQL beheerd exemplaar](../managed-instance/sql-managed-instance-paas-overview.md) met behulp van de Azure portal wordt momenteel niet ondersteund. Gebruik in plaats daarvan SQL Server Management Studio of SQLPackage.

> [!NOTE]
> Machines voor het verwerken van import/export-aanvragen die zijn ingediend via de Azure Portal of Power shell moeten het BACPAC-bestand opslaan, evenals tijdelijke bestanden die worden gegenereerd door het Framework van de Gegevenslaagtoepassing (DacFX). De benodigde schijf ruimte varieert aanzienlijk tussen data bases met dezelfde grootte en kan Maxi maal drie keer zoveel schijf ruimte vereisen als de data base. Machines met het verzoek om te importeren/exporteren hebben 450GB lokale schijf ruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout `There is not enough space on the disk` . In dit geval is de tijdelijke oplossing om sqlpackage. exe uit te voeren op een computer met voldoende lokale schijf ruimte. We raden u aan [SqlPackage](#sqlpackage-utility) -data bases die groter zijn dan 150 GB te importeren/exporteren om dit probleem te voor komen.

1. Als u een Data Base wilt exporteren met behulp van de [Azure Portal](https://portal.azure.com), opent u de pagina voor uw data base en klikt u op **exporteren** op de werk balk.

   ![Data base exporteren](./media/database-export/database-export1.png)

2. Geef de BACPAC-bestands naam op, selecteer een bestaand Azure Storage-account en een container voor de export en geef vervolgens de juiste referenties op voor toegang tot de bron database. Een SQL **Server-beheerders aanmelding** is hier ook nodig, zelfs als u de Azure-beheerder bent, omdat een Azure-beheerder niet gelijk is aan de beheerders machtigingen in Azure SQL database of Azure SQL Managed instance.

    ![Data base exporteren](./media/database-export/database-export2.png)

3. Klik op **OK**.

4. Als u de voortgang van de export bewerking wilt controleren, opent u de pagina voor de server die de Data Base bevat die wordt geëxporteerd. Onder **instellingen** en klikt u op **geschiedenis van importeren/exporteren**.

   ![Geschiedenis exporteren](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Hulp programma SQLPackage

Zie [para meters en eigenschappen exporteren](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)als u een SQL database wilt exporteren met behulp van het [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) -opdracht regel programma. Het hulp programma SQLPackage wordt geleverd met de nieuwste versies van [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) en [SQL Server Data tools voor Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), of u kunt de nieuwste versie van [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) rechtstreeks downloaden vanuit het micro soft Download centrum.

We raden u aan het SQLPackage-hulp programma te gebruiken voor schaal en prestaties in de meeste productie omgevingen. Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).

In dit voor beeld ziet u hoe u een Data Base exporteert met SqlPackage. exe met Active Directory universele verificatie:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

De nieuwste versies van SQL Server Management Studio bieden een wizard voor het exporteren van een Azure SQL Database of SQL Managed instance data base naar een BACPAC-bestand. Zie de [toepassing een gegevenslaag exporteren](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)voor meer informatie.

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) biedt momenteel geen ondersteuning voor het exporteren van een Data Base naar een BACPAC-bestand met behulp van Azure PowerShell. Als u een beheerd exemplaar wilt exporteren naar een BACPAC-bestand, gebruikt u SQL Server Management Studio of SQLPackage.

Gebruik de cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) om een aanvraag voor het exporteren van een Data Base naar de Azure SQL database-service te verzenden. Afhankelijk van de grootte van uw data base kan het enige tijd duren voordat de export bewerking is voltooid.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Als u de status van de export aanvraag wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Als deze onmiddellijk wordt uitgevoerd, wordt de status van de aanvraag meestal geretourneerd **: InProgress**. Wanneer u **status ziet: geslaagd** dat de export is voltooid.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Volgende stappen

- Zie [lange termijn retentie van back-ups](long-term-retention-overview.md)voor meer informatie over lange termijn retentie van één data base en gepoolde Data Base als een alternatief voor het exporteren van een Data Base voor archief doeleinden. U kunt SQL Agent-taken gebruiken voor het plannen van [back-ups met alleen-kopiëren](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) als alternatief voor lange termijn retentie van back-ups.
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [een BACPAC naar een SQL Server-Data Base importeren](https://msdn.microsoft.com/library/hh710052.aspx)voor meer informatie over het importeren van een BACPAC naar een SQL Server-Data Base.
- Zie [een gegevenslaag toepassing exporteren](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) voor meer informatie over het exporteren van een BACPAC vanuit een SQL Server-Data Base.
- Zie voor meer informatie over het gebruik van de Data Migration-service voor het migreren van een Data Base [migreren SQL Server om offline te Azure SQL database met behulp van DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Als u exporteert van SQL Server als een prelude naar Azure SQL Database, raadpleegt u [een SQL Server-Data Base migreren naar Azure SQL database](migrate-to-database-from-sql-server.md).
- Zie [Azure Storage Security Guide (Engelstalig](https://docs.microsoft.com/azure/storage/common/storage-security-guide)) voor meer informatie over het veilig beheren en delen van opslag sleutels en gedeelde toegangs handtekeningen.
