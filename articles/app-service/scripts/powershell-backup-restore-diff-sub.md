---
title: 'Power shell: back-up terugzetten naar een ander abonnement'
description: Meer informatie over het gebruik van Azure PowerShell voor het automatiseren van de implementatie en het beheer van App Service. In dit voor beeld ziet u hoe u een back-up herstelt in een ander abonnement.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 4a22148d4151fd3c80c7be439b5dd281200e5456
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80044577"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Een web-app terugzetten vanuit een back-up in een ander abonnement met behulp van PowerShell

Met dit voorbeeldscript haalt u een eerder voltooide back-up van een bestaande web-app op en zet u deze terug naar een web-app in een ander abonnement. 

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/overview) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure. 

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Als u de web-app niet meer nodig hebt, gebruikt u de volgende opdracht om de resourcegroep, web-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Hiermee voegt u een geverifieerd account om te gebruiken voor aanvragen van Azure Resource Manager-cmdlets.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Hiermee vraagt u een lijst met back-ups op voor een web-app. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een web-app |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Hiermee zet u een web-app terug vanuit een eerder voltooide back-up. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
