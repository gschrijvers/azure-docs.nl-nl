---
title: Gebruik automatische ML in ML-pijp lijnen
titleSuffix: Azure Machine Learning
description: Met de AutoMLStep kunt u geautomatiseerde machine learning gebruiken in uw pijp lijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 04/28/2020
ms.openlocfilehash: 9bf17512d0b14c7106101d98598e2914020afc7a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857947"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Automatische ML gebruiken in een Azure Machine Learning pijp lijn in python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met de functie voor automatische ML van Azure Machine Learning kunt u hoogwaardige modellen ontdekken zonder dat u elke mogelijke benadering hoeft te implementeren. In combi natie met Azure Machine Learning pijp lijnen kunt u Implementeer bare werk stromen maken waarmee u de algoritme die het beste werkt voor uw gegevens kunt detecteren. In dit artikel wordt uitgelegd hoe u een stap voor het voorbereiden van gegevens efficiënt kunt samen voegen met een stap van een automatische ML. Automatische ML kan snel de algoritme vinden die het meest geschikt is voor uw gegevens, terwijl u onderweg bent naar MLOps en de levens cyclus van modellen uitoefening met pijp lijnen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een gratis account aan voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md).  

* Basis kennis van het [geautomatiseerde machine learning](concept-automated-ml.md) van Azure en [machine learning pijp lijnen](concept-ml-pipelines.md) en-SDK.

## <a name="review-automated-mls-central-classes"></a>Bekijk de centrale klassen van automatische ML

Automatische ML in een pijp lijn wordt vertegenwoordigd door een `AutoMLStep` object. De `AutoMLStep` klasse is een subklasse van `PipelineStep`. Een grafiek van `PipelineStep` objecten definieert een `Pipeline`.

Er zijn verschillende subklassen van `PipelineStep`. Naast de `AutoMLStep`wordt in dit artikel een `PythonScriptStep` voor bereiding van gegevens weer gegeven en een andere voor het registreren van het model.

De voorkeurs manier _om gegevens naar_ een ml-pijp lijn te verplaatsen, `Dataset` is met objecten. Als u gegevens _tussen_ stappen wilt verplaatsen, is de voorkeurs `PipelineData` manier met objecten. Voor gebruik met `AutoMLStep`moet het `PipelineData` object worden omgezet in een `PipelineOutputTabularDataset` -object. Zie [invoer-en uitvoer gegevens van ml-pijp lijnen](how-to-move-data-in-out-of-pipelines.md)voor meer informatie.

De `AutoMLStep` is geconfigureerd via een `AutoMLConfig` -object. `AutoMLConfig`is een flexibele klasse, zoals beschreven in [automatische ml experimenten configureren in python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings). 

Een `Pipeline` uitvoering in een `Experiment`. De pijp lijn `Run` heeft voor elke stap een onderliggend `StepRun`element. De uitvoer van de automatische ML `StepRun` zijn de metrische gegevens over de training en het hoogste model.

Om zaken concreet te maken, wordt in dit artikel een eenvoudige pijp lijn voor een classificatie taak gemaakt. De taak wordt voor speld op Titanic, maar de gegevens of taak worden niet besproken, behalve bij het door geven.

## <a name="get-started"></a>Aan de slag

### <a name="retrieve-initial-dataset"></a>Initiële gegevensset ophalen

Een ML-werk stroom begint vaak met al bestaande basislijn gegevens. Dit is een goed scenario voor een geregistreerde gegevensset. Gegevens sets worden weer gegeven in de werk ruimte, ondersteunt versie beheer en kunnen interactief worden verkend. Er zijn veel manieren om een gegevensset te maken en te vullen, zoals beschreven in [Azure machine learning gegevens sets maken](how-to-create-register-datasets.md). Omdat we de python-SDK gebruiken om onze pijp lijn te maken, kunt u de SDK gebruiken om basislijn gegevens te downloaden en deze te registreren met de naam titanic_ds.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

