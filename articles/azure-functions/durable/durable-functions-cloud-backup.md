---
title: Uitwaaier-en ventilator scenario's in Durable Functions-Azure
description: Meer informatie over het implementeren van een uitgevallen ventilator in de Durable Functions extensie voor Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027640"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Voor beeld van uitwaaier/ventilator in Durable Functions-Cloud-back-up

*Uitwaaieren/ventilatoren* verwijst naar het patroon van het gelijktijdig uitvoeren van meerdere functies en het uitvoeren van enige aggregatie op de resultaten. In dit artikel wordt een voor beeld uitgelegd dat gebruikmaakt van [Durable functions](durable-functions-overview.md) voor het implementeren van een in-en uitwaaierings scenario. Het voor beeld is een duurzame functie waarmee back-ups worden gemaakt van alle of een deel van de site-inhoud van een app in Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

In dit voor beeld worden met de functies alle bestanden onder een opgegeven map recursief naar Blob Storage geüpload. Ze tellen ook het totale aantal bytes dat is geüpload.

Het is mogelijk om één functie te schrijven die van alles zorgt. Het belangrijkste probleem dat u kunt uitvoeren, is **schaal baarheid**. Het uitvoeren van één functie kan alleen worden uitgevoerd op één virtuele machine, zodat de door Voer wordt beperkt door de door Voer van de ene VM. Een ander probleem is de **betrouw baarheid**. Als er een fout is opgetreden in het midden, of als het hele proces meer dan vijf minuten duurt, kan de back-up mislukken met een gedeeltelijk voltooide status. Deze moet vervolgens opnieuw worden gestart.

Een robuustere benadering is het schrijven van twee reguliere functies: een zou de bestanden opsommen en de bestands namen toevoegen aan een wachtrij, en een andere Lees bewerking uit de wachtrij en de bestanden uploaden naar Blob-opslag. Deze aanpak is beter in het kader van de door Voer en betrouw baarheid, maar u moet wel een wachtrij inrichten en beheren. Belang rijker is dat aanzienlijk complexer wordt gemaakt in termen van **status beheer** en **coördinatie** als u iets meer wilt doen, zoals rapport het totale aantal geüploade bytes.

Een Durable Functions aanpak biedt u alle genoemde voor delen met zeer lage overhead.

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app uitgelegd:

