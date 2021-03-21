---
title: Aangepaste handlers Azure Functions
description: Meer informatie over het gebruik van Azure Functions met elke taal of runtime versie.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: dd112c74ea9f013a0e14bddd735060ddbf73c14e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578520"
---
# <a name="azure-functions-custom-handlers"></a>Aangepaste handlers Azure Functions

Elke functions-app wordt uitgevoerd door een taalspecifieke handler. Hoewel Azure Functions veel [taal ingangen](./supported-languages.md) ondersteunt, zijn er situaties waarin u mogelijk andere talen of Runtimes wilt gebruiken.

Aangepaste handlers zijn Lightweight webservers die gebeurtenissen ontvangen van de host functions. Elke taal die HTTP-primitieven ondersteunt, kan een aangepaste handler implementeren.

Aangepaste handlers zijn het meest geschikt voor situaties waar u het volgende wilt doen:

- Implementeer een functie-app in een taal die momenteel niet wordt ondersteund, zoals Go of roest.
- Implementeer een functie-app in een runtime die momenteel niet wordt ondersteund, zoals Deno.

Met aangepaste handlers kunt u [Triggers en invoer-en uitvoer bindingen](./functions-triggers-bindings.md) gebruiken via [uitbreidings bundels](./functions-bindings-register.md).

Aan de slag met Azure Functions aangepaste handlers met [Quick starts in go en roest](create-first-function-vs-code-other.md).

## <a name="overview"></a>Overzicht

In het volgende diagram ziet u de relatie tussen de host functions en een webserver die is geïmplementeerd als een aangepaste handler.

