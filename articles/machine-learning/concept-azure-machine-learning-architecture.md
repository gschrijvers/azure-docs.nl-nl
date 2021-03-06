---
title: Belangrijkste concepten van architectuur &
titleSuffix: Azure Machine Learning
description: Meer informatie over de architectuur, termen, concepten en werk stromen waaruit Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/13/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 49c23774fe16c24ba90daa02cdda1688b79b12d3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683038"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Hoe Azure Machine Learning werkt: architectuur en concepten

Meer informatie over de architectuur, concepten en werk stromen voor Azure Machine Learning. De belangrijkste onderdelen van de service en de algemene werk stroom voor het gebruik van de service worden weer gegeven in het volgende diagram:

![Azure Machine Learning architectuur en werk stroom](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Werkstroom

De werk stroom voor het machine learning model volgt doorgaans deze reeks:

1. **Trainen**
    + Ontwikkel machine learning trainings scripts in **python**, **R**of met de visuele ontwerper.
    + Een **reken doel**maken en configureren.
    + **Verzend de scripts** naar een geconfigureerde Compute-doel om in die omgeving uit te voeren. Tijdens de training kunnen de scripts lezen uit of schrijven naar **gegevens opslag**. De logboeken en uitvoer die tijdens de training zijn geproduceerd, worden opgeslagen als **uitvoeringen** in de **werk ruimte** en gegroepeerd onder **experimenten**.

1. **Pakket** -nadat een bevredigende uitvoering is gevonden, registreert u het persistente model in het **model register**.

1. **Valideren**  -  **Vraag het experiment** uit voor vastgelegde metrische gegevens uit de huidige en eerdere uitvoeringen. Als de metrische gegevens niet het gewenste resultaat aangeven, keert u terug naar stap 1 en herhaalt u de scripts.

1. **Implementeer** een score script dat gebruikmaakt van het model en **Implementeer het model** als een **webservice** in azure, of op een **IOT edge apparaat**.

1. **Monitor** : monitor voor **gegevens** overdracht tussen de trainings gegevensset en het afleiden van gegevens van een geïmplementeerd model. Als dat nodig is, keert u terug naar stap 1 om het model te trainen met nieuwe trainings gegevens.

## <a name="tools-for-azure-machine-learning"></a>Hulpprogram ma's voor Azure Machine Learning

Gebruik deze hulpprogram ma's voor Azure Machine Learning:

+  Communiceer met de service in een python-omgeving met de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Communiceer met de service in een wille keurige R-omgeving met de [Azure machine learning SDK voor R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatiseer uw machine learning activiteiten met de [Azure machine learning cli](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Gebruik [Azure machine learning Designer (preview)](concept-designer.md) om de werk stroom stappen uit te voeren zonder code te schrijven.
+ De [vele modellen oplossings versneller](https://aka.ms/many-models) (preview) is gebaseerd op Azure machine learning en stelt u in staat honderden of zelfs duizenden machine learning modellen te trainen, te bedienen en te beheren.

> [!NOTE]
> Hoewel in dit artikel voor waarden en concepten worden gedefinieerd die worden gebruikt door Azure Machine Learning, worden er geen termen en concepten voor het Azure-platform gedefinieerd. Zie de [Microsoft Azure verklarende woorden lijst](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)voor meer informatie over de terminologie van het Azure-platform.

## <a name="glossary"></a>Woordenlijst

* [Activiteit](#activities)
* [Werkruimte](#workspaces)
    * [Experimenten](#experiments)
        * [Uitvoeren](#runs) 
            * [Configuratie uitvoeren](#run-configurations)
            * [Snapshot](#snapshots)
            * [Git-tracking](#github-tracking-and-integration)
            * [Logboekregistratie](#logging)
    * [Machine learning-pijplijnen](#ml-pipelines)
    * [Modellen](#models)
        * [Omgevingen](#environments)
        * [Trainings script](#training-scripts)
        * [Schattingen](#estimators)
    * [Eindpunten](#endpoints)
        * [-Webservice](#web-service-endpoint)
        * [IoT-modules](#iot-module-endpoints)
    * [Gegevensset & gegevens opslag](#datasets-and-datastores)
    * [Rekendoelen](#compute-targets)

### <a name="activities"></a>Activiteiten

Een activiteit vertegenwoordigt een langlopende bewerking. De volgende bewerkingen zijn voor beelden van activiteiten:

* Een reken doel maken of verwijderen
* Een script uitvoeren op een compute-doel

Activiteiten kunnen meldingen geven via de SDK of de Web-UI, zodat u de voortgang van deze bewerkingen eenvoudig kunt bewaken.

### <a name="workspaces"></a>Werkruimten

[De werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor Azure machine learning. Het biedt een centrale locatie voor het werken met alle artefacten die u maakt wanneer u Azure Machine Learning gebruikt. U kunt een werk ruimte delen met anderen. Zie [Wat is een Azure machine learning-werk ruimte?](concept-workspace.md)voor een gedetailleerde beschrijving van werk ruimten.

### <a name="experiments"></a>Experimenten

Een experiment is een groepering van veel uitvoeringen van een opgegeven script. Deze behoort altijd tot een werk ruimte. Wanneer u een uitvoering verzendt, geeft u een naam op voor het experiment. Informatie over de uitvoering is opgeslagen onder dat experiment. Als u een uitvoering verzendt en een experimentnaam opgeeft die niet bestaat, wordt automatisch een nieuw experiment met de zojuist opgegeven naam gemaakt.

Zie [zelf studie: uw eerste model trainen](tutorial-1st-experiment-sdk-train.md)voor een voor beeld van het gebruik van een experiment.

### <a name="runs"></a>Wordt uitgevoerd

Een uitvoering is één uitvoering van een trainings script. Een experiment bevat normaal gesp roken meerdere uitvoeringen.

Azure Machine Learning registreert alle uitvoeringen en slaat de volgende informatie op in het experiment:

* Meta gegevens over de uitvoering (tijds tempel, duur, enzovoort)
* Metrische gegevens die door het script worden geregistreerd
* Uitvoer bestanden die door het experiment worden verzameld of expliciet door u worden geüpload
* Een moment opname van de map die uw scripts bevat vóór de uitvoering

U produceert een uitvoering wanneer u een script voor het trainen van een model verzendt. Een run kan nul of meer onderliggende uitvoeringen hebben. De uitvoering op het hoogste niveau kan bijvoorbeeld twee onderliggende uitvoeringen hebben, waarvan elk een eigen onderliggend item kan hebben.

### <a name="run-configurations"></a>Configuraties uitvoeren

Een uitvoerings configuratie is een set instructies die definieert hoe een script moet worden uitgevoerd in een opgegeven Compute-doel. De configuratie bevat een breed scala aan gedrags definities, zoals of u een bestaande python-omgeving wilt gebruiken of een Conda-omgeving wilt gebruiken die is gebaseerd op een specificatie.

Een uitvoerings configuratie kan worden opgeslagen in een bestand in de map met uw trainings script of kan worden geconstrueerd als een in-memory-object en worden gebruikt voor het verzenden van een run.

Zie bijvoorbeeld configuraties [selecteren en een compute-doel gebruiken om uw model te trainen](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Momentopnamen

Wanneer u een uitvoering verzendt, comprimeert Azure Machine Learning de map die het script bevat als een zip-bestand en verzendt het naar het Compute-doel. Het zip-bestand wordt vervolgens geëxtraheerd en het script wordt uitgevoerd. Azure Machine Learning slaat het zip-bestand ook als een moment opname op als onderdeel van de uitvoerings record. Iedereen met toegang tot de werk ruimte kan bladeren in een uitvoerings record en de moment opname downloaden.

> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="github-tracking-and-integration"></a>GitHub bijhouden en integreren

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Dit werkt met uitvoeringen die zijn verzonden met behulp van een Estimator, ML-pijp lijn of script uitvoering. Het werkt ook voor uitvoeringen die zijn verzonden vanuit de SDK of Machine Learning CLI.

Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

### <a name="logging"></a>Logboekregistratie

Wanneer u uw oplossing ontwikkelt, gebruikt u de Azure Machine Learning python-SDK in uw python-script om wille keurige gegevens te registreren. Na de uitvoering voert u een query uit op de metrische gegevens om te bepalen of de uitvoering het model heeft geproduceerd dat u wilt implementeren.

### <a name="ml-pipelines"></a>ML-pijp lijnen

U gebruikt machine learning pijp lijnen voor het maken en beheren van werk stromen die samen een machine learning fasen opdoen. Een pijp lijn kan bijvoorbeeld bestaan uit gegevens voorbereiding, model training, model implementatie en fase ring/scores. Elke fase kan meerdere stappen omvatten, die elk zonder toezicht kunnen worden uitgevoerd in verschillende reken doelen. 

U kunt de stappen van de pijp lijn herbruikbaresen en uitvoeren zonder de vorige stappen opnieuw uit te voeren als de uitvoer van deze stappen niet is gewijzigd. U kunt bijvoorbeeld een model opnieuw trainen zonder de stappen voor het voorbereiden van de kosten voor het opnieuw uitvoeren van gegevens als de gegevens niet zijn gewijzigd. Door pijp lijnen kunnen gegevens wetenschappers ook samen werken terwijl ze werken op verschillende gebieden van een machine learning werk stroom.

Zie [pijp lijnen en Azure machine learning](concept-ml-pipelines.md)voor meer informatie over het machine learning pijp lijnen met deze service.

### <a name="models"></a>Modellen

Een model is de eenvoudigste is een stukje code dat een invoer maakt en uitvoer produceert. Het maken van een machine learning model bestaat uit het selecteren van een algoritme, het leveren van gegevens en het afstemmen van Hyper parameters. Training is een iteratief proces dat een getraind model produceert dat het model dat u tijdens het trainings proces hebt geleerd.

Een model wordt gemaakt door een uitvoering in Azure Machine Learning. U kunt ook een model gebruiken dat is getraind buiten Azure Machine Learning. U kunt een model registreren in een Azure Machine Learning-werk ruimte.

Azure Machine Learning is Framework neutraal. Wanneer u een model maakt, kunt u een populair machine learning Framework gebruiken, zoals Scikit-learn, XGBoost, PyTorch, tensor flow en Chainer.

Voor een voor beeld van een model trainen met Scikit-Learn en een Estimator, Zie [zelf studie: een classificatie model voor een installatie kopie trainen met Azure machine learning](tutorial-train-models-with-aml.md).

Het **model register** houdt alle modellen in uw Azure machine learning-werk ruimte bij.

Modellen worden geïdentificeerd op naam en versie. Telkens wanneer u een model met dezelfde naam registreert als een bestaande, wordt ervan uitgegaan dat het een nieuwe versie is. De versie wordt verhoogd en het nieuwe model wordt geregistreerd onder dezelfde naam.

Wanneer u het model registreert, kunt u extra labels voor meta gegevens opgeven en vervolgens de tags gebruiken wanneer u naar modellen zoekt.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in uw Azure Machine Learning-werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

U kunt een geregistreerd model dat wordt gebruikt door een actieve implementatie niet verwijderen.

Zie voor een voor beeld van het registreren van een model [een afbeeldings classificatie model trainen met Azure machine learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Omgevingen

Azure ML-omgevingen worden gebruikt voor het opgeven van de configuratie (docker/python/Spark/etc.) die wordt gebruikt voor het maken van een reproduceer bare omgeving voor gegevens voorbereiding, model training en model. Ze zijn beheerde en geversiede entiteiten in uw Azure Machine Learning-werk ruimte waarmee reproduceer bare, audit bare en Portable machine learning werk stromen kunnen worden toegepast op verschillende Compute-doelen.

U kunt een omgevings object gebruiken op uw lokale Compute voor het ontwikkelen van uw trainings script, het hergebruiken van dezelfde omgeving op Azure Machine Learning Compute for model-training op schaal en het implementeren van uw model zelfs met diezelfde omgeving. 

Meer informatie [over het maken en beheren van een herbruikbare ml omgeving](how-to-use-environments.md) voor training en demijnen.

### <a name="training-scripts"></a>Trainings scripts

Als u een model wilt trainen, geeft u de map op die het trainings script en de bijbehorende bestanden bevat. U kunt ook een naam voor het experiment opgeven, die wordt gebruikt voor het opslaan van informatie die wordt verzameld tijdens de training. Tijdens de training wordt de hele Directory gekopieerd naar de trainings omgeving (Compute target) en het script dat is opgegeven door de uitvoerings configuratie wordt gestart. Een moment opname van de map wordt ook opgeslagen onder het experiment in de werk ruimte.

Zie [zelf studie: een classificatie model voor een installatie kopie trainen met Azure machine learning](tutorial-train-models-with-aml.md)voor een voor beeld.

### <a name="estimators"></a>Schattingen

Om model training met populaire Frameworks mogelijk te maken, kunt u met de klasse Estimator eenvoudig uitvoer configuraties bouwen. U kunt een algemene [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainings scripts te verzenden die gebruikmaken van een door u gekozen trainings raamwerk (zoals scikit-leren).

Voor PyTorch-, tensor flow-en Chainer-taken biedt Azure Machine Learning ook de geraamde [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -schattingen om het gebruik van deze frameworks te vereenvoudigen.

Raadpleeg voor meer informatie de volgende artikelen:

* [Train ml-modellen met schattingen](how-to-train-ml-models.md).
* [Train Pytorche diepe Learning-modellen op schaal met Azure machine learning](how-to-train-pytorch.md).
* [Tensor flow-modellen trainen en registreren op schaal met Azure machine learning](how-to-train-tensorflow.md).
* [Keten modellen trainen en registreren op schaal met Azure machine learning](how-to-train-ml-models.md).

### <a name="endpoints"></a>Eindpunten

Een eind punt is een instantie van uw model in een webservice die kan worden gehost in de Cloud of een IoT-module voor geïntegreerde implementaties van apparaten.

#### <a name="web-service-endpoint"></a>Webservice-eind punt

Bij het implementeren van een model als een webservice kan het eind punt worden geïmplementeerd op Azure Container Instances, Azure Kubernetes service of Fpga's. U maakt de service vanuit uw model, script en de bijbehorende bestanden. Deze worden in een basis container installatie kopie geplaatst die de uitvoerings omgeving voor het model bevat. De afbeelding heeft een HTTP-eind punt met gelijke taak verdeling dat Score aanvragen ontvangt die naar de webservice worden verzonden.

Met Azure kunt u uw webservice bewaken door Application Insights telemetrie of model telemetrie te verzamelen, als u ervoor hebt gekozen om deze functie in te scha kelen. De telemetriegegevens zijn alleen toegankelijk voor u en worden opgeslagen in uw Application Insights-en opslag account-exemplaren.

Als u automatisch schalen hebt ingeschakeld, wordt uw implementatie automatisch door Azure geschaald.

Voor een voor beeld van het implementeren van een model als een webservice raadpleegt u [een installatie kopie classificatie model implementeren in azure container instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>IoT-module-eind punten

Een geïmplementeerd IoT-module-eind punt is een docker-container die uw model en het bijbehorende script of de gekoppelde toepassing en eventuele extra afhankelijkheden bevat. U implementeert deze modules met behulp van Azure IoT Edge op edge-apparaten.

Als u bewaking hebt ingeschakeld, verzamelt Azure telemetriegegevens van het model in de module Azure IoT Edge. De telemetriegegevens zijn alleen toegankelijk voor u en worden opgeslagen in uw opslag account-exemplaar.

Azure IoT Edge zorgt ervoor dat de module wordt uitgevoerd en controleert het apparaat waarop het wordt gehost.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Reken instantie (preview-versie)

Een **Azure machine learning Compute-instantie** (voorheen laptop-VM) is een volledig beheerd werk station in de Cloud met meerdere hulpprogram ma's en omgevingen die voor machine learning zijn geïnstalleerd. Reken instanties kunnen worden gebruikt als een reken doel voor trainings-en detraining-taken. Voor grote taken is het [Azure machine learning compute-clusters](how-to-set-up-training-targets.md#amlcompute) met schaal mogelijkheden voor meerdere knoop punten een betere reken doel keuze.

Meer informatie over [Compute-exemplaren](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Gegevens sets en gegevens opslag

Met **Azure machine learning gegevens sets** (preview) kunt u eenvoudig toegang krijgen tot uw gegevens en deze gebruiken. Gegevens sets worden in verschillende scenario's beheerd, zoals model training en het maken van pijp lijnen. Met de Azure Machine Learning SDK hebt u toegang tot de onderliggende opslag, gegevens te verkennen en de levens cyclus van verschillende gegevensset-definities te beheren.

Gegevens sets bieden methoden voor het werken met in populaire indelingen, zoals het gebruik van `from_delimited_files()` of `to_pandas_dataframe()` .

Zie [Azure machine learning gegevens sets maken en registreren](how-to-create-register-datasets.md)voor meer informatie.  Raadpleeg de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)voor meer voor beelden met behulp van gegevens sets.

Een **gegevens archief** is een opslag abstractie van een Azure-opslag account. De gegevens opslag kan ofwel een Azure Blob-container ofwel een Azure-bestands share als de back-end-opslag gebruiken. Elke werk ruimte heeft een standaard gegevens opslag en u kunt aanvullende gegevens opslag registreren. Gebruik de python SDK API of de Azure Machine Learning CLI om bestanden op te slaan en op te halen uit de gegevens opslag.

### <a name="compute-targets"></a>Rekendoelen

Met een [reken doel](concept-compute-target.md) kunt u de reken resource opgeven waarin u uw trainings script uitvoert of uw service-implementatie host. Deze locatie kan uw lokale machine of een cloud-gebaseerde reken resource zijn.

Meer informatie over de [beschik bare reken doelen voor training en implementatie](concept-compute-target.md).

### <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Azure Machine Learning raadpleegt u:

* [Wat is Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Een Azure Machine Learning-werk ruimte maken](how-to-manage-workspace.md)
* [Zelf studie (deel 1): een model trainen](tutorial-train-models-with-aml.md)
