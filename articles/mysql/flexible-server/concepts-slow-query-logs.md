---
title: Langzame query logboeken-Azure Database for MySQL-flexibele server
description: Hierin worden de langzame query Logboeken beschreven die beschikbaar zijn in Azure Database for MySQL flexibele server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d311ea3158e1f9d53c51fe239103039849597d11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579182"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Trage query Logboeken in Azure Database for MySQL flexibele server (preview-versie)

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In Azure Database for MySQL flexibele server is het logboek voor trage query's beschikbaar voor gebruikers om te configureren en toegang te krijgen. Langzame query logboeken zijn standaard uitgeschakeld en kunnen worden ingeschakeld om te helpen bij het identificeren van prestatie knelpunten tijdens het oplossen van problemen.

Zie de [sectie langzaam query-logboek](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) in de MySQL-engine documentatie voor meer informatie over het MySQL-logboek voor langzame query's.

## <a name="configure-slow-query-logging"></a>Langzame query logboek registratie configureren 
Het logboek voor langzame query's is standaard uitgeschakeld. Als u logboeken wilt inschakelen, stelt `slow_query_log` u de server parameter in *op aan*. Dit kan worden geconfigureerd met behulp van de Azure Portal of Azure CLI <!-- add link to server parameter-->. 

Andere para meters die u kunt aanpassen om het gedrag van logboek registratie voor langzame query's te beheren, zijn:

- **long_query_time**: een query vastleggen als deze langer duurt dan `long_query_time` (in seconden). De standaardinstelling is 10 seconden.
- **log_slow_admin_statements**: bepaalt of administratieve instructies (bijvoorbeeld `ALTER_TABLE`, `ANALYZE_TABLE` ) worden geregistreerd.
- **log_queries_not_using_indexes**: bepaalt of query's die geen indexen gebruiken, worden geregistreerd.
- **log_throttle_queries_not_using_indexes**: beperkt het aantal niet-geïndexeerde query's dat naar het langzame query logboek kan worden geschreven. Deze para meter `log_queries_not_using_indexes` wordt van kracht wanneer is ingesteld op *op*

> [!IMPORTANT]
> Als uw tabellen niet zijn geïndexeerd, worden het `log_queries_not_using_indexes` instellen `log_throttle_queries_not_using_indexes` van de para meters op **in** mogelijk beïnvloed door de MySQL-prestaties, omdat alle query's die worden uitgevoerd op deze niet-geïndexeerde tabellen, worden geschreven naar het logboek voor langzame query's.

Zie de documentatie van het MySQL- [logboek voor langzame query's](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) voor volledige beschrijvingen van de para meters van de langzame query-Logboeken.

## <a name="access-slow-query-logs"></a>Toegang tot langzame query logboeken

Langzame query logboeken zijn geïntegreerd met Azure Monitor Diagnostische instellingen. Zodra u langzame query Logboeken hebt ingeschakeld op uw MySQL-flexibele server, kunt u deze naar Azure Monitor-logboeken, Event Hubs of Azure Storage verzenden. Raadpleeg de documentatie van de [Diagnostische logboeken](../../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie over diagnostische instellingen. Zie het [artikel over langzame query-logboeken](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics)voor meer informatie over het inschakelen van diagnostische instellingen in de Azure Portal.

In de volgende tabel wordt de uitvoer van het logboek voor langzame query's beschreven. Afhankelijk van de uitvoer methode worden de opgenomen velden en de volg orde waarin ze worden weer gegeven, mogelijk verschillend.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Naam van de server |
| `start_time_t` UTC | Tijdstip waarop de query is gestart |
| `query_time_s` | De totale tijd in seconden die de query heeft geduurd om uit te voeren |
| `lock_time_s` | Totale tijd in seconden dat de query is vergrendeld |
| `user_host_s` | Gebruikersnaam |
| `rows_sent_s` | Aantal verzonden rijen |
| `rows_examined_s` | Aantal onderzochte rijen |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Invoeg-ID |
| `sql_text_s` | Volledige query |
| `server_id_s` | De server-ID |
| `thread_id_s` | Thread-ID |
| `\_ResourceId` | Resource-URI |

> [!Note]
> Voor wordt `sql_text_s` het logboek afgekapt als dit langer is dan 2048 tekens.

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

- Query's die langer zijn dan 10 seconden weer geven op alle MySQL-servers met Diagnostische logboeken ingeschakeld

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [audit logboeken](concepts-audit-logs.md)
- Langzame query logboeken configureren via de [Azure Portal](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->