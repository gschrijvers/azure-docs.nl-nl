---
title: Door de gebruiker gedefinieerde functies in sjablonen
description: Hierin wordt beschreven hoe u door de gebruiker gedefinieerde functies definieert en gebruikt in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78943224"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Door de gebruiker gedefinieerde functies in Azure Resource Manager sjabloon

U kunt binnen uw sjabloon uw eigen functies maken. Deze functies zijn beschikbaar voor gebruik in uw sjabloon. Door de gebruiker gedefinieerde functies zijn gescheiden van de [standaard sjabloon functies](template-functions.md) die automatisch beschikbaar zijn in uw sjabloon. Maak uw eigen functies wanneer u gecompliceerde expressies hebt die herhaaldelijk worden gebruikt in uw sjabloon.

In dit artikel wordt beschreven hoe u door de gebruiker gedefinieerde functies in uw Azure Resource Manager-sjabloon kunt toevoegen.

## <a name="define-the-function"></a>Definieer de functie

Uw functies vereisen een naam ruimte waarde om naam conflicten met sjabloon functies te voor komen. In het volgende voor beeld ziet u een functie die een unieke naam retourneert:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Gebruik de functie

In het volgende voor beeld ziet u een sjabloon die een door de gebruiker gedefinieerde functie bevat. Deze functie wordt gebruikt om een unieke naam voor een opslag account op te halen. De sjabloon heeft een para meter met de naam **storageNamePrefix** die wordt door gegeven als een para meter voor de functie.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Beperkingen

Bij het definiëren van een gebruikers functie gelden enkele beperkingen:

* De functie heeft geen toegang tot variabelen.
* De functie kan alleen para meters gebruiken die in de functie zijn gedefinieerd. Wanneer u de functie [para meters](template-functions-deployment.md#parameters) in een door de gebruiker gedefinieerde functie gebruikt, bent u beperkt tot de para meters voor die functie.
* De functie kan geen andere door de gebruiker gedefinieerde functies aanroepen.
* De functie kan de functie [Reference](template-functions-resource.md#reference) of een van de [lijst](template-functions-resource.md#list) functies niet gebruiken.
* Para meters voor de functie kunnen geen standaard waarden hebben.


## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor door de gebruiker gedefinieerde functies.
* Zie [Azure Resource Manager-sjabloon functies](template-functions.md)voor een lijst met de beschik bare sjabloon functies.
