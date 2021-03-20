---
title: Azure SQL-analyse oplossing in Azure Monitor | Microsoft Docs
description: Azure SQL-analyse oplossing helpt u bij het beheren van uw Azure SQL-data bases
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 54ef88e65925ba9c7e9fe2e44ef0c76fbc9ceb04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717482"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL Database bewaken met behulp van Azure SQL-analyse (preview)

![Azure SQL-analyse-symbool](./media/azure-sql/azure-sql-symbol.png)

Azure SQL-analyse is een geavanceerde Cloud bewakings oplossing voor het bewaken van de prestaties van alle Azure SQL-data bases op schaal en op meerdere abonnementen in één weer gave. Azure SQL-analyse verzamelt en visualiseert metrische prestatie gegevens met ingebouwde intelligentie voor het oplossen van prestaties.

Door deze verzamelde metrische gegevens te gebruiken, kunt u aangepaste bewakings regels en waarschuwingen maken. Azure SQL-analyse helpt u bij het identificeren van problemen op elke laag van uw toepassings stack. Het maakt gebruik van Azure Diagnostic-metrische gegevens samen met Azure Monitor-weer gaven voor het presen teren van informatie over al uw Azure SQL-data bases in één Log Analytics-werk ruimte. Met Azure Monitor kunt u gestructureerde en ongestructureerde gegevens verzamelen, correleren en visualiseren.

Zie de Inge sloten video voor een praktische beschrijving van het gebruik van Azure SQL-analyse-oplossing en voor typische gebruiks scenario's:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Verbonden bronnen

Azure SQL-analyse is een bewakings oplossing die alleen in de Cloud wordt ondersteund voor het streamen van de telemetrie van diagnostische gegevens voor alle Azure SQL-data bases Omdat Azure SQL-analyse geen agents gebruikt om verbinding te maken met Azure Monitor, biedt het geen ondersteuning voor de bewaking van SQL Server die on-premises of in virtuele machines worden gehost.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| [Diagnostische instellingen](../essentials/diagnostic-settings.md) | **Ja** | Azure metrische en logboek gegevens worden rechtstreeks door Azure naar Azure Monitor logboeken verzonden. |
| [Azure Storage-account](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Nee | Azure Monitor leest de gegevens van een opslag account niet. |
| [Windows-agents](../agents/agent-windows.md) | Nee | Directe Windows-agents worden niet gebruikt door Azure SQL-analyse. |
| [Linux-agents](../vm/quick-collect-linux-computer.md) | Nee | Direct Linux-agents worden niet gebruikt door Azure SQL-analyse. |
| [Beheergroep System Center Operations Manager](../agents/om-agents.md) | Nee | Een directe verbinding van de Operations Manager agent naar Azure Monitor wordt niet gebruikt door Azure SQL-analyse. |

## <a name="azure-sql-analytics-options"></a>Azure SQL-analyse opties

De onderstaande tabel bevat een overzicht van de ondersteunde opties voor twee versies van het dash board Azure SQL-analyse, één voor Azure SQL Database en de andere voor Azure SQL Managed instance-data bases.

| Azure SQL-analyse optie | Beschrijving | Ondersteuning voor SQL Database | Ondersteuning voor SQL Managed Instance |
| --- | ------- | ----- | ----- |
| Resource per type | Perspectief dat alle bewaakte resources telt. | Ja | Ja |
| Inzichten | Voorziet in een hiërarchisch inzoomen op de prestaties van Intelligent Insights. | Ja | Ja |
| Fouten | Voorziet in hiërarchische inzoomen op SQL-fouten die zijn opgetreden in de data bases. | Ja | Ja |
| Time-outs | Voorziet in hiërarchische inzoomen op SQL-time-outs die zijn opgetreden in de data bases. | Ja | Nee |
| Blok keren | Voorziet in hiërarchische inzoomen op SQL-blok keringen die zijn opgetreden in de data bases. | Ja | Nee |
| Data base wacht | Voorziet in hiërarchische inzoomen op het database niveau van SQL-wacht statistieken. Bevat samen vattingen van de totale wacht tijd en de wacht tijd per wacht type. |Ja | Nee |
| Query duur | Biedt hiërarchische inzoomen op de query uitvoerings statistieken, zoals de query duur, het CPU-gebruik, het IO-gebruik van gegevens, het IO-gebruik van het logboek. | Ja | Ja |
| Querywachttijden | Voorziet in hiërarchische inzoomen op de query wacht statistieken per wachten. | Ja | Ja |

## <a name="configuration"></a>Configuratie

Gebruik het proces beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](./solutions.md) om Azure SQL-analyse (preview) toe te voegen aan uw log Analytics-werk ruimte.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Azure SQL Database voor het streamen van diagnostische gegevens configureren

