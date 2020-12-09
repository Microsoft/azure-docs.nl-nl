---
title: Node.js-services bewaken met Azure Application Insights | Microsoft Docs
description: Prestaties bewaken en problemen detecteren in Node.js-services met Application Insights.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7aea6c03b0ce35fa0e74c39ff5f94f714447ad6f
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920582"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Node.js-services en -apps bewaken met Application Insights

[Application Insights](./app-insights-overview.md) controleert uw back-end-services en-onderdelen na de implementatie, zodat u de prestaties en andere problemen snel kunt ontdekken en analyseren. U kunt Application Insights gebruiken voor Node.js-services die worden gehost in uw Data Center, Azure-Vm's en web-apps, en zelfs in andere open bare Clouds.

Neem de SDK op in de code en stel vervolgens een bijbehorende Application Insights-resource in Azure in om uw bewakingsgegevens te ontvangen, op te slaan en te onderzoeken. Met de SDK worden gegevens naar deze resource verzonden voor verdere analyse en onderzoek.

De Node.js SDK kan binnenkomende en uitgaande HTTP-aanvragen, uitzonderingen en verschillende systeemmeetgegevens automatisch bewaken. Vanaf versie 0,20 kan de SDK ook enkele algemene [pakketten van derden](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)bewaken, zoals MongoDb, MySQL en redis. Alle gebeurtenissen met betrekking tot een binnenkomende HTTP-aanvraag worden gecorreleerd voor snellere probleemoplossing.

U kunt de TelemetryClient-API gebruiken om handmatig aanvullende aspecten van de app en het systeem te instrumenteren en te bewaken. De TelemetryClient-API wordt verderop in dit artikel meer gedetailleerd beschreven.

## <a name="get-started"></a>Aan de slag

Voltooi de volgende taken om de bewaking voor een app of service in te stellen.

### <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u een Azure-abonnement hebt of moet u [een gratis nieuw abonnement aanvragen][azure-free-offer]. Als uw organisatie al een Azure-abonnement heeft, kan een beheerder [deze instructies][add-aad-user] volgen om u eraan toe te voegen.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Een Application Insights resource instellen

1. Meld u aan bij [Azure Portal][portal].
2. [Create an Application Insights resource](create-new-resource.md) (Een Application Insights-resource maken)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> De Node.ja SDK instellen

Neem de SKD op in de app zodat gegevens kunnen worden verzameld.

> [!IMPORTANT]
> Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./sdk-connection-string.md?tabs=nodejs) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.

1. Kopieer de instrumentatie sleutel van uw resource (ook wel *iKey* genoemd) van de zojuist gemaakte resource. Application Insights gebruikt de iKey om de gegevens toe te wijzen aan de Azure-resource. Voordat de SDK uw iKey kan gebruiken, moet u de iKey opgeven in een omgevingsvariabele of in uw code.  

   ![Instrumentatiesleutel kopiëren](./media/nodejs/instrumentation-key-001.png)

2. Voeg de Node.js SDK-bibliotheek toe aan de afhankelijkheden van de app via package.json. Voer in de hoofdmap van uw app de volgende opdracht uit:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Als u type script gebruikt, mag u geen afzonderlijke pakketten met ' typen ' installeren. Dit NPM-pakket bevat ingebouwde typings.

3. Laad de bibliotheek expliciet in de code. Laad de bibliotheek zo vroeg mogelijk, zelfs vóór andere `require`-instructies, omdat de SDK instrumentatie in veel andere bibliotheken injecteert.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  U kunt ook een iKey via de omgevings variabele opgeven `APPINSIGHTS_INSTRUMENTATIONKEY` , in plaats van deze hand matig door te geven aan  `setup()` of `new appInsights.TelemetryClient()` . Met deze procedure kunt u iKeys buiten vastgelegde broncode houden en kunt u verschillende iKeys opgeven voor verschillende omgevingen. Hand matig aanroepen configureren `appInsights.setup('[your ikey]');` .

    Zie de volgende secties voor extra configuratieopties.

    U kunt de SDK uitproberen zonder telemetrie te verzenden door `appInsights.defaultClient.config.disableAppInsights = true` in te stellen.

