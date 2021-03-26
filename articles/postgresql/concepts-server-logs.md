---
title: Logboeken-Azure Database for PostgreSQL-één server
description: Beschrijft configuratie van logboek registratie, opslag en analyse in Azure Database for PostgreSQL-één server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 31b3c0a1894280e90f696f6c107b67a3af7b1880
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605345"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Meldt zich aan Azure Database for PostgreSQL-één server

Met Azure Database for PostgreSQL kunt u de standaard logboeken van post gres configureren en gebruiken. De logboeken kunnen worden gebruikt om configuratie fouten en suboptimale prestaties te identificeren, op te lossen en te herstellen. Logboek registratie-informatie die u kunt configureren en toegang bevat fouten, query gegevens, autovacuüm records, verbindingen en controle punten. (Toegang tot transactie Logboeken is niet beschikbaar).

Controle logboek registratie wordt beschikbaar gesteld via een post gres-extensie, pgaudit. Ga naar het artikel over [controle concepten](concepts-audit.md) voor meer informatie.


## <a name="configure-logging"></a>Logboek registratie configureren 
U kunt post gres Standard-logboek registratie op uw server configureren met de server parameters voor registratie. Op elke Azure Database for PostgreSQL-server `log_checkpoints` en `log_connections` zijn standaard ingeschakeld. Er zijn aanvullende para meters die u kunt aanpassen aan uw logboek registratie vereisten: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Azure Database for PostgreSQL-registratie parameters":::

Ga voor meer informatie over post gres-logboek parameters naar de secties [Wanneer u zich wilt registreren](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) en [wat u wilt registreren in](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) de post gres-documentatie. De meeste, maar niet alle, post gres registratie parameters zijn beschikbaar voor configuratie in Azure Database for PostgreSQL.

Raadpleeg de documentatie van de [Portal](howto-configure-server-parameters-using-portal.md) of de [cli-documentatie](howto-configure-server-parameters-using-cli.md)voor meer informatie over het configureren van para meters in azure database for PostgreSQL. 

> [!NOTE]
> Als u een groot aantal logboeken configureert, bijvoorbeeld logboek registratie, kunt u aanzienlijke prestatie overhead toevoegen. 

## <a name="access-log-files"></a>Access. log-bestanden
De standaard indeling voor logboeken in Azure Database for PostgreSQL is. log. Een voor beeld van een regel uit dit logboek ziet er als volgt uit:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL biedt een opslag locatie voor de korte termijn voor de. log-bestanden. Een nieuw bestand begint elke 1 uur of 100 MB, afhankelijk van wat het eerste komt. Logboeken worden toegevoegd aan het huidige bestand wanneer ze worden verzonden vanuit post gres.  

U kunt de Bewaar periode voor deze logboek opslag voor de korte termijn instellen met behulp van de `log_retention_period` para meter. De standaardwaarde is drie dagen, de maximumwaarde is zeven dagen. De opslag locatie voor de korte termijn kan Maxi maal 1 GB aan logboek bestanden bevatten. Na 1 GB worden de oudste bestanden, ongeacht de Bewaar periode, verwijderd om ruimte te maken voor nieuwe logboeken. 

Voor een langere periode voor het bewaren van Logboeken en logboek analyse kunt u de. log-bestanden downloaden en verplaatsen naar een service van derden. U kunt de bestanden downloaden met behulp van de [Azure Portal](howto-configure-server-logs-in-portal.md) [Azure cli](howto-configure-server-logs-using-cli.md). U kunt ook Azure Monitor Diagnostische instellingen configureren waarmee uw logboeken (in JSON-indeling) automatisch worden meegestuurd naar locaties met langere termijn. Meer informatie over deze optie vindt u in de sectie hieronder. 

U kunt geen logboek bestanden meer genereren door de para meter `logging_collector` in te stellen op uit. Het wordt uitgeschakeld. het genereren van het logboek bestand wordt aanbevolen als u Azure Monitor Diagnostische instellingen gebruikt. Deze configuratie vermindert de invloed van de prestaties van aanvullende logboek registratie.

## <a name="resource-logs"></a>Resourcelogboeken