Zodra u Azure SQL-analyse oplossing hebt gemaakt in uw werk ruimte, moet u **elke resource configureren** die u wilt bewaken om de diagnostische telemetrie naar Azure SQL-analyse te streamen. Volg de gedetailleerde instructies op deze pagina:

- Schakel Azure Diagnostics voor uw data base in om de [telemetrie diagnostische gegevens naar Azure SQL-analyse te streamen](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

De bovenstaande pagina bevat ook instructies voor het inschakelen van ondersteuning voor het bewaken van meerdere Azure-abonnementen vanuit een enkele Azure SQL-analyse-werk ruimte als één glas venster.

## <a name="using-azure-sql-analytics"></a>Azure SQL-analyse gebruiken

Wanneer u Azure SQL-analyse toevoegt aan uw werk ruimte, wordt de Azure SQL-analyse tegel toegevoegd aan uw werk ruimte en wordt deze weer gegeven in overzicht. Selecteer overzichts koppeling weer geven om de inhoud van de tegel te laden.

![Tegel Azure SQL-analyse overzicht](./media/azure-sql/azure-sql-sol-tile-01.png)

Zodra de tegel is geladen, wordt het aantal data bases en elastische Pools weer gegeven in SQL Database en instanties en instance-data bases in het SQL-beheerde exemplaar van waaruit Azure SQL-analyse diagnostische telemetrie ontvangt.

![Azure SQL-analyse tegel](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL-analyse biedt twee afzonderlijke weer gaven: één voor het bewaken van SQL Database en de andere weer gave voor het bewaken van het SQL-beheerde exemplaar.

Als u Azure SQL-analyse bewakings dashboard voor SQL Database wilt weer geven, klikt u op het bovenste gedeelte van de tegel. Klik op het onderste deel van de tegel om Azure SQL-analyse bewakings dashboard voor SQL Managed instance te bekijken.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL-analyse gegevens weer geven

Het dash board bevat het overzicht van alle data bases die via verschillende perspectieven worden bewaakt. Als u verschillende perspectieven wilt gebruiken, moet u de juiste metrische gegevens of Logboeken op uw SQL-resources inschakelen om te streamen naar Log Analytics werk ruimte.

Als sommige metrische gegevens of logboeken niet naar Azure Monitor worden gestreamd, worden de tegels in Azure SQL-analyse niet gevuld met controle-informatie.

### <a name="sql-database-view"></a>SQL Database weer gave

Zodra de Azure SQL-analyse tegel voor de data base is geselecteerd, wordt het bewakings dashboard weer gegeven.

![Scherm opname van het bewakings dashboard.](./media/azure-sql/azure-sql-sol-overview.png)

Als u een tegel selecteert, wordt er een inzoom rapport in het specifieke perspectief geopend. Zodra het perspectief is geselecteerd, wordt het inzoom rapport geopend.

![Scherm opname van het inzoom rapport in het specifieke perspectief.](./media/azure-sql/azure-sql-sol-metrics.png)

Elk perspectief in deze weer gave bevat samen vattingen van het abonnement, de server, de elastische pool en de database niveaus. Daarnaast toont elk perspectief een specifiek perspectief voor het rapport aan de rechter kant. Als u abonnement, Server, pool of data base selecteert in de lijst, wordt het inzoomen voortgezet.

### <a name="sql-managed-instance-view"></a>Weer gave SQL Managed instance

Zodra de Azure SQL-analyse tegel voor de data bases is geselecteerd, wordt het bewakings dashboard weer gegeven.

![Overzicht van Azure SQL-analyse](./media/azure-sql/azure-sql-sol-overview-mi.png)

Als u een tegel selecteert, wordt er een inzoom rapport in het specifieke perspectief geopend. Zodra het perspectief is geselecteerd, wordt het inzoom rapport geopend.

Als u de weer gave SQL Managed instance selecteert, worden de details weer gegeven van het gebruik van de instantie, de exemplaar databases en de telemetrie voor de query's die worden uitgevoerd in het beheerde exemplaar.

![Azure SQL-analyse-time-outs](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights rapport

Azure SQL Database [intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) kunt u zien wat er gebeurt met de prestaties van alle Azure SQL-data bases. Alle Intelligent Insights verzamelde gegevens kunnen worden gevisualiseerd en geopend via het inzichten-perspectief.

![Inzichten Azure SQL-analyse](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastische Pools en database rapporten

Zowel elastische Pools als data bases hebben hun eigen specifieke rapporten waarin alle gegevens worden weer gegeven die voor de resource zijn verzameld in de opgegeven tijd.

![Azure SQL-analyse-data base](./media/azure-sql/azure-sql-sol-database.png)

![Elastische Azure SQL-groep](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Query rapporten

Met de duur van de query en de query wordt gewacht op perspectieven, kunt u de prestaties van een query door middel van het query rapport correleren. Dit rapport vergelijkt de query prestaties over verschillende data bases en maakt het eenvoudig om data bases te lokaliseren die de geselecteerde query goed tegenover elkaar hebben.

![Azure SQL-analyse Query's](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Machtigingen

Als u Azure SQL-analyse wilt gebruiken, moeten gebruikers beschikken over een minimale machtiging van de rol lezer in Azure. Deze rol staat gebruikers echter niet toe om de query tekst te zien of om automatische afstemmings acties uit te voeren. Meer strikte rollen in azure die het gebruik van Azure SQL-analyse in de meeste mate toestaan, zijn eigenaar, bijdrager, SQL DB-bijdrager of SQL Server Inzender. U kunt ook overwegen om een aangepaste rol in de portal te maken met specifieke machtigingen die alleen vereist zijn voor het gebruik van Azure SQL-analyse en zonder toegang tot het beheer van andere resources.

### <a name="creating-a-custom-role-in-portal"></a>Een aangepaste rol maken in de portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Door te herkennen dat sommige organisaties strikte machtigings controles in azure afdwingen, kunt u het volgende Power shell-script gebruiken voor het maken van een aangepaste rol SQL Analytics-bewakings operator in Azure Portal met de minimale Lees-en schrijf machtigingen die zijn vereist om Azure SQL-analyse te gebruiken.

Vervang {SubscriptionId} in het onderstaande script door de ID van uw Azure-abonnement en voer het script dat is aangemeld als eigenaar of Inzender rol in azure uit.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Zodra de nieuwe rol is gemaakt, wijst u deze rol toe aan elke gebruiker die u nodig hebt om aangepaste machtigingen te verlenen voor het gebruik van Azure SQL-analyse.

## <a name="analyze-data-and-create-alerts"></a>Gegevens analyseren en waarschuwingen maken

Gegevens analyse in Azure SQL-analyse is gebaseerd op [log Analytics taal](../logs/get-started-queries.md) voor uw aangepaste query en rapportage. Hier vindt u een beschrijving van de beschik bare gegevens die zijn verzameld uit de database resource voor aangepaste query's in [metrische gegevens en logboeken](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available).

Automatische waarschuwingen in Azure SQL-analyse is gebaseerd op het schrijven van een Log Analytics query die een waarschuwing activeert wanneer aan een voor waarde wordt voldaan. Zoek hieronder een aantal voor beelden over Log Analytics query's waarop waarschuwingen kunnen worden ingesteld in Azure SQL-analyse.

### <a name="creating-alerts-for-azure-sql-database"></a>Waarschuwingen voor Azure SQL Database maken

U kunt eenvoudig [waarschuwingen maken](../alerts/alerts-metric.md) met de gegevens die afkomstig zijn uit Azure SQL database resources. Hier volgen enkele nuttige [logboek query's](../logs/log-query-overview.md) die u kunt gebruiken met een logboek waarschuwing:

#### <a name="high-cpu"></a>Hoog CPU-gebruik

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar Azure SQL-analyse.
> - Vervang de waarde van metric cpu_percent door dtu_consumption_percent om in plaats daarvan hoge DTU-resultaten te verkrijgen.

#### <a name="high-cpu-on-elastic-pools"></a>Hoge CPU op elastische Pools

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar Azure SQL-analyse.
> - Vervang de waarde van metric cpu_percent door dtu_consumption_percent om in plaats daarvan hoge DTU-resultaten te verkrijgen.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Opslag met een gemiddelde van meer dan 95% in de afgelopen uur

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases basis gegevens streamen naar Azure SQL-analyse.
> - Deze query vereist dat er een waarschuwings regel wordt ingesteld voor het starten van een waarschuwing wanneer er resultaten zijn (> 0 resultaten) van de query, waardoor wordt bepaald dat de voor waarde in sommige data bases bestaat. De uitvoer is een lijst met database resources die boven de storage_threshold binnen de time_range zijn gedefinieerd.
> - De uitvoer is een lijst met database resources die boven de storage_threshold binnen de time_range zijn gedefinieerd.

#### <a name="alert-on-intelligent-insights"></a>Waarschuwen voor intelligente inzichten

> [!IMPORTANT]
> Als een Data Base goed wordt uitgevoerd en er geen Intelligent Insights zijn gegenereerd, mislukt deze query met een fout bericht: kan de scalaire expressie met de naam ' rootCauseAnalysis_s ' niet omzetten. Dit gedrag wordt verwacht voor alle gevallen waarin er geen intelligente inzichten zijn voor de data base.

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - De vereiste voor het instellen van deze waarschuwing is dat bewaakte data bases SQLInsights Diagnostische logboeken naar Azure SQL-analyse.
> - Voor deze query moet een waarschuwings regel worden ingesteld om te worden uitgevoerd met dezelfde frequentie als alert_run_interval om dubbele resultaten te voor komen. De regel moet zo worden ingesteld dat de waarschuwing wordt geactiveerd wanneer er resultaten zijn (> 0 resultaten) van de query.
> - Pas de alert_run_interval aan om het tijds bereik op te geven om te controleren of de voor waarde is opgetreden op data bases die zijn geconfigureerd voor het streamen van SQLInsights-logboek naar Azure SQL-analyse.
> - Pas de insights_string aan voor het vastleggen van de uitvoer van de analyse tekst van de hoofd oorzaak van Insights. Dit is dezelfde tekst die wordt weer gegeven in de gebruikers interface van Azure SQL-analyse die u vanuit de bestaande inzichten kunt gebruiken. U kunt ook de onderstaande query gebruiken om de tekst weer te geven van alle inzichten die zijn gegenereerd in uw abonnement. Gebruik de uitvoer van de query om de afzonderlijke teken reeksen voor het instellen van waarschuwingen op inzichten te verzamelen.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>Waarschuwingen maken voor SQL Managed instance

#### <a name="storage-is-above-90"></a>Opslag is meer dan 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - De vereisten voor het instellen van deze waarschuwing zijn dat het bewaakte beheerde exemplaar het streamen van ResourceUsageStats-logboek heeft ingeschakeld voor Azure SQL-analyse.
> - Deze query vereist dat er een waarschuwings regel wordt ingesteld om een waarschuwing te starten wanneer er resultaten zijn (> 0 resultaten) van de query, waarbij wordt opgegeven dat de voor waarde bestaat op het beheerde exemplaar. De uitvoer is opslag percentage van het beheerde exemplaar.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Het gemiddelde CPU-verbruik is hoger dan 95% in de afgelopen uur

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - De vereisten voor het instellen van deze waarschuwing zijn dat het bewaakte beheerde exemplaar het streamen van ResourceUsageStats-logboek heeft ingeschakeld voor Azure SQL-analyse.
> - Deze query vereist dat er een waarschuwings regel wordt ingesteld om een waarschuwing te starten wanneer er resultaten zijn (> 0 resultaten) van de query, waarbij wordt opgegeven dat de voor waarde bestaat op het beheerde exemplaar. De uitvoer is gemiddeld CPU-gebruiks percentage verbruik in gedefinieerde periode op het beheerde exemplaar.

### <a name="pricing"></a>Prijzen

Hoewel Azure SQL-analyse gratis is voor gebruik, kunt u het verbruik van de telemetrie van diagnostische gegevens boven de gratis eenheden van de gegevensopname die elke maand van toepassing is, bekijken [log Analytics prijzen](https://azure.microsoft.com/pricing/details/monitor). De gratis eenheden van gegevens opname bieden gratis bewaking van verschillende data bases per maand. Meer actieve data bases met zwaarere workloads nemen meer gegevens op in plaats van inactieve data bases. U kunt uw gegevensopname verbruik eenvoudig bewaken in Azure SQL-analyse door OMS-werk ruimte te selecteren in het navigatie menu van Azure SQL-analyse en vervolgens gebruik en geschatte kosten te selecteren.

## <a name="next-steps"></a>Volgende stappen

- Gebruik [logboek query's](../logs/log-query-overview.md) in azure monitor om gedetailleerde Azure SQL-gegevens weer te geven.
- [Maak uw eigen Dash boards](../visualize/tutorial-logs-dashboards.md) waarin Azure SQL-gegevens worden weer gegeven.
- [Maak waarschuwingen](../alerts/alerts-overview.md) wanneer er specifieke Azure SQL-gebeurtenissen optreden.

