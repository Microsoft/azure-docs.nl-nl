---
title: Duurzame Orchestrator-code beperkingen-Azure Functions
description: De functie herhalingen en code beperkingen voor Azure Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 63db8375379144b2ede78d9e7010a350b3f69b12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726407"
---
# <a name="orchestrator-function-code-constraints"></a>Functie code beperkingen van Orchestrator

Durable Functions is een uitbrei ding van [Azure functions](../functions-overview.md) waarmee u stateful apps kunt bouwen. U kunt een [Orchestrator-functie](durable-functions-orchestrations.md) gebruiken om de uitvoering van andere duurzame functies in een functie-app te organiseren. Orchestrator-functies zijn stateful, betrouwbaar en mogelijk lang actief.

## <a name="orchestrator-code-constraints"></a>Codebeperkingen voor orchestrator

Orchestrator-functies gebruiken [gebeurtenis bronnen](/azure/architecture/patterns/event-sourcing) om betrouw bare uitvoering te garanderen en om de lokale status van de variabele te onderhouden. Het [gedrag](durable-functions-orchestrations.md#reliability) voor opnieuw afspelen van Orchestrator-code maakt beperkingen voor het type code dat u kunt schrijven in een Orchestrator-functie. Orchestrator-functies moeten bijvoorbeeld *deterministisch* zijn: een Orchestrator-functie wordt meerdere keren opnieuw afgespeeld en moet telkens hetzelfde resultaat opleveren.

### <a name="using-deterministic-apis"></a>Deterministische Api's gebruiken

In deze sectie vindt u enkele eenvoudige richt lijnen om ervoor te zorgen dat uw code deterministisch is.

Orchestrator-functies kunnen wille keurige API'S aanroepen in hun doel talen. Het is echter belang rijk dat Orchestrator-functies alleen deterministische Api's aanroepen. Een *deterministische API* is een API die altijd dezelfde waarde retourneert als dezelfde invoer, ongeacht wanneer of hoe vaak deze wordt aangeroepen.

De volgende tabel bevat voor beelden van Api's die u moet vermijden omdat deze *niet* deterministisch zijn. Deze beperkingen gelden alleen voor Orchestrator-functies. Andere functie typen hebben deze beperkingen niet.

| API-categorie | Reden | Tijdelijke oplossing |
| ------------ | ------ | ---------- |
| Datums en tijden  | Api's die de huidige datum of tijd retour neren, zijn niet-deterministisch, omdat de geretourneerde waarde voor elke herhaling afwijkt. | Gebruik de eigenschap [CurrentUtcDateTime](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.currentutcdatetime) in .net, de `currentUtcDateTime` API in Java script of de `current_utc_datetime` API in Python, die veilig is voor opnieuw afspelen. |
| GUID'S en UUID  | Api's die een wille keurige GUID of UUID retour neren, zijn niet-deterministisch, omdat de gegenereerde waarde voor elke herhaling verschillend is. | Gebruik [NewGuid](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.newguid) in .net, `newGuid` in Java script en `new_guid` in Python, voor het veilig genereren van wille keurige guid's. |
| Wille keurige getallen | Api's die wille keurige getallen retour neren, zijn niet-deterministisch omdat de gegenereerde waarde voor elke herhaling verschillend is. | Gebruik een functie activiteit om wille keurige getallen te retour neren naar een indeling. De retour waarden van de activiteit functies zijn altijd veilig voor opnieuw afspelen. |
| Bindingen | Invoer-en uitvoer bindingen worden normaal gesp roken I/O en niet-deterministisch. Een Orchestrator-functie mag niet rechtstreeks gebruikmaken van de client bindingen van de [Orchestration-client](durable-functions-bindings.md#orchestration-client) en- [entiteit](durable-functions-bindings.md#entity-client) . | Gebruik invoer-en uitvoer bindingen binnen client-of activiteit functies. |
| Netwerk | Netwerk aanroepen zijn vereist voor externe systemen en zijn niet-deterministisch. | Gebruik activiteit functies om netwerk aanroepen te maken. Als u een HTTP-aanroep van uw Orchestrator-functie wilt maken, kunt u ook de [duurzame HTTP-api's](durable-functions-http-features.md#consuming-http-apis)gebruiken. |
| Api's blok keren | Het blok keren `Thread.Sleep` van api's zoals in .net en soort gelijke api's kan leiden tot prestatie-en schaal problemen voor Orchestrator-functies en moet worden vermeden. In het Azure Functions verbruiks abonnement kunnen ze zelfs leiden tot onnodige runtime kosten. | Gebruik alternatieven om Api's te blok keren wanneer deze beschikbaar zijn. U kunt bijvoorbeeld gebruiken  `CreateTimer` om vertragingen in de Orchestration-uitvoering uit te voeren. [Duurzame timer](durable-functions-timers.md) vertragingen tellen niet mee voor de uitvoerings tijd van een Orchestrator-functie. |
| Async-Api's | De Orchestrator-code mag nooit een asynchrone bewerking starten, behalve door gebruik te maken van de `IDurableOrchestrationContext` API, de `context.df` API in Java script of de `context` API in python. U kunt bijvoorbeeld niet gebruiken `Task.Run` , `Task.Delay` , en `HttpClient.SendAsync` in .net of `setTimeout` en `setInterval` in Java script. Het duurzame taak raamwerk voert Orchestrator-code uit op één thread. Er kan niet worden gecommuniceerd met andere threads die kunnen worden aangeroepen door andere async-Api's. | Een Orchestrator-functie mag alleen duurzame asynchrone aanroepen maken. Activiteit functies moeten andere async-API-aanroepen uitvoeren. |
| Asynchrone Java script-functies | U kunt geen Java script Orchestrator-functies declareren `async` omdat de node.js runtime niet garandeert dat asynchrone functies deterministisch zijn. | Java script Orchestrator-functies declareren als synchrone Generator functies |
| Python-routines | U kunt geen python Orchestrator-functies declareren als conroutines, dat wil zeggen Declareer ze met het `async` tref woord, omdat de semantiek van een coroutine niet kan worden uitgelijnd met het Durable functions replay-model. | Geef python Orchestrator-functies op als Generators, wat betekent dat u de `context` API zou moeten gebruiken `yield` in plaats van `await` .   |
| Threading-Api's | Het duurzame taak raamwerk voert Orchestrator-code uit op één thread en kan niet communiceren met andere threads. Het introduceren van nieuwe threads in de uitvoering van een Orchestration kan leiden tot niet-deterministische uitvoering of deadlocks. | Orchestrator-functies moeten vrijwel nooit threading-Api's gebruiken. Vermijd bijvoorbeeld in .NET het gebruik van `ConfigureAwait(continueOnCapturedContext: false)` ; Dit zorgt ervoor dat taak voortzettingen worden uitgevoerd op de originele Orchestrator-functie `SynchronizationContext` . Als dergelijke Api's nood zakelijk zijn, beperkt u het gebruik ervan tot alleen activiteit functies. |
| Statische variabelen | Vermijd het gebruik van niet-constante statische variabelen in Orchestrator-functies, omdat hun waarden in de loop van de tijd kunnen veranderen, wat resulteert in niet-deterministisch runtime gedrag. | Gebruik constanten of beperk het gebruik van statische variabelen tot activiteit functies. |
| Omgevingsvariabelen | Gebruik geen omgevings variabelen in Orchestrator-functies. Hun waarden kunnen na verloop van tijd veranderen, wat resulteert in niet-deterministisch runtime gedrag. | Naar omgevings variabelen moet alleen worden verwezen vanuit client functies of activiteit functies. |
| Oneindige lussen | Vermijd oneindige lussen in Orchestrator-functies. Omdat het duurzame taak raamwerk uitvoerings geschiedenis opslaat als de Orchestration-functie, kan een oneindige lus ertoe leiden dat er onvoldoende geheugen beschikbaar is voor een Orchestrator-exemplaar. | Gebruik voor oneindige lussen Api's zoals `ContinueAsNew` in .net, `continueAsNew` in Java script of `continue_as_new` in python om de uitvoering van de functie opnieuw te starten en de vorige uitvoerings geschiedenis te verwijderen. |

Hoewel het Toep assen van deze beperkingen mogelijk moeilijk te voor het eerst lijkt, kan het zijn dat ze gemakkelijk te volgen zijn.

Het duurzame taak raamwerk probeert schendingen van de voor gaande regels te detecteren. Als er een schending wordt gevonden, genereert het Framework een **NonDeterministicOrchestrationException** -uitzonde ring. Dit detectie gedrag onderschept echter niet alle schendingen en u moet er niet op vertrouwen.

## <a name="versioning"></a>Versiebeheer

Een duurzame indeling kan continu worden uitgevoerd voor dagen, maanden, jaren of zelfs [eternally](durable-functions-eternal-orchestrations.md). Code-updates die zijn aangebracht in Durable Functions-apps die invloed hebben op onvoltooide indelingen, kunnen het gedrag van het opnieuw afspelen van de Orchestration mogelijk verstoren. Daarom is het belang rijk om zorgvuldig te plannen bij het maken van updates voor code. Zie het [artikel versie nummer](durable-functions-versioning.md)voor een gedetailleerde beschrijving van de versie van uw code.

## <a name="durable-tasks"></a>Duurzame taken

> [!NOTE]
> In deze sectie worden de interne implementatie details van het duurzame taak raamwerk beschreven. U kunt gebruikmaken van duurzame functies zonder dat u deze informatie hoeft te weten. Het is alleen bedoeld om u inzicht te geven in het replay-gedrag.

Taken die veilig kunnen worden gewacht in Orchestrator-functies, worden af en toe aangeduid als *duurzame taken*. Met het duurzame taak raamwerk worden deze taken gemaakt en beheerd. Voor beelden zijn de taken die worden geretourneerd door **CallActivityAsync**, **WaitForExternalEvent** en **CreateTimer** in .net Orchestrator-functies.

Deze duurzame taken worden intern beheerd door een lijst met `TaskCompletionSource` objecten in .net. Tijdens het opnieuw afspelen worden deze taken gemaakt als onderdeel van de uitvoering van de Orchestrator-code. Ze zijn voltooid, omdat de verzender de bijbehorende geschiedenis gebeurtenissen inventariseert.

De taken worden synchroon uitgevoerd met één thread totdat alle geschiedenis is herhaald. Duurzame taken die niet zijn voltooid door het einde van de replay van de geschiedenis, hebben de juiste acties uitgevoerd. Zo kan een bericht in de wachtrij worden geplaatst om een activiteit functie aan te roepen.

In deze sectie vindt u een beschrijving van het runtime gedrag dat u kunt begrijpen waarom een Orchestrator-functie niet kan worden gebruikt `await` of `yield` in een niet-duurzame taak. Er zijn twee redenen: de dispatcher-thread kan niet wachten tot de taak is voltooid en eventuele retour aanroepen door deze taak kunnen de tracerings status van de Orchestrator-functie mogelijk beschadigen. Sommige runtime controles zijn aanwezig om deze schendingen te detecteren.

Raadpleeg de [bron code van de duurzame taak op github](https://github.com/Azure/durabletask)voor meer informatie over hoe het duurzame taak raamwerk Orchestrator-functies uitvoert. Zie met name [TaskOrchestrationExecutor. cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) en [TaskOrchestrationContext. cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het aanroepen van sub-indelingen](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Meer informatie over het verwerken van versie beheer](durable-functions-versioning.md)
