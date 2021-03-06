---
title: Zelf studie-Tags toevoegen aan resources in een sjabloon
description: Tags toevoegen aan resources die u in uw Azure Resource Manager-sjabloon implementeert. Met tags kunt u resources logisch indelen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3e0deb53e57cd29cbfce4c37f2d6c6729f15bebd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80411704"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Zelf studie: Tags toevoegen in uw ARM-sjabloon

In deze zelf studie leert u hoe u labels kunt toevoegen aan resources in uw Azure Resource Managerer-sjabloon (ARM). Met [Tags](../management/tag-resources.md) kunt u uw resources logisch indelen. De label waarden worden weer gegeven in kosten rapporten. Het volt ooien van deze zelf studie duurt **acht minuten** .

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over Quick](template-tutorial-quickstart-template.md)start-sjablonen te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de uitbrei ding Resource Manager tools en een Azure PowerShell of Azure CLI. Zie voor meer informatie [sjabloon hulpprogramma's](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sjabloon controleren

Met de vorige sjabloon is een opslag account, App Service plan en web-app geïmplementeerd.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Na de implementatie van deze resources moet u mogelijk de kosten bijhouden en resources zoeken die tot een categorie behoren. U kunt labels toevoegen om deze problemen op te lossen.

## <a name="add-tags"></a>Tags toevoegen

U labelt resources om waarden toe te voegen waarmee u het gebruik ervan kunt identificeren. U kunt bijvoorbeeld labels toevoegen die de omgeving en het project vermelden. U kunt Tags toevoegen die een kosten plaats identificeren of het team dat eigenaar is van de resource. Voeg waarden toe die zinvol zijn voor uw organisatie.

In het volgende voor beeld worden de wijzigingen in de sjabloon gemarkeerd. Kopieer het hele bestand en vervang de sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Sjabloon implementeren

Het is tijd om de sjabloon te implementeren en de resultaten te bekijken.

Als u de resource groep nog niet hebt gemaakt, raadpleegt u [resource groep maken](template-tutorial-create-first-template.md#create-resource-group). In het voor beeld wordt ervan uitgegaan dat u de **templateFile** -variabele hebt ingesteld op het pad naar het sjabloon bestand, zoals wordt weer gegeven in de [eerste zelf studie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatie opdracht wilt uitvoeren, moet u de [nieuwste versie](/cli/azure/install-azure-cli) van Azure cli hebben.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de switch **debug** met de implementatie opdracht om de logboeken voor fout opsporing weer te geven.  U kunt ook de **uitgebreide** switch gebruiken om de volledige logboeken voor fout opsporing weer te geven.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resource groep te verkennen van de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **resource groepen**in het menu links.
1. Selecteer de resource groep die u hebt geïmplementeerd.
1. Selecteer een van de resources, zoals de resource van het opslag account. U ziet dat deze nu labels bevat.

   ![Labels weer geven](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelf studie, hoeft u de resource groep niet te verwijderen.

Als u nu stopt, wilt u misschien de resources opschonen die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u tags toegevoegd aan de resources. In de volgende zelf studie leert u hoe u parameter bestanden kunt gebruiken om het door geven van waarden aan de sjabloon te vereenvoudigen.

> [!div class="nextstepaction"]
> [Parameter bestand gebruiken](template-tutorial-use-parameter-file.md)
