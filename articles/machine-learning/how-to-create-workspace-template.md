---
title: Een werk ruimte met Azure Resource Manager sjabloon maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een Azure Resource Manager sjabloon voor het maken van een nieuwe Azure Machine Learning-werk ruimte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: eae10b7ae8cd14fd120e969c39c05a8ba2525003
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681540"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Een Azure Resource Manager sjabloon gebruiken om een werk ruimte te maken voor Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

In dit artikel leert u verschillende manieren om een Azure Machine Learning-werk ruimte te maken met behulp van Azure Resource Manager sjablonen. Met een resource manager-sjabloon kunt u eenvoudig resources maken als één gecoördineerde bewerking. Een sjabloon is een JSON-document waarmee de benodigde resources voor een implementatie worden gedefinieerd. Het kan ook implementatie parameters opgeven. Para meters worden gebruikt om invoer waarden op te geven wanneer u de sjabloon gebruikt.

Zie [een toepassing implementeren met Azure Resource Manager sjabloon](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Als u een sjabloon van een CLI wilt gebruiken, moet u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) of de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-sjabloon

De volgende Resource Manager-sjabloon kan worden gebruikt om een Azure Machine Learning-werk ruimte en gekoppelde Azure-resources te maken:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Met deze sjabloon worden de volgende Azure-Services gemaakt:

* Azure-resource groep
* Azure Storage-account
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-werkruimte

De resource groep is de container waarin de services worden bewaard. De verschillende services zijn vereist voor de Azure Machine Learning-werk ruimte.

De voorbeeld sjabloon heeft twee para meters:

* De **locatie** waar de resource groep en services worden gemaakt.

    De sjabloon maakt gebruik van de locatie die u selecteert voor de meeste resources. De uitzonde ring is de Application Insights-service, die niet beschikbaar is op alle locaties die de andere services zijn. Als u een locatie selecteert waar deze niet beschikbaar is, wordt de service gemaakt op de locatie Zuid-Centraal vs.

* De **naam van de werk ruimte**, de beschrijvende naam van de werk ruimte Azure machine learning.

    > [!NOTE]
    > De naam van de werk ruimte is niet hoofdletter gevoelig.

    De namen van de andere services worden wille keurig gegenereerd.

> [!TIP]
> Terwijl de sjabloon die aan dit document is gekoppeld, een nieuwe Azure Container Registry maakt, kunt u ook een nieuwe werk ruimte maken zonder container register te maken. Er wordt een gemaakt wanneer u een bewerking uitvoert waarvoor een container register is vereist. Bijvoorbeeld training of implementatie van een model.
>
> U kunt ook verwijzen naar een bestaand container register of opslag account in de Azure Resource Manager-sjabloon in plaats van een nieuwe te maken.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Zie de volgende artikelen voor meer informatie over sjablonen:

