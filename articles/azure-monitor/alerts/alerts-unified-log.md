---
title: Waarschuwingen registreren in Azure Monitor
description: E-mail berichten, meldingen, Url's van websites (webhooks) of Automation aanroepen wanneer de door u opgegeven logboek query voorwaarde wordt vervuld
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 5e7909725f5e390f4e42a7d62e80f90f897c840f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714150"
---
# <a name="log-alerts-in-azure-monitor"></a>Waarschuwingen registreren in Azure Monitor

## <a name="overview"></a>Overzicht

Logboek waarschuwingen zijn een van de waarschuwings typen die worden ondersteund in [Azure-waarschuwingen](./alerts-overview.md). Met logboek waarschuwingen kunnen gebruikers een [log Analytics](../logs/log-analytics-tutorial.md) query gebruiken om resources te evalueren elke ingestelde frequentie, en wordt een waarschuwing gestart op basis van de resultaten. Regels kunnen een of meer acties activeren met behulp van [actie groepen](./action-groups.md).

> [!NOTE]
> Logboek gegevens van een [log Analytics-werk ruimte](../logs/log-analytics-tutorial.md) kunnen worden verzonden naar de Azure monitor metrische gegevens opslag. Waarschuwingen voor metrische gegevens hebben een [ander gedrag](alerts-metric-overview.md), wat wenselijker is, afhankelijk van de data waarmee u werkt. Zie [metrische waarschuwingen voor logboeken](alerts-metric-logs.md)voor meer informatie over hoe en hoe u Logboeken kunt routeren naar metrische gegevens.

> [!NOTE]
> Er zijn momenteel geen extra kosten verbonden aan de API-versie en de waarschuwingen over de `2020-05-01-preview` resource-georiënteerde Logboeken.  Prijzen voor functies die in de preview-versie zijn opgenomen, worden in de toekomst aangekondigd en er is een kennisgeving gegeven vóór het begin van de facturering. Als u ervoor kiest om door te gaan met het gebruik van de nieuwe API-versie en de waarschuwingen van resource gerichte logboeken na de kennisgevings periode, wordt u gefactureerd tegen het toepasselijke rente bedrag.

## <a name="prerequisites"></a>Vereisten

Logboek waarschuwingen voeren query's uit op Log Analytics gegevens. Eerst moet u beginnen met het [verzamelen van logboek gegevens](../essentials/resource-logs.md) en het uitvoeren van een query op de logboek gegevens voor problemen. U kunt het [onderwerp voor beelden van waarschuwings query's](../logs/example-queries.md) in log Analytics gebruiken om te begrijpen wat u kunt detecteren of [aan de slag gaat met het schrijven van uw eigen query](../logs/log-analytics-tutorial.md).

Beheer van [Azure-bewaking](../roles-permissions-security.md) is een algemene rol die nodig is om logboek waarschuwingen te maken, te wijzigen en bij te werken. Er zijn ook toegangs rechten voor het uitvoeren van & voor de bron logboeken nodig. Gedeeltelijke toegang tot bron logboeken kan mislukken van query's of gedeeltelijke resultaten retour neren. Meer [informatie over het configureren van logboek waarschuwingen in azure](./alerts-log.md).

