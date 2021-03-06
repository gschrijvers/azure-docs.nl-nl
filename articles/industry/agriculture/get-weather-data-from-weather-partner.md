---
title: Weer gegevens ophalen van weer partners
description: In dit artikel wordt beschreven hoe u weer gegevens van partners kunt ophalen.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266160"
---
# <a name="get-weather-data-from-weather-partners"></a>Weer gegevens ophalen van weer partners

Met Azure FarmBeats kunt u weer gegevens ophalen van uw weer gegevens provider (s) met behulp van een Connector-Framework op basis van docker. Met behulp van dit framework implementeren weer gegevens providers een docker die kan worden geïntegreerd met FarmBeats. Momenteel worden de volgende gegevens providers ondersteund:

[NOAA gegevens uit Azure open gegevens sets](https://azure.microsoft.com/services/open-datasets/)

De weer gegevens kunnen worden gebruikt voor het genereren van met actie bruikbare inzichten en het bouwen van AI/ML-modellen op FarmBeats.

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat u FarmBeats hebt geïnstalleerd om weer gegevens op te halen. De **weers integratie wordt ondersteund in versie 1.2.11 of hoger**. Zie [install FarmBeats](https://aka.ms/farmbeatsinstalldocumentation)om Azure FarmBeats te installeren.

## <a name="enable-weather-integration-with-farmbeats"></a>Weers integratie met FarmBeats inschakelen

Volg de onderstaande stappen om te beginnen met het ophalen van weer gegevens op uw FarmBeats data hub:

1. Ga naar uw FarmBeats data hub Swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Ga naar/partner API en maak een POST-aanvraag met de volgende invoer lading:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Als u bijvoorbeeld weer gegevens wilt ophalen uit NOAA door Azure open gegevens sets, gebruikt u de onderstaande nettolading. U kunt de naam en beschrijving aanpassen volgens uw voor keur.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Zie voor meer informatie over het partner object [bijlage](get-weather-data-from-weather-partner.md#appendix)

   In de voor gaande stap worden de resources ingericht zodat docker kan worden uitgevoerd in de FarmBeats omgeving van de klant.  

   Het duurt ongeveer 10-15 minuten om de bovenstaande bronnen in te richten.

3. Controleer de status van het/partner-object dat u hebt gemaakt in stap 2. Als u dit wilt doen, maakt u een GET-aanvraag op de/partner-API en controleert u op de **status** van het partner object. Zodra FarmBeats de partner met succes heeft ingericht, wordt de status ingesteld op **actief**.

4. Navigeer naar/JobType-API en maak een GET-aanvraag op dezelfde. Controleer op de weers taken die zijn gemaakt als onderdeel van het proces voor het toevoegen van partners in stap 2. Het veld **pipelineName** in de weers-taken heeft de volgende indeling: ' partner-name_partner-type_job-name '.

5. Uw FarmBeats-instantie heeft nu een actieve weer gegevens partner en u kunt taken uitvoeren om weer gegevens voor een bepaalde locatie (breedte graad/lengte graad) en een datum bereik op te vragen. De taak type (s) bevatten details over welke para meters zijn vereist voor het uitvoeren van weer taken.

   Voor NOAA gegevens uit Azure open gegevens sets worden bijvoorbeeld de volgende taak type (s) gemaakt:

   - get_weather_data (Get ISD/historische weer gegevens)
   - get_weather_forecast_data (Get GFS/Forecast weer gegeven gegevens)

6. Noteer de **id** en de para meters van de taak type (s).

7. Ga naar/Jobs API en maak een POST-aanvraag op/Jobs met de volgende invoer lading:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Als u bijvoorbeeld **get_weather_data**wilt uitvoeren, gebruikt u de volgende Payload:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. In de voor gaande stap worden de weer taken uitgevoerd zoals gedefinieerd in de partner-docker en weer gegevens opnemen in FarmBeats. U kunt de status van de taak controleren door een GET-aanvraag op/jobs te maken en te zoeken naar **currentState** in het antwoord. Zodra het proces is voltooid, wordt de currentState ingesteld op **geslaagd**.

## <a name="query-ingested-weather-data"></a>Query's opgenomen weer gegeven gegevens

Nadat de weers taken zijn voltooid, kunt u een query uitvoeren op opgenomen weer gegevens om modellen of inzichten te ontwikkelen. Er zijn twee manieren om gegevens weer te geven en op te vragen uit FarmBeats:

- API en
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Query's uitvoeren met REST API

Volg de onderstaande stappen om de weers gegevens op te vragen met behulp van FarmBeats REST API:

1. Ga in uw FarmBeats data hub Swaggerhttps://yourdatahub.azurewebsites.net/swagger)(naar/WeatherDataLocation API en maak een GET-aanvraag. Het antwoord heeft een of meer/WeatherDataLocation-objecten gemaakt voor de locatie (breedte graad/lengte graad) die is opgegeven als onderdeel van de taak uitvoering. Noteer de **id** en de **weatherDataModelId** van de object (en).

2. Maak een GET/{id} op de/WeatherDataModel-API voor de **weatherDataModelId** zoals vermeld in stap 1. Het ' weer gegeven gegevens model ' bevat alle meta gegevens en Details over de opgenomen weers gegevens. Voor beeld: de **weers meting** in het object **weer gegeven gegevens model** bevat details over welke informatie wordt ondersteund en in welke typen en eenheden. Bijvoorbeeld:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Noteer de reactie van de aanroep GET/{id} voor het weer gegevens model.

3. Navigeer naar **telemetrie** -API en maak een post-aanvraag met de volgende invoer lading:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Het antwoord met weer gegevens dat voor het opgegeven tijds bereik beschikbaar is, ziet er als volgt uit:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

In het vorige voor beeld bevat het antwoord gegevens voor twee tijds tempels, samen met de naam van de meting (' Tempe ratuur ') en waarden van de gerapporteerde weers gegevens in de twee tijds tempels. U moet verwijzen naar het bijbehorende weer gegevens model (zoals beschreven in stap 2 hierboven) om het type en de eenheid van de gerapporteerde waarden te interpreteren.

### <a name="query-using-azure-time-series-insights-tsi"></a>Query's uitvoeren met behulp van Azure Time Series Insights (TSI)

FarmBeats maakt gebruik van [Azure time series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) om gegevens op IOT-schaal te opnemen, op te slaan, op te vragen en te visualiseren: gegevens die zeer worden betrouwd en geoptimaliseerd voor tijd reeksen.

Weer gegevens worden ontvangen op een EventHub en vervolgens gepusht naar een TSI-omgeving binnen FarmBeats-resource groep. Gegevens kunnen vervolgens rechtstreeks vanuit de TSI worden opgevraagd. Raadpleeg de [TSI-documentatie](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)voor meer informatie.

Volg de stappen voor het visualiseren van gegevens op TSI:

1. Ga naar **Azure Portal** > **FarmBeats DataHub resource groep** > Selecteer **Time Series Insights** Environment (TSI-xxxx) > **beleid voor gegevens toegang**. Voeg een gebruiker toe met toegang voor lezer of Inzender.

2. Ga naar de pagina **overzicht** van **Time Series Insights** omgeving (TSI-xxxx) en selecteer de **URL van Time Series Insights Explorer**. U kunt nu de opgenomen weers gegevens visualiseren.

Naast het opslaan, doorzoeken en visualisatie van weer gegevens, biedt de TSI ook integratie met een Power BI dash board. Zie [visualiseren van gegevens van Time Series Insights in Power bi](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)voor meer informatie.

## <a name="appendix"></a>Bijlage

|        Partner   |  Details   |
| ------- | -------             |
|     DockerDetails-afbeeldings         |          Naam van docker-installatie kopie. Bijvoorbeeld docker.io/azurefarmbeats/farmbeats-noaa (image in hub.docker.com) of myazureacr.azurecr.io/mydockerimage (afbeelding in Azure Container Registry), enzovoort. Als er geen REGI ster wordt gegeven, is de standaard waarde hub.docker.com      |
|          DockerDetails - imageTag             |         De label naam van de docker-installatie kopie. De standaard waarde is "meest recent"     |
|  DockerDetails-referenties      |  Referenties voor toegang tot de privé-docker. Dit wordt door de partner aan de klant verschaft   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM-SKU. [Hier](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ziet u alle beschik bare virtuele Linux-machines. Geldige waarden zijn: ' klein ', ' ExtraLarge ', ' large ', ' A8 ', ' A9 ', ' medium ', ' A5 ', ' A6 ', ' A7 ', ' STANDARD_D1 ', ' STANDARD_D2 ', ' STANDARD_D3 ', ' STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' A11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 ', ' STANDARD_G2 ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ', ' STANDARD_NC6 ', ' STANDARD_NC12 ' , STANDARD_H8, STANDARD_H8m, STANDARD_H16, STANDARD_H16m, STANDARD_H16mr, STANDARD_H16r, STANDARD_A1_V2, STANDARD_A2_V2, STANDARD_A4_V2, STANDARD_A8_V2, STANDARD_A2m_V2, STANDARD_A4m_V2, STANDARD_A8m_V2, STANDARD_M64ms, STANDARD_M128s, STANDARD_D2_V3, is niet de enige die wordt door ', ', ', ', ', ', '. **De standaard waarde is standard_d2_v2**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Nee. van toegewezen computer knooppunten voor batch-pool. De standaardwaarde is 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    SKU-ID van Azure Batch node-agent. Momenteel wordt alleen batch. node. Ubuntu 18,04-batch-knooppunt agent ondersteund.    |
| DockerDetails - partnerCredentials | referenties voor het aanroepen van de partner-API in docker. De partner moet deze informatie aan hun klanten geven op basis van het verificatie mechanisme dat wordt ondersteund voor beeld. Gebruikers naam/wacht woord of API-sleutels. |
| partnerType | "Weer" (andere partner typen in FarmBeats zijn "sensor" en "installatie kopie")  |
|  name   |   Gewenste naam van de partner in het FarmBeats-systeem   |
|  description |  Beschrijving   |

## <a name="next-steps"></a>Volgende stappen

U hebt nu sensor gegevens opgevraagd van uw Azure FarmBeats-exemplaar. Nu leert u hoe u [kaarten](generate-maps-in-azure-farmbeats.md#generate-maps) voor uw Farms kunt genereren.
