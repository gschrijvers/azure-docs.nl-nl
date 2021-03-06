---
title: Zelf studie-variabele aan sjabloon toevoegen
description: Voeg variabelen toe aan uw Azure Resource Manager sjabloon om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b1df86e5b593edec784de21e21a4399274d820bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80411691"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Zelf studie: variabelen toevoegen aan uw ARM-sjabloon

In deze zelf studie leert u hoe u een variabele kunt toevoegen aan de sjabloon Azure Resource Manager (ARM). Variabelen vereenvoudigen uw sjablonen door u in te scha kelen een expressie één keer te schrijven en opnieuw te gebruiken in de sjabloon. Het volt ooien van deze zelf studie duurt **zeven minuten** .

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over functies](template-tutorial-add-functions.md)uit te voeren, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelf studie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

De para meter voor de naam van het opslag account is moeilijk te gebruiken omdat u een unieke naam moet opgeven. Als u de eerdere zelf studies in deze serie hebt voltooid, is het waarschijnlijk een unieke naam te raden. U lost dit probleem op door een variabele toe te voegen die een unieke naam voor het opslag account bouwt.

## <a name="use-variable"></a>Variabele gebruiken

In het volgende voor beeld worden de wijzigingen gemarkeerd om een variabele aan uw sjabloon toe te voegen waarmee een unieke opslag accountnaam wordt gemaakt. Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

U ziet dat het een variabele bevat met de naam **uniqueStorageName**. Deze variabele maakt gebruik van vier functies om een teken reeks waarde te maken.

U bent al bekend met de functie [para meters](template-functions-deployment.md#parameters) , dus we onderzoeken deze niet.

U bent ook bekend met de functie [resourceGroup](template-functions-resource.md#resourcegroup) . In dit geval haalt u de eigenschap **id** op in plaats van de eigenschap **Location** , zoals wordt weer gegeven in de vorige zelf studie. De eigenschap **id** retourneert de volledige id van de resource groep, met inbegrip van de abonnements-id en de naam van de resource groep.

Met de functie [Unique string](template-functions-string.md#uniquestring) maakt u een hash-waarde van 13 tekens. De geretourneerde waarde wordt bepaald door de para meters die u doorgeeft. Voor deze zelf studie gebruikt u de ID van de resource groep als invoer voor de hash-waarde. Dit betekent dat u deze sjabloon kunt implementeren in verschillende resource groepen en een andere unieke teken reeks waarde kunt krijgen. U krijgt echter dezelfde waarde als u implementeert in dezelfde resource groep.

De functie [concat](template-functions-string.md#concat) heeft waarden en combineert deze. Voor deze variabele wordt de teken reeks uit de para meter en de teken reeks uit de Unique string-functie gebruikt en gecombineerd tot één teken reeks.

Met de para meter **storagePrefix** kunt u een voor voegsel door geven dat u helpt bij het identificeren van opslag accounts. U kunt uw eigen naamgevings Conventie maken die het eenvoudiger maakt om opslag accounts te identificeren na de implementatie vanuit een lange lijst met resources.

Ten slotte ziet u dat de opslag naam nu is ingesteld op de variabele in plaats van een para meter.

## <a name="deploy-template"></a>Sjabloon implementeren

We gaan de sjabloon implementeren. Het implementeren van deze sjabloon is eenvoudiger dan de vorige sjablonen omdat u alleen het voor voegsel voor de opslag naam opgeeft.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatie opdracht wilt uitvoeren, moet u de [nieuwste versie](/cli/azure/install-azure-cli) van Azure cli hebben.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de switch **debug** met de implementatie opdracht om de logboeken voor fout opsporing weer te geven.  U kunt ook de **uitgebreide** switch gebruiken om de volledige logboeken voor fout opsporing weer te geven.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resource groep te verkennen van de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu links.
1. Selecteer de resource groep die u hebt geïmplementeerd.
1. U ziet dat er een opslag account resource is geïmplementeerd. De naam van het opslag account is **Store** plus een teken reeks van wille keurige tekens.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een variabele toegevoegd waarmee een unieke naam voor een opslag account wordt gemaakt. In de volgende zelf studie retourneert u een waarde uit het geïmplementeerde opslag account.

> [!div class="nextstepaction"]
> [Uitvoer toevoegen](template-tutorial-add-outputs.md)
