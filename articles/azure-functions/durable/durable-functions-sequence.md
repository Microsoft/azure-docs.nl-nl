---
title: Functie koppeling in Durable Functions-Azure
description: Meer informatie over het uitvoeren van een Durable Functions-voor beeld dat een reeks functies uitvoert.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028247"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Functie koppeling in Durable Functions-Hello-reeks voorbeeld

Functie koppeling heeft betrekking op het patroon van het uitvoeren van een reeks functies in een bepaalde volg orde. Vaak moet de uitvoer van de ene functie worden toegepast op de invoer van een andere functie. In dit artikel wordt de keten volgorde beschreven die u maakt wanneer u de Durable functions Quick Start ([C#](durable-functions-create-first-csharp.md),  [Java script](quickstart-js-vscode.md)of [python](quickstart-python-vscode.md)) hebt voltooid. Zie [Durable functions Overview](durable-functions-overview.md)voor meer informatie over Durable functions.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app uitgelegd:

* `E1_HelloSequence`: Een [Orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die `E1_SayHello` meerdere keren in een reeks aanroept. De uitvoer van de `E1_SayHello` aanroepen wordt opgeslagen en de resultaten worden vastgelegd.
* `E1_SayHello`: Een [activiteit functie](durable-functions-bindings.md#activity-trigger) die een teken reeks samenvoegt met ' Hello '.
* `HttpStart`: Een door HTTP geactiveerde [duurzame client](durable-functions-bindings.md#orchestration-client) functie waarmee een exemplaar van de Orchestrator wordt gestart.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence Orchestrator-functie

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Alle C#-Orchestration-functies moeten een para meter van `DurableOrchestrationContext` het type, die voor komt in de `Microsoft.Azure.WebJobs.Extensions.DurableTask` Assembly, hebben. Met dit context object kunt u andere *activiteiten* functies aanroepen en invoer parameters door geven met behulp van de `CallActivityAsync` methode.

De code roept `E1_SayHello` drie keer op met verschillende parameter waarden. De geretourneerde waarde van elke aanroep wordt toegevoegd aan de `outputs` lijst, die aan het einde van de functie wordt geretourneerd.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Java script-Durable Functions zijn alleen beschikbaar voor de functies 3,0 runtime.

#### <a name="functionjson"></a>function.json

Als u Visual Studio code of de Azure Portal voor ontwikkeling gebruikt, is dit de inhoud van de *function.jsin* het bestand voor de Orchestrator-functie. De meeste Orchestrator- *function.jsop* bestanden zien bijna precies zo.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

De belang rijke ding is het `orchestrationTrigger` bindings type. Alle Orchestrator-functies moeten dit trigger type gebruiken.

> [!WARNING]
> Als u de regel ' geen I/O ' van Orchestrator-functies wilt gebruiken, gebruikt u geen invoer-of uitvoer bindingen wanneer u de `orchestrationTrigger` trigger binding gebruikt.  Als er andere invoer-of uitvoer bindingen nodig zijn, moeten ze in plaats daarvan worden gebruikt in de context van `activityTrigger` functions, die worden aangeroepen door de Orchestrator. Zie het artikel [Orchestrator functie code constraints](durable-functions-code-constraints.md) voor meer informatie.

#### <a name="indexjs"></a>index.js

Dit is de Orchestrator-functie:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alle Java script-Orchestration-functies moeten de [ `durable-functions` module](https://www.npmjs.com/package/durable-functions)bevatten. Het is een bibliotheek waarmee u Durable Functions kunt schrijven in Java script. Er zijn drie belang rijke verschillen tussen een Orchestrator-functie en andere Java script-functies:

1. De Orchestrator-functie is een [functie Generator](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. De functie wordt ingepakt in een aanroep naar de `durable-functions` methode van de module `orchestrator` (hier `df` ).
3. De functie moet synchroon zijn. Omdat de ' Orchestrator '-methode de aanroep van ' context. done ' verwerkt, moet de functie gewoon ' return ' zijn.

Het `context` object bevat een `df` duurzaam Orchestration context-object waarmee u andere *activiteiten* functies kunt aanroepen en invoer parameters met behulp van de methode kan door geven `callActivity` . De code roept `E1_SayHello` drie keer op met verschillende parameter waarden, met `yield` om aan te geven dat de uitvoering moet wachten op de functie aanroepen van de async-activiteit die moeten worden geretourneerd. De geretourneerde waarde van elke aanroep wordt toegevoegd aan de `outputs` matrix, die aan het einde van de functie wordt geretourneerd.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python-Durable Functions zijn alleen beschikbaar voor de functies 3,0-runtime.


#### <a name="functionjson"></a>function.json

Als u Visual Studio code of de Azure Portal voor ontwikkeling gebruikt, is dit de inhoud van de *function.jsin* het bestand voor de Orchestrator-functie. De meeste Orchestrator- *function.jsop* bestanden zien bijna precies zo.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

De belang rijke ding is het `orchestrationTrigger` bindings type. Alle Orchestrator-functies moeten dit trigger type gebruiken.

> [!WARNING]
> Als u de regel ' geen I/O ' van Orchestrator-functies wilt gebruiken, gebruikt u geen invoer-of uitvoer bindingen wanneer u de `orchestrationTrigger` trigger binding gebruikt.  Als er andere invoer-of uitvoer bindingen nodig zijn, moeten ze in plaats daarvan worden gebruikt in de context van `activityTrigger` functions, die worden aangeroepen door de Orchestrator. Zie het artikel [Orchestrator functie code constraints](durable-functions-code-constraints.md) voor meer informatie.

#### <a name="__init__py"></a>\_\_init\_\_.py

Dit is de Orchestrator-functie:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Alle python-Orchestration-functies moeten het [ `durable-functions` pakket](https://pypi.org/project/azure-functions-durable)bevatten. Het is een bibliotheek waarmee u Durable Functions in python kunt schrijven. Er zijn twee belang rijke verschillen tussen een Orchestrator-functie en andere python-functies:

1. De Orchestrator-functie is een [functie Generator](https://wiki.python.org/moin/Generators).
2. Het _bestand_ moet de Orchestrator-functie als Orchestrator registreren door `main = df.Orchestrator.create(<orchestrator function name>)` aan het einde van het bestand te vermelden. Dit helpt het onderscheid te maken tussen andere, hulp, functies die in het bestand zijn gedeclareerd.

Met het- `context` object kunt u andere *activiteiten* functies aanroepen en invoer parameters door geven met behulp van de `call_activity` methode. De code roept `E1_SayHello` drie keer op met verschillende parameter waarden, met `yield` om aan te geven dat de uitvoering moet wachten op de functie aanroepen van de async-activiteit die moeten worden geretourneerd. De geretourneerde waarde van elke aanroep wordt aan het einde van de functie geretourneerd.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello-activiteit functie

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Activiteiten gebruiken het- `ActivityTrigger` kenmerk. Gebruik de opgegeven `IDurableActivityContext` voor het uitvoeren van activiteiten die betrekking hebben op activiteit, zoals het openen van de invoer waarde met behulp van `GetInput<T>` .

De implementatie van `E1_SayHello` is een relatief tamelijk opmaak bewerking voor teken reeksen.

In plaats van een binding met een `IDurableActivityContext` , kunt u rechtstreeks verbinding maken met het type dat wordt door gegeven aan de functie activity. Bijvoorbeeld:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsop

De *function.jsin* het bestand voor de functie activiteit `E1_SayHello` is vergelijkbaar met die van `E1_HelloSequence` , behalve dat er een `activityTrigger` bindings type wordt gebruikt in plaats van een `orchestrationTrigger` bindings type.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Alle activiteit functies die worden aangeroepen door een Orchestration-functie, moeten de `activityTrigger` binding gebruiken.

De implementatie van `E1_SayHello` is een relatief tamelijk opmaak bewerking voor teken reeksen.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

In tegens telling tot de Orchestration-functie heeft een activiteit functie geen speciale configuratie nodig. De invoer die wordt door gegeven door de Orchestrator-functie bevindt zich op het `context.bindings` object onder de naam van de `activityTrigger` binding-in dit geval `context.bindings.name` . De naam van de binding kan worden ingesteld als een para meter van de geëxporteerde functie en direct worden geopend. Dit is de voorbeeld code.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsop

De *function.jsin* het bestand voor de functie activiteit `E1_SayHello` is vergelijkbaar met die van `E1_HelloSequence` , behalve dat er een `activityTrigger` bindings type wordt gebruikt in plaats van een `orchestrationTrigger` bindings type.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> Alle activiteit functies die worden aangeroepen door een Orchestration-functie, moeten de `activityTrigger` binding gebruiken.

De implementatie van `E1_SayHello` is een relatief tamelijk opmaak bewerking voor teken reeksen.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

In tegens telling tot de Orchestrator-functie heeft een activiteit functie geen speciale configuratie nodig. De invoer die door de Orchestrator-functie wordt door gegeven, is rechtstreeks toegankelijk als de para meter voor de functie.

---

### <a name="httpstart-client-function"></a>HttpStart-client functie

U kunt een exemplaar van Orchestrator-functie starten met behulp van een client functie. U gebruikt de `HttpStart` http-geactiveerde functie om exemplaren van te starten `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

De functie moet een invoer binding bevatten om te kunnen communiceren met Orchestrator `DurableClient` . U gebruikt de-client om een indeling te starten. Het kan ook helpen u een HTTP-antwoord met Url's te retour neren voor het controleren van de status van de nieuwe indeling.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsop

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

De functie moet een invoer binding bevatten om te kunnen communiceren met Orchestrator `durableClient` .

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Gebruiken `df.getClient` om een object te verkrijgen `DurableOrchestrationClient` . U gebruikt de-client om een indeling te starten. Het kan ook helpen u een HTTP-antwoord met Url's te retour neren voor het controleren van de status van de nieuwe indeling.

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsop

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

De functie moet een invoer binding bevatten om te kunnen communiceren met Orchestrator `durableClient` .

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

Gebruik de `DurableOrchestrationClient` constructor om een Durable functions-client te verkrijgen. U gebruikt de-client om een indeling te starten. Het kan ook helpen u een HTTP-antwoord met Url's te retour neren voor het controleren van de status van de nieuwe indeling.

---

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Als u de indeling wilt uitvoeren `E1_HelloSequence` , verzendt u de volgende HTTP POST-aanvraag naar de `HttpStart` functie.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> In het vorige HTTP-fragment wordt ervan uitgegaan dat er een vermelding in het `host.json` bestand staat waarmee het standaard `api/` voorvoegsel van alle http-trigger functies url's wordt verwijderd. U kunt de opmaak voor deze configuratie vinden in het `host.json` bestand in de voor beelden.

Als u bijvoorbeeld het voor beeld uitvoert in een functie-app met de naam ' myfunctionapp ', vervangt u ' {host} ' door ' myfunctionapp.azurewebsites.net '.

Het resultaat is een HTTP 202-antwoord, zoals dit (afgekapt voor de boog):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Op dit moment wordt de indeling in de wachtrij geplaatst en begint deze onmiddellijk te worden uitgevoerd. De URL in de `Location` header kan worden gebruikt om de status van de uitvoering te controleren.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Het resultaat is de status van de indeling. De service wordt uitgevoerd en snel voltooid, zodat deze in de status *voltooid* wordt weer gegeven met een antwoord dat er als volgt uitziet (afgekapt voor de boog):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Zoals u ziet, is de `runtimeStatus` van de instantie *voltooid* en bevat het `output` JSON-serialisatie resultaat van de functie-uitvoering van Orchestrator.

> [!NOTE]
> U kunt soort gelijke starter Logic implementeren voor andere trigger typen, zoals `queueTrigger` , `eventHubTrigger` of `timerTrigger` .

Bekijk de uitvoer logboeken van de functie. De `E1_HelloSequence` functie is meerdere keren gestart en voltooid als gevolg van het gedrag voor opnieuw afspelen dat wordt beschreven in het onderwerp over de [Orchestration-betrouw baarheid](durable-functions-orchestrations.md#reliability) . Aan de andere kant waren er maar drie uitvoeringen van, `E1_SayHello` omdat deze functies niet opnieuw worden afgespeeld.

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld is een eenvoudige functie koppelings architectuur gedemonstreerd. In het volgende voor beeld ziet u hoe u het patroon uitwaaieren/ventilatoren implementeert.

> [!div class="nextstepaction"]
> [Voer het voor beeld uit voor uitwaaieren/ventilatoren](durable-functions-cloud-backup.md)
