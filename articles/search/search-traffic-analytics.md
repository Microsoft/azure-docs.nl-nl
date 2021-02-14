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
ms.openlocfilehash: 2ad6f5bf18d362f846e12720e00584b854729366
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515662"
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

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

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
