---
title: Quick Start-Azure Key Vault-client bibliotheek voor python
description: Meer informatie over het maken, ophalen en verwijderen van sleutels van een Azure-sleutel kluis met behulp van de python-client bibliotheek
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 663d219e73d8b3de001e02d465016f45974855bb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982255"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Snelstartgids: Azure Key Vault-client bibliotheek voor python

Ga aan de slag met de Azure Key Vault-client bibliotheek voor python. Volg de onderstaande stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-client bibliotheek voor python voor het volgende:

- Verbeter de beveiliging en controle over sleutels en wacht woorden.
- Versleutelings sleutels maken en importeren in een paar minuten.
- Verminder de latentie met Cloud schaal en wereld wijde redundantie.
- Vereenvoudig en Automatiseer taken voor TLS/SSL-certificaten.
- Gebruik FIPS 140-2 level 2 Validated Hsm's.

[Naslag informatie over](/python/api/overview/azure/key-vault?view=azure-python) | API-referentie[bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [pakket (python-pakket index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 of hoger
- [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

In deze Snelstartgids wordt ervan uitgegaan dat u [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) uitvoert in een Linux-Terminal venster.

## <a name="setting-up"></a>Instellen

### <a name="install-the-package"></a>Het pakket installeren

Installeer de Azure Key Vault sleutels bibliotheek voor python in het console venster.

```console
pip install azure-keyvault-keys
```

Voor deze Quick start moet u ook het Azure. Identity-pakket installeren:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resource groep en sleutel kluis maken

In deze Snelstartgids wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutel kluis. U kunt een sleutel kluis maken met behulp van de stappen in de [Snelstartgids van Azure cli](quick-create-cli.md), [Azure PowerShell Snelstartgids](quick-create-powershell.md)of [Azure Portal Quick](quick-create-portal.md)start. U kunt ook de volgende Azure CLI-opdrachten uitvoeren.

> [!Important]
> Elke sleutel kluis moet een unieke naam hebben. Vervang <uw-unieke kluis naam> door de naam van uw sleutel kluis in de volgende voor beelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een op de cloud gebaseerde .NET-toepassing te verifiëren, is met een beheerde identiteit. Zie [een beheerde app Service-id gebruiken voor toegang tot Azure Key Vault](../general/managed-identity.md) voor meer informatie. 

In het omwille van de eenvoud maakt deze Snelstartgids echter een bureaublad toepassing, waarvoor het gebruik van een Service-Principal en een beleid voor toegangs beheer vereist is. Voor uw service-principal is een unieke naam vereist in de&lt;indeling ' http://My-Unique-service-&gt;Principle-Name '.

Maak een service-principal met behulp van de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principle-name&gt;" --sdk-auth
```

Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Noteer de clientId-en clientSecret, aangezien we deze gebruiken in de stap [omgevings variabele instellen](#set-environmental-variables) hieronder.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De Service-Principal toegang verlenen tot uw sleutel kluis

Maak een toegangs beleid voor de sleutel kluis die machtigingen verleent aan uw Service-Principal door de clientId door te geven aan de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Geef de Service-Principal Get-, List-en Create-machtigingen voor sleutels.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Omgevings variabelen instellen

De methode DefaultAzureCredential in onze toepassing is afhankelijk van drie omgevings variabelen `AZURE_CLIENT_ID`: `AZURE_CLIENT_SECRET`, en `AZURE_TENANT_ID`. Stel deze variabelen in op de clientId-, clientSecret-en tenantId-waarden die u hebt genoteerd in de `export VARNAME=VALUE` stap [een service-principal maken](#create-a-service-principal) met behulp van de indeling. (Met deze methode worden alleen de variabelen ingesteld voor uw huidige shell en processen die zijn gemaakt op basis van de shell; als u deze variabelen permanent `/etc/environment ` wilt toevoegen aan uw omgeving, bewerkt u het bestand.) 

U moet de naam van de sleutel kluis ook opslaan als een omgevings variabele `KEY_VAULT_NAME`genaamd.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Object model

Met de Azure Key Vault-client bibliotheek voor python kunt u sleutels en gerelateerde assets beheren, zoals certificaten en geheimen. In de onderstaande code voorbeelden ziet u hoe u een client maakt, een sleutel maakt, een sleutel ophaalt en een sleutel verwijdert.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

Het verifiëren van uw sleutel kluis en het maken van een sleutel kluis-client is afhankelijk van de omgevings variabelen in de bovenstaande stap [omgevings variabelen instellen](#set-environmental-variables) . De naam van de sleutel kluis wordt uitgebreid naar de sleutel kluis-URI, in de indeling ' https://<Your-Key-kluis-name>. vault.azure.net '.

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Een sleutel opslaan

Nu uw toepassing is geverifieerd, kunt u een sleutel in uw-kluis plaatsen 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

U kunt controleren of de sleutel is ingesteld met de opdracht [AZ sleutel kluis Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Een sleutel ophalen

U kunt nu de eerder gemaakte sleutel ophalen

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

Uw sleutel wordt nu opgeslagen als `retrieved_key`.

### <a name="delete-a-key"></a>Een sleutel verwijderen

Ten slotte gaan we de sleutel uit uw sleutel kluis verwijderen

```python
client.delete_key(keyName)
```

U kunt controleren of de sleutel is verdwenen met de opdracht [AZ sleutel kluis Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de Azure CLI of Azure PowerShell gebruiken om uw sleutel kluis en de bijbehorende resource groep te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Voorbeeldcode

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een sleutel kluis gemaakt, een sleutel opgeslagen en die sleutel opgehaald. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- Een [overzicht van Azure Key Vault](../general/overview.md) lezen
- Raadpleeg de [Azure Key Vault hand leiding voor ontwikkel aars](../general/developers-guide.md)
- [Azure Key Vault aanbevolen procedures](../general/best-practices.md) controleren