Azure Database for PostgreSQL is geïntegreerd met Azure Monitor Diagnostische instellingen. Met Diagnostische instellingen kunt u uw post gres-Logboeken in JSON-indeling verzenden naar Azure Monitor logboeken voor analyse-en waarschuwings doeleinden, Event Hubs voor streaming en Azure Storage voor archivering. 

> [!IMPORTANT]
> Deze diagnostische functie voor Server Logboeken is alleen beschikbaar in de [prijs categorie](concepts-pricing-tiers.md)algemeen en geoptimaliseerd voor geheugen.


### <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

U kunt Diagnostische instellingen inschakelen voor uw post gres-server met behulp van de Azure Portal, CLI, REST API en Power shell. De logboek categorie die moet worden geselecteerd, is **PostgreSQLLogs**. (Er zijn andere logboeken die u kunt configureren als u [query Store](concepts-query-store.md)gebruikt.)

Resource logboeken inschakelen met behulp van de Azure Portal:

   1. Ga in de portal naar *Diagnostische instellingen* in het navigatie menu van uw post gres-server.
   2. Selecteer *Diagnostische instelling toevoegen*.
   3. Geef deze instelling een naam. 
   4. Selecteer uw gewenste eind punt (opslag account, Event Hub, log Analytics). 
   5. Selecteer het logboek type **PostgreSQLLogs**.
   7. Sla de instelling op.

Ga naar het artikel [Diagnostische instellingen](../azure-monitor/essentials/diagnostic-settings.md) om bron Logboeken in te scha kelen met behulp van Power shell, CLI of rest API.

### <a name="access-resource-logs"></a>Toegang tot resource logboeken

De manier waarop u de logboeken opent, is afhankelijk van het eind punt dat u kiest. Zie het artikel over het [opslag account voor logboeken](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) voor Azure Storage. Zie het artikel [Stream Azure logs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) voor Event hubs.

Voor Azure Monitor-logboeken worden logboeken verzonden naar de werk ruimte die u hebt geselecteerd. De post gres-Logboeken gebruiken de **AzureDiagnostics** -verzamelings modus, zodat ze kunnen worden opgevraagd vanuit de tabel AzureDiagnostics. De velden in de tabel worden hieronder beschreven. Meer informatie over het uitvoeren van query's en waarschuwingen vindt u in het overzicht van de [Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md) .

Hier volgen enkele query's die u kunt proberen om aan de slag te gaan. U kunt waarschuwingen configureren op basis van query's.

Zoeken naar alle post gres-logboeken voor een bepaalde server in de afgelopen dag
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Zoeken naar alle niet-localhost Verbindings pogingen
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
In de bovenstaande query worden de resultaten weer gegeven in de afgelopen 6 uur voor alle logboek registratie van post gres-servers in deze werk ruimte.

### <a name="log-format"></a>Logboek indeling

De volgende tabel beschrijft de velden voor het type **PostgreSQLLogs** . Afhankelijk van het uitvoer eindpunt dat u kiest, worden de opgenomen velden en de volg orde waarin ze worden weer gegeven. 

|**Veld** | **Beschrijving** |
|---|---|
| TenantId | Uw Tenant-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Tijds tempel voor het vastleggen van het logboek in UTC |
| Type | Het type van het logboek. Altijd `AzureDiagnostics` |
| SubscriptionId | GUID voor het abonnement waartoe de server behoort |
| ResourceGroup | Naam van de resource groep waartoe de server behoort |
| ResourceProvider | De naam van de resource provider. Altijd `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Resource-URI |
| Resource | Naam van de server |
| Categorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Gelijk | Logboek registratie niveau, voor beeld: logboek, fout, kennisgeving |
| Bericht | Primair logboek bericht | 
| Domain | Server versie, voor beeld: post gres-10 |
| Detail | Secundair logboek bericht (indien van toepassing) |
| ColumnName | De naam van de kolom (indien van toepassing) |
| SchemaName | De naam van het schema (indien van toepassing) |
| Gegevens type | Naam van het gegevens type (indien van toepassing) |
| LogicalServerName | Naam van de server | 
| _ResourceId | Resource-URI |
| Voorvoegsel | Voor voegsel van logboek regel |


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het openen van Logboeken vanuit de [Azure Portal](howto-configure-server-logs-in-portal.md) of [Azure cli](howto-configure-server-logs-using-cli.md).
- Meer informatie over [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).
- Meer informatie over [audit logboeken](concepts-audit.md)