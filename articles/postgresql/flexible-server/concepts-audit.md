---
title: Controle logboek registratie-Azure Database for PostgreSQL-flexibele server
description: Concepten voor pgAudit audit logging in Azure Database for PostgreSQL-flexibele server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b344e2a845a9da8333860599bd4ff9041108202f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588249"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Controle logboek registratie in Azure Database for PostgreSQL-flexibele server

Controle logboek registratie van database activiteiten in Azure Database for PostgreSQL-flexibele server is beschikbaar via de PostgreSQL-controle extensie: [pgAudit](https://www.pgaudit.org/). pgAudit biedt gedetailleerde sessie-en/of object controle logboek registratie.

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibele server is als preview-versie beschikbaar

Als u Azure-logboeken op resource niveau wilt voor bewerkingen als Compute en opslag schalen, raadpleegt u het [Azure-activiteiten logboek](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Gebruiks overwegingen
Standaard worden pgAudit-logboekinstructies samen met uw reguliere logboeken verzonden met behulp van de standaardfunctie voor logboekregistratie van Postgres. In Azure Database for PostgreSQL-flexibele server kunt u alle logboeken configureren voor verzen ding naar Azure Monitor logboek Archief voor latere analyses in Log Analytics. Als u Azure Monitor bron logboek registratie inschakelt, worden uw logboeken automatisch verzonden (in JSON-indeling) naar Azure Storage, Event Hubs en/of Azure Monitor logboeken, afhankelijk van uw keuze.

Ga naar de sectie resource logs van het [artikel server logboeken](concepts-logging.md)voor meer informatie over het instellen van logboek registratie voor Azure Storage, Event Hubs of Azure monitor Logboeken.

## <a name="enabling-pgaudit"></a>PgAudit inschakelen

Als u pgAudit wilt inschakelen, moet u verbinding maken met uw server met behulp van een-client (zoals psql) en de extensie pgAudit inschakelen door de volgende opdracht uit te voeren:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>pgAudit-instellingen

met pgAudit kunt u de sessie of object controle logboek registratie configureren. Met de [sessie controle logboek registratie](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) worden gedetailleerde logboeken van de uitgevoerde instructies opgenomen. De [object controle logboek registratie](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) is een controle bereik voor specifieke relaties. U kunt ervoor kiezen om een of beide typen logboek registratie in te stellen. 

> [!NOTE]
> pgAudit-instellingen zijn globaal opgegeven en kunnen niet worden opgegeven op een Data Base-of Role niveau.

Zodra u [pgAudit hebt ingeschakeld](#enabling-pgaudit), kunt u de para meters configureren om de logboek registratie te starten. De [pgAudit-documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) bevat de definitie van elke para meter. Test eerst de para meters en controleer of u het verwachte gedrag krijgt.

> [!NOTE]
> Als `pgaudit.log_client` wordt ingesteld op aan, worden logboeken omgeleid naar een client proces (zoals psql) in plaats van naar het bestand te schrijven. Deze instelling moet over het algemeen uitgeschakeld blijven. <br> <br>
> `pgaudit.log_level` wordt alleen ingeschakeld als `pgaudit.log_client` aan is.

> [!NOTE]
> In Azure Database for PostgreSQL-flexibele server `pgaudit.log` kan niet worden ingesteld met behulp van een `-` (minteken) snelkoppeling, zoals beschreven in de pgAudit-documentatie. Alle vereiste instructie klassen (lezen, schrijven, enz.) moeten afzonderlijk worden opgegeven.

## <a name="audit-log-format"></a>Indeling van auditlogboek
Elke controle vermelding wordt aan `AUDIT:` het begin van de logboek regel aangeduid. De indeling van de rest van de vermelding wordt beschreven in de [pgAudit-documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Aan de slag
Als u snel aan de slag wilt gaan, stelt `pgaudit.log` u in `WRITE` en opent u de logboeken van de server om de uitvoer te controleren. 

## <a name="viewing-audit-logs"></a>Audit logboeken weer geven
De manier waarop u de logboeken opent, is afhankelijk van het eind punt dat u kiest. Zie het artikel over het [opslag account voor logboeken](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) voor Azure Storage. Zie het artikel [Stream Azure logs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) voor Event hubs.

Voor Azure Monitor-logboeken worden logboeken verzonden naar de werk ruimte die u hebt geselecteerd. De post gres-Logboeken gebruiken de **AzureDiagnostics** -verzamelings modus, zodat ze kunnen worden opgevraagd vanuit de tabel AzureDiagnostics. De velden in de tabel worden hieronder beschreven. Meer informatie over het uitvoeren van query's en waarschuwingen vindt u in het overzicht van de [Azure monitor-logboeken](../../azure-monitor/logs/log-query-overview.md) .

U kunt deze query gebruiken om aan de slag te gaan. U kunt waarschuwingen configureren op basis van query's.

Zoeken naar alle pgAudit-vermeldingen in post gres-logboeken voor een bepaalde server in de afgelopen dag
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het registreren van Azure Database for PostgreSQL-flexibele server](concepts-logging.md)
- [Meer informatie over het instellen van logboek registratie in Azure Database for PostgreSQL-flexibele server en toegang tot logboeken](howto-configure-and-access-logs.md)