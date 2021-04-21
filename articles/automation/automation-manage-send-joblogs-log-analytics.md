---
title: Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken
description: In dit artikel wordt beschreven hoe u taakstatus- en runbook jobstreams verzendt naar Azure Monitor logboeken.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c24c38368ef20dadd0dc19b1804f9d27ad68bd27
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833684"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken

Azure Automation kunt runbook-taakstatus en taakstromen verzenden naar uw Log Analytics-werkruimte. Dit proces heeft geen betrekking op het koppelen van werkruimten en is volledig onafhankelijk. Taaklogboeken en taakstromen zijn zichtbaar in de Azure Portal, of met PowerShell, voor afzonderlijke taken. Hierdoor kunt u eenvoudige onderzoeken uitvoeren. Met de Azure Monitor kunt u het volgende doen:

* Krijg inzicht in de status van uw Automation-taken.
* Activeer een e-mailbericht of waarschuwing op basis van de status van uw runbook-taak (bijvoorbeeld mislukt of tijdelijk).
* Schrijf geavanceerde query's over uw taakstromen.
* Taken correleren in Automation-accounts.
* Gebruik aangepaste weergaven en zoekquery's om uw runbookresultaten, runbook-taakstatus en andere gerelateerde sleutelindicatoren of metrische gegevens te visualiseren.

## <a name="prerequisites"></a>Vereisten

Als u uw Automation-logboeken wilt verzenden naar Azure Monitor logboeken, hebt u het volgende nodig:

* De nieuwste versie van [Azure PowerShell](/powershell/azure/).

* Een Log Analytics-werkruimte en de resource-id. Zie Get [started with Azure Monitor logs (Aan de slag met logboeken) Azure Monitor meer informatie.](../azure-monitor/overview.md)

* De resource-id van uw Azure Automation account.

## <a name="how-to-find-resource-ids"></a>Resource-ID's vinden

1. Gebruik de volgende opdracht om de resource-id voor uw Azure Automation vinden:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Kopieer de waarde voor **ResourceID**.

3. Gebruik de volgende opdracht om de resource-id van uw Log Analytics-werkruimte te vinden:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Kopieer de waarde voor **ResourceID**.

Als u resultaten van een specifieke resourcegroep wilt retourneren, moet u de `-ResourceGroupName` parameter opnemen. Zie [Get-AzResource voor meer informatie.](/powershell/module/az.resources/get-azresource)

