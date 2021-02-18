---
title: Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken
description: In dit artikel leest u hoe u taak status-en runbook-taak stromen verzendt om logboeken te Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 4199c5576662eee1dd6cedc388440a71e21f8b74
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581216"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken

Azure Automation kunt de status van de runbook-taak en taak stromen verzenden naar uw Log Analytics-werk ruimte. Voor dit proces is het koppelen van werk ruimten niet vereist en is het volledig onafhankelijk. Taak logboeken en taak stromen zijn zichtbaar in de Azure Portal, of met Power shell, voor afzonderlijke taken. Dit biedt u de mogelijkheid om eenvoudig onderzoek uit te voeren. U kunt nu met Azure Monitor-logboeken:

* Krijg inzicht in de status van uw automatiserings taken.
* Een e-mail of waarschuwing activeren op basis van de status van uw runbook-taak (bijvoorbeeld mislukt of onderbroken).
* Schrijf geavanceerde query's over uw werk stromen.
* Verdeel taken tussen Automation-accounts.
* Gebruik aangepaste weer gaven en zoek query's om uw runbook-resultaten, de status van de runbook-taak en andere gerelateerde sleutel indicatoren of metrische gegevens te visualiseren.

## <a name="prerequisites"></a>Vereisten

Als u uw Automation-logboeken wilt gaan verzenden naar Azure Monitor-logboeken, hebt u het volgende nodig:

* De nieuwste versie van [Azure PowerShell](/powershell/azure/).

* Een Log Analytics-werk ruimte en de resource-ID. Zie [aan de slag met Azure monitor-logboeken](../azure-monitor/overview.md)voor meer informatie.

* De resource-ID van uw Azure Automation-account.

## <a name="how-to-find-resource-ids"></a>Resource-Id's zoeken

1. Gebruik de volgende opdracht om de resource-ID te vinden voor uw Azure Automation-account:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Kopieer de waarde voor **ResourceID**.

3. Gebruik de volgende opdracht om de resource-ID te vinden van uw Log Analytics-werk ruimte:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Kopieer de waarde voor **ResourceID**.

Als u de resultaten van een specifieke resource groep wilt retour neren, neemt u de `-ResourceGroupName` para meter op. Zie [Get-AzResource](/powershell/module/az.resources/get-azresource)voor meer informatie.

