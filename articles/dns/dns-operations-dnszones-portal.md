---
title: DNS-zones beheren in Azure DNS-Azure Portal | Microsoft Docs
description: U kunt DNS-zones beheren met behulp van de Azure Portal. In dit artikel wordt beschreven hoe u DNS-zones bijwerkt, verwijdert en maakt op Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76936792"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>DNS-zones beheren in de Azure Portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Zo](dns-operations-dnszones.md)
> * [Klassieke versie van Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure-CLI](dns-operations-dnszones-cli.md)

In dit artikel leest u hoe u uw DNS-zones beheert met behulp van de Azure Portal. U kunt ook uw DNS-zones beheren met behulp van de platformoverschrijdende [Azure cli](dns-operations-dnszones-cli.md) of de Azure [Power shell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

1. Aanmelden bij Azure Portal
2. Ga in het menu hub naar **een resource > netwerk > DNS-zone maken** om de Blade **DNS-zone maken** te openen.

    ![DNS-zone](./media/dns-operations-dnszones-portal/openzone650.png)

4. Voer op de blade **DNS-zone maken** de volgende waarden in en klik op **Maken**:


   | **Instelling** | **Waarde** | **Nadere** |
   |---|---|---|
   |**Naam**|contoso.com|De naam van de DNS-zone|
   |**Abonnement**|[Uw abonnement]|Selecteer een abonnement waarin de DNS-zone moet worden gemaakt.|
   |**Resourcegroep**|**Nieuwe maken:** contosoDNSRG|Maak een resourcegroep. De naam van de resourcegroep moet uniek zijn binnen het abonnement dat u hebt geselecteerd. Lees het artikel overzicht van [Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) voor meer informatie over resource groepen.|
   |**Locatie**|VS - west||

> [!NOTE]
> De resourcegroep verwijst naar de locatie van de resourcegroep en heeft geen invloed op de DNS-zone. De locatie van de DNS-zone is altijd 'global' en wordt niet weergegeven.

## <a name="list-dns-zones"></a>DNS-zones vermelden

Ga in het Azure Portal naar **meer services** > **netwerken** > **DNS zones**. Elke DNS-zone is een eigen resource en informatie, zoals het aantal record sets en naam servers, kan vanuit deze weer gave worden bekeken. De kolom **naam servers** bevindt zich niet in de standaard weergave. Als u deze wilt toevoegen, klikt u op **kolommen**, selecteert u **naam servers**en klikt u vervolgens op **gereed**.

![DNS-zones weer geven](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Een DNS-zone verwijderen

Navigeer naar een DNS-zone in de portal. Klik op **zone verwijderen**op de Blade **DNS-zone** . U wordt vervolgens gevraagd om te bevestigen dat u de DNS-zone wilt verwijderen. Als u een DNS-zone verwijdert, worden ook alle records uit de zone verwijderd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het werken met uw DNS-zone en records met [behulp van de Azure portal aan de slag met Azure DNS](dns-getstarted-portal.md).