* `E2_BackupSiteContent`: Een [Orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) waarmee `E2_GetFileList` een lijst met bestanden wordt opgehaald waarvan een back-up moet worden gemaakt, wordt aangeroepen om een back-up van `E2_CopyFileToBlob` elk bestand te maken.
* `E2_GetFileList`: Een [activiteit functie](durable-functions-bindings.md#activity-trigger) die een lijst met bestanden in een map retourneert.
* `E2_CopyFileToBlob`: Een activiteit functie die een back-up maakt van één bestand naar Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent Orchestrator-functie

Deze Orchestrator-functie doet hoofd zakelijk het volgende:

1. Neemt een `rootDirectory` waarde als invoer parameter.
2. Hiermee wordt een functie aangeroepen om een recursieve lijst met bestanden te verkrijgen onder `rootDirectory` .
3. Maakt meerdere parallelle functie aanroepen om elk bestand te uploaden naar Azure Blob Storage.
4. Er wordt gewacht tot alle uploads zijn voltooid.
5. Retourneert het totale aantal bytes dat is geüpload naar Azure Blob Storage.

# <a name="c"></a>[C#](#tab/csharp)

Dit is de code waarmee de Orchestrator-functie wordt geïmplementeerd:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Let op de `await Task.WhenAll(tasks);` regel. Alle afzonderlijke aanroepen naar de `E2_CopyFileToBlob` functie zijn *niet* in afwachting van een sessie, waardoor ze parallel kunnen worden uitgevoerd. Wanneer we deze matrix van taken door geven aan `Task.WhenAll` , wordt een taak teruggezet die pas wordt voltooid als *alle kopieer bewerkingen zijn voltooid*. Als u bekend bent met de parallelle bibliotheek van de taak (TPL) in .NET, is dit niet nieuw voor u. Het verschil is dat deze taken gelijktijdig kunnen worden uitgevoerd op meerdere virtuele machines, en de uitbrei ding van de Durable Functions zorgt ervoor dat de end-to-end-uitvoering flexibel is voor proces recycling.

Nadat we hebben gewacht `Task.WhenAll` , weet u dat alle functie aanroepen zijn voltooid en waarden hebben geretourneerd naar ons. Elke aanroep voor `E2_CopyFileToBlob` het retour neren van het aantal geüploade bytes, dus het berekenen van het totaal aantal bytes is een kwestie van het toevoegen van alle retour waarden.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

De functie maakt gebruik van de standaard *function.js* voor Orchestrator-functies.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Dit is de code waarmee de Orchestrator-functie wordt geïmplementeerd:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Let op de `yield context.df.Task.all(tasks);` regel. Alle afzonderlijke aanroepen naar de `E2_CopyFileToBlob` functie zijn *niet* opgeleverd, waardoor ze parallel kunnen worden uitgevoerd. Wanneer we deze matrix van taken door geven aan `context.df.Task.all` , wordt een taak teruggezet die pas wordt voltooid als *alle kopieer bewerkingen zijn voltooid*. Als u bekend bent met [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) in Java script, is dit niet nieuw voor u. Het verschil is dat deze taken gelijktijdig kunnen worden uitgevoerd op meerdere virtuele machines, en de uitbrei ding van de Durable Functions zorgt ervoor dat de end-to-end-uitvoering flexibel is voor proces recycling.

> [!NOTE]
> Hoewel taken conceptueel lijken op Java script-belofte, moeten Orchestrator-functies gebruikmaken `context.df.Task.all` `context.df.Task.any` van en in plaats van `Promise.all` en `Promise.race` om taak parallel Lise ring te beheren.

Nadat u hebt verkregen van `context.df.Task.all` , weet u dat alle functie aanroepen zijn voltooid en waarden hebben geretourneerd naar ons. Elke aanroep voor `E2_CopyFileToBlob` het retour neren van het aantal geüploade bytes, dus het berekenen van het totaal aantal bytes is een kwestie van het toevoegen van alle retour waarden.

# <a name="python"></a>[Python](#tab/python)

De functie maakt gebruik van de standaard *function.js* voor Orchestrator-functies.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

Dit is de code waarmee de Orchestrator-functie wordt geïmplementeerd:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

Let op de `yield context.task_all(tasks);` regel. Alle afzonderlijke aanroepen naar de `E2_CopyFileToBlob` functie zijn *niet* opgeleverd, waardoor ze parallel kunnen worden uitgevoerd. Wanneer we deze matrix van taken door geven aan `context.task_all` , wordt een taak teruggezet die pas wordt voltooid als *alle kopieer bewerkingen zijn voltooid*. Als u bekend bent met [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather) in Python, is dit niet nieuw voor u. Het verschil is dat deze taken gelijktijdig kunnen worden uitgevoerd op meerdere virtuele machines, en de uitbrei ding van de Durable Functions zorgt ervoor dat de end-to-end-uitvoering flexibel is voor proces recycling.

> [!NOTE]
> Hoewel taken conceptueel lijken op de python-awaitables, moeten Orchestrator-functies `yield` en-api's worden gebruikt `context.task_all` voor het beheer van `context.task_any` taak parallel Lise ring.

Nadat u hebt verkregen van `context.task_all` , weet u dat alle functie aanroepen zijn voltooid en waarden hebben geretourneerd naar ons. Elke aanroep voor `E2_CopyFileToBlob` het retour neren van het aantal geüploade bytes, zodat we het totale aantal bytes kunnen berekenen door alle retour waarden samen toe te voegen.

---

### <a name="helper-activity-functions"></a>Functies van de Help-activiteit

De functies van de Help-activiteit, net als bij andere voor beelden, zijn alleen normale functies die gebruikmaken van de `activityTrigger` trigger binding.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList-activiteit functie

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Het *function.js* bestand voor `E2_GetFileList` ziet er als volgt uit:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

En dit is de implementatie:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

De functie gebruikt de `readdirp` module (versie 2. x) om de mapstructuur recursief te lezen.

# <a name="python"></a>[Python](#tab/python)

Het *function.js* bestand voor `E2_GetFileList` ziet er als volgt uit:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

En dit is de implementatie:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> U vraagt zich misschien af waarom u deze code niet rechtstreeks in de Orchestrator-functie wilt plaatsen. Dat kan, maar dit zou een van de fundamentele regels van Orchestrator-functies verstoren, dat wil zeggen dat ze nooit I/O moeten, waaronder lokale bestandssysteem toegang. Zie voor meer informatie [Orchestrator functie code beperkingen](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob-activiteit functie

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> U moet het `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet-pakket installeren om de voorbeeld code uit te voeren.

De functie maakt gebruik van een aantal geavanceerde functies van Azure Functions bindingen (dat wil zeggen, het gebruik van de [ `Binder` para meter](../functions-dotnet-class-library.md#binding-at-runtime)), maar u hoeft zich geen zorgen te maken over deze Details voor het doel van deze procedure.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Het *function.js* bestand voor `E2_CopyFileToBlob` is op soort gelijke wijze eenvoudig:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

De Java script-implementatie maakt gebruik [van de Azure Storage SDK voor het knoop punt](https://github.com/Azure/azure-storage-node) om de bestanden te uploaden naar Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

Het *function.js* bestand voor `E2_CopyFileToBlob` is op soort gelijke wijze eenvoudig:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

De python-implementatie maakt gebruik [van de Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python) voor het uploaden van de bestanden naar Azure Blob Storage.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

Met de implementatie wordt het bestand van de schijf geladen en wordt de inhoud asynchroon naar een blob met dezelfde naam in de container back-ups gestreamd. De geretourneerde waarde is het aantal bytes dat wordt gekopieerd naar de opslag, dat vervolgens wordt gebruikt door de Orchestrator-functie om de cumulatieve som te berekenen.

> [!NOTE]
> Dit is een perfecte voor beeld van het verplaatsen van I/O-bewerkingen in een `activityTrigger` functie. Niet alleen kan het werk worden verdeeld over verschillende machines, maar u krijgt ook de voor delen van het controle punt van de voortgang. Als het hostproces om welke reden dan ook wordt beëindigd, weet u dat er al een upload bewerking is voltooid.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U kunt de indeling in Windows starten door de volgende HTTP POST-aanvraag te verzenden.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

Daarnaast kunt u op een Linux-functie-app (python die momenteel alleen op Linux wordt uitgevoerd voor App Service) de indeling als volgt starten:

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> De `HttpStart` functie die u aanroept, werkt alleen met inhoud in JSON-indeling. Daarom `Content-Type: application/json` is de header vereist en wordt het mappad gecodeerd als een JSON-teken reeks. Daarnaast wordt ervan uitgegaan dat er een vermelding in het `host.json` bestand staat waarmee het standaard `api/` voorvoegsel van alle http-trigger functies url's wordt verwijderd. U kunt de opmaak voor deze configuratie vinden in het `host.json` bestand in de voor beelden.

Met deze HTTP-aanvraag wordt de `E2_BackupSiteContent` Orchestrator geactiveerd en wordt de teken reeks door gegeven `D:\home\LogFiles` als para meter. Het antwoord bevat een koppeling om de status van de back-upbewerking op te halen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Afhankelijk van het aantal logboek bestanden dat u in uw functie-app hebt, kan het enkele minuten duren voordat deze bewerking is voltooid. U kunt de meest recente status verkrijgen door een query uit te geven op de URL in de `Location` kop van het vorige HTTP 202-antwoord.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

In dit geval wordt de functie nog steeds uitgevoerd. U kunt de invoer zien die is opgeslagen in de Orchestrator-status en het tijdstip waarop de gegevens voor het laatst zijn bijgewerkt. U kunt de `Location` header waarden blijven gebruiken om te controleren op voltooiing. Wanneer de status voltooid is, ziet u een HTTP-antwoord waarde die lijkt op het volgende:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Nu kunt u zien dat de indeling is voltooid en ongeveer de hoeveelheid tijd die nodig is om te volt ooien. U ziet ook een waarde voor het `output` veld, waarmee wordt aangegeven dat er ongeveer 450 kB aan logboeken is geüpload.

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld wordt aangegeven hoe u het patroon uitwaaieren/ventilatoren implementeert. In het volgende voor beeld ziet u hoe u het monitor patroon kunt implementeren met behulp van [duurzame timers](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [De monitor-voor beeld uitvoeren](durable-functions-monitor.md)