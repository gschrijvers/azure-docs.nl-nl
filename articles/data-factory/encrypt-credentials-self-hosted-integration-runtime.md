---
title: Referenties in Azure Data Factory versleutelen
description: Meer informatie over het versleutelen en opslaan van referenties voor uw on-premises gegevens archieven op een computer met een zelf-hostende Integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: cd775c5a3bf367600a4537a9409a9bb8f902f588
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628977"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Referenties voor on-premises gegevens archieven in Azure Data Factory versleutelen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

U kunt referenties voor uw on-premises gegevens archieven (gekoppelde services met gevoelige informatie) versleutelen en opslaan op een computer met zelf-hostende Integration runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U geeft een JSON-definitie bestand met referenties door aan de <br/>De cmdlet [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) voor het maken van een JSON-uitvoer bestand met de versleutelde referenties. Gebruik vervolgens de bijgewerkte JSON-definitie om de gekoppelde services te maken.

## <a name="author-sql-server-linked-service"></a>SQL Server gekoppelde service maken
Maak een JSON-bestand met de naam **SqlServerLinkedService. json** in een wille keurige map met de volgende inhoud:  

Vervang `<servername>`, `<databasename>` `<username>`, en `<password>` door de waarden voor uw SQL Server voordat u het bestand opslaat. En vervang `<integration runtime name>` door de naam van uw Integration runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Referenties versleutelen
Als u de gevoelige gegevens van de JSON-nettolading wilt versleutelen op een on-premises zelf-hostende Integration runtime, voert u **New-AzDataFactoryV2LinkedServiceEncryptedCredential**uit en geeft u de JSON-nettolading door. Deze cmdlet zorgt ervoor dat de referenties worden versleuteld met DPAPI en lokaal worden opgeslagen op het zelf-hostende Integration runtime-knoop punt. De uitvoer lading met de versleutelde verwijzing naar de referentie kan worden omgeleid naar een ander JSON-bestand (in dit geval ' encryptedLinkedService. json ').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>De JSON met versleutelde referenties gebruiken
Gebruik nu het JSON-bestand van de uitvoer van de vorige opdracht met de versleutelde referentie om de **SqlServerLinkedService**in te stellen.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Volgende stappen
Zie [beveiligings overwegingen voor gegevens verplaatsing](data-movement-security-considerations.md)voor meer informatie over de beveiligings overwegingen voor het verplaatsen van gegevens.

