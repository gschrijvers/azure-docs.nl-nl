---
title: Cisco-gegevens verbinden met Azure-Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Cisco-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588396"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Cisco ASA verbinden met Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Cisco ASA-apparaat verbindt met Azure Sentinel. Met de Cisco ASA Data Connector kunt u eenvoudig uw Cisco ASA-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Met Cisco ASA op Azure Sentinel krijgt u meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Cisco ASA-logboeken door sturen naar de syslog-agent

Cisco ASA biedt geen ondersteuning voor CEF, dus de logboeken worden verzonden als syslog en de Azure Sentinel agent weet hoe ze kunnen worden geparseerd alsof ze CEF-logboeken zijn. Cisco ASA configureren voor het door sturen van syslog-berichten naar uw Azure-werk ruimte via de syslog-agent:

1. Ga naar [syslog-berichten verzenden naar een externe syslog-server](https://aka.ms/asi-syslog-cisco-forwarding)en volg de instructies voor het instellen van de verbinding. Gebruik deze para meters wanneer u hierom wordt gevraagd:
    - Stel **poort** in op 514 of de poort die u in de agent hebt ingesteld.
    - Stel **syslog_ip** in op het IP-adres van de agent.

1. Als u het relevante schema in Log Analytics voor de Cisco-gebeurtenissen wilt gebruiken `CommonSecurityLog`, zoekt u naar.

1. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Cisco ASA-toestellen verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.


