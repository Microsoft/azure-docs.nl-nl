---
title: 'Azure Functions F # referentie voor ontwikkel aars'
description: 'Meer informatie over het ontwikkelen van Azure Functions met behulp van F # script.'
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87088016"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F # referentie voor ontwikkel aars

F # voor Azure Functions is een oplossing voor het eenvoudig uitvoeren van kleine stukjes code of ' functions ' in de Cloud. Gegevens stromen naar uw F #-functie via functie argumenten. Argument namen worden opgegeven in `function.json` en er zijn vooraf gedefinieerde namen voor het verkrijgen van toegang tot zaken als de functie Logboeken en annulerings tokens. 

>[!IMPORTANT]
>F #-script (. FSX) wordt alleen ondersteund door [versie 1. x](functions-versions.md#creating-1x-apps) van de Azure functions runtime. Als u F # met versie 2. x en latere versies van de runtime wilt gebruiken, moet u een vooraf gecompileerde F # Class Library-project (. FS) gebruiken. U maakt, beheert en publiceert een F # Class Library-project met behulp van Visual Studio, net zoals u een [C# Class Library-project](functions-dotnet-class-library.md)zou doen. Zie [Azure functions runtime versies Overview](functions-versions.md)(Engelstalig) voor meer informatie over functies.

In dit artikel wordt ervan uitgegaan dat u de [Azure functions Naslag informatie voor ontwikkel aars](functions-reference.md)al hebt gelezen.

## <a name="how-fsx-works"></a>Hoe. FSX werkt
Een `.fsx` bestand is een F #-script. Het kan worden beschouwd als een F #-project dat zich in één bestand bevindt. Het bestand bevat de code voor uw programma (in dit geval uw Azure-functie) en de instructies voor het beheren van afhankelijkheden.

Wanneer u een `.fsx` functie voor Azure gebruikt, worden meestal vereiste assembly's automatisch voor u opgenomen, zodat u zich kunt concentreren op de functie in plaats van ' standaard code '.

## <a name="folder-structure"></a>Mapstructuur

De mapstructuur voor een F #-script project ziet er als volgt uit:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Er is een gedeelde [host.jsvoor](functions-host-json.md) het bestand dat kan worden gebruikt om de functie-app te configureren. Elke functie heeft een eigen code bestand (. FSX) en een bindings configuratie bestand (function.jsaan).

