---
title: Gegevens kopiëren van en naar de Sales Force-Service-Cloud
description: Informatie over het kopiëren van gegevens uit Sales Force Service-Cloud naar ondersteunde Sink-gegevens archieven of van ondersteunde brongegevens archieven naar Sales Force Service-Cloud met behulp van een Kopieer activiteit in een data factory-pijp lijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ec2aa5b1492534908adb55544623110242717609
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416678"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar de Sales Force-Service-Cloud met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar de Sales Force-Service-Cloud te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Cloud connector van de Sales Force-service wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)

U kunt gegevens uit Sales Force Service-Cloud kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens uit een ondersteund brongegevens archief kopiëren naar de Sales Force-Service-Cloud. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Deze Sales Force-Service Cloud connector ondersteunt het volgende:

- Edities van Sales Force Developer, Professional, Enter prise of Unlimited.
- Gegevens kopiëren van en naar Sales Force-productie, sandbox en aangepast domein.

De Sales Force-connector is gebaseerd op de REST/bulk API Sales Force. De connector gebruikt standaard [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) om gegevens uit Sales Force te kopiëren en maakt gebruik van [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) om gegevens te kopiëren naar Sales Force. U kunt ook expliciet de API-versie instellen die wordt gebruikt om gegevens te [ `apiVersion` ](#linked-service-properties) lezen/schrijven via de eigenschap in de gekoppelde service.

## <a name="prerequisites"></a>Vereisten

API-machtiging moet zijn ingeschakeld in Sales Force. Zie [Enable API Access in Sales Force by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Engelstalig) voor meer informatie.

## <a name="salesforce-request-limits"></a>Limieten voor Sales Force-aanvragen

Sales Force heeft limieten voor zowel het totale aantal API-aanvragen als gelijktijdige API-aanvragen. Houd rekening met de volgende punten:

- Als het aantal gelijktijdige aanvragen de limiet overschrijdt, wordt er een beperking weer gegeven en ziet u wille keurige fouten.
- Als het totale aantal aanvragen de limiet overschrijdt, wordt het Sales Force-account 24 uur geblokkeerd.

Het fout bericht ' REQUEST_LIMIT_EXCEEDED ' kan in beide scenario's ook worden weer gegeven. Zie de sectie ' API-aanvraag limieten ' in [Sales Force-ontwikkelaars limieten](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)voor meer informatie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Sales Force-Service-Cloud connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service Sales Force.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op **SalesforceServiceCloud**. |Ja |
| environmentUrl | Geef de URL van het Sales Force Service-Cloud exemplaar op. <br> -Standaard is `"https://login.salesforce.com"`. <br> -Als u gegevens wilt kopiëren uit de `"https://test.salesforce.com"`sandbox, geeft u op. <br> -Als u gegevens wilt kopiëren uit een aangepast domein, geeft u `"https://[domain].my.salesforce.com"`bijvoorbeeld op. |Nee |
| gebruikersnaam |Geef een gebruikers naam op voor het gebruikers account. |Ja |
| wachtwoord |Geef een wacht woord op voor het gebruikers account.<br/><br/>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| Security |Geef een beveiligings token op voor het gebruikers account. <br/><br/>Zie [beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligings tokens in het algemeen. Het beveiligings token kan alleen worden overgeslagen als u het IP-adres van de Integration Runtime toevoegt aan de [lijst met vertrouwde IP-adressen](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) in Sales Force. Als u Azure IR gebruikt, raadpleegt u [Azure Integration runtime IP-adressen](azure-integration-runtime-ip-addresses.md).<br/><br/>Zie [een beveiligings Token ophalen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)voor instructies over het ophalen en opnieuw instellen van een beveiligings token. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Nee |
| apiVersion | Geef de Sales Force-API-versie op die u wilt gebruiken `48.0`, bijvoorbeeld. De connector gebruikt standaard [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) om gegevens uit Sales Force te kopiëren en maakt gebruik van [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) om gegevens te kopiëren naar Sales Force. | Nee |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee voor bron, ja voor Sink als de gekoppelde bron service geen Integration runtime heeft |

>[!IMPORTANT]
>Wanneer u gegevens naar de Sales Force-service-cloud kopieert, kan de standaard Azure Integration Runtime niet worden gebruikt voor het uitvoeren van een kopie. Met andere woorden, als uw gekoppelde bron service geen opgegeven Integration runtime heeft, maakt u expliciet [een Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) met een locatie in de buurt van uw Sales Force-Service-Cloud exemplaar. Koppel de gekoppelde service van de Sales Force-service aan de Cloud, zoals in het volgende voor beeld.

**Voor beeld: referenties opslaan in Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: referenties opslaan in Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Cloud gegevensset van de Sales Force-service.

De volgende eigenschappen worden ondersteund om gegevens van en naar de Sales Force-Service-Cloud te kopiëren.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **SalesforceServiceCloudObject**.  | Ja |
| objectApiName | De naam van het Sales Force-object waaruit gegevens worden opgehaald. | Nee voor bron, ja voor Sink |

> [!IMPORTANT]
> Het deel ' __c ' van de **API-naam** is vereist voor een aangepast object.

![Naam van Data Factory Sales Force-API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Hierbij**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **RelationalTable**. | Ja |
| tableName | De naam van de tabel in de Sales Force-Service-Cloud. | Nee (als ' query ' in de activiteit bron is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Sales Force-Service-Cloud bron en-Sink.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Cloud van de Sales Force-service als bron type

Als u gegevens wilt kopiëren uit de Sales Force-Service-Cloud, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SalesforceServiceCloudSource**. | Ja |
| query |Gebruik de aangepaste query om gegevens te lezen. U kunt de [Sales Force object query language-query (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) of SQL-92-query gebruiken. Meer tips vindt u in de sectie [query tips](#query-tips) . Als er geen query is opgegeven, worden alle gegevens van het Cloud object Sales Force-service dat is opgegeven in ' objectApiName ' in dataset opgehaald. | Nee (als "objectApiName" in de gegevensset is opgegeven) |
| readBehavior | Hiermee wordt aangegeven of een query moet worden doorzocht op de bestaande records, of dat alle records moeten worden opgevraagd met een query. Als deze niet wordt opgegeven, is het standaard gedrag het voormalige. <br>Toegestane waarden: **query** (standaard), **queryAll**.  | Nee |

> [!IMPORTANT]
> Het deel ' __c ' van de **API-naam** is vereist voor een aangepast object.

![Lijst met namen van Data Factory Sales Force-API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Hierbij**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Cloud van de Sales Force-service als een Sink-type

Als u gegevens wilt kopiëren naar de Sales Force-Service-Cloud, worden de volgende eigenschappen ondersteund in het gedeelte **sink** voor kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **SalesforceServiceCloudSink**. | Ja |
| writeBehavior | Het schrijf gedrag voor de bewerking.<br/>Toegestane waarden zijn **Insert** en **Upsert**. | Nee (standaard waarde is invoegen) |
| externalIdFieldName | De naam van het veld externe ID voor de bewerking upsert. Het opgegeven veld moet worden gedefinieerd als een externe ID-veld in het Cloud object van de Sales Force-service. De waarde mag geen NULL-waarden bevatten in de bijbehorende invoer gegevens. | Ja voor ' Upsert ' |
| writeBatchSize | Het aantal rijen van gegevens dat is geschreven naar de Sales Force-Service-Cloud in elke batch. | Nee (de standaard waarde is 5.000) |
| ignoreNullValues | Hiermee wordt aangegeven of NULL-waarden moeten worden genegeerd uit de invoer gegevens tijdens een schrijf bewerking.<br/>Toegestane waarden zijn **waar** en **Onwaar**.<br>- **Waar**: laat de gegevens in het doel object ongewijzigd wanneer u een upsert of update-bewerking doet. Voeg een gedefinieerde standaard waarde in wanneer u een INSERT-bewerking wilt uitvoeren.<br/>- **Onwaar**: werk de gegevens in het doel object bij naar NULL wanneer u een upsert of update-bewerking uitgevoerd. Voeg een NULL-waarde toe wanneer u een INSERT-bewerking uitgevoerd. | Nee (standaard waarde is False) |

**Hierbij**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Query tips

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Gegevens ophalen uit een Sales Force-Service-Cloud rapport

U kunt gegevens ophalen uit Sales Force-Service-Cloud rapporten door een `{call "<report name>"}`query op te geven als. Een voorbeeld is `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Verwijderde records uit de Prullenbak van de Sales Force-service ophalen

Als u een query wilt uitvoeren op de voorlopig verwijderde records uit de Prullenbak van de Sales Force `readBehavior` - `queryAll`service, kunt u opgeven als. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Verschil tussen de SOQL-en SQL-query syntaxis

Bij het kopiëren van gegevens uit de Sales Force-Service-Cloud kunt u de SOQL-query of SQL-query gebruiken. Houd er rekening mee dat deze twee verschillende syntaxis-en functionaliteits ondersteuning hebben, maar niet kunnen combi neren. U wordt aangeraden de SOQL-query te gebruiken die standaard wordt ondersteund door Sales Force Service Cloud. De volgende tabel bevat de belangrijkste verschillen:

| Syntaxis | SOQL-modus | SQL-modus |
|:--- |:--- |:--- |
| Kolom selectie | U moet de velden opsommen die moeten worden gekopieerd in de query, bijvoorbeeld`SELECT field1, filed2 FROM objectname` | `SELECT *`wordt ondersteund naast kolom selectie. |
| Aanhalings tekens | Gearchiveerde/object namen kunnen niet worden opgenomen in een aanhalings teken. | De namen van velden/objecten kunnen worden opgenomen in een aanhalings teken, bijvoorbeeld`SELECT "id" FROM "Account"` |
| Datum notatie |  Raadpleeg [hier](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) de details en voor beelden in de volgende sectie. | Raadpleeg [hier](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) de details en voor beelden in de volgende sectie. |
| Booleaanse waarden | Wordt weer `False` gegeven `True`als en, `SELECT … WHERE IsDeleted=True`bijvoorbeeld | Wordt weer gegeven als 0 of 1, `SELECT … WHERE IsDeleted=1`bijvoorbeeld. |
| Kolom naam wijzigen | Wordt niet ondersteund. | Ondersteund, bijvoorbeeld: `SELECT a AS b FROM …`. |
| Relatie | Ondersteund, bijvoorbeeld `Account_vod__r.nvs_Country__c`. | Wordt niet ondersteund. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Gegevens ophalen met behulp van een WHERE-component in de datum/tijd-kolom

Wanneer u de SOQL of SQL-query opgeeft, moet u rekening best Eden aan het verschil in datum-en tijd notatie. Bijvoorbeeld:

* Voor **beeld van SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL**-voor beeld:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Fout van MALFORMED_QUERY: afgekapt

Als u de fout melding ' MALFORMED_QUERY: afgekapt ' krijgt, is het normaal dat de kolom Type JunctionIdList in data en Sales Force is beperkt tot het ondersteunen van dergelijke gegevens met een groot aantal rijen. Als u het probleem wilt oplossen, probeert u de kolom JunctionIdList uit te sluiten of het aantal te kopiëren rijen te beperken (u kunt partitioneren op meerdere uitvoeringen van de Kopieer activiteit).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Toewijzing van gegevens type voor de Sales Force-Service-Cloud

Wanneer u gegevens van de Sales Force-service-cloud kopieert, worden de volgende toewijzingen gebruikt vanuit gegevens typen van de Sales Force-service voor het Data Factory van tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

| Cloud gegevens type Sales Force-service | Data Factory tussentijds gegevens type |
|:--- |:--- |
| Automatisch nummer |Tekenreeks |
| Checkbox |Booleaans |
| Valuta |Decimal |
| Date |DateTime |
| Datum/tijd |DateTime |
| E-mail |Tekenreeks |
| Id |Tekenreeks |
| Opzoek relatie |Tekenreeks |
| Meervoudige selectie lijst |Tekenreeks |
| Aantal |Decimal |
| Percentage |Decimal |
| Telefoon |Tekenreeks |
| Picklist |Tekenreeks |
| Tekst |Tekenreeks |
| Tekst gebied |Tekenreeks |
| Tekst gebied (lang) |Tekenreeks |
| Tekst gebied (uitgebreid) |Tekenreeks |
| Tekst (versleuteld) |Tekenreeks |
| URL |Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
