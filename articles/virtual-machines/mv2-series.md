---
title: Mv2-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Mv2-serie.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 7df8dd439008258ea1b4986054660fb0fb9070ce
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204182"
---
# <a name="mv2-series"></a>Mv2-serie

De Mv2-serie biedt een platform met hoge door Voer en een laag latentie dat wordt uitgevoerd op een Hyper-Threaded Intel® Xeon® Platinum 8180M 2.5 GHz (Skylake)-processor met een basis frequentie van 2,5 GHz en een maximale Turbo frequentie van 3,8 GHz. Alle grootten voor virtuele machines uit de Mv2-serie kunnen zowel standaard als permanente schijven gebruiken. Instanties van de Mv2-serie zijn geoptimaliseerd voor geheugen die ongeëvenaarde reken prestaties bieden ter ondersteuning van grote data bases in het geheugen en workloads, met een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, grote caches en analyse in het geheugen.

Mv2-serie VM-functie Intel® Hyper-Threading-technologie

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

Write Accelerator: [ondersteund](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |

<sup>1</sup> virtuele machines uit de Mv2-serie zijn alleen van de tweede generatie en ondersteunen een subset van met generatie 2 ondersteunde installatie kopieën. Zie hieronder voor de volledige lijst met ondersteunde installatie kopieën voor Mv2-Series. Als u Linux gebruikt, raadpleegt u [ondersteuning voor virtuele machines van de tweede generatie op Azure](./linux/generation-2.md) voor instructies over het zoeken en selecteren van een installatie kopie. Als u Windows gebruikt, raadpleegt u [ondersteuning voor virtuele machines van de 2e generatie op Azure](./windows/generation-2.md) voor instructies over het zoeken en selecteren van een installatie kopie. 

- Windows Server 2019 of hoger
- SUSE Linux Enterprise Server 12 SP4 en hoger of SUSE Linux Enterprise Server 15 SP1 en hoger
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 of hoger 
- Oracle Enter prise Linux 7,7 of hoger



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
