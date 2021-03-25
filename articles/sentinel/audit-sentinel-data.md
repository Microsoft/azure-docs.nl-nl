---
title: Azure-Sentinel-query's en-activiteiten controleren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u query's en activiteiten controleert die zijn uitgevoerd in azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a02be0938b1ab925fb0343351ce1c414cc59c615
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044835"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Azure-Sentinel-query's en-activiteiten controleren

In dit artikel wordt beschreven hoe u controle gegevens kunt weer geven voor query's die worden uitgevoerd in uw Azure Sentinel-werk ruimte, zoals voor interne en externe nalevings vereisten in uw SOC-werk ruimte (Security Operations).

Azure Sentinel biedt toegang tot:

- De tabel **AzureActivity** , die details bevat over alle acties die zijn uitgevoerd in de Azure-Sentinel, zoals het bewerken van waarschuwings regels. De **AzureActivity** -tabel registreert geen specifieke query gegevens. Zie [controle met Azure-activiteiten logboeken](#auditing-with-azure-activity-logs)voor meer informatie.

- De tabel **LAQueryLogs** , die details bevat over de query's die worden uitgevoerd in log Analytics, inclusief query's die worden uitgevoerd vanuit Azure Sentinel. Zie [controle met LAQueryLogs](#auditing-with-laquerylogs)voor meer informatie.

> [!TIP]
> Naast de hand matige query's die in dit artikel worden beschreven, biedt Azure Sentinel een ingebouwde werkmap waarmee u de activiteiten in uw SOC-omgeving kunt controleren.
>
> Zoek in het gebied Azure Sentinel **Workbooks** naar de **werk ruimte audit** werkmap.



## <a name="auditing-with-azure-activity-logs"></a>Controleren met activiteiten logboeken van Azure

De audit logboeken van Azure Sentinel worden bijgehouden in de [activiteiten logboeken](../azure-monitor/essentials/platform-logs-overview.md)van Azure, waarbij de **AzureActivity** -tabel alle acties bevat die in uw Azure Sentinel-werk ruimte worden uitgevoerd.

U kunt de **AzureActivity** -tabel gebruiken bij het controleren van de activiteiten in uw Soc-omgeving met Azure Sentinel.

**Query's uitvoeren op de tabel AzureActivity**:

1. Verbind de gegevens bron van de [Azure-activiteit](connect-azure-activity.md) om controle gebeurtenissen te streamen naar een nieuwe tabel in het venster **Logboeken** met de naam AzureActivity.

1. Vervolgens voert u een query uit op de gegevens met behulp van KQL, zoals u zou doen met een andere tabel.

    De **AzureActivity** -tabel bevat gegevens uit vele services, waaronder Azure Sentinel. Als u alleen gegevens uit Azure Sentinel wilt filteren, start u de query met de volgende code:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Als u bijvoorbeeld wilt weten wie de laatste gebruiker was voor het bewerken van een bepaalde analyse regel, gebruikt u de volgende query (vervangen `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` door de regel-id van de regel die u wilt controleren):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Voeg meer para meters toe aan de query om de **AzureActivities** -tabel verder te verkennen, afhankelijk van wat u wilt rapporteren. De volgende secties bevatten andere voorbeeld query's die moeten worden gebruikt bij het controleren met **AzureActivity** -tabel gegevens. 

Zie voor meer informatie [Azure Sentinel data die zijn opgenomen in activiteiten logboeken van Azure](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Alle acties zoeken die door een specifieke gebruiker in de afgelopen 24 uur zijn uitgevoerd

In de volgende **AzureActivity** wordt een lijst weer gegeven met alle acties die zijn uitgevoerd door een specifieke Azure AD-gebruiker in de afgelopen 24 uur.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Alle Verwijder bewerkingen zoeken

In de volgende tabel **AzureActivity** worden alle Verwijder bewerkingen vermeld die worden uitgevoerd in uw Azure Sentinel-werk ruimte.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Azure-Sentinelgegevens opgenomen in activiteiten logboeken van Azure
 
De audit logboeken van Azure Sentinel worden bijgehouden in de [activiteiten logboeken van Azure](../azure-monitor/essentials/platform-logs-overview.md)en bevatten de volgende typen informatie:

|Bewerking  |Informatie typen  |
|---------|---------|
|**Gemaakt**     |Waarschuwingsregels <br> Case-opmerkingen <br>Reacties op incidenten <br>Opgeslagen Zoek opdrachten<br>Watchlists    <br>Werkmappen     |
|**Verwijderd**     |Waarschuwingsregels <br>Bladwijzers <br>Gegevensconnectors <br>Incidenten <br>Opgeslagen Zoek opdrachten <br>Instellingen <br>Threat Intelligence-rapporten <br>Watchlists      <br>Werkmappen <br>Werkstroom  |
|**Vernieuwd**     |  Waarschuwingsregels<br>Bladwijzers <br> Meldingen <br> Gegevensconnectors <br>Incidenten <br>Reacties op incidenten <br>Threat Intelligence-rapporten <br> Werkmappen <br>Werkstroom       |
|     |         |

U kunt ook de logboeken van Azure-activiteiten gebruiken om te controleren op gebruikers autorisaties en-licenties. 

In de volgende tabel worden bijvoorbeeld de geselecteerde bewerkingen in azure-activiteiten logboeken weer gegeven met de specifieke resource waaruit de logboek gegevens worden opgehaald.

|Naam van bewerking|    Resourcetype|
|----|----|
|Werkmap maken of bijwerken  |Micro soft. Insights/werkmappen|
|Werkmap verwijderen    |Micro soft. Insights/werkmappen|
|Werk stroom instellen   |Microsoft.Logic/workflows|
|Werk stroom verwijderen    |Microsoft.Logic/workflows|
|Opgeslagen zoek opdracht maken    |Micro soft. OperationalInsights/werk ruimten/savedSearches|
|Opgeslagen zoek opdracht verwijderen    |Micro soft. OperationalInsights/werk ruimten/savedSearches|
|Waarschuwings regels bijwerken |Micro soft. SecurityInsights/alertRules|
|Waarschuwings regels verwijderen |Micro soft. SecurityInsights/alertRules|
|Reactie acties van waarschuwings regel bijwerken |Micro soft. SecurityInsights/alertRules/acties|
|Reactie acties voor waarschuwings regels verwijderen |Micro soft. SecurityInsights/alertRules/acties|
|Blad wijzers bijwerken   |Micro soft. SecurityInsights/blad wijzers|
|Blad wijzers verwijderen   |Micro soft. SecurityInsights/blad wijzers|
|Cases bijwerken   |Micro soft. SecurityInsights/cases|
|Case-onderzoek bijwerken  |Micro soft. SecurityInsights/cases/onderzoeken|
|Case-opmerkingen maken   |Micro soft. SecurityInsights/cases/opmerkingen|
|Gegevens connectors bijwerken |Micro soft. SecurityInsights/dataConnectors|
|Gegevens connectors verwijderen |Micro soft. SecurityInsights/dataConnectors|
|Update-instellingen    |Micro soft. SecurityInsights/Settings|
| | |

Zie [gebeurtenis schema voor Azure-activiteiten logboek](../azure-monitor/essentials/activity-log-schema.md)voor meer informatie.


## <a name="auditing-with-laquerylogs"></a>Controleren met LAQueryLogs

De **LAQueryLogs** -tabel bevat details over logboek query's die in log Analytics worden uitgevoerd. Omdat Log Analytics wordt gebruikt als het onderliggende gegevens archief van Azure Sentinel, kunt u uw systeem zo configureren dat LAQueryLogs-gegevens worden verzameld in uw Azure Sentinel-werk ruimte.

LAQueryLogs-gegevens bevatten informatie zoals:

- Wanneer query's werden uitgevoerd
- Wie voert query's uit in Log Analytics
- Welk hulp programma is gebruikt om query's uit te voeren in Log Analytics, zoals Azure Sentinel
- De query teksten zelf
- Prestatie gegevens voor elke uitgevoerde query

> [!NOTE]
> - De **LAQueryLogs** -tabel bevat alleen query's die zijn uitgevoerd op de Blade logboeken van Azure Sentinel. Het bevat geen query's die worden uitgevoerd door geplande analyse regels, met behulp van de **onderzoek grafiek** of op de Azure Sentinel **jacht** -pagina.
> - Er kan een korte vertraging optreden tussen de tijd dat een query wordt uitgevoerd en de gegevens worden ingevuld in de tabel **LAQueryLogs** . U kunt het beste ongeveer 5 minuten wachten om de **LAQueryLogs** -tabel voor controle gegevens op te vragen.
>


**Query's uitvoeren op de tabel LAQueryLogs**:

1. De **LAQueryLogs** -tabel is niet standaard ingeschakeld in uw log Analytics-werk ruimte. Als u **LAQueryLogs** -gegevens wilt gebruiken bij het controleren in azure Sentinel, moet u eerst de **LAQueryLogs** inschakelen in het gebied **Diagnostische instellingen** van log Analytics werk ruimte.

    Zie [Query's controleren in azure monitor-logboeken](../azure-monitor/logs/query-audit.md)voor meer informatie.


1. Vervolgens voert u een query uit op de gegevens met behulp van KQL, zoals u zou doen met een andere tabel.

    De volgende query laat bijvoorbeeld zien hoeveel query's er in de afgelopen week zijn uitgevoerd, per dag:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

In de volgende secties ziet u meer voor beelden van query's die worden uitgevoerd in de **LAQueryLogs** -tabel bij het controleren van activiteiten in uw Soc-omgeving met behulp van Azure Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Het aantal query's dat wordt uitgevoerd waarbij het antwoord niet OK is

In de volgende tabel query **LAQueryLogs** wordt het aantal uitgevoerde query's weer gegeven, waarbij iets anders dan een HTTP-antwoord van **200 OK** werd ontvangen. Dit aantal bevat bijvoorbeeld query's die niet konden worden uitgevoerd.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Gebruikers weer geven voor CPU-intensieve query's

De volgende tabel query **LAQueryLogs** bevat de gebruikers die de meeste CPU-intensieve query's hebben uitgevoerd, op basis van de gebruikte CPU en de lengte van de query tijd.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Gebruikers weer geven die de meeste query's in de afgelopen week hebben uitgevoerd

De volgende **LAQueryLogs** -tabel query bevat de gebruikers die de meeste query's in de afgelopen week hebben uitgevoerd.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Waarschuwingen voor Azure-Sentinel-activiteiten configureren

U kunt de controle bronnen van Azure Sentinel gebruiken om proactieve waarschuwingen te maken.

Als u bijvoorbeeld gevoelige tabellen in uw Azure Sentinel-werk ruimte hebt, gebruikt u de volgende query om u te waarschuwen elke keer dat er een query wordt uitgevoerd op deze tabellen:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Volgende stappen

In azure Sentinel gebruikt u de **werk ruimte audit** werkmap om de activiteiten in uw Soc-omgeving te controleren.

Zie [zelf studie: uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)voor meer informatie.