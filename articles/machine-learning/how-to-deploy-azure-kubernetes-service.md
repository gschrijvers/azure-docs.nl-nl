---
title: Modellen implementeren in azure Kubernetes service
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning-modellen als een webservice met behulp van de Azure Kubernetes-service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: aec1b7f7bf60be34d21d52ca652a776cf3275fe8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811762"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Een model implementeren in een Azure Kubernetes service-cluster
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model als een webservice op Azure Kubernetes service (AKS). De Azure Kubernetes-service is geschikt voor grootschalige productie-implementaties. Gebruik de Azure Kubernetes-service als u een of meer van de volgende mogelijkheden nodig hebt:

- __Snelle reactie tijd__.
- Automatisch __schalen__ van de geïmplementeerde service.
- __Opties voor hardwareversnelling,__ zoals GPU en Programmeer bare poort matrices (FPGA).

> [!IMPORTANT]
> Het schalen van clusters wordt niet ondersteund via de Azure Machine Learning SDK. Zie [het aantal knoop punten schalen in een AKS-cluster](../aks/scale-cluster.md)voor meer informatie over het schalen van de knooppunten in een AKS-cluster.

Wanneer u implementeert in azure Kubernetes service, implementeert u naar een AKS-cluster dat is __verbonden met uw werk ruimte__. Er zijn twee manieren om een AKS-cluster te verbinden met uw werk ruimte:

