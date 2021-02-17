---
title: Log Analytics waarschuwing gebruiken REST API
description: Met de Log Analytics waarschuwing REST API kunt u waarschuwingen maken en beheren in Log Analytics, die deel uitmaakt van Log Analytics.  In dit artikel vindt u meer informatie over de API en enkele voor beelden voor het uitvoeren van verschillende bewerkingen.
ms.subservice: logs
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6c347b7b8ddaac103019e15e32fb5c06219e0064
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544847"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Waarschuwings regels in Log Analytics maken en beheren met REST API 

> [!IMPORTANT]
> Zoals [aangekondigd](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), log Analytics-werk ruimte (n) die is gemaakt na *1 juni 2019* waarschuwings regels beheren met de huidige [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules/). We raden klanten aan om [over te scha kelen op de huidige API](./alerts-log-api-switch.md) in oudere werk ruimten om Azure monitor scheduledQueryRules- [voor delen](./alerts-log-api-switch.md#benefits)te gebruiken. In dit artikel wordt het beheer van waarschuwings regels beschreven met behulp van de verouderde API.

Met de Log Analytics waarschuwing REST API kunt u waarschuwingen in Log Analytics maken en beheren.  In dit artikel vindt u meer informatie over de API en enkele voor beelden voor het uitvoeren van verschillende bewerkingen.

De Log Analytics Zoek REST API bevindt zich in de REST en is toegankelijk via de Azure Resource Manager REST API. In dit document vindt u voor beelden waarin de API wordt geopend vanuit een Power shell-opdracht regel met behulp van  [ARMClient](https://github.com/projectkudu/ARMClient), een open-source opdracht regel programma waarmee de Azure Resource Manager-API wordt vereenvoudigd. Het gebruik van ARMClient en Power shell is een van de vele opties om toegang te krijgen tot de Log Analytics Search-API. Met deze hulpprogram ma's kunt u gebruikmaken van de REST-Azure Resource Manager-API om aanroepen naar Log Analytics werk ruimten te maken en zoek opdrachten in deze te voeren. De API voert Zoek resultaten naar u in JSON-indeling uit, zodat u de zoek resultaten op verschillende manieren programmatisch kunt gebruiken.

## <a name="prerequisites"></a>Vereisten
Waarschuwingen kunnen momenteel alleen worden gemaakt met een opgeslagen zoek opdracht in Log Analytics.  Raadpleeg de [rest API voor zoeken in Logboeken](../log-query/log-query-overview.md) voor meer informatie.

## <a name="schedules"></a>Schema's
Een opgeslagen zoek opdracht kan een of meer planningen hebben. De planning bepaalt hoe vaak de zoek actie wordt uitgevoerd en het tijds interval waarover de criteria worden geïdentificeerd.
Schema's hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Interval |Hoe vaak de zoek actie wordt uitgevoerd. Gemeten in minuten. |
| QueryTimeSpan |Het tijds interval waarover de criteria worden geëvalueerd. Moet gelijk aan of groter dan interval zijn. Gemeten in minuten. |
| Versie |De API-versie die wordt gebruikt.  Dit moet op dit moment altijd worden ingesteld op 1. |

Denk bijvoorbeeld aan een gebeurtenis query met een interval van 15 minuten en een time span van 30 minuten. In dit geval wordt de query om de 15 minuten uitgevoerd en wordt er een waarschuwing geactiveerd als de criteria voor een periode van 30 minuten nog steeds worden omgezet in True.

### <a name="retrieving-schedules"></a>Planningen ophalen
Gebruik de Get-methode om alle schema's voor een opgeslagen zoek opdracht op te halen.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

Gebruik de Get-methode met een schema-ID om een bepaald schema op te halen voor een opgeslagen zoek opdracht.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

Hier volgt een voor beeld van een antwoord op een schema.

```json
{
   "value": [{
      "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
      "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
      "properties": {
         "Interval": 15,
         "QueryTimeSpan": 15,
         "Enabled": true,
      }
   }]
}
```

### <a name="creating-a-schedule"></a>Een planning maken
Gebruik de put-methode met een unieke schema-ID om een nieuw schema te maken.  Twee schema's kunnen niet dezelfde ID hebben, zelfs als ze zijn gekoppeld aan verschillende opgeslagen Zoek opdrachten.  Wanneer u een schema in de Log Analytics-console maakt, wordt er een GUID gemaakt voor de schema-ID.

> [!NOTE]
> De naam voor alle opgeslagen Zoek opdrachten, planningen en acties die zijn gemaakt met de Log Analytics-API, moet een kleine letter zijn.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Een planning bewerken
Gebruik de put-methode met een bestaande schema-ID voor dezelfde opgeslagen zoek opdracht om dat schema te wijzigen. in het onderstaande voor beeld is het schema uitgeschakeld. De hoofd tekst van de aanvraag moet de *ETAG* van het schema bevatten.

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Schema's verwijderen
Gebruik de methode Delete met een schema-ID om een schema te verwijderen.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Acties
Een planning kan meerdere acties hebben. Een actie kan een of meer processen definiëren die moeten worden uitgevoerd, zoals het verzenden van een e-mail of het starten van een runbook, of het definiëren van een drempel waarde die bepaalt wanneer de resultaten van een zoek opdracht voldoen aan bepaalde criteria.  Bij sommige acties worden beide gedefinieerd, zodat de processen worden uitgevoerd wanneer aan de drempel wordt voldaan.

Alle acties hebben de eigenschappen in de volgende tabel.  Verschillende soorten waarschuwingen hebben verschillende extra eigenschappen, die hieronder worden beschreven.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |Het type actie.  Momenteel zijn de mogelijke waarden waarschuwing en webhook. |
| `Name` |Weergave naam voor de waarschuwing. |
| `Version` |De API-versie die wordt gebruikt.  Dit moet op dit moment altijd worden ingesteld op 1. |

### <a name="retrieving-actions"></a>Acties ophalen

Gebruik de Get-methode om alle acties voor een schema op te halen.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Gebruik de Get-methode met de actie-ID om een bepaalde actie voor een schema op te halen.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Acties maken of bewerken
Gebruik de put-methode met een actie-ID die uniek is voor het schema om een nieuwe actie te maken.  Wanneer u een actie in de Log Analytics-console maakt, is een GUID voor de actie-ID.

> [!NOTE]
> De naam voor alle opgeslagen Zoek opdrachten, planningen en acties die zijn gemaakt met de Log Analytics-API, moet een kleine letter zijn.

Gebruik de put-methode met een bestaande actie-ID voor dezelfde opgeslagen zoek opdracht om het schema te wijzigen.  De hoofd tekst van de aanvraag moet de ETAG van het schema bevatten.

De aanvraag indeling voor het maken van een nieuwe actie is afhankelijk van het actie type, zodat deze voor beelden worden gegeven in de volgende secties.

### <a name="deleting-actions"></a>Acties verwijderen

Gebruik de methode Delete met de actie-ID om een actie te verwijderen.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Waarschuwings acties
Een planning moet één en slechts één waarschuwings actie hebben.  Waarschuwings acties hebben een of meer van de secties in de volgende tabel.  Deze worden hieronder beschreven.

| Sectie | Beschrijving | Gebruik |
|:--- |:--- |:--- |
| Drempelwaarde |Criteria voor wanneer de actie wordt uitgevoerd.| Vereist voor elke waarschuwing, vóór of na de verlenging van Azure. |
| Severity |Label dat wordt gebruikt om een waarschuwing te classificeren wanneer deze wordt geactiveerd.| Vereist voor elke waarschuwing, vóór of na de verlenging van Azure. |
| Spanning |Optie om meldingen van de waarschuwing te stoppen. | Optioneel voor elke waarschuwing, vóór of na de verlenging van Azure. |
| Actiegroepen |Id's van Azure ActionGroup waarvoor vereiste acties zijn opgegeven, zoals e-mail berichten, SMSs, spraak oproepen, webhooks, Automation-Runbooks, ITSM-connectors enzovoort.| Vereist zodra waarschuwingen zijn uitgebreid naar Azure|
| Acties aanpassen|De standaard uitvoer voor Select-acties wijzigen vanuit ActionGroup| Optioneel voor elke waarschuwing kan worden gebruikt nadat waarschuwingen zijn uitgebreid naar Azure. |

### <a name="thresholds"></a>Drempel waarden
Een waarschuwings actie moet één en slechts één drempel waarde hebben.  Wanneer de resultaten van een opgeslagen zoek opdracht overeenkomen met de drempel waarde in een actie die is gekoppeld aan die zoek opdracht, worden alle andere processen in die actie uitgevoerd.  Een actie kan ook alleen een drempel waarde bevatten zodat deze kan worden gebruikt met acties van andere typen die geen drempel waarden bevatten.

Drempel waarden hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Operator` |Operator voor het vergelijken van de drempel waarde. <br> gt = groter dan <br> lt = kleiner dan |
| `Value` |Waarde voor de drempel. |

Denk bijvoorbeeld aan een gebeurtenis query met een interval van 15 minuten, een time span van 30 minuten en een drempel van meer dan 10. In dit geval wordt de query elke 15 minuten uitgevoerd en wordt er een waarschuwing geactiveerd als er 10 gebeurtenissen worden geretourneerd die zijn gemaakt gedurende een periode van 30 minuten.

Hier volgt een voor beeld van een antwoord voor een actie met alleen een drempel waarde.  

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Version": 1
}
```

Gebruik de put-methode met een unieke actie-ID om een nieuwe drempel actie voor een planning te maken.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Gebruik de put-methode met een bestaande actie-ID om een drempel actie voor een planning te wijzigen.  De hoofd tekst van de aanvraag moet de ETAG van de actie bevatten.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Severity
Met Log Analytics kunt u uw waarschuwingen in categorieën indelen, zodat u gemakkelijker beheer en sorteren kunt maken. De gedefinieerde ernst van de waarschuwing is: informatief, waarschuwing en kritiek. Deze worden toegewezen aan de genormaliseerde Ernst schaal van Azure-waarschuwingen als:

|Log Analytics Ernst niveau  |Ernst niveau van Azure-waarschuwingen  |
|---------|---------|
|`critical` |Ernst 0|
|`warning` |Ernst 1|
|`informational` | Sev 2|

Hieronder volgt een voor beeld van een reactie op een actie met alleen een drempel waarde en ernst. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

Gebruik de put-methode met een unieke actie-ID om een nieuwe actie te maken voor een planning met de ernst.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Gebruik de put-methode met een bestaande actie-ID om de ernst actie voor een schema te wijzigen.  De hoofd tekst van de aanvraag moet de ETAG van de actie bevatten.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Spanning
Op Log Analytics gebaseerde query waarschuwingen worden geactiveerd elke keer dat er een drempel waarde wordt bereikt of wordt overschreden. Op basis van de logica die in de query is geïmpliceerd, kan dit ertoe leiden dat een waarschuwing wordt geactiveerd voor een reeks van intervallen en daarom worden meldingen ook voortdurend verzonden. Om dit scenario te voor komen, kan een gebruiker de optie onderdrukken instellen Log Analytics om te wachten op een vastgestelde hoeveelheid tijd voordat de melding de tweede keer voor de waarschuwings regel wordt geactiveerd. Als onderdrukken is ingesteld op 30 minuten; vervolgens wordt de waarschuwing de eerste keer geactiveerd en worden er meldingen verzonden die zijn geconfigureerd. Daarna moet u 30 minuten wachten voordat de melding voor de waarschuwings regel opnieuw wordt gebruikt. In de tussentijdse periode wordt de waarschuwings regel alleen uitgevoerd voor uitvoering-alleen-melding wordt onderdrukt door Log Analytics voor opgegeven tijd, ongeacht het aantal keren dat de waarschuwings regel in deze periode is geactiveerd.

De eigenschap onderdrukking van Log Analytics waarschuwings regel is opgegeven met behulp van de *beperkings* waarde en de onderdrukkings periode met de waarde *DurationInMinutes* .

Hier volgt een voor beeld van een antwoord voor een actie met alleen een drempel waarde, ernst en onderdrukking-eigenschap

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Throttling": {
   "DurationInMinutes": 30
   },
   "Severity": "critical",
   "Version": 1
}
```

Gebruik de put-methode met een unieke actie-ID om een nieuwe actie te maken voor een planning met de ernst.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Gebruik de put-methode met een bestaande actie-ID om de ernst actie voor een schema te wijzigen.  De hoofd tekst van de aanvraag moet de ETAG van de actie bevatten.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Actiegroepen
Alle waarschuwingen in azure, gebruiken actie groep als het standaard mechanisme voor het afhandelen van acties. Met actie groep kunt u uw acties één keer opgeven en vervolgens de actie groep koppelen aan meerdere waarschuwingen-in Azure. Zonder dat dit nodig is om herhaaldelijk dezelfde acties opnieuw te declareren. Actie groepen ondersteunen meerdere acties, zoals e-mail, SMS, spraak oproep, ITSM-verbinding, Automation-Runbook, webhook-URI en meer. 

Voor gebruikers die hun waarschuwingen hebben uitgebreid naar Azure, moeten er nu actie groeps Details worden door gegeven aan de drempel waarde, om een waarschuwing te kunnen maken. E-mail gegevens, webhook-Url's, Details van Runbook-automatisering en andere acties moeten eerst worden gedefinieerd in een actie groep naast het maken van een waarschuwing. een kan [actie groep maken op basis van Azure monitor](./action-groups.md) in portal of [actie groeps-API](/rest/api/monitor/actiongroups)gebruiken.

Als u de koppeling van een actie groep wilt toevoegen aan een waarschuwing, geeft u de unieke Azure Resource Manager-ID van de actie groep op in de definitie van de waarschuwing. Hieronder vindt u een voor beeld van een afbeelding:

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ]
   },
   "Severity": "critical",
   "Version": 1
}
```