Als u meer dan één Automation-account of werk ruimte hebt in de uitvoer van de voor gaande opdrachten, kunt u de naam en andere gerelateerde eigenschappen vinden die deel uitmaken van de volledige Resource-ID van uw Automation-account door het volgende uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in de Azure Portal uw Automation-account op de pagina **Automation-accounts** .
1. Selecteer op de pagina van het geselecteerde Automation-account onder **account instellingen** de optie **Eigenschappen**.
1. Op de pagina **Eigenschappen** ziet u de details die hieronder worden weer gegeven.

    ![Eigenschappen van Automation-account](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

Diagnostische instellingen voor automatisering ondersteunt het door sturen van de volgende platform logboeken en metrische gegevens:

* JobLogs
* JobStreams
* DSCNodeStatus
* Metrische gegevens-totaal aantal taken, totale aantal uitgevoerde update-implementatie computers, totale aantal uitgevoerde update-implementaties

Als u wilt beginnen met het verzenden van uw automatiserings logboeken naar Azure Monitor-logboeken, raadpleegt u [Diagnostische instellingen maken](../azure-monitor/essentials/diagnostic-settings.md) om inzicht te krijgen in de functie en methoden die beschikbaar zijn om de diagnostische instellingen te configureren

## <a name="azure-monitor-log-records"></a>Azure Monitor logboek records

Met Azure Automation diagnostische gegevens worden twee typen records gemaakt in Azure Monitor logboeken, gelabeld als `AzureDiagnostics` . De tabellen in de volgende secties zijn voor beelden van records die Azure Automation gegenereerd en de gegevens typen die worden weer gegeven in de zoek resultaten van het logboek.

### <a name="job-logs"></a>Taaklogboeken

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De aanroeper die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| JobId_g |GUID waarmee de runbook-taak wordt geïdentificeerd. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>-Nieuw<br>-Gemaakt<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>-Voltooid |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs. |
| OperationName | Het type bewerking dat in azure wordt uitgevoerd. Voor Automation is de waarde taak. |
| Resource | De naam van het Automation-account |
| SourceSystem | Systeem dat Azure Monitor logboeken gebruikt om de gegevens te verzamelen. De waarde is altijd Azure voor Azure Diagnostics. |
| ResultDescription |De resulterende status van de runbook-taak. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De resource-ID van het Azure Automation-account van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | De resource provider. De waarde is micro soft. Automat. |
| ResourceType | Het resource type. De waarde is AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Taakstromen
| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De aanroeper die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| StreamType_s |Het type taakstroom. Mogelijke waarden zijn:<br>- Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| JobId_g |GUID waarmee de runbook-taak wordt geïdentificeerd. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>-Wordt uitgevoerd |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams. |
| OperationName | Het type bewerking dat in azure wordt uitgevoerd. Voor Automation is de waarde taak. |
| Resource | De naam van het Automation-account. |
| SourceSystem | Systeem dat Azure Monitor logboeken gebruikt om de gegevens te verzamelen. De waarde is altijd Azure voor Azure Diagnostics. |
| ResultDescription |Beschrijving die de uitvoer stroom van het runbook bevat. |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De resource-ID van het Azure Automation-account van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | De resource provider. De waarde is micro soft. Automat. |
| ResourceType | Het resource type. De waarde is AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Automatiserings logboeken weer geven in Azure Monitor-logboeken

Nu u bent begonnen met het verzenden van uw Automation-taak stromen en-logboeken naar Azure Monitor-logboeken, ziet u wat u met deze logboeken kunt doen in Azure Monitor-Logboeken.

Voer de volgende query uit om de logboeken weer te geven: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Een e-mail verzenden wanneer een runbook-taak is mislukt of onderbroken

De volgende stappen laten zien hoe u waarschuwingen instelt in Azure Monitor om u te waarschuwen wanneer er iets mis gaat met een runbook-taak.

Als u een waarschuwings regel wilt maken, moet u beginnen met het maken van een zoek opdracht in het logboek voor de runbook-taak records die de waarschuwing moeten aanroepen. Klik op de knop **waarschuwing** om de waarschuwings regel te maken en te configureren.

1. Klik op de pagina overzicht van Log Analytics werk ruimte op **Logboeken weer geven**.

2. Maak een zoek opdracht in het logboek voor uw waarschuwing door de volgende zoek opdracht in het query veld in te voeren: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>U kunt ook groeperen op de naam van het runbook met behulp van: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Als u logboeken van meer dan één Automation-account of-abonnement op uw werk ruimte instelt, kunt u uw waarschuwingen groeperen op abonnement en Automation-account. De naam van het Automation-account vindt u in het `Resource` veld in de zoek actie van `JobLogs` .

3. Als u het scherm **regel maken** wilt openen, klikt u op **nieuwe waarschuwings regel** boven aan de pagina. Zie [logboek waarschuwingen in azure](../azure-monitor/alerts/alerts-unified-log.md)voor meer informatie over de opties voor het configureren van de waarschuwing.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Alle taken zoeken die zijn voltooid met fouten

Naast het melden van fouten, kunt u nagaan wanneer een runbook-taak een niet-afsluit fout heeft. In dergelijke gevallen produceert Power shell een fout stroom, maar de niet-afsluit fouten zorgen ervoor dat uw taak niet wordt onderbroken of mislukt.

1. Klik in de werk ruimte Log Analytics op **Logboeken**.

2. Typ in het veld query `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Klik op de knop **zoeken** .

### <a name="view-job-streams-for-a-job"></a>Taak stromen voor een taak weer geven

Wanneer u fouten opspoort in een taak, wilt u mogelijk ook de taak stromen bekijken. Met de volgende query worden alle streams voor één taak met GUID weer gegeven `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Historische taak status weer geven

Ten slotte kunt u uw taak geschiedenis gedurende een periode visualiseren. U kunt deze query gebruiken om de status van uw taken in de loop van de tijd te zoeken.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Status diagram van Log Analytics historische taak](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Taak status uitvoer die is geconverteerd naar een JSON-object filteren

Onlangs hebben we het gedrag gewijzigd van de manier waarop de gegevens van het Automation-logboek worden geschreven naar de `AzureDiagnostics` tabel in de log Analytics-service, waar de JSON-eigenschappen niet langer in afzonderlijke velden worden opgesplitst. Als u uw runbook hebt geconfigureerd om objecten in de uitvoer stroom in JSON-indeling als afzonderlijke kolommen in te delen, moet u uw query's opnieuw configureren om dat veld te parseren naar een JSON-object om die eigenschappen te openen. Dit wordt gerealiseerd met behulp van [parsejson](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) om toegang te krijgen tot een specifiek JSON-element in een bekend pad.

Zo wordt met een runbook de eigenschap *ResultDescription* in de uitvoer stroom in JSON-indeling met meerdere velden opgemaakt. Als u wilt zoeken naar de status van uw taken met een mislukte status die is opgegeven in een veld met de naam **status**, gebruikt u deze voorbeeld query om te zoeken in de *ResultDescription* met de status **mislukt**:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![JSON-indeling van Log Analytics historische taak stroom](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../azure-monitor/logs/log-query-overview.md)voor meer informatie over het maken van zoek query's en het controleren van de Automation-taak logboeken met Azure monitor-Logboeken.
* Zie [runbook-uitvoer bewaken](automation-runbook-output-and-messages.md)voor meer informatie over het maken en ophalen van uitvoer-en fout berichten van runbooks.
* Voor meer informatie over het uitvoeren van een runbook, het controleren van runbook-taken en andere technische details raadpleegt u [runbook execution in azure Automation](automation-runbook-execution.md).
* Zie [Azure Storage-gegevens verzamelen in azure monitor logs Overview](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)voor meer informatie over Azure monitor logboeken en bronnen voor gegevens verzameling.
* Zie voor hulp bij het oplossen van problemen Log Analytics [problemen oplossen waarom log Analytics niet langer gegevens verzamelt](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).