---
title: Resources implementeren met REST API en sjabloon
description: Gebruik Azure Resource Manager en Resource Manager-REST API om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: d7865ac6f9b2bb176ea5308e326dec0741a80962
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723116"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Resources implementeren met ARM-sjablonen en Resource Manager-REST API

In dit artikel wordt uitgelegd hoe u de sjablonen van Resource Manager REST API met Azure Resource Manager (ARM) kunt gebruiken om uw resources te implementeren in Azure.

U kunt uw sjabloon insluiten in de hoofd tekst van de aanvraag of een koppeling naar een bestand. Wanneer u een bestand gebruikt, kan dit een lokaal bestand zijn of een extern bestand dat beschikbaar is via een URI. Als uw sjabloon zich in een opslag account bevindt, kunt u de toegang tot de sjabloon beperken en een SAS-token (Shared Access Signature) opgeven tijdens de implementatie.

## <a name="deployment-scope"></a>Implementatie bereik

U kunt uw implementatie richten op een beheer groep, een Azure-abonnement of een resource groep. In de meeste gevallen streeft u implementaties naar een resource groep. Beheer groep-of abonnements implementaties gebruiken om beleid en roltoewijzingen toe te passen binnen het opgegeven bereik. U kunt ook abonnements implementaties gebruiken voor het maken van een resource groep en het implementeren van resources. Afhankelijk van het bereik van de implementatie, gebruikt u verschillende opdrachten.

* Gebruik [implementaties-maken](/rest/api/resources/deployments/createorupdate)om te implementeren in een **resource groep**. De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

* Als u wilt implementeren in een **abonnement**, gebruikt u [implementaties-maken bij abonnements bereik](/rest/api/resources/deployments/createorupdateatsubscriptionscope). De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie over implementaties op abonnements niveau.

* Als u wilt implementeren in een **beheer groep**, gebruikt u [implementaties-maken voor het bereik van de beheer groep](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  Zie [resources maken op het niveau van de beheer groep](deploy-to-management-group.md)voor meer informatie over implementaties op het niveau van beheer groepen.

* Als u wilt implementeren in een **Tenant**, gebruikt u [implementaties-maken of bijwerken in het Tenant bereik](/rest/api/resources/deployments/createorupdateattenantscope). De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  Zie [resources maken op Tenant niveau](deploy-to-tenant.md)voor meer informatie over implementaties op Tenant niveau.

In de voor beelden in dit artikel worden de implementaties van resource groepen gebruikt.

## <a name="deploy-with-the-rest-api"></a>Implementeren met de REST-API

1. [Algemene para meters en kopteksten](/rest/api/azure/)instellen, inclusief verificatie tokens.

1. Als u geen bestaande resource groep hebt, maakt u een resource groep. Geef uw abonnements-ID, de naam van de nieuwe resource groep en de locatie op die u nodig hebt voor uw oplossing. Zie [een resource groep maken](/rest/api/resources/resourcegroups/createorupdate)voor meer informatie.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-10-01
   ```

   Met een aanvraag tekst zoals:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Valideer uw implementatie voordat u deze uitvoert door de implementatie bewerking voor het [valideren van een sjabloon](/rest/api/resources/deployments/validate) uit te voeren. Wanneer u de implementatie test, geeft u de para meters op exact dezelfde manier op als bij het uitvoeren van de implementatie (Zie de volgende stap).

1. Als u een sjabloon wilt implementeren, geeft u uw abonnements-ID, de naam van de resource groep, de naam van de implementatie op in de aanvraag-URI.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   Geef in de hoofd tekst van de aanvraag een koppeling op naar uw sjabloon en parameter bestand. Zie voor meer informatie over het parameter bestand [Resource Manager-parameter bestand maken](parameter-files.md).

   U ziet dat de **modus** is ingesteld op **Incrementeel**. Stel de **modus** in op **voltooid**als u een volledige implementatie wilt uitvoeren. Wees voorzichtig met het gebruik van de volledige modus, omdat u per ongeluk resources kunt verwijderen die zich niet in uw sjabloon bevinden.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Als u de respons inhoud wilt registreren, moet u de inhoud van de aanvraag, of beide, de **debugSetting** toevoegen.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    U kunt uw opslag account instellen om een SAS-token (Shared Access Signature) te gebruiken. Zie [toegang overdragen met een Shared Access Signature](/rest/api/storageservices/delegating-access-with-a-shared-access-signature)voor meer informatie.

    Als u een gevoelige waarde voor een para meter (bijvoorbeeld een wacht woord) moet opgeven, voegt u die waarde toe aan een sleutel kluis. Haal de sleutel kluis op tijdens de implementatie, zoals wordt weer gegeven in het vorige voor beeld. Zie [veilige waarden door geven tijdens de implementatie](key-vault-parameter.md)voor meer informatie.

1. In plaats van te koppelen aan bestanden voor de sjabloon en para meters, kunt u deze in de hoofd tekst van de aanvraag toevoegen. In het volgende voor beeld wordt de hoofd tekst van de aanvraag met de sjabloon en de parameter inline weer gegeven:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Gebruik [implementaties-ophalen](/rest/api/resources/deployments/get)om de status van de sjabloon implementatie te verkrijgen.

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="next-steps"></a>Volgende stappen

- Als u wilt terugkeren naar een geslaagde implementatie wanneer u een fout krijgt, raadpleegt u [herstellen bij fout naar geslaagde implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [asynchrone Azure-bewerkingen volgen](../management/async-operations.md)voor meer informatie over het verwerken van asynchrone rest-bewerkingen.
- Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over sjablonen.

