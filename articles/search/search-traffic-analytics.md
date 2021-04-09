---
title: Telemetrie voor het zoeken naar verkeer analyse
titleSuffix: Azure Cognitive Search
description: Schakel zoek verkeer Analytics in voor Azure Cognitive Search, verzamel telemetrie en door de gebruiker geïnitieerde gebeurtenissen met behulp van Application Insights en analyseer vervolgens de resultaten in een Power BI rapport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485466"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Telemetriegegevens verzamelen voor analyse van het zoek verkeer

Search Traffic Analytics is een patroon voor het verzamelen van telemetrie over gebruikers interacties met uw Azure Cognitive Search-toepassing, zoals door de gebruiker geïnitieerde Klik gebeurtenissen en toetsenbord invoer. Met behulp van deze informatie kunt u de effectiviteit van uw zoek oplossing bepalen, waaronder populaire zoek termen, wissel frequentie en de resultaten van de query invoer nul als resultaat.

Dit patroon neemt afhankelijk van [Application Insights](../azure-monitor/app/app-insights-overview.md) (een functie van [Azure monitor](../azure-monitor/index.yml)) voor het verzamelen van gebruikers gegevens. Hiervoor moet u instrumentatie toevoegen aan de client code, zoals wordt beschreven in dit artikel. Ten slotte hebt u een rapportage mechanisme nodig om de gegevens te analyseren. We raden Power BI aan, maar u kunt het toepassings dashboard of een hulp programma gebruiken dat verbinding maakt met Application Insights.

> [!NOTE]
> Het patroon dat in dit artikel wordt beschreven, is bedoeld voor geavanceerde scenario's en clickstream gegevens die worden gegenereerd door de code die u aan uw client toevoegt. Service logboeken zijn daarentegen eenvoudig in te stellen, bieden een bereik aan metrische gegevens en kunnen worden uitgevoerd in de Portal zonder dat hiervoor code is vereist. Het inschakelen van logboek registratie wordt aanbevolen voor alle scenario's. Zie [logboek gegevens verzamelen en analyseren](search-monitor-logs.md)voor meer informatie.

## <a name="identify-relevant-search-data"></a>Relevante Zoek gegevens identificeren

Als u nuttige metrische gegevens wilt hebben voor de analyse van het verkeer, is het nood zakelijk dat u bepaalde signalen van de gebruikers van uw zoek toepassing registreert. Deze signalen duiden inhoud aan waar gebruikers geïnteresseerd zijn en die ze relevant achten. Voor de analyse van het verkeer, waaronder:

+ Door de gebruiker gegenereerde Zoek gebeurtenissen: alleen Zoek opdrachten die door een gebruiker zijn gestart, zijn interessant. Andere zoek aanvragen, zoals die worden gebruikt voor het vullen van facetten of het ophalen van interne informatie, zijn niet belang rijk. Zorg ervoor dat alleen door de gebruiker geïnitieerde gebeurtenissen worden uitgevoerd om scheefheid of bias in uw resultaten te voor komen.

+ Door de gebruiker gegenereerde Klik gebeurtenissen: op een pagina met zoek resultaten betekent een gebeurtenis klikken doorgaans dat een document een relevant resultaat is voor een specifieke zoek query.

Als u een koppeling zoekt en klikt op gebeurtenissen met een correlatie-ID, krijgt u een beter inzicht in de manier waarop de zoek functionaliteit van uw toepassing wordt uitgevoerd.

## <a name="add-search-traffic-analytics"></a>Analyse van zoek verkeer toevoegen

