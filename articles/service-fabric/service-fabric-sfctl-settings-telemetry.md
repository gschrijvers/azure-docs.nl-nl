---
title: Azure Service Fabric CLI-sfctl-instellingen telemetrie
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het configureren van sfctl-telemetrie.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76903026"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Telemetrie-instellingen configureren die lokaal zijn voor dit exemplaar van sfctl.

Sfctl-telemetrie verzamelt opdracht naam zonder opgegeven para meters of hun waarden, Sfctl versie, type besturings systeem, python-versie, het slagen of mislukken van de opdracht, het fout bericht dat wordt geretourneerd.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| set-telemetrie | Telemetrie in-of uitschakelen. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-instellingen telemetrie-set-telemetrie
Telemetrie in-of uitschakelen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --uit | Schakel telemetrie uit. |
| --aan | Schakel telemetrie in. Dit is de standaardwaarde. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

### <a name="examples"></a>Voorbeelden

Schakel telemetrie uit.

```
sfctl settings telemetry set_telemetry --off
```

Schakel telemetrie in.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).