---
title: Hand leiding voor het geïsoleerde .NET-proces voor .NET 5,0 in Azure Functions
description: Meer informatie over het gebruik van een door .NET geïsoleerd proces om uw C#-functies uit te voeren op .NET 5,0 out-of-process in Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 5ee38fa4b005cf053890c223dfec9244c637bd00
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561818"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Hand leiding voor het uitvoeren van functies op .NET 5,0 in azure

Dit artikel is een inleiding tot het gebruik van C# voor het ontwikkelen van .NET geïsoleerde proces functies, die out-of-process worden uitgevoerd in Azure Functions. Als u out-of-process uitvoert, kunt u uw functie code loskoppelen van de Azure Functions runtime. Het biedt ook een manier om functies te maken en uit te voeren die gericht zijn op de huidige versie van .NET 5,0. 

| Aan de slag | Concepten| Voorbeelden |
|--|--|--| 
| <ul><li>[Visual Studio Code gebruiken](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Opdracht regel Programma's gebruiken](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Visual Studio gebruiken](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Hostingopties](functions-scale.md)</li><li>[Controle](functions-monitoring.md)</li> | <ul><li>[Referentie voorbeelden](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Als u .NET 5,0 niet meer nodig hebt of als u uw functions out-of-process wilt uitvoeren, kunt u beter in plaats daarvan [C#-Class-bibliotheek functies te ontwikkelen](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Waarom is het proces van .NET geïsoleerd?

Voorheen heeft Azure Functions alleen een nauw geïntegreerde modus ondersteund voor .NET-functies, die [als een klassen bibliotheek](functions-dotnet-class-library.md) worden uitgevoerd in hetzelfde proces als de host. Deze modus biedt een diep gaande integratie tussen het hostproces en de functies. Zo kunnen bijvoorbeeld functies van de .NET-klassen bibliotheek binding-Api's en-typen delen. Voor deze integratie is echter ook een nauw keurigere koppeling tussen het hostproces en de .NET-functie vereist. .NET-functies die in-process worden uitgevoerd, moeten bijvoorbeeld worden uitgevoerd op dezelfde versie van .NET als de functions-runtime. U kunt er nu voor kiezen om in een geïsoleerd proces uit te voeren. Met deze proces isolatie kunt u ook functies ontwikkelen die gebruikmaken van huidige .NET-releases (zoals .NET 5,0), die niet systeem eigen worden ondersteund door de functions-runtime.

Omdat deze functies in een afzonderlijk proces worden uitgevoerd, zijn er enkele functies [en functionaliteits verschillen](#differences-with-net-class-library-functions) tussen .net geïsoleerde functie-apps en de .net Class Library-functie-apps.

### <a name="benefits-of-running-out-of-process"></a>Voor delen van out-of-process

Bij het uitvoeren van out-of-process kunnen uw .NET-functies profiteren van de volgende voor delen: 

+ Minder conflicten: omdat de functies in een afzonderlijk proces worden uitgevoerd, conflicteren de assembly's die in uw app worden gebruikt niet met een andere versie van dezelfde assembly die wordt gebruikt door het hostproces.  
+ Volledig beheer van het proces: u bepaalt het opstarten van de app en kan bepalen welke configuraties worden gebruikt en of de middleware is gestart.
+ Afhankelijkheids injectie: omdat u de volledige controle over het proces hebt, kunt u het huidige .NET-gedrag voor het invoegen van afhankelijkheden gebruiken en middleware opnemen in uw functie-app. 

## <a name="supported-versions"></a>Ondersteunde versies

De enige versie van .NET die momenteel wordt ondersteund om out-of-process uit te voeren, is .NET 5,0.

## <a name="net-isolated-project"></a>.NET geïsoleerd project

Een .NET geïsoleerd function-project is in principe een .NET-console-app-project die is gericht op .NET 5,0. Hieronder vindt u de basis bestanden die vereist zijn in elk van de in .NET geïsoleerde projecten:

+ [host.jsop](functions-host-json.md) bestand.
+ [local.settings.jsop](functions-run-local.md#local-settings-file) bestand.
+ C#-project bestand (. csproj) dat het project en de afhankelijkheden definieert.
+ Program.cs-bestand dat het toegangs punt voor de app is.

## <a name="package-references"></a>Pakket verwijzingen

Bij het uitvoeren van out-of-process maakt uw .NET-project gebruik van een unieke set pakketten, waarmee zowel de kern functionaliteit als de binding-extensies worden geïmplementeerd. 

### <a name="core-packages"></a>Kern pakketten 

De volgende pakketten zijn vereist om uw .NET-functies in een geïsoleerd proces uit te voeren:

+ [Micro soft. Azure. functions. worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Micro soft. Azure. functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Uitbreidings pakketten

Omdat functies die worden uitgevoerd in een door .NET geïsoleerd proces verschillende bindings typen gebruiken, is een unieke set van de pakketten met bindings extensies vereist. 

U vindt deze extensie pakketten onder [micro soft. Azure. functions. Worker. Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Opstarten en configureren 

Wanneer u met behulp van .NET geïsoleerde functies hebt, hebt u toegang tot het starten van uw functie-app, meestal in Program.cs. U bent zelf verantwoordelijk voor het maken en starten van uw eigen exemplaar van de host. Als zodanig hebt u ook directe toegang tot de configuratie pijplijn voor uw app. U kunt veel gemakkelijker afhankelijkheden injecteren en middleware uitvoeren wanneer u out-of-process uitvoert. 

De volgende code toont een voor beeld van een `HostBuilder` pijp lijn:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

A `HostBuilder` wordt gebruikt voor het bouwen en retour neren van een volledig geïnitialiseerd `IHost` exemplaar dat u asynchroon uitvoert om uw functie-app te starten. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configuratie

Als u toegang hebt tot de pijp lijn van de host Builder, kunt u tijdens de initialisatie alle app-specifieke configuraties instellen. Deze configuraties zijn van toepassing op de functie-app die wordt uitgevoerd in een afzonderlijk proces. Als u wijzigingen wilt aanbrengen in de functies host of trigger-en bindings configuratie, moet u de [host.jsin het bestand](functions-host-json.md)gebruiken.      

<!--The following example shows how to add configuration `args`, which are read as command-line arguments: 
 
:::code language="csharp" 
                .ConfigureAppConfiguration(c =>
                {
                    c.AddCommandLine(args);
                })
                :::

The `ConfigureAppConfiguration` method is used to configure the rest of the build process and application. This example also uses an [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true), which makes it easier to add multiple configuration items. Because `ConfigureAppConfiguration` returns the same instance of [`IConfiguration`](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true), you can also just call it multiple times to add multiple configuration items.-->  
U kunt toegang krijgen tot de volledige set configuraties van zowel [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) en [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

Zie [configuratie in ASP.net core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)voor meer informatie over configuratie. 

### <a name="dependency-injection"></a>Afhankelijkheidsinjectie

Het invoegen van afhankelijkheden is vereenvoudigd, vergeleken met .NET-klassen bibliotheken. In plaats van een opstart klasse te maken voor het registreren van services, hoeft u alleen `ConfigureServices` op de host Builder aan te roepen en de uitbreidings methoden te gebruiken voor het [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) injecteren van specifieke services. 

In het volgende voor beeld wordt een singleton-service afhankelijkheid geïnjecteerd:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Zie [afhankelijkheids injectie in ASP.net core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)voor meer informatie.

<!--### Middleware

.NET isolated also supports middleware registration, again by using a model similar to what exists in ASP.NET. This model gives you the ability to inject logic into the invocation pipeline, and before and after functions execute.

While the full middleware registration set of APIs is not yet exposed, we do support middleware registration and have added an example to the sample application under the Middleware folder.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_middleware" :::-->

## <a name="execution-context"></a>Context voor uitvoering

Met .NET geïsoleerd wordt een object door gegeven `FunctionContext` aan uw functie methoden. Met dit object kunt u een [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) instantie ophalen voor het schrijven naar de logboeken door de methode aan te roepen `GetLogger` en een teken reeks op te geven `categoryName` . Zie [logboek registratie](#logging)voor meer informatie. 

## <a name="bindings"></a>Bindingen 

Bindingen worden gedefinieerd door gebruik te maken van kenmerken voor methoden, para meters en retour typen. Een functie methode is een methode met `Function` en een trigger kenmerk dat wordt toegepast op een invoer parameter, zoals wordt weer gegeven in het volgende voor beeld:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Het trigger kenmerk geeft het trigger type aan en bindt invoer gegevens aan een methode parameter. De vorige voorbeeld functie wordt geactiveerd door een wachtrij bericht en het wachtrij bericht wordt door gegeven aan de methode in de `myQueueItem` para meter.

Het `Function` kenmerk markeert de methode als een functie-ingangs punt. De naam moet uniek zijn binnen een project, beginnen met een letter en mag alleen letters, cijfers, `_` en `-` tot 127 tekens lang zijn. Project sjablonen maken vaak een methode met `Run` de naam, maar de naam van de methode kan elke geldige naam van een C#-methode zijn.

Omdat .NET geïsoleerde projecten worden uitgevoerd in een afzonderlijk werk proces, kunnen bindingen geen gebruik maken van geavanceerde binding klassen, zoals `ICollector<T>` , `IAsyncCollector<T>` en `CloudBlockBlob` . Er is ook geen rechtstreekse ondersteuning voor typen die zijn overgenomen van onderliggende service-Sdk's, zoals [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). In plaats daarvan zijn bindingen afhankelijk van teken reeksen, matrices en serialiseerbare typen, zoals gewone oude klassen objecten (POCOs). 

Voor HTTP-triggers moet u gebruiken `HttpRequestData` `HttpResponseData` om toegang te krijgen tot de aanvraag-en antwoord gegevens. Dit is omdat u geen toegang hebt tot de oorspronkelijke HTTP-aanvraag-en antwoord objecten wanneer u out-of-process uitvoert. 

### <a name="input-bindings"></a>Invoerbindingen

Een functie kan nul of meer invoer bindingen bevatten die gegevens kunnen door geven aan een functie. Net als triggers worden invoer bindingen gedefinieerd door een bindings kenmerk toe te passen op een invoer parameter. Wanneer de functie wordt uitgevoerd, probeert de runtime gegevens op te halen die zijn opgegeven in de binding. De gegevens die worden aangevraagd, zijn vaak afhankelijk van de gegevens die door de trigger worden verstrekt met behulp van bindings parameters.  

### <a name="output-bindings"></a>Uitvoerbindingen

Als u naar een uitvoer binding wilt schrijven, moet u een uitvoer binding kenmerk Toep assen op de functie methode, die is gedefinieerd om te schrijven naar de gebonden service. De waarde die wordt geretourneerd door de methode, wordt naar de uitvoer binding geschreven. Het volgende voor beeld schrijft bijvoorbeeld een teken reeks waarde naar een berichten wachtrij met de naam met `functiontesting2` behulp van een uitvoer binding:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Meerdere uitvoer bindingen

De gegevens die naar een uitvoer binding worden geschreven, zijn altijd de retour waarde van de functie. Als u naar meer dan één uitvoer binding wilt schrijven, moet u een aangepast retour type maken. Voor dit retour type moet het bindings kenmerk output worden toegepast op een of meer eigenschappen van de klasse. In het volgende voor beeld wordt naar zowel een HTTP-antwoord als een wachtrij-uitvoer binding geschreven:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>HTTP-trigger

HTTP-triggers vertaalt het binnenkomende HTTP-aanvraag bericht naar een `HttpRequestData` object dat wordt door gegeven aan de functie. Dit object bevat gegevens uit de aanvraag, inclusief `Headers` , `Cookies` , `Identities` , `URL` en optioneel een bericht `Body` . Dit object is een representatie van het HTTP-aanvraag object en niet de aanvraag zelf. 

Op dezelfde manier retourneert de functie een `HttpReponseData` object, dat gegevens bevat die worden gebruikt om het HTTP-antwoord te maken, inclusief bericht `StatusCode` , `Headers` en optioneel een bericht `Body` .  

De volgende code is een HTTP-trigger 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Logboekregistratie

In .NET geïsoleerd kunt u naar Logboeken schrijven met behulp [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) van een exemplaar dat is verkregen van een `FunctionContext` object dat aan uw functie is door gegeven. Roep de `GetLogger` methode aan, waarbij een teken reeks waarde wordt door gegeven die de naam is van de categorie waarin de logboeken worden geschreven. De categorie is doorgaans de naam van de specifieke functie van waaruit de logboeken worden geschreven. Zie het [artikel bewaking](functions-monitoring.md#log-levels-and-categories)voor meer informatie over categorieën. 

In het volgende voor beeld ziet u hoe u `ILogger` in een-functie Logboeken kunt ophalen en opslaan:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Gebruik verschillende methoden `ILogger` om verschillende logboek niveaus te schrijven, zoals `LogWarning` of `LogError` . Zie het [artikel bewaking](functions-monitoring.md#log-levels-and-categories)voor meer informatie over logboek niveaus.

Er [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) wordt ook een gegeven bij het gebruik van [afhankelijkheids injectie](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Verschillen met de functies van de .NET-klassen bibliotheek

In deze sectie worden de huidige status van de functionele en gedrags verschillen beschreven die worden uitgevoerd op .NET 5,0 out-of-process vergeleken met .NET Class Library-functies die in-process worden uitgevoerd:

| Functie/gedrag |  In-process (.NET Core 3,1) | Out-of-process (.NET 5,0) |
| ---- | ---- | ---- |
| .NET-versies | LTS (.NET Core 3,1) | Huidige (.NET 5,0) |
| Kern pakketten | [Micro soft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Micro soft. Azure. functions. worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Micro soft. Azure. functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Bindings extensie pakketten | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Onder [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Logboekregistratie | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) door gegeven aan de functie | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) verkregen van `FunctionContext` |
| Annulerings tokens | [Ondersteund](functions-dotnet-class-library.md#cancellation-tokens) | Niet ondersteund |
| Uitvoerbindingen | Out-para meters | Retourwaarden |
| Typen uitvoerbindingen |  `IAsyncCollector`, [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true), [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)en andere client-specifieke typen | Eenvoudige typen, JSON Serializable-typen en arrays. |
| Meerdere uitvoer bindingen | Ondersteund | [Ondersteund](#multiple-output-bindings) |
| HTTP-trigger | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Durable Functions | [Ondersteund](durable/durable-functions-overview.md) | Niet ondersteund | 
| Verplichte bindingen | [Ondersteund](functions-dotnet-class-library.md#binding-at-runtime) | Niet ondersteund |
| function.jsop artefact | Gegenereerd | Niet gegenereerd |
| Configuratie | [host.jsop](functions-host-json.md) | [host.js](functions-host-json.md) en aangepaste initialisatie |
| Afhankelijkheidsinjectie | [Ondersteund](functions-dotnet-dependency-injection.md)  | [Ondersteund](#dependency-injection) |
| Middleware | Niet ondersteund | Ondersteund |
| Koude start tijden | Standaard | Langer, omdat de just-in-time-out wordt opgestart. Voer in Linux in plaats van Windows uit om mogelijke vertragingen te verminderen. |
| ReadyToRun | [Ondersteund](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Bekende problemen

Zie [Deze pagina met bekende problemen](https://aka.ms/AAbh18e)voor meer informatie over het oplossen van problemen met het uitvoeren van met .net geïsoleerde-proces functies. [Maak een probleem in deze github-opslag plaats](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)om problemen op te lossen.  

## <a name="next-steps"></a>Volgende stappen

+ [Meer informatie over triggers en bindingen](functions-triggers-bindings.md)
+ [Meer informatie over Best Practices for Azure Functions](functions-best-practices.md)