De code meldt zich eerst aan bij de Azure Machine Learning werkruimte die is gedefinieerd in **config. json** (Zie [zelf studie: aan de slag met het maken van uw eerste ml-experiment met de python-SDK](tutorial-1st-experiment-sdk-setup.md)). Als er nog geen gegevensset met de `'titanic_ds'` naam geregistreerd is, wordt er een gemaakt. Met de code worden CSV-gegevens van het web gedownload, worden deze `TabularDataset` gebruikt voor het instantiëren van een en wordt de gegevensset vervolgens geregistreerd bij de werk ruimte. Ten slotte wijst de `Dataset.get_by_name()` functie de `Dataset` aan toe `titanic_ds`. 

### <a name="configure-your-storage-and-compute-target"></a>Uw opslag en het reken doel configureren

Aanvullende resources die de pijp lijn nodig heeft, zijn opslag en worden meestal Azure Machine Learning Reken bronnen. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

De tussenliggende gegevens tussen de gegevens voorbereiding en de automatische ML stap kunnen worden opgeslagen in de standaard gegevens opslag van de werk ruimte, zodat we niet meer op het `get_default_datastore()` `Workspace` object hoeven te bellen. 

Daarna controleert de code of het AML-reken doel `'cpu-cluster'` al bestaat. Als dat niet het geval is, geeft u op dat er een klein, op CPU gebaseerd reken doel moet worden uitgevoerd. Als u van plan bent om de uitgebreide leer functies van de ML te gebruiken (bijvoorbeeld tekst parametrisatie met DNN-ondersteuning), kiest u een compute met krachtige GPU-ondersteuning, zoals wordt beschreven in [grootten voor GPU geoptimaliseerde virtuele machines](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu). 

De code blokken totdat het doel is ingericht en vervolgens een aantal details van het zojuist gemaakte Compute-doel afdrukt. Ten slotte wordt het benoemde Compute-doel opgehaald uit de werk ruimte en `compute_target`toegewezen aan. 

### <a name="configure-the-training-run"></a>De trainings uitvoering configureren

De volgende stap zorgt ervoor dat de uitvoering van de externe training alle afhankelijkheden heeft die vereist zijn voor de trainings stappen. Afhankelijkheden en de runtime context worden ingesteld door een `RunConfiguration` -object te maken en te configureren. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
aml_run_config.environment.python.user_managed_dependencies = False

# Add some packages relied on by data prep step
aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
    conda_packages=['pandas','scikit-learn'], 
    pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)