5. Automatisch gegevens verzamelen en verzenden door aan te roepen `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Uw app controleren

De SDK verzamelt automatisch telemetrie over de Node.js runtime en enkele algemene modules van derden. Gebruik de toepassing om een aantal van deze gegevens te genereren.

Ga vervolgen in [Azure Portal][portal] naar de Application Insights-resource die u hebt gemaakt. Zoek in de **Overzichtstijdlijn** naar de eerste gegevenspunten. Selecteer verschillende onderdelen in de grafieken om meer gedetailleerde gegevens te zien.

U kunt de [toepassings toewijzing](app-map.md)gebruiken om de topologie weer te geven die voor uw app is gedetecteerd.

#### <a name="no-data"></a>Geen gegevens

Er kan vertraging optreden voordat items worden weergegeven in de portal omdat met de SDK gegevens in batches worden geplaatst voor verzending. Als u geen gegevens ziet in de resource, kunt u de volgende oplossingen proberen:

* Ga door met het gebruiken van de toepassing. Neem meer acties om meer telemetrie te genereren.
* Klik op **Vernieuwen** in de resourceweergave van de portal. Grafieken worden periodiek automatisch gegeneerd, maar als u ze handmatig vernieuwt, worden ze onmiddellijk vernieuwd.
* Controleer of de [vereiste uitgaande poorten](./ip-addresses.md) open zijn.
* Gebruik [Zoeken](./diagnostic-search.md) om naar specifieke gebeurtenissen te zoeken.
* Raadpleeg de [Veelgestelde vragen][FAQ].

## <a name="basic-usage"></a>Basis gebruik

Voor een out-of-the-box verzameling van HTTP-aanvragen, populaire bibliotheek gebeurtenissen van derden, niet-verwerkte uitzonde ringen en systeem metrieken:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Als de instrumentatie sleutel is ingesteld in de omgevings variabele `APPINSIGHTS_INSTRUMENTATIONKEY` , `.setup()` kan zonder argumenten worden aangeroepen. Dit maakt het eenvoudig om verschillende ikeys te gebruiken voor verschillende omgevingen.

Laad zo snel mogelijk de Application Insights-bibliotheek `require("applicationinsights")` in uw scripts voordat u andere pakketten laadt. Dit is nodig om ervoor te zorgen dat de Application Insights-bibliotheek later pakketten kan voorbereiden voor tracering. Als u conflicten ondervindt met andere bibliotheken die een soort gelijke voor bereiding ondergaan, kunt u proberen de Application Insights-bibliotheek te laden.

Vanwege de manier waarop Java script Call backs afhandelt, is extra werk vereist voor het volgen van een aanvraag voor externe afhankelijkheden en latere retour aanroepen. Deze aanvullende tracking is standaard ingeschakeld. Schakel deze optie uit door aanroepen `setAutoDependencyCorrelation(false)` zoals beschreven in de onderstaande [configuratie](#sdk-configuration) sectie.

## <a name="migrating-from-versions-prior-to-022"></a>Migreren van versies voorafgaand aan 0,22

Eerdere versies van versie 0,22 en later worden gewijzigd. Deze wijzigingen zijn ontworpen om consistentie met andere Application Insights Sdk's te bieden en toekomstige uitbreid baarheid mogelijk te maken.

Over het algemeen kunt u migreren met het volgende:

- Vervang verwijzingen naar `appInsights.client` with `appInsights.defaultClient` .
- Verwijzingen `appInsights.getClient()` vervangen door `new appInsights.TelemetryClient()`
- Vervang alle argumenten door client. track *-methoden met één object dat benoemde eigenschappen bevat als argumenten. Zie het ingebouwde type hint of [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) van uw IDE voor het uitgezonderde object voor elk type telemetrie.

Als u toegang hebt tot de SDK-configuratie functies zonder ze aan te koppelen `appInsights.setup()` , kunt u deze functies nu vinden `appInsights.Configurations` (bijvoorbeeld `appInsights.Configuration.setAutoCollectDependencies(true)` ). Bekijk de wijzigingen in de standaard configuratie in de volgende sectie.

## <a name="sdk-configuration"></a>SDK-configuratie

Het `appInsights` object biedt een aantal configuratie methoden. Ze worden weer gegeven in het volgende code fragment met hun standaard waarden.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Om gebeurtenissen in een service volledig te correleren, moet u `.setAutoDependencyCorrelation(true)` instellen. Als deze optie is ingesteld, kan met de SDK de context tussen asynchrone callbacks in Node.js worden getraceerd.

Bekijk hun beschrijvingen in het ingebouwde type van de IDE of [applicationinsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) voor gedetailleerde informatie over dit besturings element en optionele secundaire argumenten.

> [!NOTE]
>  Standaard `setAutoCollectConsole` is geconfigureerd om aanroepen naar *exclude* `console.log` (en andere console methoden) uit te sluiten. Alleen aanroepen naar ondersteunde logboeken van derden (bijvoorbeeld Winston en Bunyan) worden verzameld. U kunt dit gedrag wijzigen om oproepen naar methoden op te vragen met `console` behulp van `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>Steekproeven

