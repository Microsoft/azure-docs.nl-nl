---
title: Hand leiding voor het geïsoleerde .NET-proces voor .NET 5,0 in Azure Functions
description: Meer informatie over het gebruik van een door .NET geïsoleerd proces om uw C#-functies uit te voeren op .NET 5,0 out-of-process in Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 4da685c247427e78297df1753779ee9b5c7866b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023194"
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
+ Het bestand Program. cs dat het toegangs punt voor de app is.

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

Wanneer u met behulp van .NET geïsoleerde functies hebt, hebt u toegang tot het starten van uw functie-app, meestal in Program. cs. U bent zelf verantwoordelijk voor het maken en starten van uw eigen exemplaar van de host. Als zodanig hebt u ook directe toegang tot de configuratie pijplijn voor uw app. Wanneer u out-of-process uitvoert, kunt u veel eenvoudiger configuraties toevoegen, afhankelijkheden invoeren en uw eigen middleware uitvoeren. 

De volgende code toont een voor beeld van een [HostBuilder] -pijp lijn:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Een [HostBuilder] wordt gebruikt voor het bouwen en retour neren van een volledig geïnitialiseerd [IHost] -exemplaar dat u asynchroon uitvoert om uw functie-app te starten. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configuratie

De methode [ConfigureFunctionsWorkerDefaults] wordt gebruikt om de vereiste instellingen voor de functie-app toe te voegen die out-of-process worden uitgevoerd. Dit omvat de volgende functionaliteit:

+ Standaardset converters.
+ Stel de standaard [JsonSerializerOptions] in op het negeren van de behuizing op eigenschapnamen.
+ Integreren met Azure Functions logboek registratie.
+ Middleware en functies voor het binden van uitvoer.
+ Middleware voor functie-uitvoering.
+ Standaard gRPC-ondersteuning. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Als u toegang hebt tot de pijp lijn van de host Builder, kunt u tijdens de initialisatie ook specifieke app-configuraties instellen. U kunt de [ConfigureAppConfiguration] -methode op [HostBuilder] een of meer keren aanroepen om de configuraties toe te voegen die vereist zijn voor uw functie-app. Zie [configuratie in ASP.net core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)voor meer informatie over app-configuratie. 

Deze configuraties zijn van toepassing op de functie-app die wordt uitgevoerd in een afzonderlijk proces. Als u wijzigingen wilt aanbrengen in de functies host of trigger-en bindings configuratie, moet u de [host.jsin het bestand](functions-host-json.md)gebruiken.   

### <a name="dependency-injection"></a>Afhankelijkheidsinjectie

Het invoegen van afhankelijkheden is vereenvoudigd, vergeleken met .NET-klassen bibliotheken. In plaats van een opstart klasse te maken om services te registreren, moet u [ConfigureServices] op de host Builder aanroepen en de uitbreidings methoden op [IServiceCollection] gebruiken voor het injecteren van specifieke services. 

In het volgende voor beeld wordt een singleton-service afhankelijkheid geïnjecteerd:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Zie [afhankelijkheids injectie in ASP.net core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)voor meer informatie.

### <a name="middleware"></a>Middleware

.NET geïsoleerd ondersteunt ook middleware-registratie, met behulp van een model dat lijkt op wat er in ASP.NET is. Dit model biedt u de mogelijkheid om logica in te voeren in de aanroep pijplijn en vóór en na de uitvoering van de functies.