De bindings uitbreidingen die zijn vereist in [versie 2. x en latere versies](functions-versions.md) van de functions runtime worden gedefinieerd in het `extensions.csproj` bestand, met de daad werkelijke bibliotheek bestanden in de `bin` map. Wanneer u lokaal ontwikkelt, moet u [bindings uitbreidingen registreren](./functions-bindings-register.md#extension-bundles). Bij het ontwikkelen van functies in de Azure Portal, wordt deze registratie voor u uitgevoerd.

## <a name="binding-to-arguments"></a>Binding met argumenten
Elke binding ondersteunt een aantal argumenten, zoals beschreven in de [Naslag informatie over Azure functions triggers en bindingen](functions-triggers-bindings.md). Een van de argument bindingen die een BLOB-trigger ondersteunt, is bijvoorbeeld een POCO die kan worden weer gegeven met een F #-record. Bijvoorbeeld:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Uw F # Azure-functie voert een of meer argumenten in. Wanneer we over Azure Functions argumenten praten, verwijzen we naar *invoer* argumenten en *uitvoer* argumenten. Een invoer argument is precies wat het klinkt als: invoer naar uw F # Azure-functie. Een *uitvoer* argument is onveranderlijke gegevens of een `byref<>` argument dat fungeert als een manier om gegevens *uit* de functie terug te geven.

In het bovenstaande voor beeld `blob` is een invoer argument en `output` is dit een uitvoer argument. U ziet dat we hebben gebruikt `byref<>` voor `output` (u hoeft de aantekening niet toe te voegen `[<Out>]` ). Als u een type gebruikt, `byref<>` kan de functie de naam wijzigen van de record of het object waarnaar het argument verwijst.

Wanneer een F #-record wordt gebruikt als een invoer type, moet de definitie van de record worden gemarkeerd met om `[<CLIMutable>]` ervoor te zorgen dat het Azure functions Framework de velden op de juiste wijze kan instellen voordat de record aan uw functie wordt door gegeven. Onder de schermen worden `[<CLIMutable>]` setters voor de record eigenschappen gegenereerd. Bijvoorbeeld:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Een F #-klasse kan ook worden gebruikt voor de argumenten in en uit. Voor een klasse heeft eigenschappen meestal getters en setters nodig. Bijvoorbeeld:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Logboekregistratie
Voor het vastleggen van de uitvoer naar uw [streaming-logboeken](../app-service/troubleshoot-diagnostic-logs.md) in F # moet uw functie een argument van het type [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Voor consistentie wordt aangeraden dit argument een naam te krijgen `log` . Bijvoorbeeld:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
De `async` werk stroom kan worden gebruikt, maar het resultaat moet een worden geretourneerd `Task` . Dit kan worden gedaan met `Async.StartAsTask` , bijvoorbeeld:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Annulerings token
Als uw functie afsluiten zonder problemen moet afhandelen, kunt u een [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument opgeven. Dit kan worden gecombineerd met `async` , bijvoorbeeld:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Naam ruimten importeren
Naam ruimten kunnen op de gebruikelijke manier worden geopend:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

De volgende naam ruimten worden automatisch geopend:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Verwijzen naar externe Assembly's
Ook kunnen Framework-assembly-verwijzingen worden toegevoegd met de `#r "AssemblyName"` instructie.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

De volgende assembly's worden automatisch toegevoegd door de Azure Functions hosting omgeving:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Daarnaast zijn de volgende assembly's speciaal hoofdletter gevoelig en kunnen ze worden verwezen door simplenaam (bijvoorbeeld `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Als u wilt verwijzen naar een persoonlijke assembly, kunt u het assembly bestand uploaden naar een `bin` map die relatief is ten opzichte van de functie en ernaar verwijzen met behulp van de bestands naam (bijvoorbeeld  `#r "MyAssembly.dll"`). Zie de volgende sectie over pakket beheer voor informatie over het uploaden van bestanden naar uw functie map.

## <a name="editor-prelude"></a>Editor prelude
Een editor die F # compiler Services ondersteunt, is niet op de hoogte van de naam ruimten en de assembly's die Azure Functions automatisch bevat. Daarom kan het nuttig zijn om een prelude op te stellen waarmee de editor de assembly's kan vinden die u gebruikt, en om expliciet naam ruimten te openen. Bijvoorbeeld:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Wanneer Azure Functions uw code uitvoert, wordt de bron verwerkt met `COMPILED` gedefinieerd, zodat de editor prelude wordt genegeerd.

<a name="package"></a>

## <a name="package-management"></a>Pakketbeheer
Als u NuGet-pakketten wilt gebruiken in een F #-functie, voegt `project.json` u een bestand toe aan de map van de functie in het bestands systeem van de functie-app. Hier volgt een voorbeeld `project.json` bestand waarmee een NuGet-pakket verwijzing wordt toegevoegd aan `Microsoft.ProjectOxford.Face` versie 1.1.0:

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

Alleen de .NET Framework 4,6 wordt ondersteund, dus zorg ervoor dat het `project.json` bestand is opgegeven, `net46` zoals hier wordt weer gegeven.

Wanneer u een bestand uploadt `project.json` , worden de pakketten door de runtime opgehaald en worden automatisch verwijzingen naar de pakket-assembly's toegevoegd. U hoeft geen instructies toe te voegen `#r "AssemblyName"` . Voeg de vereiste `open` instructies toe aan het `.fsx` bestand.

U kunt automatisch verwijzen naar assembly's in uw editor Prelude, om de interactie van uw editor met F # compile-services te verbeteren.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Een `project.json` bestand toevoegen aan uw Azure-functie
1. Begin door te controleren of uw functie-app actief is. u kunt dit doen door de functie te openen in de Azure Portal. Dit biedt ook toegang tot de streaming-logboeken waar de uitvoer van de pakket installatie wordt weer gegeven.
2. Als u een `project.json` bestand wilt uploaden, gebruikt u een van de methoden die worden beschreven in de [functie-app-bestanden bijwerken](functions-reference.md#fileupdate). Als u [doorlopende implementatie voor Azure functions](functions-continuous-deployment.md)gebruikt, kunt u een `project.json` bestand aan uw staging-vertakking toevoegen om het eerst te experimenteren voordat u het toevoegt aan uw implementatie vertakking.
3. Nadat het `project.json` bestand is toegevoegd, ziet u uitvoer die lijkt op het volgende voor beeld in het streaming-logboek van uw functie:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Omgevingsvariabelen
Als u een omgevings variabele of een waarde voor een app-instelling wilt ophalen, gebruikt u `System.Environment.GetEnvironmentVariable` bijvoorbeeld:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>FSX code opnieuw gebruiken
U kunt code uit andere bestanden gebruiken met `.fsx` behulp van een `#load` instructie. Bijvoorbeeld:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Paden naar de `#load` instructie zijn relatief ten opzichte van de locatie van het `.fsx` bestand.

* `#load "logger.fsx"` Hiermee wordt een bestand geladen dat zich in de map function bevindt.
* `#load "package\logger.fsx"` Hiermee wordt een bestand geladen dat zich in de `package` map in de map function bevindt.
* `#load "..\shared\mylogger.fsx"` Hiermee wordt een bestand geladen dat zich in de `shared` map op hetzelfde niveau bevindt als de functie map, direct onder `wwwroot` .

De `#load` instructie werkt alleen met `.fsx` (F # script) bestanden en niet met `.fs` bestanden.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor meer informatie:

* [F# Guide](/dotnet/articles/fsharp/index)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)
* [Azure Functions testen](functions-test-a-function.md)
* [Azure Functions schalen](functions-scale.md)