* [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)
* [Een toepassing implementeren met Azure Resource Manager sjablonen](../azure-resource-manager/templates/deploy-powershell.md)
* [Resource typen van micro soft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Geavanceerde sjabloon

In de volgende voorbeeld sjabloon ziet u hoe u een werk ruimte met drie instellingen maakt:

* Hoog vertrouwelijkheids instellingen voor de werk ruimte inschakelen
* Versleuteling inschakelen voor de werk ruimte
* Maakt gebruik van een bestaande Azure Key Vault voor het ophalen van door de klant beheerde sleutels

Zie [versleuteling bij rest](concept-enterprise-security.md#encryption-at-rest)voor meer informatie.

> [!IMPORTANT]
> Er zijn enkele specifieke vereisten waaraan uw abonnement moet voldoen voordat u deze sjabloon gebruikt:
> * De __Azure machine learning__ toepassing moet een __bijdrager__ zijn voor uw Azure-abonnement.
> * U moet een bestaande Azure Key Vault hebben die een versleutelings sleutel bevat.
> * U moet een toegangs beleid hebben in de Azure Key Vault waarmee u de toegang tot de __Azure Cosmos DB__ toepassing __krijgt__, __verpakt__en __terugloopt__ .
> * De Azure Key Vault moet zich in dezelfde regio bevinden waar u van plan bent de Azure Machine Learning-werk ruimte te maken.

Gebruik de volgende opdrachten __om de Azure machine learning-app als Inzender toe te voegen__:

1. Als u wilt verifiëren bij Azure vanuit de CLI, gebruikt u de volgende opdracht:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Gebruik de volgende opdracht om de object-ID van de Azure Machine Learning-app op te halen. De waarde kan verschillen voor elk van uw Azure-abonnementen:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Met deze opdracht wordt de object-ID geretourneerd. Dit is een GUID.

1. Als u de object-ID als Inzender wilt toevoegen aan uw abonnement, gebruikt u de volgende opdracht. Vervang door `<object-ID>` de GUID uit de vorige stap. Vervang door `<subscription-ID>` de naam of id van uw Azure-abonnement:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Als u een sleutel aan uw Azure Key Vault wilt toevoegen__, gebruikt u de informatie in de sectie [een sleutel, geheim of certificaat toevoegen aan de sleutel kluis](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) van het artikel __Manage Key Vault using Azure cli__ .

__Gebruik de volgende opdrachten om een toegangs beleid toe te voegen aan de sleutel kluis__:

1. Gebruik de volgende opdracht om de object-ID van de Azure Cosmos DB-app op te halen. De waarde kan verschillen voor elk van uw Azure-abonnementen:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Met deze opdracht wordt de object-ID geretourneerd. Dit is een GUID.

1. Als u het beleid wilt instellen, gebruikt u de volgende opdracht. Vervang door `<keyvault-name>` de naam van de bestaande Azure Key Vault. Vervang door `<object-ID>` de GUID uit de vorige stap:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Als u de waarden__ voor de `cmk_keyvault` (ID van de Key Vault) en de `resource_cmk_uri` para meters (Key URI) wilt ophalen die nodig zijn voor deze sjabloon, gebruikt u de volgende stappen:

1. Als u de Key Vault-ID wilt ophalen, gebruikt u de volgende opdracht:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault` .

1. Als u de waarde voor de URI voor de door de klant beheerde sleutel wilt ophalen, gebruikt u de volgende opdracht:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` .

__Voorbeeld sjabloon__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Als een werk ruimte eenmaal is gemaakt, kunt u de instellingen voor vertrouwelijke gegevens, versleuteling, sleutel kluis-ID of sleutel-id's niet wijzigen. Als u deze waarden wilt wijzigen, moet u een nieuwe werk ruimte maken met behulp van de nieuwe waarden.

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

1. Volg de stappen in [resources implementeren vanuit aangepaste sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Wanneer u het scherm __sjabloon bewerken__ aankomt, plakt u de sjabloon uit dit document.
1. Selecteer __Opslaan__ om de sjabloon te gebruiken. Geef de volgende informatie op en ga akkoord met de vermelde voor waarden:

   * Abonnement: Selecteer het Azure-abonnement dat u wilt gebruiken voor deze resources.
   * Resource groep: Selecteer of maak een resource groep om de services te bevatten.
   * Werkruimte naam: de naam die moet worden gebruikt voor de Azure Machine Learning werk ruimte die wordt gemaakt. De naam van de werk ruimte moet tussen de 3 en 33 tekens lang zijn. De naam mag alleen alfanumerieke tekens en '-' bevatten.
   * Locatie: Selecteer de locatie waar de resources worden gemaakt.

Zie [resources implementeren vanuit een aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)voor meer informatie.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

In dit voor beeld wordt ervan uitgegaan dat u de sjabloon hebt opgeslagen in een bestand met `azuredeploy.json` de naam in de huidige map:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) en een [privé Resource Manager-sjabloon met SAS-token en Azure PowerShell implementeren](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In dit voor beeld wordt ervan uitgegaan dat u de sjabloon hebt opgeslagen in een bestand met `azuredeploy.json` de naam in de huidige map:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../azure-resource-manager/templates/deploy-cli.md) en een [persoonlijke Resource Manager-sjabloon implementeren met SAS-token en Azure cli](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten van de resource provider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault toegangs beleid en Azure Resource Manager sjablonen

Wanneer u een Azure Resource Manager sjabloon gebruikt om de werk ruimte en de gekoppelde resources (inclusief Azure Key Vault) meerdere keren te maken. Gebruik bijvoorbeeld de sjabloon meerdere keren met dezelfde para meters als onderdeel van een continue integratie-en implementatie pijplijn.

De meeste bewerkingen voor het maken van resources via sjablonen zijn idempotent, maar Key Vault wist het toegangs beleid telkens wanneer de sjabloon wordt gebruikt. Als u het toegangs beleid wist, wordt de toegang tot de Key Vault verbroken voor een bestaande werk ruimte waarin deze wordt gebruikt. Bijvoorbeeld: stop/Create-functionaliteit van Azure Notebooks VM kan mislukken.  

Om dit probleem te voor komen, raden we u aan een van de volgende benaderingen te volgen:

* Implementeer de sjabloon niet meer dan één keer voor dezelfde para meters. Of verwijder de bestaande resources voordat u de sjabloon opnieuw maakt.

* Controleer de Key Vault toegangs beleid en gebruik vervolgens dit beleid om de `accessPolicies` eigenschap van de sjabloon in te stellen. Gebruik de volgende Azure CLI-opdracht om het toegangs beleid weer te geven:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    `accessPolicies`Zie de [AccessPolicyEntry-object verwijzing](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)voor meer informatie over het gebruik van de sectie van de sjabloon.

* Controleer of de Key Vault resource al bestaat. Als dit het geval is, moet u het niet opnieuw maken via de sjabloon. Als u bijvoorbeeld de bestaande Key Vault wilt gebruiken in plaats van een nieuwe te maken, moet u de volgende wijzigingen aanbrengen in de sjabloon:

    * **Voeg** een para meter toe die de id van een bestaande Key Vault resource accepteert:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Verwijder** de sectie waarmee een Key Vault resource wordt gemaakt:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Verwijder** de `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` regel uit de `dependsOn` sectie van de werk ruimte. **Wijzig** ook de `keyVault` vermelding in de `properties` sectie van de werk ruimte om te verwijzen naar de `keyVaultId` para meter:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Nadat u deze wijzigingen hebt aangebracht, kunt u de ID van de bestaande Key Vault resource opgeven wanneer u de sjabloon uitvoert. Vervolgens wordt de Key Vault door de sjabloon opnieuw gebruikt door de `keyVault` eigenschap van de werk ruimte in te stellen op de bijbehorende id.

    Als u de ID van de Key Vault wilt ophalen, kunt u verwijzen naar de uitvoer van de oorspronkelijke sjabloon run of de Azure CLI gebruiken. De volgende opdracht is een voor beeld van het gebruik van de Azure CLI om de Key Vault Resource-ID op te halen:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Volgende stappen

* [Resources implementeren met Resource Manager-sjablonen en Resource Manager-rest API](../azure-resource-manager/templates/deploy-rest.md).
* [Azure-resource groepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
