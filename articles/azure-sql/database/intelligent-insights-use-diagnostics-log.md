---
title: Diagnostische logboeken van Intelligent Insights prestatie
description: Intelligent Insights biedt een diagnostische logboek van Azure SQL Database en prestatie problemen met Azure SQL Managed instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: b03c21eea18c966616154b5cfc5df5d8924fd335
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589303"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Het Intelligent Insights prestatie diagnose logboek van Azure SQL Database en prestatie problemen met Azure SQL Managed instance gebruiken
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Deze pagina bevat informatie over het gebruik van het logboek voor prestatie diagnose dat is gegenereerd door [intelligent Insights](intelligent-insights-overview.md) van Azure SQL database en prestatie problemen met Azure SQL Managed instance, de indeling en de gegevens die het bevat voor uw aangepaste ontwikkelings behoeften. U kunt dit diagnostische logboek verzenden naar [Azure monitor-logboeken](../../azure-monitor/insights/azure-sql.md), [Azure Event hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)of een oplossing van derden voor aangepaste DevOps-waarschuwingen en rapportage mogelijkheden.

> [!NOTE]
> Intelligent Insights is een preview-functie die niet beschikbaar is in de volgende regio's: Europa-west, Europa-noord, VS-West 1 en VS-Oost 1.

## <a name="log-header"></a>Logboek header

Het diagnostische logboek gebruikt de JSON-standaard indeling voor het uitvoeren van Intelligent Insights bevindingen. De eigenschap van de exacte categorie voor toegang tot een Intelligent Insights-logboek is de vaste waarde ' SQLInsights '.

De header van het logboek is gebruikelijk en bestaat uit het tijds tempel (TimeGenerated) dat laat zien wanneer een item is gemaakt. Het bevat ook een resource-ID (ResourceId) die verwijst naar de specifieke data base waarin de vermelding is gekoppeld. De categorie (categorie), het niveau (niveau) en de bewerkings naam (Operationname) zijn vaste eigenschappen waarvan de waarden niet worden gewijzigd. Ze geven aan dat de logboek vermelding informatief is en afkomstig is van Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Probleem-ID en betrokken data base

De Issue Identification-eigenschap (issueId_d) biedt een manier om prestatie problemen op te lossen tot ze zijn opgelost. Meerdere gebeurtenis records in de logboek rapportage status van hetzelfde probleem delen dezelfde probleem-ID.

Samen met de probleem-ID rapporteert het diagnostische Logboeken het start (intervalStartTime_t) en het eind (intervalEndTme_t) tijds tempels van de specifieke gebeurtenis gerelateerd aan een probleem dat wordt gerapporteerd in het diagnostische logboek.

