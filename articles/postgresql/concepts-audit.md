---
title: Controle logboek registratie-Azure Database for PostgreSQL-één server
description: Concepten voor pgAudit-controle logboek registratie in Azure Database for PostgreSQL-één server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 33fa6420f52cae9c869cc75a04ea82de0ec48262
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596298"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Controle logboek registratie in Azure Database for PostgreSQL-één server

Audit logboek registratie van database activiteiten in Azure Database for PostgreSQL-één server is beschikbaar via de PostgreSQL-controle extensie: [pgAudit](https://www.pgaudit.org/). pgAudit biedt gedetailleerde sessie-en/of object controle logboek registratie.

> [!NOTE]
> pgAudit is beschikbaar in de preview-versie van Azure Database for PostgreSQL.
> De uitbrei ding kan alleen worden ingeschakeld op servers met Algemeen en geoptimaliseerd voor geheugen.

Als u Azure-logboeken op resource niveau wilt voor bewerkingen als Compute en opslag schalen, raadpleegt u het [Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Gebruiks overwegingen
Standaard worden pgAudit-logboekinstructies samen met uw reguliere logboeken verzonden met behulp van de standaardfunctie voor logboekregistratie van Postgres. In Azure Database for PostgreSQL kunnen deze .log-bestanden worden gedownload via de Azure-portal of de CLI. De maximale opslag voor het verzamelen van bestanden is 1 GB en elk bestand is Maxi maal zeven dagen beschikbaar (de standaard waarde is drie dagen). Deze service is een korte-termijn opslag optie.

U kunt ook alle logboeken zo configureren dat deze naar Azure Monitor logboek archief worden verzonden voor latere analyses in Log Analytics. Als u Azure Monitor bron logboek registratie inschakelt, worden uw logboeken automatisch verzonden (in JSON-indeling) naar Azure Storage, Event Hubs en/of Azure Monitor logboeken, afhankelijk van uw keuze.

Als pgAudit wordt ingeschakeld, wordt er een groot aantal logboekregistraties op een server gegenereerd. Dit heeft gevolgen voor de prestaties en de logboekopslag. U wordt aangeraden Azure Monitor-logboeken te gebruiken, die opslag opties voor langere termijn bieden, evenals analyse-en waarschuwings functies. U wordt aangeraden de standaard logboekregistratie uit te schakelen om de impact van prestaties van extra logboekregistratie te verminderen:

   1. Stel de para meter `logging_collector` in op uit. 
   2. Start de server opnieuw op om deze wijziging toe te passen.

Ga naar de sectie resource logs van het [artikel server logboeken](concepts-server-logs.md)voor meer informatie over het instellen van logboek registratie voor Azure Storage, Event Hubs of Azure monitor Logboeken.

## <a name="installing-pgaudit"></a>PgAudit installeren

Als u pgAudit wilt installeren, moet u deze toevoegen aan de gedeelde vooraf geladen bibliotheken van de server. Voor een wijziging van de `shared_preload_libraries` para meter post gres moet de server opnieuw worden opgestart. U kunt para meters wijzigen met behulp van de [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure cli](howto-configure-server-parameters-using-cli.md)of [rest API](/rest/api/postgresql/configurations/createorupdate).

Met behulp van de [Azure Portal](https://portal.azure.com):

   1. Selecteer uw Azure Database for PostgreSQL-server.
   2. Selecteer op de zijbalk **server parameters**.
   3. Zoek de `shared_preload_libraries` para meter.
   4. Selecteer **pgaudit**.
   5. Start de server opnieuw op om de wijziging toe te passen.

   6. Verbinding maken met uw server met behulp van een-client (zoals psql) en de pgAudit-uitbrei ding inschakelen
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Als er een fout optreedt, controleert u of u de server opnieuw hebt opgestart na het opslaan `shared_preload_libraries` .

## <a name="pgaudit-settings"></a>pgAudit-instellingen

met pgAudit kunt u de sessie of object controle logboek registratie configureren. Met de [sessie controle logboek registratie](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) worden gedetailleerde logboeken van de uitgevoerde instructies opgenomen. De [object controle logboek registratie](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) is een controle bereik voor specifieke relaties. U kunt ervoor kiezen om een of beide typen logboek registratie in te stellen. 

> [!NOTE]
> pgAudit-instellingen zijn opgegeven gloabally en kunnen niet worden opgegeven op een Data Base-of Role niveau.

Nadat u [pgAudit hebt geïnstalleerd](#installing-pgaudit), kunt u de para meters configureren om de logboek registratie te starten. De [pgAudit-documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) bevat de definitie van elke para meter. Test eerst de para meters en controleer of u het verwachte gedrag krijgt.

> [!NOTE]
> Als `pgaudit.log_client` wordt ingesteld op aan, worden logboeken omgeleid naar een client proces (zoals psql) in plaats van naar het bestand te schrijven. Deze instelling moet over het algemeen uitgeschakeld blijven. <br> <br>
> `pgaudit.log_level` wordt alleen ingeschakeld als `pgaudit.log_client` aan is.

> [!NOTE]
> In Azure Database for PostgreSQL `pgaudit.log` kan niet worden ingesteld met behulp van een `-` (minteken) snelkoppeling-teken zoals beschreven in de pgAudit-documentatie. Alle vereiste instructieklassen (lezen, schrijven, enzovoort) moeten afzonderlijk worden opgegeven.

### <a name="audit-log-format"></a>Indeling van auditlogboek
Elke controle vermelding wordt aan `AUDIT:` het begin van de logboek regel aangeduid. De indeling van de rest van de vermelding wordt beschreven in de [pgAudit-documentatie](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Als u andere velden nodig hebt om te voldoen aan uw audit vereisten, gebruikt u de para meter post gres `log_line_prefix` . `log_line_prefix` is een teken reeks die aan het begin van elke post gres-logboek regel wordt uitgevoerd. De volgende `log_line_prefix` instelling bevat bijvoorbeeld tijds tempel, gebruikers naam, database naam en proces-id:

```
t=%m u=%u db=%d pid=[%p]:
```

Ga voor meer informatie naar `log_line_prefix` de [postgresql-documentatie](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Aan de slag
Als u snel aan de slag wilt gaan, stelt `pgaudit.log` u in `WRITE` en opent u de logboeken om de uitvoer te controleren. 

## <a name="viewing-audit-logs"></a>Audit logboeken weer geven
Als u. log-bestanden gebruikt, worden uw audit Logboeken in hetzelfde bestand opgenomen als uw PostgreSQL-fout Logboeken. U kunt logboek bestanden downloaden via de Azure- [Portal](howto-configure-server-logs-in-portal.md) of [cli](howto-configure-server-logs-using-cli.md). 

Als u Azure resource logging gebruikt, is de manier waarop u toegang hebt tot de logboeken afhankelijk van het eind punt dat u kiest. Zie het artikel over het [opslag account voor logboeken](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) voor Azure Storage. Zie het artikel [Stream Azure logs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) voor Event hubs.

Voor Azure Monitor-logboeken worden logboeken verzonden naar de werk ruimte die u hebt geselecteerd. De post gres-Logboeken gebruiken de **AzureDiagnostics** -verzamelings modus, zodat ze kunnen worden opgevraagd vanuit de tabel AzureDiagnostics. De velden in de tabel worden hieronder beschreven. Meer informatie over het uitvoeren van query's en waarschuwingen vindt u in het overzicht van de [Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md) .

U kunt deze query gebruiken om aan de slag te gaan. U kunt waarschuwingen configureren op basis van query's.

Zoeken naar alle post gres-logboeken voor een bepaalde server in de afgelopen dag
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het registreren van Azure Database for PostgreSQL](concepts-server-logs.md)
- Meer informatie over het instellen van para meters met behulp van de [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure cli](howto-configure-server-parameters-using-cli.md)of [rest API](/rest/api/postgresql/configurations/createorupdate).