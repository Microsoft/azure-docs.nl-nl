---
title: Controle logboek registratie-Azure Database for PostgreSQL-grootschalige (Citus)
description: Concepten voor pgAudit-controle logboek registratie in Azure Database for PostgreSQL-grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 8a36062a2d29bcec10279d73211526a0dcba619e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702110"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Audit logboek registratie in Azure Database for PostgreSQL-grootschalige (Citus)

Controle logboek registratie van database activiteiten in Azure Database for PostgreSQL-grootschalige (Citus) is beschikbaar via de PostgreSQL audit Extension: [pgAudit](https://www.pgaudit.org/). pgAudit biedt gedetailleerde sessie-en/of object controle logboek registratie.

> [!IMPORTANT]
> pgAudit is in preview op Azure Database for PostgreSQL-grootschalige (Citus)

Als u Azure-logboeken op resource niveau wilt voor bewerkingen als Compute en opslag schalen, raadpleegt u het [Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Gebruiks overwegingen
Standaard worden pgAudit-logboekinstructies samen met uw reguliere logboeken verzonden met behulp van de standaardfunctie voor logboekregistratie van Postgres. In Azure Database for PostgreSQL-grootschalige (Citus) kunt u alle logboeken configureren voor verzen ding naar Azure Monitor logboek Archief voor latere analyses in Log Analytics. Als u Azure Monitor bron logboek registratie inschakelt, worden uw logboeken automatisch verzonden (in JSON-indeling) naar Azure Storage, Event Hubs en/of Azure Monitor logboeken, afhankelijk van uw keuze.

## <a name="enabling-pgaudit"></a>PgAudit inschakelen

De pgAudit-extensie is vooraf geïnstalleerd en ingeschakeld op alle grootschalige (Citus)-server groeps knooppunten. Er is geen actie vereist om deze functie in te scha kelen.

## <a name="pgaudit-settings"></a>pgAudit-instellingen

met pgAudit kunt u de sessie of object controle logboek registratie configureren. Met de [sessie controle logboek registratie](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) worden gedetailleerde logboeken van de uitgevoerde instructies opgenomen. De [object controle logboek registratie](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) is een controle bereik voor specifieke relaties. U kunt ervoor kiezen om een of beide typen logboek registratie in te stellen. 

> [!NOTE]
> pgAudit-instellingen zijn globaal opgegeven en kunnen niet worden opgegeven op een Data Base-of Role niveau.
>
> Daarnaast worden pgAudit-instellingen per knoop punt in een server groep opgegeven. Als u een wijziging wilt aanbrengen op alle knoop punten, moet u deze afzonderlijk Toep assen op elk knoop punt.

U moet pgAudit-para meters configureren om de logboek registratie te starten. De [pgAudit-documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) bevat de definitie van elke para meter. Test eerst de para meters en controleer of u het verwachte gedrag krijgt.

> [!NOTE]
> Als `pgaudit.log_client` wordt ingesteld op aan, worden logboeken omgeleid naar een client proces (zoals psql) in plaats van naar het bestand te schrijven. Deze instelling moet over het algemeen uitgeschakeld blijven. <br> <br>
> `pgaudit.log_level` is alleen ingeschakeld wanneer `pgaudit.log_client` zich op bevindt.

> [!NOTE]
> In Azure Database for PostgreSQL-grootschalige (Citus) `pgaudit.log` kan niet worden ingesteld met behulp van een `-` (minteken) snelkoppeling, zoals beschreven in de pgAudit-documentatie. Alle vereiste instructie klassen (lezen, schrijven, enz.) moeten afzonderlijk worden opgegeven.

## <a name="audit-log-format"></a>Indeling van auditlogboek
Elke controle vermelding wordt aan `AUDIT:` het begin van de logboek regel aangeduid. De indeling van de rest van de vermelding wordt beschreven in de [pgAudit-documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Aan de slag
Als u snel aan de slag wilt gaan, stelt `pgaudit.log` u in `WRITE` en opent u de logboeken van de server om de uitvoer te controleren. 

## <a name="viewing-audit-logs"></a>Audit logboeken weer geven
De manier waarop u de logboeken opent, is afhankelijk van het eind punt dat u kiest. Zie het artikel over het [opslag account voor logboeken](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) voor Azure Storage. Zie het artikel [Stream Azure logs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) voor Event hubs.

Voor Azure Monitor-logboeken worden logboeken verzonden naar de werk ruimte die u hebt geselecteerd. De post gres-Logboeken gebruiken de **AzureDiagnostics** -verzamelings modus, zodat ze kunnen worden opgevraagd vanuit de tabel AzureDiagnostics. De velden in de tabel worden hieronder beschreven. Meer informatie over het uitvoeren van query's en waarschuwingen vindt u in het overzicht van de [Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md) .

U kunt deze query gebruiken om aan de slag te gaan. U kunt waarschuwingen configureren op basis van query's.

Zoeken naar alle pgAudit-vermeldingen in post gres-logboeken voor een bepaalde server in de afgelopen dag
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het instellen van logboek registratie in Azure Database for PostgreSQL-grootschalige (Citus) en het openen van Logboeken](howto-hyperscale-logging.md)