> [!NOTE]
> Logboek waarschuwingen voor Log Analytics die worden beheerd met behulp van de verouderde [log Analytics-waarschuwings-API](./api-alerts.md). [Meer informatie over overschakelen naar de huidige ScheduledQueryRules-API](../alerts/alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Definitie van de query-evaluatie

Definitie van de voor waarden voor logboek zoeken van regels wordt gestart vanaf:

- Welke query moet worden uitgevoerd?
- Hoe kunt u de resultaten gebruiken?

In de volgende secties worden de verschillende para meters beschreven die u kunt gebruiken om de bovenstaande logica in te stellen.

### <a name="log-query"></a>Logboekquery
De [log Analytics](../logs/log-analytics-tutorial.md) query die wordt gebruikt om de regel te evalueren. De resultaten die door deze query worden geretourneerd, worden gebruikt om te bepalen of een waarschuwing moet worden geactiveerd. Het bereik van de query kan worden toegewezen aan:

- Een specifieke resource, zoals een virtuele machine.
- Een at-schaal resource, zoals een abonnement of resource groep.
- Meerdere resources met behulp van een [Cross-resource query](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> Waarschuwings query's hebben beperkingen om te zorgen voor optimale prestaties en de relevantie van de resultaten. [Klik hier voor meer informatie](./alerts-log-query.md).

> [!IMPORTANT]
> Resource gerichte en [query's op meerdere bronnen](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) worden alleen ondersteund met de huidige SCHEDULEDQUERYRULES-API. Als u de verouderde [log Analytics alert-API](./api-alerts.md)gebruikt, moet u overschakelen. [Meer informatie over scha kelen](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Query tijds bereik

Het tijds bereik wordt ingesteld in de definitie van de regel voorwaarde. In werk ruimten en Application Insights, wordt het een **punt** genoemd. In alle andere resource typen wordt het **opheffen van de query tijd bereik** genoemd.

Net als bij log Analytics beperkt het tijds bereik de query gegevens tot het opgegeven bereik. Zelfs als de opdracht **geleden** in de query wordt gebruikt, is het tijds bereik van toepassing.

Een query scant bijvoorbeeld 60 minuten, wanneer het tijds bereik 60 minuten is, zelfs als de tekst **geleden (1d)** bevat. Het tijds bereik en de filter tijd voor query's moeten overeenkomen. In het voorbeeld geval wordt de **periode** voor het overschrijven van de  /  **query tijd** op één dag gewijzigd. dit werkt zoals verwacht.

### <a name="measure"></a>Meting

Logboek waarschuwingen maken het mogelijk om in te scha kelen naar numerieke waarden die kunnen worden geëvalueerd. U kunt twee verschillende dingen meten:

#### <a name="count-of-the-results-table-rows"></a>Aantal rijen met resultaten tabel

Aantal resultaten is de standaard maat eenheid. Ideaal voor het werken met gebeurtenissen, zoals Windows-gebeurtenis logboeken, syslog, toepassings uitzonderingen. Wordt geactiveerd wanneer records in het logboek worden uitgevoerd of in het geëvalueerde tijd venster niet plaatsvinden.

Logboek waarschuwingen werken het beste wanneer u gegevens in het logboek probeert te detecteren. Het werkt minder goed wanneer u een gebrek aan gegevens in de logboeken wilt detecteren. Bijvoorbeeld een waarschuwing over de heartbeat van de virtuele machine.

Voor werk ruimten en Application Insights wordt deze aangeroepen **op basis van** het selectie **aantal resultaten**. In alle andere resource typen wordt **meting** met selectie **tabel rijen** genoemd.

> [!NOTE]
> Aangezien logboeken semi-gestructureerde gegevens zijn, zijn ze inherent meer te laten laten verlichten dan de metrische gegevens. u kunt zich niet op de hoogte brengen wanneer u niet in de logboeken probeert te ontdekken en u kunt overwegen [metrische waarschuwingen](alerts-metric-overview.md)te gebruiken. U kunt gegevens verzenden naar het metrische archief vanuit Logboeken met [metrische waarschuwingen voor logboeken](alerts-metric-logs.md).

##### <a name="example-of-results-table-rows-count-use-case"></a>Voor beeld van het aantal use-cases voor tabel rijen met resultaten

U wilt weten wanneer uw toepassing reageert met fout code 500 (interne server fout). U maakt een waarschuwings regel met de volgende details:

- **Ophalen** 

```Kusto
requests
| where resultCode == "500"
```

- **Nauw keurigheid van de tijds periode/aggregatie:** 15 minuten
- **Waarschuwings frequentie:** 15 minuten
- **Drempel waarde:** Groter dan 0

Vervolgens worden de waarschuwings regels gecontroleerd op aanvragen die eindigen met een 500-fout code. De query wordt elke 15 minuten uitgevoerd, in de afgelopen 15 minuten. Als er zelfs één record wordt gevonden, wordt de waarschuwing geactiveerd en worden de geconfigureerde acties geactiveerd.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Berekening van een meting op basis van een numerieke kolom (zoals een CPU-item waarde)

Voor werk ruimten en Application Insights wordt deze aangeroepen **op basis van** de **metrische maat eenheid** van de selectie. In alle andere resource typen wordt **meting** genoemd met de selectie van een wille keurige kolom naam.

### <a name="aggregation-type"></a>Type aggregatie

De berekening die wordt uitgevoerd op meerdere records om ze samen te voegen tot één numerieke waarde. Bijvoorbeeld:
- **Count** retourneert het aantal records in de query
- **Gemiddelde** retourneert het gemiddelde van de meting van de [**aggregatie**](#aggregation-granularity) van de meet kolom die is gedefinieerd.

In werk ruimten en Application Insights wordt het alleen ondersteund in meet type meet **waarde** . Het query resultaat moet een kolom bevatten met de naam AggregatedValue die een numerieke waarde bevat na een door de gebruiker gedefinieerde aggregatie. In alle andere resource typen wordt het **aggregatie type** geselecteerd in het veld met die naam.

### <a name="aggregation-granularity"></a>Granulariteit aggregatie

Bepaalt het interval dat wordt gebruikt om meerdere records samen te voegen tot één numerieke waarde. Als u bijvoorbeeld **vijf minuten** hebt opgegeven, worden records gegroepeerd met een interval van vijf minuten met behulp van het opgegeven **aggregatie type** .

In werk ruimten en Application Insights wordt het alleen ondersteund in meet type meet **waarde** . Het query resultaat moet een [bin ()](/azure/kusto/query/binfunction) bevatten die het interval in de query resultaten bepaalt. In alle andere resource typen wordt het veld dat deze instelling bepaalt **aggregatie granulatie** genoemd.

> [!NOTE]
> Als [bin ()](/azure/kusto/query/binfunction) kan leiden tot een oneven tijds intervallen, wordt de functie [bin ()](/azure/kusto/query/binfunction) automatisch geconverteerd naar de functie [bin_at ()](/azure/kusto/query/binatfunction) met de juiste tijd tijdens runtime, om ervoor te zorgen dat er resultaten zijn met een vast punt.

### <a name="split-by-alert-dimensions"></a>Splitsen op waarschuwings dimensies

Splits waarschuwingen op cijfer-of teken reeks kolommen in afzonderlijke waarschuwingen door ze te groeperen in unieke combi Naties. Bij het maken van resource gerichte waarschuwingen op schaal (abonnement of het bereik van een resource groep) kunt u de kolom van de Azure-Resource-ID splitsen. Bij het splitsen van de Azure-Resource-ID-kolom wordt het doel van de waarschuwing gewijzigd in de opgegeven resource.

De kolom voor het splitsen van de Azure-Resource-ID wordt aanbevolen als u dezelfde voor waarde op meerdere Azure-resources wilt bewaken. U kunt bijvoorbeeld alle virtuele machines bewaken voor CPU-gebruik via 80%. U kunt er ook voor kiezen om te splitsen wanneer u een voor waarde op meerdere resources in het bereik wilt, zoals het controleren of het CPU-gebruik van ten minste vijf computers in het bereik van de resource groep meer dan 80% heeft.

In werk ruimten en Application Insights wordt het alleen ondersteund in meet type meet **waarde** . Het veld heet **aggregatie op**. Het is beperkt tot drie kolommen. Het hebben van meer dan drie groepen per kolom in de query kan leiden tot onverwachte resultaten. In alle andere resource typen wordt het geconfigureerd in de sectie **splitsen op dimensies** van de voor waarde (beperkt tot zes splitsingen).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Voor beeld van splitsen op basis van waarschuwings dimensies

U wilt bijvoorbeeld fouten bewaken voor meerdere virtuele machines waarop uw website/app wordt uitgevoerd in een specifieke resource groep. U kunt dit als volgt doen met behulp van een waarschuwings regel voor logboek:

- **Ophalen** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    Wanneer u werk ruimten en Application Insights met **metrische meet** waarschuwing logica gebruikt, moet deze regel worden toegevoegd aan de query tekst:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Resource-ID-kolom:** _ResourceId (splitsen op resource-id-kolom in waarschuwings regels is momenteel alleen beschikbaar voor abonnementen en resource groepen)
- **Afmetingen/geaggregeerd op:**
  - Computer = VM1, VM2 (waarden filteren in waarschuwings regel definitie is momenteel niet beschikbaar voor werk ruimten en Application Insights. Filter in de query tekst.)
- **Nauw keurigheid van de tijds periode/aggregatie:** 15 minuten
- **Waarschuwings frequentie:** 15 minuten
- **Drempel waarde:** Groter dan 0

Deze regel controleert of een virtuele machine fout gebeurtenissen bevat in de afgelopen 15 minuten. Elke virtuele machine wordt afzonderlijk bewaakt en de acties worden afzonderlijk geactiveerd.

> [!NOTE]
> De dimensies splitsen op basis van waarschuwingen zijn alleen beschikbaar voor de huidige scheduledQueryRules-API. Als u de verouderde [log Analytics alert-API](./api-alerts.md)gebruikt, moet u overschakelen. Meer [informatie over overschakelen](./alerts-log-api-switch.md). Resource gerichte waarschuwingen op schaal worden alleen ondersteund in de API-versie `2020-05-01-preview` en hoger.

## <a name="alert-logic-definition"></a>Definitie van waarschuwings logica

Wanneer u de query hebt gedefinieerd om de resultaten uit te voeren en te evalueren, moet u de logica voor waarschuwingen opgeven en wanneer u acties wilt starten. In de volgende secties worden de verschillende para meters beschreven die u kunt gebruiken:

### <a name="threshold-and-operator"></a>Drempel en operator

De query resultaten worden omgezet in een getal dat wordt vergeleken met de drempel waarde en de operator.

### <a name="frequency"></a>Frequentie

Het interval waarin de query wordt uitgevoerd. Kan worden ingesteld van 5 minuten tot één dag. Moet gelijk zijn aan of kleiner zijn dan het [tijds bereik](#query-time-range) van de query om logboek records niet te missen.

Als u bijvoorbeeld de tijds periode instelt op 30 minuten en de frequentie op 1 uur.  Als de query wordt uitgevoerd om 00:00, retourneert deze records tussen 23:30 en 00:00. De volgende keer dat de query wordt uitgevoerd, is 01:00 die records retourneert tussen 00:30 en 01:00. Records die zijn gemaakt tussen 00:00 en 00:30, worden nooit geëvalueerd.

### <a name="number-of-violations-to-trigger-alert"></a>Aantal schendingen voor trigger waarschuwing

U kunt de evaluatie periode van de waarschuwing en het aantal fouten opgeven die nodig zijn om een waarschuwing te activeren. Met kunt u beter een impact tijd definiëren om een waarschuwing te activeren. 

Als de granulariteit van de regel [**aggregatie**](#aggregation-granularity) bijvoorbeeld is gedefinieerd als 5 minuten, kunt u alleen een waarschuwing activeren als er drie fouten (15 minuten) van het afgelopen uur zijn opgetreden. Deze instelling wordt gedefinieerd door het bedrijfs beleid van uw toepassing.

## <a name="state-and-resolving-alerts"></a>Status en waarschuwingen oplossen

Logboek waarschuwingen zijn stateless. Waarschuwingen worden elke keer dat aan de voor waarde wordt voldaan, geactiveerd, zelfs als u deze eerder hebt geactiveerd. Geactiveerde waarschuwingen worden niet opgelost. U kunt [de waarschuwing markeren als gesloten](../alerts/alerts-managing-alert-states.md). U kunt ook acties dempen om te voor komen dat ze worden geactiveerd voor een bepaalde periode na het starten van een waarschuwings regel.

In werk ruimten en Application Insights heten deze **waarschuwingen onderdrukken**. In alle andere resource typen wordt **demping-acties** genoemd. 

Zie het volgende voor beeld van een waarschuwing:

| Tijd    | Evaluatie van de logboek voorwaarde | Resultaat 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | De waarschuwing wordt niet geactiveerd. Er zijn geen acties aangeroepen.
| 00:10 | TRUE  | Waarschuwingen worden geactiveerd en actie groepen worden aangeroepen. Nieuwe waarschuwings status actief.
| 00:15 | TRUE  | Waarschuwingen worden geactiveerd en actie groepen worden aangeroepen. Nieuwe waarschuwings status actief.
| 00:20 | FALSE | De waarschuwing wordt niet geactiveerd. Er zijn geen acties aangeroepen. Status van eerdere-waarschuwingen blijft actief.

## <a name="pricing-and-billing-of-log-alerts"></a>Prijzen en facturering van logboek waarschuwingen

De prijs informatie bevindt zich op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/). Logboek waarschuwingen worden vermeld onder resource provider `microsoft.insights/scheduledqueryrules` met:

- Waarschuwingen in het logboek vastleggen op Application Insights die worden weer gegeven met de exacte resource naam samen met de eigenschappen van de resource groep en de waarschuwing.
- Waarschuwingen in het logboek vastleggen op Log Analytics die worden weer gegeven met de exacte resource naam samen met de eigenschappen van de resource groep en de waarschuwing. Wanneer deze is gemaakt met behulp van de [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules).
- Logboek waarschuwingen die zijn gemaakt op basis van [verouderde log Analytics-API](./api-alerts.md) , worden niet bijgehouden in [Azure-resources](../../azure-resource-manager/management/overview.md) en hebben geen unieke resource namen afgedwongen. Deze waarschuwingen worden nog steeds gemaakt `microsoft.insights/scheduledqueryrules` als verborgen resources, die deze resource naamgevings structuur hebben `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Logboek waarschuwingen op een verouderde API worden weer gegeven met de naam van de verborgen resource, samen met de eigenschappen van de resource groep en de waarschuwing.

> [!NOTE]
> Niet-ondersteunde resource tekens zoals `<, >, %, &, \, ?, /` worden vervangen door `_` in de namen van de verborgen resources. dit wordt ook weer gegeven in de facturerings gegevens.

> [!NOTE]
> Logboek waarschuwingen voor Log Analytics die worden beheerd met behulp van de verouderde [log Analytics waarschuwings-API](./api-alerts.md) en verouderde sjablonen van [log Analytics opgeslagen Zoek opdrachten en waarschuwingen](../insights/solutions.md). [Meer informatie over overschakelen naar de huidige ScheduledQueryRules-API](../alerts/alerts-log-api-switch.md). Het beheer van waarschuwings regels moet worden uitgevoerd met behulp van [verouderde log Analytics-API](./api-alerts.md) totdat u besluit om over te scha kelen en u de verborgen resources niet kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken van waarschuwingen in Logboeken in azure](./alerts-log.md).
* Informatie [over webhooks in logboek waarschuwingen in azure](../alerts/alerts-log-webhook.md).
* Meer informatie over [Azure-waarschuwingen](./alerts-overview.md).
* Meer informatie over [log Analytics](../logs/log-query-overview.md).