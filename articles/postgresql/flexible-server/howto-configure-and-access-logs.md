---
title: Logboeken configureren en openen-flexibele server-Azure Database for PostgreSQL
description: Toegang krijgen tot database logboeken voor Azure Database for PostgreSQL-flexibele server
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 3b54fe1ffd49c12db82a038ad449190a0049133f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492349"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Logboeken configureren en openen in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

PostgreSQL-logboeken zijn beschikbaar op elk knoop punt van een flexibele server. U kunt Logboeken verzenden naar een opslag server of naar een analyse service. De logboeken kunnen worden gebruikt om configuratie fouten en suboptimale prestaties te identificeren, op te lossen en te herstellen.

## <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

U kunt Diagnostische instellingen inschakelen voor uw post gres-server met behulp van de Azure Portal, CLI, REST API en Power shell. De logboek categorie die moet worden geselecteerd, is **PostgreSQLLogs**.

Resource logboeken inschakelen met behulp van de Azure Portal:

1. Ga in de portal naar *Diagnostische instellingen* in het navigatie menu van uw post gres-server.
   
2. Selecteer *Diagnostische instelling toevoegen*.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Knop Diagnostische instellingen toevoegen":::

3. Geef deze instelling een naam. 

4. Selecteer uw gewenste eind punt (opslag account, Event Hub, log Analytics). 

5. Selecteer het logboek type **PostgreSQLLogs**.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="PostgreSQL-logboeken kiezen":::

7. Sla de instelling op.

Ga naar het artikel [Diagnostische instellingen](../../azure-monitor/platform/diagnostic-settings.md) om bron Logboeken in te scha kelen met behulp van Power shell, CLI of rest API.

### <a name="access-resource-logs"></a>Toegang tot resource logboeken

De manier waarop u de logboeken opent, is afhankelijk van het eind punt dat u kiest. Zie het artikel over het [opslag account voor logboeken](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) voor Azure Storage. Zie het artikel [Stream Azure logs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) voor Event hubs.

Voor Azure Monitor-logboeken worden logboeken verzonden naar de werk ruimte die u hebt geselecteerd. De post gres-Logboeken gebruiken de **AzureDiagnostics** -verzamelings modus, zodat ze kunnen worden opgevraagd vanuit de tabel AzureDiagnostics. De velden in de tabel worden hieronder beschreven. Meer informatie over het uitvoeren van query's en waarschuwingen vindt u in het overzicht van de [Azure monitor-logboeken](../../azure-monitor/log-query/log-query-overview.md) .

Hier volgen enkele query's die u kunt proberen om aan de slag te gaan. U kunt waarschuwingen configureren op basis van query's.

Zoeken naar alle post gres-logboeken voor een bepaalde server in de afgelopen dag

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Zoeken naar alle niet-localhost Verbindings pogingen

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

In de bovenstaande query worden de resultaten weer gegeven in de afgelopen 6 uur voor alle logboek registratie van post gres-servers in deze werk ruimte.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met log Analytics-query's](../../azure-monitor/log-query/log-analytics-tutorial.md)
- Meer informatie over [Azure Event hubs](../../event-hubs/event-hubs-about.md)