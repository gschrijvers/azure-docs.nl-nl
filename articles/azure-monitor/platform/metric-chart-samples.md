---
title: Voor beeld van een grafiek met Azure Monitor metrische gegevens
description: Meer informatie over het visualiseren van uw Azure Monitor gegevens.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 00935762a5e19ec47074021aff59992fd3b801bf
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797449"
---
# <a name="metric-chart-examples"></a>Voor beelden van metrische grafieken 

Het Azure-platform biedt [meer dan duizend metrische gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), waarvan vele dimensies zijn. Met behulp van [dimensie filters](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), het Toep assen van [splitsen](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), het beheren van grafiek typen en het aanpassen van de grafiek instellingen kunt u krachtige diagnostische weer gaven en dash boards maken die inzicht bieden in de status van uw infra structuur en toepassingen. Dit artikel bevat enkele voor beelden van de grafieken die u kunt bouwen met [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) en legt de benodigde stappen voor het configureren van elk van deze grafieken uit.

Wilt u de voor beelden van uw fantastische grafieken delen met de wereld? U kunt hier een bijdrage leveren aan deze pagina op GitHub en uw eigen grafiek voorbeelden delen.

## <a name="website-cpu-utilization-by-server-instances"></a>CPU-gebruik van website per Server exemplaar

In dit diagram wordt weer gegeven of de CPU voor een App Service binnen het aanvaard bare bereik valt en opsplitst in instantie om te bepalen of de belasting goed is gedistribueerd. U kunt in het diagram zien dat de app vóór 6 uur op één Server exemplaar werd uitgevoerd en vervolgens omhoog schalen door een andere instantie toe te voegen.

![Lijn diagram van gemiddeld CPU-percentage per Server exemplaar](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hoe kan ik deze grafiek configureren?

Selecteer uw App Service Resource en zoek het **CPU-percentage** metriek. Klik vervolgens op **splitsing Toep assen** en selecteer de dimensie **exemplaar** .

## <a name="application-availability-by-region"></a>Beschik baarheid van toepassingen per regio

Bekijk de beschik baarheid per regio van uw toepassing om te bepalen welke geografische locaties problemen ondervinden. Dit diagram toont de Application Insights beschikbaarheids metriek. U kunt zien dat de bewaakte toepassing geen problemen ondervindt met Beschik baarheid van het VS-Oost-Data Center, maar er is wel een deel van de beschik baarheid van West-VS en Azië-oost.

![Grafiek van gemiddelde Beschik baarheid per locatie](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Hoe kan ik deze grafiek configureren?

U moet eerst [Application Insights beschikbaarheids](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) controle inschakelen voor uw website. Vervolgens kiest u uw Application Insights resource en selecteert u de beschikbaarheids metriek. Splitsing Toep assen op de dimensie **uitvoerings locatie** .

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume van opslag account transacties op API-naam

Er is een overmatig volume trans acties voor uw opslag account resource. U kunt de metrische gegevens van de trans actie gebruiken om te bepalen welke API verantwoordelijk is voor de overmaat belasting. U ziet dat de volgende grafiek is geconfigureerd met dezelfde dimensie (API-naam) in filteren en splitsen om de weer gave te beperken tot alleen de API-aanroepen van de interesse:

![Staaf diagram van API-trans acties](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Hoe kan ik deze grafiek configureren?

Selecteer uw opslag account en de metrische gegevens voor de **trans acties** in de metrische kiezer. Scha kelen tussen grafiek type en **staaf diagram**. Klik op **splitsing Toep assen** en selecteer dimensie- **API-naam**. Klik vervolgens op het **filter toevoegen** en kies de **API-naam** dimensie opnieuw. Selecteer in het dialoog venster filteren de Api's die u in de grafiek wilt uitzetten.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure Monitor [werkmappen](../../azure-monitor/platform/workbooks-overview.md)
* Meer informatie over [metrische Explorer](metrics-charts.md)
