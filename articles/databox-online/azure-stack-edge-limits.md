---
title: Limieten voor Azure Stack rand | Microsoft Docs
description: Hierin worden systeem limieten en aanbevolen grootten voor de Azure Stack rand beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 4f7800efb5d4382e8d73c819d950fdfafd10f296
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569834"
---
# <a name="azure-stack-edge-limits"></a>Limieten voor Azure Stack Edge

Houd rekening met deze beperkingen wanneer u uw Microsoft Azure Stack EDGE-oplossing implementeert en gebruikt. 

## <a name="azure-stack-edge-service-limits"></a>Service limieten voor Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Limieten voor Azure Stack apparaten

In de volgende tabel worden de limieten voor het Azure Stack edge-apparaat beschreven. 

| Beschrijving | Waarde |
|---|---|
|Nee. bestanden per apparaat |100.000.000 |
|Nee. van shares per apparaat |24 |
|Nee. van shares per container |1 |
|De maximale bestands grootte die naar een share is geschreven| 5 TB |

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limieten voor Azure Storage-account en object grootte

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden om Azure Stack Edge te implementeren](azure-stack-edge-deploy-prep.md)
