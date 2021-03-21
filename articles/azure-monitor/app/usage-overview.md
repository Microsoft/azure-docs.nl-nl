---
title: Gebruiks analyse met Azure-toepassing Insights | Micro soft docs
description: Inzicht in uw gebruikers en wat ze met uw app doen.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: d9de1e10363f2100b9dfe557dc12e0be951ce6b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489035"
---
# <a name="usage-analysis-with-application-insights"></a>Gebruiksanalyse met Application Insights

Welke functies van uw web-of mobiele app zijn het meest populair? Bereiken uw gebruikers hun doel stellingen met uw app? Vallen ze op bepaalde punten uit en retour neren ze later?  [Azure-toepassing Insights](./app-insights-overview.md) helpt u krachtige inzichten te krijgen in de manier waarop mensen uw app gebruiken. Telkens wanneer u uw app bijwerkt, kunt u bepalen hoe goed het werkt voor gebruikers. Met deze kennis kunt u op gegevens gebaseerde beslissingen nemen over uw volgende ontwikkelings cycli.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Telemetrie verzenden vanuit uw app

De beste ervaring wordt verkregen door Application Insights te installeren in de code van uw app-server en op uw webpagina's. De client-en Server onderdelen van uw app sturen telemetrie terug naar de Azure Portal voor analyse.

1. **Server code:** Installeer de juiste module voor uw [ASP.net](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md)of een [andere](./platforms.md) app.

    * *Wilt u geen server code installeren? U hoeft alleen maar [een Azure-toepassing Insights-resource te maken](./create-new-resource.md).*

2. **Webpagina code:** Voeg het volgende script toe aan de webpagina voordat deze wordt gesloten ``</head>`` . Vervang de instrumentatie sleutel door de juiste waarde voor uw Application Insights Bron:
    
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
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Raadpleeg het [naslag artikel over Java script SDK](./javascript.md)voor meer informatie over geavanceerde configuraties voor het controleren van websites.

3. **Mobiele app-code:** Gebruik de App Center SDK voor het verzamelen van gebeurtenissen uit uw app en verzend vervolgens kopieën van deze gebeurtenissen naar Application Insights voor analyse door [deze hand leiding te volgen](../app/mobile-center-quickstart.md).

4. **Telemetrie ophalen:** Voer uw project gedurende enkele minuten uit in de foutopsporingsmodus en zoek vervolgens naar resultaten op de Blade overzicht in Application Insights.

    Publiceer uw app voor het bewaken van de prestaties van uw app en ontdek wat uw gebruikers doen met uw app.

## <a name="explore-usage-demographics-and-statistics"></a>Demografische gegevens over gebruik en statistieken verkennen
Ontdek wanneer mensen uw app gebruiken, op welke pagina's ze het meest geïnteresseerd zijn, waar uw gebruikers zich bevinden, welke browsers en besturings systemen ze gebruiken. 

Met de rapporten gebruikers en sessies worden uw gegevens op pagina's of aangepaste gebeurtenissen gefilterd en gesegmenteerd op basis van eigenschappen zoals locatie, omgeving en pagina. U kunt ook uw eigen filters toevoegen.

![Scherm opname toont de overzichts pagina van gebruikers voor een fictief bedrijf.](./media/usage-overview/users.png)  

Inzichten aan de rechter kant van interessante patronen in de set met gegevens.  

* Het rapport **gebruikers** telt het aantal unieke gebruikers dat toegang heeft tot uw pagina's binnen de gekozen tijds periode. Voor web-apps worden gebruikers geteld met behulp van cookies. Als iemand uw site opent met verschillende browsers of client machines, of als de cookies worden gewist, worden ze meer dan één keer geteld.
* Het rapport **sessies** telt het aantal gebruikers sessies die toegang hebben tot uw site. Een sessie is een periode van activiteit door een gebruiker, die door een periode van inactiviteit van meer dan een half uur wordt beëindigd.

