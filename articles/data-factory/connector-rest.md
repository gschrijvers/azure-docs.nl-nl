---
title: Gegevens kopiëren uit een REST-bron met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit een Cloud of een on-premises REST-bron naar ondersteunde Sink-gegevens opslag met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 2657f1998e3ca908bc52166154ac3353e1e5a66b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415034"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Gegevens kopiëren van een REST-eind punt met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een REST-eind punt te kopiëren. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

Het verschil tussen deze REST-connector, de [http-connector](connector-http.md) en de [Web Table-connector](connector-web-table.md) zijn:

- **Rest-connector** ondersteunt het kopiëren van gegevens uit rest-api's; 
- **Http-connector** is algemeen om gegevens op te halen uit een http-eind punt, bijvoorbeeld om het bestand te downloaden. Voordat deze REST-connector beschikbaar komt, kunt u gebruikmaken van de HTTP-connector om gegevens te kopiëren van de REST-API, die wordt ondersteund, maar minder functioneel is vergeleken met de REST-connector.
- Met **Web Table connector** wordt tabel inhoud geëxtraheerd van een HTML-webpagina.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit een REST-bron kopiëren naar elk ondersteund Sink-gegevens archief. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Deze algemene REST-connector ondersteunt met name:

- Gegevens ophalen van een REST-eind punt met behulp van de **Get** -of **post** -methoden.
- Gegevens ophalen met behulp van een van de volgende authenticaties: **anoniem**, **basis**, **Aad-Service-Principal**en **beheerde identiteiten voor Azure-resources**.
- **[Paginering](#pagination-support)** in de rest api's.
- De REST JSON-respons kopiëren [als-is](#export-json-response-as-is) of parseren met behulp van [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping). Alleen de nettolading van een reactie in **JSON** wordt ondersteund.

> [!TIP]
> Als u een aanvraag voor het ophalen van gegevens wilt testen voordat u de REST-connector in Data Factory configureert, kunt u meer informatie vinden over de API-specificatie voor vereisten voor koptekst en hoofd tekst. U kunt de hulpprogram ma's zoals postman of een webbrowser gebruiken om te valideren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor de REST-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de REST-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **RestService**. | Ja |
| url | De basis-URL van de REST-service. | Ja |
| enableServerCertificateValidation | Hiermee wordt aangegeven of het TLS/SSL-certificaat aan de server zijde moet worden gevalideerd wanneer er verbinding wordt gemaakt met het eind punt. | Nee<br /> (de standaard waarde is **True**) |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met de REST-service. Toegestane waarden zijn **anoniem**, **Basic**, **AadServicePrincipal** en **ManagedServiceIdentity**. Raadpleeg de bijbehorende secties hieronder voor meer eigenschappen en voor beelden. | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

### <a name="use-basic-authentication"></a>Basis verificatie gebruiken

Stel de eigenschap **authenticationType** in op **Basic**. Naast de algemene eigenschappen die in de voor gaande sectie worden beschreven, geeft u de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | De gebruikers naam die moet worden gebruikt voor toegang tot het REST-eind punt. | Ja |
| wachtwoord | Het wacht woord voor de gebruiker (de waarde van de **gebruikers naam** ). Markeer dit veld als **SecureString** -type om het veilig op te slaan in Data Factory. U kunt ook [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Voorbeeld**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-aad-service-principal-authentication"></a>Principal-verificatie van AAD-service gebruiken

Stel de eigenschap **authenticationType** in op **AadServicePrincipal**. Naast de algemene eigenschappen die in de voor gaande sectie worden beschreven, geeft u de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| servicePrincipalId | Geef de client-ID van de Azure Active Directory toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de Azure Active Directory toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja |
| aadResourceId | Geef de AAD-resource op die u aanvraagt voor `https://management.core.windows.net`autorisatie, bijvoorbeeld.| Ja |

**Voorbeeld**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Beheerde identiteiten gebruiken voor Azure-bronnen verificatie

Stel de eigenschap **authenticationType** in op **ManagedServiceIdentity**. Naast de algemene eigenschappen die in de voor gaande sectie worden beschreven, geeft u de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| aadResourceId | Geef de AAD-resource op die u aanvraagt voor `https://management.core.windows.net`autorisatie, bijvoorbeeld.| Ja |

**Voorbeeld**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

In deze sectie vindt u een lijst met eigenschappen die de REST-gegevensset ondersteunt. 

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Als u gegevens wilt kopiëren uit REST, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **RestResource**. | Ja |
| relativeUrl | Een relatieve URL naar de resource die de gegevens bevat. Als deze eigenschap niet is opgegeven, wordt alleen de URL gebruikt die in de definitie van de gekoppelde service is opgegeven. De HTTP-connector kopieert gegevens van de gecombineerde URL `[URL specified in linked service]/[relative URL specified in dataset]`:. | Nee |

`requestMethod`Als u instelt `additionalHeaders`,, `requestBody` en `paginationRules` in DataSet, wordt deze nog steeds ondersteund als-is, terwijl u het nieuwe model in activiteit bron gaat gebruiken.

**Hierbij**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

In deze sectie vindt u een lijst met eigenschappen die door de REST-bron worden ondersteund.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="rest-as-source"></a>REST als bron

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **RestSource**. | Ja |
| requestMethod | De HTTP-methode. Toegestane waarden zijn **Get** (standaard) en **post**. | Nee |
| additionalHeaders | Aanvullende HTTP-aanvraag headers. | Nee |
| requestBody | De hoofd tekst van de HTTP-aanvraag. | Nee |
| paginationRules | De paginerings regels voor het opstellen van volgende pagina-aanvragen. Raadpleeg de sectie [ondersteuning voor paginering](#pagination-support) voor meer informatie. | Nee |
| httpRequestTimeout | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Deze waarde is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. De standaard waarde is **00:01:40**.  | Nee |
| requestInterval | De tijd die moet worden gewacht voordat de aanvraag wordt verzonden naar de volgende pagina. De standaard waarde is **00:00:01** |  Nee |

>[!NOTE]
>De header ' Accept ' die is opgegeven in `additionalHeaders`, wordt door de rest-connector genegeerd. Als REST-connector alleen ondersteuning biedt voor een reactie in JSON, wordt er automatisch `Accept: application/json`een header van gegenereerd.

**Voor beeld 1: de methode Get gebruiken met paginering**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Voor beeld 2: de post-methode gebruiken**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Paginerings ondersteuning

Normaal gesp roken beperkt REST API de grootte van de reactie lading van een enkele aanvraag onder een redelijk nummer; tijdens het retour neren van een grote hoeveelheid gegevens wordt het resultaat in meerdere pagina's gesplitst en moeten aanroepers opeenvolgende aanvragen verzenden om de volgende pagina van het resultaat op te halen. Normaal gesp roken is de aanvraag voor één pagina dynamisch en samengesteld op basis van de informatie die wordt geretourneerd door het antwoord op de vorige pagina.

Deze algemene REST-connector ondersteunt de volgende paginerings patronen: 

* Absolute of relatieve URL van de volgende aanvraag waarde van eigenschap in huidige antwoord tekst
* Absolute of relatieve URL van de volgende aanvraag = koptekst waarde in huidige antwoord headers
* Query parameter van volgende aanvraag = eigenschaps waarde in huidige antwoord tekst
* Query parameter = koptekst waarde van volgende aanvraag in huidige antwoord headers
* Header van volgende aanvraag = waarde van eigenschap in huidige antwoord tekst
* Kop van volgende aanvraag = waarde van header in huidige antwoord headers

**Paginerings regels** worden gedefinieerd als een woorden lijst in een gegevensset die een of meer hoofdletter gevoelige sleutel-waardeparen bevat. De configuratie wordt gebruikt om de aanvraag te genereren vanaf de tweede pagina. De connector stopt met herhalen wanneer de HTTP-status code 204 (geen inhoud) wordt opgehaald, of een van de JSONPath-expressies in paginationRules retourneert null.

**Ondersteunde sleutels** in de paginerings regels:

| Sleutel | Beschrijving |
|:--- |:--- |
| AbsoluteUrl | Hiermee wordt de URL aangegeven voor het uitgeven van de volgende aanvraag. Dit kan **absolute URL of een relatieve URL**zijn. |
| QueryParameters. *request_query_parameter* OF QueryParameters [' request_query_parameter '] | ' request_query_parameter ' is door de gebruiker gedefinieerd en verwijst naar één query parameter naam in de volgende HTTP-aanvraag-URL. |
| Koppen. *request_header* OF headers [' request_header '] | ' request_header ' is door de gebruiker gedefinieerd en verwijst naar één header naam in de volgende HTTP-aanvraag. |

**Ondersteunde waarden** in de paginerings regels:

| Waarde | Beschrijving |
|:--- |:--- |
| Koppen. *response_header* OF headers [' response_header '] | "response_header" is door de gebruiker gedefinieerd en verwijst naar één header naam in het huidige HTTP-antwoord, waarvan de waarde wordt gebruikt om de volgende aanvraag uit te geven. |
| Een JSONPath-expressie die begint met ' $ ' (die de hoofdmap van de antwoord tekst vertegenwoordigt) | De antwoord tekst mag slechts één JSON-object bevatten. De JSONPath-expressie moet één primitieve waarde Retour neren, die wordt gebruikt voor het uitgeven van de volgende aanvraag. |

**Hierbij**

Facebook Graph API retourneert een antwoord in de volgende structuur, in dat geval de URL van de volgende pagina wordt weer gegeven in ***paginering. volgende***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

De bijbehorende bron configuratie van de REST Copy- `paginationRules` activiteit met name de is als volgt:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>OAuth gebruiken
In deze sectie wordt beschreven hoe u een oplossings sjabloon gebruikt om gegevens van de REST-connector naar Azure Data Lake Storage in JSON-indeling te kopiëren met behulp van OAuth. 

### <a name="about-the-solution-template"></a>Over de oplossings sjabloon

De sjabloon bevat twee activiteiten:
- **Webactiviteit haalt** het Bearer-token op en geeft dit vervolgens door aan de volgende Kopieer activiteit als autorisatie.
- Met de **Kopieer** activiteit worden gegevens van REST naar Azure data Lake Storage gekopieerd.

De sjabloon definieert twee para meters:
- **SinkContainer** is het pad naar de hoofdmap waarnaar de gegevens worden gekopieerd in uw Azure data Lake Storage. 
- **SinkDirectory** is het mappad in de hoofdmap waarnaar de gegevens worden gekopieerd in uw Azure data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Ga naar de **kopie van rest of http met OAuth** -sjabloon. Een nieuwe verbinding maken voor de bron verbinding. 
    ![Nieuwe verbindingen maken](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Hieronder vindt u belang rijke stappen voor nieuwe instellingen voor de gekoppelde service (REST):
    
     1. Geef onder **basis-URL**de URL-para meter voor uw eigen bron rest-service op. 
     2. Kies *anoniem*bij **verificatie type**.
        ![Nieuwe REST-verbinding](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Maak een nieuwe verbinding voor de doel verbinding.  
    ![Nieuwe Gen2-verbinding](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Selecteer **deze sjabloon gebruiken**.
    ![Deze sjabloon gebruiken](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. U ziet de pijp lijn die u hebt gemaakt, zoals wordt weer gegeven ![in het volgende voor beeld: pijp lijn](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Selecteer **Web** webactiviteit. Geef in **instellingen**de corresponderende **URL**, **methode**, **headers**en **hoofd tekst** op om een OAUTH Bearer-token op te halen uit de API voor aanmelden van de service waarvan u gegevens wilt kopiëren. In de tijdelijke aanduiding in de sjabloon wordt een voor beeld van een Azure Active Directory (AAD) OAuth gedemonstreerd. Opmerking AAD-verificatie wordt systeem eigen ondersteund door REST connector. Dit is slechts een voor beeld van een OAuth-stroom. 

    | Eigenschap | Beschrijving |
    |:--- |:--- |:--- |
    | URL |Geef de URL op waarvoor het OAuth Bearer-token moet worden opgehaald. bijvoorbeeld in het voor beeld is dithttps://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Methode | De HTTP-methode. Toegestane waarden zijn **post** en **Get**. | 
    | Headers | De header is door de gebruiker gedefinieerd, die verwijst naar één header naam in de HTTP-aanvraag. | 
    | Hoofdtekst | De hoofd tekst van de HTTP-aanvraag. | 

    ![Pijplijn](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Selecteer in activiteit **gegevens kopiëren** het tabblad *bron* , u kunt zien dat het Bearer-token (access_token) dat is opgehaald uit de vorige stap, wordt door gegeven om de activiteit gegevens te kopiëren als **verificatie** onder aanvullende headers. Bevestig de instellingen voor de volgende eigenschappen voordat u begint met het uitvoeren van een pijplijn.

    | Eigenschap | Beschrijving |
    |:--- |:--- |:--- | 
    | Aanvraag methode | De HTTP-methode. Toegestane waarden zijn **Get** (standaard) en **post**. | 
    | Aanvullende kopteksten | Aanvullende HTTP-aanvraag headers.| 

   ![Bron verificatie kopiëren](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Selecteer **debug**, voer de **para meters**in en selecteer **volt ooien**.
   ![Pijplijn uitvoering](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Wanneer de uitvoering van de pijp lijn is voltooid, ziet u het resultaat dat lijkt op het volgende voor ![beeld: resultaat van pijplijn uitvoering](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Klik op het pictogram uitvoer van webactiviteit in de kolom **acties** , maar u ziet de access_token die door de service is geretourneerd.

   ![Token uitvoer](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Klik op het pictogram invoer in CopyActivity in **acties** kolom, maar u ziet dat de access_token opgehaald door webactiviteit wordt door gegeven aan CopyActivity voor verificatie. 

    ![Token invoer](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Als u wilt voor komen dat token wordt geregistreerd als tekst zonder opmaak, schakelt u ' beveiligde uitvoer ' in bij webactiviteit en ' beveiligde invoer ' in Kopieer activiteit.


## <a name="export-json-response-as-is"></a>JSON-antwoord exporteren als-is

U kunt deze REST-connector gebruiken voor het exporteren van REST API JSON-antwoord naar verschillende archieven op basis van bestanden. Om een dergelijke schema-neutraal kopie te krijgen, slaat u de sectie ' Structure ' (ook wel *schema*genoemd) in de gegevensset en schema toewijzing in de Kopieer activiteit over.

## <a name="schema-mapping"></a>Schema toewijzing

Zie [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping)voor informatie over het kopiëren van het rest-eind punt naar tabellaire sink.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.
