---
title: Wat zijn reken doelen?
titleSuffix: Azure Machine Learning
description: Bepaal waar u uw model wilt trainen of implementeren met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: ed65d69c18f2dbcd53324fe3cc18af8c51c546b2
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780110"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Wat zijn Compute-doelen in Azure Machine Learning? 

Een **Compute-doel** is een aangewezen reken resource/omgeving waar u uw trainings script uitvoert of uw service-implementatie host. Deze locatie kan uw lokale machine of een cloud-gebaseerde reken resource zijn. Met Compute-doelen kunt u op een later tijdstip uw berekenings omgeving wijzigen zonder dat u de code hoeft te wijzigen.  

In een typische levens cyclus voor model ontwikkeling kunt u het volgende doen:
1. Begin met het ontwikkelen en experimenteren van een kleine hoeveelheid gegevens. In deze fase raden wij uw lokale omgeving (lokale computer of Cloud-VM) aan als uw reken doel. 
2. Schaal omhoog naar grotere gegevens of voer gedistribueerde training uit met behulp van een van deze [trainings Compute-doelen](#train).  
3. Zodra uw model klaar is, implementeert u het in een webhost of IoT-apparaat met een van deze doelen voor de [implementatie](#deploy).

De reken resources die u voor uw reken doelen gebruikt, worden aan een [werk ruimte](concept-workspace.md)gekoppeld. Andere reken bronnen dan de lokale computer worden gedeeld door gebruikers van de werk ruimte.

## <a name="training-compute-targets"></a><a name="train"></a>Doelen van de trainings compute

Azure Machine Learning heeft verschillende ondersteuning voor verschillende reken bronnen.  U kunt ook uw eigen reken resource toevoegen, maar ondersteuning voor verschillende scenario's kan variëren.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Meer informatie over [het instellen en gebruiken van een berekenings doel voor model training](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Implementatie doelen

De volgende reken bronnen kunnen worden gebruikt voor het hosten van uw model implementatie.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Meer informatie over [waar en hoe u uw model implementeert in een compute-doel](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (beheerd)

Een beheerde Compute-resource wordt gemaakt en beheerd door Azure Machine Learning. Deze berekening is geoptimaliseerd voor machine learning workloads. Azure Machine Learning compute-clusters en [reken instanties](concept-compute-instance.md) zijn de enige beheerde reken processen. Extra beheerde reken bronnen kunnen in de toekomst worden toegevoegd.

U kunt Azure Machine Learning Compute instances (preview) of reken clusters maken van:
* Azure Machine Learning Studio
* Azure Portal
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) -en [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) -klassen
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Resource Manager-sjabloon

U kunt ook reken clusters maken met behulp [van de extensie machine learning voor de Azure cli](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training).

Wanneer u deze reken resources maakt, maakt u automatisch deel uit van uw werk ruimte, in tegens telling tot andere soorten Compute-doelen.

### <a name="compute-clusters"></a>Reken clusters

U kunt Azure Machine Learning compute-clusters gebruiken voor training en voor het afleiden van batches (preview-versie).  Met deze reken resource hebt u het volgende:

* Cluster met één of meerdere knoop punten
* Automatisch schalen telkens wanneer u een uitvoering verzendt 
* Automatische Cluster beheer en taak planning 
* Ondersteuning voor zowel CPU-als GPU-resources

### <a name="supported-vm-series-and-sizes"></a>Ondersteunde VM-reeksen en-groottes

Wanneer u de grootte van een knoop punt selecteert voor een beheerde Compute-resource in Azure Machine Learning, kunt u kiezen uit beschik bare VM-grootten in Azure. Azure biedt tal van grootten voor Linux en Windows voor verschillende werk belastingen. Hier vindt u meer informatie over de verschillende [VM-typen en-grootten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

Er zijn enkele uitzonde ringen en beperkingen voor het kiezen van een VM-grootte:
* Sommige VM-reeksen worden niet ondersteund in Azure Machine Learning.
* Sommige VM-reeksen zijn beperkt. Als u een beperkte serie wilt gebruiken, neemt u contact op met de ondersteuning en vraagt u een quotum toename aan te vragen voor de serie. Zie [ondersteunings opties voor Azure](https://azure.microsoft.com/support/options/) voor meer informatie over contact opnemen met de ondersteuning.

Raadpleeg de volgende tabel voor meer informatie over ondersteunde reeksen en beperkingen. 

| **Ondersteunde VM-serie**  | **Beperkingen** |
|------------|------------|
| D | Geen |
| Dv2 | Geen |  
| DSv2 | Geen |  
| FSv2 | Geen |  
| M | Goedkeuring vereist |
| NC | Geen |    
| NCsv2 | Goedkeuring vereist |
| NCsv3 | Goedkeuring vereist |  
| NDs | Goedkeuring vereist |
| NDv2 | Goedkeuring vereist |
| NV | Geen |
| NVv3 | Goedkeuring vereist | 


Hoewel Azure Machine Learning deze VM-serie ondersteunt, zijn ze mogelijk niet beschikbaar in alle Azure-regio's. U kunt controleren of er VM-serie beschikbaar is: [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Onbeheerde compute

Een niet-beheerd reken doel wordt *niet* beheerd door Azure machine learning. U maakt dit type reken doel buiten Azure Machine Learning en koppelt het vervolgens aan uw werk ruimte. Onbeheerde reken resources kunnen extra stappen vereisen om de prestaties van machine learning werk belastingen te hand haven of te verbeteren.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:
* [Een reken doel instellen om uw model te trainen](how-to-set-up-training-targets.md)
* [Uw model implementeren naar een berekenings doel](how-to-deploy-and-where.md)
