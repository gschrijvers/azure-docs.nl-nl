---
title: Azure Machine Learning pijp lijnen uitvoeren
description: Meer informatie over het uitvoeren van uw Azure Machine Learning-pijp lijnen in uw Azure Data Factory pijp lijnen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418997"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Machine Learning pijp lijnen uitvoeren in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Voer uw Azure Machine Learning-pijp lijnen uit als een stap in uw Azure Data Factory pijp lijnen. Met de activiteit pijp lijn uitvoeren Machine Learning kunt u batch Voorspellings scenario's maken, zoals het identificeren van mogelijke standaard waarden voor leningen, het bepalen van sentiment en het analyseren van gedrags patronen van klanten.

De onderstaande video bevat een inleiding en demonstratie van zes minuten voor deze functie.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van de activiteit in de pijp lijn | Tekenreeks | Ja
type | Type activiteit is ' AzureMLExecutePipeline ' | Tekenreeks | Ja
linkedServiceName | Gekoppelde service aan Azure Machine Learning | Verwijzing naar gekoppelde service | Ja
mlPipelineId | ID van de gepubliceerde Azure Machine Learning pijp lijn | Teken reeks (of expressie met het resultType van de teken reeks) | Ja
experimentnaam | Naam van het experiment met de uitvoerings geschiedenis van de Machine Learning pijplijn uitvoering | Teken reeks (of expressie met het resultType van de teken reeks) | Nee
mlPipelineParameters | Sleutel-, waardeparen die moeten worden door gegeven aan het gepubliceerde eind punt van Azure Machine Learning pijplijn. Sleutels moeten overeenkomen met de namen van de pijplijn parameters die in de gepubliceerde Machine Learning pijp lijn zijn gedefinieerd | Object met sleutel waarde-paren (of expressie met het resultType-object) | Nee
mlParentRunId | De ID van de bovenliggende Azure Machine Learning pijplijn uitvoering | Teken reeks (of expressie met het resultType van de teken reeks) | Nee
continueOnStepFailure | Of de uitvoering van andere stappen in de Machine Learning pijplijn uitvoering moet worden voortgezet als een stap is mislukt | booleaans | Nee

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert:

* [Activiteit gegevens stroom uitvoeren](control-flow-execute-data-flow-activity.md)
* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
