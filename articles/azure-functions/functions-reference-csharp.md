---
title: Naslag informatie voor ontwikkel aars van C#-scripts Azure Functions
description: Meer informatie over het ontwikkelen van Azure Functions met behulp van een C#-script.
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 48614640660da6d85face5ea416d267fa9f59515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92164836"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Naslag informatie voor ontwikkel aars van Azure Functions C#-script (. CSX)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Dit artikel is een inleiding tot het ontwikkelen van Azure Functions met behulp van C# script (*. CSX*).

Azure Functions ondersteunt programmeer talen voor C#-en C#-scripts. Zie [Naslag informatie over c#-ontwikkel aars](functions-dotnet-class-library.md)voor meer informatie over [het gebruik van C# in een Visual Studio Class Library-project](functions-develop-vs.md).

In dit artikel wordt ervan uitgegaan dat u de [hand leiding voor de Azure functions-ontwikkel aars](functions-reference.md)al hebt gelezen.

## <a name="how-csx-works"></a>Hoe. CSX werkt

De C#-script ervaring voor Azure Functions is gebaseerd op de [Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Gegevens stromen naar uw C#-functie via methode argumenten. Argument namen worden opgegeven in een `function.json` bestand en er zijn vooraf gedefinieerde namen voor het verkrijgen van toegang tot zaken als de functie Logboeken en annulerings tokens.

Met de *CSX* -indeling kunt u minder ' standaard ' schrijven en zich richten op het schrijven van een C#-functie. In plaats van alles in een naam ruimte en klasse te laten teruglopen, definieert u een `Run` methode. Neem de volgende assembly-verwijzingen en naam ruimten aan het begin van het bestand op.

De *CSX* -bestanden van een functie-app worden gecompileerd wanneer een exemplaar wordt geïnitialiseerd. Deze compilatie stap betekent dat zaken zoals koude start mogelijk langer duren voor C#-script functies vergeleken met C#-klassen bibliotheken. Deze compilatie stap is ook de reden waarom C#-script functies kunnen worden bewerkt in de Azure Portal, terwijl C# class-bibliotheken niet.

## <a name="folder-structure"></a>Mapstructuur

De mapstructuur voor een C#-script project ziet er als volgt uit:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Er is een gedeelde [host.jsvoor](functions-host-json.md) het bestand dat kan worden gebruikt om de functie-app te configureren. Elke functie heeft een eigen code bestand (. CSX) en een bindings configuratie bestand (function.jsaan).

