---
title: Azure Data Lake-tools voor Visual Studio installeren
description: In dit artikel wordt beschreven hoe u Azure Data Lake-Hulpprogram Ma's voor Visual Studio installeert.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70914090"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools voor Visual Studio installeren

Meer informatie over het gebruik van Visual Studio voor het maken van Azure Data Lake Analytics-accounts. U kunt taken in [U-SQL](data-lake-analytics-u-sql-get-started.md) definiëren en taken verzenden naar de data Lake Analytics-service. Zie [Azure data Lake Analytics Overview](data-lake-analytics-overview.md)voor meer informatie over data Lake Analytics.

## <a name="prerequisites"></a>Vereisten

* **Visual Studio**: alle versies behalve Express worden ondersteund.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK voor .NET** versie 2.7.1 of hoger. U kunt dit installeren met het [webplatforminstallatieprogramma](https://www.microsoft.com/web/downloads/platform.aspx).
* Een **Data Lake Analytics** -account. Zie [Aan de slag met Azure Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md) om een account te maken.

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Azure Data Lake-Hulpprogram Ma's voor Visual Studio 2017 of Visual Studio 2019 installeren

Azure Data Lake-Hulpprogram Ma's voor Visual Studio worden ondersteund in Visual Studio 2017 15,3 of hoger. Het hulp programma maakt deel uit van de werk belastingen voor **gegevens opslag en-verwerking** en voor **Azure-ontwikkel aars** . Schakel een van deze twee workloads in als onderdeel van uw installatie van Visual Studio.

Schakel de werk belasting voor **gegevens opslag en-verwerking** in zoals weer gegeven:

![De werk belasting voor gegevens opslag en-verwerking inschakelen](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Schakel de werk belasting van **Azure Development** in zoals weer gegeven:

![Een Azure Development-workload selecteren](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Azure Data Lake Tools voor Visual Studio 2013 en 2015 installeren

Down load en Installeer [Microsoft Azure data Lake en stream Analytics Hulpprogram ma's voor Visual Studio ](https://aka.ms/adltoolsvs). Na de installatie heeft Visual Studio de volgende wijzigingen:

* Het **Server Explorer** > **Azure** -knoop punt bevat een **Data Lake Analytics** -knoop punt.
* Het menu **Extra** bevat een **Data Lake**-item.

## <a name="next-steps"></a>Volgende stappen

* Zie [toegang tot Diagnostische logboeken voor Azure data Lake Analytics voor](data-lake-analytics-diagnostic-logs.md)informatie over het vastleggen van diagnostische gegevens.
* Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
* Zie [de weer gave vertex Execution gebruiken in data Lake-Hulpprogram ma's voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)voor meer informatie over het gebruik van de vertex Execution-weer gave.
