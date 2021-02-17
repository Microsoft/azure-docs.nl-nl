---
title: Problemen met logboek waarschuwingen in Azure Monitor oplossen | Microsoft Docs
description: Veelvoorkomende problemen, fouten en oplossingen voor logboek waarschuwings regels in Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/22/2020
ms.openlocfilehash: 06deb9a736998cf32a33d7c4ad08340cf4846b7c
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547788"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Problemen met logboek waarschuwingen in Azure Monitor oplossen  

In dit artikel wordt beschreven hoe u veelvoorkomende problemen met logboek waarschuwingen in Azure Monitor kunt oplossen. Het bevat ook oplossingen voor veelvoorkomende problemen met de functionaliteit en configuratie van logboek waarschuwingen.

Met logboek waarschuwingen kunnen gebruikers een [log Analytics](../log-query/log-analytics-tutorial.md) query gebruiken om resources te evalueren elke ingestelde frequentie, en wordt een waarschuwing gestart op basis van de resultaten. Regels kunnen een of meer acties activeren met behulp van [actie groepen](./action-groups.md). [Meer informatie over de functionaliteit en termen van logboek waarschuwingen](alerts-unified-log.md).

> [!NOTE]
> In dit artikel wordt niet in overweging genomen gevallen waarin de Azure Portal een waarschuwings regel weergeeft en er geen melding wordt uitgevoerd door een gekoppelde actie groep. Zie voor dergelijke gevallen de details over [het oplossen van](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)problemen.

## <a name="log-alert-didnt-fire"></a>De logboek waarschuwing is niet geactiveerd

### <a name="data-ingestion-time-for-logs"></a>Gegevens opname tijd voor logboeken

Azure Monitor verwerkt terabytes aan logboeken van klanten over de hele wereld, wat kan leiden tot [opname latentie van Logboeken](./data-ingestion-time.md).

Logboeken zijn semi-gestructureerde gegevens en meer laten verlaten, meer dan metrieken. Als u meer dan 4 minuten vertraging in gebrande waarschuwingen ondervindt, kunt u overwegen [metrische waarschuwingen](alerts-metric-overview.md)te gebruiken. U kunt gegevens verzenden naar het metrische archief vanuit Logboeken met [metrische waarschuwingen voor logboeken](alerts-metric-logs.md).

Het systeem probeert de evaluatie van waarschuwingen meerdere keren opnieuw uit te stellen om latentie te beperken. Zodra de gegevens binnenkomen, wordt de waarschuwing geactiveerd, die in de meeste gevallen niet gelijk is aan de logboek record tijd.

### <a name="incorrect-query-time-range-configured"></a>Onjuist bereik voor query tijd geconfigureerd

Het tijds bereik voor de query is ingesteld in de definitie van de regel voorwaarde. Dit veld wordt een **periode** genoemd voor werk ruimten en Application Insights, en het **bereik voor opheffen query tijden** voor alle andere resource typen. Net als bij log Analytics beperkt het tijds bereik de query gegevens tot de opgegeven periode. Zelfs als de opdracht **geleden** in de query wordt gebruikt, is het tijds bereik van toepassing. 

Een query scant bijvoorbeeld 60 minuten, wanneer het tijds bereik 60 minuten is, zelfs als de tekst **geleden (1d)** bevat. Het tijds bereik en de filter tijd voor query's moeten overeenkomen. In het voorbeeld geval wordt de **periode** voor het overschrijven van de  /  **query tijd** op één dag gewijzigd. dit werkt zoals verwacht.

