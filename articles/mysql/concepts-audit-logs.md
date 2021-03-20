---
title: Audit logboeken-Azure Database for MySQL
description: Beschrijft de audit logboeken die beschikbaar zijn in Azure Database for MySQL en de beschik bare para meters voor het inschakelen van logboek registratie niveaus.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: fa845e7c402073a64f51d75b1da51f56142eee2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630496"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Audit Logboeken in Azure Database for MySQL

In Azure Database for MySQL is het controle logboek beschikbaar voor gebruikers. Het controle logboek kan worden gebruikt om activiteit op database niveau bij te houden en wordt vaak gebruikt voor naleving.

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

>[!IMPORTANT]
> Het is raadzaam om alleen de gebeurtenis typen en gebruikers die vereist zijn voor controle doeleinden te registreren om ervoor te zorgen dat de prestaties van uw server niet sterk worden beïnvloed.

Het controle logboek is standaard uitgeschakeld. Als u deze wilt inschakelen, stelt `audit_log_enabled` u in op aan.

Andere para meters die u kunt aanpassen zijn onder andere:

- `audit_log_events`: Hiermee beheert u de gebeurtenissen die moeten worden vastgelegd. Zie de onderstaande tabel voor specifieke controle gebeurtenissen.
- `audit_log_include_users`: MySQL-gebruikers die moeten worden opgenomen voor logboek registratie. De standaard waarde voor deze para meter is leeg, die alle gebruikers bevat die moeten worden geregistreerd. Dit heeft een hogere prioriteit dan `audit_log_exclude_users` . De maximale lengte van de para meter is 512 tekens.
- `audit_log_exclude_users`: MySQL-gebruikers die moeten worden uitgesloten van logboek registratie. De maximale lengte van de para meter is 512 tekens.

> [!NOTE]
> `audit_log_include_users` heeft een hogere prioriteit dan `audit_log_exclude_users` . Als `audit_log_include_users`  =  `demouser` en wordt `audit_log_exclude_users`  =  `demouser` de gebruiker bijvoorbeeld opgenomen in de audit logboeken, omdat deze een `audit_log_include_users` hogere prioriteit heeft.

| **Gebeurtenis** | **Beschrijving** |
|---|---|
| `CONNECTION` | -Initiëren van verbinding (geslaagd of mislukt) <br> -Herauthenticatie van gebruiker met andere gebruiker/wacht woord tijdens sessie <br> -Verbinding beëindigen |
| `DML_SELECT`| Query's selecteren |
| `DML_NONSELECT` | Query's invoegen/verwijderen/bijwerken |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Query's als "DROP data base" |
| `DCL` | Query's zoals ' machtiging verlenen ' |
| `ADMIN` | Query's als "STATUS weer geven" |
| `GENERAL` | Alles in DML_SELECT, DML_NONSELECT, DML, DDL, DCL en beheerder |
| `TABLE_ACCESS` | -Beschikbaar voor MySQL 5,7 en MySQL 8,0 <br> -Lees instructies voor tabellen, zoals selecteren of invoegen in... UITGESCHAKELD <br> -Instructies voor het verwijderen van tabellen, zoals verwijderen of TRUNCATE TABLE <br> -Instructies voor het invoegen van tabellen, zoals invoegen of vervangen <br> -Update-instructies voor tabellen, zoals UPDATE |

## <a name="access-audit-logs"></a>Auditlogboeken inzien

Auditlogboeken zijn geïntegreerd in Diagnostische logboeken van Azure Monitor. Zodra u auditlogboeken op uw MySQL-server hebt ingeschakeld, kunt u ze naar Azure Monitor-logboeken, Event Hubs of Azure Storage verzenden. Zie het artikel over het [controle logboek](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)van de portal voor meer informatie over het inschakelen van Diagnostische logboeken in de Azure Portal.

## <a name="diagnostic-logs-schemas"></a>Schema's voor Diagnostische logboeken

In de volgende secties wordt beschreven wat er wordt uitgevoerd door MySQL-controle logboeken op basis van het gebeurtenis type. Afhankelijk van de uitvoer methode worden de opgenomen velden en de volg orde waarin ze worden weer gegeven, mogelijk verschillend.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (alleen beschikbaar voor MySQL 5,7) |
| `connection_id_d` | Unieke verbindings-ID die is gegenereerd door MySQL |
| `host_s` | Leeg |
| `ip_s` | IP-adres van de client die verbinding maakt met MySQL |
| `user_s` | Naam van de gebruiker die de query uitvoert |
| `db_s` | Naam van de data base die is verbonden met |
| `\_ResourceId` | Resource-URI |

### <a name="general"></a>Algemeen

Schema hieronder is van toepassing op de gebeurtenis typen algemeen, DML_SELECT, DML_NONSELECT, DML, DDL, DCL en beheerder.

> [!NOTE]
> Voor wordt `sql_text` het logboek afgekapt als dit langer is dan 2048 tekens.

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
> Logboeken voor tabel toegang worden alleen uitgevoerd voor MySQL 5,7.<br>Voor wordt `sql_text` het logboek afgekapt als dit langer is dan 2048 tekens.

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

- [Audit logboeken configureren in de Azure Portal](howto-configure-audit-logs-portal.md)
