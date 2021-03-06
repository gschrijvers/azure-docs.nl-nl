---
title: Overzicht van Peering Service-partners
titleSuffix: Azure
description: Overzicht van Peering Service-partners
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720245"
---
# <a name="peering-service-partner-walkthrough"></a>Overzicht van Peering Service-partners

In deze sectie wordt uitgelegd welke stappen een provider moet volgen om direct peering in te scha kelen voor de peering-service.

## <a name="create-direct-peering-connection-for-peering-service"></a>Directe peering-verbinding maken voor de peering-service
Service providers kunnen hun geografische bereik uitbreiden door nieuwe rechtstreekse peering te maken die ondersteuning biedt voor de peering-service. Om dit te bereiken,
1. Als dat nog niet het geval is, wordt een peering service-partner
1. Volg de instructies voor [het maken of wijzigen van een directe peering met behulp van de portal](howto-direct-portal.md). Zorg ervoor dat het voldoet aan de vereiste voor hoge Beschik baarheid.
1. Volg vervolgens de stappen om [peering service in te scha kelen op een directe peering met behulp van de portal](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Verouderde directe peering-verbinding gebruiken voor peering-service
Als u verouderde directe peering hebt die u wilt gebruiken ter ondersteuning van de peering-service,
1. Als dit nog niet het geval is, wordt er een peering service-partner geworden.
1. Volg de instructies voor [het converteren van een verouderde directe peering naar Azure-resource met behulp van de portal](howto-legacy-direct-portal.md). Bestel zo nodig extra circuits om te voldoen aan de vereiste voor hoge Beschik baarheid.
1. Volg vervolgens de stappen om [peering service in te scha kelen op een directe peering met behulp van de portal](howto-peering-service-portal.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beleid voor peering](https://peering.azurewebsites.net/peering).
* Voor meer informatie over de stappen om direct peering in te stellen met micro soft, volgt u het [scenario voor directe peering](walkthrough-direct-all.md).
* Voor meer informatie over de stappen voor het instellen van Exchange-peering met micro soft, volgt u het [scenario voor Exchange-peering](walkthrough-exchange-all.md).