Gebruik de put-methode met een unieke actie-ID om al een bestaande actie groep aan een planning te koppelen.  Hier volgt een voor beeld van het gebruik van de afbeelding.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Gebruik de put-methode met een bestaande actie-ID voor het wijzigen van een actie groep die is gekoppeld aan een schema.  De hoofd tekst van de aanvraag moet de ETAG van de actie bevatten.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Acties aanpassen
Standaard worden acties gevolgd door de standaard sjabloon en-indeling voor meldingen. Maar de gebruiker kan sommige acties aanpassen, zelfs als deze worden beheerd door actie groepen. Op dit moment kunnen aanpassingen voor e-mail onderwerp en webhook Payload worden uitgevoerd.

##### <a name="customize-e-mail-subject-for-action-group"></a>E-mail onderwerp aanpassen voor actie groep
Standaard is het e-mail onderwerp voor waarschuwingen: waarschuwings melding `<AlertName>` voor `<WorkspaceName>` . Maar dit kan worden aangepast, zodat u specifieke woorden of labels kunt gebruiken om eenvoudig filter regels in uw postvak in te maken. De details van de e-mailkop tekst moeten worden verzonden, samen met ActionGroup-Details, zoals hieronder wordt beschreven.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
      "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
}
```

Gebruik de put-methode met een unieke actie-ID om al een bestaande actie groep te koppelen aan een planning.  Hier volgt een voor beeld van het gebruik van de afbeelding.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Gebruik de put-methode met een bestaande actie-ID voor het wijzigen van een actie groep die is gekoppeld aan een schema.  De hoofd tekst van de aanvraag moet de ETAG van de actie bevatten.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>De nettolading van de webhook voor de actie groep aanpassen
De webhook die wordt verzonden via de actie groep voor log Analytics, heeft standaard een vaste structuur. U kunt de JSON-nettolading echter aanpassen met behulp van specifieke variabelen die worden ondersteund om te voldoen aan de vereisten van het webhook-eind punt. Zie [webhook-actie voor waarschuwings regels voor logboeken](./alerts-log-webhook.md)voor meer informatie. 

De gegevens voor het aanpassen van de webhook moeten samen met ActionGroup-Details worden verzonden en worden toegepast op alle webhook-URI'S die zijn opgegeven in de actie groep. zoals in het voor beeld hieronder.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
   "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
   "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
},
```

Gebruik de put-methode met een unieke actie-ID om al een bestaande actie groep te koppelen aan een planning.  Hier volgt een voor beeld van het gebruik van de afbeelding.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Gebruik de put-methode met een bestaande actie-ID voor het wijzigen van een actie groep die is gekoppeld aan een schema.  De hoofd tekst van de aanvraag moet de ETAG van de actie bevatten.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Volgende stappen

* Gebruik de [rest API om Zoek opdrachten](../log-query/log-query-overview.md) in het logboek in log Analytics uit te voeren.
* Meer informatie over [logboek waarschuwingen in azure monitor](./alerts-unified-log.md)
* [Waarschuwings regels voor logboeken maken, bewerken of beheren in azure monitor](./alerts-log.md)