```

## <a name="prepare-data-for-automated-machine-learning"></a>Gegevens voorbereiden voor automatische machine learning

### <a name="write-the-data-preparation-code"></a>De code voor gegevens voorbereiding schrijven

De Titanic-gegevensset van de basis lijn bestaat uit gemengde numerieke en tekst gegevens, waarbij bepaalde waarden ontbreken. Voor de voor bereiding op automatische machine learning is de pijp lijn stap voor het voorbereiden van gegevens:

- Ontbrekende gegevens vullen met wille keurige gegevens of een categorie die overeenkomt met ' onbekend '
- Categorische-gegevens transformeren naar gehele getallen
- Kolommen verwijderen die u niet wilt gebruiken
- De gegevens splitsen in trainings-en test sets
- De getransformeerde gegevens naar de `PipelineData` uitvoer paden schrijven

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

Het bovenstaande code fragment is een volledige, maar minimale, voor beeld van het voorbereiden van gegevens voor de Titanic-gegevens. Het fragment begint met een Jupyter ' Magic Command ' om de code naar een bestand te laten uitvoeren. Als u geen Jupyter-notebook gebruikt, verwijdert u die regel en maakt u het bestand hand matig.

De verschillende `prepare_` functies in het bovenstaande code fragment wijzigen de relevante kolom in de invoer gegevensset. Deze functies werken aan de gegevens zodra deze zijn gewijzigd in een `DataFrame` object Panda. In elk geval worden ontbrekende gegevens gevuld met representatieve wille keurige gegevens of categorische gegevens die duiden op ' Unknown '. Op tekst gebaseerde categorische-gegevens worden toegewezen aan gehele getallen. Kolommen die niet meer nodig zijn, worden overschreven of verwijderd. 

Nadat de code de functies voor gegevens voorbereiding heeft gedefinieerd, parseert de code het argument invoer. Dit is het pad waarnaar we onze gegevens willen schrijven. (Deze waarden worden bepaald door `PipelineData` objecten die worden besproken in de volgende stap.) Met de code wordt de `'titanic_cs'` `Dataset`registratie opgehaald, geconverteerd naar een Panda `DataFrame`en worden de verschillende functies voor gegevens voorbereiding aangeroepen. 

Omdat de `output_path` is volledig gekwalificeerd, wordt de `os.makedirs()` functie gebruikt om de mapstructuur voor te bereiden. Op dit moment kunt u gebruiken `DataFrame.to_csv()` om de uitvoer gegevens te schrijven, maar Parquet-bestanden zijn efficiënter. Deze efficiency is waarschijnlijk niet irrelevant met een dergelijke kleine gegevensset, maar het gebruik **PyArrow** van de PyArrow `from_pandas()` en `write_table()` -functies van het pakket zijn slechts een paar toetsaanslagen dan `to_csv()`.

Parquet-bestanden worden standaard ondersteund door de automatische ML-stap die hieronder wordt beschreven, dus er is geen speciale verwerking vereist om deze te gebruiken. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>De pijp lijn stap voor gegevens voorbereiding schrijven`PythonScriptStep`()

De hierboven beschreven code voor gegevens voorbereiding moet worden gekoppeld aan `PythonScripStep` een object dat met een pijp lijn moet worden gebruikt. Het pad waarnaar de uitvoer van de Parquet-gegevens voorbereiding wordt geschreven, wordt gegenereerd `PipelineData` door een object. De bronnen die eerder `ComputeTarget` `RunConfig` `'titanic_ds' Dataset` zijn voor bereid, zoals de, en worden gebruikt om de specificatie te volt ooien.

```python
from azureml.pipeline.core import PipelineData
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```

Het `prepped_data_path` object is van het `PipelineOutputFileDataset`type. U ziet dat deze zowel in de `arguments` als `outputs` -argumenten is opgegeven. Als u de vorige stap bekijkt, ziet u dat in de code voor gegevens voorbereiding de waarde van het argument `'--output_path'` het bestandspad is waarnaar het Parquet-bestand is geschreven. 

## <a name="train-with-automlstep"></a>Trainen met AutoMLStep

Het configureren van een automatische ML-pijplijn stap wordt uitgevoerd `AutoMLConfig` met de-klasse. Deze flexibele klasse wordt beschreven in [automatische ml experimenten configureren in python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Gegevens invoer en-uitvoer zijn de enige aspecten van de configuratie die speciale aandacht vereisen in een ML-pijp lijn. De invoer en uitvoer `AutoMLConfig` voor in pijp lijnen worden hieronder uitvoerig besproken. Behalve gegevens is een voor deel van ML-pijp lijnen de mogelijkheid om verschillende reken doelen te gebruiken voor verschillende stappen. U kunt ervoor kiezen om een krachtiger `ComputeTarget` te gebruiken voor het geautomatiseerde ml proces. Dit is net zo eenvoudig als het toewijzen van een `RunConfiguration` krachtiger `AutoMLConfig` aan de `run_configuration` para meter van het object.

### <a name="send-data-to-automlstep"></a>Gegevens verzenden naar`AutoMLStep`

In een ML-pijp lijn moeten de invoer gegevens een `Dataset` object zijn. De hoogst presterende manier is om de invoer gegevens op te geven in `PipelineOutputTabularDataset` de vorm van objecten. U maakt een object van dat type met de `parse_parquet_files()` of `parse_delimited_files()` op een `PipelineOutputFileDataset`, zoals het `prepped_data_path` object.

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

Met het bovenstaande code fragment maakt u een `PipelineOutputTabularDataset` hoge uitvoering `PipelineOutputFileDataset` van de uitvoer van de stap voor het voorbereiden van gegevens.

Een andere optie is het `Dataset` gebruik van objecten die zijn geregistreerd in de werk ruimte:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Vergelijking van de twee technieken:

| Techniek |  | 
|-|-|
|`PipelineOutputTabularDataset`| Hogere prestaties | 
|| Natuurlijke route van`PipelineData` | 
|| De gegevens zijn niet behouden na de pijplijn uitvoering |
|| [Laptop met `PipelineOutputTabularDataset` techniek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registratie`Dataset` | Lagere prestaties |
| | Kunnen op verschillende manieren worden gegenereerd | 
| | Gegevens blijven behouden en worden weer gegeven in de werk ruimte |
| | [Notitie blok met `Dataset` geregistreerde techniek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)

### <a name="specify-automated-ml-outputs"></a>Automatische ML-uitvoer opgeven

De uitvoer van de `AutoMLStep` zijn de laatste metrische scores van het model met de hogere prestaties en het model zelf. Als u deze uitvoer in verdere pijplijn stappen wilt gebruiken, moet `PipelineData` u objecten voorbereiden om ze te ontvangen.

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                           datastore=datastore,
                           pipeline_output_name='metrics_output',
                           training_output=TrainingOutput(type='Metrics'))
model_data = PipelineData(name='best_model_data',
                           datastore=datastore,
                           pipeline_output_name='model_output',
                           training_output=TrainingOutput(type='Model'))
```

