---
title: Azure-toepassing Insights voor Java script-web-apps
description: Het ophalen van de pagina weergave en aantal sessies, webclientgegevens, toepassingen met één pagina (SPA) en het bijhouden van gebruiks patronen. Detecteer uitzonderingen en prestatieproblemen in JavaScript-webpagina's.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 60b3e9229adb93ce32c97c2822a465f7f629d47d
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234355"
---
# <a name="application-insights-for-web-pages"></a>Application Insights voor webpagina’s

Krijg inzicht in de prestaties en het gebruik van uw webpagina's of app. Als u [Application Insights](app-insights-overview.md) toevoegt aan uw pagina script, krijgt u de beschikking over de pagina laden en Ajax-aanroepen, tellingen en Details van browser uitzonderingen en Ajax-fouten, evenals de aantallen gebruikers en sessies. Al deze gegevens kunnen worden gesegmenteerd op pagina, clientbesturingssysteem en browserversie, geografische locatie en andere dimensies. U kunt waarschuwingen instellen voor foutaantallen of het langzaam laden van de pagina. En door het invoegen van trace-aanroepen in JavaScript-code, kunt u bijhouden hoe de verschillende functies van uw webpaginatoepassing worden gebruikt.

Application Insights kan met elke webpagina worden gebruikt. Het enige wat u hiervoor hoeft te doen, is een klein stukje JavaScript toevoegen. Als uw webservice [Java](java-get-started.md) of [ASP.net](asp-net.md)is, kunt u de sdk's aan de server zijde in combi natie met de Java script-SDK aan de client zijde gebruiken om een end-to-end-goed idee te krijgen van de prestaties van uw app.

## <a name="adding-the-javascript-sdk"></a>De Java script-SDK toevoegen

> [!IMPORTANT]
> Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./sdk-connection-string.md?tabs=js) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.

