---
title: Gegevens kopiëren van en naar Azure SQL Managed instance
description: Meer informatie over het verplaatsen van gegevens van en naar Azure SQL Managed instance met behulp van Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: d33c54c677cb3c16b6ef295ee39c0ac3b6fb0159
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148307"
---
# <a name="copy-data-to-and-from-azure-sql-managed-instance-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure SQL Managed instance met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure SQL Managed instance. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze connector voor SQL Managed instance wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

U kunt gegevens van een door SQL beheerd exemplaar kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar het SQL Managed instance. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit.

Deze SQL Managed instance connector ondersteunt met name:

- Kopiëren van gegevens met behulp van SQL-verificatie en Azure Active Directory (Azure AD) verificatie van het toepassings token met een service-principal of beheerde identiteiten voor Azure-resources.
- Als bron, waarbij gegevens worden opgehaald met behulp van een SQL-query of een opgeslagen procedure.
- Het toevoegen van gegevens aan een doel tabel of het aanroepen van een opgeslagen procedure met aangepaste logica tijdens het kopiëren als een sink.

>[!NOTE]
> De door SQL beheerde instantie [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) wordt nu niet ondersteund door deze connector. U kunt een [algemene ODBC-Connector](connector-odbc.md) en een SQL Server ODBC-stuur programma gebruiken via een zelf-hostende Integration runtime. Volg [deze richt lijnen voor het](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) downloaden van ODBC-stuur Programma's en het Connection String van configuraties.

## <a name="prerequisites"></a>Vereisten

Om toegang te krijgen tot het [open bare eind punt](../azure-sql/managed-instance/public-endpoint-overview.md)van het SQL-beheerde exemplaar, kunt u een Azure Data Factory beheerde Azure Integration runtime gebruiken. Zorg ervoor dat u het open bare eind punt inschakelt en ook openbaar eindpunt verkeer op de netwerk beveiligings groep toestaat, zodat Azure Data Factory verbinding kan maken met uw data base. Zie [deze richt lijnen](../azure-sql/managed-instance/public-endpoint-configure.md)voor meer informatie.