De extensie methode [ConfigureFunctionsWorkerDefaults] heeft een overbelasting waarmee u uw eigen middleware kunt registreren, zoals u in het volgende voor beeld ziet.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Voor een vollediger voor beeld van het gebruik van aangepaste middleware in uw functie-app raadpleegt u het voor beeld van een [aangepaste middleware-referentie](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Context voor uitvoering

Met .NET geïsoleerd wordt een [FunctionContext] -object door gegeven aan uw functie methoden. Met dit object kunt u een [ILogger] -exemplaar ophalen om naar de logboeken te schrijven door de [GetLogger] -methode aan te roepen en een teken reeks op te geven `categoryName` . Zie [logboek registratie](#logging)voor meer informatie. 

## <a name="bindings"></a>Bindingen 

Bindingen worden gedefinieerd door gebruik te maken van kenmerken voor methoden, para meters en retour typen. Een functie methode is een methode met een `Function` kenmerk en een trigger kenmerk dat wordt toegepast op een invoer parameter, zoals wordt weer gegeven in het volgende voor beeld:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Het trigger kenmerk geeft het trigger type aan en bindt invoer gegevens aan een methode parameter. De vorige voorbeeld functie wordt geactiveerd door een wachtrij bericht en het wachtrij bericht wordt door gegeven aan de methode in de `myQueueItem` para meter.

Het `Function` kenmerk markeert de methode als een functie-ingangs punt. De naam moet uniek zijn binnen een project, beginnen met een letter en mag alleen letters, cijfers, `_` en `-` tot 127 tekens lang zijn. Project sjablonen maken vaak een methode met `Run` de naam, maar de naam van de methode kan elke geldige naam van een C#-methode zijn.

Omdat .NET geïsoleerde projecten worden uitgevoerd in een afzonderlijk werk proces, kunnen bindingen geen gebruik maken van geavanceerde binding klassen, zoals `ICollector<T>` , `IAsyncCollector<T>` en `CloudBlockBlob` . Er is ook geen rechtstreekse ondersteuning voor typen die zijn overgenomen van onderliggende service-Sdk's, zoals [DocumentClient] en [BrokeredMessage]. In plaats daarvan zijn bindingen afhankelijk van teken reeksen, matrices en serialiseerbare typen, zoals gewone oude klassen objecten (POCOs). 

Voor HTTP-triggers moet u [HttpRequestData] en [HttpResponseData] gebruiken om toegang te krijgen tot de aanvraag-en antwoord gegevens. Dit is omdat u geen toegang hebt tot de oorspronkelijke HTTP-aanvraag-en antwoord objecten wanneer u out-of-process uitvoert.

Zie voor een volledige set met referentie voorbeelden voor het gebruik van triggers en bindingen wanneer u out-of-process uitvoert het [referentie voorbeeld voor bindings extensies](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Invoerbindingen

Een functie kan nul of meer invoer bindingen bevatten die gegevens kunnen door geven aan een functie. Net als triggers worden invoer bindingen gedefinieerd door een bindings kenmerk toe te passen op een invoer parameter. Wanneer de functie wordt uitgevoerd, probeert de runtime gegevens op te halen die zijn opgegeven in de binding. De gegevens die worden aangevraagd, zijn vaak afhankelijk van de gegevens die door de trigger worden verstrekt met behulp van bindings parameters.  

### <a name="output-bindings"></a>Uitvoerbindingen

Als u naar een uitvoer binding wilt schrijven, moet u een uitvoer binding kenmerk Toep assen op de functie methode, die is gedefinieerd om te schrijven naar de gebonden service. De waarde die wordt geretourneerd door de methode, wordt naar de uitvoer binding geschreven. Het volgende voor beeld schrijft bijvoorbeeld een teken reeks waarde naar een berichten wachtrij met de naam met `functiontesting2` behulp van een uitvoer binding:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Meerdere uitvoer bindingen

De gegevens die naar een uitvoer binding worden geschreven, zijn altijd de retour waarde van de functie. Als u naar meer dan één uitvoer binding wilt schrijven, moet u een aangepast retour type maken. Voor dit retour type moet het bindings kenmerk output worden toegepast op een of meer eigenschappen van de klasse. Het volgende voor beeld van een HTTP-trigger schrijft naar zowel het HTTP-antwoord als een wachtrij-uitvoer binding:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

Het antwoord van een HTTP-trigger wordt altijd beschouwd als een uitvoer. Daarom is er geen retour waarde-kenmerk vereist.

### <a name="http-trigger"></a>HTTP-trigger

Met HTTP-triggers wordt het binnenkomende HTTP-aanvraag bericht omgezet in een [HttpRequestData] -object dat wordt door gegeven aan de functie. Dit object bevat gegevens uit de aanvraag, inclusief `Headers` , `Cookies` , `Identities` , `URL` en optioneel een bericht `Body` . Dit object is een representatie van het HTTP-aanvraag object en niet de aanvraag zelf. 

De functie retourneert ook een [HttpResponseData] -object, dat gegevens bevat die worden gebruikt voor het maken van het HTTP-antwoord, inclusief bericht `StatusCode` , `Headers` en optioneel een bericht `Body` .  

De volgende code is een HTTP-trigger 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Logboekregistratie

In .NET geïsoleerd kunt u naar Logboeken schrijven met behulp van een [ILogger] -exemplaar dat is verkregen van een [FunctionContext] -object dat is door gegeven aan de functie. Roep de methode [GetLogger] aan, waarbij een teken reeks waarde wordt door gegeven die de naam is van de categorie waarin de logboeken worden geschreven. De categorie is doorgaans de naam van de specifieke functie van waaruit de logboeken worden geschreven. Zie het [artikel bewaking](functions-monitoring.md#log-levels-and-categories)voor meer informatie over categorieën. 

In het volgende voor beeld ziet u hoe u een [ILogger] -en schrijf Logboeken in een functie kunt ophalen:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Gebruik verschillende methoden van [ILogger] om verschillende logboek niveaus te schrijven, zoals `LogWarning` of `LogError` . Zie het [artikel bewaking](functions-monitoring.md#log-levels-and-categories)voor meer informatie over logboek niveaus.

Er wordt ook een [ILogger] gegeven bij het gebruik van [afhankelijkheids injectie](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Verschillen met de functies van de .NET-klassen bibliotheek

In deze sectie worden de huidige status van de functionele en gedrags verschillen beschreven die worden uitgevoerd op .NET 5,0 out-of-process vergeleken met .NET Class Library-functies die in-process worden uitgevoerd:

| Functie/gedrag |  In-process (.NET Core 3,1) | Out-of-process (.NET 5,0) |
| ---- | ---- | ---- |
| .NET-versies | LTS (.NET Core 3,1) | Huidige (.NET 5,0) |
| Kern pakketten | [Micro soft. NET. SDK. functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Micro soft. Azure. functions. worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Micro soft. Azure. functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Bindings extensie pakketten | [Micro soft. Azure. webjobs. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Onder [micro soft. Azure. functions. Worker. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Logboekregistratie | [ILogger] door gegeven aan de functie | [ILogger] verkregen van [FunctionContext] |
| Annulerings tokens | [Ondersteund](functions-dotnet-class-library.md#cancellation-tokens) | Niet ondersteund |
| Uitvoerbindingen | Out-para meters | Retourwaarden |
| Typen uitvoerbindingen |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage]en andere client-specifieke typen | Eenvoudige typen, JSON Serializable-typen en arrays. |
| Meerdere uitvoer bindingen | Ondersteund | [Ondersteund](#multiple-output-bindings) |
| HTTP-trigger | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
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


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
