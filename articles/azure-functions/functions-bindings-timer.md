---
title: Timer trigger voor Azure Functions
description: Meer informatie over het gebruik van timer triggers in Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: c6b3bd61386cbde0e8de63055eee9218e372dfcd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547839"
---
# <a name="timer-trigger-for-azure-functions"></a>Timer trigger voor Azure Functions

In dit artikel wordt uitgelegd hoe u met timer triggers in Azure Functions kunt werken. Met een timer trigger kunt u een functie uitvoeren volgens een schema.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Zie [hand matig een niet met http geactiveerde functie uitvoeren](./functions-manually-run-non-http.md)voor meer informatie over het hand matig uitvoeren van een door een timer geactiveerde functie.

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De timer trigger is opgenomen in het pakket [micro soft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

De timer trigger is opgenomen in het pakket [micro soft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versie 2. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die elke keer dat de minuten wordt uitgevoerd een waarde heeft die deelbaar is door vijf (bijvoorbeeld als de functie wordt gestart om 18:57:00, de volgende prestaties op 19:00:00). Het [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object wordt door gegeven aan de functie.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van een timer trigger in een *function.jsin* een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. Met de functie wordt een logboek geschreven dat aangeeft of deze functie wordt aangeroepen vanwege een gemiste schema gebeurtenis. Het [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object wordt door gegeven aan de functie.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Dit is de C# Script-code:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld wordt de functie geactiveerd en wordt elke vijf minuten uitgevoerd. De `@TimerTrigger` aantekening voor de functie definieert het schema met dezelfde teken reeks notatie [als cron-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een binding van een timer trigger in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie wordt een logboek geschreven dat aangeeft of deze functie wordt aangeroepen vanwege een gemiste schema gebeurtenis. Er wordt een [Timer object](#usage) door gegeven aan de functie.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Dit is de JavaScript-code:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u hoe u de *function.jsop* en *run.ps1* bestand kunt configureren voor een timer trigger in [Power shell](./functions-reference-powershell.md).

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

Er wordt een exemplaar van het [object Timer](#usage) door gegeven als het eerste argument voor de functie.

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld wordt een timer trigger binding gebruikt waarvan de configuratie wordt beschreven in de *function.jsin* het bestand. De daad werkelijke [python-functie](functions-reference-python.md) die gebruikmaakt van de binding, wordt beschreven in het bestand *__init__. py* . Het object dat is door gegeven aan de functie, is van het type [Azure. functions. TimerRequest-object](/python/api/azure-functions/azure.functions.timerrequest). De functie logica schrijft naar de logboeken die aangeven of de huidige aanroep wordt veroorzaakt door een gemist schema voorval.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Dit is de Python-code:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

De constructor van het kenmerk heeft een CRON-expressie of een `TimeSpan` . U kunt `TimeSpan` alleen gebruiken als de functie-app wordt uitgevoerd op een app service-abonnement. `TimeSpan` wordt niet ondersteund voor verbruiks-of elastische Premium-functies.

In het volgende voor beeld ziet u een CRON-expressie:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

De `@TimerTrigger` aantekening voor de functie definieert het schema met dezelfde teken reeks notatie [als cron-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `TimerTrigger`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' Timer trigger '. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op in. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**name** | N.v.t. | De naam van de variabele die het Timer object in functie code vertegenwoordigt. | 
|**planning**|**ScheduleExpression**|Een [cron-expressie](#ncrontab-expressions) of een [time span](#timespan) -waarde. A `TimeSpan` kan alleen worden gebruikt voor een functie-app die wordt uitgevoerd op een app service plan. U kunt de schema-expressie in een app-instelling plaatsen en deze eigenschap instellen op de naam van de app-instelling die wordt verpakt in **%** tekens, zoals in dit voor beeld:% ScheduleAppSetting%. |
|**runOnStartup**|**RunOnStartup**|Als de `true` functie wordt aangeroepen wanneer de runtime wordt gestart. De runtime wordt bijvoorbeeld gestart wanneer de functie-app wordt geactiveerd nadat de inactiviteit is voltooid. Wanneer de functie-app opnieuw wordt gestart vanwege functie wijzigingen en wanneer de functie-app wordt geschaald. Daarom moet **runOnStartup** zelden worden ingesteld op `true` , met name bij de productie. |
|**useMonitor**|**UseMonitor**|Ingesteld op `true` of `false` om aan te geven of het schema moet worden bewaakt. Het plannen van de controle houdt in dat het schema wordt gebruikt om ervoor te zorgen dat het schema goed wordt onderhouden, zelfs wanneer de functie-app-exemplaren opnieuw worden gestart. Als niet expliciet is ingesteld, is de standaard waarde `true` voor schema's met een terugkeer patroon dat groter is dan of gelijk is aan 1 minuut. Voor schema's die meer dan één keer per minuut activeren, is de standaard waarde `false` .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> We raden u aan om **runOnStartup** in te stellen op `true` in productie. Met deze instelling wordt de code op zeer onvoorspelbare tijden uitgevoerd. In bepaalde productie-instellingen kan deze extra uitvoeringen leiden tot aanzienlijk hogere kosten voor apps die worden gehost in verbruiks abonnementen. Als **runOnStartup** is ingeschakeld, wordt de trigger bijvoorbeeld aangeroepen wanneer uw functie-app wordt geschaald. Zorg ervoor dat u het productie gedrag van uw functies volledig begrijpt voordat u **runOnStartup** in productie inschakelt.

## <a name="usage"></a>Gebruik

Wanneer een timer functie wordt geactiveerd, wordt er een timer object door gegeven aan de functie. De volgende JSON is een voorbeeld weergave van het object Timer.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

De `isPastDue` eigenschap is op het `true` moment dat de huidige functie aanroep later is dan gepland. Het opnieuw starten van een functie-app kan ertoe leiden dat een aanroep wordt gemist.

## <a name="ncrontab-expressions"></a>NCRONTAB-expressies

Azure Functions maakt gebruik van de [NCronTab](https://github.com/atifaziz/NCrontab) -bibliotheek om NCronTab-expressies te interpreteren. Een NCRONTAB-expressie is vergelijkbaar met een CRON-expressie, behalve dat het een extra zesde veld bevat die aan het begin moet worden gebruikt voor tijd nauwkeurigheid in seconden:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Elk veld kan een van de volgende typen waarden hebben:

|Type  |Voorbeeld  |Wanneer geactiveerd  |
|---------|---------|---------|
|Een specifieke waarde |<nobr>`0 5 * * * *`</nobr>| Eenmaal per uur van de dag om 5 van elk uur |
|Alle waarden ( `*` )|<nobr>`0 * 5 * * *`</nobr>| Op elke minuut in het uur, te beginnen bij uur 5 |
|Een bereik ( `-` operator)|<nobr>`5-7 * * * * *`</nobr>| Drie keer per minuut 5 tot en met 7 gedurende elke minuut van elk uur van elke dag |
|Een reeks waarden ( `,` operator)|<nobr>`5,8,10 * * * * *`</nobr>| Drie keer per minuut in seconden 5, 8 en 10 gedurende elke minuut van elk uur van elke dag |
|Een interval waarde ( `/` operator)|<nobr>`0 */5 * * * *`</nobr>| 12 keer per uur per seconde van elke vijfde minuut van elk uur van elke dag |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB-voor beelden

Hier volgen enkele voor beelden van NCRONTAB-expressies die u kunt gebruiken voor de timer trigger in Azure Functions.

| Voorbeeld            | Wanneer geactiveerd                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | eenmaal per vijf minuten            |
| `0 0 * * * *`      | eenmaal aan de bovenkant van elk uur      |
| `0 0 */2 * * *`    | eenmaal per twee uur               |
| `0 0 9-17 * * *`   | eenmaal per uur van 9 tot 5 uur  |
| `0 30 9 * * *`     | elke dag om 9:30 uur               |
| `0 30 9 * * 1-5`   | om 9:30 uur om de dag           |
| `0 30 9 * Jan Mon` | om 9:30 uur elke maandag in januari |

> [!NOTE]
> De NCRONTAB-expressie vereist een indeling van **zes velden** . De zesde veld positie is een waarde voor seconden die aan het begin van de expressie wordt geplaatst. Vijf veld cron expressies worden niet ondersteund in Azure.

### <a name="ncrontab-time-zones"></a>NCRONTAB tijd zones

De getallen in een CRON-expressie verwijzen naar een datum en tijd, niet een tijds Panne. Bijvoorbeeld, een 5 in het `hour` veld verwijst naar 5:00 uur, niet om de vijf uur.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` kan alleen worden gebruikt voor een functie-app die wordt uitgevoerd op een app service plan.

In tegens telling tot een CRON-expressie `TimeSpan` specificeert een waarde het tijds interval tussen elke functie aanroep. Wanneer een functie wordt voltooid nadat deze langer dan het opgegeven interval is uitgevoerd, roept de timer de functie opnieuw aan.

Wordt weer gegeven als een teken reeks, de `TimeSpan` notatie is `hh:mm:ss` `hh` kleiner dan 24. Als de eerste twee cijfers 24 of hoger zijn, is de notatie `dd:hh:mm` . Hier volgen enkele voorbeelden:

| Voorbeeld      | Wanneer geactiveerd |
|--------------|----------------|
| "01:00:00"   | elk uur     |
| "00:01:00"   | elke minuut   |
| "24:00:00"   | elke 24 dagen  |
| "1,00:00:00" | Elke dag      |

## <a name="scale-out"></a>Uitschalen

Als een functie-app wordt geschaald naar meerdere instanties, wordt slechts één exemplaar van een door een timer geactiveerde functie uitgevoerd voor alle exemplaren. Als er nog een uitstaande aanroep wordt uitgevoerd, wordt deze niet opnieuw geactiveerd.

## <a name="function-apps-sharing-storage"></a>Functie-apps die opslag ruimte delen

Als u opslag accounts deelt in functie-apps die niet zijn geïmplementeerd op app service, moet u mogelijk expliciet een host-ID aan elke app toewijzen.

| Functie versie | Instelling                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (en hoger)  | `AzureFunctionsWebHost__hostid` omgevings variabele |
| 1.x               | `id` in *host.jsop*                                  |

U kunt de identificerende waarde weglaten of hand matig de identificerende configuratie van elke functie-app instellen op een andere waarde.

De timer trigger gebruikt een opslag vergrendeling om ervoor te zorgen dat er slechts één timer exemplaar is wanneer een functie-app wordt geschaald naar meerdere exemplaren. Als twee functie-apps dezelfde identificerende configuratie delen en elk een timer trigger gebruikt, wordt er slechts één timer uitgevoerd.

## <a name="retry-behavior"></a>Gedrag voor opnieuw proberen

In tegens telling tot de wachtrij trigger, wordt de timer trigger niet meer geprobeerd nadat een functie is mislukt. Wanneer een functie mislukt, wordt deze niet opnieuw aangeroepen tot de volgende tijd op de planning.

## <a name="troubleshooting"></a>Problemen oplossen

Zie voor meer informatie over wat u moet doen wanneer de timer trigger niet werkt zoals verwacht, het [onderzoeken en rapporteren van problemen met door timer geactiveerde functies die niet](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)worden gestart.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ga naar een Snelstartgids die gebruikmaakt van een timer trigger](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