* Maak het AKS-cluster met behulp van de Azure Machine Learning SDK, de Machine Learning CLI of [Azure machine learning Studio](https://ml.azure.com). Dit proces verbindt automatisch het cluster met de werk ruimte.
* Koppel een bestaand AKS-cluster aan uw Azure Machine Learning-werk ruimte. Een cluster kan worden gekoppeld met behulp van de Azure Machine Learning SDK, Machine Learning CLI of Azure Machine Learning Studio.

> [!IMPORTANT]
> Het maken of verzenden van een bijlage is een eenmalige taak. Zodra een AKS-cluster is verbonden met de werk ruimte, kunt u het gebruiken voor implementaties. U kunt het AKS-cluster loskoppelen of verwijderen als u het niet meer nodig hebt. Zodra de verbinding is verbroken of verwijderd, kunt u niet meer implementeren naar het cluster.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een machine learning model dat in uw werk ruimte is geregistreerd. Als u geen geregistreerd model hebt, raadpleegt u [hoe en hoe u modellen implementeert](how-to-deploy-and-where.md).

- De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

- In de code fragmenten van __python__ in dit artikel wordt ervan uitgegaan dat de volgende variabelen zijn ingesteld:

    * `ws`-Ingesteld op uw werk ruimte.
    * `model`-Ingesteld op uw geregistreerde model.
    * `inference_config`-Stel in op de configuratie voor het afstellen van het model.

    Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

- In het __cli__ -fragment in dit artikel wordt ervan uitgegaan dat `inferenceconfig.json` u een document hebt gemaakt. Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van dit document.

## <a name="create-a-new-aks-cluster"></a>Een nieuw AKS-cluster maken

**Geschatte tijd**: circa 20 minuten.

Het maken of koppelen van een AKS-cluster is een eenmalig proces voor uw werk ruimte. U kunt dit cluster hergebruiken voor meerdere implementaties. Als u het cluster of de resource groep verwijdert die het bevat, moet u de volgende keer dat u moet implementeren een nieuw cluster maken. Er kunnen meerdere AKS-clusters aan uw werk ruimte zijn gekoppeld.

> [!TIP]
> Als u uw AKS-cluster met behulp van een Azure Virtual Network wilt beveiligen, moet u eerst het virtuele netwerk maken. Zie voor meer informatie [beveiligd experimenten en demijnen met Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Als u een AKS-cluster wilt maken voor __ontwikkeling__, __validatie__en __testen__ in plaats van productie, kunt u het __cluster doel__ opgeven voor __dev test__.

> [!WARNING]
> Als u dit `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`hebt ingesteld, is het cluster dat is gemaakt niet geschikt voor verkeer op productie niveau en kunnen de tijden voor de afnemen toenemen. Dev/test-clusters garanderen ook geen fout tolerantie. We raden ten minste twee virtuele Cpu's aan voor dev/test-clusters.

De volgende voor beelden laten zien hoe u een nieuw AKS-cluster maakt met behulp van de SDK en CLI:

**De SDK gebruiken**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Voor [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), als u aangepaste waarden `agent_count` voor en `vm_size`selecteert en `cluster_purpose` niet `DEV_TEST`, moet u ervoor zorgen dat `agent_count` vermenigvuldigd door `vm_size` is groter dan of gelijk aan 12 virtuele cpu's. Als u bijvoorbeeld een `vm_size` van ' Standard_D3_v2 ' gebruikt, die 4 virtuele cpu's heeft, moet u een van de `agent_count` drie of grotere kiezen.
>
> De Azure Machine Learning SDK biedt geen ondersteuning voor het schalen van een AKS-cluster. Als u de knoop punten in het cluster wilt schalen, gebruikt u de gebruikers interface voor uw AKS-cluster in de Azure Machine Learning Studio. U kunt alleen het aantal knoop punten wijzigen, niet de VM-grootte van het cluster.

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**De CLI gebruiken**

```azurecli
az ml computetarget create aks -n myaks
```

Zie voor meer informatie de referentie [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

## <a name="attach-an-existing-aks-cluster"></a>Een bestaand AKS-cluster koppelen

**Geschatte tijd:** Ongeveer 5 minuten.

Als u al een AKS-cluster in uw Azure-abonnement hebt en versie 1,17 of lager is, kunt u het gebruiken om uw installatie kopie te implementeren.

> [!TIP]
> Het bestaande AKS-cluster kan zich in een andere Azure-regio bevinden dan uw Azure Machine Learning-werk ruimte.
>
> Als u uw AKS-cluster met behulp van een Azure Virtual Network wilt beveiligen, moet u eerst het virtuele netwerk maken. Zie voor meer informatie [beveiligd experimenten en demijnen met Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Wanneer u een AKS-cluster koppelt aan een werk ruimte, kunt u definiëren hoe u het cluster gaat gebruiken `cluster_purpose` door de para meter in te stellen.

Als u de `cluster_purpose` para meter of set `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`niet instelt, moet het cluster ten minste 12 virtuele cpu's hebben.

Als u instelt `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, hoeft het cluster niet 12 virtuele cpu's te hebben. We raden ten minste twee virtuele Cpu's aan voor dev/test. Een cluster dat is geconfigureerd voor dev/test is echter niet geschikt voor verkeer op productie niveau en kan de tijd voor de afnemen verhogen. Dev/test-clusters garanderen ook geen fout tolerantie.

> [!WARNING]
> Maak vanuit uw werk ruimte niet meerdere gelijktijdige bijlagen op hetzelfde AKS-cluster. U kunt bijvoorbeeld een AKS-cluster koppelen aan een werk ruimte met behulp van twee verschillende namen. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).
>
> Als u een AKS-cluster opnieuw wilt koppelen, bijvoorbeeld om TLS of een andere cluster configuratie-instelling te wijzigen, moet u eerst de bestaande bijlage verwijderen met behulp van [AksCompute. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Raadpleeg de volgende artikelen voor meer informatie over het maken van een AKS-cluster met behulp van de Azure CLI of portal:

* [Een AKS-cluster maken (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Een AKS-cluster maken (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

De volgende voor beelden laten zien hoe u een bestaand AKS-cluster kunt koppelen aan uw werk ruimte:

**De SDK gebruiken**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**De CLI gebruiken**

Als u een bestaand cluster wilt koppelen met behulp van de CLI, moet u de bron-ID van het bestaande cluster ophalen. Gebruik de volgende opdracht om deze waarde op te halen. Vervang `myexistingcluster` door de naam van uw AKS-cluster. Vervang `myresourcegroup` door de resource groep die het cluster bevat:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Gebruik de volgende opdracht om het bestaande cluster aan uw werk ruimte te koppelen. Vervang `aksresourceid` door de waarde die door de vorige opdracht is geretourneerd. Vervang `myresourcegroup` door de resource groep die uw werk ruimte bevat. Vervang `myworkspace` door de naam van uw werk ruimte.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Zie voor meer informatie de referentie [AZ ml computetarget attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

## <a name="deploy-to-aks"></a>Implementeren naar AKS

Als u een model wilt implementeren in azure Kubernetes service, maakt u een __implementatie configuratie__ waarin de benodigde reken resources worden beschreven. Bijvoorbeeld het aantal kernen en het geheugen. U hebt ook een Afleidings __configuratie__nodig, waarmee de omgeving wordt beschreven die nodig is voor het hosten van het model en de webservice. Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van de configuratie voor demijnen.

### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. implementeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice-wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>De CLI gebruiken

Als u wilt implementeren met behulp van de CLI, gebruikt u de volgende opdracht. Vervang `myaks` door de naam van het AKS Compute-doel. Vervang `mymodel:1` door de naam en versie van het geregistreerde model. Vervang `myservice` door de naam om deze service te verlenen:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Zie voor meer informatie de referentie [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="using-vs-code"></a>VS Code gebruiken

Zie voor meer informatie over het gebruik van VS code [implementeren naar AKS via de VS code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Voor de implementatie via VS code moet het AKS-cluster vooraf worden gemaakt of aan uw werk ruimte zijn gekoppeld.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellen implementeren op AKS met behulp van gecontroleerde implementatie (preview-versie)

Analyseer en promoot model versies op een gecontroleerde manier met behulp van eind punten. U kunt Maxi maal zes versies achter één eind punt implementeren. Eind punten bieden de volgende mogelijkheden:

* Het __percentage score verkeer configureren dat naar elk eind punt wordt verzonden__. Route bijvoorbeeld 20% van het verkeer naar eind punt ' test ' en 80% naar ' productie '.

    > [!NOTE]
    > Als u geen account voor 100% van het verkeer hebt, wordt het resterende percentage doorgestuurd naar de __standaard__ eindpunt versie. Als u bijvoorbeeld eind punt versie ' test ' configureert om 10% van het verkeer te krijgen, en ' Prod ' voor 30%, wordt de resterende 60% naar de standaard eindpunt versie verzonden.
    >
    > De eerste versie van het eind punt die wordt gemaakt, wordt automatisch geconfigureerd als standaard. U kunt dit wijzigen door in `is_default=True` te stellen wanneer u een eindpunt versie maakt of bijwerkt.
     
* Een eindpunt versie als een __controle__ of __behandeling__labelen. Zo kan de huidige versie van het productie-eind punt het besturings element zijn, terwijl mogelijke nieuwe modellen worden geïmplementeerd als behandelings versies. Na het evalueren van de prestaties van de behandelings versies, kan deze worden gepromoveerd tot de nieuwe productie/het andere besturings element.

    > [!NOTE]
    > U kunt slechts __één__ besturings element hebben. U kunt meerdere behandelingen hebben.

U kunt app Insights inschakelen om operationele metrische gegevens van eind punten en geïmplementeerde versies weer te geven.

### <a name="create-an-endpoint"></a>Een eindpunt maken
Wanneer u klaar bent om uw modellen te implementeren, maakt u een score-eind punt en implementeert u uw eerste versie. In het volgende voor beeld ziet u hoe u het eind punt implementeert en maakt met behulp van de SDK. De eerste implementatie wordt gedefinieerd als de standaard versie, wat betekent dat niet-opgegeven verkeers percentiel in alle versies naar de standaard versie gaat.  

> [!TIP]
> In het volgende voor beeld wordt met de configuratie de eerste eindpunt versie ingesteld voor het verwerken van 20% van het verkeer. Aangezien dit het eerste eind punt is, is het ook de standaard versie. Omdat we geen andere versies hebben voor de andere 80% van het verkeer, wordt deze ook doorgestuurd naar de standaard waarde. Totdat andere versies die een percentage van het verkeer uitvoeren, worden geïmplementeerd, ontvangt deze een effectief 100% van het verkeer.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Versies bijwerken en toevoegen aan een eind punt

Voeg nog een versie toe aan uw eind punt en configureer het Score verkeer percentiel naar de versie. Er zijn twee soorten versies, een besturings element en een behandelings versie. Er kunnen meerdere behandelings versies zijn om te vergelijken met één versie van het besturings element.

> [!TIP]
> De tweede versie, gemaakt door het volgende code fragment, accepteert 10% van het verkeer. De eerste versie is geconfigureerd voor 20%, dus slechts 30% van het verkeer is geconfigureerd voor specifieke versies. De resterende 70% wordt verzonden naar de eerste eindpunt versie, omdat het ook de standaard versie is.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Bestaande versies bijwerken of verwijderen in een eind punt. U kunt het standaard type van de versie, het type besturings element en het verkeers percentiel wijzigen. In het volgende voor beeld verhoogt de tweede versie het verkeer naar 40% en is nu de standaard waarde.

> [!TIP]
> Na het volgende code fragment is de tweede versie nu standaard. Het is nu geconfigureerd voor 40%, terwijl de oorspronkelijke versie nog steeds is geconfigureerd voor 20%. Dit betekent dat 40% van het verkeer niet is verwerkt door versie configuraties. Het overgebleven verkeer wordt doorgestuurd naar de tweede versie, omdat dit nu standaard is. Het ontvangt effectief 80% van het verkeer.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Verificatie van webservice

Bij het implementeren naar de Azure Kubernetes-service is verificatie op __basis van een sleutel__ standaard ingeschakeld. U kunt ook verificatie __op basis van tokens__ inschakelen. Voor verificatie op basis van tokens moeten clients een Azure Active Directory-account gebruiken om een verificatie token aan te vragen, dat wordt gebruikt om aanvragen te doen voor de geïmplementeerde service.

Als __disable__ u de verificatie wilt uitschakelen `auth_enabled=False` , stelt u de para meter bij het maken van de implementatie configuratie in. In het volgende voor beeld wordt de verificatie uitgeschakeld met de SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Zie voor meer informatie over het verifiëren van een client toepassing het [model een Azure machine learning gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Verificatie met sleutels

Als sleutel verificatie is ingeschakeld, kunt u de `get_keys` methode gebruiken om een primaire en secundaire verificatie sleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel opnieuw moet genereren, gebruikt u[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Verificatie met tokens

Als u token verificatie wilt inschakelen, `token_auth_enabled=True` stelt u de para meter in wanneer u een implementatie maakt of bijwerkt. In het volgende voor beeld wordt token verificatie met behulp van de SDK ingeschakeld:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Als token verificatie is ingeschakeld, kunt u de `get_token` methode gebruiken om een JWT-token op te halen en de verval tijd van dat token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen na de tijd van `refresh_by` de token.
>
> Micro soft raadt u ten zeerste aan om uw Azure Machine Learning-werk ruimte te maken in dezelfde regio als uw Azure Kubernetes service-cluster. Als u wilt verifiëren met een token, wordt er door de webservice een aanroep naar de regio waarin uw Azure Machine Learning-werk ruimte is gemaakt. Als de regio van uw werk ruimte niet beschikbaar is, kunt u geen token voor uw webservice ophalen, zelfs als uw cluster zich in een andere regio bevindt dan uw werk ruimte. Dit leidt ertoe dat op tokens gebaseerde verificatie niet beschikbaar is totdat de regio van de werk ruimte weer beschikbaar is. Daarnaast is de afstand tussen de regio van uw cluster en de regio van uw werk ruimte groter, hoe langer het duurt om een token op te halen.

## <a name="update-the-web-service"></a>De webservice bijwerken

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Volgende stappen

* [Veilig experimenteren en dezicht in een virtueel netwerk](how-to-enable-virtual-network.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie oplossen](how-to-troubleshoot-deployment.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
