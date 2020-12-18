---
title: Naslaginformatie over Azure Functions C# voor ontwikkelaars
description: Meer informatie over het ontwikkelen van Azure Functions met C#.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: 9e11d013b6e7473f290ba1ccb54857034491d116
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672662"
---
# <a name="azure-functions-c-developer-reference"></a>Naslaginformatie over Azure Functions C# voor ontwikkelaars

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Dit artikel is een inleiding tot het ontwikkelen van Azure Functions door C# te gebruiken in .NET-klassen bibliotheken.

Als C#-ontwikkelaar bent u mogelijk ook geïnteresseerd in een van de volgende artikelen:

| Aan de slag | Concepten| Begeleide training/voor beelden |
| -- | -- | -- | 
| <ul><li>[Visual Studio gebruiken](functions-create-your-first-function-visual-studio.md)</li><li>[Visual Studio Code gebruiken](create-first-function-vs-code-csharp.md)</li><li>[Opdracht regel Programma's gebruiken](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[Hostingopties](functions-scale.md)</li><li>[Prestatie &nbsp; overwegingen](functions-best-practices.md)</li><li>[Ontwikkelen in Visual Studio](functions-develop-vs.md)</li><li>[Afhankelijkheidsinjectie](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[Serverloze toepassingen maken](/learn/paths/create-serverless-applications/)</li><li>[C#-voorbeelden](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Functions ondersteunt programmeer talen voor C#-en C#-scripts. Zie [Naslag informatie over c#-script (. CSX) voor ontwikkel aars](functions-reference-csharp.md)voor meer informatie over [het gebruik van c# in de Azure Portal](functions-create-function-app-portal.md).

## <a name="supported-versions"></a>Ondersteunde versies

Versies van de functions-runtime werken met specifieke versies van .NET. De volgende tabel toont het hoogste niveau van .NET core en .NET Framework en .NET core dat kan worden gebruikt met een specifieke versie van functies in uw project. 

| Runtime versie van functions | Maximum versie van .NET |
| ---- | ---- |
| Functies 3. x | .NET Core 3.1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4,7 |

Zie [Azure functions overzicht van runtime versies](functions-versions.md) voor meer informatie

## <a name="functions-class-library-project"></a>Class-bibliotheek project voor functies

In Visual Studio maakt de sjabloon **Azure functions** project een C#-klassen bibliotheek project die de volgende bestanden bevat:

* [host.json](functions-host-json.md) -slaat configuratie-instellingen die van invloed zijn op alle functies in het project wanneer deze lokaal of in Azure worden uitgevoerd.
* [local.settings.json](functions-run-local.md#local-settings-file) -Stores-app-instellingen en verbindings reeksen die worden gebruikt bij het lokaal uitvoeren. Dit bestand bevat geheimen en wordt niet gepubliceerd in uw functie-app in Azure. Voeg in plaats daarvan [app-instellingen toe aan de functie-app](functions-develop-vs.md#function-app-settings).

Wanneer u het project bouwt, wordt een mapstructuur die eruitziet als in het volgende voor beeld gegenereerd in de map build Uitvoermap:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Deze map wordt geïmplementeerd in uw functie-app in Azure. De binding-uitbrei dingen die zijn vereist in [versie 2. x](functions-versions.md) van de functions-runtime, worden [toegevoegd aan het project als NuGet-pakketten](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Het bouw proces maakt een *function.jsin* het bestand voor elke functie. Deze *function.jsin* het bestand is niet bedoeld om rechtstreeks te worden bewerkt. U kunt de bindings configuratie niet wijzigen of de functie uitschakelen door dit bestand te bewerken. Zie [functies uitschakelen](disable-function.md)voor meer informatie over het uitschakelen van een functie.


## <a name="methods-recognized-as-functions"></a>Methoden die worden herkend als functions

In een klassen bibliotheek is een functie een statische methode met een `FunctionName` en een trigger-kenmerk, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Het `FunctionName` kenmerk markeert de methode als een functie-ingangs punt. De naam moet uniek zijn binnen een project, beginnen met een letter en mag alleen letters, cijfers, `_` en `-` tot 127 tekens lang zijn. Project sjablonen maken vaak een methode met `Run` de naam, maar de naam van de methode kan elke geldige naam van een C#-methode zijn.

Het trigger kenmerk geeft het trigger type aan en bindt invoer gegevens aan een methode parameter. De functie voor beeld wordt geactiveerd door een wachtrij bericht en het wachtrij bericht wordt door gegeven aan de methode in de `myQueueItem` para meter.

## <a name="method-signature-parameters"></a>Methode handtekening parameters

De methode handtekening kan andere para meters bevatten dan de hand tekening die is gebruikt met het trigger kenmerk. Hier volgen enkele van de aanvullende para meters die u kunt gebruiken:

* [Invoer-en uitvoer bindingen](functions-triggers-bindings.md) die als zodanig zijn gemarkeerd door ze te verduidelijken met kenmerken.  
* Een `ILogger` of `TraceWriter` ([versie 1. x](functions-versions.md#creating-1x-apps)) para meter voor [logboek registratie](#logging).
* Een `CancellationToken` para meter voor een [correcte afsluit](#cancellation-tokens)actie.
* [Bindings expressies](./functions-bindings-expressions-patterns.md) para meters voor het ophalen van meta gegevens van triggers.

De volg orde van de para meters in de functie handtekening is hierbij niet van belang. U kunt bijvoorbeeld trigger parameters plaatsen voor of na andere bindingen en u kunt de para meter logger voor of na de trigger-of bindings parameters plaatsen.

### <a name="output-binding-example"></a>Voor beeld van uitvoer binding

In het volgende voor beeld wordt de vorige wijziging aangebracht door een uitvoer wachtrij binding toe te voegen. Met de functie wordt het wachtrij bericht geschreven dat de functie activeert naar een nieuw wachtrij bericht in een andere wachtrij.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

De bindende referentie artikelen ([opslag wachtrijen](functions-bindings-storage-queue.md), bijvoorbeeld) geven aan welke parameter typen u kunt gebruiken met de bindings kenmerken trigger, invoer of uitvoer.

### <a name="binding-expressions-example"></a>Voor beeld van bindings expressies

Met de volgende code wordt de naam van de wachtrij opgehaald die moet worden bewaakt vanuit een app-instelling en wordt de aanmaak tijd van het wachtrij bericht opgehaald in de `insertionTime` para meter.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatisch gegenereerde function.jsop

Met het bouw proces maakt u een *function.jsvoor* een bestand in een functie map in de map build. Zoals eerder is vermeld, is dit bestand niet bedoeld om rechtstreeks te worden bewerkt. U kunt de bindings configuratie niet wijzigen of de functie uitschakelen door dit bestand te bewerken. 

Het doel van dit bestand is om informatie aan de schaal controller te verstrekken die moet worden gebruikt voor [het schalen van beslissingen over het verbruiks abonnement](functions-scale.md#how-the-consumption-and-premium-plans-work). Daarom heeft het bestand alleen trigger gegevens, geen invoer-of uitvoer bindingen.

De gegenereerde *function.jsvoor* het bestand bevat een `configurationSource` eigenschap die aangeeft dat de runtime .net-kenmerken voor bindingen moet gebruiken in plaats van *function.jsop* configuratie. Hier volgt een voorbeeld:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Micro soft. NET. SDK. functions

De *function.jsvoor* het genereren van bestanden wordt uitgevoerd door de [micro soft \. net \. SDK- \. functies](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)van het NuGet-pakket. 

Hetzelfde pakket wordt gebruikt voor versie 1. x en 2. x van de functions-runtime. Het doel raamwerk is een onderscheid tussen een 1. x-project en een 2. x-project. Hier vindt u de relevante delen van *. csproj* -bestanden, met verschillende doel raamwerken en hetzelfde `Sdk` pakket:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


Onder de `Sdk` pakket afhankelijkheden zijn triggers en bindingen. Een 1. x-project verwijst naar 1. x-triggers en-bindingen, omdat de triggers en bindingen het .NET Framework doel hebben, terwijl 2. x-triggers en-bindingen .NET core hebben.

Het `Sdk` pakket is ook afhankelijk van [Newtonsoft.Jsop](https://www.nuget.org/packages/Newtonsoft.Json)en indirect op [WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Deze afhankelijkheden zorgen ervoor dat uw project gebruikmaakt van de versies van de pakketten die werken met de runtime versie van functions die het project doel heeft. `Newtonsoft.Json`Heeft bijvoorbeeld versie 11 voor .NET Framework 4.6.1, maar de functions-runtime die als doel heeft .NET Framework 4.6.1 is alleen compatibel met `Newtonsoft.Json` 9.0.1. De functie code in dat project moet dus ook 9.0.1 gebruiken `Newtonsoft.Json` .

De bron code voor `Microsoft.NET.Sdk.Functions` is beschikbaar in de Azure functions van github opslag plaats en [ \- \- \- Build \- SDK](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Runtime versie

Visual Studio maakt gebruik van de [Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools) om functies projecten uit te voeren. De belangrijkste Hulpprogram Ma's is een opdracht regel interface voor de functions-runtime.

Als u de basis Hulpprogramma's installeert met behulp van NPM, is dit niet van invloed op de kern Hulpprogramma's versie die wordt gebruikt door Visual Studio. Voor de functions runtime versie 1. x slaat Visual Studio versies van kern Hulpprogramma's op in *%userprofile%\AppData\Local\Azure.functions.cli* en maakt gebruik van de meest recente versie die daar is opgeslagen. Voor functies 2. x zijn de kern Hulpprogramma's opgenomen in de uitbrei ding **Azure functions en webjobs** . Voor beide 1. x en 2. x kunt u zien welke versie wordt gebruikt in de console-uitvoer wanneer u een functions-project uitvoert:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

U kunt de functie-app compileren als [binaire ReadyToRun](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images). ReadyToRun is een vorm van een vooraf te compileren compilatie waarmee de opstart prestaties kunnen worden verbeterd om de impact van [koude-start](functions-scale.md#cold-start) bij het uitvoeren van een [verbruiks abonnement](functions-scale.md#consumption-plan)te verminderen.

ReadyToRun is beschikbaar in .NET 3,0 en vereist [versie 3,0 van de Azure functions runtime](functions-versions.md).

Als u uw project wilt compileren als ReadyToRun, werkt u het project bestand bij door de elementen en toe te voegen `<PublishReadyToRun>` `<RuntimeIdentifier>` . Hier volgt de configuratie voor het publiceren naar een Windows 32-bits functie-app.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun biedt momenteel geen ondersteuning voor cross-compilatie. U moet uw app bouwen op hetzelfde platform als het implementatie doel. Let ook op de ' Bitness ' die is geconfigureerd in uw functie-app. Als uw functie-app in azure bijvoorbeeld Windows 64-bits is, moet u uw app met `win-x64` als [runtime-id](/dotnet/core/rid-catalog)compileren in Windows.

U kunt uw app ook bouwen met ReadyToRun vanaf de opdracht regel. Zie de optie in voor meer informatie `-p:PublishReadyToRun=true` [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>Ondersteunde typen voor bindingen

Elke binding heeft zijn eigen ondersteunde typen. een BLOB-trigger kenmerk kan bijvoorbeeld worden toegepast op een teken reeks parameter, een POCO-para meter, een `CloudBlockBlob` para meter of een of meer andere ondersteunde typen. Het [referentie-artikel voor bindingen voor BLOB-bindingen](functions-bindings-storage-blob-trigger.md#usage) bevat een lijst met alle ondersteunde parameter typen. Zie voor meer informatie [Triggers en bindingen](functions-triggers-bindings.md) en de [verwijzings documenten voor bindingen voor elk bindings type](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Binding met retour waarde van methode

U kunt de retour waarde van een methode voor een uitvoer binding gebruiken door het kenmerk toe te passen op de retour waarde van de methode. Zie [Triggers en bindingen](./functions-bindings-return-value.md)voor voor beelden. 

Gebruik de retour waarde alleen als de uitvoering van een geslaagde functie altijd resulteert in een retour waarde die aan de uitvoer binding moet worden door gegeven. Gebruik `ICollector` of `IAsyncCollector` , zoals wordt weer gegeven in de volgende sectie.

## <a name="writing-multiple-output-values"></a>Meerdere uitvoer waarden schrijven

Als u meerdere waarden naar een uitvoer binding wilt schrijven, of als het aanroepen van een geslaagde functie ertoe kan leiden dat er niets kan worden door gegeven aan de uitvoer binding, gebruikt u de [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typen of. Deze typen zijn alleen-schrijven verzamelingen die naar de uitvoer binding worden geschreven wanneer de methode is voltooid.

In dit voor beeld worden meerdere wachtrij berichten naar dezelfde wachtrij geschreven met behulp van `ICollector` :

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="async"></a>Async

Als u een functie [asynchroon](/dotnet/csharp/programming-guide/concepts/async/)wilt maken, gebruikt u het `async` tref woord en retourneert u een `Task` object.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

U kunt geen `out` para meters gebruiken in async-functies. Voor uitvoer bindingen gebruikt u in plaats daarvan de [functie retour waarde](#binding-to-method-return-value) of een [Collector-object](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Annulerings tokens

Een functie kan een [CancellationToken](/dotnet/api/system.threading.cancellationtoken) -para meter accepteren, waardoor het besturings systeem uw code op de hoogte stelt wanneer de functie wordt beëindigd. U kunt deze melding gebruiken om ervoor te zorgen dat de functie niet onverwacht wordt beëindigd op een manier die gegevens in een inconsistente status laat.

In het volgende voor beeld ziet u hoe u kunt controleren op verdere beëindiging van de functie.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="logging"></a>Logboekregistratie

In de functie code kunt u uitvoer schrijven naar logboeken die als traceringen worden weer gegeven in Application Insights. De aanbevolen manier om naar de logboeken te schrijven, is door een para meter van het type [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)op te neemt. Dit is meestal een naam `log` . Versie 1. x van de functions runtime gebruikt `TraceWriter` , die ook naar Application Insights schrijft, maar geen ondersteuning biedt voor gestructureerde logboek registratie. Gebruik `Console.Write` deze niet om uw logboeken te schrijven, omdat deze gegevens niet worden vastgelegd door Application Insights. 

### <a name="ilogger"></a>ILogger

Neem in de functie definitie een [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) -para meter op die [gestructureerde logboek registratie](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)ondersteunt.

Met een `ILogger` -object roept u `Log<level>` [uitbreidings methoden aan op ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) om logboeken te maken. Met de volgende code worden `Information` Logboeken geschreven met de categorie `Function.<YOUR_FUNCTION_NAME>.User.` :

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

`ILogger`Zie [telemetrie-gegevens verzamelen](functions-monitoring.md#collecting-telemetry-data)voor meer informatie over hoe functies worden geïmplementeerd. Er `Function` wordt ervan uitgegaan dat u een `ILogger` instantie gebruikt. Als u in plaats daarvan een wilt gebruiken `ILogger<T>` , is de naam van de categorie mogelijk op basis van `T` .  

### <a name="structured-logging"></a>Gestructureerde logboekregistratie

De volg orde van tijdelijke aanduidingen, niet de namen, bepaalt welke para meters worden gebruikt in het logboek bericht. Stel dat u de volgende code hebt:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Als u dezelfde bericht teken reeks behoudt en de volg orde van de para meters omkeert, zouden de resulterende bericht tekst de waarden op de verkeerde plaatsen zouden hebben.

Tijdelijke aanduidingen worden op deze manier verwerkt, zodat u gestructureerde logboek registratie kunt uitvoeren. Application Insights slaat de parameter naam/waarde-paren en de bericht teken reeks op. Het resultaat is dat de bericht argumenten worden velden waarop u kunt zoeken.

Als de aanroep van de traceer methode lijkt op het vorige voor beeld, kunt u het veld opvragen `customDimensions.prop__rowKey` . Het `prop__` voor voegsel wordt toegevoegd om ervoor te zorgen dat er geen conflicten zijn tussen velden die door de runtime worden toegevoegd en de velden die door de functie code worden toegevoegd.

U kunt ook een query uitvoeren op de oorspronkelijke bericht teken reeks door te verwijzen naar het veld `customDimensions.prop__{OriginalFormat}` .  

Hier volgt een voor beeld van een JSON-weer gave van `customDimensions` gegevens:

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

## <a name="log-custom-telemetry-in-c-functions"></a>Aangepaste telemetrie voor logboeken in C#-functies

Er is een functie-specifieke versie van de Application Insights SDK waarmee u aangepaste telemetriegegevens van uw functies kunt verzenden naar Application Insights: [micro soft. Azure. webjobs. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Gebruik de volgende opdracht vanaf de opdracht prompt om dit pakket te installeren:

# <a name="command"></a>[Opdracht](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Vervang in deze opdracht door `<VERSION>` een versie van dit pakket die de geïnstalleerde versie van [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)ondersteunt. 

In de volgende C#-voor beelden wordt de [aangepaste telemetrie-API](../azure-monitor/app/api-custom-events-metrics.md)gebruikt. Het voor beeld is voor een .NET-klassebibliotheek, maar de Application Insights code is hetzelfde voor het C#-script.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Versie 2. x en latere versies van de runtime gebruiken nieuwere functies in Application Insights om telemetrie automatisch te correleren met de huidige bewerking. U hoeft de bewerking `Id` , of velden, niet hand matig in te stellen `ParentId` `Name` .

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

In dit voor beeld worden de aangepaste metrische gegevens geaggregeerd door de host voordat ze worden verzonden naar de tabel customMetrics. Zie de [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) -documentatie in Application Insights voor meer informatie. 

Wanneer u lokaal uitvoert, moet u de `APPINSIGHTS_INSTRUMENTATIONKEY` instelling, met de Application Insights sleutel, toevoegen aan de [local.settings.jsin](functions-run-local.md#local-settings-file) het bestand.


# <a name="v1x"></a>[v1. x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

`TrackRequest`U kunt niet bellen of `StartOperation<RequestTelemetry>` omdat er dubbele aanvragen voor een functie aanroep worden weer geven.  Met de functie runtime worden aanvragen automatisch bijgehouden.

Niet instellen `telemetryClient.Context.Operation.Id` . Deze globale instelling veroorzaakt een onjuiste correlatie wanneer er meerdere functies tegelijkertijd worden uitgevoerd. Maak in plaats daarvan een nieuwe telemetrie-instantie ( `DependencyTelemetry` , `EventTelemetry` ) en wijzig de bijbehorende `Context` eigenschap. Geef vervolgens de telemetrie-instantie door aan de overeenkomstige `Track` methode op `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` , `TrackMetric()` ). Deze methode zorgt ervoor dat de telemetrie de juiste correlatie details heeft voor de huidige functie aanroep.


## <a name="environment-variables"></a>Omgevingsvariabelen

Als u een omgevings variabele of een instellings waarde voor een app wilt ophalen, gebruikt u `System.Environment.GetEnvironmentVariable` , zoals wordt weer gegeven in het volgende code voorbeeld:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

App-instellingen kunnen worden gelezen van omgevings variabelen bij het lokaal ontwikkelen en wanneer ze worden uitgevoerd in Azure. Wanneer u lokaal ontwikkelt, worden de app-instellingen opgehaald uit de `Values` verzameling in de *local.settings.jsvoor* het bestand. In beide omgevingen, lokaal en Azure, wordt `GetEnvironmentVariable("<app setting name>")` de waarde van de app-instelling met de naam opgehaald. Als u bijvoorbeeld lokaal uitvoert, wordt ' mijn site naam ' geretourneerd als uw *local.settings.jsin* het bestand bevat `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` .

De eigenschap [System.Configuration.ConfigurationManager. AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) is een alternatieve API voor het ophalen van waarden voor app-instellingen, maar we raden u aan om te gebruiken `GetEnvironmentVariable` zoals hier wordt weer gegeven.

## <a name="binding-at-runtime"></a>Binding tijdens runtime

In C# en andere .NET-talen kunt u een [dwingend](https://en.wikipedia.org/wiki/Imperative_programming) bindings patroon gebruiken, in plaats van de [*declaratieve*](https://en.wikipedia.org/wiki/Declarative_programming) bindingen in kenmerken. Dwingende binding is handig wanneer bindings parameters tijdens runtime moeten worden berekend in plaats van ontwerp tijd. Met dit patroon kunt u verbinding maken met ondersteunde invoer-en uitvoer bindingen die onderweg zijn in uw functie code.

Definieer als volgt een dwingende binding:

- Neem in de hand tekening van de functie **geen** kenmerk op voor de gewenste dwingende bindingen.
- Geef een invoer parameter [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) of op [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Gebruik het volgende C#-patroon om de gegevens binding uit te voeren.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` is het .NET-kenmerk dat uw binding definieert en `T` is een invoer-of uitvoer type dat wordt ondersteund door dat bindings type. `T` kan geen `out` parameter type zijn (zoals `out JObject` ). De Mobile Apps tabel uitvoer binding ondersteunt bijvoorbeeld [zes uitvoer typen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), maar u kunt alleen [ \<T> ICollector](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) of [IAsyncCollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) gebruiken met dwingende binding.

### <a name="single-attribute-example"></a>Voor beeld van één kenmerk

Met de volgende voorbeeld code wordt een [opslag-BLOB-uitvoer binding](functions-bindings-storage-blob-output.md) gemaakt met een BLOB-pad dat tijdens runtime is gedefinieerd, waarna een teken reeks naar de BLOB wordt geschreven.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definieert de invoer-of uitvoer binding van de [opslag-BLOB](functions-bindings-storage-blob.md) en [TextWriter](/dotnet/api/system.io.textwriter) is een ondersteund type uitvoer binding.

### <a name="multiple-attribute-example"></a>Voor beeld van meerdere kenmerken

In het vorige voor beeld wordt de app-instelling voor het hoofd-opslag account van de functie-app opgehaald connection string (dat wil zeggen `AzureWebJobsStorage` ). U kunt een aangepaste app-instelling opgeven die moet worden gebruikt voor het opslag account door de [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) toe te voegen en de kenmerk matrix door te geven aan `BindAsync<T>()` . Gebruik een `Binder` para meter, niet `IBinder` .  Bijvoorbeeld:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Triggers en bindingen 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Meer informatie over Best Practices for Azure Functions](functions-best-practices.md)
