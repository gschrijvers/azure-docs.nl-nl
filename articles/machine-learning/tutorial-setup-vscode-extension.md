---
title: 'Zelf studie: de Visual Studio code extension instellen'
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen van de Visual Studio code Azure Machine Learning-extensie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: 731ab18346ac9f100862174312c2c9950026f1eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272905"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Azure Machine Learning Visual Studio code extension instellen

Meer informatie over het installeren en uitvoeren van scripts met de Azure Machine Learning Visual Studio code extension.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De Azure Machine Learning Visual Studio code-extensie installeren
> * Meld u aan bij uw Azure-account vanuit Visual Studio code
> * De extensie Azure Machine Learning gebruiken om een voorbeeld script uit te voeren

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement. Als u er nog geen hebt, kunt u zich aanmelden om de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree)te proberen.
- Visual Studio Code. Als u dit niet hebt, [installeert u het](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>De extensie installeren

1. Open Visual Studio Code.
1. Selecteer **uitbreidings** pictogram op de **activiteiten balk** om de weer gave extensies te openen.
1. Zoek in de weer gave extensies naar ' Azure Machine Learning '.
1. Selecteer **Installeren**.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning VS-code-extensie installeren](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> U kunt de Azure Machine Learning-extensie ook installeren via de Visual Studio Marketplace door [het installatie programma rechtstreeks te downloaden](https://aka.ms/vscodetoolsforai). 

De overige stappen in deze zelf studie zijn getest met **versie 0.6.8** van de uitbrei ding.

## <a name="sign-in-to-your-azure-account"></a>Meld u aan bij uw Azure-account

Als u resources wilt inrichten en werk belastingen op Azure wilt uitvoeren, moet u zich aanmelden met de referenties van uw Azure-account. Azure Machine Learning installeert automatisch de Azure-account extensie om het account beheer te helpen. Ga naar de volgende site voor [meer informatie over de Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Open het opdracht palet door **> opdracht venster weer geven** te selecteren in de menu balk. 
1. Voer de opdracht ' Azure: Sign in ' in het opdracht palet in om het aanmeldings proces te starten.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Een trainings script voor machine learning model uitvoeren in azure

Nu u bent aangemeld bij Azure met uw account referenties, gebruikt u de stappen in deze sectie om te leren hoe u de extensie gebruikt om een machine learning model te trainen.

1. Down load en pak de [VS code-Hulpprogram ma's voor AI-opslag](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) plaatsen overal op uw computer uit.
1. Open de `mnist-vscode-docs-sample` map in Visual Studio code.
1. Selecteer het pictogram van **Azure** in de activiteiten balk.
1. Selecteer het pictogram **experiment uitvoeren** boven aan de weer gave Azure machine learning.

    > [!div class="mx-imgBorder"]
    > ![Experiment uitvoeren](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Wanneer het opdracht palet wordt uitgevouwen, volgt u de aanwijzingen.

    1. Selecteer uw Azure-abonnement.
    1. Selecteer **Conda-afhankelijkheids bestand**in de lijst met omgevingen.
    1. Druk op **Enter** om door het Conda-afhankelijkheids bestand te bladeren. Dit bestand bevat de afhankelijkheden die nodig zijn om het script uit te voeren. In dit geval is het afhankelijkheden bestand het `env.yml` bestand in de `mnist-vscode-docs-sample` map.
    1. Druk op **Enter** om door het trainings script bestand te bladeren. Dit is het bestand dat code bevat voor een machine learning model waarmee afbeeldingen van handgeschreven cijfers worden gecategoriseerd. In dit geval is het script voor het trainen van het model `train.py` het bestand in `mnist-vscode-docs-sample` de map.

1. Op dit moment wordt een configuratie bestand dat lijkt op het onderstaande, weer gegeven in de tekst editor. De configuratie bevat de informatie die nodig is om de trainings taak uit te voeren, zoals het bestand dat de code bevat voor het trainen van het model en eventuele python-afhankelijkheden die zijn opgegeven in de vorige stap.

    ```json
    {
        "workspace": "WS04131142",
        "resourceGroup": "WS04131142-rg1",
        "location": "South Central US",
        "experiment": "WS04131142-exp1",
        "compute": {
            "name": "WS04131142-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS04131142-com1-rc1",
            "environment": {
                "name": "WS04131142-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. Wanneer u tevreden bent met uw configuratie, verzendt u uw experiment door het opdracht palet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Submit Experiment
    ```

    Hiermee verzendt u `train.py` het en configuratie bestand naar uw Azure machine learning-werk ruimte. Vervolgens wordt de trainings taak gestart op een reken resource in Azure.

### <a name="track-the-progress-of-the-training-script"></a>De voortgang van het trainings script volgen

Het uitvoeren van uw script kan enkele minuten duren. De voortgang volgen:

1. Selecteer het pictogram van **Azure** in de activiteiten balk.
1. Vouw het knoop punt van uw abonnement uit.
1. Vouw het knoop punt van uw lopende experiment uit. Dit bevindt zich `{workspace}/Experiments/{experiment}` in het knoop punt waar de waarden voor uw werk ruimte en experiment hetzelfde zijn als de eigenschappen die zijn gedefinieerd in het configuratie bestand.
1. Alle uitvoeringen voor het experiment worden vermeld, evenals hun status. Als u de meest recente status wilt weten, klikt u op het pictogram vernieuwen boven aan de weer gave Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Voortgang van experiment volgen](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Het getrainde model downloaden

Wanneer de uitvoering van het experiment is voltooid, is de uitvoer een getraind model. De uitvoer lokaal downloaden:

1. Klik met de rechter muisknop op de meest recente uitvoering en selecteer **Download uitvoer**.

    > [!div class="mx-imgBorder"]
    > ![Getraind model downloaden](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Selecteer een locatie waar de uitvoer moet worden opgeslagen.
1. Een map met de naam van uw run wordt lokaal gedownload. Navigeer naar die pagina.
1. De model bestanden bevinden zich `outputs/outputs/model` in de map.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een tensor flow model voor de classificatie van een installatie kopie trainen en implementeren met behulp van de Visual Studio code extension van Azure machine learning](tutorial-train-deploy-image-classification-model-vscode.md).