Met het bovenstaande fragment maakt u `PipelineData` de twee objecten voor de metrische gegevens en de model uitvoer. Deze worden aangeduid met de naam, toegewezen aan de standaard gegevens opslag die eerder is opgehaald, `type` en `TrainingOutput` gekoppeld aan `AutoMLStep`het specifieke van de. Omdat we deze `pipeline_output_name` `PipelineData` objecten toewijzen, zijn hun waarden niet alleen beschikbaar vanuit de afzonderlijke pijplijn stap, maar van de pijp lijn als geheel, zoals hieronder wordt beschreven in de sectie "pijp lijn resultaten onderzoeken". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>De geautomatiseerde pijp lijn-stap voor ML configureren en maken

Zodra de invoer en uitvoer zijn gedefinieerd, is het tijd om de `AutoMLConfig` en `AutoMLStep`toe te voegen. De details van de configuratie zijn afhankelijk van uw taak, zoals beschreven in [automatische ml experimenten configureren in python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Voor de taak Titanic overlevings classificatie ziet u in het volgende code fragment een eenvoudige configuratie.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    allow_reuse=True)
```
Het fragment bevat een Idiom die meestal wordt `AutoMLConfig`gebruikt met. Argumenten die meer Hydraulica (afstemming-ish) zijn, worden in een afzonderlijke woorden lijst opgegeven, terwijl de waarden die kleiner zijn dan de `AutoMLConfig` waarde die u wilt wijzigen, rechtstreeks in de constructor worden opgegeven. In dit geval `automl_settings` geeft u een korte uitvoering op: de uitvoering wordt gestopt na slechts twee iteraties of 15 minuten, afhankelijk van wat het eerste komt.

De `automl_settings` woorden lijst wordt door gegeven `AutoMLConfig` aan de constructor als kwargs. De andere para meters zijn niet complex:

- `task`is ingesteld op `classification` voor dit voor beeld. Andere geldige waarden zijn `regression` en`forecasting`
- `path`en `debug_log` Beschrijf het pad naar het project en een lokaal bestand waarnaar fout opsporingsgegevens worden geschreven 
- `compute_target`is de eerder gedefinieerde `compute_target` die in dit voor beeld een goedkope, op CPU gebaseerde machine is. Als u de uitgebreide leer faciliteiten van AutoML gebruikt, wilt u het Compute-doel wijzigen in op GPU gebaseerd
- `featurization`is ingesteld op `auto`. Meer informatie vindt u in de sectie [gegevens parametrisatie](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization) van het document voor automatische configuratie van ml 
- `training_data`wordt ingesteld op de `PipelineOutputTabularDataset` objecten die zijn gemaakt op basis van de uitvoer van de stap voor het voorbereiden van gegevens. 
- `label_column_name`Hiermee wordt aangegeven in welke kolom u wilt voors pellen 

`AutoMLStep` Het gaat hierbij om `AutoMLConfig` de en heeft, als uitvoer, de `PipelineData` objecten die zijn gemaakt om de metrieken en model gegevens op te slaan. 

>[!Important]
> U moet zo `passthru_automl_config` instellen `False` dat objecten `AutoMLStep` worden gebruikt `PipelineOutputTabularDataset` voor invoer.

In dit voor beeld voert het geautomatiseerde ML proces Kruis validaties uit op de `training_data`. U kunt het aantal Kruis validaties beheren met het `n_cross_validations` argument. Als u uw trainings gegevens al hebt gesplitst als onderdeel van de stappen voor het voorbereiden van gegevens, `validation_data` kunt u deze `Dataset`zelf instellen.

U kunt af en toe gebruikmaken van `X` het gebruik voor gegevens `y` functies en voor gegevenslabels. Deze techniek is afgeschaft en moet worden `training_data` gebruikt voor invoer. 

## <a name="register-the-model-generated-by-automated-ml"></a>Het model registreren dat is gegenereerd door automatische MILLILITERs 

In de laatste stap van een Basic ML-pijp lijn wordt het gemaakte model geregistreerd. Door het model toe te voegen aan het model register van de werk ruimte, is het beschikbaar in de portal en kan dit versie nummer hebben. Als u het model wilt registreren, `PythonScriptStep` schrijft u een `model_data` andere die de `AutoMLStep`uitvoer van de uitvoert.

### <a name="write-the-code-to-register-the-model"></a>De code schrijven om het model te registreren

Een model is geregistreerd in een `Workspace`. U bent waarschijnlijk vertrouwd met het `Workspace.from_config()` gebruik van om u aan te melden bij uw werk ruimte op uw lokale computer, maar er is een andere manier om de werk ruimte te verkrijgen vanuit een lopende ml-pijp lijn. Hiermee `Run.get_context()` haalt u de `Run`actieve op. Dit `run` object biedt toegang tot veel belang rijke objecten, met `Workspace` inbegrip van de hier gebruikte.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>De PythonScriptStep-code schrijven

Het model registratie `PythonScriptStep` maakt gebruik van `PipelineParameter` een voor een van de argumenten. Pijplijn parameters zijn argumenten voor pijp lijnen die eenvoudig kunnen worden ingesteld tijdens het uitvoeren van de aanvraag. Zodra deze zijn gedeclareerd, worden ze door gegeven als normale argumenten. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Uw Automated ML-pijp lijn maken en uitvoeren

Het maken en uitvoeren van een pijp lijn met `AutoMLStep` een is niet anders dan een normale pijp lijn. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

De bovenstaande code combineert de voor bereiding van gegevens, automatische MILLILITERs en het registreren van `Pipeline` stappen in een object. Vervolgens wordt een `Experiment` -object gemaakt. De `Experiment` constructor haalt het genoemde experiment op als dit bestaat, of maak het als dat nodig is. Het verzendt `Pipeline` een `Experiment` `Run` -object waarmee de pijp lijn asynchroon kan worden uitgevoerd. De `wait_for_completion()` functie blokkeert tot de uitvoering is voltooid.

### <a name="examine-pipeline-results"></a>Pijplijn resultaten onderzoeken 

Zodra de `run` bewerking is voltooid, kunt u `PipelineData` objecten ophalen waaraan een `pipeline_output_name`is toegewezen. U kunt de resultaten downloaden en laden voor verdere verwerking.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Gedownloade bestanden worden naar de submap `azureml/{run.id}/`geschreven. Het metrieke bestand is JSON-indeling en kan worden geconverteerd naar een Panda data frame voor onderzoek.

Voor lokale verwerking moet u mogelijk relevante pakketten installeren, zoals Panda, selectie, de AzureML-SDK, enzovoort. In dit voor beeld is het waarschijnlijk dat het beste model dat door automatische ML wordt gevonden, afhankelijk is van XGBoost.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

Het bovenstaande code fragment toont het metrische bestand dat wordt geladen vanaf de locatie in het Azure-gegevens archief. U kunt de app ook laden vanuit het gedownloade bestand, zoals wordt weer gegeven in de opmerking. Zodra u de app hebt gedeserialiseerd en geconverteerd naar een Panda data frame, kunt u gedetailleerde metrische gegevens bekijken voor elk van de iteraties van de stap Automated ML.

Het model bestand kan worden gedeserialiseerd in een-object `Model` dat u kunt gebruiken voor de afleiding, verdere metrische analyse, enzovoort. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Zie [een bestaand model gebruiken met Azure machine learning](how-to-deploy-existing-model.md)voor meer informatie over het laden en werken met bestaande modellen.

### <a name="download-the-results-of-an-automated-ml-run"></a>De resultaten van een automatische ML-uitvoering downloaden

Als u samen met het artikel hebt gevolgd, hebt u een geïnstantieerd `run` object. U kunt echter ook voltooide `Run` objecten ophalen uit de `Workspace` in de richting van `Experiment` een object.

De werk ruimte bevat een volledig overzicht van al uw experimenten en wordt uitgevoerd. U kunt de portal gebruiken om de uitvoer van experimenten te zoeken en te downloaden of code te gebruiken. Als u toegang wilt krijgen tot de records uit een historische uitvoering, gebruikt u Azure Machine Learning om de ID te vinden van de uitvoering waarin u geïnteresseerd bent. Met deze ID kunt u de specifieke `run` `Workspace` optie kiezen. `Experiment`

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

U moet de teken reeksen in de bovenstaande code wijzigen in de details van uw historische uitvoering. In het bovenstaande code fragment wordt ervan uitgegaan `ws` dat u hebt `Workspace` toegewezen aan de `from_config()`relevante met het normale. Het experiment of interest wordt direct opgehaald en vervolgens vindt de code de `Run` interesse door te voldoen aan de `run.id` waarde.

Zodra u een `Run` object hebt, kunt u de metrische gegevens en het model downloaden. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Elk `Run` object bevat `StepRun` objecten die informatie bevatten over de stap van de uitvoering van de afzonderlijke pijp lijn. `run` Er wordt gezocht naar het `StepRun` object voor `AutoMLStep`. De metrische gegevens en het model worden opgehaald met hun standaard namen, die ook beschikbaar zijn als u geen objecten `PipelineData` door geeft aan `outputs` de para meter `AutoMLStep`van. 

Ten slotte worden de werkelijke metrische gegevens en het model gedownload naar uw lokale computer, zoals beschreven in de sectie ' Bekijk de resultaten van de pijp lijn '.

## <a name="next-steps"></a>Volgende stappen

- Voer deze Jupyter-notebook uit met een [volledig voor beeld van geautomatiseerd ml in een pijp lijn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) die regressie tarieven gebruikt om te voors pellen
- [Automatische ML experimenten maken zonder code te schrijven](how-to-use-automated-ml-for-ml-models.md)
- Verken verschillende [Jupyter-notebooks die automatische milliliters tonen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Meer informatie over het integreren van uw pijp lijn in [end-to-end MLOps](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) of het onderzoeken van de [MLOps github-opslag plaats](https://github.com/Microsoft/MLOpspython) 
