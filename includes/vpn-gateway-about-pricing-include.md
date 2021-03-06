---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67666384"
---
U betaalt voor twee dingen: het uurtarief voor het berekenen van de gateway van het virtuele netwerk en de overdracht van egress-gegevens van de gateway van het virtuele netwerk. Prijsinformatie vindt u op de pagina [Prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway). Zie de [pagina met prijzen voor ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) en schuif naar het gedeelte **Virtual Network gateways** voor de prijzen voor oudere Gateway-sku's.

**Berekeningskosten voor de virtuele netwerkgateway**<br>Voor de berekening van elke virtuele netwerkgateway wordt een uurtarief in rekening gebracht. De prijs is gebaseerd op de gateway-SKU die u opgeeft wanneer u een virtuele netwerkgateway maakt. De kosten zijn voor de gateway zelf en komen boven op de kosten voor de gegevensoverdracht die via de gateway verloopt. De kosten van een actief/actief-configuratie zijn hetzelfde als voor een actief/passief-configuratie.

**Kosten voor de gegevensoverdracht**<br>De kosten voor de gegevensoverdracht worden berekend op basis van uitgaand verkeer van de bron voor de gateway van het virtuele netwerk.

* Als u verkeer naar uw on-premises VPN-apparaat verzendt, wordt dit in rekening gebracht met de overdrachtssnelheid van egress-gegevens via het internet.
* Als u verkeer tussen virtuele netwerken in verschillende gebieden verzendt, zijn de prijzen gebaseerd op de regio.
* Als u alleen verkeer verzendt tussen virtuele netwerken die zich in dezelfde regio bevinden, worden er geen gegevenskosten in rekening gebracht. Verkeer tussen VNets in dezelfde regio is gratis.
