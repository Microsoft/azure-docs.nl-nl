---
title: Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor | Microsoft Docs
description: Azure Monitor gebruiken om waarschuwings regels voor logboeken te maken, weer te geven en te beheren
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8759a539005a38892498c9450d31f97884547db7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033704"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor

## <a name="overview"></a>Overzicht

Met logboek waarschuwingen kunnen gebruikers een [log Analytics](../logs/log-analytics-tutorial.md) query gebruiken om resources te evalueren elke ingestelde frequentie, en wordt een waarschuwing gestart op basis van de resultaten. Regels kunnen een of meer acties activeren met behulp van [actie groepen](./action-groups.md). [Meer informatie over de functionaliteit en termen van logboek waarschuwingen](./alerts-unified-log.md).

In dit artikel wordt beschreven hoe u logboek waarschuwingen maakt en beheert met behulp van Azure Monitor. Waarschuwings regels worden gedefinieerd door drie onderdelen:
- Doel: een specifieke Azure-resource om te bewaken.
- Criteria: logica om te evalueren. Als aan wordt voldaan, wordt de waarschuwing geactiveerd.  
- Actie: meldingen of automatisering: e-mail, SMS, webhook, enzovoort.

U kunt ook waarschuwings regels voor logboeken maken met behulp van Azure Resource Manager sjablonen, die in [een afzonderlijk artikel](../alerts/alerts-log-create-templates.md)worden beschreven.

> [!NOTE]
> Logboek gegevens van een [log Analytics-werk ruimte](../logs/log-analytics-tutorial.md) kunnen worden verzonden naar de Azure monitor metrische gegevens opslag. Waarschuwingen voor metrische gegevens hebben een [ander gedrag](./alerts-metric-overview.md), wat wenselijker is, afhankelijk van de data waarmee u werkt. Zie [metrische waarschuwingen voor logboeken](./alerts-metric-logs.md)voor meer informatie over hoe en hoe u Logboeken kunt routeren naar metrische gegevens.

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Een waarschuwings regel voor het logboek maken met de Azure Portal

Hier volgen de stappen om aan de slag te gaan met het schrijven van query's voor waarschuwingen:

