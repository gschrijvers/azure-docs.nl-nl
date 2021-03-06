---
title: Langzame query logboeken-Azure Database for MariaDB
description: Hierin worden de Logboeken beschreven die beschikbaar zijn in Azure Database for MariaDB en de beschik bare para meters voor het inschakelen van verschillende registratie niveaus.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272080"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Trage query Logboeken in Azure Database for MariaDB
In Azure Database for MariaDB is het langzame query logboek beschikbaar voor gebruikers. Toegang tot het transactie logboek wordt niet ondersteund. Het logboek met trage query's kan worden gebruikt om prestatie knelpunten voor het oplossen van problemen te identificeren.

Zie de MariaDB-documentatie voor [langzame query logboeken](https://mariadb.com/kb/en/library/slow-query-log-overview/)voor meer informatie over het langzame query logboek.

## <a name="configure-slow-query-logging"></a>Langzame query logboek registratie configureren
Het logboek voor langzame query's is standaard uitgeschakeld. Als u deze wilt inschakelen `slow_query_log` , stelt u in op aan. Dit kan worden ingeschakeld met behulp van de Azure Portal of Azure CLI. 

Andere para meters die u kunt aanpassen zijn onder andere:

- **long_query_time**: als een query langer duurt dan long_query_time (in seconden) dat de query wordt geregistreerd. De standaardinstelling is 10 seconden.
- **log_slow_admin_statements**: als u beheer instructies zoals ALTER_TABLE en ANALYZE_TABLE in de instructies bevat die naar de slow_query_log zijn geschreven.
- **log_queries_not_using_indexes**: bepaalt of query's die geen indexen gebruiken, worden geregistreerd in het slow_query_log
- **log_throttle_queries_not_using_indexes**: deze para meter beperkt het aantal niet-index query's dat naar het langzame query logboek kan worden geschreven. Deze para meter wordt van kracht als log_queries_not_using_indexes is ingesteld op aan.
- **log_output**: als ' bestand ' is, kan het langzame query logboek worden geschreven naar zowel de lokale server opslag als Azure monitor Diagnostische logboeken. Als ' geen ' is, wordt het logboek met trage query's alleen naar Azure Monitor Diagnostische logboeken geschreven. 

> [!IMPORTANT]
> Als uw tabellen niet zijn geïndexeerd, kan het `log_queries_not_using_indexes` instellen `log_throttle_queries_not_using_indexes` van de para meters op in de prestaties van MariaDB beïnvloeden omdat alle query's die worden uitgevoerd op deze niet-geïndexeerde tabellen, worden geschreven naar het logboek voor langzame query's.<br><br>
> Als u logboek registratie van langzame query's gedurende een lange periode wilt plannen, wordt aangeraden om in `log_output` te stellen op ' geen '. Als deze is ingesteld op ' bestand ', worden deze logboeken naar de lokale server opslag geschreven en kunnen ze de prestaties van MariaDB beïnvloeden. 

Zie de documentatie voor MariaDB [langzame query logboeken](https://mariadb.com/kb/en/library/slow-query-log-overview/) voor volledige beschrijvingen van de para meters van de langzame query-Logboeken.

## <a name="access-slow-query-logs"></a>Toegang tot langzame query logboeken
Er zijn twee opties voor het openen van langzame query Logboeken in Azure Database for MariaDB: lokale server opslag of Azure Monitor Diagnostische logboeken. Dit wordt ingesteld met behulp van de `log_output` para meter.

Voor lokale server opslag kunt u langzame query logboeken weer geven en downloaden met behulp van de Azure Portal of de Azure CLI. Ga in het Azure Portal naar uw server in de Azure Portal. Selecteer de pagina **Server logboeken** onder de sectie **bewaking** . Zie [Server logboeken configureren en openen met Azure cli](howto-configure-server-logs-cli.md)voor meer informatie over Azure cli. 

Met Azure Monitor Diagnostische logboeken kunt u langzame query logboeken door geven aan Azure Monitor Logboeken (Log Analytics), Azure Storage of Event Hubs. Zie [hieronder](concepts-server-logs.md#diagnostic-logs) voor meer informatie.

## <a name="local-server-storage-log-retention"></a>Bewaar periode van lokaal server archief
Wanneer u zich registreert voor de lokale opslag van de server, zijn Logboeken Maxi maal zeven dagen beschikbaar vanaf het moment waarop ze zijn gemaakt. Als de totale grootte van de beschik bare logboeken groter is dan 7 GB, worden de oudste bestanden verwijderd totdat er ruimte beschikbaar is.

Logboeken worden elke 24 uur of 7 GB gedraaid, afhankelijk van wat het eerste komt.

> [!Note]
> De bovenstaande logboek bewaaring is niet van toepassing op Logboeken die worden gepiped met behulp van Azure Monitor Diagnostische logboeken. U kunt de Bewaar periode wijzigen voor de gegevens-sinks die worden verzonden naar (bijvoorbeeld Azure Storage).

## <a name="diagnostic-logs"></a>Diagnostische logboeken
Azure Database for MariaDB is geïntegreerd met Azure Monitor Diagnostische logboeken. Zodra u langzame query logboeken op uw MariaDB-server hebt ingeschakeld, kunt u ervoor kiezen om deze aan Azure Monitor-logboeken, Event Hubs of Azure Storage uit te geven. Zie de sectie over de [documentatie van Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het inschakelen van Diagnostische logboeken.

In de volgende tabel wordt de inhoud van elk logboek beschreven. Afhankelijk van de uitvoer methode worden de opgenomen velden en de volg orde waarin ze worden weer gegeven, mogelijk verschillend.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated`UTC | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd`AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Naam van de server |
| `start_time_t`UTC | Tijdstip waarop de query is gestart |
| `query_time_s` | De totale tijd die de query heeft uitgevoerd |
| `lock_time_s` | Totale tijd dat de query is vergrendeld |
| `user_host_s` | Gebruikersnaam |
| `rows_sent_s` | Aantal verzonden rijen |
| `rows_examined_s` | Aantal onderzochte rijen |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Invoeg-ID |
| `sql_text_s` | Volledige query |
| `server_id_s` | Server-ID |
| `thread_id_s` | Thread-ID |
| `\_ResourceId` | Resource-URI |

> [!Note]
> Voor `sql_text`wordt het logboek afgekapt als dit langer is dan 2048 tekens.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Logboeken analyseren in Azure Monitor-logboeken

Als uw logboeken voor trage query's worden gepiped naar Azure Monitor logboeken via Diagnostische logboeken, kunt u uw trage query's verder analyseren. Hieronder vindt u enkele voor beelden van query's waarmee u aan de slag kunt gaan. Zorg ervoor dat u het onderstaande bijwerkt met de naam van uw server.

- Query's die langer zijn dan 10 seconden op een bepaalde server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Top 5 van de langste query's vermelden op een bepaalde server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Een overzicht van langzame query's op basis van de query tijd minimum, maximum, gemiddelde en standaard afwijking op een bepaalde server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- De langzame query distributie op een bepaalde server tekenen

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Query's die langer zijn dan 10 seconden weer geven op alle MariaDB-servers met Diagnostische logboeken ingeschakeld

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Volgende stappen
- [Langzame query logboeken configureren vanuit de Azure Portal](howto-configure-server-logs-portal.md)
- [Langzame query logboeken configureren vanuit de Azure CLI](howto-configure-server-logs-cli.md)