[Meer informatie over de hulpprogram ma's gebruikers, sessies en gebeurtenissen](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Bewaren-hoeveel gebruikers worden weer gegeven?

Retentie helpt u te begrijpen hoe vaak uw gebruikers terugkeren naar het gebruik van hun app, op basis van cohorts van gebruikers die een bepaalde zakelijke actie hebben uitgevoerd tijdens een bepaalde periode. 

- Meer informatie over de specifieke functies waarmee gebruikers meer dan andere kunnen terugkomen 
- Formulier hypo Thesen op basis van echte gebruikers gegevens 
- Bepalen of er een probleem is met het bewaren van uw product 

![Scherm opname toont de overzichts pagina voor retentie, waarin informatie wordt weer gegeven over hoe vaak gebruikers retour neren om hun app te gebruiken.](./media/usage-overview/retention.png) 

Met de besturings elementen voor retentie bovenaan kunt u specifieke gebeurtenissen en tijds bereik definiëren voor het berekenen van de Bewaar periode. De grafiek in het midden geeft een visuele weer gave van het algemene Bewaar percentage op basis van het opgegeven tijds bereik. In de grafiek aan de onderkant staat een afzonderlijke Bewaar periode in een bepaalde tijd. Met dit detail niveau kunt u begrijpen wat uw gebruikers doen en wat van invloed kan zijn op het retour neren van gebruikers met een gedetailleerdere granulariteit.  

[Meer informatie over het hulp programma voor retentie](usage-retention.md)

## <a name="custom-business-events"></a>Aangepaste zakelijke gebeurtenissen

Om duidelijk inzicht te krijgen in wat gebruikers met uw app doen, is het handig om regels code in te voegen voor het vastleggen van aangepaste gebeurtenissen. Deze gebeurtenissen kunnen alles volgen van gedetailleerde gebruikers acties, zoals het klikken op specifieke knoppen, tot belang rijke zakelijke gebeurtenissen, zoals het maken van een aankoop of het winnen van een spel.

U kunt ook de [invoeg toepassing voor automatisch verzamelen van analyses](javascript-click-analytics-plugin.md) gebruiken om aangepaste gebeurtenissen te verzamelen.

In sommige gevallen kunnen pagina weergaven nuttige gebeurtenissen vertegenwoordigen. Dit is in het algemeen niet waar. Een gebruiker kan een product pagina openen zonder het product aan te schaffen. 

Met specifieke zakelijke evenementen kunt u de voortgang van uw gebruikers via uw site in kaart brengen. U kunt hun voor keuren vinden voor verschillende opties en waar ze uitvallen of problemen opleveren. Met deze kennis kunt u weloverwogen beslissingen nemen over de prioriteiten in uw ontwikkelings achterstand.

Gebeurtenissen kunnen worden vastgelegd van de client zijde van de app:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Of aan de server zijde:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

U kunt eigenschaps waarden aan deze gebeurtenissen koppelen, zodat u de gebeurtenissen kunt filteren of splitsen wanneer u ze in de portal inspecteert. Daarnaast is er een standaardset eigenschappen gekoppeld aan elke gebeurtenis, zoals een anonieme gebruikers-ID, waarmee u de volg orde van de activiteiten van een afzonderlijke gebruiker kunt traceren.

Meer informatie over [aangepaste gebeurtenissen](./api-custom-events-metrics.md#trackevent) en [Eigenschappen](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Segment-en dobbel stenen gebeurtenissen

In de hulpprogram ma's gebruikers, sessies en gebeurtenissen kunt u aangepaste gebeurtenissen op basis van gebruiker, gebeurtenis naam en eigenschappen delen en aan een dobbel stenen uitdelen.
![Scherm opname toont de overzichts pagina van gebruikers voor een fictief bedrijf.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>De telemetrie ontwerpen met de app

Houd bij het ontwerpen van elk onderdeel van uw app rekening met de manier waarop u het succes van uw gebruikers wilt meten. Bepaal welke zakelijke gebeurtenissen u moet vastleggen en codeer de tracerings aanroepen voor die gebeurtenissen in uw app vanaf het begin.

## <a name="a--b-testing"></a>A | B testen
Als u niet weet welke variant van een functie succesvol is, kunt u beide versies vrijgeven, zodat elke gebruiker toegankelijk is voor verschillende gebruikers. Meet het succes van elke en ga vervolgens naar een uniforme versie.

Voor deze techniek koppelt u afzonderlijke eigenschaps waarden aan alle telemetrie die door elke versie van uw app worden verzonden. U kunt dit doen door eigenschappen te definiëren in de actieve TelemetryContext. Deze standaard eigenschappen worden toegevoegd aan elk telemetrie-bericht dat de toepassing verzendt, niet alleen uw aangepaste berichten, maar ook de standaard-telemetrie.

Filter en Splits uw gegevens op de eigenschaps waarden in de Application Insights Portal, zodat u de verschillende versies kunt vergelijken.

[Stel hiervoor een initialisatie functie voor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)in:

**ASP.NET-apps**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

In de initialisatie functie van de web-app, zoals Global. asax. CS:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core-apps**

> [!NOTE]
> Het toevoegen van initializer met `ApplicationInsights.config` of gebruikt `TelemetryConfiguration.Active` is niet geldig voor ASP.net core toepassingen. 

Voor [ASP.net core](asp-net-core.md#adding-telemetryinitializers) toepassingen voegt u een nieuw `TelemetryInitializer` item toe door het toe te voegen aan de container voor het invoegen van afhankelijkheden, zoals hieronder wordt weer gegeven. Dit doet u in `ConfigureServices` de methode van uw `Startup.cs` klasse.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Alle nieuwe TelemetryClients voegen automatisch de eigenschaps waarde toe die u opgeeft. Afzonderlijke telemetrie-gebeurtenissen kunnen de standaard waarden onderdrukken.

## <a name="next-steps"></a>Volgende stappen
   - [Gebruikers, sessies, gebeurtenissen](usage-segmentation.md)
   - [Trechters](usage-funnels.md)
   - [Bewaar](usage-retention.md)
   - [Gebruikersstromen](usage-flows.md)
   - [Werkmappen](../visualize/workbooks-overview.md)