Standaard worden alle verzamelde gegevens naar de Application Insights-service verzonden met de SDK. Als u veel gegevens verzamelt, wilt u mogelijk steek proeven inschakelen om de hoeveelheid verzonden gegevens te verminderen. Stel het `samplingPercentage` veld in op het `config` object van een client om dit te bereiken. `samplingPercentage`Als u instelt op 100 (de standaard instelling), worden alle gegevens verzonden en 0 betekent dat er niets wordt verzonden.

Als u automatische correlatie gebruikt, worden alle gegevens die zijn gekoppeld aan een enkele aanvraag, opgenomen of uitgesloten als een eenheid.

Voeg de volgende code toe om steek proeven in te scha kelen:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Meerdere rollen voor toepassingen met meerdere onderdelen

Als uw toepassing bestaat uit meerdere onderdelen die u wilt instrumenteren met dezelfde instrumentatie sleutel en deze onderdelen nog steeds als afzonderlijke eenheden worden weer geven in de portal, alsof ze gebruikmaken van afzonderlijke instrumentatie sleutels (bijvoorbeeld als afzonderlijke knoop punten op het toepassings overzicht), moet u het veld rolnaam wellicht hand matig configureren om onderscheid te maken tussen de telemetrie van het ene onderdeel en andere componenten die gegevens verzenden naar uw Application Insights-resource.

Gebruik het volgende om het veld rolnaam in te stellen:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Automatische instrumentatie van derden

Sommige wijzigingen zijn vereist in bibliotheken van derden, zoals MongoDB en redis, om de context van asynchrone aanroepen bij te houden. Application Insights wordt standaard gebruikt [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) om aap te gebruiken-patch enkele van deze bibliotheken. U kunt dit uitschakelen door de `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` omgevings variabele in te stellen.

> [!NOTE]
> Door die omgevings variabele in te stellen, is het mogelijk dat er geen gebeurtenissen meer aan de juiste bewerking worden gekoppeld.

 Afzonderlijke aap: patches kunnen worden uitgeschakeld door de `APPLICATION_INSIGHTS_NO_PATCH_MODULES` omgevings variabele in te stellen op een lijst met door komma's gescheiden pakketten die moeten worden uitgeschakeld (bijvoorbeeld) om te `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` voor komen dat patches `console` en pakketten worden geïnstalleerd `redis` .

Momenteel zijn er negen pakketten die worden beinstrumented: `bunyan` ,, `console` ,,,, `mongodb` `mongodb-core` `mysql` `redis` `winston` , `pg` en `pg-pool` . Ga naar het [Leesmij-bestand Diagnostic-Channel-uitgevers](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) voor informatie over precies welke versie van deze pakketten er patches worden uitgevoerd.

