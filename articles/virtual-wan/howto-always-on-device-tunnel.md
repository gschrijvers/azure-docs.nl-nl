---
title: Een tunnel voor altijd-op-VPN configureren
titleSuffix: Azure Virtual WAN
description: Stappen voor het configureren van de tunnel voor altijd op VPN-apparaten voor virtuele WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502852"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Een tunnel voor altijd op VPN-apparaat configureren voor virtuele WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Vereisten

U moet een punt-naar-site-configuratie maken en de toewijzing van de virtuele hub bewerken. Raadpleeg de volgende secties voor instructies:

* [Een P2S-configuratie maken](virtual-wan-point-to-site-portal.md#p2sconfig)
* [De hub-toewijzing bewerken](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>De tunnel van het apparaat configureren

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u het profiel wilt verwijderen, voert u de volgende opdracht uit:

![Opschonen](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.