![Periode](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>Acties zijn gedempt in de waarschuwings regel

Logboek waarschuwingen bieden een optie om het aantal geactiveerde waarschuwings acties voor een bepaalde periode te dempen. Dit veld heet **waarschuwingen onderdrukken** in werk ruimten en Application Insights. In alle andere resource typen wordt **demping-acties** genoemd. 

Een veelvoorkomend probleem is dat u denkt dat de-waarschuwing de acties niet heeft gestart vanwege een service probleem. Zelfs moeilijk is de regel configuratie gedempt.

![Waarschuwingen onderdrukken](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Waarschuwings regel voor metrische meting met splitsen met de verouderde Log Analytics-API

[Metrische maat eenheid](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) is een type logboek waarschuwing dat is gebaseerd op een samenvatte tijd reeks resultaten. Met deze regels kan groeperen op kolommen worden toegestaan om [waarschuwingen te splitsen](alerts-unified-log.md#split-by-alert-dimensions). Als u de verouderde Log Analytics-API gebruikt, werkt splitsen niet zoals verwacht. Het kiezen van de groepering in de verouderde API wordt niet ondersteund.

Met de huidige ScheduledQueryRules-API kunt u **aggregatie instellen op** [metrische meet](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) regels, wat naar verwachting werkt. [Meer informatie over overschakelen naar de huidige ScheduledQueryRules-API](alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>De logboek waarschuwing is onnodig geactiveerd

Een geconfigureerde [logboek waarschuwings regel in azure monitor](./alerts-log.md) kan onverwacht worden geactiveerd. In de volgende secties worden enkele veelvoorkomende redenen beschreven.

### <a name="alert-triggered-by-partial-data"></a>Waarschuwing geactiveerd door gedeeltelijke gegevens

Azure Monitor verwerkt terabytes aan logboeken van klanten over de hele wereld, wat kan leiden tot [opname latentie van Logboeken](./data-ingestion-time.md).

Logboeken zijn semi-gestructureerde gegevens en meer laten verlaten, meer dan metrieken. Als u veel problemen ondervindt bij het starten van waarschuwingen, kunt u overwegen [metrische waarschuwingen](alerts-metric-overview.md)te gebruiken. U kunt gegevens verzenden naar het metrische archief vanuit Logboeken met [metrische waarschuwingen voor logboeken](alerts-metric-logs.md).

Logboek waarschuwingen werken het beste wanneer u gegevens in de logboeken wilt detecteren. Het werkt minder goed wanneer u een gebrek aan gegevens in de logboeken wilt detecteren. Bijvoorbeeld een waarschuwing over de heartbeat van de virtuele machine. 

Hoewel er ingebouwde mogelijkheden zijn om te voor komen dat er valse waarschuwingen optreden, kunnen ze nog steeds worden uitgevoerd op zeer vertoonde gegevens (meer dan ongeveer 30 minuten) en gegevens met latentie pieken.

### <a name="query-optimization-issues"></a>Problemen met query optimalisatie

De waarschuwings service wijzigt uw query zodat deze kan worden geoptimaliseerd voor een lagere belasting en een latentie van een waarschuwing. De waarschuwings stroom is gebouwd om de resultaten te transformeren die het probleem aan een waarschuwing wijzen. In het geval van een query bijvoorbeeld:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Als het doel van de gebruiker is om te waarschuwen wanneer dit gebeurtenis type plaatsvindt, wordt de waarschuwings logica toegevoegd `count` aan de query. De query die wordt uitgevoerd, is:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

U hoeft geen waarschuwings logica aan de query toe te voegen en dat kan zelfs problemen veroorzaken. Als u in het bovenstaande voor beeld uw query opneemt `count` , resulteert dit altijd in de waarde 1, omdat de waarschuwings service van wordt `count` uitgevoerd `count` .

De geoptimaliseerde query is wat de logboek waarschuwing service uitvoert. U kunt de gewijzigde query uitvoeren in Log Analytics [Portal](../log-query/log-query-overview.md) of [API](/rest/api/loganalytics/).

Voor werk ruimten en Application Insights wordt de query genoemd, die wordt **uitgevoerd** in het deel venster voor waarde. Selecteer in alle andere resource typen de optie **laatste waarschuwings query weer geven** op het tabblad voor waarde.

![De query die moet worden uitgevoerd](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>De logboek waarschuwing is uitgeschakeld

De volgende secties bevatten een aantal redenen waarom Azure Monitor een waarschuwings regel voor het logboek kan uitschakelen. We hebben ook een [voor beeld opgenomen van het activiteiten logboek dat wordt verzonden wanneer een regel wordt uitgeschakeld](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>Het waarschuwings bereik bestaat niet meer of is verplaatst

Wanneer de bereik resources van een waarschuwings regel niet meer geldig zijn, mislukt de uitvoering van de regel. In dit geval wordt de facturering ook gestopt.

Azure Monitor schakelt de logboek waarschuwing na een week uit als deze continu is mislukt.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>De query die wordt gebruikt in een logboek waarschuwing is ongeldig

Als er een waarschuwings regel voor het logboek wordt gemaakt, wordt de query gevalideerd voor de juiste syntaxis. Maar soms kan de query die in de waarschuwings regel voor logboeken wordt gegeven, mislukken. Enkele veelvoorkomende oorzaken zijn:

- Er zijn regels gemaakt via de API en de validatie is overgeslagen door de gebruiker.
- De query [wordt uitgevoerd op meerdere resources](../log-query/cross-workspace-query.md) en een of meer resources zijn verwijderd of verplaatst.
- De [query is mislukt](https://dev.loganalytics.io/documentation/Using-the-API/Errors) omdat:
    - De oplossing voor logboek registratie is niet [geïmplementeerd in de werk ruimte](../insights/solutions.md#install-a-monitoring-solution), waardoor er geen tabellen worden gemaakt.
    - Gegevens die niet meer dan 30 dagen naar een tabel in de query zijn gestroomd, worden gestopt.
    - [Aangepaste logboeken tabellen](data-sources-custom-logs.md) zijn nog niet gemaakt, omdat de gegevens stroom niet is gestart.
- Wijzigingen in de [query taal](/azure/kusto/query/) bevatten een gewijzigde indeling voor opdrachten en functies. De query die u eerder hebt gegeven, is dus niet meer geldig.

[Azure Advisor](../../advisor/advisor-overview.md) wordt u gewaarschuwd over dit gedrag. Er wordt een aanbeveling toegevoegd over de waarschuwings regel voor het logboek. De categorie die wordt gebruikt, is ' hoge Beschik baarheid ' met gemiddelde impact en een beschrijving van de regel voor logboek waarschuwing herstellen om bewaking te garanderen.

## <a name="alert-rule-quota-was-reached"></a>Het quotum voor waarschuwings regels is bereikt

Het aantal waarschuwingsregels voor zoeken in logboeken per abonnement en resource is afhankelijk van de quotumlimieten die [hier worden beschreven](../service-limits.md).

### <a name="recommended-steps"></a>Aanbevolen stappen
    
Als u de quotum limiet hebt bereikt, kunnen de volgende stappen u helpen om het probleem op te lossen.

1. Probeer de waarschuwings regels voor logboek zoeken te verwijderen of uit te scha kelen die niet meer worden gebruikt.
1. Probeer het [splitsen van waarschuwingen per dimensie](alerts-unified-log.md#split-by-alert-dimensions) te gebruiken om het aantal regels te verminderen. Deze regels kunnen veel resources en detectie cases bewaken.
1. Als de quotum limiet moet worden verhoogd, gaat u door met het openen van een ondersteunings aanvraag en geeft u de volgende informatie op:

    - Abonnements-Id's en resource-Id's waarvoor de quotum limiet moet worden verhoogd.
    - Reden voor toename van quotum.
    - Resource type voor de quota verhoging: **log Analytics**, **Application Insights**, enzovoort.
    - Aangevraagde quotum limiet.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Het huidige gebruik van nieuwe waarschuwings regels voor logboeken controleren
    
#### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

1. Open het scherm *waarschuwingen* en selecteer *waarschuwings regels beheren*
2. Filteren op het relevante abonnement met behulp van de vervolgkeuzelijst *Abonnement*
3. Zorg ervoor dat u niet filtert op een specifieke resource groep, resource type of resource
4. Selecteer 'Zoeken in logboeken' in de vervolgkeuzelijst voor besturingselementen *Signaaltype*
5. Controleer of de vervolgkeuzelijst voor besturingselementen *Status* is ingesteld op ‘ingeschakeld’
6. Het totale aantal waarschuwingsregels voor zoeken in logboeken wordt weergegeven boven de lijst met regels

#### <a name="from-api"></a>Van API

- Power shell- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [Lijst per abonnement](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Voor beeld van een activiteiten logboek wanneer de regel is uitgeschakeld

Als de query gedurende zeven dagen doorlopend mislukt, wordt de waarschuwing in het logboek door Azure Monitor uitgeschakeld en wordt de regel niet meer in rekening gebracht. U kunt de exacte tijd ontdekken wanneer Azure Monitor de logboek waarschuwing hebt uitgeschakeld in het [Azure-activiteiten logboek](../../azure-resource-manager/management/view-activity-logs.md). Zie dit voor beeld:

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboek waarschuwingen in azure](./alerts-unified-log.md).
- Meer informatie over het [configureren van logboek waarschuwingen](../log-query/log-query-overview.md).
- Meer informatie over [logboek query's](../log-query/log-query-overview.md).