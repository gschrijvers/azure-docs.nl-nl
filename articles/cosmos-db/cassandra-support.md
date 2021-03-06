---
title: Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies
description: Meer informatie over de ondersteuning van Apache Cassandra-functies in Azure Cosmos DB Cassandra-API
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 978dbf3d8e6a92242c0a984b26bb35cf911a3369
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590419"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies 

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt communiceren met de Azure Cosmos DB Cassandra-API via open-source Cassandra client[stuurprogramma's](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) die compatibel zijn met Cassandra Query Language (CQL) v4 [wire-protocol](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec). 

Door de Azure Cosmos DB Cassandra API te gebruiken, kunt u profiteren van de voordelen van de Apache Cassandra API’s en van de enterprise-mogelijkheden die Azure Cosmos DB biedt. De enterprise-mogelijkheden zijn onder andere [wereldwijde distributie](distribute-data-globally.md), [automatische scale-out partitionering](partition-data.md), beschikbaarheid- en latentiegaranties, versleuteling van gegevens in rust, back-ups en nog veel meer.

## <a name="cassandra-protocol"></a>Cassandra-protocol 

De Cassandra-API van Azure Cosmos DB is compatibel met CQL versie **v4**. De ondersteunde CQL-opdrachten, hulpprogramma's, beperkingen en uitzonderingen worden hieronder vermeld. Elk clientstuurprogramma dat deze protocollen kent, kan verbinding maken met Azure Cosmos DB Cassandra-API.

## <a name="cassandra-driver"></a>Cassandra-stuurprogramma

De volgende versies van de Cassandra-stuurprogramma's worden ondersteund door Azure Cosmos DB Cassandra-API:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CQL-gegevenstypen 

Azure Cosmos DB Cassandra-API ondersteunt de volgende CQL-gegevenstypen:

* ascii  
* bigint  
* blob  
* booleaans  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* tekst  
* tijd  
* tijdstempel  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>CQL-functies

Azure Cosmos DB Cassandra-API ondersteunt de volgende CQL-functies:

* Token  
* Statistische functies
  * min, max, gem, aantal
* Blob-conversiefuncties 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID- en timeuuid-functies 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-api-limits"></a>Limieten voor Cassandra-API

Azure Cosmos DB Cassandra-API heeft geen limiet wat betreft de grootte van gegevens die in een tabel worden opgeslagen. Er kunnen honderden terabytes of petabytes aan gegevens worden opgeslagen terwijl de partitiesleutellimieten worden gerespecteerd. Evenzo heeft elke entiteit of rij-equivalent geen limieten voor het aantal kolommen. De totale omvang van de entiteit mag echter niet groter zijn dan 2 MB. De gegevens per partitiesleutel mogen niet groter zijn dan 20 GB, zoals in alle andere API's.

## <a name="tools"></a>Hulpprogramma's 

Azure Cosmos DB Cassandra-API is een beheerd serviceplatform. Het vereist geen beheeroverhead of hulpprogramma's zoals Garbage Collector, Java Virtual Machine (JVM) en nodetool om het cluster te beheren. Het ondersteunt hulpprogramma’s zoals cqlsh, dat binaire CQLv4-compatibiliteit gebruikt. 

* Andere ondersteunde mechanismen voor het beheren van het account zijn Data Explorer, metrische gegevens, logboekdiagnose, PowerShell en CLI in de Azure-portal.

## <a name="hosted-cql-shell-preview"></a>Gehoste CQL-shell (preview)

U kunt een gehoste Cassandra-shell (CQLSH v5.0.1) rechtstreeks vanuit Data Explorer openen in de [Azure-portal](data-explorer.md) of in [Azure Cosmos Explorer](https://cosmos.azure.com/). Voordat u de CQL-shell inschakelt, moet u de functie [Notebooks](enable-notebooks.md) inschakelen in uw account (als deze nog niet is ingeschakeld, wordt u gevraagd dit te doen wanneer u klikt op `Open Cassandra Shell`). Controleer de gemarkeerde notitie in [Notebooks inschakelen voor Azure Cosmos DB-accounts](enable-notebooks.md) voor ondersteunde Azure-regio's.

![CQLSH](./media/cassandra-support/cqlsh.png)

U kunt ook verbinding maken met de Cassandra-API in Azure Cosmos DB met behulp van de CQLSH die is geïnstalleerd op een lokale computer. De API wordt geleverd met Apache Cassandra 3.1.1 en is meteen klaar voor gebruik door de omgevingsvariabelen in te stellen. De volgende secties bevatten instructies voor het installeren, configureren en verbinding maken met de Cassandra-API in Azure Cosmos DB, in Windows of Linux met behulp van CQLSH.

**Windows:**

Als u Windows gebruikt, raden we u aan om het [Windows-bestandssysteem voor Linux](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux) in te schakelen. U kunt vervolgens de onderstaande Linux-opdrachten volgen.

**UNIX/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>CQL-opdrachten

Azure Cosmos DB ondersteunt de volgende databaseopdrachten op Cassandra-API-accounts.

* CREATE KEYSPACE (de replicatie-instellingen voor deze opdracht worden genegeerd)
* CREATE TABLE 
* CREATE INDEX (zonder de indexnaam op te geven, en volledig geblokkeerde indexen worden nog niet ondersteund)
* ALLOW FILTERING
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - alleen niet-geregistreerde opdrachten worden ondersteund 
* DELETE

Alle CRUD-bewerkingen die worden uitgevoerd via een SDK die compatibel is met CQL v4 retourneren, extra informatie over fouten en verbruikte aanvraageenheden. De opdrachten DELETE en UPDATE moeten worden afgehandeld met resourcebeheer om te zorgen voor het meest efficiënte gebruik van de ingerichte doorvoer.

* Let wel: de waarde gc_grace_seconds moet nul zijn als deze is opgegeven.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Consistentietoewijzing 

Azure Cosmos DB Cassandra-API biedt een keuze aan consistentie voor leesbewerkingen.  [Hier](consistency-levels-across-apis.md#cassandra-mapping) vindt u de details van de consistentietoewijzing.

## <a name="permission-and-role-management"></a>Machtigings- en rolbeheer

Azure Cosmos DB biedt op rollen gebaseerd toegangsbeheer (RBAC) voor inrichten, wisselen van sleutels, weergeven van metrische gegevens, en wachtwoorden voor lezen/schrijven en wachtwoorden met het kenmerk alleen-lezen die kunnen worden verkregen via de [Azure-portal](https://portal.azure.com). Azure Cosmos DB biedt geen ondersteuning voor rollen voor CRUD-activiteiten.

## <a name="keyspace-and-table-options"></a>Opties voor Keyspace en tabel

De opties region name, class, replication_factor en datacenter in de opdracht CREATE KEYSPACE worden momenteel genegeerd. Het systeem maakt gebruik van de onderliggende replicatiemethode voor [globale distributie](global-dist-under-the-hood.md) van Azure Cosmos DB om de regio's toe te voegen. Als u wilt dat gegevens in meerdere regio's aanwezig zijn, kunt u dit op accountniveau inschakelen met PowerShell, CLI of de portal. Raadpleeg het artikel [Regio's toevoegen](how-to-manage-database-account.md#addremove-regions-from-your-database-account) voor meer informatie. Durable_writes kan niet worden uitgeschakeld omdat in Azure Cosmos DB elke schrijfbewerking gegarandeerd duurzaam is. Met Azure Cosmos DB worden de gegevens in elke regio gerepliceerd in de hele replicaset die bestaat uit vier replica’s, en de [configuratie](global-dist-under-the-hood.md) van deze replicaset kan niet worden gewijzigd.
 
Alle opties worden genegeerd bij het maken van de tabel, met uitzondering van gc_grace_seconds, die moeten worden ingesteld op nul.
Keyspace en tabel hebben een extra optie genaamd cosmosdb_provisioned_throughput, met een minimumwaarde van 400 RU/s. De Keyspace-doorvoer staat delen van doorvoer in meerdere tabellen toe, en is handig voor scenario’s waarbij geen van de tabellen gebruikmaakt van de ingerichte doorvoer. De opdracht Alter Table maakt het wijzigen van ingerichte doorvoer mogelijk in alle regio’s. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Gebruik van het Cassandra-beleid voor opnieuw proberen

Azure Cosmos DB is een systeem voor resourcebeheer. Dit betekent dat u een bepaald aantal bewerkingen in een opgegeven seconde kunt uitvoeren, op basis van de aanvraageenheden die bij deze bewerkingen worden verbruikt. Als een toepassing deze limiet in een opgegeven seconde overschrijdt, gelden er beperkingen voor aanvragen en worden er uitzonderingen gegenereerd. Met de Cassandra-API in Azure Cosmos DB worden deze uitzonderingen vertaald in overbelastfouten in het systeemeigen Cassandra-protocol. De [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)- en [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions)-extensies worden geboden om ervoor te zorgen dat de toepassing aanvragen kan onderscheppen en opnieuw kan proberen wanneer er sprake is van een beperking. Bekijk ook de Java-codevoorbeelden voor [versie 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) en [versie 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) van de Datastax-stuurprogramma's, wanneer u verbinding maakt met de Cassandra-API in Azure Cosmos DB. Als u andere SDK's gebruikt om toegang te krijgen tot de Cassandra-API in Azure Cosmos DB, maakt u verbindingsbeleid om deze uitzonderingen opnieuw te proberen.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing
