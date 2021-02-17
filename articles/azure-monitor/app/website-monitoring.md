---
title: 'Quickstart: Websites bewaken met Azure Monitor Application Insights'
description: In deze quickstart krijgt u informatie over het instellen van client/browser-functies voor het controleren van websites met Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: mvc
ms.openlocfilehash: 0a30fb12e93058690fbd7611ae64fc0a7dd96f64
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583437"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Quickstart: Uw website bewaken met Azure Monitor Application Insights

In deze quickstart leert u hoe u de opensource Application Insights JavaScript-SDK aan uw website kunt toevoegen. Ook leert u hoe meer inzicht krijgt in de client-/browserervaring voor bezoekers van uw website.

Met Azure Monitor Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een website controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Application Insights biedt mogelijkheden voor controles van de server evenals van de browser/client.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een website waaraan u de Application Insights JavaScript-SDK kunt toevoegen.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens weer te geven:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer **Een resource maken** > **Hulpprogramma's voor beheer** > **Application Insights**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights-resource maakt, raadpleegt u [Een Application Insights-resource maken](./create-new-resource.md).
1. Wanneer een configuratiescherm wordt weergegeven, gebruikt u de volgende tabel om de invoervelden in te vullen:

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | De naam die de app identificeert die u wilt controleren. |
   | **Resourcegroep**     | myResourceGroup      | De naam voor de nieuwe resourcegroep waarin App Insights-gegevens worden gehost. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost. |
1. Selecteer **Maken**.

## <a name="create-an-html-file"></a>Een HTML-bestand maken

1. Maak op uw lokale computer een bestand met de naam ``hello_world.html``. Maak bijvoorbeeld het bestand in de hoofdmap van station C zodat het er als volgt uitziet: ``C:\hello_world.html``.
1. Kopieer en plak het volgende script in ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Application Insights-SDK configureren

1. Selecteer **Overzicht** > **Essentials** en kopieer de **Instrumentatiesleutel** van uw toepassing.

   ![Nieuw Application Insights-resourceformulier](media/website-monitoring/instrumentation-key-001.png)

1. Voeg het volgende script toe aan het ``hello_world.html``-bestand vóór de afsluitende ``</head>``-code:

    ```javascript
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
    src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
    //name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    //ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    //useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    //crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
    cfg: { // Application Insights Configuration
        instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
        /* ...Other Configuration Options... */
    }});
    </script>
    ```
    
1. Bewerk ``hello_world.html`` en voeg de instrumentatiesleutel toe.

1. Open ``hello_world.html`` in een lokale browsersessie. Met deze actie maakt u één paginaweergave. U kunt uw browser vernieuwen voor het genereren van meerdere testpaginaweergaven.

## <a name="monitor-your-website-in-the-azure-portal"></a>Uw website bewaken in Azure Portal

1. Open de pagina **Overzicht** van Application Insights opnieuw in Azure Portal om details weer te geven van de toepassing die momenteel wordt uitgevoerd. De pagina **Overzicht** is de locatie waar u uw instrumentatiesleutel hebt opgehaald.

   De vier standaardgrafieken op de overzichtspagina hebben betrekking op toepassingsgegevens op de server. Omdat we de interacties van de browser/client instrumenteren met de JavaScript SDK, is deze specifieke weergave niet van toepassing tenzij er ook een SDK op de server is geïnstalleerd.

1. Selecteer **Analytics** ![pictogram van toepassingsoverzicht](media/website-monitoring/006.png).  Door deze actie wordt **Analytics** geopend. Dit biedt een uitgebreide querytaal voor het analyseren van alle gegevens die zijn verzameld met Application Insights. Als u gegevens wilt weergeven die betrekking hebben op de browseraanvragen van de client, moet u de volgende query uitvoeren:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Analytics-grafiek met gebruikersaanvragen gedurende een tijdsperiode](./media/website-monitoring/analytics-query.png)

1. Ga terug naar de **overzichtspagina**. Selecteer onder de header **Onderzoeken** de optie **Browser** en selecteer vervolgens **Prestaties**.  Er worden metrische gegevens met betrekking tot de prestaties van uw website weergegeven. Er is een overeenkomstige weergave waarmee u fouten en uitzonderingen op uw website kunt analyseren. U kunt **Voorbeelden** selecteren voor toegang tot de [volledige transactiedetails](./transaction-diagnostics.md).

   ![Grafiek voor metrische servergegevens](./media/website-monitoring/browser-performance.png)

1. Selecteer in het hoofdmenu van Application Insights, onder de header **Gebruik** de optie [**Gebruikers**](./usage-segmentation.md) om aan de slag te gaan met het verkennen van de [hulpprogramma's voor analyse van het gedrag van gebruikers](./usage-overview.md). Omdat we vanaf één computer testen, zien we alleen gegevens voor één gebruiker. Voor een live website kan de verdeling van gebruikers er als volgt uitzien:

     ![Gebruikersgrafiek](./media/website-monitoring/usage-users.png)

1. Voor een complexere website met meerdere pagina's kunt u het hulpprogramma [**User Flows**](./usage-flows.md) gebruiken om de routes die bezoekers door de verschillende onderdelen van uw website bij te houden.

   ![Gebruikersstromen visualiseren](./media/website-monitoring/user-flows.png)

Zie de [naslagdocumentatie over de JavaScript SDK API](./javascript.md) als u meer wilt weten over geavanceerdere configuraties voor het controleren van websites.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om met extra quickstarts of zelfstudies te werken, moet u geen resources opschonen die u in deze quickstart hebt gemaakt. Anders kunt u de volgende stappen gebruiken om alle resources te verwijderen die door deze quickstart in Azure Portal zijn gemaakt.

> [!NOTE]
> Als u een bestaande resourcegroep gebruikt, werken de volgende instructies niet. In plaats daarvan kunt u gewoon de afzonderlijke Application Insights-resource verwijderen. Houd er rekening mee dat wanneer u een resourcegroep verwijdert, alle onderliggende resources die lid zijn van die groep ook worden verwijderd.

1. Selecteer in het linkermenu in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens **myResourceGroup** of de naam van uw tijdelijke resourcegroep.
1. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, voer **myResourceGroup** in het tekstvak in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](../logs/log-query-overview.md)