De bindings uitbreidingen die zijn vereist in [versie 2. x en latere versies](functions-versions.md) van de functions runtime worden gedefinieerd in het `extensions.csproj` bestand, met de daad werkelijke bibliotheek bestanden in de `bin` map. Wanneer u lokaal ontwikkelt, moet u [bindings uitbreidingen registreren](./functions-bindings-register.md#extension-bundles). Bij het ontwikkelen van functies in de Azure Portal, wordt deze registratie voor u uitgevoerd.

## <a name="binding-to-arguments"></a>Binding met argumenten

Invoer-of uitvoer gegevens zijn gebonden aan een C#-script functie parameter via de `name` eigenschap in de *function.js* in het configuratie bestand. In het volgende voor beeld ziet u een *function.js* bestand en *Run. CSX* -bestand voor een functie die door een wachtrij wordt geactiveerd. De para meter die gegevens van het wachtrij bericht ontvangt, krijgt `myQueueItem` de naam, omdat dat de waarde van de `name` eigenschap is.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

De `#r` instructie wordt [verderop in dit artikel](#referencing-external-assemblies)uitgelegd.

## <a name="supported-types-for-bindings"></a>Ondersteunde typen voor bindingen

Elke binding heeft zijn eigen ondersteunde typen. bijvoorbeeld, een BLOB-trigger kan worden gebruikt met een teken reeks parameter, een POCO-para meter, een `CloudBlockBlob` para meter of een van verschillende andere ondersteunde typen. Het [referentie-artikel voor bindingen voor BLOB-bindingen](functions-bindings-storage-blob-trigger.md#usage) bevat een lijst met alle ondersteunde parameter typen voor BLOB-triggers. Zie voor meer informatie [Triggers en bindingen](functions-triggers-bindings.md) en de [verwijzings documenten voor bindingen voor elk bindings type](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Verwijzen naar aangepaste klassen

Als u een aangepaste, verouderde POCO-klasse (CLR object) moet gebruiken, kunt u de klassedefinitie in hetzelfde bestand opnemen of in een afzonderlijk bestand plaatsen.

In het volgende voor beeld ziet u een voor beeld van *Run. CSX* dat een poco-klassedefinitie bevat.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Voor een POCO-klasse moet voor elke eigenschap een getter en setter worden gedefinieerd.

## <a name="reusing-csx-code"></a>CSX code opnieuw gebruiken

U kunt klassen en methoden gebruiken die zijn gedefinieerd in andere *. CSX* -bestanden in het bestand *Run. CSX* . Gebruik hiervoor `#load` de instructies in het bestand *Run. CSX* . In het volgende voor beeld wordt een logboek registratie routine met `MyLogger` de naam gedeeld in *myLogger. CSX* en in *Run. CSX* geladen met de `#load` instructie:

Voor beeld van *Run. CSX*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Voor beeld van *mylogger. CSX*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Het gebruik van een gedeeld *CSX* -bestand is een gemeen schappelijk patroon wanneer u de gegevens die tussen functies worden door gegeven, sterk wilt typen met behulp van een poco-object. In het volgende vereenvoudigde voor beeld wordt met een trigger voor HTTP-triggers en-wacht rijen een POCO-object gedeeld `Order` met de naam, zodat de order gegevens sterk worden getypt:

Voor beeld van *Run. CSX* voor http-trigger:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Voor beeld van *Run. CSX* voor de wachtrij trigger:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Voor beeld van *order. CSX*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

U kunt een relatief pad gebruiken met de `#load` instructie:

* `#load "mylogger.csx"` Hiermee wordt een bestand geladen dat zich in de map function bevindt.
* `#load "loadedfiles\mylogger.csx"` Hiermee wordt een bestand geladen dat zich in een map in de map function bevindt.
* `#load "..\shared\mylogger.csx"` Hiermee wordt een bestand geladen dat zich in een map op hetzelfde niveau bevindt als de map functie, dat zich direct onder *wwwroot* bevindt.

De `#load` -instructie werkt alleen met *. CSX* -bestanden, niet met *. cs* -bestanden.

## <a name="binding-to-method-return-value"></a>Binding met retour waarde van methode

U kunt een methode retour waarde voor een uitvoer binding gebruiken met behulp van de naam `$return` in *function.jsop*. Zie [Triggers en bindingen](./functions-bindings-return-value.md)voor voor beelden.

Gebruik de retour waarde alleen als de uitvoering van een geslaagde functie altijd resulteert in een retour waarde die aan de uitvoer binding moet worden door gegeven. Gebruik `ICollector` of `IAsyncCollector` , zoals wordt weer gegeven in de volgende sectie.

## <a name="writing-multiple-output-values"></a>Meerdere uitvoer waarden schrijven

Als u meerdere waarden naar een uitvoer binding wilt schrijven, of als het aanroepen van een geslaagde functie ertoe kan leiden dat er niets kan worden door gegeven aan de uitvoer binding, gebruikt u de [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typen of. Deze typen zijn alleen-schrijven verzamelingen die naar de uitvoer binding worden geschreven wanneer de methode is voltooid.

In dit voor beeld worden meerdere wachtrij berichten naar dezelfde wachtrij geschreven met behulp van `ICollector` :

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Logboekregistratie

Als u de uitvoer wilt registreren in de streaming-Logboeken in C#, neemt u een argument van het type [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)op. U wordt aangeraden deze naam te noemen `log` . Vermijd het gebruik `Console.Write` van in azure functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> `TraceWriter`Zie de [ILogger](functions-dotnet-class-library.md#ilogger) -documentatie in de .net Class Library-ontwikkelaars handleiding voor meer informatie over een nieuwer Framework voor logboek registratie dat u kunt gebruiken in plaats van.

### <a name="custom-metrics-logging"></a>Logboek registratie voor aangepaste metrische gegevens

U kunt de `LogMetric` uitbreidings methode gebruiken `ILogger` om aangepaste metrische gegevens te maken in Application Insights. Dit is een voor beeld van een methode aanroep:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Deze code is een alternatief voor het aanroepen `TrackMetric` van met behulp van de Application Insights-API voor .net.

## <a name="async"></a>Async

Als u een functie [asynchroon](/dotnet/csharp/programming-guide/concepts/async/)wilt maken, gebruikt u het `async` tref woord en retourneert u een `Task` object.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

U kunt geen `out` para meters gebruiken in async-functies. Voor uitvoer bindingen gebruikt u in plaats daarvan de [functie retour waarde](#binding-to-method-return-value) of een [Collector-object](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Annulerings tokens

Een functie kan een [CancellationToken](/dotnet/api/system.threading.cancellationtoken) -para meter accepteren, waardoor het besturings systeem uw code op de hoogte stelt wanneer de functie wordt beëindigd. U kunt deze melding gebruiken om ervoor te zorgen dat de functie niet onverwacht wordt beëindigd op een manier die gegevens in een inconsistente status laat.

In het volgende voor beeld ziet u hoe u kunt controleren op verdere beëindiging van de functie.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
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
```

## <a name="importing-namespaces"></a>Naam ruimten importeren

Als u naam ruimten wilt importeren, kunt u dit zo gebruikelijk doen met de- `using` component.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

De volgende naam ruimten worden automatisch geïmporteerd en zijn daarom optioneel:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Verwijzen naar externe assembly's

Voeg voor Framework-assembly's verwijzingen toe met behulp van de `#r "AssemblyName"` instructie.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

De volgende assembly's worden automatisch toegevoegd door de Azure Functions hosting omgeving:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Naar de volgende assembly's kan worden verwezen met een eenvoudige naam (bijvoorbeeld `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Verwijzen naar aangepaste assembly's

Als u wilt verwijzen naar een aangepaste assembly, kunt u een *gedeelde* assembly of een *persoonlijke* assembly gebruiken:

* Gedeelde assembly's worden gedeeld met alle functies in een functie-app. Als u wilt verwijzen naar een aangepaste assembly, uploadt u de assembly naar een map met `bin` de naam in de hoofdmap van de [functie-app](functions-reference.md#folder-structure) (wwwroot).

* Persoonlijke assembly's maken deel uit van de context van een bepaalde functie en ondersteunen het laden van verschillende versies. Persoonlijke assembly's moeten worden geüpload in een `bin` map in de functie Directory. Verwijzing naar de assembly's met de bestands naam, zoals `#r "MyAssembly.dll"` .

Zie de sectie over [pakket beheer](#using-nuget-packages)voor informatie over het uploaden van bestanden naar uw functie map.

### <a name="watched-directories"></a>Gevolgde directory's

De map met het functie script bestand wordt automatisch bekeken voor wijzigingen in assembly's. Als u wilt controleren op assembly wijzigingen in andere directory's, voegt u deze toe aan de `watchDirectories` lijst in [host.jsop](functions-host-json.md).

## <a name="using-nuget-packages"></a>NuGet-pakketten gebruiken
Als u NuGet-pakketten wilt gebruiken in een C#-functie van 2. x en hoger, uploadt u een *functie. project* -bestand naar de map van de functie in het bestands systeem van de functie-app. Hier volgt een voor beeld van een *functie. project* -bestand waarmee een verwijzing wordt toegevoegd aan *micro soft. ProjectOxford. Face* versie *1.1.0*:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Als u een aangepaste NuGet-feed wilt gebruiken, geeft u de feed op in een *Nuget.Config* bestand in de hoofdmap van functie-app. Zie [Configuring NuGet Behavior](/nuget/consume-packages/configuring-nuget-behavior)(Engelstalig) voor meer informatie.

> [!NOTE]
> In de 1. x C#-functies wordt naar NuGet-pakketten verwezen met een *project.jsin* plaats van een *functie. proj* -bestand.

Gebruik in plaats daarvan een *project.jsin* het bestand. Hier volgt een voor beeld *project.jsvan* het bestand:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Een bestand function. proj gebruiken

1. Open de functie in de Azure Portal. Op het tabblad Logboeken wordt de uitvoer van de pakket installatie weer gegeven.
2. Gebruik een van de methoden die worden beschreven in de [functie-app-bestanden bijwerken](functions-reference.md#fileupdate) in het onderwerp Azure functions Naslag informatie voor ontwikkel aars voor het uploaden van een *functie. proj* bestand.
3. Nadat het bestand *Function. proj* is geüpload, ziet u uitvoer zoals het volgende voor beeld in het streaming-logboek van uw functie:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Omgevingsvariabelen

Als u een omgevings variabele of een instellings waarde voor een app wilt ophalen, gebruikt u `System.Environment.GetEnvironmentVariable` , zoals wordt weer gegeven in het volgende code voorbeeld:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Binding tijdens runtime

In C# en andere .NET-talen kunt u een [dwingend](https://en.wikipedia.org/wiki/Imperative_programming) bindings patroon gebruiken, in plaats van de [*declaratieve*](https://en.wikipedia.org/wiki/Declarative_programming) bindingen in *function.jsop*. Dwingende binding is handig wanneer bindings parameters tijdens runtime moeten worden berekend in plaats van ontwerp tijd. Met dit patroon kunt u verbinding maken met ondersteunde invoer-en uitvoer bindingen die onderweg zijn in uw functie code.

Definieer als volgt een dwingende binding:

- Neem infunction.js **geen** vermelding *op* voor uw gewenste dwingende bindingen.
- Geef een invoer parameter [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) of op [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Gebruik het volgende C#-patroon om de gegevens binding uit te voeren.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` is het .NET-kenmerk dat uw binding definieert en `T` is een invoer-of uitvoer type dat wordt ondersteund door dat bindings type. `T` kan geen `out` parameter type zijn (zoals `out JObject` ). De Mobile Apps tabel uitvoer binding ondersteunt bijvoorbeeld [zes uitvoer typen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), maar u kunt alleen [ \<T> ICollector](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) of [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) voor gebruiken `T` .

### <a name="single-attribute-example"></a>Voor beeld van één kenmerk

Met de volgende voorbeeld code wordt een [opslag-BLOB-uitvoer binding](functions-bindings-storage-blob-output.md) gemaakt met een BLOB-pad dat tijdens runtime is gedefinieerd, waarna een teken reeks naar de BLOB wordt geschreven.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definieert de invoer-of uitvoer binding van de [opslag-BLOB](functions-bindings-storage-blob.md) en [TextWriter](/dotnet/api/system.io.textwriter) is een ondersteund type uitvoer binding.

### <a name="multiple-attribute-example"></a>Voor beeld van meerdere kenmerken

In het vorige voor beeld wordt de app-instelling voor het hoofd-opslag account van de functie-app opgehaald connection string (dat wil zeggen `AzureWebJobsStorage` ). U kunt een aangepaste app-instelling opgeven die moet worden gebruikt voor het opslag account door de [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) toe te voegen en de kenmerk matrix door te geven aan `BindAsync<T>()` . Gebruik een `Binder` para meter, niet `IBinder` .  Bijvoorbeeld:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

De volgende tabel bevat de .NET-kenmerken voor elk bindings type en de pakketten waarin deze zijn gedefinieerd.

> [!div class="mx-codeBreakAll"]
> | Binding | Kenmerk | Verwijzing toevoegen |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAttributeTests.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Opslagwachtrij | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Opslag-blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Opslag tabel | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Meer informatie over Best Practices for Azure Functions](functions-best-practices.md)
