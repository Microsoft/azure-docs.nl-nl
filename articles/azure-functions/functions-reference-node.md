---
title: Naslag informatie over Java script-ontwikkel aars voor Azure Functions
description: Meer informatie over het ontwikkelen van functies met behulp van Java script.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 11/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0b32efe3738dedbe8178889b3e9008964d485b00
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034914"
---
# <a name="azure-functions-javascript-developer-guide"></a>Ontwikkelaarshandleiding voor Azure Functions Javascript

Deze hand leiding bevat gedetailleerde informatie die u kan helpen bij het ontwikkelen van Azure Functions met behulp van Java script.

Als Express.js, Node.js of Java script-ontwikkelaar, als u geen ervaring hebt met Azure Functions, kunt u eerst een van de volgende artikelen lezen:

| Aan de slag | Concepten| Begeleide training |
| -- | -- | -- | 
| <ul><li>[Node.js functie met Visual Studio code](./create-first-function-vs-code-node.md)</li><li>[Node.js functie met Terminal/opdracht prompt](./create-first-function-cli-node.md)</li></ul> | <ul><li>[Ontwikkelaarsgids](functions-reference.md)</li><li>[Hostingopties](functions-scale.md)</li><li>[Type script-functies](#typescript)</li><li>[Prestatie &nbsp; overwegingen](functions-best-practices.md)</li></ul> | <ul><li>[Serverloze toepassingen maken](/learn/paths/create-serverless-applications/)</li><li>[Refactorion-Node.js en Express-Api's naar Serverloze Api's](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>Basis beginselen van Java script-functies

Een Java script-functie (Node.js) is een export `function` die wordt uitgevoerd wanneer geactiveerd ([triggers worden geconfigureerd in function.jsop](functions-triggers-bindings.md)). Het eerste argument dat aan elke functie is door gegeven, is een `context` object dat wordt gebruikt voor het ontvangen en verzenden van bindings gegevens, logboek registratie en communicatie met de runtime.

## <a name="folder-structure"></a>Mapstructuur

De vereiste mapstructuur voor een Java script-project ziet er als volgt uit. Deze standaard instelling kan worden gewijzigd. Zie het gedeelte [script](#using-scriptfile) voor meer informatie.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

In de hoofdmap van het project bevindt zich een gedeelde [host.jsvoor](functions-host-json.md) het bestand dat kan worden gebruikt om de functie-app te configureren. Elke functie heeft een map met een eigen code bestand (. js) en een bindings configuratie bestand (function.jsaan). De naam van de `function.json` bovenliggende map is altijd de naam van uw functie.

De bindings uitbreidingen vereist in [versie 2. x](functions-versions.md) van de functions runtime worden gedefinieerd in het `extensions.csproj` bestand, met de daad werkelijke bibliotheek bestanden in de `bin` map. Wanneer u lokaal ontwikkelt, moet u [bindings uitbreidingen registreren](./functions-bindings-register.md#extension-bundles). Bij het ontwikkelen van functies in de Azure Portal, wordt deze registratie voor u uitgevoerd.

## <a name="exporting-a-function"></a>Een functie exporteren

Java script-functies moeten worden geëxporteerd via [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (of [`exports`](https://nodejs.org/api/modules.html#modules_exports) ). De geëxporteerde functie moet een Java script-functie die wordt uitgevoerd wanneer deze wordt geactiveerd.

De functies runtime zoekt standaard naar uw functie in `index.js` , waarbij `index.js` dezelfde bovenliggende map wordt gedeeld als de bijbehorende `function.json` . In het standaard geval moet de geëxporteerde functie de enige export van het bestand of de export met de naam `run` of zijn `index` . Meer informatie over het configureren van het [toegangs punt van uw functie](functions-reference-node.md#configure-function-entry-point) vindt u in de bestands locatie en export naam van uw functie.

De geëxporteerde functie heeft een aantal argumenten door gegeven bij de uitvoering. Het eerste argument dat wordt gebruikt, is altijd een- `context` object. Als uw functie synchroon is (geen belofte retourneert), moet u het object door geven `context` , omdat aanroepen `context.done` vereist is voor een juiste gebruik.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Een async-functie exporteren
Wanneer u de [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaratie of de eenvoudige Java script- [belofte](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) in versie 2. x van de functions-runtime gebruikt, hoeft u de [`context.done`](#contextdone-method) call back niet expliciet aan te roepen om aan te geven dat de functie is voltooid. De functie wordt voltooid wanneer de geëxporteerde async-functie/Promise is voltooid. Voor functies die zijn gericht op versie 1. x runtime moet u nog steeds aanroepen [`context.done`](#contextdone-method) wanneer uw code wordt uitgevoerd.

Het volgende voor beeld is een eenvoudige functie die logboeken aanmeldt dat deze is geactiveerd en de uitvoering onmiddellijk voltooit.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Bij het exporteren van een async-functie kunt u ook een uitvoer binding configureren om de waarde te halen `return` . Dit wordt aanbevolen als u slechts één uitvoer binding hebt.

Als u een uitvoer wilt toewijzen met `return` , wijzigt u de `name` eigenschap `$return` in in `function.json` .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

In dit geval moet uw functie eruitzien zoals in het volgende voor beeld:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Bindingen 
In Java script worden [bindingen](functions-triggers-bindings.md) geconfigureerd en gedefinieerd in de function.jsvan een functie in. Functies werken op een aantal manieren met bindingen.

### <a name="inputs"></a>Invoerwaarden
De invoer is onderverdeeld in twee categorieën in Azure Functions: een is de invoer van de trigger en de andere is de extra invoer. Triggers en andere invoer bindingen (bindingen van `direction === "in"` ) kunnen op drie manieren worden gelezen door een functie:
 - **_[Aanbevolen]_ Als para meters die zijn door gegeven aan de functie.** Ze worden door gegeven aan de functie in dezelfde volg orde als waarin ze zijn gedefinieerd in *function.jsop*. De `name` eigenschap die is gedefinieerd in *function.js* in, hoeft niet overeen te komen met de naam van uw para meter, maar dit moet wel.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Als leden van het [`context.bindings`](#contextbindings-property) object.** Elk lid krijgt de naam van de eigenschap die is `name` gedefinieerd in *function.jsop*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Als invoer met behulp van het Java script- [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) object.** Dit is in wezen hetzelfde als het door voeren van invoer als para meters, maar biedt u de mogelijkheid om invoer dynamisch te verwerken.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Uitvoerwaarden
Uitvoer (bindingen van `direction === "out"` ) kan op een aantal manieren worden geschreven naar een functie. In alle gevallen komt de `name` eigenschap van de binding, zoals gedefinieerd in *function.js* , overeen met de naam van het object lid dat is geschreven in uw functie. 

U kunt gegevens aan uitvoer bindingen op een van de volgende manieren toewijzen (deze methoden niet combi neren):

- **_[Aanbevolen voor meerdere uitvoer]_ Een object retour neren.** Als u een functie van async/Promise retourneert, kunt u een object retour neren met de toegewezen uitvoer gegevens. In het onderstaande voor beeld zijn de uitvoer bindingen de naam ' httpResponse ' en ' queueOutput ' in *function.jsop*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Als u een synchrone functie gebruikt, kunt u dit object retour neren met [`context.done`](#contextdone-method) (Zie voor beeld).
- **_[Aanbevolen voor één uitvoer]_ Een waarde rechtstreeks en met de naam van de $return binding wordt geretourneerd.** Dit werkt alleen voor async/Promise-functies. Zie voor beelden van [het exporteren van een async-functie](#exporting-an-async-function). 
- **Waarden toewijzen aan `context.bindings`** U kunt waarden rechtstreeks aan context. bindingen toewijzen.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Gegevens type bindingen

Als u het gegevens type voor een invoer binding wilt definiëren, gebruikt u de `dataType` eigenschap in de bindings definitie. Als u de inhoud van een HTTP-aanvraag in binaire indeling wilt lezen, gebruikt u bijvoorbeeld het type `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Opties voor `dataType` zijn: `binary` , `stream` en `string` .

## <a name="context-object"></a>context object

De runtime gebruikt een `context` object om gegevens van en naar uw functie en de runtime door te geven. Voor het lezen en instellen van gegevens van bindingen en voor het schrijven naar Logboeken `context` is het object altijd de eerste para meter die aan een functie is door gegeven.

Voor functies met synchrone code bevat het context object de `done` call back die u aanroept wanneer de functie wordt uitgevoerd. Expliciet aanroepen `done` is niet nodig voor het schrijven van asynchrone code; de `done` call back wordt impliciet aangeroepen.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

De context die wordt door gegeven aan de functie `executionContext` , bevat een eigenschap, een object met de volgende eigenschappen:

| Naam van eigenschap  | Type  | Beschrijving |
|---------|---------|---------|
| `invocationId` | Tekenreeks | Biedt een unieke id voor de specifieke functie aanroep. |
| `functionName` | Tekenreeks | Geeft de naam van de functie die wordt uitgevoerd |
| `functionDirectory` | Tekenreeks | Voorziet in de app-map met functies. |

In het volgende voor beeld ziet u hoe de wordt geretourneerd `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>context. bindings, eigenschap

```js
context.bindings
```

Retourneert een benoemd object dat wordt gebruikt om bindings gegevens te lezen of toe te wijzen. Invoer-en trigger gegevens voor bindingen kunnen worden geopend door het lezen van eigenschappen van `context.bindings` . Uitvoer binding gegevens kunnen worden toegewezen door gegevens toe te voegen aan `context.bindings`

Met de volgende bindings definities in uw function.jshebt u bijvoorbeeld toegang tot de inhoud van een wachtrij van `context.bindings.myInput` en het toewijzen van uitvoer aan een wachtrij met behulp van `context.bindings.myOutput` .

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

U kunt ervoor kiezen om gegevens over uitvoer bindingen te definiëren met behulp `context.done` van de methode in plaats van het `context.binding` object (zie hieronder).

### <a name="contextbindingdata-property"></a>context. bindingData eigenschap

```js
context.bindingData
```

Retourneert een benoemd object dat trigger-meta gegevens en functie aanroepgegevens ( `invocationId` , `sys.methodName` , `sys.utcNow` ,, `sys.randGuid` ) bevat. Voor een voor beeld van meta gegevens van triggers raadpleegt u dit [voor beeld van Event hubs](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>context. Done-methode

```js
context.done([err],[propertyBag])
```

Laat de runtime weten dat uw code is voltooid. Als uw functie gebruikmaakt van de [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaratie, hoeft u deze niet te gebruiken `context.done()` . De `context.done` call back wordt impliciet aangeroepen. Asynchrone functies zijn beschikbaar in knoop punt 8 of een latere versie, waarvoor versie 2. x van de functions-runtime vereist is.

Als uw functie geen async-functie is, **moet u aanroepen** `context.done` om de runtime te informeren dat de functie is voltooid. Er wordt een time-out uitgevoerd als deze ontbreekt.

Met de- `context.done` methode kunt u zowel een door de gebruiker gedefinieerde fout terugsturen naar de runtime als een JSON-object dat uitvoer bindings gegevens bevat. Eigenschappen die zijn door gegeven voor `context.done` het overschrijven van items die zijn ingesteld voor het `context.bindings` object.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context. log-methode  

```js
context.log(message)
```

Hiermee kunt u schrijven naar de streaming-functie Logboeken op het standaard tracerings niveau, met andere registratie niveaus beschikbaar. Traceer logboek registratie wordt gedetailleerd beschreven in de volgende sectie. 

## <a name="write-trace-output-to-logs"></a>Uitvoer van tracering naar Logboeken schrijven

In functies gebruikt u de `context.log` methoden om tracerings uitvoer naar de logboeken en de-console te schrijven. Wanneer u belt `context.log()` , wordt uw bericht naar de logboeken geschreven op het niveau van de standaard tracering. Dit is het tracerings niveau _info_ . Functies kunnen worden geïntegreerd met Azure-toepassing Insights om uw functie-app-logboeken beter vast te leggen. Application Insights, onderdeel van Azure Monitor, biedt voorzieningen voor verzameling, visuele rendering en analyse van de telemetrie van de toepassing en uw traceer uitvoer. Zie [bewaking Azure functions](functions-monitoring.md)voor meer informatie.

In het volgende voor beeld wordt een logboek op het tracerings niveau info geschreven, met inbegrip van de aanroep-ID:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Alle `context.log` methoden ondersteunen dezelfde parameter indeling die wordt ondersteund door de methode Node.js [util. Format](https://nodejs.org/api/util.html#util_util_format_format). Bekijk de volgende code, waarmee functie Logboeken worden geschreven met behulp van het standaard tracerings niveau:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

U kunt ook dezelfde code in de volgende indeling schrijven:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> Gebruik niet `console.log` voor het schrijven van tracerings uitvoer. Omdat uitvoer van `console.log` wordt vastgelegd op het niveau van de functie-app, is deze niet gekoppeld aan een specifieke functie aanroep en wordt niet weer gegeven in de logboeken van een specifieke functie. Versie 1. x van de functions-runtime biedt ook geen ondersteuning `console.log` voor het gebruik van om naar de-console te schrijven.

### <a name="trace-levels"></a>Tracerings niveaus

Naast het standaard niveau zijn de volgende logboek registratie methoden beschikbaar waarmee u functie Logboeken op specifieke tracerings niveaus kunt schrijven.

| Methode                 | Beschrijving                                |
| ---------------------- | ------------------------------------------ |
| **fout (_bericht_)**   | Hiermee wordt een gebeurtenis op fout niveau naar de logboeken geschreven.   |
| **Warning (_bericht_)**    | Hiermee wordt een gebeurtenis op waarschuwings niveau naar de logboeken geschreven. |
| **info (_bericht_)**    | Schrijft naar logboek registratie op info niveau of lager.    |
| **uitgebreid (_bericht_)** | Schrijft naar uitgebreide logboek registratie.           |

In het volgende voor beeld wordt hetzelfde logboek op het tracerings niveau waarschuwing geschreven, in plaats van het info niveau:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Omdat de _fout_ het hoogste traceer niveau is, wordt deze tracering naar de uitvoer op alle tracerings niveaus geschreven zolang logboek registratie is ingeschakeld.

### <a name="configure-the-trace-level-for-logging"></a>Het tracerings niveau voor logboek registratie configureren

Met functies kunt u het tracerings niveau voor drempel waarden definiëren voor het schrijven naar de logboeken of de-console. De specifieke drempel waarden zijn afhankelijk van uw versie van de functions-runtime.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Als u de drempel waarde voor traceringen die naar de logboeken worden geschreven, wilt instellen, gebruikt u de `logging.logLevel` eigenschap in de host.jsin het bestand. Met dit JSON-object kunt u een standaard drempel definiëren voor alle functies in uw functie-app. Daarnaast kunt u specifieke drempel waarden voor afzonderlijke functies definiëren. Zie [How to configure Monitoring for Azure functions voor](configure-monitoring.md)meer informatie.

# <a name="v1x"></a>[v1. x](#tab/v1)

Als u de drempel waarde wilt instellen voor alle traceringen die worden geschreven naar Logboeken en de-console, gebruikt u de `tracing.consoleLevel` eigenschap in de host.jsin het bestand. Deze instelling is van toepassing op alle functies in uw functie-app. In het volgende voor beeld wordt de drempel voor tracering ingesteld om uitgebreide logboek registratie in te scha kelen:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

De waarden van **consoleLevel** komen overeen met de namen van de `context.log` methoden. Als u alle traceer logboek registratie wilt uitschakelen voor de-console, stelt u **consoleLevel** in op _uit_. Zie voor meer informatie de [ referentie overhost.jsop v1. x](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Aangepaste telemetrie vastleggen in logboek

Standaard schrijft functies uitvoer als traceringen naar Application Insights. Voor meer controle kunt u in plaats daarvan de [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) gebruiken om aangepaste telemetriegegevens naar uw Application Insights-exemplaar te verzenden. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides`Met de para meter wordt de `operation_Id` aanroep-id van de functie ingesteld. Met deze instelling kunt u alle automatisch gegenereerde en aangepaste telemetrie voor een bepaalde functie aanroep correleren.

## <a name="http-triggers-and-bindings"></a>HTTP-triggers en-bindingen

HTTP-en webhook-triggers en HTTP-uitvoer bindingen gebruiken aanvraag-en antwoord objecten om de HTTP-berichten te vertegenwoordigen.  

### <a name="request-object"></a>Aanvraag object

Het `context.req` object (Request) heeft de volgende eigenschappen:

| Eigenschap      | Beschrijving                                                    |
| ------------- | -------------------------------------------------------------- |
| _organen_        | Een object dat de hoofd tekst van de aanvraag bevat.               |
| _koppen_     | Een object dat de aanvraag headers bevat.                   |
| _methode_      | De HTTP-methode van de aanvraag.                                |
| _originalUrl_ | De URL van de aanvraag.                                        |
| _params_      | Een object dat de routerings parameters van de aanvraag bevat. |
| _ophalen_       | Een object dat de query parameters bevat.                  |
| _rawBody_     | De hoofd tekst van het bericht als een teken reeks.                           |


### <a name="response-object"></a>Responsobject

Het `context.res` (antwoord)-object heeft de volgende eigenschappen:

| Eigenschap  | Beschrijving                                               |
| --------- | --------------------------------------------------------- |
| _organen_    | Een object dat de hoofd tekst van het antwoord bevat.         |
| _koppen_ | Een object dat de antwoord headers bevat.             |
| _isRaw_   | Hiermee wordt aangegeven dat de opmaak voor het antwoord wordt overgeslagen.    |
| _status_  | De HTTP-status code van het antwoord.                     |
| _cookies_ | Een matrix met HTTP-cookie-objecten die in het antwoord zijn ingesteld. Een HTTP-cookie object heeft een `name` , `value` en andere cookie-eigenschappen, zoals `maxAge` of `sameSite` . |

### <a name="accessing-the-request-and-response"></a>De aanvraag en het antwoord openen 

Wanneer u met HTTP-triggers werkt, kunt u op een aantal manieren toegang krijgen tot de HTTP-aanvraag-en-antwoord objecten:

+ **Van `req` en `res` Eigenschappen van het `context` object.** Op deze manier kunt u het conventionele patroon gebruiken om toegang te krijgen tot HTTP-gegevens van het context object, in plaats van het volledige patroon te gebruiken `context.bindings.name` . Het volgende voor beeld laat zien hoe u toegang krijgt tot de `req` `res` objecten en `context` :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Van de benoemde invoer-en uitvoer bindingen.** Op deze manier werken de HTTP-trigger en de bindingen hetzelfde als elke andere binding. In het volgende voor beeld wordt het object Response ingesteld met behulp van een benoemde `response` binding: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Alleen antwoord]_ Door aan te roepen `context.res.send(body?: any)` .** Er wordt een HTTP-antwoord met invoer gemaakt `body` als de antwoord tekst. `context.done()` wordt impliciet aangeroepen.

+ **_[Alleen antwoord]_ Door aan te roepen `context.done()` .** Een speciaal type HTTP-binding retourneert het antwoord dat is door gegeven aan de- `context.done()` methode. De volgende HTTP-uitvoer binding definieert een `$return` uitvoer parameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Schalen en gelijktijdigheid

Standaard controleert Azure Functions automatisch de belasting van uw toepassing en worden er indien nodig extra exemplaren van de host gemaakt voor Node.js. Functies gebruiken ingebouwde (niet door de gebruiker te configureren) drempel waarden voor verschillende trigger typen om te bepalen wanneer instanties moeten worden toegevoegd, zoals de leeftijd van berichten en de grootte van de wachtrij voor Queue trigger. Zie [hoe het verbruik en de Premium-abonnementen werken](event-driven-scaling.md)voor meer informatie.

Dit gedrag voor schalen is voldoende voor veel Node.js toepassingen. Voor CPU-gebonden toepassingen kunt u de prestaties verder verbeteren door gebruik te maken van werk processen in meerdere talen.

Elk functions-exemplaar heeft standaard een werk proces met één taal. U kunt het aantal werk processen per host (Maxi maal 10) verhogen met behulp van de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) toepassings instelling. Azure Functions probeert vervolgens gelijktijdige functie aanroepen voor deze werk nemers gelijkmatig te verdelen. 

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die functies maakt wanneer uw toepassing wordt geschaald om aan de vraag te voldoen. 

## <a name="node-version"></a>Knooppunt versie

In de volgende tabel ziet u de huidige ondersteunde Node.js versies voor elke hoofd versie van de functions runtime, door het besturings systeem:

| Functie versie | Knooppunt versie (Windows) | Knooppunt versie (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (vergrendeld door de runtime) | n.v.t. |
| 2.x  | `~8`<br/>`~10` aanbevelingen<br/>`~12` | `node|8`<br/>`node|10` aanbevelingen  |
| 3.x | `~10`<br/>`~12` aanbevelingen<br/>`~14` evaluatie  | `node|10`<br/>`node|12` aanbevelingen<br/>`node|14` evaluatie |

U kunt de huidige versie bekijken die door de runtime wordt gebruikt door logboek registratie `process.version` vanuit een functie.

### <a name="setting-the-node-version"></a>De knooppunt versie instellen

Voor Windows-functie-apps moet u de versie in azure richten door de `WEBSITE_NODE_DEFAULT_VERSION` [app-instelling](functions-how-to-use-azure-function-app-settings.md#settings) in te stellen op een ondersteunde versie van LTS, zoals `~12` .

Voor Linux-functie-apps voert u de volgende Azure CLI-opdracht uit om de knooppunt versie bij te werken.

```bash
az functionapp config set --linux-fx-version "node|12" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

## <a name="dependency-management"></a>Beheer van afhankelijkheden
Als u Community-bibliotheken in uw Java script-code wilt gebruiken, zoals in het onderstaande voor beeld wordt weer gegeven, moet u ervoor zorgen dat alle afhankelijkheden zijn geïnstalleerd op uw functie-app in Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> U moet een `package.json` bestand definiëren in de hoofdmap van uw functie-app. Als u het bestand definieert, kunnen alle functies in de app dezelfde pakketten in de cache delen, wat de beste prestaties biedt. Als er een versie conflict ontstaat, kunt u dit oplossen door een bestand toe te voegen `package.json` in de map van een specifieke functie.  

Bij het implementeren van functie-apps vanuit broncode beheer, `package.json` wordt in elk bestand dat aanwezig is in uw opslag plaats, `npm install` in de map geactiveerd tijdens de implementatie. Maar wanneer u implementeert via de portal of CLI, moet u de pakketten hand matig installeren.

Er zijn twee manieren om pakketten te installeren op uw functie-app: 

### <a name="deploying-with-dependencies"></a>Implementeren met afhankelijkheden
1. Installeer alle vereiste pakketten lokaal door uit te voeren `npm install` .

2. Implementeer uw code en zorg ervoor dat de `node_modules` map is opgenomen in de implementatie. 


### <a name="using-kudu"></a>Kudu gebruiken
1. Ga naar `https://<function_app_name>.scm.azurewebsites.net`.

2. Klik op **debug console**-  >  **cmd**.

3. Ga naar `D:\home\site\wwwroot` en sleep uw package.jsnaar bestand naar de map **wwwroot** in het bovenste gedeelte van de pagina.  
    U kunt ook op andere manieren bestanden uploaden naar uw functie-app. Zie de [functie-app-bestanden bijwerken](functions-reference.md#fileupdate)voor meer informatie. 

4. Nadat de package.jsin het bestand is geüpload, voert u de `npm install` opdracht uit in de **kudu-console voor externe uitvoering**.  
    Met deze actie worden de pakketten gedownload die zijn aangegeven in de package.jsin het bestand en wordt de functie-app opnieuw gestart.

## <a name="environment-variables"></a>Omgevingsvariabelen

Voeg uw eigen omgevings variabelen toe aan een functie-app, zowel in uw lokale als in de Cloud omgevingen, zoals operationele geheimen (verbindings reeksen, sleutels en eind punten) of omgevings instellingen (zoals variabelen voor het instellen van de profilering). Toegang tot deze instellingen met `process.env` in uw functie code.

### <a name="in-local-development-environment"></a>In lokale ontwikkel omgeving

Wanneer u lokaal uitvoert, bevat uw functions-project een [ `local.settings.json` bestand](/azure/azure-functions/functions-run-local)waarin u de omgevings variabelen in het `Values` object opslaat. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>In een Azure-cloud omgeving

Wanneer u in azure werkt, kunt u met de functie-app [instellingen](functions-app-settings.md)gebruiken, zoals service verbindings reeksen, en deze instellingen weer gegeven als omgevings variabelen tijdens de uitvoering. 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>Toegang tot omgevings variabelen in code

Toegang tot toepassings instellingen als omgevings variabelen met `process.env` , zoals hier wordt weer gegeven in het tweede en derde aanroepen naar `context.log()` waar we de `AzureWebJobsStorage` en `WEBSITE_SITE_NAME` omgevings variabelen registreren:

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="configure-function-entry-point"></a>Functie-ingangs punt configureren

De `function.json` eigenschappen `scriptFile` en `entryPoint` kunnen worden gebruikt voor het configureren van de locatie en de naam van de geëxporteerde functie. Deze eigenschappen kunnen belang rijk zijn wanneer uw Java script wordt transmaald.

### <a name="using-scriptfile"></a>`scriptFile` gebruiken

Een Java script-functie wordt standaard uitgevoerd vanuit `index.js` , een bestand met dezelfde bovenliggende map als de bijbehorende `function.json` .

`scriptFile` kan worden gebruikt om een mapstructuur te verkrijgen die eruitziet als in het volgende voor beeld:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

De `function.json` for `myNodeFunction` moet een eigenschap bevatten die `scriptFile` verwijst naar het bestand met de geëxporteerde functie om uit te voeren.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` gebruiken

In `scriptFile` (of `index.js` ) moet een functie worden geëxporteerd met om `module.exports` te vinden en uit te voeren. De functie die wordt uitgevoerd wanneer de trigger wordt geactiveerd, is standaard de enige export vanuit dat bestand, de export naam `run` of de export met de naam `index` .

Dit kan worden geconfigureerd met `entryPoint` in `function.json` , zoals in het volgende voor beeld:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

In functions v2. x, dat de `this` para meter in gebruikers functies ondersteunt, kan de functie code in het volgende voor beeld worden gebruikt:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In dit voor beeld is het belang rijk te weten dat er een object wordt geëxporteerd, maar er zijn geen garanties voor het behoud van de status tussen uitvoeringen.

## <a name="local-debugging"></a>Lokale fout opsporing

Wanneer met de `--inspect` para meter wordt gestart, luistert een Node.js proces naar een client voor fout opsporing op de opgegeven poort. In Azure Functions 2. x kunt u argumenten opgeven die moeten worden door gegeven aan het Node.js proces waarmee de code wordt uitgevoerd door de omgevings variabele of app-instelling toe te voegen `languageWorkers:node:arguments = <args>` . 

Als u lokaal fouten wilt opsporen, voegt u `"languageWorkers:node:arguments": "--inspect=5858"` onder `Values` in uw [local.settings.js](./functions-run-local.md#local-settings-file) toe aan het bestand en koppelt u een fout opsporingsprogramma aan poort 5858.

Bij fout opsporing met behulp van VS code `--inspect` wordt de para meter automatisch toegevoegd met behulp van de `port` waarde in de launch.jsvan het project in het bestand.

In versie 1. x wordt de instelling `languageWorkers:node:arguments` niet gebruikt. De poort voor fout opsporing kan worden geselecteerd met de [`--nodeDebugPort`](./functions-run-local.md#start) para meter op Azure functions core tools.

## <a name="typescript"></a>TypeScript

Wanneer u versie 2. x van de functions runtime richt, hebben zowel [Azure functions voor Visual Studio code](./create-first-function-cli-typescript.md) als de [Azure functions core tools](functions-run-local.md) u functie-apps kunnen maken met behulp van een sjabloon die type script functie-app-projecten ondersteunt. De sjabloon genereert `package.json` en `tsconfig.json` Project bestanden die het eenvoudiger maken om Java script-functies in de type script-code op te nemen, uit te voeren en te publiceren met deze hulpprogram ma's.

Een gegenereerd `.funcignore` bestand wordt gebruikt om aan te geven welke bestanden worden uitgesloten wanneer een project wordt gepubliceerd naar Azure.  

Type script-bestanden (. TS) worden gepoold naar Java script-bestanden (. js) in de `dist` uitvoermap. Type script-sjablonen gebruiken de [ `scriptFile` para meter](#using-scriptfile) in `function.json` om de locatie van het bijbehorende js-bestand in de map aan te geven `dist` . De uitvoer locatie wordt ingesteld door de sjabloon met behulp van de `outDir` para meter in het `tsconfig.json` bestand. Als u deze instelling of de naam van de map wijzigt, kan de runtime niet vinden welke code moet worden uitgevoerd.

De manier waarop u lokaal een type script-project ontwikkelt en implementeert, is afhankelijk van uw ontwikkel programma.

### <a name="visual-studio-code"></a>Visual Studio Code

Met de [Azure functions voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) -extensie kunt u uw functies ontwikkelen met behulp van type script. De belangrijkste hulp middelen zijn vereist voor de uitbrei ding Azure Functions.

Als u een type script-functie-app in Visual Studio code wilt maken, kiest `TypeScript` u als taal bij het maken van een functie-app.

Wanneer u op **F5** drukt om de app lokaal uit te voeren, wordt transpilation uitgevoerd voordat de host (func.exe) wordt geïnitialiseerd. 

Wanneer u de functie-app in azure implementeert met behulp van de knop **implementeren in functie app...** , genereert de Azure functions extensie eerst een productie-app-kant van Java script-bestanden van de type script-bron bestanden.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Er zijn verschillende manieren waarop een type script-project verschilt van een Java script-project wanneer de kern Hulpprogramma's worden gebruikt.

#### <a name="create-project"></a>Project maken

Als u een type script-functie-app-project wilt maken met behulp van basis Hulpprogramma's, moet u de optie type script taal opgeven wanneer u de functie-app maakt. U kunt dit op een van de volgende manieren doen:

- Voer de `func init` opdracht uit, selecteer `node` als taal stack en selecteer `typescript` .

- Voer de opdracht `func init --worker-runtime typescript` uit.

#### <a name="run-local"></a>Lokaal uitvoeren

Als u de code van de functie-app lokaal wilt uitvoeren met behulp van kern Hulpprogramma's, gebruikt u de volgende opdrachten in plaats van `func host start` : 

```command
npm install
npm start
```

De `npm start` opdracht is gelijk aan de volgende opdrachten:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publiceren naar Azure

Voordat u de [`func azure functionapp publish`] opdracht gebruikt om te implementeren in azure, maakt u een productie-gereed build van Java script-bestanden van de type script-bron bestanden. 

Met de volgende opdrachten wordt uw type script-project voor bereid en gepubliceerd met kern Hulpprogramma's: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Vervang in deze opdracht door `<APP_NAME>` de naam van uw functie-app.

## <a name="considerations-for-javascript-functions"></a>Overwegingen voor Java script-functies

Wanneer u werkt met Java script-functies, moet u rekening houden met de overwegingen in de volgende secties.

### <a name="choose-single-vcpu-app-service-plans"></a>VCPU plannen voor eenmalige App Service kiezen

Wanneer u een functie-app maakt die gebruikmaakt van het App Service-abonnement, wordt u aangeraden een schema met één vCPU te selecteren in plaats van een plan met meerdere Vcpu's. Vandaag voeren functies java script-functies efficiënter uit op virtuele machines met één vCPU, en het gebruik van grotere Vm's produceert niet de verwachte prestatie verbeteringen. Als dat nodig is, kunt u hand matig uitschalen door meer VM-exemplaren met één vCPU toe te voegen of automatisch schalen in te scha kelen. Zie [aantal exemplaren hand matig of automatisch schalen](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)voor meer informatie.

### <a name="cold-start"></a>Koude start

Bij het ontwikkelen van Azure Functions in het serverloze hosting model is koude start een werkelijkheid. *Koude start* verwijst naar het feit dat het starten van de functie-app voor de eerste keer na een periode van inactiviteit langer duurt. Voor Java script-functies met grote afhankelijkheids structuren met name kan koude start aanzienlijk zijn. Als u het koude start proces wilt versnellen, [voert u indien mogelijk uw functies als pakket bestand uit](run-functions-from-deployment-package.md) . Bij veel implementatie methoden wordt standaard het model voor uitvoeren vanaf pakket gebruikt, maar als u een grote koude start ondervindt die niet op deze manier wordt uitgevoerd, kan deze wijziging een aanzienlijke verbetering opleveren.

### <a name="connection-limits"></a>Verbindings limieten

Wanneer u een servicespecifieke client gebruikt in een Azure Functions-toepassing, moet u geen nieuwe client maken bij elke functie aanroep. Maak in plaats daarvan een enkele statische client in het globale bereik. Zie [verbindingen beheren in azure functions](manage-connections.md)voor meer informatie.

### <a name="use-async-and-await"></a>Gebruiken `async` en `await`

Wanneer u Azure Functions in Java script schrijft, moet u code schrijven met behulp van de `async` `await` sleutel woorden en. Het schrijven van code met behulp `async` `await` van en in plaats van retour aanroepen of `.then` en `.catch` met beloftes helpt twee veelvoorkomende problemen te voor komen:
 - Niet-onderschepte uitzonde ringen genereren die [het Node.js proces vastlopen](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), waardoor de uitvoering van andere functies kan worden beïnvloed.
 - Onverwacht gedrag, zoals ontbrekende logboeken van context. log, veroorzaakt door asynchrone aanroepen die niet goed zijn gewacht.

In het onderstaande voor beeld wordt de asynchrone methode `fs.readFile` aangeroepen met een fout-eerste call back functie als de tweede para meter. Met deze code worden beide hierboven vermelde problemen veroorzaakt. Een uitzonde ring die niet expliciet is gevangen in het juiste bereik, heeft het hele proces vastlopen (probleem #1). Het aanroepen `context.done()` van buiten het bereik van de call back-functie betekent dat de functie aanroep kan eindigen voordat het bestand wordt gelezen (probleem #2). In dit voor beeld roept het aanroepen `context.done()` van te vroege resultaten aan ontbrekende logboek vermeldingen die beginnen met `Data from file:` .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Met behulp `async` `await` van de tref woorden en kunnen beide fouten worden voor komen. U moet de functie Node.js Utility gebruiken om de functies voor het automatisch [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) aanroepen van fouten in te scha kelen in functies die kunnen worden afgewacht.

In het onderstaande voor beeld mislukken alle niet-verwerkte uitzonde ringen die tijdens de uitvoering van de functie worden gegenereerd de afzonderlijke aanroep die een uitzonde ring heeft veroorzaakt. Het `await` sleutel woord geeft aan dat de stappen na voltooiing van de procedure `readFileAsync` `readFile` worden uitgevoerd. Met `async` en `await` hoeft u ook de call back niet aan te roepen `context.done()` .

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

+ [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
+ [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
+ [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)

[' func Azure functionapp Publish ']: functions-run-local.md#project-file-deployment
