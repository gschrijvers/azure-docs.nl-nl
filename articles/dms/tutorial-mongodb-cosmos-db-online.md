---
title: 'Zelf studie: MongoDB online migreren naar Azure Cosmos DB-API voor MongoDB'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het migreren van MongoDB on-premises naar Azure Cosmos DB API voor MongoDB online met behulp van Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 66375d83dca4edef17919e3b493d5e45be37cc40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78255625"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Zelf studie: MongoDB migreren naar de API van Azure Cosmos DB voor MongoDB online met behulp van DMS

U kunt Azure Database Migration Service gebruiken om een online migratie (minimale downtime) van data bases uit te voeren vanuit een on-premises of Cloud exemplaar van MongoDB naar de API van Azure Cosmos DB voor MongoDB.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Maak een exemplaar van de Azure Database Migration Service.
> * Een migratie project maken met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.
> * Voltooi de migratie als u klaar bent.

In deze zelf studie migreert u een gegevensset in MongoDB die wordt gehost op een virtuele machine van Azure naar Azure Cosmos DB API voor MongoDB met minimale downtime door gebruik te maken van Azure Database Migration Service. Als u nog geen MongoDB-bron hebt ingesteld, raadpleegt u het artikel over het [installeren en configureren van MongoDB op een Windows-VM in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt micro soft aan om een instantie van Azure Database Migration Service te maken in dezelfde Azure-regio als de doel database. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een onlinemigratie beschreven van MongoDB naar Azure Cosmos DB's API voor MongoDB. Zie [MongoDB migreren naar Azure Cosmos DB's API voor offline MongoDB met behulp van DMS](tutorial-mongodb-cosmos-db.md) voor een offlinemigratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* [Voltooi de stappen voorafgaand aan de migratie](../cosmos-db/mongodb-pre-migration.md) , zoals het schatten van de door Voer, het kiezen van een partitie sleutel en het indexerings beleid.
* [Maak een account voor Azure Cosmos DB's API voor MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van Azure Resource Manager implementatie model, waarmee u een site-naar-site-verbinding met uw on-premises bron servers kunt maken met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Als u tijdens de installatie van het virtuele netwerk ExpressRoute gebruikt met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > * Opslag eindpunt
    > * Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat Azure Database Migration Service geen verbinding met internet heeft.

* Zorg ervoor dat de regels voor de netwerk beveiligings groep (NSG) van uw virtuele netwerk niet de volgende communicatie poorten blok keren: 53, 443, 445, 9354 en 10000-20000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van NSG verkeer van virtuele netwerken.
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron MongoDB-server, die standaard TCP-poort 27017 is.
* Wanneer u een firewall apparaat voor uw bron database (s) gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database (s) voor de migratie.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het exemplaar van Azure Database Migration Service wilt maken en selecteer vervolgens **resource providers**.

    ![Resourceproviders weergeven](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Zoek naar migratie, klik rechts van **micro soft. DataMigration**, selecteer **registreren**.

    ![Resourceprovider registreren](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarin u het exemplaar van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuwe.

   Het virtuele netwerk biedt Azure Database Migration Service toegang tot het bron MongoDB-exemplaar en het doel Azure Cosmos DB-account.

   Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure Portal.

6. Selecteer een SKU in de prijscategorie Premium.

    > [!NOTE]
    > Onlinemigraties worden alleen ondersteund bij gebruik van deze categorie. Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Op het scherm **Azure data base Migration Services** zoekt u de naam van Azure database Migration service exemplaar dat u hebt gemaakt en selecteert u vervolgens het exemplaar.

    U kunt ook het Azure data base Migration service-exemplaar vinden in het deel venster zoeken in Azure Portal.

    ![Het zoekvenster gebruiken in de Azure-portal](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **Type bronserver**, selecteer **MongoDB**, selecteer in het tekstvak **Type doelserver****CosmosDB (MongoDB-API)**, en selecteer bij **Type activiteit kiezen** de optie **Online gegevensmigratie [preview-versie]**.

    ![Database Migration Service-project maken](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Selecteer **Opslaan** en vervolgens **Create and run activity** om het project te maken en de migratieactiviteit uit te voeren.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails** de verbindingsgegevens op voor de MongoDB-bronserver.

   > [!IMPORTANT]
   > Azure Database Migration Service biedt geen ondersteuning voor Azure Cosmos DB als bron.

    Er zijn drie modi om verbinding te maken met een bron:
   * **Standaardmodus**: deze accepteert een Fully Qualified Domain Name of een IP-adres, poortnummer en verbindingsreferenties.
   * **Modus verbindingsreeks**: deze accepteert een MongoDB-verbindingsreeks, zoals beschreven in het artikel [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (URI-indeling van verbindingsreeks).
   * **Gegevens uit Azure-opslag**: deze accepteert een SAS-URL van de blob-container. Selecteer **Blob contains BSON dump** als de blob-container BSON-dumps bevat die zijn geproduceerd door het [bsondump-hulpprogramma](https://docs.mongodb.com/manual/reference/program/bsondump/) van MongoDB en deselecteer het als de container JSON-bestanden bevat.

     Als u deze optie selecteert, controleer dan of de verbindingsreeks van het opslagaccount wordt weergegeven in de volgende indeling:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Op basis van het type dump-informatie in Azure Storage moet u ook de volgende details in de hand.

     * Voor BSON-dumps moeten de gegevens in de blob-container de bsondump-indeling hebben, zodat de gegevensbestanden worden geplaatst in mappen die worden genoemd naar de omvattende databases in de collection.bson-indeling. Metagegevensbestanden (indien aanwezig) moeten een naam krijgen op basis van de indeling *verzameling*.metadata.json.

     * Voor JSON-dumps moeten de bestanden in de blob-container worden geplaatst in mappen die zijn genoemd naar de omvattende databases. In elke databasemap moeten gegevensbestanden worden geplaatst in een submap met de naam 'data' en ze moeten een naam krijgen op basis van de indeling *verzameling*.json. Metagegevensbestanden (indien aanwezig) moeten worden geplaatst in een submap met de naam 'metadata' en ze moeten een naam krijgen op basis van dezelfde indeling *verzameling*.json. De metagegevensbestanden moeten de indeling hebben die wordt geproduceerd door het MongoDB-hulpprogramma bsondump.

    > [!IMPORTANT]
    > Het wordt afgeraden om een zelfondertekend certificaat te gebruiken op de Mongo-server. Als er echter een wordt gebruikt, moet u verbinding maken met de server met behulp van **Connection String modus** en ervoor zorgen dat uw Connection String ' "
    >
    >```
    >&sslVerifyCertificate=false
    >```

    U kunt het IP-adres gebruiken voor situaties waarin DNS-naamomzetting niet mogelijk is.

   ![Geef brondetails op](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Selecteer **Opslaan**.

   > [!NOTE]
   > Het adres van de bronserver moet het adres zijn van de primaire server als de bron een replicaset is. Het moet het adres van de router zijn als de bron een shard-MongoDB-cluster is. Voor een shard-MongoDB-cluster moet Azure Database Migration Service verbinding kunnen maken met de afzonderlijke shards in het cluster. Hiervoor moet mogelijk de firewall op meerdere machines worden geopend.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Details migratiedoel** de verbindingsgegevens op voor het Azure Cosmos DB-doelaccount, dat bestaat uit het vooraf ingerichte Azure Cosmos DB's API voor MongoDB-account waarnaar u de MongoDB-database migreert.

    ![Doeldetails opgeven](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Selecteer **Opslaan**.

## <a name="map-to-target-databases"></a>Toewijzen aan doeldatabases

1. Wijs in het scherm **Toewijzen aan doeldatabases** de bron- en doeldatabase voor de migratie toe.

   Als de doel database dezelfde database naam als de bron database bevat Azure Database Migration Service de doel database standaard geselecteerd.

   Als de teken reeks wordt **gemaakt** naast de naam van de data base, geeft deze aan dat Azure database Migration service de doel database niet heeft gevonden en de service de Data Base voor u maakt.

   Geef op dit moment in de migratie een doorvoer-RU op als u door voer wilt delen voor de data base. In Cosmos DB kunt u doorvoer inrichten op het niveau van de database of afzonderlijk voor elke verzameling. Doorvoer wordt gemeten in [Aanvraageenheden](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU's). Meer informatie over de [prijzen van Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Toewijzen aan doeldatabases](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Selecteer **Opslaan**.

3. Vouw in het scherm **Verzamelingsinstelling** de lijst met verzamelingen uit en bekijk welke verzamelingen worden gemigreerd.

   Azure Database Migration Service selecteert automatisch alle verzamelingen die bestaan in het bron MongoDB-exemplaar dat niet bestaat op het doel Azure Cosmos DB account. Als u verzamelingen die al gegevens bevatten, opnieuw wilt migreren, moet u die verzamelingen expliciet selecteren in dit scherm.

   U kunt opgeven hoeveel RU's u voor de verzamelingen wilt gebruiken. In de meeste gevallen moet een waarde tussen de 500 (minimaal 1000 voor shard-verzamelingen) en 4000 voldoende zijn. Azure Database Migration Service suggereert slimme standaard waarden op basis van de grootte van de verzameling.

    > [!NOTE]
    > Voer, indien nodig, de database migratie en verzameling parallel uit met behulp van meerdere exemplaren van Azure Database Migration Service, om de uitvoering te versnellen.

   U kunt ook een shardsleutel opgeven om te profiteren van [partitionering in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) voor optimale schaalbaarheid. Lees de [best practices voor het selecteren van een shard-/partitiesleutel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Als u geen partitiesleutel hebt, kunt u altijd gebruikmaken van **_id** als de shardsleutel voor een betere doorvoer.

   ![Verzamelingstabellen selecteren](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Selecteer **Opslaan**.

5. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

    ![Migratieoverzicht](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

   Het venster van de migratieactiviteit wordt weergegeven en de **status** van de activiteit wordt weergegeven.

   ![Activiteitsstatus](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

* Selecteer **Vernieuwen** in het scherm van de activiteitenmigratie om de weergave bij te werken totdat de **status** van de migratie als **Opnieuw afspelen** wordt weergegeven.

   > [!NOTE]
   > U kunt de activiteit selecteren voor meer informatie over metrische migratiegegevens op het niveau van een database of verzameling.

   ![Activiteitsstatus opnieuw afspelen](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Gegevens controleren in Cosmos DB

1. Breng wijzigingen aan uw MongoDB-brondatabase aan.
2. Maak verbinding met COSMOS DB om te verifiëren of de gegevens vanuit de MongoDB-bronserver worden gerepliceerd.

    ![Activiteitsstatus opnieuw afspelen](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Migratie voltooien

* Als alle documenten van de bron beschikbaar zijn op het COSMOS DB-doel, selecteert u **Voltooien** in het snelmenu van de migratieactiviteit om de migratie te voltooien.

    Met deze actie wordt het opnieuw afspelen van alle wijzigingen die in behandeling zijn, voltooid en tevens de migratie.

    ![Activiteitsstatus opnieuw afspelen](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optimalisatie na migratie

Nadat u de gegevens die zijn opgeslagen in MongoDB Data Base hebt gemigreerd naar de API van Azure Cosmos DB voor MongoDB, kunt u verbinding maken met Azure Cosmos DB en de gegevens beheren. U kunt ook andere optimalisatie stappen voor na de migratie uitvoeren, zoals het optimaliseren van het indexerings beleid, het standaard consistentie niveau bijwerken of de globale distributie configureren voor uw Azure Cosmos DB-account. Zie voor meer informatie het artikel [optimalisatie na de migratie](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Aanvullende bronnen

* [Informatie over Cosmos DB-service](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Volgende stappen

* Lees de Microsoft-[handleiding voor databasemigratie](https://datamigration.microsoft.com/) voor hulp bij andere migratiescenario's.