De eigenschap elastische groep (elasticPoolName_s) geeft aan aan welke elastische pool de data base met een probleem behoort. Als de data base geen deel uitmaakt van een elastische pool, heeft deze eigenschap geen waarde. De Data Base waarin een probleem is gedetecteerd, wordt vermeld in de eigenschap data base name (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Gedetecteerde problemen

De volgende sectie van het prestatie logboek van Intelligent Insights bevat prestatie problemen die zijn gedetecteerd via ingebouwde kunst matige intelligentie. Detecties worden vermeld in eigenschappen in het JSON Diagnostics-logboek. Deze detecties bestaan uit de categorie van een probleem, de impact van het probleem, de betrokken query's en de metrische gegevens. De detectie-eigenschappen kunnen meerdere prestatie problemen bevatten die zijn gedetecteerd.

Gedetecteerde prestatie problemen worden gerapporteerd met de volgende detectie van eigenschappen structuur:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

De volgende tabel bevat de prestatie patronen die kunnen worden gegenereerd en de details die in het diagnostische logboek worden beschreven.

### <a name="detection-category"></a>Detectie categorie

Met de eigenschap Category (categorie) wordt de categorie van Detecteer bare prestatie patronen beschreven. Zie de volgende tabel voor alle mogelijke categorieën van Detecteer bare prestatie patronen. Zie [problemen met database prestaties oplossen met intelligent Insights](intelligent-insights-troubleshoot-performance.md)voor meer informatie.

Afhankelijk van het prestatie probleem dat is gedetecteerd, verschillen de details die worden gegenereerd in het logboek bestand voor diagnostische gegevens dienovereenkomstig.

| Detecteerbare prestatiepatronen | Details die zijn gegenereerd |
| :------------------- | ------------------- |
| Bron limieten bereiken | <li>Betrokken resources</li><li>Query's uitvoeren op hashes</li><li>Resource verbruiks percentage</li> |
| Toename van de workload | <li>Aantal query's waarvan de uitvoering is verhoogd</li><li>Een query uitvoeren op hashes van query's met de grootste bijdrage aan de toename van de werk belasting</li> |
| Geheugenbelasting | <li>Geheugen-Clerk</li> |
| Vergrendelen | <li>Betroffen query-hashes</li><li>Query-hashes blok keren</li> |
| Verhoogde MAXDOP | <li>Query's uitvoeren op hashes</li><li>CXP wacht tijden</li><li>Wacht tijden</li> |
| Pagelatch-conflicten | <li>Query's uitvoeren op hashes van query's die conflicten veroorzaken</li> |
| Ontbrekende index | <li>Query's uitvoeren op hashes</li> |
| Nieuwe query | <li>Query-hash van de nieuwe query's</li> |
| Ongebruikelijke wacht statistieken | <li>Ongebruikelijke wacht typen</li><li>Query's uitvoeren op hashes</li><li>Wacht tijden voor query's</li> |
| TempDB-conflicten | <li>Query's uitvoeren op hashes van query's die conflicten veroorzaken</li><li>Query-toewijzing aan de totale wacht tijd van de pagelatch-inhoudsatie voor de data base [%]</li> |
| DTU-tekort elastische groep | <li>Elastische pool</li><li>Meest gebruikte Data Base voor DTU-verbruik</li><li>Percentage van groeps-DTU dat wordt gebruikt door de belangrijkste gebruiker</li> |
| Regressie plannen | <li>Query's uitvoeren op hashes</li><li>Goede plan-Id's</li><li>Ongeldige plan-Id's</li> |
| Wijziging in de configuratie waarde Database-Scoped | <li>Wijzigingen in de database bereik configuratie ten opzichte van de standaard waarden</li> |
| Trage client | <li>Query's uitvoeren op hashes</li><li>Wacht tijden</li> |
| Prijs categorie downgrade | <li>Tekst melding</li> |

### <a name="impact"></a>Impact

De eigenschap impact (effect) beschrijft hoeveel een gedetecteerd gedrag heeft bijgedragen aan het probleem dat een Data Base heeft. Het aantal effecten is van 1 tot 3, met 3 als de hoogste bijdrage, 2 als gemiddeld en 1 als de laagste bijdrage. De impact waarde kan worden gebruikt als invoer voor aangepaste automatisering van waarschuwingen, afhankelijk van uw specifieke behoeften. De beïnvloede eigenschaps query's (QueryHashes) bieden een lijst van de query-hashes die door een bepaalde detectie zijn beïnvloed.

### <a name="impacted-queries"></a>Beïnvloede query's

In de volgende sectie van het Intelligent Insights-logboek vindt u informatie over bepaalde query's die worden beïnvloed door de gedetecteerde prestatie problemen. Deze informatie wordt vermeld als een matrix met objecten die zijn Inge sloten in de eigenschap impact_s. De eigenschap impact bestaat uit entiteiten en metrische gegevens. Entiteiten verwijzen naar een bepaalde query (type: query). De unieke query-hash wordt vermeld onder de waarde van de eigenschap Value (waarde). Daarnaast worden alle query's die worden vermeld, gevolgd door een metriek en een waarde, die duiden op een gedetecteerd prestatie probleem.

In het volgende voor beeld van het logboek is vastgesteld dat de query met de hash-0x9102EXZ4 een verhoogde duur van de uitvoering (metrisch: DurationIncreaseSeconds) heeft. De waarde van 110 seconden geeft aan dat deze specifieke query 110 seconden langer duurde om uit te voeren. Omdat er meerdere query's kunnen worden gedetecteerd, kan dit specifieke logboek sectie meerdere query vermeldingen bevatten.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Metrische gegevens

De meet eenheid voor elke gerapporteerde metrische gegevens is beschikbaar onder de metrische eigenschap (metrisch) met de mogelijke waarden van seconden, getal en percentage. De waarde van een gemeten metriek wordt gerapporteerd in de waarde van de eigenschap Value (waarde).

De eigenschap DurationIncreaseSeconds biedt de maat eenheid in seconden. De CriticalErrorCount-maat eenheid is een getal dat een aantal fouten vertegenwoordigt.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Aanbevelingen voor het analyseren en verbeteren van de hoofd oorzaak

Het laatste deel van het Intelligent Insights prestatie logboek is van toepassing op de automatische analyse van de hoofd oorzaak van het probleem met de geïdentificeerde prestatie vermindering. De informatie wordt weer gegeven in mensen vriendelijker verbiage in de eigenschap hoofd oorzaak analyse (rootCauseAnalysis_s). Indien mogelijk zijn er verbeteringen in het logboek opgenomen.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

U kunt het Intelligent Insights prestatie logboek gebruiken met [Azure monitor-logboeken]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) of een oplossing van derden voor aangepaste DevOps-waarschuwingen en rapportage mogelijkheden.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [intelligent Insights](intelligent-insights-overview.md) -concepten.
- Meer informatie over het [oplossen van prestatie problemen met intelligent Insights](intelligent-insights-troubleshoot-performance.md).
- Meer informatie over het [bewaken van prestatie problemen met behulp van Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md).
- Meer informatie over het [verzamelen en gebruiken van logboek gegevens van uw Azure-resources](../../azure-monitor/essentials/platform-logs-overview.md).