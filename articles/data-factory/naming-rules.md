---
title: Regels voor het benoemen van Azure Data Factory entiteiten
description: Hierin worden de naamgevings regels voor Data Factory entiteiten beschreven.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: f922ada663391cf65a61f4e18bba53668f9c4a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419405"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory naamgevings regels

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

De volgende tabel bevat naamgevings regels voor Data Factory artefacten.

| Naam | Unieke naam | Validatie controles |
|:--- |:--- |:--- |
| Data Factory |Uniek in Microsoft Azure. Namen zijn niet hoofdletter gevoelig, dat wil zeggen, `MyDF` en `mydf` verwijzen naar dezelfde Data Factory. |<ul><li>Elk data factory is gekoppeld aan precies één Azure-abonnement.</li><li>Object namen moeten beginnen met een letter of een cijfer en mogen alleen letters, cijfers en het koppel teken (-) bevatten.</li><li>Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. Opeenvolgende streepjes zijn niet toegestaan in container namen.</li><li>De naam kan 3-63 tekens lang zijn.</li></ul> |
| Gekoppelde Services/gegevens sets/pijp lijnen |Uniek met in een data factory. Namen zijn niet hoofdletter gevoelig. |<ul><li>Object namen moeten beginnen met een letter, cijfer of onderstrepings teken (_).</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ',\\' '</li><li>Streepjes (-) zijn niet toegestaan in de namen van gekoppelde services en alleen van gegevens sets.</li></ul>  |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn niet hoofdletter gevoelig. | Zie [Azure-naamgevings regels en-beperkingen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)voor meer informatie. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het maken van gegevens fabrieken met behulp van stapsgewijze instructies in [Quick Start: een Data Factory](quickstart-create-data-factory-powershell.md) -artikel maken. 
