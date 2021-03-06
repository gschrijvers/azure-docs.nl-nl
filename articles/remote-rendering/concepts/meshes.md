---
title: Meshes
description: De definitie van mazen in het bereik van Azure remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681764"
---
# <a name="meshes"></a>Meshes

## <a name="mesh-resource"></a>Netresource

Mazen zijn een onveranderbare [gedeelde bron](../concepts/lifetime.md)die alleen via [model conversie](../how-tos/conversion/model-conversion.md)kan worden gemaakt. Mazen bevatten een of meer *subnetten*. Elk subnet verwijst naar een [materiaal](materials.md) waarmee het standaard moet worden gerenderd. Als u een net in 3D-ruimte wilt plaatsen, voegt u een [MeshComponent](#meshcomponent) toe aan een [entiteit](entities.md).

### <a name="mesh-resource-properties"></a>Eigenschappen van mesh-bron

De `Mesh` klasse-eigenschappen zijn:

* **Materialen:** Een matrix van materialen. Elk materiaal wordt gebruikt door een ander subnet. Meerdere vermeldingen in de matrix kunnen verwijzen naar hetzelfde [materiaal](materials.md). Deze gegevens kunnen niet worden gewijzigd tijdens runtime.

* **Grenzen:** Een in de as uitgelijnde begrenzingsvak (AABB) van de mesh-hoek punten.

## <a name="meshcomponent"></a>MeshComponent

De `MeshComponent` klasse wordt gebruikt om een exemplaar van een netresource te plaatsen. Elke MeshComponent verwijst naar één net. Het kan overschrijven welke materialen worden gebruikt voor het weer geven van elk subnet.

### <a name="meshcomponent-properties"></a>MeshComponent-eigenschappen

* **Mesh:** De netresource die wordt gebruikt door dit onderdeel.

* **Materialen:** De matrix van materialen die zijn opgegeven in het netonderdeel zelf. De matrix heeft altijd dezelfde lengte als de *materiaal* matrix op de netresource. Materialen die niet worden overschreven van de mesh-standaard, worden in deze matrix ingesteld op *Null* .

* **UsedMaterials:** De matrix met werkelijk gebruikte materialen voor elk subnet. Is gelijk aan de gegevens in de *materiaal* matrix, voor waarden die niet null zijn. Anders bevat deze de waarde uit de *materiaal* matrix in de mesh-instantie.

## <a name="next-steps"></a>Volgende stappen

* [Materialen](materials.md)