1. Ga naar de resource waarvoor u een waarschuwing wilt ontvangen. Overweeg waarschuwings regels in te stellen voor meerdere resources door waar mogelijk een abonnement of een resource groeps bereik te selecteren. Als u een waarschuwing voor meerdere resources maakt, vermindert u de kosten en hoeft u meerdere waarschuwings regels te beheren.
1. Onder **monitor** selecteert u **Logboeken**.
1. Voer een query uit op de logboekgegevens die het probleem kunnen aanduiden. U kunt het [onderwerp voor beelden van waarschuwings query's](../logs/example-queries.md) gebruiken om te begrijpen wat u kunt detecteren of [aan de slag gaat met het schrijven van uw eigen query](../logs/log-analytics-tutorial.md). U kunt ook [leren hoe u geoptimaliseerde waarschuwingsquery's maakt](alerts-log-query.md).
1. Druk op de knop + nieuwe waarschuwings regel om de stroom voor het maken van waarschuwingen te starten.

    ![Log Analytics-ingestelde waarschuwing](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Het is raadzaam om waarschuwingen op schaal te maken wanneer u de resource toegangs modus voor logboeken gebruikt, die wordt uitgevoerd op meerdere resources met een resource groep of een abonnements bereik. Bij een waarschuwing op schaal worden de overhead van regel beheer gereduceerd. Als u de resources wilt kunnen bereiken, neemt u de kolom Resource-ID op in de resultaten. Meer [informatie over het splitsen van waarschuwingen per dimensie](./alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Logboek waarschuwing voor Log Analytics en Application Insights

1. Als de query syntaxis juist is, worden historische gegevens voor de query weer gegeven als een grafiek met de optie om de grafiek periode te verfijnen van de laatste zes uur naar de vorige week.
 
    Als uw query resultaten samenvattings gegevens [bevatten of specifieke](/azure/kusto/query/projectoperator) kolommen zonder tijd kolom bevat, wordt er een enkele waarde weer gegeven in de grafiek.

    ![Waarschuwings regel configureren](media/alerts-log/AlertsPreviewAlertLog.png)

1. Kies het tijds bereik waarover de opgegeven voor waarde moet worden geëvalueerd, met behulp van de [**periode**](./alerts-unified-log.md#query-time-range) optie.

1. Logboek waarschuwingen kunnen worden gebaseerd op twee soorten [**metingen**](./alerts-unified-log.md#measure):
    1. **Aantal resultaten** (aantal records dat door de query wordt geretourneerd).
    1. **Metrische meet waarde**  -  *Cumulatieve waarde* berekend met behulp van samenvatten gegroepeerd op gekozen expressies en [bin ()](/azure/kusto/query/binfunction) selecteren. Bijvoorbeeld:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. Voor metrische meet waarschuwing logica kunt u optioneel opgeven hoe u [de waarschuwingen wilt splitsen](./alerts-unified-log.md#split-by-alert-dimensions) op basis van de optie **aggregatie op** . De expressie voor het groeperen van rijen moet uniek zijn en worden gesorteerd.

    > [!NOTE]
    > Als [bin ()](/azure/kusto/query/binfunction) kan leiden tot een oneven tijds intervallen, wordt de functie [bin ()](/azure/kusto/query/binfunction) automatisch geconverteerd naar de functie [bin_at ()](/azure/kusto/query/binatfunction) met de juiste tijd tijdens runtime, om ervoor te zorgen dat er resultaten zijn met een vast punt.

    > [!NOTE]
    > De dimensies splitsen op basis van waarschuwingen zijn alleen beschikbaar voor de huidige scheduledQueryRules-API. Als u de verouderde [log Analytics alert-API](./api-alerts.md)gebruikt, moet u overschakelen. Meer [informatie over overschakelen](./alerts-log-api-switch.md). Resource gerichte waarschuwingen op schaal worden alleen ondersteund in de API-versie `2020-05-01-preview` en hoger.

    ![aggregatie op optie](media/alerts-log/aggregate-on.png)

1. Vervolgens stelt u de [ **operator**, de **drempel waarde**](./alerts-unified-log.md#threshold-and-operator)en de [**frequentie**](./alerts-unified-log.md#frequency)in op basis van de preview-gegevens.

1. U kunt eventueel ook het [aantal schendingen instellen om een waarschuwing te activeren](./alerts-unified-log.md#number-of-violations-to-trigger-alert) door gebruik te maken van een **totaal of een opeenvolgende schending**.

1. Selecteer **Gereed**. 

1. Definieer de **naam van de waarschuwings regel**, **Beschrijving** en selecteer de **Ernst** van de waarschuwing. Deze details worden in alle waarschuwings acties gebruikt. Daarnaast kunt u ervoor kiezen om de waarschuwings regel niet te activeren bij het maken door **regel inschakelen bij het maken** te selecteren.

1. Kies of u regel acties wilt onderdrukken voor een tijd nadat een waarschuwing is geactiveerd, gebruikt u de optie [**waarschuwingen onderdrukken**](./alerts-unified-log.md#state-and-resolving-alerts) . De regel wordt nog steeds uitgevoerd en er worden geen waarschuwingen gemaakt, maar er worden geen acties geactiveerd om ruis te voor komen. De waarde voor dempen moet groter zijn dan de frequentie van de waarschuwing om effectief te zijn.

    ![Waarschuwingen onderdrukken voor waarschuwingen in Logboeken](media/alerts-log/AlertsPreviewSuppress.png)

1. Geef op of met de waarschuwings regel een of meer [**actie groepen**](./action-groups.md#webhook) moeten worden geactiveerd wanneer aan de waarschuwings voorwaarde wordt voldaan.

    > [!NOTE]
    > Raadpleeg de [service limieten voor Azure-abonnementen](../../azure-resource-manager/management/azure-subscription-service-limits.md) voor limieten voor de acties die kunnen worden uitgevoerd.  

1. U kunt eventueel acties aanpassen in waarschuwings regels voor logboeken:

    - **Aangepast e-mail onderwerp**: onderdrukt het *e-mail onderwerp* van e-mail acties. U kunt de hoofd tekst van de e-mail niet wijzigen en dit veld **is niet bedoeld voor e-mail adressen**.
    - **Aangepaste JSON-nettolading toevoegen**: overschrijft de JSON van de webhook die wordt gebruikt door actie groepen, ervan uitgaande dat de actie groep een webhook-actie bevat. Meer informatie over de [actie webhook voor logboek waarschuwingen](./alerts-log-webhook.md).

    ![Actie onderdrukkingen voor waarschuwingen in Logboeken](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Als alle velden correct zijn ingesteld, kunt u op de knop **waarschuwings regel maken** klikken en een waarschuwing maken.

    Binnen een paar minuten is de waarschuwing actief en worden triggers zoals eerder beschreven.

    ![Regel maken](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>Een logboek waarschuwing maken voor Log Analytics en Application Insights vanuit het beheer van waarschuwingen

> [!NOTE]
> Het maken van het beheer van waarschuwingen wordt momenteel niet ondersteund voor resource-georiënteerde logboeken

1. Selecteer in de [Portal](https://portal.azure.com/) **monitor** en kies vervolgens **waarschuwingen**.

    ![Bewaking](media/alerts-log/AlertsPreviewMenu.png)

1. Selecteer **nieuwe waarschuwings regel**. 

    ![Waarschuwing toevoegen](media/alerts-log/AlertsPreviewOption.png)

1. Het deel venster **waarschuwing maken** wordt weer gegeven. Er zijn vier onderdelen: 
    - De resource waarop de waarschuwing van toepassing is.
    - De voor waarde die moet worden gecontroleerd.
    - De acties die moeten worden uitgevoerd als de voor waarde waar is.
    - De Details voor de naam en beschrijving van de waarschuwing. 

    ![Regel maken](media/alerts-log/AlertsPreviewAdd.png)

1. Druk op de knop **resource selecteren** . Filter door het *abonnement*, het *resource type* te kiezen en een resource te selecteren. Controleer of er logboeken beschikbaar zijn voor de resource.

   ![Resource selecteren](media/alerts-log/Alert-SelectResourceLog.png)

1. Gebruik vervolgens de knop **voor waarde** toevoegen om een lijst weer te geven met signaal opties die beschikbaar zijn voor de resource. Selecteer de optie **aangepaste zoek opdracht voor logboeken** .

   ![Selecteer een resource-aangepast zoeken naar Logboeken](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > De portal waarschuwingen bevat een lijst met opgeslagen query's van Log Analytics en Application Insights en ze kunnen worden gebruikt als sjabloon waarschuwings query's.

1. Nadat u de waarschuwing hebt geselecteerd, schrijft, plakt of bewerkt u de query in het veld **Zoek query** .

1. Ga door naar de volgende stappen die in de [laatste sectie](#log-alert-for-log-analytics-and-application-insights)worden beschreven.

### <a name="log-alert-for-all-other-resource-types"></a>Logboek waarschuwing voor alle andere resource typen

> [!NOTE]
> Er zijn momenteel geen extra kosten verbonden aan de API-versie en de waarschuwingen over de `2020-05-01-preview` resource-georiënteerde Logboeken.  Prijzen voor functies die in de preview-versie zijn opgenomen, worden in de toekomst aangekondigd en er is een kennisgeving gegeven vóór het begin van de facturering. Als u ervoor kiest om door te gaan met het gebruik van de nieuwe API-versie en de waarschuwingen van resource gerichte logboeken na de kennisgevings periode, wordt u gefactureerd tegen het toepasselijke rente bedrag.

1. Start op het tabblad **voor waarde** :

    1. Controleer of de [**metingen**](./alerts-unified-log.md#measure), het [**aggregatie type**](./alerts-unified-log.md#aggregation-type)en de [**aggregatie granulatie**](./alerts-unified-log.md#aggregation-granularity) juist zijn. 
        1. Standaard telt de regel het aantal resultaten in de afgelopen vijf minuten.
        1. Als we een overzicht van de query resultaten detecteren, wordt de regel binnen een paar seconden automatisch bijgewerkt om dit vast te leggen.

    1. Kies zo nodig [waarschuwingen voor het splitsen per dimensie](./alerts-unified-log.md#split-by-alert-dimensions): 
       - De **kolom Resource-id** wordt automatisch geselecteerd, indien gedetecteerd, en wijzigt de context van de geactiveerde waarschuwing in de resource van de record. 
       - De **resource-id-kolom** kan worden uitgeschakeld voor het activeren van waarschuwingen voor abonnementen of resource groepen. Het selecteren van de selectie is handig wanneer query resultaten zijn gebaseerd op cross-resources. Een query die controleert of 80% van de virtuele machines van de resource groep een hoog CPU-gebruik ondervindt, is bijvoorbeeld.
       - Er kunnen Maxi maal zes meer splitsingen worden geselecteerd voor elk type getallen of tekst kolommen met behulp van de tabel dimensies.
       - Waarschuwingen worden afzonderlijk gestart op basis van het splitsen van unieke combi Naties en payload van waarschuwingen bevat deze informatie.
    
        ![Aggregatie parameters selecteren en splitsen](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. In het **voorbeeld** diagram ziet u de resultaten van query-evaluaties in de loop van de tijd. U kunt de grafiek periode wijzigen of een andere tijd reeks selecteren die het resultaat is van het opsplitsen van unieke waarschuwingen per dimensie.

        ![Voorbeeld grafiek](media/alerts-log/preview-chart.png)

    1. Stel vervolgens op basis van de voorbeeld gegevens de logica van de **waarschuwing** in. [ **Operator**, **drempel waarde**](./alerts-unified-log.md#threshold-and-operator)en [**frequentie**](./alerts-unified-log.md#frequency).

        ![Voorbeeld grafiek met drempel en waarschuwings logica](media/alerts-log/chart-and-alert-logic.png)

    1. U kunt eventueel [**aantal schendingen instellen om de waarschuwing te activeren**](./alerts-unified-log.md#number-of-violations-to-trigger-alert) in het gedeelte **Geavanceerde opties** .
    
        ![Geavanceerde opties](media/alerts-log/advanced-options.png)

1. Selecteer of maak de vereiste [actie groepen](./action-groups.md)op het tabblad **acties** .

    ![Tabblad acties](media/alerts-log/actions-tab.png)

1. Definieer de details van de **waarschuwings regel** en **Project Details** op het tabblad **Details** . U kunt desgewenst instellen of u nu niet wilt starten of [**acties dempen**](./alerts-unified-log.md#state-and-resolving-alerts) voor een periode nadat de waarschuwings regel **wordt** geactiveerd.

    > [!NOTE]
    > Waarschuwings regels voor logboeken zijn momenteel stateless en elke keer dat er een waarschuwing wordt gemaakt een actie wordt geactiveerd, tenzij dempen is gedefinieerd.

    ![Het tabblad Details](media/alerts-log/details-tab.png)

1. Stel op het tabblad **labels** de vereiste labels in de resource van de waarschuwings regel in.

    ![Tabblad Labels](media/alerts-log/tags-tab.png)

1. Op het tabblad **controleren en maken** wordt een validatie uitgevoerd en wordt er op de hoogte gebracht van problemen. De regel definitie controleren en goed keuren.
1. Als alle velden juist zijn, selecteert u de knop **maken** en voltooit u de waarschuwings regel maken. Alle waarschuwingen kunnen worden weer gegeven in het beheer van waarschuwingen.
 
    ![Tabblad controleren en maken](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Waarschuwingen voor logboeken in Azure Portal weer geven & beheren

1. Selecteer in de [Portal](https://portal.azure.com/)de relevante resource of de **controle** service. Selecteer vervolgens **waarschuwingen** in de sectie monitor.

1. In het waarschuwingen beheer worden alle geactiveerde waarschuwingen weer gegeven. Meer [informatie over het beheer van waarschuwingen](alerts-managing-alert-instances.md).

    > [!NOTE]
    > Waarschuwings regels voor logboeken zijn momenteel [stateless en kunnen niet worden opgelost](./alerts-unified-log.md#state-and-resolving-alerts).

1. Selecteer de knop **waarschuwings regels beheren** op de bovenste balk om de regels te bewerken:

    ![ waarschuwings regels beheren](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>Logboek waarschuwingen beheren met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Power shell wordt momenteel niet ondersteund in de API-versie `2020-05-01-preview`

De onderstaande Power shell-cmdlets zijn beschikbaar voor het beheren van regels met de [geplande query regels-API](/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : Power shell-cmdlet voor het maken van een nieuwe waarschuwings regel voor het logboek.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : Power shell-cmdlet om een bestaande waarschuwings regel voor het logboek bij te werken.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Power shell-cmdlet voor het maken of bijwerken van een object dat de bron parameters voor een logboek waarschuwing opgeeft. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) en [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): Power shell-cmdlet voor het maken of bijwerken van een object dat de plannings parameters voor een logboek waarschuwing opgeeft. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) en [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : Power shell-cmdlet voor het maken of bijwerken van een object waarmee actie parameters voor een logboek waarschuwing worden opgegeven. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) en [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Power shell-cmdlet voor het maken of bijwerken van een object waarmee actie groepen para meters voor een logboek waarschuwing worden opgegeven. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Power shell-cmdlet voor het maken of bijwerken van een object waarmee trigger voorwaarde parameters voor de logboek waarschuwing worden opgegeven. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Power shell-cmdlet voor het maken of bijwerken van een object waarvoor metrische trigger voorwaarde parameters worden opgegeven voor de waarschuwing voor het [meting type van metrische](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)gegevens. Wordt gebruikt als invoer door de cmdlet [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : Power shell-cmdlet voor het weer geven van de bestaande logboek waarschuwings regels of een specifieke waarschuwings regel voor het logboek
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : Power shell-cmdlet voor het in-of uitschakelen van de waarschuwings regel voor logboek registratie
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): Power shell-cmdlet voor het verwijderen van een bestaande waarschuwings regel voor het logboek

> [!NOTE]
> ScheduledQueryRules Power shell-cmdlets kunnen alleen regels beheren die zijn gemaakt in de huidige [geplande query regels-API](/rest/api/monitor/scheduledqueryrules/). Logboek waarschuwings regels die zijn gemaakt met verouderde [log Analytics alert API](./api-alerts.md) kunnen alleen worden beheerd met Power shell nadat is [overgeschakeld naar de API voor geplande query regels](../alerts/alerts-log-api-switch.md).

Hier volgen enkele voor beelden van stappen voor het maken van een waarschuwings regel voor een logboek met behulp van Power shell:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

Hier volgen enkele voor beelden van stappen voor het maken van een waarschuwings regel voor een logboek met behulp van de Power shell met query's voor meerdere resources:

```powershell
$authorized = @ ("/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicewsCrossExample", "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceAppInsights")

$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews" -AuthorizedResource $authorized

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition
New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name" 
```

U kunt ook de logboek waarschuwing maken met behulp van een [sjabloon en parameter](./alerts-log-create-templates.md) bestanden met Power shell:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>Logboek waarschuwingen beheren met CLI

> [!NOTE]
> Azure CLI-ondersteuning is alleen beschikbaar voor de scheduledQueryRules-API-versie `2020-05-01-preview` en hoger. De eerdere-API-versie kan de Azure Resource Manager CLI gebruiken met sjablonen zoals hieronder wordt beschreven. Als u de verouderde [log Analytics alert-API](./api-alerts.md)gebruikt, moet u overschakelen naar CLI gebruiken. Meer [informatie over overschakelen](./alerts-log-api-switch.md).

In de vorige secties wordt beschreven hoe u logboek waarschuwings regels kunt maken, weer geven en beheren met behulp van Azure Portal. In deze sectie wordt beschreven hoe u hetzelfde kunt doen met meerdere platformen van [Azure cli](/cli/azure/get-started-with-azure-cli). De snelste manier om te beginnen met het gebruik van Azure CLI is via [Azure Cloud shell](../../cloud-shell/overview.md). Voor dit artikel gebruiken we Cloud Shell.

1. Ga naar Azure Portal en selecteer **Cloud shell**.

1. Bij de prompt kunt u opdrachten gebruiken met ``--help`` de optie om meer te weten te komen over de opdracht en hoe u deze gebruikt. Met de volgende opdracht wordt bijvoorbeeld een lijst weer gegeven met opdrachten die beschikbaar zijn voor het maken, weer geven en beheren van logboek waarschuwingen:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. U kunt een waarschuwings regel voor het logboek maken die het aantal systeem gebeurtenis fouten bewaakt:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. U kunt alle logboek waarschuwingen in een resource groep weer geven met behulp van de volgende opdracht:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. U kunt de details van een bepaalde waarschuwings regel voor het logboek weer geven met behulp van de naam of de resource-ID van de regel:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. U kunt een waarschuwings regel voor logboek registratie uitschakelen met de volgende opdracht:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. U kunt een waarschuwings regel voor logboeken verwijderen met de volgende opdracht:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

U kunt Azure Resource Manager CLI ook gebruiken met [sjabloon](./alerts-log-create-templates.md) bestanden:

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

Wanneer het maken is voltooid, wordt 201 geretourneerd. Als de update is voltooid, wordt 200 geretourneerd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waarschuwingen voor logboeken](./alerts-unified-log.md).
* Maak logboek waarschuwingen met behulp van [Azure Resource Manager sjablonen](./alerts-log-create-templates.md).
* Informatie [over webhook-acties voor logboek waarschuwingen](./alerts-log-webhook.md).
* Meer informatie over [logboek query's](../logs/log-query-overview.md).
