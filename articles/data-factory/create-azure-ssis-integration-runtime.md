---
title: Een Azure SSIS Integration runtime in Azure Data Factory maken
description: Meer informatie over het maken van een Azure SSIS Integration runtime in Azure Data Factory zodat u SSIS-pakketten kunt implementeren en uitvoeren in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: af6e6346616255cfb153d59df7031c6d0d4710da
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117892"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Een Azure SSIS Integration runtime in Azure Data Factory maken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel worden de stappen beschreven voor het inrichten van een Azure-SQL Server Integration Services (SSIS) Integration runtime (IR) in Azure Data Factory. Een Azure-SSIS IR ondersteunt:

- Het uitvoeren van pakketten die zijn geïmplementeerd in de SSIS-catalogus (SSISDB) op Azure SQL Database of SQL Managed instance (project implementatie model).
- Het uitvoeren van pakketten die zijn geïmplementeerd in bestands systemen, bestands shares of Azure Files (pakket implementatie model). 

Nadat een Azure-SSIS IR is ingericht, kunt u de vertrouwde hulpprogram ma's gebruiken om uw pakketten in azure te implementeren en uit te voeren. Tot deze hulpprogram ma's behoren SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) en opdracht regel Programma's, zoals `dtinstall` , `dtutil` , en `dtexec` .

In de zelf studie voor het [inrichten van Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) ziet u hoe u een Azure-SSIS IR maakt via de Azure portal of de Data Factory-app. In de zelf studie ziet u ook hoe u optioneel een SQL Database of een door SQL beheerd exemplaar kunt gebruiken om SSISDB te hosten. In dit artikel wordt de zelf studie uitgebreid en wordt beschreven hoe u deze optionele taken kunt uitvoeren:

- Gebruik SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of een door SQL beheerd exemplaar met een persoonlijk eind punt om SSISDB te hosten. Als vereiste moet u de machtigingen en instellingen van het virtuele netwerk voor uw Azure-SSIS IR configureren om lid te worden van een virtueel netwerk.

- Gebruik Azure Active Directory-verificatie (Azure AD) met de beheerde identiteit voor uw data factory om verbinding te maken met SQL Database of een door SQL beheerd exemplaar. Als vereiste moet u de beheerde identiteit voor uw data factory toevoegen als een database gebruiker die een SSISDB-exemplaar kan maken.

- Voeg uw Azure-SSIS IR toe aan een virtueel netwerk of configureer een zelf-hostende IR als proxy voor uw Azure-SSIS IR om toegang te krijgen tot gegevens on-premises.

