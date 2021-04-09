---
title: Audit logboeken-Azure Database for MySQL-flexibele server
description: Beschrijft de audit logboeken die beschikbaar zijn op Azure Database for MySQL flexibele server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 1232a0753c988f5a28ebba28f9819aa67ce28603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718740"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Database activiteiten bijhouden met audit Logboeken in Azure Database for MySQL flexibele server

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview

Azure Database for MySQL flexibele server biedt gebruikers de mogelijkheid om audit logboeken te configureren. Audit logboeken kunnen worden gebruikt om activiteiten op database niveau te volgen, waaronder verbindings-, beheer-, DDL-en DML-gebeurtenissen. Deze typen logboeken worden vaak gebruikt voor nalevingsdoeleinden.

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

>[!IMPORTANT]
> Het is raadzaam om alleen de gebeurtenis typen en gebruikers die vereist zijn voor controle doeleinden te registreren om ervoor te zorgen dat de prestaties van uw server niet sterk worden beïnvloed.

Audit logboeken zijn standaard uitgeschakeld. Als u deze wilt inschakelen, stelt `audit_log_enabled` u de server parameter in *op aan*. Dit kan worden geconfigureerd met behulp van de Azure Portal of Azure CLI <!-- add link to server parameter-->. 

Andere para meters die u kunt aanpassen om het gedrag van de controle logboek registratie te bepalen, zijn:

- `audit_log_events`: Hiermee beheert u de gebeurtenissen die moeten worden vastgelegd. Zie de onderstaande tabel voor specifieke controle gebeurtenissen.
- `audit_log_include_users`: MySQL-gebruikers die moeten worden opgenomen voor logboek registratie. De standaard waarde voor deze para meter is leeg, die alle gebruikers bevat die moeten worden geregistreerd. Dit heeft een hogere prioriteit dan `audit_log_exclude_users` . De maximale lengte van de para meter is 512 tekens.
- `audit_log_exclude_users`: MySQL-gebruikers die moeten worden uitgesloten van logboek registratie. De maximale lengte van de para meter is 512 tekens.

> [!NOTE]
> `audit_log_include_users` heeft een hogere prioriteit dan `audit_log_exclude_users` . Als `audit_log_include_users`  =  `demouser` en wordt `audit_log_exclude_users`  =  `demouser` de gebruiker bijvoorbeeld opgenomen in de audit logboeken, omdat deze een `audit_log_include_users` hogere prioriteit heeft.

| **Gebeurtenis** | **Beschrijving** |
|---|---|
| `CONNECTION` | -Initiëren van verbinding (geslaagd of mislukt) <br> -Gebruiker opnieuw verifiëren met andere gebruiker/wacht woord tijdens sessie <br> -Verbinding beëindigen |
| `DML_SELECT`| Query's selecteren |
| `DML_NONSELECT` | Query's invoegen/verwijderen/bijwerken |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Query's als "DROP data base" |
| `DCL` | Query's zoals ' machtiging verlenen ' |
| `ADMIN` | Query's als "STATUS weer geven" |
| `GENERAL` | Alles in DML_SELECT, DML_NONSELECT, DML, DDL, DCL en beheerder |
| `TABLE_ACCESS` | -Lees instructies voor tabellen, zoals selecteren of invoegen in... UITGESCHAKELD <br> -Instructies voor het verwijderen van tabellen, zoals verwijderen of TRUNCATE TABLE <br> -Instructies voor het invoegen van tabellen, zoals invoegen of vervangen <br> -Update-instructies voor tabellen, zoals UPDATE |

## <a name="access-audit-logs"></a>Auditlogboeken inzien

Audit logboeken zijn geïntegreerd met Azure Monitor Diagnostische instellingen. Zodra u audit logboeken op uw MySQL-flexibele server hebt ingeschakeld, kunt u deze naar Azure Monitor-logboeken, Event Hubs of Azure Storage verzenden. Raadpleeg de documentatie van de [Diagnostische logboeken](../../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie over diagnostische instellingen. Zie het artikel over het [controle logboek](how-to-configure-audit-logs-portal.md#set-up-diagnostics)van de portal voor meer informatie over het inschakelen van diagnostische instellingen in de Azure Portal.

In de volgende secties wordt de uitvoer van MySQL-audit Logboeken beschreven op basis van het gebeurtenis type. Afhankelijk van de uitvoer methode worden de opgenomen velden en de volg orde waarin ze worden weer gegeven, mogelijk verschillend.

### <a name="connection"></a>Verbinding

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` |
| `connection_id_d` | Unieke verbindings-ID die is gegenereerd door MySQL |
| `host_s` | Leeg |
| `ip_s` | IP-adres van de client die verbinding maakt met MySQL |
| `user_s` | Naam van de gebruiker die de query uitvoert |
| `db_s` | Naam van de data base die is verbonden met |
| `\_ResourceId` | Resource-URI |

### <a name="general"></a>Algemeen

Schema hieronder is van toepassing op de gebeurtenis typen algemeen, DML_SELECT, DML_NONSELECT, DML, DDL, DCL en beheerder.

> [!NOTE]
> Voor wordt `sql_text_s` het logboek afgekapt als dit langer is dan 2048 tekens.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (alleen beschikbaar voor MySQL 5,6) |
| `event_time` | Begin tijd van de query in UTC-tijds tempel |
| `error_code_d` | Fout code als de query is mislukt. `0` betekent geen fout |
| `thread_id_d` | ID van de thread die de query heeft uitgevoerd |
| `host_s` | Leeg |
| `ip_s` | IP-adres van de client die verbinding maakt met MySQL |
| `user_s` | Naam van de gebruiker die de query uitvoert |
| `sql_text_s` | Volledige query tekst |
| `\_ResourceId` | Resource-URI |

### <a name="table-access"></a>Tabel toegang

> [!NOTE]
> Voor wordt `sql_text_s` het logboek afgekapt als dit langer is dan 2048 tekens.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` , `UPDATE` of `DELETE` |
| `connection_id_d` | Unieke verbindings-ID die is gegenereerd door MySQL |
| `db_s` | De naam van de data base die wordt geopend |
| `table_s` | De naam van de tabel die wordt geopend |
| `sql_text_s` | Volledige query tekst |
| `\_ResourceId` | Resource-URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Logboeken analyseren in Azure Monitor-logboeken

Zodra de controle logboeken zijn gepiped naar Azure Monitor logboeken via Diagnostische logboeken, kunt u verdere analyse van uw gecontroleerde gebeurtenissen uitvoeren. Hieronder vindt u enkele voor beelden van query's waarmee u aan de slag kunt gaan. Zorg ervoor dat u het onderstaande bijwerkt met de naam van uw server.

- ALGEMENE gebeurtenissen op een bepaalde server weer geven

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- VERBINDINGS gebeurtenissen op een bepaalde server weer geven

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Gecontroleerde gebeurtenissen op een bepaalde server samenvatten

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Grafiek van de distributie van het type controle gebeurtenis op een bepaalde server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Gecontroleerde gebeurtenissen op alle MySQL-servers weer geven met Diagnostische logboeken die zijn ingeschakeld voor audit logboeken

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Logboeken voor langzame query's](concepts-slow-query-logs.md)
- Logboeken voor controle query's configureren vanuit de [Azure Portal](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->