Open op de pagina [Portal](https://portal.azure.com) voor uw Azure Cognitive Search-service de pagina Zoek Traffic Analytics om toegang te krijgen tot een cheat blad voor het volgen van dit telemetrie-patroon. Op deze pagina kunt u een Application Insights resource selecteren of maken, de instrumentatie sleutel ophalen, fragmenten kopiëren die u kunt aanpassen voor uw oplossing en een Power BI rapport downloaden dat is gebaseerd op het schema dat in het patroon wordt weer gegeven.

![Traffic Analytics pagina zoeken in de portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Traffic Analytics pagina zoeken in de portal")

## <a name="1---set-up-application-insights"></a>1-Application Insights instellen

Selecteer een bestaande Application Insights resource of [Maak er een](../azure-monitor/app/create-new-resource.md) als u er nog geen hebt. Als u de pagina Zoek Traffic Analytics gebruikt, kunt u de instrumentatie sleutel kopiëren die uw toepassing nodig heeft om verbinding te maken met Application Insights.

Zodra u een Application Insights resource hebt, kunt u de [instructies voor ondersteunde talen en platforms](../azure-monitor/app/platforms.md) volgen om uw app te registreren. Bij de registratie wordt simpelweg de instrumentatie sleutel van Application Insights toegevoegd aan uw code, waarmee de koppeling wordt ingesteld. U kunt de sleutel vinden in de portal of op de pagina zoeken Traffic Analytics wanneer u een bestaande resource selecteert.

Een snelkoppeling die werkt voor sommige Visual Studio-project typen wordt weer gegeven in de volgende stappen. Hiermee maakt u een resource en registreert u uw app in slechts enkele klikken.

1. Voor Visual Studio en ASP.NET Development opent u uw oplossing en selecteert u **project**  >  **toevoegen Application Insights Telemetry**.

1. Klik op **Aan de slag**.

1. Registreer uw app door een Microsoft-account, een Azure-abonnement en een Application Insights bron op te geven (een nieuwe resource is de standaard instelling). Klik op **Registreren**.

Uw toepassing is op dit moment ingesteld voor toepassings bewaking, wat betekent dat alle pagina belasting wordt bijgehouden met standaard metrische gegevens. Zie [Application Insights telemetrie aan de server zijde inschakelen](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio)voor meer informatie over de vorige stappen.

## <a name="2---add-instrumentation"></a>2: instrumentatie toevoegen

In deze stap gaat u uw eigen zoek toepassing gebruiken met behulp van de Application Insights resource die u in de bovenstaande stap hebt gemaakt. Er zijn vier stappen voor dit proces, te beginnen met het maken van een telemetrie-client.

### <a name="step-1-create-a-telemetry-client"></a>Stap 1: een telemetrie-client maken

Maak een object waarmee gebeurtenissen naar Application Insights worden verzonden. U kunt instrumentatie toevoegen aan de toepassings code aan de server zijde of aan de client zijde uitgevoerde code in een browser, die hier wordt weer gegeven als C#-en Java script-varianten (Zie de volledige lijst met [ondersteunde platforms en frameworks](../azure-monitor/app/platforms.md)voor andere talen). Kies de benadering die u de gewenste diepte van de informatie biedt.

Met telemetrie aan de server zijde worden metrische gegevens op de toepassingslaag vastgelegd, bijvoorbeeld in toepassingen die worden uitgevoerd als een webservice in de Cloud, of als een on-premises app in een bedrijfs netwerk. Op de telemetrie van de server worden Zoek-en klik gebeurtenissen, de positie van een document in resultaten en query gegevens, maar uw gegevens verzameling wordt bepaald door de gegevens die op die laag beschikbaar zijn.

Op de client hebt u mogelijk extra code voor het bewerken van query-invoer, het toevoegen van navigatie of het opnemen van context (bijvoorbeeld query's die worden gestart vanaf een start pagina versus een product pagina). Als uw oplossing wordt beschreven, kunt u ervoor kiezen om aan client zijde instrumentatie toe te staan, zodat de telemetrie het aanvullende detail weergeeft. Hoe dit aanvullende detail wordt verzameld, valt buiten het bereik van dit patroon, maar u kunt [Application Insights voor](../azure-monitor/app/javascript.md#explore-browserclient-side-data) webpagina's bekijken voor meer richting. 

**C# gebruiken**

Voor C# moet de **InstrumentationKey** worden gedefinieerd in de configuratie van uw toepassing, zoals appsettings.jsop als uw project ASP.net is. Ga terug naar de registratie-instructies als u niet zeker weet wat de sleutel locatie is.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**JavaScript gebruiken**

Het huidige (onderstaande) fragment is versie 5. de versie wordt in het fragment gecodeerd als SV: "#" en de [huidige versie is ook beschikbaar op github](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Voor de Lees baarheid en om mogelijke java script-fouten te verminderen, worden alle mogelijke configuratie opties vermeld op een nieuwe regel in code hierboven. Als u de waarde van een gefragmenteerde lijn niet wilt wijzigen, kunt u deze verwijderen.


### <a name="step-2-request-a-search-id-for-correlation"></a>Stap 2: een zoek-ID aanvragen voor correlatie

Voor het correleren van zoek aanvragen met klikken moet u een correlatie-ID hebben die deze twee afzonderlijke gebeurtenissen verbindt. Azure Cognitive Search biedt u een zoek-ID wanneer u deze aanvraagt met een HTTP-header.

Het hebben van de zoek-ID staat correlatie toe van de metrische gegevens die door Azure Cognitive Search worden gegenereerd voor de aanvraag zelf, met de aangepaste metrische gegevens die u aanmeldt Application Insights.

**C# (nieuwere V11-SDK) gebruiken**

De nieuwste SDK vereist het gebruik van een http-pijp lijn voor het instellen van de header zoals beschreven in dit voor [beeld](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**C# (oudere V10 toevoegen-SDK) gebruiken**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Java script gebruiken (REST-Api's aanroepen)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Stap 3: gebeurtenissen in een logboek zoeken

Telkens wanneer een zoek opdracht wordt uitgegeven door een gebruiker, moet u zich als een zoek gebeurtenis aanmelden met het volgende schema voor een Application Insights aangepaste gebeurtenis. Vergeet alleen door gebruikers gegenereerde Zoek opdrachten te registreren.

+ **SearchServiceName**: (teken reeks) zoek service naam
+ **SearchId**: (GUID) de unieke id van de zoek query (komt in het zoek antwoord)
+ **Indexnaam**: (teken reeks) zoek service index waarvoor een query moet worden uitgevoerd
+ **QueryTerms**: (teken reeks) zoek termen die zijn ingevoerd door de gebruiker
+ **ResultCount**: (int) aantal opgehaalde documenten (komt voor in het zoek antwoord)
+ **ScoringProfile**: (teken reeks) naam van het gebruikte Score profiel, indien van toepassing

> [!NOTE]
> Vraag het aantal door de gebruiker gegenereerde query's aan door $count = True toe te voegen aan uw zoek query. Zie [documenten zoeken (rest)](/rest/api/searchservice/search-documents#query-parameters)voor meer informatie.
>

**C# gebruiken**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript gebruiken**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Stap 4: gebeurtenissen op logboeken registreren

Telkens wanneer een gebruiker op een document klikt, is dit een signaal dat moet worden vastgelegd voor de analyse van de zoek opdracht. Gebruik Application Insights aangepaste gebeurtenissen om deze gebeurtenissen te registreren met het volgende schema:

+ **ServiceName**: (teken reeks) naam zoek service
+ **SearchId**: (GUID) unieke id van de gerelateerde zoek query
+ **Documenten**: (teken reeks) document-id
+ **Position**: (int) positie van het document op de pagina met zoek resultaten

> [!NOTE]
> De positie verwijst naar de Cardinal-volg orde in uw toepassing. U kunt dit nummer instellen, zolang het altijd hetzelfde is, om een vergelijking mogelijk te maken.
>

**C# gebruiken**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript gebruiken**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3-in Power BI analyseren

Nadat u uw app hebt geinstrumenteerd en hebt gecontroleerd of uw toepassing correct is verbonden met Application Insights, downloadt u een vooraf gedefinieerde rapport sjabloon om gegevens te analyseren in Power BI bureau blad. Het rapport bevat vooraf gedefinieerde grafieken en tabellen die nuttig zijn voor het analyseren van de aanvullende gegevens die zijn vastgelegd voor analyse van het zoek verkeer.

1. Klik in het linkerdeel venster van het dash board van Azure Cognitive Search onder **instellingen** op **verkeer analyse zoeken**.

1. Klik op de pagina **Traffic Analytics zoeken** in stap 3 op **Power BI Desktop ophalen** om Power bi te installeren.

   ![Power BI-rapporten ophalen](./media/search-traffic-analytics/get-use-power-bi.png "Power BI-rapporten ophalen")

1. Klik op dezelfde pagina op **Power bi rapport downloaden**.

1. Het rapport wordt geopend in Power BI Desktop en u wordt gevraagd verbinding te maken met Application Insights en referenties op te geven. U kunt verbindings informatie vinden op de Azure Portal pagina's voor uw Application Insights resource. Geef voor referenties dezelfde gebruikers naam en hetzelfde wacht woord op die u gebruikt voor de aanmelding bij de portal.

   ![Verbinding maken met Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Verbinding maken met Application Insights")

1. Klik op **laden**.

Het rapport bevat grafieken en tabellen waarmee u meer onderbouwde beslissingen kunt nemen om uw zoek prestaties en relevantie te verbeteren.

Metrische gegevens zijn opgenomen in de volgende items:

+ Zoek het volume en de populairste term-document paren: de termen die tot hetzelfde document hebben geklikt, gesorteerd door te klikken.
+ Zoek opdrachten zonder klikken: voor waarden voor top query's die geen klikken registreren

In de volgende scherm afbeelding ziet u hoe een ingebouwd rapport eruit kan zien als u alle schema-elementen hebt gebruikt.

![Power BI dash board voor Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI dash board voor Azure Cognitive Search")

## <a name="next-steps"></a>Volgende stappen

Instrumenteer uw zoek toepassing om krachtige en zicht bare gegevens over uw zoek service te krijgen.

U vindt meer informatie over [Application Insights](../azure-monitor/app/app-insights-overview.md) en gaat u naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-insights/) voor meer informatie over de verschillende service lagen.

Meer informatie over het maken van verbluffende rapporten. Zie [aan de slag met Power bi Desktop](/power-bi/fundamentals/desktop-getting-started) voor meer informatie.