In dit artikel wordt beschreven hoe u een Azure-SSIS IR inricht met behulp van de Azure Portal, Azure PowerShell en een Azure Resource Manager sjabloon.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement hebt, kunt u een [gratis proef](https://azure.microsoft.com/pricing/free-trial/) account maken.

- **SQL database of SQL Managed instance (optioneel)**. Als u nog geen server in SQL Database of een exemplaar van SQL Managed instance hebt, maakt u er een in de Azure Portal voordat u aan de slag gaat. Met Data Factory wordt een SSISDB-exemplaar voor deze server of dit exemplaar gemaakt. 

  We raden u aan de server of het beheerde exemplaar te maken in dezelfde Azure-regio als de Integration runtime. Met deze configuratie kan de integratie-runtime uitvoer logboeken schrijven naar SSISDB zonder overschrijding van Azure-regio's.

  Houd u aan de volgende punten:

  - Op basis van het geselecteerde product kan het SSISDB-exemplaar namens u worden gemaakt in SQL Database als één data base of als onderdeel van een elastische pool of in een door SQL beheerd exemplaar. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [SQL database en SQL Managed instance vergelijken](#comparison-of-sql-database-and-sql-managed-instance)voor hulp bij het kiezen van het juiste database product om SSISDB te hosten. 
  
    Als u SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of een door SQL beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [een Azure-SSIS IR toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)voor meer informatie.

  - Configureer voor SQL Database IP-firewall regels/virtuele netwerk service-eind punten of een door SQL beheerd exemplaar met een persoonlijk eind punt om SSISDB te hosten. Zie [Secure your Azure SQL database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules) (Azure SQL-database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)om deze instelling in te scha kelen met behulp van Power shell.

  - Voeg het IP-adres van de client computer of een bereik van IP-adressen met het IP-adres van de client computer toe aan de lijst met client-IP-adressen in de firewall instellingen voor de database server. Zie [firewall regels op server niveau en database niveau](../azure-sql/database/firewall-configure.md)voor meer informatie.

  - U kunt verbinding maken met de database server door SQL-verificatie te gebruiken met de referenties van uw server beheerder of door Azure AD-verificatie te gebruiken met de beheerde identiteit voor uw data factory. Voor de laatste moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor de database server. Zie [Azure AD-verificatie inschakelen voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)voor meer informatie.

  - Controleer of de database server al een SSISDB-exemplaar heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaand SSISDB-exemplaar.

- **Azure Resource Manager virtueel netwerk (optioneel)**. U moet een Azure Resource Manager virtueel netwerk hebben als ten minste één van de volgende voor waarden waar is:
  - U host SSISDB op SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of een door SQL beheerd exemplaar met een persoonlijk eind punt.
  - U wilt verbinding maken met on-premises gegevens archieven vanaf SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR zonder een zelf-hostende IR te configureren.

- **Azure PowerShell (optioneel)**. Volg de instructies in het [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps)als u een Power shell-script wilt uitvoeren om uw Azure-SSIS IR in te richten.

### <a name="regional-support"></a>Regionale ondersteuning

Zie [Data Factory en SSIS IR-Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-regio's waarin Data Factory en een Azure-SSIS IR beschikbaar zijn.

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Vergelijking van SQL Database en SQL Managed instance

In de volgende tabel worden bepaalde functies van SQL Database en SQL Managed instance vergeleken met de Azure-SSIR IR:

| Functie | SQL Database | SQL Managed Instance |
|---------|--------------|------------------|
| **Planning** | De SQL Server Agent is niet beschikbaar.<br/><br/>Zie [een pakket uitvoering plannen in een Data Factory-pijp lijn](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| De agent voor SQL Managed instance is beschikbaar. |
| **Verificatie** | U kunt een SSISDB-exemplaar maken met een Inge sloten database gebruiker die een Azure AD-groep vertegenwoordigt met de beheerde identiteit van uw data factory als lid van de **db_owner** rol.<br/><br/>Zie [Azure AD-verificatie inschakelen als u een SSISDB-exemplaar wilt maken op SQL database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | U kunt een SSISDB-exemplaar maken met een Inge sloten database gebruiker die de beheerde identiteit van uw data factory vertegenwoordigt. <br/><br/>Zie [Azure AD-verificatie inschakelen voor het maken van een SSISDB-exemplaar in een SQL Managed instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Servicelaag** | Wanneer u een Azure-SSIS IR met SQL Database maakt, kunt u de servicelaag voor SSISDB selecteren. Er zijn meerdere service lagen. | Wanneer u een Azure-SSIS IR met SQL Managed instance maakt, kunt u de servicelaag voor SSISDB niet selecteren. Alle data bases in SQL Managed instance delen dezelfde resource die aan dat exemplaar is toegewezen. |
| **Virtueel netwerk** | Uw Azure-SSIS IR kan worden toegevoegd aan een Azure Resource Manager virtueel netwerk als u SQL Database gebruikt met IP-firewall regels of virtuele-netwerk service-eind punten. | Uw Azure-SSIS IR kan worden toegevoegd aan een Azure Resource Manager virtueel netwerk als u een door SQL beheerd exemplaar met een persoonlijk eind punt gebruikt. Het virtuele netwerk is vereist wanneer u geen openbaar eind punt inschakelt voor uw beheerde exemplaar.<br/><br/>Als u uw Azure-SSIS IR lid maakt van hetzelfde virtuele netwerk als uw beheerde exemplaar, moet u ervoor zorgen dat uw Azure-SSIS IR zich in een ander subnet bevindt dan uw beheerde exemplaar. Als u uw Azure-SSIS IR lid maakt van een ander virtueel netwerk dan uw beheerde exemplaar, raden we u aan een peering voor het virtuele netwerk of een netwerk verbinding te maken. Zie [uw toepassing verbinden met een door SQL beheerd exemplaar](../azure-sql/managed-instance/connect-application-instance.md). |
| **Gedistribueerde transacties** | Deze functie wordt ondersteund via elastische trans acties. MSDTC-trans acties (micro soft Distributed Transaction Coordinator) worden niet ondersteund. Als uw SSIS-pakketten gebruikmaken van MSDTC voor het coördineren van gedistribueerde trans acties, kunt u overwegen om te migreren naar elastische trans acties voor Azure SQL Database. Zie [gedistribueerde trans acties in Cloud databases](../azure-sql/database/elastic-transactions-overview.md)voor meer informatie. | Niet ondersteund. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>De Azure Portal gebruiken om een Integration runtime te maken

In deze sectie gebruikt u de Azure Portal, met name de Data Factory gebruikers interface (UI) of de app, om een Azure-SSIS IR te maken.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

Als u uw data factory via de Azure Portal wilt maken, volgt u de stapsgewijze instructies in [een Data Factory maken via de gebruikers interface](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Selecteer **in dat geval vastmaken aan dash board** om snelle toegang toe te staan nadat het is gemaakt. 

Nadat uw data factory is gemaakt, opent u de pagina overzicht in de Azure Portal. Selecteer de tegel **auteur & monitor** om de pagina aan de **slag** te openen op een afzonderlijk tabblad. Daar kunt u door gaan met het maken van uw Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS-integratieruntime inrichten

1. Selecteer op de pagina **Aan de slag** de tegel **SSIS-integratieruntime configureren**.

   ![De tegel SSIS-integratieruntime configureren](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Voer de volgende stappen uit in het gedeelte **algemene instellingen** van **Integration runtime** configuratie venster.

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Voer bij **Naam** de naam van de integratieruntime in.
   2. Voer bij **beschrijving** de beschrijving van de integratieruntime in.
   3. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB.
   4. Selecteer voor **knooppunt grootte**de grootte van het knoop punt in het cluster Integration runtime. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer de grootte van een groot knoop punt (omhoog schalen) als u veel reken intensief of geheugenintensieve pakketten wilt uitvoeren.
   5. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knoop punten (uitschalen) als u veel pakketten parallel wilt uitvoeren.
   6. Voor **editie/licentie**selecteert u de SQL Server Edition voor uw Integration runtime: Standard of ENTER prise. Selecteer onderneming als u geavanceerde functies wilt gebruiken in uw Integration runtime.
   7. Selecteer voor **Bespaar geld**de Azure Hybrid Benefit optie voor de Integration runtime: **Ja** of **Nee**. Selecteer **Ja** als u uw eigen SQL Server licentie met Software Assurance wilt gebruiken om kosten besparingen met hybride gebruik te voor komen.
   8. Selecteer **Next**.

3. Voer de volgende stappen uit in de sectie **SQL-instellingen** .

   ![SQL-instellingen](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Schakel het selectie vakje **SSIS-catalogus maken (SSISDB) met SQL database/SQL Managed instance om uw projecten/pakketten/omgevingen/uitvoerings logboeken** op te slaan in om het Azure SQL-product te kiezen voor pakketten die op uw Azure-SSIS IR moeten worden uitgevoerd. U kiest ofwel het project implementatie model waarin pakketten worden geïmplementeerd in SSISDB die worden gehost door SQL Database of een door SQL beheerd exemplaar, of het implementatie model van het pakket waarbij pakketten worden geïmplementeerd in bestands systemen, bestands shares of Azure Files. 
    
    Als u het selectie vakje inschakelt, moet u uw eigen SQL Database of SQL Managed instance voor het hosten van de SSISDB-instantie die u namens u maakt en beheert.
   
    1. Selecteer bij **abonnement**het Azure-abonnement met uw SQL database of SQL Managed instance om SSISDB te hosten. 

    1. Selecteer bij **locatie**de locatie van uw SQL database of SQL Managed instance. We raden u aan dezelfde locatie van uw integratieruntime te selecteren. 

    1. Selecteer voor het **eind punt van de catalogus database server**het eind punt van uw SQL database of SQL Managed instance. 
    
       Op basis van de geselecteerde SQL Database of SQL Managed instance kan het SSISDB-exemplaar namens u worden gemaakt als één data base of als onderdeel van een elastische groep in SQL Database, of als een exemplaar database in een SQL-beheerd exemplaar. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [Azure SQL database en SQL Managed instance](#comparison-of-sql-database-and-sql-managed-instance)(Engelstalig) voor meer informatie over het kiezen van het product om SSISDB te hosten.
       Als u SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of-SQL Managed instance met persoonlijk eind punt selecteert om SSISDB te hosten of als u toegang nodig hebt tot on-premises gegevens zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [een Azure-SSIS IR toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)voor meer informatie. 

    1. Schakel het selectie vakje **Aad-verificatie gebruiken met de beheerde identiteit voor uw ADF** in om de verificatie methode voor uw database server te kiezen voor het hosten van SSISDB. U kiest SQL-verificatie of Azure AD-verificatie met de beheerde identiteit voor uw data factory. 
    
        Als u het selectie vakje inschakelt, moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor uw database server. Zie [Azure AD-verificatie inschakelen voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)voor meer informatie. 

    1. Voor **beheerders naam**voert u de gebruikers naam van de SQL-verificatie voor uw database server in om SSISDB te hosten. 

    1. Voor **beheerders wachtwoord**voert u het wacht woord voor SQL-verificatie voor uw database server in om SSISDB te hosten. 

    1. Selecteer voor de Servicelaag van de **catalogus database**de servicelaag voor de database server om SSISDB te hosten. Selecteer de laag Basic, Standard of Premium of selecteer de naam van een elastische groep. 

      1. Selecteer **verbinding testen**. Als de test is geslaagd, selecteert u **volgende**. 

4. Voer de volgende stappen uit in de sectie **Geavanceerde instellingen** .

   ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Voor **maximale parallelle uitvoeringen per knoop punt**selecteert u het maximum aantal pakketten dat gelijktijdig per knoop punt in uw Integration runtime-cluster moet worden uitgevoerd. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een laag aantal als u meer dan één kern wilt gebruiken voor het uitvoeren van één groot pakket dat wordt berekend of geheugenintensieve. Selecteer een hoog nummer als u een of meer kleine pakketten wilt uitvoeren in één kern.

   1. Schakel het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in om te kiezen of u standaard instellingen voor aangepaste/snelle installatie wilt toevoegen aan uw Azure-SSIS IR. Zie [aangepaste installatie voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)voor meer informatie.

      Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

      ![Geavanceerde instellingen met aangepaste installatie](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Voor de SAS-URI van de **aangepaste installatie container**voert u de SAS-URI in van uw container waarin u scripts en gekoppelde bestanden opslaat voor aangepaste standaard instellingen.

      1. Voor **snelle aangepaste installatie**selecteert u **Nieuw** om het deel venster **snelle aangepaste configuratie toevoegen** te openen en selecteert u vervolgens de gewenste typen onder het vervolg keuzemenu **expliciet aangepaste installatie type** , bijvoorbeeld **opdracht cmdkey**, een **omgevings variabele toevoegen**, **een gelicentieerd onderdeel installeren**, enzovoort.

        Als u het **onderdeel type licentie installeren** selecteert, kunt u vervolgens alle geïntegreerde onderdelen van onze ISV-partners selecteren in het vervolg keuzemenu **onderdeel naam** en, indien nodig, de product licentie code invoeren die u hebt aangeschaft in het veld **licentie sleutel** .
  
        Uw toegevoegde snelle aangepaste Setup wordt weer gegeven in de sectie **Geavanceerde instellingen** . Als u deze wilt verwijderen, kunt u de bijbehorende selectie vakjes selecteren en vervolgens **verwijderen**selecteren.

   1. Selecteer **een VNet selecteren voor uw Azure-SSIS Integration runtime om lid te worden van, ADF toestaan om bepaalde netwerk resources te maken en Schakel eventueel uw eigen statische open bare IP-adressen** in om te kiezen of u wilt samen voegen met een virtueel netwerk. 

    Selecteer deze optie als u SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of SQL Managed instance met een persoonlijk eind punt gebruikt om SSISDB te hosten, of als u toegang nodig hebt tot on-premises gegevens (dat wil zeggen: u hebt on-premises gegevens bronnen of-bestemmingen in uw SSIS-pakketten) zonder een zelf-hostende IR te configureren. Zie voor meer informatie [lid worden van Azure-SSIS IR aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

    ![Geavanceerde instellingen met een virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    1. Selecteer bij **abonnement**het Azure-abonnement met uw virtuele netwerk.

    1. Voor **locatie**is dezelfde locatie van de Integration runtime geselecteerd.

    1. Selecteer bij **type**het type van uw virtuele netwerk: klassiek of Azure Resource Manager. We raden u aan om een Azure Resource Manager virtueel netwerk te selecteren, omdat klassieke virtuele netwerken binnenkort worden afgeschaft.

    1. Selecteer voor **VNet-naam**de naam van het virtuele netwerk. Dit moet hetzelfde zijn als de naam die wordt gebruikt voor uw SQL Database met Service-eind punten voor virtuele netwerken of uw SQL Managed instance met een persoonlijk eind punt om SSISDB te hosten. Of het moet hetzelfde zijn als het account dat is verbonden met uw on-premises netwerk. Anders kan het een virtueel netwerk zijn om uw eigen statische open bare IP-adressen te maken voor Azure-SSIS IR.

    1. Selecteer bij **subnet naam**de naam van het subnet voor het virtuele netwerk. Dit moet hetzelfde zijn als de SQL Database met de service-eind punten van het virtuele netwerk om SSISDB te hosten. Of het moet een ander subnet zijn dat wordt gebruikt voor uw SQL Managed instance met een persoonlijk eind punt om SSISDB te hosten. Anders kan het een wille keurige subnet zijn om uw eigen statische open bare IP-adressen voor Azure-SSIS IR te brengen.

    1. Schakel het selectie vakje **statische open bare IP-adressen voor uw Azure-SSIS Integration runtime** in om te kiezen of u uw eigen statische open bare IP-adressen wilt meenemen voor Azure-SSIS IR, zodat u ze op de firewall voor uw gegevens bronnen kunt toestaan.

        Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

        1. Voor het **eerste statische open bare IP-adres**selecteert u het eerste statische open bare IP-adres dat voldoet aan de vereisten voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.
        2. Voor het **tweede statische open bare IP-adres**selecteert u het tweede statische open bare IP-adres dat voldoet aan de vereisten voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.

   1. Selecteer het selectie vakje **zelf-hostende Integration runtime als proxy voor uw Azure-SSIS Integration runtime instellen** om te kiezen of u een zelf-hostende IR wilt configureren als proxy voor uw Azure-SSIS IR. Zie [een zelf-hostende IR instellen als proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)voor meer informatie. 

      Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

      ![Geavanceerde instellingen met een zelf-hostende IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Voor **zelf-hostende Integration runtime**selecteert u uw bestaande zelf-hostende IR als proxy voor Azure-SSIS IR.

      1. Voor **faserings opslag gekoppelde**service selecteert u uw bestaande Azure Blob Storage-gekoppelde service of maakt u een nieuwe voor fase ring.

      1. Geef voor het **tijdelijke pad**een BLOB-container op in het geselecteerde Azure Blob Storage-account of laat het leeg om een standaard waarde te gebruiken voor fase ring.

   1. Selecteer **VNet-validatie**  >  **door gaan**. 

1. Controleer in de sectie **samen vatting** alle inrichtings instellingen, blad wijzer de aanbevolen documentatie koppelingen en selecteer **volt ooien** om het maken van de Integration runtime te starten.

   > [!NOTE]
   > Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid. Maar het kan 20-30 minuten duren voordat de Azure-SSIS IR lid wordt van een virtueel netwerk.
   >
   > Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw database server om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
   > 
   > Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel-bestanden, Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die in de ingebouwde onderdelen al worden ondersteund. Zie [aangepaste installatie voor een Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md)voor informatie over andere onderdelen die u kunt installeren.

1. Ga op het tabblad **Verbindingen** indien nodig naar **Integratieruntimes**. Selecteer **Vernieuwen** om de status te vernieuwen.

   ![Status van het maken](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Gebruik de koppelingen in de kolom **Acties** om de integratieruntime te stoppen/starten, te bewerken of te verwijderen. Gebruik de laatste koppeling om JSON-code weer te geven voor de integratieruntime. De knoppen Bewerken en Verwijderen zijn alleen beschikbaar wanneer de IR is gestopt.

   ![Azure SSIS-IR-acties](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integratieruntimes in de portal

1. Ga in de Azure Data Factory gebruikers interface naar het tabblad **bewerken** en selecteer **verbindingen**. Schakel vervolgens over naar het tabblad **Integration Runtimes** om bestaande integratie-Runtimes in uw Data Factory weer te geven.

   ![Bestaande IR’s weergeven](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecteer **Nieuw** om een nieuw Azure-SSIS IR te maken.

   ![Integratieruntime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Selecteer in het configuratie scherm van **Integration runtime** de **lift-en Shift-bestaande SSIS-pakketten die moeten worden uitgevoerd in de Azure-** tegel en selecteer **volgende**.

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zie de sectie [een Azure SSIS Integration runtime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS IR.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Azure PowerShell gebruiken om een Integration runtime te maken

In deze sectie gebruikt u Azure PowerShell om een Azure-SSIS IR te maken.

### <a name="create-variables"></a>Variabelen maken

Kopieer en plak het volgende script. Geef waarden op voor de variabelen. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Meld u aan en selecteer een abonnement

Voeg het volgende script toe om u aan te melden en uw Azure-abonnement te selecteren.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection"></a>De verbinding valideren

Voeg het volgende script toe om de verbinding met SQL Database of SQL Managed instance te valideren.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Het virtuele netwerk configureren

Voeg het volgende script toe om de machtigingen en instellingen van het virtuele netwerk voor uw Azure-SSIS Integration runtime automatisch te configureren om lid te worden.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resource groep](../azure-resource-manager/management/overview.md) met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

Als uw resourcegroep al bestaat, hoeft u deze code niet naar het script te kopiëren. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voer de volgende opdracht uit om een data factory te maken.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Een Integration Runtime maken

Voer de volgende opdrachten uit om een Azure SSIS Integration runtime te maken waarmee SSIS-pakketten worden uitgevoerd in Azure.

Als u SSISDB niet gebruikt, kunt u de `CatalogServerEndpoint` `CatalogPricingTier` para meters, en weglaten `CatalogAdminCredential` .

Als u geen gebruik maakt van SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of SQL Managed instance met een persoonlijk eind punt om SSISDB te hosten, of toegang tot on-premises gegevens nodig hebt, kunt u de `VNetId` `Subnet` para meters en gebruiken of lege waarden door geven. U kunt ze ook weglaten als u een zelf-hostende IR als proxy voor uw Azure-SSIS IR wilt gebruiken om toegang te krijgen tot gegevens on-premises. Als dat niet het geval is, kunt u ze niet weglaten en moet u geldige waarden van de configuratie van uw virtuele netwerk door geven. Zie [een Azure-SSIS IR toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)voor meer informatie.

Als u SQL Managed instance gebruikt om SSISDB te hosten, kunt u de `CatalogPricingTier` para meter weglaten of een lege waarde voor het exemplaar door geven. Als dat niet het geval is, kunt u deze niet weglaten en moet u een geldige waarde door geven uit de lijst met ondersteunde prijs categorieën voor Azure SQL Database. Zie [SQL database resource limieten](../sql-database/sql-database-resource-limits.md)voor meer informatie.

Als u Azure AD-verificatie gebruikt met de beheerde identiteit voor uw data factory om verbinding te maken met de database server, kunt u de `CatalogAdminCredential` para meter weglaten. Maar u moet de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor de database server. Zie [Azure AD-verificatie inschakelen voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)voor meer informatie. Als dat niet het geval is, kunt u deze niet weglaten en moet u een geldig object door geven uit de gebruikers naam en het wacht woord van uw server beheerder voor SQL-verificatie

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>De Integration runtime starten

Voer de volgende opdrachten uit om de Azure-SSIS Integration runtime te starten.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid. Maar het kan 20-30 minuten duren voordat de Azure-SSIS IR lid wordt van een virtueel netwerk.
>
> Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw database server om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
> 
> Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel-bestanden, Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die in de ingebouwde onderdelen al worden ondersteund. Zie [aangepaste installatie voor een Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md)voor informatie over andere onderdelen die u kunt installeren.

### <a name="full-script"></a>Volledige script

Hier is het volledige script waarmee een Azure SSIS Integration runtime wordt gemaakt.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Een Azure Resource Manager sjabloon gebruiken om een Integration runtime te maken

In deze sectie gebruikt u een Azure Resource Manager sjabloon om de Azure SSIS Integration runtime te maken. Hier volgt een voor beeld van een scenario:

1. Maak een JSON-bestand met de volgende Azure Resource Manager sjabloon. Vervang waarden in de punt haken (tijdelijke aanduidingen) door uw eigen waarden.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<server name>.database.windows.net",
                                "catalogAdminUserName": "<server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Als u de Azure Resource Manager sjabloon wilt implementeren, voert u de opdracht uit, `New-AzResourceGroupDeployment` zoals in het volgende voor beeld wordt weer gegeven. In het voor beeld `ADFTutorialResourceGroup` is de naam van de resource groep. `ADFTutorialARM.json`is het bestand dat de JSON-definitie bevat voor uw data factory en de Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Met deze opdracht worden uw data factory gemaakt en Azure-SSIS IR, maar wordt de IR niet gestart.

3. Voer de volgende opdracht uit om de Azure-SSIS IR te starten `Start-AzDataFactoryV2IntegrationRuntime` :

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid. Maar het kan 20-30 minuten duren voordat de Azure-SSIS IR lid wordt van een virtueel netwerk.
>
> Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw database server om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
> 
> Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel-bestanden, Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die in de ingebouwde onderdelen al worden ondersteund. Zie [aangepaste installatie voor een Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md)voor informatie over andere onderdelen die u kunt installeren.

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten erin implementeren en deze uitvoeren op uw Azure-SSIS IR met behulp van SQL Server Data Tools (SSDT) of SQL Server Management Studio (SSMS)-hulpprogram ma's. Deze hulpprogram ma's maken verbinding met uw database server via het server-eind punt: 

- Voor SQL Database is de indeling van het server eindpunt `<server name>.database.windows.net` .
- Voor een door SQL beheerd exemplaar met een persoonlijk eind punt is de indeling van het server eindpunt `<server name>.<dns prefix>.database.windows.net` .
- Voor een SQL-beheerd exemplaar met een openbaar eind punt is de indeling van het server eindpunt `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Als u SSISDB niet gebruikt, kunt u uw pakketten implementeren in bestands systemen, bestands shares of Azure Files en deze uitvoeren op uw Azure-SSIS IR met behulp van de-, `dtinstall` `dtutil` -en- `dtexec` opdracht regel Programma's. Zie [SSIS-pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)voor meer informatie. 

In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de activiteit voor het uitvoeren van SSIS-pakketten in Data Factory-pijp lijnen. Zie [de uitvoering van SSIS-pakketten aanroepen als een eerste klasse Data Factory-activiteit](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie andere Azure-SSIS IR onderwerpen in deze documentatie:

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel vindt u informatie over Integration Runtimes in het algemeen, met inbegrip van Azure-SSIS IR.
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over uw Azure-SSIS IR ophaalt en begrijpt.
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u uw Azure-SSIS IR kunt stoppen, starten of verwijderen. U ziet ook hoe u uw Azure-SSIS IR kunt uitschalen door meer knoop punten toe te voegen.
- [SSIS-pakketten implementeren, uitvoeren en bewaken in azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 
- [Pakket uitvoeringen plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)