Stel een [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) in die toegang heeft tot de data base om toegang te krijgen tot het privé-eind punt van het SQL Managed instance. Als u de zelf-hostende Integration runtime in hetzelfde virtuele netwerk als uw beheerde exemplaar inricht, moet u ervoor zorgen dat uw Integration runtime-machine zich in een ander subnet bevindt dan uw beheerde exemplaar. Als u uw zelf-hostende Integration runtime inricht in een ander virtueel netwerk dan uw beheerde exemplaar, kunt u een peering van een virtueel netwerk of een virtueel netwerk naar een virtueel netwerk verbinding maken. Zie [verbinding maken tussen uw toepassing en een SQL Managed instance](../azure-sql/managed-instance/connect-application-instance.md)voor meer informatie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Azure Data Factory entiteiten die specifiek zijn voor de SQL Managed instance connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service van het SQL Managed instance:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureSqlMI**. | Ja |
| Verbindings |Met deze eigenschap geeft u de **Connections Tring** -gegevens die nodig zijn om verbinding te maken met een SQL-beheerd exemplaar met behulp van SQL-verificatie. Zie de volgende voor beelden voor meer informatie. <br/>De standaardpoort is 1433. Als u SQL Managed instance met een openbaar eind punt gebruikt, geeft u expliciet poort 3342 op.<br> U kunt ook een wacht woord in Azure Key Vault plaatsen. Als de SQL-verificatie wordt uitgevoerd, haalt u de `password` configuratie uit het Connection String. Zie voor meer informatie het JSON-voor beeld dat volgt op de tabel en [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| servicePrincipalId | Geef de client-ID van de toepassing op. | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in azure Data Factory of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| tenant | Geef de Tenant gegevens op, zoals de domein naam of Tenant-ID, waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| connectVia | Deze [Integration runtime](concepts-integration-runtime.md) wordt gebruikt om verbinding te maken met het gegevens archief. U kunt een zelf-hostende Integration runtime of een Azure Integration runtime gebruiken als uw beheerde exemplaar een openbaar eind punt heeft en Azure Data Factory toegang heeft. Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Ja |

Raadpleeg de volgende secties over respectievelijk de vereisten en JSON-voor beelden voor verschillende verificatie typen:

- [SQL-verificatie](#sql-authentication)
- [Verificatie van Azure AD-toepassings token: Service-Principal](#service-principal-authentication)
- [Verificatie van Azure AD-toepassings tokens: beheerde identiteiten voor Azure-resources](#managed-identity)

### <a name="sql-authentication"></a>SQL-verificatie

**Voor beeld 1: SQL-verificatie gebruiken**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: SQL-verificatie gebruiken met een wacht woord in Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Voer de volgende stappen uit om een Azure AD-toepassings token verificatie op basis van een service-principal te gebruiken:

1. Volg de stappen om [een Azure Active Directory beheerder in te richten voor uw beheerde exemplaar](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) vanuit de Azure Portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

    - Toepassings-id
    - Toepassings sleutel
    - Tenant-id

3. [Aanmeldingen maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) voor de door de Azure Data Factory beheerde identiteit. Maak in SQL Server Management Studio (SSMS) verbinding met uw beheerde exemplaar met behulp van een SQL Server account dat een **sysadmin**is. Voer de volgende T-SQL uit in de **hoofd** database:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Inge sloten database gebruikers maken](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) voor de Azure Data Factory beheerde identiteit. Maak verbinding met de data base van of naar waarnaar u gegevens wilt kopiëren en voer de volgende T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Verleen de Data Factory beheerde identiteit de benodigde machtigingen zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit. Zie [dit document](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)voor meer opties.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Een gekoppelde service van een door SQL beheerde instantie configureren in Azure Data Factory.

**Voor beeld: Service-Principal-verificatie gebruiken**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Beheerde identiteiten voor Azure-bronnen verificatie

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke Data Factory vertegenwoordigt. U kunt deze beheerde identiteit voor SQL Managed instance-verificatie gebruiken. De aangewezen Factory heeft toegang tot en kopiëren van gegevens van of naar uw data base met behulp van deze identiteit.

Voer de volgende stappen uit om beheerde identiteits verificatie te gebruiken.

1. Volg de stappen om [een Azure Active Directory beheerder in te richten voor uw beheerde exemplaar](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Aanmeldingen maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) voor de door de Azure Data Factory beheerde identiteit. Maak in SQL Server Management Studio (SSMS) verbinding met uw beheerde exemplaar met behulp van een SQL Server account dat een **sysadmin**is. Voer de volgende T-SQL uit in de **hoofd** database:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Inge sloten database gebruikers maken](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) voor de Azure Data Factory beheerde identiteit. Maak verbinding met de data base van of naar waarnaar u gegevens wilt kopiëren en voer de volgende T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Verleen de Data Factory beheerde identiteit de benodigde machtigingen zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit. Zie [dit document](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)voor meer opties.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Een gekoppelde service van een door SQL beheerde instantie configureren in Azure Data Factory.

**Voor beeld: beheerde identiteits verificatie gebruiken**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel gegevens sets voor een volledige lijst met secties en eigenschappen die kunnen worden gebruikt voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de door de SQL Managed instance-gegevensset.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens naar en van een SQL Managed instance:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzureSqlMITable**. | Ja |
| schema | De naam van het schema. |Nee voor bron, ja voor Sink  |
| tabel | De naam van de tabel/weer gave. |Nee voor bron, ja voor Sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor nieuwe werk `schema` belasting `table` . | Nee voor bron, ja voor Sink |

**Hierbij**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die u kunt gebruiken om activiteiten te definiëren. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de SQL Managed Instance-bron en Sink.

### <a name="sql-managed-instance-as-a-source"></a>SQL-beheerd exemplaar als een bron

Als u gegevens wilt kopiëren van een SQL-beheerd exemplaar, worden de volgende eigenschappen ondersteund in de sectie bron van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SqlMISource**. | Ja |
| sqlReaderQuery |Deze eigenschap maakt gebruik van de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Deze eigenschap is de naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Deze para meters zijn voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-of waardeparen. De namen en de behuizing van de para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |Nee |
| isolationLevel | Hiermee geeft u het vergrendelings gedrag van de trans actie voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted** (standaard), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **snap shot**. Raadpleeg [dit document](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) voor meer informatie. | Nee |

**Houd rekening met de volgende punten:**

- Als **sqlReaderQuery** is opgegeven voor **SqlMISource**, voert de Kopieer activiteit deze query uit op basis van de bron van het SQL Managed instance om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven als voor de opgeslagen procedure para meters worden gebruikt.
- Als u de eigenschap **sqlReaderQuery** of **sqlReaderStoredProcedureName** niet opgeeft, worden de kolommen die in de sectie ' Structure ' van de JSON van de gegevensset zijn gedefinieerd, gebruikt om een query te maken. De query `select column1, column2 from mytable` wordt uitgevoerd op basis van het SQL Managed instance. Als de definitie van de gegevensset niet de structuur ' Structure ' bevat, worden alle kolommen geselecteerd in de tabel.

**Voor beeld: een SQL-query gebruiken**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Voor beeld: een opgeslagen procedure gebruiken**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlMISource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**De definitie van de opgeslagen procedure**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-managed-instance-as-a-sink"></a>SQL Managed instance as a Sink

> [!TIP]
> Meer informatie over het ondersteunde schrijf gedrag, configuraties en aanbevolen procedures voor het [laden van gegevens in een SQL Managed instance](#best-practice-for-loading-data-into-sql-managed-instance).

Als u gegevens wilt kopiëren naar een SQL Managed instance, worden de volgende eigenschappen ondersteund in het gedeelte Sink van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **SqlMISink**. | Ja |
| writeBatchSize |Het aantal rijen dat *per batch*in de SQL-tabel moet worden ingevoegd.<br/>Toegestane waarden zijn gehele getallen voor het aantal rijen. Standaard bepaalt Azure Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte.  |Nee |
| writeBatchTimeout |Met deze eigenschap geeft u de wacht tijd op waarna de batch INSERT-bewerking moet worden voltooid voordat er een time-out optreedt.<br/>Toegestane waarden zijn voor de time span. Een voor beeld is ' 00:30:00 '. Dit is 30 minuten. |Nee |
| preCopyScript |Met deze eigenschap geeft u een SQL-query op voor het uitvoeren van de Kopieer activiteit voordat u gegevens naar een SQL-beheerd exemplaar schrijft. Het wordt slechts één keer per Kopieer bewerking aangeroepen. U kunt deze eigenschap gebruiken om vooraf geladen gegevens op te schonen. |Nee |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die definieert hoe bron gegevens in een doel tabel worden toegepast. <br/>Deze opgeslagen procedure wordt *per batch aangeroepen*. Voor bewerkingen die slechts één keer worden uitgevoerd en niets te doen met bron gegevens, bijvoorbeeld verwijderen of afkappen, gebruikt u de `preCopyScript` eigenschap. | Nee |
| storedProcedureTableTypeParameterName |De parameter naam van het tabel type dat is opgegeven in de opgeslagen procedure.  |Nee |
| sqlWriterTableType |De naam van het tabel type dat moet worden gebruikt in de opgeslagen procedure. Met de Kopieer activiteit worden de gegevens in een tijdelijke tabel met dit tabel type beschikbaar gemaakt. Met de opgeslagen procedure code kunt u vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |Nee |
| storedProcedureParameters |Para meters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-en waardeparen. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. | Nee |
| tableOption | Hiermee wordt aangegeven of de Sink-tabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bron schema. Het automatisch maken van tabellen wordt niet ondersteund wanneer Sink de opgeslagen procedure opgeeft of een gefaseerde kopie is geconfigureerd in de Kopieer activiteit. Toegestane waarden zijn: `none` (standaard), `autoCreate` . |Nee |

**Voor beeld 1: gegevens toevoegen**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Voor beeld 2: een opgeslagen procedure aanroepen tijdens het kopiëren**

Meer informatie over [het aanroepen van een opgeslagen procedure vanuit een SQL mi-Sink](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Aanbevolen procedure voor het laden van gegevens in een SQL-beheerd exemplaar

Wanneer u gegevens naar een SQL Managed instance kopieert, hebt u mogelijk een ander schrijf gedrag nodig:

- [Toevoegen](#append-data): mijn bron gegevens hebben alleen nieuwe records.
- [Upsert](#upsert-data): mijn bron gegevens hebben zowel toevoegingen als updates.
- [Overschrijven](#overwrite-the-entire-table): Ik wil de hele dimensie tabel elke keer opnieuw laden.
- [Schrijven met aangepaste logica](#write-data-with-custom-logic): Ik heb extra verwerking nodig vóór de laatste invoeging in de doel tabel. 

Zie de betreffende secties voor informatie over het configureren van Azure Data Factory en aanbevolen procedures.

### <a name="append-data"></a>Gegevens toevoegen

Het toevoegen van gegevens is het standaard gedrag van de SQL Managed instance Sink-connector. Azure Data Factory voert een bulksgewijze invoer uit om uw tabel efficiënt te schrijven. U kunt de bron en Sink dienovereenkomstig configureren in de Kopieer activiteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens hebt om te kopiëren, gebruikt u de volgende methode om een upsert te maken: 

- Gebruik eerst een [tijdelijke tabel](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) om alle records bulksgewijs te laden met behulp van de Kopieer activiteit. Omdat bewerkingen voor tijdelijke tabellen niet worden geregistreerd, kunt u miljoenen records in enkele seconden laden.
- Voer een opgeslagen procedure-activiteit uit in Azure Data Factory om een instructie [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) of insert/update toe te passen. Gebruik de tijdelijke tabel als bron om alle updates uit te voeren of als één trans actie in te voegen. Op deze manier wordt het aantal Retouren en logboek bewerkingen gereduceerd. Aan het einde van de opgeslagen procedure-activiteit kan de tijdelijke tabel worden afgekapt zodat deze klaar is voor de volgende upsert-cyclus.

In Azure Data Factory kunt u bijvoorbeeld een pijp lijn maken met een **Kopieer activiteit** die is gekoppeld aan een **opgeslagen procedure activiteit**. De eerste kopie kopieert gegevens uit het bron archief naar een tijdelijke tabel, bijvoorbeeld **# #UpsertTempTable**, als de tabel naam in de gegevensset. Vervolgens roept de laatste een opgeslagen procedure aan om bron gegevens van de tijdelijke tabel samen te voegen in de doel tabel en de tijdelijke tabel op te schonen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definieer in uw data base een opgeslagen procedure met SAMENVOEG logica, zoals in het volgende voor beeld, dat verwijst naar de vorige opgeslagen procedure activiteit. Stel dat het doel de **marketing** tabel is met drie kolommen: **ProfileID**, **State**en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Optie 2:** U kunt er ook voor kiezen om [een opgeslagen procedure binnen een Kopieer activiteit](#invoke-a-stored-procedure-from-a-sql-sink)aan te roepen. Met deze benadering wordt elke rij in de bron tabel uitgevoerd in plaats van bulksgewijs invoegen als de standaard benadering in de Kopieer activiteit, wat niet van toepassing is op grootschalige upsert.

### <a name="overwrite-the-entire-table"></a>De volledige tabel overschrijven

U kunt de eigenschap **preCopyScript** in een Sink voor kopieer activiteiten configureren. In dit geval voert Azure Data Factory voor elke Kopieer activiteit die wordt uitgevoerd eerst het script uit. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de volledige tabel wilt overschrijven met de meest recente gegevens, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen voor het schrijven van gegevens met aangepaste logica zijn vergelijkbaar met die in de sectie [Upsert-gegevens](#upsert-data) . Wanneer u extra verwerking wilt Toep assen voordat de laatste invoeging van bron gegevens in de doel tabel, op grote schaal, een van de volgende twee dingen doen: 

- Laden naar een tijdelijke tabel en vervolgens een opgeslagen procedure aanroepen.
- Een opgeslagen procedure aanroepen tijdens het kopiëren.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Een opgeslagen procedure aanroepen vanuit een SQL-Sink

Wanneer u gegevens naar een SQL Managed instance kopieert, kunt u ook een door de gebruiker opgegeven opgeslagen procedure met aanvullende para meters configureren en aanroepen. De functie opgeslagen procedure maakt gebruik van [para meters met tabel waarden](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Als u een opgeslagen procedure aanroept, wordt de gegevensrij per rij verwerkt in plaats van met behulp van een bulk bewerking. dit wordt niet aanbevolen voor grootschalige kopieën. Meer informatie over [Best practices voor het laden van gegevens in een SQL Managed instance](#best-practice-for-loading-data-into-sql-managed-instance).

U kunt een opgeslagen procedure gebruiken wanneer ingebouwde Kopieer mechanismen het doel niet behouden. Een voor beeld hiervan is wanneer u een extra verwerking wilt Toep assen vóór het uiteindelijke invoegen van bron gegevens in de doel tabel. Sommige extra verwerkings voorbeelden zijn wanneer u kolommen wilt samen voegen, aanvullende waarden wilt opzoeken en gegevens in meer dan één tabel wilt invoegen.

In het volgende voor beeld ziet u hoe u een opgeslagen procedure gebruikt om een upsert te maken in een tabel in de SQL Server-Data Base. Stel dat de invoer gegevens en de provinciale **marketing** tabel drie kolommen hebben: **ProfileID**, **State**en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** en pas deze alleen toe voor een specifieke categorie met de naam ProductA.

1. Definieer in uw data base het tabel type met de naam **sqlWriterTableType**. Het schema van het tabel type is hetzelfde als het schema dat wordt geretourneerd door de invoer gegevens.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Definieer in uw data base de opgeslagen procedure met de naam **sqlWriterStoredProcedureName**. De invoer gegevens van de opgegeven bron worden verwerkt en samen voegingen in de uitvoer tabel. De parameter naam van het tabel type in de opgeslagen procedure is hetzelfde als **TableName** gedefinieerd in de gegevensset.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. In Azure Data Factory definieert u de sectie **SQL mi Sink** in de Kopieer activiteit als volgt:

    ```json
    "sink": {
        "type": "SqlMISink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-managed-instance"></a>Toewijzing van gegevens type voor door SQL beheerd exemplaar

Wanneer gegevens worden gekopieerd naar en van een SQL Managed instance, worden de volgende toewijzingen gebruikt vanuit gegevens typen van SQL Managed instance om tussenliggende gegevens typen te Azure Data Factory. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit wordt toegewezen vanuit het bron schema en het gegevens type naar de sink.

| Gegevens type voor SQL-beheerde exemplaren | Azure Data Factory tussentijds gegevens type |
|:--- |:--- |
| bigint |Int64 |
| binair |Byte [] |
| bit |Boolean |
| char |Teken reeks, char [] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Date time offset |Date time offset |
| Decimal |Decimal |
| FILESTREAM-kenmerk (varbinary (max)) |Byte [] |
| Float |Double |
| image |Byte [] |
| int |Int32 |
| financieel |Decimal |
| nchar |Teken reeks, char [] |
| ntext |Teken reeks, char [] |
| numeriek |Decimal |
| nvarchar |Teken reeks, char [] |
| werkelijk |Enkel |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| tekst |Teken reeks, char [] |
| tijd |TimeSpan |
| tijdstempel |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Teken reeks, char [] |
| xml |Xml |

>[!NOTE]
> Voor gegevens typen die worden toegewezen aan het type van de tijdelijke decimalen, Azure Data Factory op dit moment de precisie Maxi maal 28 ondersteunen. Als u gegevens hebt die een grotere nauw keurigheid dan 28 vereisen, kunt u overwegen om te converteren naar een teken reeks in een SQL-query.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
