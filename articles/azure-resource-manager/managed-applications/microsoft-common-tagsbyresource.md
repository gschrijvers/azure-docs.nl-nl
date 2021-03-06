---
title: TagsByResource UI-element
description: Hierin wordt het micro soft. common. TagsByResource UI-element voor Azure Portal beschreven. Gebruiken om tags toe te passen op een resource tijdens de implementatie.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652201"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Micro soft. common. TagsByResource UI-element

Een besturings element voor het koppelen van [Tags](../management/tag-resources.md) aan de resources in een implementatie.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Opmerkingen

- Er moet ten minste één item `resources` in de matrix worden opgegeven.
- Elk element in `resources` moet een volledig gekwalificeerd resource type zijn. Deze elementen worden weer gegeven in de vervolg keuzelijst **resource** en worden taggable door de gebruiker.
- De uitvoer van het besturings element is opgemaakt voor eenvoudige toewijzing van label waarden in een Azure Resource Manager sjabloon. Als u de uitvoer van het besturings element in een sjabloon wilt ontvangen, moet u een para meter in uw sjabloon toevoegen, zoals wordt weer gegeven in het volgende voor beeld:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Wijs voor elke resource die kan worden gelabeld de eigenschap Tags toe aan de parameter waarde voor dat resource type:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Gebruik de [als](../templates/template-functions-logical.md#if) -functie bij het openen van de para meter tagsByResource. U kunt hiermee een leeg object toewijzen wanneer er geen labels aan het opgegeven resource type zijn toegewezen.

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
- Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