`bunyan`Met de `winston` `console` patches, en worden Application Insights tracerings gebeurtenissen gegenereerd op basis van het feit of dit `setAutoCollectConsole` is ingeschakeld. Met de rest worden Application Insights afhankelijkheids gebeurtenissen gegenereerd op basis van het feit of het `setAutoCollectDependencies` is ingeschakeld.

### <a name="live-metrics"></a>Live Metrics

Gebruik om live Metrics van uw app naar Azure te verzenden `setSendLiveMetrics(true)` . Filteren van dynamische metrische gegevens in de portal wordt momenteel niet ondersteund.

### <a name="extended-metrics"></a>Uitgebreide metrische gegevens

> [!NOTE]
> De mogelijkheid om uitgebreide systeem eigen metrieken te verzenden is toegevoegd aan versie 1.4.0

Om het verzenden van uitgebreide native metrische gegevens van uw app naar Azure mogelijk te maken, installeert u het afzonderlijke systeem eigen metrische meet pakket. De SDK wordt automatisch geladen wanneer deze is geïnstalleerd en het verzamelen van Node.js systeem eigen metrische gegevens wordt gestart.

```bash
npm install applicationinsights-native-metrics
```

Op dit moment voert het pakket met systeem eigen metrische gegevens de CPU-tijd van de garbagecollection, de ticks van de gebeurtenis en het heap-gebruik:

- **Garbage Collection**: de hoeveelheid CPU-tijd die aan elk type garbagecollection is besteed en het aantal exemplaren van elk type.
- **Event-lus**: hoeveel maat streepjes zijn opgetreden en hoeveel CPU-tijd er in totaal is uitgegeven.
- **Heap versus niet-heap**: hoeveel geheugen gebruik van uw app bevindt zich in de heap of niet-heap.

### <a name="distributed-tracing-modes"></a>Gedistribueerde tracerings modi

