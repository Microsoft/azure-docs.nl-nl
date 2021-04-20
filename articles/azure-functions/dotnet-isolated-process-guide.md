---
title: .NET Isolated-proceshandleiding voor .NET 5.0 in Azure Functions
description: Meer informatie over het gebruik van een geïsoleerd .NET-proces om uw C#-functies uit te voeren op .NET 5.0 out-of-process in Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 53f3c79886d26b20a584d747759176ea842741cf
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739274"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Handleiding voor het uitvoeren van functies op .NET 5.0 in Azure

Dit artikel is een inleiding tot het gebruik van C# voor het ontwikkelen van geïsoleerde .NET-procesfuncties die niet meer worden verwerkt in Azure Functions. Als u niet meer aan het proces werkt, kunt u uw functiecode loskoppelen van Azure Functions runtime. Het biedt u ook een manier om functies te maken en uit te voeren die zijn gericht op de huidige .NET 5.0-release. 

| Aan de slag | Concepten| Voorbeelden |
|--|--|--| 
| <ul><li>[Visual Studio Code gebruiken](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Opdrachtregelprogramma's gebruiken](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Visual Studio gebruiken](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Hostingopties](functions-scale.md)</li><li>[Controle](functions-monitoring.md)</li> | <ul><li>[Referentievoorbeelden](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Als u .NET 5.0 niet hoeft te ondersteunen of uw functies niet meer hoeft uit te voeren, kunt u in plaats daarvan C#-klassebibliotheekfuncties [ontwikkelen.](functions-dotnet-class-library.md)

## <a name="why-net-isolated-process"></a>Waarom een geïsoleerd .NET-proces?

Voorheen Azure Functions alleen een nauw geïntegreerde modus ondersteund voor .NET-functies, die worden uitgevoerd als een klassebibliotheek [in](functions-dotnet-class-library.md) hetzelfde proces als de host. Deze modus biedt een diepe integratie tussen het hostproces en de functies. .NET-klassebibliotheekfuncties kunnen bijvoorbeeld binding-API's en -typen delen. Voor deze integratie is echter ook een nauwe koppeling tussen het hostproces en de .NET-functie vereist. .NET-functies die in-process worden uitgevoerd, moeten bijvoorbeeld worden uitgevoerd op dezelfde versie van .NET als de Functions-runtime. Als u buiten deze beperkingen wilt uitvoeren, kunt u er nu voor kiezen om uit te voeren in een geïsoleerd proces. Met deze procesisolatie kunt u ook functies ontwikkelen die gebruikmaken van huidige .NET-releases (zoals .NET 5.0), die niet systeemeigen worden ondersteund door de Functions-runtime.

Omdat deze functies in een afzonderlijk [](#differences-with-net-class-library-functions) proces worden uitgevoerd, zijn er enkele functie- en functionaliteitsverschillen tussen geïsoleerde .NET-functie-apps en .NET-klassebibliotheekfunctie-apps.

### <a name="benefits-of-running-out-of-process"></a>Voordelen van een bijna geen proces meer

Wanneer er geen gebruik meer wordt gemaakt van het proces, kunnen uw .NET-functies profiteren van de volgende voordelen: 

+ Minder conflicten: omdat de functies worden uitgevoerd in een afzonderlijk proces, conflicteren de in uw app gebruikte assemblies niet met een andere versie van dezelfde assemblies die door het hostproces worden gebruikt.  
+ Volledige controle over het proces: u kunt het starten van de app bepalen en de configuraties en de gestarte middleware bepalen.
+ Afhankelijkheidsinjectie: omdat u volledige controle over het proces hebt, kunt u huidig .NET-gedrag gebruiken voor afhankelijkheidsinjectie en middleware opnemen in uw functie-app. 

## <a name="supported-versions"></a>Ondersteunde versies

De enige versie van .NET die momenteel wordt ondersteund om buiten het proces te worden uitgevoerd, is .NET 5.0.

## <a name="net-isolated-project"></a>Geïsoleerd .NET-project

Een geïsoleerd .NET-functieproject is in feite een .NET-console-app-project dat gericht is op .NET 5.0. Hier volgen de basisbestanden die vereist zijn in een geïsoleerd .NET-project:

+ [host.jsin het](functions-host-json.md) bestand.
+ [local.settings.jsin het](functions-run-local.md#local-settings-file) bestand.
+ C#-projectbestand (.csproj) dat het project en de afhankelijkheden definieert.
+ Program.cs-bestand dat het toegangspunt voor de app is.

## <a name="package-references"></a>Pakketverwijzingen

Wanneer uw .NET-project niet meer wordt verwerkt, wordt een unieke set pakketten gebruikt, die zowel kernfunctionaliteit als bindingsextensies implementeren. 

### <a name="core-packages"></a>Kernpakketten 

De volgende pakketten zijn vereist om uw .NET-functies in een geïsoleerd proces uit te voeren:

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Extensiepakketten

Omdat functies die worden uitgevoerd in een geïsoleerd .NET-proces verschillende bindingstypen gebruiken, hebben ze een unieke set bindingsextensiepakketten nodig. 

U vindt deze extensiepakketten onder [Microsoft.Azure.Functions.Worker.Extensions.](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)

## <a name="start-up-and-configuration"></a>Starten en configureren 

Wanneer u geïsoleerde .NET-functies gebruikt, hebt u toegang tot het starten van uw functie-app, meestal in Program.cs. U bent verantwoordelijk voor het maken en starten van uw eigen host-exemplaar. Daarom hebt u ook directe toegang tot de configuratiepijplijn voor uw app. Wanneer u niet meer aan het proces bent, kunt u veel gemakkelijker configuraties toevoegen, afhankelijkheden injecteren en uw eigen middleware uitvoeren. 

De volgende code toont een voorbeeld van een [HostBuilder-pijplijn:]

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Voor deze code is `using Microsoft.Extensions.DependencyInjection;` vereist. 

Een [HostBuilder wordt] gebruikt om een volledig geitialiseerd [IHost-exemplaar] te bouwen en te retourneren, dat u asynchroon kunt uitvoeren om uw functie-app te starten. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configuratie

De [methode ConfigureFunctionsWorkerDefaults] wordt gebruikt om de instellingen toe te voegen die nodig zijn om de functie-app out-of-process te laten uitvoeren. Deze bevat de volgende functionaliteit:

+ Standaardset converters.
+ Stel de [standaard JsonSerializerOptions] in om casing voor eigenschapsnamen te negeren.
+ Integreren met Azure Functions logboekregistratie.
+ Middleware en functies voor uitvoerbinding.
+ Middleware voor functie-uitvoering.
+ Standaard gRPC-ondersteuning. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Als u toegang hebt tot de host builder-pijplijn, kunt u ook app-specifieke configuraties instellen tijdens de initialisatie. U kunt de methode [ConfigureAppConfiguration] op [HostBuilder] een of meer keren aanroepen om de configuraties toe te voegen die vereist zijn voor uw functie-app. Zie Configuratie in ASP.NET Core voor [meer informatie over app-configuratie.](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true) 

Deze configuraties zijn van toepassing op uw functie-app die in een afzonderlijk proces wordt uitgevoerd. Als u wijzigingen wilt aanbrengen in de functiehost of trigger- en bindingsconfiguratie, moet u nog steeds dehost.js[op bestand .](functions-host-json.md)   

### <a name="dependency-injection"></a>Afhankelijkheidsinjectie

Afhankelijkheidsinjectie is vereenvoudigd, vergeleken met .NET-klassebibliotheken. In plaats van dat u een opstartklasse moet maken om services te registreren, moet u [Alleen ConfigureServices] aanroepen op de hostbouwer en de extensiemethoden op [IServiceCollection] gebruiken om specifieke services te injecteren. 

In het volgende voorbeeld wordt een singleton-serviceafhankelijkheid injecteert:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Voor deze code is `using Microsoft.Extensions.DependencyInjection;` vereist. Zie [Afhankelijkheidsinjectie in ASP.NET Core voor meer informatie.](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)

### <a name="middleware"></a>Middleware

.NET Isolated ondersteunt ook middleware-registratie, opnieuw met behulp van een model dat vergelijkbaar is met wat er in ASP.NET. Dit model biedt u de mogelijkheid om logica in de aanroeppijplijn te injecteren en voordat en nadat functies worden uitgevoerd.

De [extensiemethode ConfigureFunctionsWorkerDefaults] heeft een overbelasting waarmee u uw eigen middleware kunt registreren, zoals u in het volgende voorbeeld kunt zien.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Zie het referentievoorbeeld voor aangepaste middleware voor een vollediger voorbeeld van het gebruik van aangepaste middleware in [uw functie-app.](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware)

## <a name="execution-context"></a>Context voor uitvoering

.NET Isolated geeft een [FunctionContext-object] door aan uw functiemethoden. Met dit object kunt u een [ILogger-exemplaar] naar de logboeken laten schrijven door de [GetLogger-methode] aan te roepen en een tekenreeks op te `categoryName` leveren. Zie Logboekregistratie voor [meer informatie.](#logging) 

## <a name="bindings"></a>Bindingen 

Bindingen worden gedefinieerd met behulp van kenmerken voor methoden, parameters en retourtypen. Een functiemethode is een methode met een kenmerk en een triggerkenmerk dat is toegepast op een `Function` invoerparameter, zoals wordt weergegeven in het volgende voorbeeld:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Het triggerkenmerk geeft het triggertype op en verbindt invoergegevens met een methodeparameter. De vorige voorbeeldfunctie wordt geactiveerd door een wachtrijbericht en het wachtrijbericht wordt doorgegeven aan de methode in de `myQueueItem` parameter .

Het `Function` kenmerk markeert de methode als een functie-ingangspunt. De naam moet uniek zijn binnen een project, beginnen met een letter en alleen letters, cijfers en , maximaal `_` `-` 127 tekens lang bevatten. Projectsjablonen maken vaak een methode met de naam , maar de naam van de methode `Run` kan elke geldige C#-methodenaam zijn.

Omdat geïsoleerde .NET-projecten worden uitgevoerd in een afzonderlijk werkproces, kunnen bindingen niet profiteren van uitgebreide bindingsklassen, `ICollector<T>` zoals `IAsyncCollector<T>` , en `CloudBlockBlob` . Er is ook geen directe ondersteuning voor typen die zijn overgenomen van onderliggende service-SDK's, zoals [DocumentClient] en [BrokeredMessage.] Bindingen zijn in plaats daarvan afhankelijk van tekenreeksen, matrices en serializeerbare typen, zoals gewone oude klasseobjecten (POCO's). 

Voor HTTP-triggers moet u [HttpRequestData] en [HttpResponseData] gebruiken om toegang te krijgen tot de aanvraag- en antwoordgegevens. Dit komt doordat u geen toegang hebt tot de oorspronkelijke HTTP-aanvraag- en antwoordobjecten wanneer het proces niet meer is uitgevoerd.

Zie het referentievoorbeeld voor bindingsextensies voor een volledige set referentievoorbeelden voor het gebruik van triggers [en bindingen](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions)bij het uitvoeren van out-of-process. 

### <a name="input-bindings"></a>Invoerbindingen

Een functie kan nul of meer invoerbindingen hebben die gegevens kunnen doorgeven aan een functie. Net als triggers worden invoerbindingen gedefinieerd door een bindingskenmerk toe te passen op een invoerparameter. Wanneer de functie wordt uitgevoerd, probeert de runtime gegevens op te halen die zijn opgegeven in de binding. De gegevens die worden aangevraagd, zijn vaak afhankelijk van informatie die door de trigger wordt verstrekt met behulp van bindingsparameters.  

### <a name="output-bindings"></a>Uitvoerbindingen

Als u naar een uitvoerbinding wilt schrijven, moet u een uitvoerbindingskenmerk toepassen op de functiemethode, die heeft gedefinieerd hoe naar de gebonden service moet worden geschreven. De waarde die door de methode wordt geretourneerd, wordt naar de uitvoerbinding geschreven. In het volgende voorbeeld wordt bijvoorbeeld een tekenreekswaarde naar een berichtenwachtrij met de naam schrijft `functiontesting2` met behulp van een uitvoerbinding:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Meerdere uitvoerbindingen

De gegevens die naar een uitvoerbinding worden geschreven, zijn altijd de retourwaarde van de functie. Als u naar meer dan één uitvoerbinding wilt schrijven, moet u een aangepast retourtype maken. Op dit retourtype moet het kenmerk uitvoerbinding zijn toegepast op een of meer eigenschappen van de klasse. Het volgende voorbeeld van een HTTP-trigger schrijft naar zowel het HTTP-antwoord als een wachtrijuitvoerbinding:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

Het antwoord van een HTTP-trigger wordt altijd beschouwd als een uitvoer, dus een retourwaardekenmerk is niet vereist.

### <a name="http-trigger"></a>HTTP-trigger

HTTP-triggers vertaalt het binnenkomende HTTP-aanvraagbericht naar een [HttpRequestData-object] dat wordt doorgegeven aan de functie. Dit object biedt gegevens van de aanvraag, waaronder `Headers` , , , en `Cookies` `Identities` `URL` optioneel een bericht `Body` . Dit object is een weergave van het HTTP-aanvraagobject en niet van de aanvraag zelf. 

Op dezelfde manier retourneert de functie een [HttpResponseData-object,] dat gegevens levert die worden gebruikt om het HTTP-antwoord te maken, waaronder het bericht , en optioneel `StatusCode` een bericht `Headers` `Body` .  

De volgende code is een HTTP-trigger 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Logboekregistratie

In .NET Isolated kunt u naar logboeken schrijven met behulp van een [ILogger-exemplaar] dat is verkregen van een [FunctionContext-object] dat is doorgegeven aan uw functie. Roep de [GetLogger-methode] aan, waarbij een tekenreekswaarde wordt door gegeven die de naam is van de categorie waarin de logboeken zijn geschreven. De categorie is doorgaans de naam van de specifieke functie van waaruit de logboeken worden geschreven. Zie het bewakingsartikel voor meer informatie [over categorieën.](functions-monitoring.md#log-levels-and-categories) 

In het volgende voorbeeld ziet u hoe u een [ILogger op kunt halen en] logboeken kunt schrijven binnen een functie:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Gebruik verschillende methoden van [ILogger om] verschillende logboekniveaus te schrijven, zoals `LogWarning` of `LogError` . Zie het bewakingsartikel voor meer informatie over [logboekniveaus.](functions-monitoring.md#log-levels-and-categories)

Er [wordt ook een ILogger] opgegeven wanneer [afhankelijkheidsinjectie wordt gebruikt.](#dependency-injection)

## <a name="differences-with-net-class-library-functions"></a>Verschillen met .NET-klassebibliotheekfuncties

In deze sectie wordt de huidige status beschreven van de functionele en gedragsverschillen die worden uitgevoerd op .NET 5.0 out-of-process in vergelijking met .NET-klassebibliotheekfuncties die in-process worden uitgevoerd:

| Functie/gedrag |  In-process (.NET Core 3.1) | Out-of-process (.NET 5.0) |
| ---- | ---- | ---- |
| .NET-versies | LTS (.NET Core 3.1) | Huidig (.NET 5.0) |
| Kernpakketten | [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Bindingsextensiepakketten | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Onder [Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Logboekregistratie | [ILogger doorgegeven] aan de functie | [ILogger] verkregen van [FunctionContext] |
| Annuleringstokens | [Ondersteund](functions-dotnet-class-library.md#cancellation-tokens) | Niet ondersteund |
| Uitvoerbindingen | Out-parameters | Retourwaarden |
| Typen uitvoerbindingen |  `IAsyncCollector`, [DocumentClient,] [BrokeredMessage]en andere clientspecifieke typen | Eenvoudige typen, JSON-serializeerbare typen en matrices. |
| Meerdere uitvoerbindingen | Ondersteund | [Ondersteund](#multiple-output-bindings) |
| HTTP-trigger | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Durable Functions | [Ondersteund](durable/durable-functions-overview.md) | Niet ondersteund | 
| Imperatieve bindingen | [Ondersteund](functions-dotnet-class-library.md#binding-at-runtime) | Niet ondersteund |
| function.jsover artefact | Gegenereerd | Niet gegenereerd |
| Configuratie | [host.jsop](functions-host-json.md) | [host.jsen](functions-host-json.md) aangepaste initialisatie |
| Afhankelijkheidsinjectie | [Ondersteund](functions-dotnet-dependency-injection.md)  | [Ondersteund](#dependency-injection) |
| Middleware | Niet ondersteund | Ondersteund |
| Koude starttijden | Typische | Langer, vanwege Just-In-Time-start-up. Voer uit op Linux in plaats van Windows om potentiële vertragingen te verminderen. |
| ReadyToRun | [Ondersteund](functions-dotnet-class-library.md#readytorun) | _Tbd_ |

## <a name="known-issues"></a>Bekende problemen

Zie deze pagina met bekende problemen voor informatie over tijdelijke oplossingen voor het uitvoeren van problemen met geïsoleerde [.NET-procesfuncties.](https://aka.ms/AAbh18e) Als u problemen wilt melden, [maakt u een probleem in deze GitHub-opslagplaats.](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)  

## <a name="next-steps"></a>Volgende stappen

+ [Meer informatie over triggers en bindingen](functions-triggers-bindings.md)
+ [Meer informatie over best practices voor Azure Functions](functions-best-practices.md)


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
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