Als u meer dan één Automation-account of werkruimte in de uitvoer van de voorgaande opdrachten hebt, kunt u de naam en andere gerelateerde eigenschappen vinden die deel uitmaken van de volledige resource-id van uw Automation-account door het volgende uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in Azure Portal Automation-account op de pagina **Automation-accounts.**
1. Selecteer op de pagina van het geselecteerde Automation-account onder **Accountinstellingen** de optie **Eigenschappen.**
1. Let op **de** pagina Eigenschappen op de details die hieronder worden weergegeven.

    ![Eigenschappen van Automation-account](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

Diagnostische instellingen voor Automation bieden ondersteuning voor het doorsturen van de volgende platformlogboeken en metrische gegevens:

* JobLogs
* JobStreams
* DSCNodeStatus
* Metrische gegevens : totaal aantal taken, totaal aantal computertaken voor update-implementatie, totaal aantal update-implementaties

Als u uw Automation-logboeken wilt verzenden [](../azure-monitor/essentials/diagnostic-settings.md) naar Azure Monitor logboeken, bekijkt u Diagnostische instellingen maken om inzicht te krijgen in de functie en methoden die beschikbaar zijn voor het configureren van diagnostische instellingen voor het verzenden van platformlogboeken.

## <a name="azure-monitor-log-records"></a>Azure Monitor logboekrecords maken

Azure Automation diagnostische gegevens kunt u twee typen records maken in Azure Monitor logboeken, gelabeld als `AzureDiagnostics` . De tabellen in de volgende secties zijn voorbeelden van records die Azure Automation gegenereerd en de gegevenstypen die worden weergegeven in de zoekresultaten van logboeken.

### <a name="job-logs"></a>Taaklogboeken

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De aanroeper die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| Tenant_g | GUID die de tenant voor de aanroeper identificeert. |
| JobId_g |GUID die de runbook-taak identificeert. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>- Nieuw<br>- Gemaakt<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>- Voltooid |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs. |
| OperationName | Het type bewerking dat in Azure wordt uitgevoerd. Voor Automation is de waarde Taak. |
| Resource | De naam van het Automation-account |
| SourceSystem | Het systeem dat Azure Monitor gebruiken om de gegevens te verzamelen. De waarde is altijd Azure voor Diagnostische gegevens van Azure. |
| ResultDescription |De status van het resultaat van de runbook-taak. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De Azure Automation accountresource-id van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resourcegroep voor het Automation-account. |
| ResourceProvider | De resourceprovider. De waarde is MICROSOFT. Automatisering. |
| ResourceType | Het resourcetype. De waarde is AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Taakstromen
| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De aanroeper die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| StreamType_s |Het type taakstroom. Mogelijke waarden zijn:<br>- Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid |
| Tenant_g | GUID die de tenant voor de aanroeper identificeert. |
| JobId_g |GUID die de runbook-taak identificeert. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>- Wordt uitgevoerd |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams. |
| OperationName | Type bewerking uitgevoerd in Azure. Voor Automation is de waarde Taak. |
| Resource | De naam van het Automation-account. |
| SourceSystem | Het systeem dat Azure Monitor gebruiken om de gegevens te verzamelen. De waarde is altijd Azure voor Diagnostische gegevens van Azure. |
| ResultDescription |Beschrijving van de uitvoerstroom van het runbook. |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De Azure Automation accountresource-id van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resourcegroep voor het Automation-account. |
| ResourceProvider | De resourceprovider. De waarde is MICROSOFT. Automatisering. |
| ResourceType | Het resourcetype. De waarde is AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Automation-logboeken weergeven in Azure Monitor logboeken

Nu u uw Automation-taakstromen en -logboeken naar Azure Monitor-logboeken hebt verzonden, gaan we kijken wat u met deze logboeken kunt doen in Azure Monitor logboeken.

Voer de volgende query uit om de logboeken te bekijken: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Een e-mail verzenden wanneer een runbook-taak mislukt of wordt opgeschort

De volgende stappen laten zien hoe u waarschuwingen in een Azure Monitor om u te waarschuwen wanneer er iets misgaat met een runbook-taak.

Als u een waarschuwingsregel wilt maken, begint u met het maken van een logboekzoekactie voor de runbook-taakrecords die de waarschuwing moeten aanroepen. Klik op de **knop Waarschuwing** om de waarschuwingsregel te maken en te configureren.

1. Klik op de pagina Overzicht van de Log Analytics-werkruimte op **Logboeken weergeven.**

2. Maak een zoekquery voor uw waarschuwing door de volgende zoekopdracht in het queryveld te typen: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>U kunt ook groep op de naam van het runbook met behulp van: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Als u logboeken van meer dan één Automation-account of -abonnement in uw werkruimte in stelt, kunt u uw waarschuwingen groepeert op abonnement en Automation-account. De naam van het Automation-account vindt u in `Resource` het veld in de zoekopdracht . `JobLogs`

3. Als u het **scherm Regel maken** wilt openen, klikt u op Nieuwe **waarschuwingsregel** boven aan de pagina. Zie Logboekwaarschuwingen in Azure voor meer informatie over de opties voor het configureren [van de waarschuwing.](../azure-monitor/alerts/alerts-unified-log.md)

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Alle taken zoeken die zijn voltooid met fouten

Naast waarschuwingen over fouten kunt u ook zien wanneer een runbook-taak een niet-beëindigingsfout heeft. In dergelijke gevallen produceert PowerShell een foutstroom, maar de niet-beëindigingsfouten zorgen er niet voor dat uw taak wordt opgeschort of mislukt.

1. Klik in uw Log Analytics-werkruimte op **Logboeken.**

2. Typ in het `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` queryveld.

3. Klik op **de knop** Zoeken.

### <a name="view-job-streams-for-a-job"></a>Taakstromen voor een taak weergeven

Wanneer u een taak debugt, kunt u ook kijken naar de taakstromen. De volgende query toont alle stromen voor één taak met GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Historische taakstatus weergeven

Ten slotte wilt u mogelijk uw taakgeschiedenis in de tijd visualiseren. U kunt deze query gebruiken om te zoeken naar de status van uw taken gedurende een periode.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Historische taakstatusgrafiek van Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Uitvoer van de taakstatus geconverteerd naar een JSON-object

Onlangs hebben we het gedrag gewijzigd van de manier waarop de Automation-logboekgegevens naar de tabel in de Log Analytics-service worden geschreven, waarbij de JSON-eigenschappen niet meer worden onderverdeeld `AzureDiagnostics` in afzonderlijke velden. Als u uw runbook hebt geconfigureerd om objecten in de uitvoerstroom in JSON-indeling op te maken als afzonderlijke kolommen, moet u uw query's opnieuw configureren om dat veld te parseren naar een JSON-object om toegang te krijgen tot deze eigenschappen. Dit wordt bereikt met [parsejson om toegang](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) te krijgen tot een specifiek JSON-element in een bekend pad.

Een runbook formatteert bijvoorbeeld de *eigenschap ResultDescription* in de uitvoerstroom in JSON-indeling met meerdere velden. Als u wilt zoeken naar de status van uw taken met de status Mislukt, zoals opgegeven in een veld met de naam **Status,** gebruikt u deze voorbeeldquery om te zoeken naar *de ResultDescription* met de status **Mislukt:**

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![JSON-indeling van Historische taakstroom in Log Analytics](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Volgende stappen

* Zie Zoekopdrachten in logboeken in Azure Monitor logboeken voor meer informatie over het maken van zoekquery's en het controleren van de Automation-taaklogboeken Azure Monitor [logboeken.](../azure-monitor/logs/log-query-overview.md)
* Zie Runbookuitvoer bewaken voor meer informatie over het maken en ophalen van uitvoer- en foutberichten uit [runbooks.](automation-runbook-output-and-messages.md)
* Zie Runbookuitvoering in Azure Automation voor meer informatie over runbookuitvoering, het bewaken van runbooktaken [en andere technische details.](automation-runbook-execution.md)
* Zie Het overzicht van Azure-Azure Monitor-opslaggegevens verzamelen in Azure Monitor logboeken voor meer informatie over het verzamelen [Azure Monitor logboeken en gegevensverzamelingsbronnen.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
* Zie Problemen oplossen waarom Log Analytics geen gegevens meer verzamelt voor hulp bij het oplossen [van problemen met Log Analytics.](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)