Standaard worden door de SDK headers verzonden die zijn begrepen door andere toepassingen/services, met een Application Insights SDK. U kunt eventueel verzen ding/ontvangst van [W3C-tracerings context](https://github.com/w3c/trace-context) headers inschakelen naast de bestaande AI-headers, zodat u de correlatie met een van uw bestaande verouderde Services niet verbreekt. Als u W3C-headers inschakelt, kan uw app correleren met andere services die niet met Application Insights worden geinstrumenteerd, maar deze W3C-standaard wel aannemen.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient-API

Zie [Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens](./api-custom-events-metrics.md) voor een volledige beschrijving van de TelemetryClient-API.

U kunt elke aanvraag, gebeurtenis, metriek of uitzondering traceren met behulp van de Node.js SDK van Application Insights. Het volgende codevoorbeeld toont enkele API's die u kunt gebruiken:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Afhankelijkheden bijhouden

Gebruik de volgende code om de afhankelijkheden bij te houden:

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Een voor beeld van een hulp programma `trackMetric` waarmee u kunt meten hoe lang de planning van de gebeurtenis loop gaat duren:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Een aangepaste eigenschap toevoegen aan alle gebeurtenissen

Gebruik de volgende code om een aangepaste eigenschap toe te voegen aan alle gebeurtenissen:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET-aanvragen traceren

Gebruik de volgende code om HTTP GET-aanvragen hand matig bij te houden:

> [!NOTE]
> Alle aanvragen worden standaard bijgehouden. Als u automatische verzameling wilt uitschakelen, roept u. setAutoCollectRequests (ONWAAR) aan voordat u start () aanroept.

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

U kunt ook aanvragen volgen met behulp van de `trackNodeHttpRequest` methode:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>Serveropstarttijd traceren

Gebruik de volgende code om serveropstarttijd bij te houden:

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="preprocess-data-with-telemetry-processors"></a>Gegevens voorverwerken met telemetrie-processors

U kunt verzamelde gegevens verwerken en filteren voordat deze worden verzonden voor retentie met *telemetrie-processors*. Telemetrie-processors worden één voor één aangeroepen in de volg orde waarin ze zijn toegevoegd voordat het telemetrie-item naar de Cloud wordt verzonden.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Als een telemetrie-processor False retourneert, wordt dat telemetrie-item niet verzonden.

Alle telemetrie-processors ontvangen de telemetriegegevens en de bijbehorende envelop om te controleren en te wijzigen. Ze ontvangen ook een context-object. De inhoud van dit object wordt gedefinieerd door de `contextObjects` para meter bij het aanroepen van een track methode voor hand matig bijgehouden telemetrie. Voor automatisch verzamelde telemetrie wordt dit object gevuld met beschik bare aanvraag gegevens en de permanente aanvraag inhoud zoals opgegeven door `appInsights.getCorrelationContext()` (als automatische afhankelijkheids correlatie is ingeschakeld).

Het type script-type voor een telemetrie-processor is:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Een processor die stacks uit uitzonde ringen verwijdert, kan bijvoorbeeld als volgt worden geschreven en toegevoegd:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Meerdere instrumentatie sleutels gebruiken

U kunt meerdere Application Insights resources maken en elke gewenste gegevens verzenden met behulp van hun respectieve instrumentatie sleutels ("iKey").

 Bijvoorbeeld:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Geavanceerde configuratie opties

Het client object bevat een `config` eigenschap met veel optionele instellingen voor geavanceerde scenario's. Deze kunnen als volgt worden ingesteld:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Deze eigenschappen zijn specifiek voor de client, zodat u `appInsights.defaultClient` afzonderlijk kunt configureren van clients die zijn gemaakt met `new appInsights.TelemetryClient()` .

| Eigenschap                        | Beschrijving                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Een id voor uw Application Insights-resource.                                                      |
| endpointUrl                     | Het opname eindpunt voor het verzenden van telemetrie-nettoladingen naar.                                                      |
| quickPulseHost                  | De Live Metrics Stream-host waarnaar de telemetrie Live Metrics moet worden verzonden.                                            |
| proxyHttpUrl                    | Een proxy server voor SDK HTTP-verkeer (optioneel, standaard opgehaald uit de `http_proxy` omgevings variabele).     |
| proxyHttpsUrl                   | Een proxy server voor SDK HTTPS Traffic (optioneel, standaard opgehaald uit de `https_proxy` omgevings variabele).   |
| httpAgent                       | Een http. Te gebruiken agent voor SDK HTTP-verkeer (optioneel, standaard niet gedefinieerd).                                   |
| httpsAgent                      | Een HTTPS. Te gebruiken agent voor SDK HTTPS Traffic (optioneel, standaard niet gedefinieerd).                                 |
| maxBatchSize                    | Het maximum aantal telemetriegegevens dat moet worden opgenomen in een Payload voor het opname-eind punt (standaard `250` ).   |
| maxBatchIntervalMs              | De maximale wacht tijd voor een nettolading om maxBatchSize te bereiken (standaard `15000` ).               |
| disableAppInsights              | Een vlag waarmee wordt aangegeven of telemetrie-verzen ding is uitgeschakeld (standaard `false` ).                                 |
| samplingPercentage              | Het percentage van het bijgehouden aantal telemetriegegevens dat moet worden verzonden (standaard `100` ).                      |
| correlationIdRetryIntervalMs    | De tijd die moet worden gewacht voordat een nieuwe poging wordt gedaan om de ID voor correlatie tussen verschillende onderdelen (standaard) op te halen `30000` .     |
| correlationHeaderExcludedDomains| Een lijst met domeinen die moeten worden uitgesloten van de injectie van de correlatie-header van een cross-onderdeel (standaard Zie [config. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Volgende stappen

* [Uw telemetrie in de portal bewaken](./overview-dashboard.md)
* [Analysequery’s schrijven over uw telemetrie](../log-query/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md