![Overzicht van Azure Functions aangepaste handler](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Elke gebeurtenis activeert een aanvraag die wordt verzonden naar de host functions. Een gebeurtenis is een trigger die wordt ondersteund door Azure Functions.
1. De host functions verzendt vervolgens een [aanvraag lading](#request-payload) aan de webserver. De payload bevat trigger-en invoer bindings gegevens en andere meta gegevens voor de functie.
1. De webserver voert de afzonderlijke functie uit en retourneert een nettolading van de [reactie](#response-payload) op de host functions.
1. Met de functie-host worden gegevens door gegeven van het antwoord op de uitvoer bindingen van de functie voor verwerking.

Een Azure Functions-app die is geïmplementeerd als aangepaste handler moet de *host.jsop*, *local.settings.jsop* en *function.jsop* bestanden configureren volgens een aantal conventies.

## <a name="application-structure"></a>Toepassings structuur

Als u een aangepaste handler wilt implementeren, hebt u de volgende aspecten nodig voor uw toepassing:

- Een *host.jsin* het bestand in de hoofdmap van uw app
- Een *local.settings.jsin* het bestand in de hoofdmap van uw app
- Een *function.jsin* het bestand voor elke functie (in een map die overeenkomt met de naam van de functie)
- Een opdracht, script of uitvoerbaar bestand, waarmee een webserver wordt uitgevoerd

In het volgende diagram ziet u hoe deze bestanden eruitzien op het bestands systeem voor een functie met de naam ' MyQueueFunction ' en een uitvoerbaar bestand van een aangepaste handler met de naam *handler.exe*.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Configuratie

De toepassing wordt geconfigureerd via de *host.jsop* en *local.settings.jsop* bestanden.

#### <a name="hostjson"></a>host.jsop

*host.js* geeft aan dat de functions-host waar aanvragen moet worden verzonden door een webserver aan te wijzen die http-gebeurtenissen kan verwerken.

Er wordt een aangepaste handler gedefinieerd door de *host.js* te configureren in het bestand met informatie over het uitvoeren van de webserver via de `customHandler` sectie.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

De `customHandler` sectie verwijst naar een doel zoals gedefinieerd door de `defaultExecutablePath` . Het doel van de uitvoering kan een opdracht, een uitvoerbaar bestand of een file zijn waarin de webserver is geïmplementeerd.

Gebruik de `arguments` matrix om eventuele argumenten door te geven aan het uitvoer bare bestand. Argumenten ondersteunen de uitbrei ding van omgevings variabelen (toepassings instellingen) met behulp van een `%%` notatie.

U kunt de werkmap die wordt gebruikt door het uitvoer bare bestand ook wijzigen met `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Ondersteuning voor bindingen

Standaard triggers samen met invoer-en uitvoer bindingen zijn beschikbaar door te verwijzen naar [uitbreidings bundels](./functions-bindings-register.md) in uw *host.js* in het bestand.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.jsop* definieert toepassings instellingen die worden gebruikt bij het lokaal uitvoeren van de functie-app. Omdat het mogelijk geheimen bevat, moet *local.settings.jsop* worden uitgesloten van broncode beheer. In azure gebruikt u in plaats daarvan toepassings instellingen.

Stel voor aangepaste handlers `FUNCTIONS_WORKER_RUNTIME` in op `Custom` in *local.settings.jsop*.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>Functie-meta gegevens

Wanneer u gebruikt met een aangepaste handler, zijn de *function.jsop* inhoud niet verschillend van hoe u een functie onder elke andere context definieert. De enige vereiste is dat *function.jsop* bestanden moet zich in een map bevinden met de naam die overeenkomt met de naam van de functie.

Met de volgende *function.jsop* configureert u een functie met een wachtrij trigger en een binding van de wachtrij-uitvoer. Omdat het zich in een map bevindt met de naam *MyQueueFunction*, wordt een functie gedefinieerd met de naam *MyQueueFunction*.

**MyQueueFunction/function.jsop**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Payload aanvragen

Wanneer een wachtrij bericht wordt ontvangen, verzendt de host functions een HTTP POST-aanvraag naar de aangepaste handler met een nettolading in de hoofd tekst.

De volgende code vertegenwoordigt een nettolading van de voorbeeld aanvraag. De payload bevat een JSON-structuur met twee leden: `Data` en `Metadata` .

Het `Data` lid bevat sleutels die overeenkomen met de namen van de invoer en de trigger, zoals gedefinieerd in de bindingen matrix in de *function.jsvoor* het bestand.

Het `Metadata` lid bevat [meta gegevens die zijn gegenereerd op basis van de bron van de gebeurtenis](./functions-bindings-expressions-patterns.md#trigger-metadata).

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Nettolading van reactie

Per Conventie worden functie reacties opgemaakt als sleutel/waarde-paren. Ondersteunde sleutels zijn onder andere:

| <nobr>Payload-sleutel</nobr>   | Gegevenstype | Opmerkingen                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | Bevat reactie waarden zoals gedefinieerd door de `bindings` matrix in *function.jsop*.<br /><br />Als een functie bijvoorbeeld is geconfigureerd met een wachtrij-uitvoer binding met de naam ' myQueueOutput ', `Outputs` bevat deze een sleutel met `myQueueOutput` de naam, die door de aangepaste handler wordt ingesteld op de berichten die naar de wachtrij worden verzonden. |
| `Logs`        | matrix     | Berichten worden weer gegeven in de functie Logboeken van functies.<br /><br />Bij het uitvoeren in Azure worden berichten weer gegeven in Application Insights. |
| `ReturnValue` | tekenreeks    | Wordt gebruikt om een reactie te geven wanneer een uitvoer is geconfigureerd als `$return` in de *function.jsvoor* het bestand. |

Dit is een voor beeld van een nettolading van een reactie.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Voorbeelden

Aangepaste handlers kunnen worden geïmplementeerd in elke taal die ondersteuning biedt voor het ontvangen van HTTP-gebeurtenissen. In de volgende voor beelden ziet u hoe u een aangepaste handler implementeert met behulp van de programmeer taal go.

### <a name="function-with-bindings"></a>Functie met bindingen

Het scenario dat in dit voor beeld wordt geïmplementeerd, bevat een functie met de naam `order` die een-Payload accepteert die `POST` een product order vertegenwoordigt. Als een order wordt gepost naar de functie, wordt een Queue Storage bericht gemaakt en wordt een HTTP-antwoord geretourneerd.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementatie

In een map met de naam *order* wordt met de *function.jsin* het bestand de functie http-activering geconfigureerd.

**order/function.jsop**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Deze functie is gedefinieerd als een [http-geactiveerde functie](./functions-bindings-http-webhook-trigger.md) die een [http-antwoord](./functions-bindings-http-webhook-output.md) retourneert en een [wachtrij-opslag](./functions-bindings-storage-queue-output.md) bericht uitvoert.

In de hoofdmap van de app is de *host.jsin* het bestand geconfigureerd voor het uitvoeren van een uitvoerbaar bestand met de naam `handler.exe` ( `handler` in Linux of macOS).

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Dit is de HTTP-aanvraag die wordt verzonden naar de functions-runtime.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

De runtime van functions stuurt vervolgens de volgende HTTP-aanvraag naar de aangepaste handler:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Sommige delen van de payload zijn verwijderd voor de boog.

*handler.exe* is het gecompileerde, aangepaste handler-programma dat een webserver uitvoert en reageert op aanvragen voor het aanroepen van de functie van de host functions.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

In dit voor beeld voert de aangepaste handler een webserver uit voor het afhandelen van HTTP-gebeurtenissen en wordt ingesteld om te Luis teren naar aanvragen via de `FUNCTIONS_CUSTOMHANDLER_PORT` .

Hoewel de functie host de oorspronkelijke HTTP-aanvraag op heeft ontvangen `/api/order` , roept deze de aangepaste handler aan met behulp van de naam (de naam van de map). In dit voor beeld wordt de functie gedefinieerd in het pad van `/order` . De host verzendt de aangepaste handler een HTTP-aanvraag op het pad van `/order` .

Wanneer `POST` aanvragen naar deze functie worden verzonden, zijn de meta gegevens van de trigger en de functie beschikbaar via de hoofd tekst van de HTTP-aanvraag. De oorspronkelijke hoofd tekst van de HTTP-aanvraag kan worden geopend in de payload `Data.req.Body` .

De reactie van de functie is ingedeeld in sleutel-waardeparen waarbij het `Outputs` lid een JSON-waarde bevat waarin de sleutels overeenkomen met de uitvoer zoals gedefinieerd in de *function.jsvoor* het bestand.

Dit is een voor beeld-nettolading die door deze handler wordt geretourneerd naar de host functions.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Door de `message` uitvoer gelijk te stellen aan de volg orde van de gegevens in de aanvraag, wordt de functie uitgevoerd die gegevens bestelt in de geconfigureerde wachtrij. De host functions retourneert ook het HTTP-antwoord `res` dat is geconfigureerd voor aan de aanroeper.

### <a name="http-only-function"></a>Alleen HTTP-functie

Voor HTTP-geactiveerde functies zonder extra bindingen of uitvoer, wilt u mogelijk dat uw handler rechtstreeks werkt met de HTTP-aanvraag en het antwoord in plaats van de nettolading van de aangepaste handler- [aanvraag](#request-payload) en de [reactie](#response-payload) . Dit gedrag kan worden geconfigureerd in *host.jsvoor* het gebruik van de `enableForwardingHttpRequest` instelling.

> [!IMPORTANT]
> Het primaire doel van de functie voor aangepaste handlers is om talen en Runtimes in te scha kelen die momenteel geen eersteklas ondersteuning voor de Azure Functions hebben. Hoewel het mogelijk is om webtoepassingen uit te voeren met aangepaste handlers, is Azure Functions geen standaard omgekeerde proxy. Sommige functies, zoals het streamen van antwoorden, HTTP/2 en websockets zijn niet beschikbaar. Sommige onderdelen van de HTTP-aanvraag, zoals bepaalde headers en routes, zijn mogelijk beperkt. Uw toepassing ondervindt mogelijk ook overmatig [koud starten](event-driven-scaling.md#cold-start).
>
> Als u deze omstandigheden wilt aanpakken, kunt u uw web-apps uitvoeren op [Azure app service](../app-service/overview.md).

In het volgende voor beeld ziet u hoe u een door HTTP geactiveerde functie kunt configureren zonder extra bindingen of uitvoer. Het scenario dat in dit voor beeld wordt geïmplementeerd, bevat een functie met de naam `hello` die een `GET` of accepteert `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementatie

In een map met de naam *Hello* wordt de functie met http-activering geconfigureerd door het bestand *function.js* .

**Hallo/function.jsop**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

De functie is geconfigureerd om zowel als-aanvragen te accepteren `GET` `POST` en de resultaat waarde wordt opgegeven via een argument met de naam `res` .

In de hoofdmap van de app is de *host.jsin* het bestand geconfigureerd om te worden uitgevoerd `handler.exe` en `enableForwardingHttpRequest` is ingesteld op `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Als `enableForwardingHttpRequest` `true` dat het geval is, verschilt het gedrag van alleen-HTTP-functies van het standaard gedrag aangepaste handlers op de volgende manieren:

* De HTTP-aanvraag bevat niet de [aanvraag](#request-payload) lading van de aangepaste handlers. In plaats daarvan roept de host functions de handler aan met een kopie van de oorspronkelijke HTTP-aanvraag.
* De host functions roept de handler aan met hetzelfde pad als de oorspronkelijke aanvraag, inclusief eventuele query teken reeks parameters.
* De functie host retourneert een kopie van het HTTP-antwoord van de handler als reactie op de oorspronkelijke aanvraag.

Hier volgt een POST-aanvraag naar de functions-host. De host functions stuurt vervolgens een kopie van de aanvraag naar de aangepaste handler op hetzelfde pad.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

Het bestand *handler. go* implementeert een webserver en http-functie.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

In dit voor beeld maakt de aangepaste handler een webserver voor het afhandelen van HTTP-gebeurtenissen en wordt ingesteld om te Luis teren naar aanvragen via de `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` aanvragen worden verwerkt door een teken reeks te retour neren en `POST` aanvragen hebben toegang tot de hoofd tekst van de aanvraag.

De route voor de functie order is hier `/api/hello` hetzelfde als de oorspronkelijke aanvraag.

>[!NOTE]
>De `FUNCTIONS_CUSTOMHANDLER_PORT` is niet de open bare poort die wordt gebruikt om de functie aan te roepen. Deze poort wordt gebruikt door de host functions om de aangepaste handler aan te roepen.

## <a name="deploying"></a>Installeert

Een aangepaste handler kan worden geïmplementeerd voor elke Azure Functions Hosting optie. Als uw handler afhankelijkheden van het besturings systeem of platform vereist (zoals een taal runtime), moet u mogelijk een [aangepaste container](./functions-create-function-linux-custom-image.md)gebruiken.

Wanneer u een functie-app in azure maakt voor aangepaste handlers, raden we u aan om .NET Core als stack te selecteren. In de toekomst wordt een ' aangepaste ' stack voor aangepaste handlers toegevoegd.

Als u een aangepaste handler-app wilt implementeren met behulp van Azure Functions Core Tools, voert u de volgende opdracht uit.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Zorg ervoor dat alle bestanden die nodig zijn om uw aangepaste handler uit te voeren, zich in de map bevinden en zijn opgenomen in de-implementatie. Als uw aangepaste handler een binair uitvoerbaar bestand is of platformspecifieke afhankelijkheden heeft, moet u ervoor zorgen dat deze bestanden overeenkomen met het doel implementatie platform.

## <a name="restrictions"></a>Beperkingen

- De webserver voor de aangepaste handler moet binnen 60 seconden beginnen.

## <a name="samples"></a>Voorbeelden

Raadpleeg de aangepaste handler-voor beelden [github opslag plaats](https://github.com/Azure-Samples/functions-custom-handlers) voor voor beelden van hoe functies in verschillende talen kunnen worden geïmplementeerd.

## <a name="troubleshooting-and-support"></a>Probleem oplossing en ondersteuning

### <a name="trace-logging"></a>Traceer logboek registratie

Als uw aangepaste handler-proces niet kan worden gestart of als er problemen zijn met de communicatie met de functions-host, kunt u het logboek niveau van de functie-app verhogen om `Trace` meer diagnostische berichten van de host weer te geven.

Als u het standaard logboek niveau van de functie-app wilt wijzigen, configureert u de `logLevel` instelling in de `logging` sectie van *host.jsop*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Met de functions-host worden extra logboek berichten uitgevoerd, inclusief informatie over het proces van de aangepaste handler. Gebruik de logboeken voor het onderzoeken van problemen bij het starten van uw aangepaste handler-proces of het aanroepen van functies in uw aangepaste handler.

Lokaal worden logboeken afgedrukt op de-console.

Zoek in azure [Application Insights traceringen](analyze-telemetry-data.md#query-telemetry-data) om de logboek berichten weer te geven. Als uw app een groot aantal logboeken produceert, worden er alleen een subset van logboek berichten verzonden naar Application Insights. [Schakel steek proeven uit](configure-monitoring.md#configure-sampling) om ervoor te zorgen dat alle berichten worden geregistreerd.

### <a name="test-custom-handler-in-isolation"></a>Aangepaste handler in isolatie testen

Aangepaste handler-apps zijn een webserver proces, dus het kan handig zijn om deze te starten met de eigen en test functie aanroepen door het verzenden van [HTTP-aanvragen](#request-payload) via een hulp programma zoals [krul](https://curl.haxx.se/) of [postman](https://www.postman.com/).

U kunt deze strategie ook gebruiken in uw CI/CD-pijp lijnen om automatische tests uit te voeren op uw aangepaste handler.

### <a name="execution-environment"></a>Uitvoerings omgeving

Aangepaste handlers worden uitgevoerd in dezelfde omgeving als een typische Azure Functions-app. Test uw handler om te controleren of de omgeving alle afhankelijkheden bevat die moeten worden uitgevoerd. Voor apps waarvoor extra afhankelijkheden zijn vereist, moet u deze mogelijk uitvoeren met een [aangepaste container installatie kopie](functions-create-function-linux-custom-image.md) die wordt gehost op Azure functions [Premium-abonnement](functions-premium-plan.md).

### <a name="get-support"></a>Ondersteuning krijgen

Als u hulp nodig hebt bij een functie-app met aangepaste handlers, kunt u een aanvraag indienen via reguliere ondersteunings kanalen. Vanwege de grote verscheidenheid aan mogelijke talen die worden gebruikt voor het bouwen van aangepaste handlers-apps, is ondersteuning niet onbeperkt.

Ondersteuning is beschikbaar als de host functions problemen ondervindt met het starten of communiceren met het aangepaste registratie proces. Voor problemen die betrekking hebben op de interne werking van uw aangepaste handler-proces, zoals problemen met de gekozen taal of het Framework, kan het ondersteunings team geen hulp bieden in deze context.

## <a name="next-steps"></a>Volgende stappen

Aan de slag met het bouwen van een Azure Functions-app in go of roest met de [Snelstartgids voor aangepaste handlers](create-first-function-vs-code-other.md).