1. Eerst hebt u een Application Insights resource nodig. Als u nog geen resource en instrumentatie sleutel hebt, volgt u de [instructies een nieuwe resource maken](create-new-resource.md).
2. Kopieer de _instrumentatie sleutel_ (ook wel ' iKey ' genoemd) of [Connection String](#connection-string-setup) voor de resource waar u uw Java script-telemetrie wilt verzenden (uit stap 1.) U voegt deze toe aan de `instrumentationKey` of `connectionString` instelling van de Application Insights java script-SDK.
3. Voeg de Application Insights java script-SDK toe aan uw webpagina of app via een van de volgende twee opties:
    * [NPM-installatie](#npm-based-setup)
    * [Java script-fragment](#snippet-based-setup)

> [!IMPORTANT]
> Gebruik slechts één methode om de Java script-SDK toe te voegen aan uw toepassing. Als u de NPM-instellingen gebruikt, gebruikt u het fragment niet en vice versa.

> [!NOTE]
> NPM Setup installeert de Java script-SDK als een afhankelijkheid van uw project, waardoor IntelliSense, terwijl het fragment de SDK tijdens runtime ophaalt. Beide ondersteunen dezelfde functies. Ontwikkel aars die behoefte hebben aan meer aangepaste gebeurtenissen en configuratie, kiezen meestal voor NPM-Setup, terwijl gebruikers op zoek zijn naar snelle activering van out-of-the-box Web Analytics voor het fragment.

### <a name="npm-based-setup"></a>installatie op basis van NPM

Installeren via NPM.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **Typen zijn opgenomen in dit pakket**, dus **u hoeft geen** afzonderlijk type pakket te installeren.
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Installatie op basis van een fragment

Als uw app geen gebruik maakt van NPM, kunt u uw webpagina's rechtstreeks met Application Insights door middel van het plakken van dit fragment boven aan de pagina's. Bij voor keur moet het het eerste script in uw `<head>` sectie zijn, zodat alle potentiële problemen met al uw afhankelijkheden en eventueel java script-fouten kunnen worden gecontroleerd. Als u een razendsnelle server-app gebruikt, voegt u het fragment boven aan het bestand `_Host.cshtml` in de `<head>` sectie toe.

Om u te helpen bij het volgen van de versie van het fragment dat door uw toepassing wordt gebruikt, vanaf versie 2.5.5 de gebeurtenis pagina weergave bevat een nieuwe tag ' ai. internal. fragment ', waarin de geïdentificeerde fragment versie wordt opgenomen.

Het huidige fragment (hieronder weer gegeven) wordt aangeduid als versie 3.

```html
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

> [!NOTE]
> Voor de Lees baarheid en om mogelijke java script-fouten te verminderen, worden alle mogelijke configuratie opties vermeld op een nieuwe regel in code hierboven. Als u de waarde van een gefragmenteerde lijn niet wilt wijzigen, kunt u deze verwijderen.


#### <a name="reporting-script-load-failures"></a>Fouten bij het laden van scripts melden

Deze versie van het fragment detecteert en rapporteert fouten bij het laden van de SDK van het CDN als een uitzonde ring op de Azure Monitor-Portal (in de &gt; browser uitzonde ringen op fouten &gt; ), zodat u weet dat de toepassing geen telemetrie (of andere uitzonde ringen) rapporteert zoals verwacht. Dit signaal is een belang rijke meet tijd om te weten dat u telemetrie kwijt bent omdat de SDK niet is geladen of geïnitialiseerd, wat kan leiden tot:
- Onder-rapportage over hoe gebruikers uw site gebruiken (of proberen te gebruiken);
- Er ontbreekt een telemetrie voor het gebruik van uw site door uw eind gebruikers.
- Er ontbreken java script-fouten waardoor het mogelijk is dat uw eind gebruikers uw site gebruiken.

Ga voor meer informatie over deze uitzonde ring naar de pagina [SDK Load Failure](javascript-sdk-load-failure.md) Troubleshooting (Engelstalig).

Rapportage van deze fout als een uitzonde ring op de portal maakt geen gebruik van de configuratie optie ```disableExceptionTracking``` van de Application Insights-configuratie. als deze fout zich voordoet, wordt deze altijd gerapporteerd door het code fragment, zelfs wanneer de ondersteuning Window. onerror wordt uitgeschakeld.

Rapportage van problemen met SDK-belasting wordt specifiek niet ondersteund op IE 8 (of lager). Dit helpt bij het verminderen van de minified grootte van het fragment door ervan uit te gaan dat de meeste omgevingen niet uitsluitend IE 8 of minder zijn. Als u deze vereiste hebt en u deze uitzonde ringen wilt ontvangen, moet u een ophaal-poly vulling opnemen of uw eigen fragment versie maken die wordt gebruikt ```XDomainRequest``` in plaats van ```XMLHttpRequest``` . het wordt aanbevolen dat u de [gegeven bron code](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) voor het fragment als uitgangs punt gebruikt.

> [!NOTE]
> Als u een eerdere versie van het fragment gebruikt, wordt het ten zeerste aanbevolen dat u de nieuwste versie bijwerkt, zodat u deze eerder niet-gemelde problemen ontvangt.

#### <a name="snippet-configuration-options"></a>Opties voor fragment configuratie

Alle configuratie opties zijn nu aan het einde van het script geplaatst om te voor komen dat er per ongeluk java script-fouten worden geïntroduceerd waardoor de SDK niet kan worden geladen, maar ook de rapportage van de fout wordt uitgeschakeld.

Elke configuratie optie wordt hierboven op een nieuwe regel weer gegeven. Als u de standaard waarde van een item dat wordt weer gegeven als [Optioneel] niet wilt overschrijven, kunt u die regel verwijderen om de resulterende grootte van de geretourneerde pagina te minimaliseren.

De beschik bare configuratie opties zijn

| Naam | Type | Beschrijving
|------|------|----------------
| src | teken reeks **[vereist]** | De volledige URL van waaruit de SDK moet worden geladen. Deze waarde wordt gebruikt voor het kenmerk src van een dynamisch toegevoegd &lt; script/ &gt; label. U kunt de open bare CDN-locatie of uw eigen privé-hostserver gebruiken.
| naam | teken reeks *[Optioneel]* | De globale naam voor de geïnitialiseerde SDK, wordt standaard ingesteld op `appInsights` . Dit is dus ```window.appInsights``` een verwijzing naar het geïnitialiseerde exemplaar. Opmerking: als u een naam waarde opgeeft of een vorige instantie lijkt te zijn toegewezen (via de globale naam appInsightsSDK), wordt deze naam ook gedefinieerd in de globale naam ruimte, omdat ```window.appInsightsSDK=<name value>``` Dit vereist is voor de SDK-initialisatie code om ervoor te zorgen dat deze de juiste skelet-en proxy methoden van het fragment initialiseert en bijwerkt.
| ! | nummer in MS *[Optioneel]* | Hiermee wordt de laad vertraging gedefinieerd die moet worden gewacht voordat wordt geprobeerd de SDK te laden. De standaard waarde is 0ms en een negatieve waarde voegt onmiddellijk een script code toe aan het &lt; hoofd &gt; gebied van de pagina. vervolgens wordt de gebeurtenis voor het laden van de pagina geblokkeerd totdat het script wordt geladen (of mislukt).
| useXhr | Booleaanse waarde *[Optioneel]* | Deze instelling wordt alleen gebruikt voor het rapporteren van SDK-laad fouten. Reporting probeert eerst fetch (), indien beschikbaar en vervolgens terug te gebruiken naar XHR, om deze waarde in te stellen op True, alleen de ophaal controle te omzeilen. Het gebruik van deze waarde is alleen vereist als uw toepassing wordt gebruikt in een omgeving waarin ophalen de fout gebeurtenissen niet zou kunnen verzenden.
| crossOrigin | teken reeks *[Optioneel]* | Door deze instelling op te nemen, neemt de script code die is toegevoegd voor het downloaden van de SDK het kenmerk crossOrigin met deze teken reeks waarde. Wanneer niet gedefinieerd (de standaard instelling), wordt er geen crossOrigin-kenmerk toegevoegd. Er zijn geen aanbevolen waarden gedefinieerd (de standaard instelling). ""; of anoniem (voor alle geldige waarden raadpleegt u [HTML-kenmerk `crossorigin` :](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) documentatie)
| configuratie | object **[vereist]** | De configuratie die wordt door gegeven aan de Application Insights SDK tijdens de initialisatie.

### <a name="connection-string-setup"></a>Verbindingsteken reeks instellen

Voor de installatie van NPM of fragment kunt u uw exemplaar van Application Insights ook configureren met behulp van een verbindings reeks. Vervang gewoon het `instrumentationKey` veld door het `connectionString` veld.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Telemetrie verzenden naar de Azure Portal

Standaard verzamelt de Application Insights java script SDK een aantal telemetriegegevens die nuttig zijn bij het bepalen van de status van uw toepassing en de onderliggende gebruikers ervaring. Deze omvatten:

- Niet- **onderschepte uitzonde ringen** in uw app, inclusief informatie over
    - Stack tracering
    - Details van uitzonde ring en bericht dat de fout vergezelt
    - Regel & kolom aantal fouten
    - URL waar de fout is opgetreden
- Aanvragen voor **netwerk afhankelijkheden** die zijn gemaakt door uw app **xhr** en **Fetch** (de verzameling ophalen is standaard uitgeschakeld), bevatten informatie over
    - URL van afhankelijkheids bron
    - Opdracht & methode voor het aanvragen van de afhankelijkheid
    - Duur van de aanvraag
    - Resultaat code en succes status van de aanvraag
    - ID (indien van toepassing) van de gebruiker die de aanvraag maakt
    - Correlatie context (indien aanwezig) waar de aanvraag is ingediend
- **Gebruikers informatie** (bijvoorbeeld locatie, netwerk, IP)
- **Apparaatgegevens** (bijvoorbeeld browser, besturings systeem, versie, taal, model)
- **Sessiegegevens**

### <a name="telemetry-initializers"></a>Initialisatie functies voor telemetrie
Initialisatie functies voor telemetrie worden gebruikt om de inhoud van de verzamelde telemetrie te wijzigen voordat ze worden verzonden vanuit de browser van de gebruiker. Ze kunnen ook worden gebruikt om te voor komen dat bepaalde telemetriegegevens worden verzonden door te retour neren `false` . U kunt meerdere telemetrie-initialisatie functies toevoegen aan uw Application Insights-exemplaar en ze worden uitgevoerd in de volg orde waarin ze worden toegevoegd.

Het invoer argument voor `addTelemetryInitializer` is een call back die een [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) als-argument gebruikt en een `boolean` or retourneert `void` . Als er `false` wordt geretourneerd, wordt het telemetrie-item niet verzonden. anders wordt het teruggestuurd naar de volgende telemetrie-initialisatie functie, indien van toepassing, of verzonden naar het eind punt van de telemetrie-verzameling.

Een voor beeld van het gebruik van telemetrie-initialisatie functies:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Configuratie
De meeste configuratie velden hebben de naam zo, dat ze standaard kunnen worden ingesteld op ONWAAR. Alle velden zijn optioneel, behalve voor `instrumentationKey` .

| Naam | Standaard | Beschrijving |
|------|---------|-------------|
| instrumentationKey | null | **Vereist**<br>Instrumentatie sleutel die u hebt verkregen van de Azure Portal. |
| accountId | null | Een optionele account-ID als uw app gebruikers in accounts groepeert. Geen spaties, komma's, punt komma's, is gelijk aan of verticale balken |
| sessionRenewalMs | 1800000 | Er wordt een sessie geregistreerd als de gebruiker gedurende deze tijd inactief is in milliseconden. De standaard waarde is 30 minuten |
| sessionExpirationMs | 86400000 | Een sessie wordt geregistreerd als deze de tijd in milliseconden heeft geduurd. De standaard waarde is 24 uur |
| maxBatchSizeInBytes | 10.000 | De maximale grootte van de telemetrie batch. Als een batch deze limiet overschrijdt, wordt deze onmiddellijk verzonden en wordt een nieuwe batch gestart |
| maxBatchInterval | 15.000 | Hoe lang batch-telemetrie voor verzen ding (in milliseconden) |
| disableExceptionTracking | onjuist | Als deze eigenschap waar is, worden uitzonde ringen niet verzameld. De standaardinstelling is onwaar. |
| disableTelemetry | onjuist | Indien waar, wordt de telemetrie niet verzameld of verzonden. De standaardinstelling is onwaar. |
| enableDebug | onjuist | Indien waar, worden **interne** fout opsporingsgegevens gegenereerd als een uitzonde ring **in plaats** van vastgelegd, ongeacht de instellingen voor de logboek registratie van de SDK. De standaardinstelling is onwaar. <br>**_Opmerking:_* _ als u deze instelling inschakelt, wordt de telemetrie verwijderd wanneer er een interne fout optreedt. Dit kan handig zijn voor het snel identificeren van problemen met uw configuratie of het gebruik van de SDK. Als u geen telemetriegegevens wilt verliezen tijdens het opsporen van fouten, kunt u overwegen `consoleLoggingLevel` of `telemetryLoggingLevel` in plaats van te gebruiken `enableDebug` . |
| loggingLevelConsole | 0 | Logboeken _ *interne** Application Insights fouten naar de console. <br>0: uit, <br>1: alleen kritieke fouten, <br>2: alles (fouten & waarschuwingen) |
| loggingLevelTelemetry | 1 | Hiermee worden **interne** Application Insights-fouten als telemetriegegevens verzonden. <br>0: uit, <br>1: alleen kritieke fouten, <br>2: alles (fouten & waarschuwingen) |
| diagnosticLogInterval | 10.000 | binnen Polling-interval (in MS) voor interne logboek registratie wachtrij |
| samplingPercentage | 100 | Het percentage gebeurtenissen dat wordt verzonden. De standaard waarde is 100, wat betekent dat alle gebeurtenissen worden verzonden. Stel deze waarde in als u uw gegevens limiet voor grootschalige toepassingen wilt behouden. |
| autoTrackPageVisitTime | onjuist | Indien true, op een pagina weergave, wordt de vorige gestuurde pagina tijd bijgehouden als telemetrie en wordt er een nieuwe timer gestart voor de huidige pagina weergave. De standaardinstelling is onwaar. |
| disableAjaxTracking | onjuist | Indien true, worden Ajax-aanroepen niet autoinnen. De standaardinstelling is onwaar. |
| disableFetchTracking | true | Indien waar, worden aanvragen voor ophalen niet meer verzameld. De standaard waarde is True |
| overridePageViewDuration | onjuist | Als deze eigenschap waar is, wordt het standaard gedrag van trackPageView gewijzigd om het eind punt van de pagina weergave te registreren wanneer trackPageView wordt aangeroepen. Als deze eigenschap onwaar is en er geen aangepaste duur wordt gegeven aan trackPageView, wordt de prestaties van de pagina weergave berekend met behulp van de API voor navigatie tijd. De standaardinstelling is onwaar. |
| maxAjaxCallsPerView | 500 | Standaard 500-bepaalt hoeveel Ajax-aanroepen worden bewaakt per pagina weergave. Stel deze waarde in op-1 om alle Ajax-aanroepen (onbeperkt) op de pagina te bewaken. |
| disableDataLossAnalysis | true | Indien onwaar, worden interne telemetriegegevens van de verzender bij het opstarten gecontroleerd op items die nog niet zijn verzonden. |
| disableCorrelationHeaders | onjuist | Als deze eigenschap onwaar is, voegt de SDK twee headers (' aanvraag-id ' en ' request-context ') toe aan alle afhankelijkheids aanvragen om ze te correleren met bijbehorende aanvragen aan de server zijde. De standaardinstelling is onwaar. |
| correlationHeaderExcludedDomains |  | Correlatie headers voor specifieke domeinen uitschakelen |
| correlationHeaderDomains |  | Correlatie headers voor specifieke domeinen inschakelen |
| disableFlushOnBeforeUnload | onjuist | De standaard waarde is False. Indien waar, wordt de methode flush niet aangeroepen wanneer onBeforeUnload gebeurtenis triggers |
| enableSessionStorageBuffer | true | Standaard waarde waar. Als deze eigenschap waar is, wordt de buffer met alle niet-verzonden telemetrie opgeslagen in de sessie opslag. De buffer wordt hersteld bij het laden van pagina's |
| isCookieUseDisabled | onjuist | De standaard waarde is False. Als deze eigenschap waar is, worden er geen gegevens van cookies opgeslagen of gelezen met de SDK. Houd er rekening mee dat hiermee de gebruikers-en sessie cookies worden uitgeschakeld en de Blade gebruiks weergaven worden weer gegeven. |
| cookieDomain | null | Aangepast cookie domein. Dit is handig als u Application Insights cookies wilt delen in subdomeinen. |
| isRetryDisabled | onjuist | De standaard waarde is False. Indien onwaar, opnieuw proberen op 206 (gedeeltelijk geslaagd), 408 (time-out), 429 (te veel aanvragen), 500 (interne server fout), 503 (service niet beschikbaar) en 0 (alleen offline, alleen indien gedetecteerd) |
| isStorageUseDisabled | onjuist | Als deze eigenschap waar is, worden er geen gegevens uit lokale en sessie opslag opgeslagen of gelezen met de SDK. De standaardinstelling is onwaar. |
| isBeaconApiDisabled | true | Als deze eigenschap onwaar is, verzendt de SDK alle telemetrie met behulp van de [Beacon-API](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | onjuist | De standaard waarde is False. Wanneer het tabblad wordt gesloten, verzendt de SDK alle resterende telemetrie met behulp van de [Beacon-API](https://www.w3.org/TR/beacon) |
| sdkExtension | null | Hiermee stelt u de naam van de SDK-extensie. Alleen alfabetische tekens zijn toegestaan. De naam van de extensie wordt toegevoegd als een voor voegsel voor de tag ' ai. internal. sdkVersion ' (bijvoorbeeld ' ext_javascript: 2.0.0 '). De standaard waarde is null. |
| isBrowserLinkTrackingEnabled | onjuist | De standaardinstelling is onwaar. Indien waar, worden alle aanvragen voor [browser koppelingen](/aspnet/core/client-side/using-browserlink) door de SDK bijgehouden. |
| appId | null | AppId wordt gebruikt voor de correlatie tussen AJAX-afhankelijkheden die zich voordoen aan de client zijde met de aanvragen aan de server zijde. Als Beacon-API is ingeschakeld, kan deze niet automatisch worden gebruikt, maar deze kan hand matig worden ingesteld in de configuratie. De standaard waarde is null |
| enableCorsCorrelation | onjuist | Als deze eigenschap waar is, voegt de SDK twee headers (' aanvraag-id ' en ' request-context ') toe aan alle CORS-aanvragen voor het correleren van uitgaande AJAX-afhankelijkheden met bijbehorende aanvragen aan de server zijde. De standaard waarde is False |
| namePrefix | gedefinieerde | Een optionele waarde die wordt gebruikt als naam achtervoegsel voor localStorage en cookie naam.
| enableAutoRouteTracking | onjuist | Route wijzigingen automatisch bijhouden in toepassingen met één pagina (SPA). Indien waar, stuurt elke route wijziging een nieuwe pagina weergave naar Application Insights. Hash-route wijzigingen ( `example.com/foo#bar` ) worden ook vastgelegd als nieuwe pagina weergaven.
| enableRequestHeaderTracking | onjuist | Indien waar, worden AJAX-& aanvraag headers voor ophalen bijgehouden. de standaard waarde is False.
| enableResponseHeaderTracking | onjuist | Als deze waarde True is, wordt de antwoord headers voor de aanvraag van AJAX & ophalen bijgehouden. de standaard waarde is False.
| distributedTracingMode | `DistributedTracingModes.AI` | Hiermee stelt u de gedistribueerde traceer modus in. Als AI_AND_W3C modus of de W3C-modus is ingesteld, worden W3C-tracering context headers (traceparent/tracestate) gegenereerd en opgenomen in alle uitgaande aanvragen. AI_AND_W3C is voorzien van back-compatibiliteit met alle verouderde services die zijn Application Insights instrumented. Zie [hier](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)voor beeld.
| enableAjaxErrorStatusText | onjuist | De standaard waarde is False. Indien waar, wordt tekst met een reactie fout gegevens in afhankelijkheids gebeurtenis ingevoegd op mislukte AJAX-aanvragen.
| enableAjaxPerfTracking | onjuist | De standaard waarde is False. Vlag waarmee u het opzoeken van het aanvullende browser venster kunt inschakelen. de prestatie-instellingen in de gerapporteerde `ajax` (xhr en Fetch) gerapporteerde metrische gegevens.
| maxAjaxPerfLookupAttempts | 3 | Standaardwaarde is 3. Het maximum aantal keer dat er naar het venster moet worden gekeken. prestatie tijd (indien beschikbaar). Dit is vereist omdat niet alle browsers het venster invullen. prestaties vóór het rapporteren van het einde van de XHR-aanvraag en voor aanvragen voor ophalen wordt deze toegevoegd na het volt ooien.
| ajaxPerfLookupDelay | 25 | De standaard waarde is 25 MS. De hoeveelheid tijd die moet worden gewacht voordat opnieuw wordt geprobeerd om de Vensters te vinden. prestatie-instellingen voor een `ajax` aanvraag, tijd is in milliseconden en wordt direct door gegeven aan setTimeout ().
| enableUnhandledPromiseRejectionTracking | onjuist | Indien waar, worden niet-verwerkte beloofings afwijzingen automatisch verzameld en gerapporteerd als Java script-fout. Wanneer disableExceptionTracking is ingesteld op True (geen uitzonde ringen bijhouden), wordt de configuratie waarde genegeerd en worden niet-verwerkte beloofe weigeringen niet gerapporteerd.

## <a name="enable-time-on-page-tracking"></a>Bijhouden van tijd op pagina inschakelen

Als u deze instelling inschakelt `autoTrackPageVisitTime: true` , wordt de tijd die een gebruiker aan elke pagina besteed, bijgehouden. Bij elke nieuwe pagina weergave wordt de duur die de gebruiker op de *vorige* pagina heeft besteed, verzonden als een [aangepaste metriek](../platform/metrics-custom-overview.md) met de naam `PageVisitTime` . Deze aangepaste metriek kan worden weer gegeven in de [Metrics Explorer](../platform/metrics-getting-started.md) als een ' op logboek gebaseerde metriek '.

## <a name="enable-correlation"></a>Correlatie inschakelen

Correlatie genereert en verzendt gegevens die gedistribueerde tracering mogelijk maken en de [toepassings toewijzing](../app/app-map.md), de [end-to-end-transactie weergave](../app/app-map.md#go-to-details)en andere diagnostische hulpprogram ma's.

In het volgende voor beeld ziet u alle mogelijke configuraties die vereist zijn voor het inschakelen van correlatie, met hieronder scenario-specifieke opmerkingen:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Als een van de servers van derden waarmee de client communiceert `Request-Id` , de-en-headers niet kan accepteren `Request-Context` en u de configuratie ervan niet kunt bijwerken, moet u ze in een uitsluitings lijst plaatsen via de `correlationHeaderExcludeDomains` configuratie-eigenschap. Deze eigenschap ondersteunt joker tekens.

Aan de server zijde moeten verbindingen met deze headers zijn toegestaan. Afhankelijk van de `Access-Control-Allow-Headers` configuratie van de server is het vaak nodig om de lijst aan de server zijde uit te breiden door hand matig toe te voegen `Request-Id` en `Request-Context` .

Access-Control-Allow-headers: `Request-Id` , `Request-Context` , `<your header>`

> [!NOTE]
> Als u gebruikmaakt van OpenTelemtry of Application Insights Sdk's die zijn uitgebracht in 2020 of hoger, kunt u het beste [WC3 tracering voor](https://www.w3.org/TR/trace-context/)gebruiken. Zie configuratie-instructies [hier](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).

## <a name="single-page-applications"></a>Toepassingen met één pagina

Deze SDK verwerkt standaard **geen** route wijzigingen op basis van status die optreden in toepassingen met één pagina. Als u het bijhouden van automatische route wijzigingen wilt inschakelen voor uw toepassing met één pagina, kunt u `enableAutoRouteTracking: true` aan uw installatie configuratie toevoegen.

Op dit moment bieden we een afzonderlijke [reageer-plugin](javascript-react-plugin.md), die u kunt initialiseren met deze SDK. Ook wordt het bijhouden van route wijzigingen voor u uitgevoerd, evenals andere gereageerde telemetrie.
> [!NOTE]
> Gebruik `enableAutoRouteTracking: true` alleen als u de **niet** -reagerende invoeg toepassing niet gebruikt. Beide kunnen nieuwe page views verzenden wanneer de route verandert. Als beide zijn ingeschakeld, kan dubbele page views worden verzonden.

## <a name="extensions"></a>Extensies

| Extensies |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Klik op analyse automatisch verzamelen](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Browser/gegevens van client zijde verkennen

De browser/client-side gegevens kunnen worden weer gegeven door te gaan naar **metrieken** en afzonderlijke metrieken toe te voegen:

![Scherm afbeelding van de pagina metrische gegevens in Application Insights grafische weer gaven van de statistieken voor een webtoepassing weer geven.](./media/javascript/page-view-load-time.png)

U kunt uw gegevens ook weer geven via de Java script-SDK via de browser ervaring in de portal.

Selecteer **browser** en kies vervolgens **fouten** of **prestaties**.

![Scherm opname van de browser pagina in Application Insights laat zien hoe browser fouten of browser prestaties kunnen worden toegevoegd aan de metrische gegevens die u kunt weer geven voor uw webtoepassing.](./media/javascript/browser.png)

### <a name="performance"></a>Prestaties

![Scherm afbeelding van de pagina prestaties in Application Insights grafische weer gaven van metrische bewerkingen voor een webtoepassing weer geven.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Afhankelijkheden

![Scherm afbeelding van de pagina prestaties in Application Insights grafische weer gaven van afhankelijkheids gegevens weer geven voor een webtoepassing.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analyse

Als u een query wilt uitvoeren op uw telemetrie die is verzameld door de Java script-SDK, selecteert u de knop **weer gave op Logboeken (analyse)** Door een instructie toe te voegen `where` `client_Type == "Browser"` , ziet u alleen gegevens van de Java script-SDK en alle telemetrie aan de server zijde die door andere sdk's zijn verzameld, worden uitgesloten.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Ondersteuning voor bron toewijzing

De minified-call stack van uw telemetrie van de uitzonde ring kan worden unminified in de Azure Portal. Alle bestaande integraties in het deel venster uitzonderings Details werken met de nieuwe unminified-opgeroepen procedures.

#### <a name="link-to-blob-storage-account"></a>Koppeling naar Blob Storage-account

U kunt uw Application Insights-resource koppelen aan uw eigen Azure Blob Storage-container om automatisch unminify te roepen. Zie [automatische bron toewijzing ondersteuning](./source-map-support.md)om aan de slag te gaan.

### <a name="drag-and-drop"></a>Slepen en neerzetten

1. Selecteer een telemetrie voor uitzonde ringen in de Azure Portal om de ' end-to-end-transactie details weer te geven '
2. Identificeer welke bron kaarten overeenkomen met deze aanroep stack. De bron toewijzing moet overeenkomen met het bron bestand van een stack frame, maar een achtervoegsel met `.map`
3. Sleep en zet de bron kaarten neer op de aanroep stack in de Azure Portal ![ een bewegende afbeelding die laat zien hoe u bron toewijzings bestanden vanuit een build-map kunt slepen en neerzetten in het stack venster met opgeroepen procedures in de Azure Portal.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Voor een licht gewicht-ervaring kunt u in plaats daarvan de basis versie van Application Insights installeren
```
npm i --save @microsoft/applicationinsights-web-basic
```
Deze versie wordt geleverd met het minimale aantal functies en functionaliteiten en is afhankelijk van u om het te kunnen bouwen. Er wordt bijvoorbeeld geen verzameling (niet-onderschepte uitzonde ringen, AJAX, enzovoort) uitgevoerd. De Api's voor het verzenden van bepaalde typen telemetrie, zoals `trackTrace` , `trackException` , enzovoort, zijn niet opgenomen in deze versie, dus u moet uw eigen wrapper opgeven. De enige API die beschikbaar is is `track` . Hier vindt u een voor [beeld](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) .

## <a name="examples"></a>Voorbeelden

Zie [Application Insights java script SDK](https://github.com/Azure-Samples?q=applicationinsights-js-demo)-voor beelden voor uitvoer bare.

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Een upgrade uitvoeren van de oude versie van Application Insights

Belang rijke wijzigingen in de SDK v2-versie:
- Als u betere API-hand tekeningen wilt toestaan, zijn een aantal van de API-aanroepen, zoals trackPageView en trackException, bijgewerkt. Uitvoeren in Internet Explorer 8 en eerdere versies van de browser wordt niet ondersteund.
- De telemetrie-envelop heeft veld naam-en structuur wijzigingen door gegevens schema-updates.
- Verplaatst `context.operation` naar `context.telemetryTrace` . Sommige velden zijn ook gewijzigd ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Als u de huidige pagina weergave-ID hand matig wilt vernieuwen (bijvoorbeeld in SPA-apps), gebruikt u `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > Als u de tracerings-ID uniek wilt laten, waar u eerder hebt gebruikt `Util.newId()` , gebruikt u nu `Util.generateW3CId()` . Beide uiteindelijk eindigen op de bewerkings-ID.

Als u de huidige Application Insights PRODUCTION SDK (1.0.20) gebruikt en wilt zien of de nieuwe SDK in runtime werkt, werkt u de URL bij, afhankelijk van het huidige scenario voor het laden van de SDK.

- Down load via CDN scenario: werk het code fragment dat u momenteel gebruikt om naar de volgende URL te verwijzen:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- NPM scenario: aanroep `downloadAndSetup` voor het downloaden van het volledige ApplicationInsights-script uit CDN en Initialiseer het met de instrumentatie sleutel:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test in interne omgeving om te controleren of telemetrie van de bewaking naar verwachting werkt. Als dat niet het geval is, werkt u de API-hand tekeningen goed bij naar SDK versie 2 en implementeert u deze in uw productie omgevingen.

## <a name="sdk-performanceoverhead"></a>SDK-prestaties/overhead

Application Insights een verschuldigde hoeveelheid van loadtime aan uw website toevoegt, en slechts ~ 15 MS kan worden geïnitialiseerd met 36 KB-gzipped. Met behulp van het fragment worden minimale onderdelen van de bibliotheek snel geladen. Ondertussen wordt het volledige script gedownload op de achtergrond.

Tijdens het downloaden van het script uit het CDN, wordt het bijhouden van de pagina in een wachtrij geplaatst. Zodra het gedownloade script asynchroon is geïnitialiseerd, worden alle gebeurtenissen bijgehouden die in de wachtrij zijn geplaatst. Als gevolg hiervan zult u geen telemetrie kwijt raken tijdens de hele levens cyclus van de pagina. Dit installatie proces voorziet uw pagina van een naadloos analyse systeem, dat niet zichtbaar is voor uw gebruikers.

> Samenvatting:
> - ![NPM-versie](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![grootte van gzip-compressie](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 MS** algemene initialisatie tijd
> - Bijhouden van **nul** gemist tijdens de levens cyclus van de pagina

## <a name="browser-support"></a>Browserondersteuning

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Nieuwste ✔ van Chrome |  Nieuwste ✔ in Firefox | &-rand ✔ voor IE 9 +<br>IE 8-compatibel | Nieuwste ✔ | Nieuwste ✔ voor Safari |

## <a name="es3ie8-compatibility"></a>Compatibiliteit met ES3/IE8

Als SDK zijn er talrijke gebruikers die de browsers die hun klanten gebruiken niet kunnen beheren. Daarom moeten we ervoor zorgen dat deze SDK blijft werken en niet de JS-uitvoering verbreekt wanneer deze door een oudere browser wordt geladen. Hoewel dit ideaal is voor het ondersteunen van IE8 en ES3-browsers (oudere generatie), zijn er talrijke grote klanten/gebruikers die pagina's blijven gebruiken voor ' werk ' en, zoals vermeld, ze kunnen of kunnen niet bepalen welke browser de eind gebruikers kiezen.

Dit betekent niet dat we alleen ondersteuning bieden voor de laagste common set functies, maar dat we de compatibiliteit met ES3-code moeten behouden en wanneer ze nieuwe functies toevoegen die ze moeten toevoegen op een manier die het parseren van ES3 java script niet verbreekt en als een optionele functie is toegevoegd.

[Zie GitHub voor volledige informatie over de ondersteuning van IE8](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>Open-Source-SDK

De Application Insights java script SDK is open source om de bron code weer te geven of om bij te dragen aan het project Ga naar de [officiële github-opslag plaats](https://github.com/Microsoft/ApplicationInsights-JS). 

[Raadpleeg de opmerkingen bij de release](./release-notes.md)voor de nieuwste updates en oplossingen voor problemen.

## <a name="next-steps"></a><a name="next"></a> Volgende stappen
* [Bijhouden van gebruik](usage-overview.md)
* [Aangepaste gebeurtenissen en metrische gegevens](api-custom-events-metrics.md)
* [Bouwen-meten-leren](usage-overview.md)
* [Problemen met SDK-belasting oplossen](javascript-sdk-load-failure.md)
