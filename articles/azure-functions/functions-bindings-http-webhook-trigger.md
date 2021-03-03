---
title: HTTP-trigger Azure Functions
description: Meer informatie over het aanroepen van een Azure-functie via HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: a9bb87206ccb0dca56c1744d5578eac7a17418c7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726390"
---
# <a name="azure-functions-http-trigger"></a>HTTP-trigger Azure Functions

Met de HTTP-trigger kunt u een functie aanroepen met een HTTP-aanvraag. U kunt een HTTP-trigger gebruiken om serverloze Api's te maken en te reageren op webhooks.

De standaard retour waarde voor een door HTTP geactiveerde functie is:

- `HTTP 204 No Content` met een lege hoofd tekst in de functies 2. x en hoger
- `HTTP 200 OK` met een lege hoofd tekst in functions 1. x

Als u het HTTP-antwoord wilt wijzigen, configureert u een [uitvoer binding](./functions-bindings-http-webhook-output.md).

Zie [overzicht](./functions-bindings-http-webhook.md) en [uitvoer binding referentie](./functions-bindings-http-webhook-output.md)voor meer informatie over http-bindingen.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die zoekt naar een `name` para meter in de query teken reeks of de hoofd tekst van de HTTP-aanvraag. U ziet dat de retour waarde wordt gebruikt voor de uitvoer binding, maar een retour waarde-kenmerk is niet vereist.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een trigger binding in een *function.jsin* een bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-script code die wordt gekoppeld aan `HttpRequest` :

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

U kunt een verbinding maken met een aangepast object in plaats van `HttpRequest` . Dit object wordt gemaakt op basis van de hoofd tekst van de aanvraag en geparseerd als JSON. Op dezelfde manier kan een type worden door gegeven aan de HTTP-antwoord uitvoer binding en geretourneerd als de antwoord tekst, samen met een `200` status code.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="java"></a>[Java](#tab/java)

* [De para meter lezen uit de query reeks](#read-parameter-from-the-query-string)
* [Hoofd tekst van een POST-aanvraag lezen](#read-body-from-a-post-request)
* [Para meter lezen vanuit een route](#read-parameter-from-a-route)
* [POJO hoofd tekst van een POST-aanvraag lezen](#read-pojo-body-from-a-post-request)

In de volgende voor beelden ziet u de binding HTTP-trigger.

#### <a name="read-parameter-from-the-query-string"></a>De para meter lezen uit de query reeks

In dit voor beeld wordt een para meter `id` met de naam van de query teken reeks gelezen en wordt deze gebruikt om een JSON-document te maken dat wordt geretourneerd naar de client, met het inhouds type `application/json` .

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Hoofd tekst van een POST-aanvraag lezen

In dit voor beeld wordt de hoofd tekst van een POST-aanvraag gelezen als een `String` , en wordt deze gebruikt om een JSON-document te maken dat wordt geretourneerd naar de client, met het inhouds type `application/json` .

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Para meter lezen vanuit een route

In dit voor beeld worden een verplichte para meter, `id` een naam en een optionele para meter `name` van het routenet werk gelezen en worden deze gebruikt om een JSON-document te maken dat wordt geretourneerd naar de client, met het inhouds type `application/json` . T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>POJO hoofd tekst van een POST-aanvraag lezen

Hier volgt de code voor de `ToDoItem` klasse, waarnaar wordt verwezen in dit voor beeld:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

In dit voor beeld wordt de hoofd tekst van een POST-aanvraag gelezen. De aanvraag tekst wordt automatisch gedeserialiseerd in een `ToDoItem` object en wordt geretourneerd naar de client met het inhouds type `application/json` . De `ToDoItem` para meter wordt geserialiseerd door de functions-runtime, omdat deze is toegewezen aan de `body` eigenschap van de `HttpMessageResponse.Builder` klasse.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een trigger binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u een trigger binding in een *function.jsin* een bestand en een [Power shell-functie](functions-reference-node.md). De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een trigger binding in een *function.jsin* het bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie zoekt naar een `name` para meter in de query reeks of de hoofd tekst van de HTTP-aanvraag.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        }
    ]
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de Python-code:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

In [C#-klassen bibliotheken](functions-dotnet-class-library.md) en Java `HttpTrigger` is het kenmerk beschikbaar voor het configureren van de functie.

U kunt het autorisatie niveau en toegestane HTTP-methoden instellen in de para meters van de kenmerk-constructor, het type webhook en een route sjabloon. Zie [configuratie](#configuration)voor meer informatie over deze instellingen.

# <a name="c"></a>[C#](#tab/csharp)

In dit voor beeld ziet u hoe u het kenmerk [http trigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) gebruikt.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Zie voor een volledig voor beeld het [voor beeld](#example)van de trigger.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

In dit voor beeld ziet u hoe u het kenmerk [http trigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) gebruikt.

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Zie voor een volledig voor beeld het [voor beeld](#example)van de trigger.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `HttpTrigger`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
| **type** | N.v.t.| Vereist: moet worden ingesteld op `httpTrigger` . |
| **direction** | N.v.t.| Vereist: moet worden ingesteld op `in` . |
| **name** | N.v.t.| Vereist: de naam van de variabele die wordt gebruikt in de functie code voor de aanvraag of aanvraag tekst. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Bepaalt welke sleutels, indien aanwezig, moeten aanwezig zijn in de aanvraag om de functie te kunnen aanroepen. Het autorisatie niveau kan een van de volgende waarden hebben: <ul><li><code>anonymous</code>&mdash;Er is geen API-sleutel vereist.</li><li><code>function</code>&mdash;Een functie-specifieke API-sleutel is vereist. Dit is de standaard waarde als er geen is ingesteld.</li><li><code>admin</code>&mdash;De hoofd sleutel is vereist.</li></ul> Zie de sectie over [autorisatie sleutels](#authorization-keys)voor meer informatie. |
| **technieken** |**Methoden** | Een matrix van de HTTP-methoden waarop de functie reageert. Als deze niet wordt opgegeven, reageert de functie op alle HTTP-methoden. Zie [het HTTP-eind punt aanpassen](#customize-the-http-endpoint). |
| **route** | **Route** | Hiermee wordt de route sjabloon gedefinieerd, waarmee wordt bepaald welke Url's van aanvragen uw functie reageert. De standaard waarde als er geen wordt gegeven, is `<functionname>` . Zie [het HTTP-eind punt aanpassen](#customize-the-http-endpoint)voor meer informatie. |
| **webHookType** | **WebHookType** | _Alleen ondersteund voor de runtime van versie 1. x._<br/><br/>Hiermee wordt de HTTP-trigger geconfigureerd om te fungeren als een [webhook](https://en.wikipedia.org/wiki/Webhook) -ontvanger voor de opgegeven provider. Stel de eigenschap niet in `methods` Als u deze eigenschap instelt. Het type webhook kan een van de volgende waarden hebben:<ul><li><code>genericJson</code>&mdash;Een webhook-eind punt voor algemeen gebruik zonder logica voor een specifieke provider. Met deze instelling worden aanvragen beperkt tot gebruikers met behulp van HTTP POST en met het `application/json` inhouds type.</li><li><code>github</code>&mdash;De functie reageert op [github-webhooks](https://developer.github.com/webhooks/). Gebruik niet de eigenschap  _authLevel_ met github-webhooks. Zie de sectie GitHub-webhooks verderop in dit artikel voor meer informatie.</li><li><code>slack</code>&mdash;De functie reageert op [toegestane webhooks](https://api.slack.com/outgoing-webhooks). Gebruik niet de eigenschap _authLevel_ met toegestane webhooks. Zie de sectie over toegestane webhooks verderop in dit artikel voor meer informatie.</li></ul>|

## <a name="payload"></a>Nettolading

Het invoer type van de trigger wordt gedeclareerd als ofwel `HttpRequest` een aangepast type. Als u kiest `HttpRequest` , krijgt u volledige toegang tot het aanvraag object. Voor een aangepast type probeert de runtime de JSON-aanvraag tekst te parseren om de object eigenschappen in te stellen.

## <a name="customize-the-http-endpoint"></a>Het HTTP-eind punt aanpassen

Wanneer u een functie voor een HTTP-trigger maakt, is de functie standaard adresseerbaar met een route van het formulier:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

U kunt deze route aanpassen met behulp van de optionele `route` eigenschap voor de invoer binding van de HTTP-trigger. Als voor beeld wordt met de volgende *function.jsin* file een `route` eigenschap gedefinieerd voor een http-trigger:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Met deze configuratie is de functie nu adresseerbaar met de volgende route in plaats van de oorspronkelijke route.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Met deze configuratie kan de functie code twee para meters ondersteunen in het adres, de _categorie_ en de _id_.

# <a name="c"></a>[C#](#tab/csharp)

U kunt elke [Web-API-route beperking](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) gebruiken met de para meters. De volgende C#-functie code maakt gebruik van beide para meters.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

U kunt elke [Web-API-route beperking](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) gebruiken met de para meters. De volgende C#-functie code maakt gebruik van beide para meters.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="java"></a>[Java](#tab/java)

De context van de functie-uitvoering is de eigenschappen die zijn gedeclareerd in het `HttpTrigger` kenmerk. Met het kenmerk kunt u route parameters, autorisatie niveaus, HTTP-woorden en het exemplaar van de inkomende aanvraag definiëren.

Route parameters worden gedefinieerd via het- `HttpTrigger` kenmerk.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het knoop punt biedt de functions-runtime de aanvraag tekst van het `context` object. Zie voor meer informatie het [voor beeld van Java script-trigger](#example).

In het volgende voor beeld ziet u hoe route parameters van worden gelezen `context.bindingData` .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Route parameters die zijn gedeclareerd in de *function.jsop* bestand zijn toegankelijk als eigenschap van het `$Request.Params` object.

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

De context van de functie-uitvoering wordt weer gegeven via een para meter die is gedeclareerd als `func.HttpRequest` . Met dit exemplaar kunt u een functie gebruiken om toegang te krijgen tot gegevens route parameters, waarden van query reeksen en methoden waarmee u HTTP-antwoorden kunt ophalen.

Eenmaal gedefinieerd, zijn de route parameters beschikbaar voor de functie door de methode aan te roepen `route_params` .

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

Standaard worden alle functie routes voorafgegaan door *API*. U kunt het voor voegsel ook aanpassen of verwijderen met behulp van de `extensions.http.routePrefix` eigenschap in uw [host.jsvoor](functions-host-json.md) het bestand. In het volgende voor beeld wordt het voor voegsel *API* -route verwijderd met behulp van een lege teken reeks voor het voor voegsel in de *host.jsin* het bestand.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Route parameters gebruiken

Route parameters die een functie `route` patroon hebben gedefinieerd, zijn beschikbaar voor elke binding. Als u bijvoorbeeld een route hebt gedefinieerd als, `"route": "products/{id}"` kan een tabel opslag binding de waarde van de `{id}` para meter in de bindings configuratie gebruiken.

In de volgende configuratie ziet u hoe de `{id}` para meter wordt door gegeven aan de binding `rowKey` .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

Wanneer u route parameters gebruikt, `invoke_URL_template` wordt er automatisch een gemaakt voor uw functie. Uw clients kunnen de URL-sjabloon gebruiken om inzicht te krijgen in de para meters die moeten worden door gegeven in de URL wanneer de functie wordt aangeroepen met behulp van de URL. Navigeer naar een van de functies die door HTTP worden geactiveerd in de [Azure Portal](https://portal.azure.com) en selecteer **functie-URL ophalen**.

U kunt via een programma toegang krijgen tot de `invoke_URL_template` met behulp van de Azure Resource Manager-api's voor [lijst functies](/rest/api/appservice/webapps/listfunctions) of [Get-functie](/rest/api/appservice/webapps/getfunction).

## <a name="working-with-client-identities"></a>Werken met client identiteiten

Als uw functie-app gebruikmaakt van [app service verificatie/autorisatie](../app-service/overview-authentication-authorization.md), kunt u informatie weer geven over geverifieerde clients vanuit uw code. Deze informatie is beschikbaar als [aanvraag headers die zijn geïnjecteerd door het platform](../app-service/app-service-authentication-how-to.md#access-user-claims).

U kunt deze informatie ook lezen van bindings gegevens. Deze functie is alleen beschikbaar voor de functions-runtime in 2. x en hoger. Het is momenteel alleen beschikbaar voor .NET-talen.

# <a name="c"></a>[C#](#tab/csharp)

Informatie over geverifieerde clients is beschikbaar als een [claimsprincipal is](/dotnet/api/system.security.claims.claimsprincipal). De Claimsprincipal is is beschikbaar als onderdeel van de context van de aanvraag, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

U kunt de Claimsprincipal is ook gewoon opnemen als een extra para meter in de functie handtekening:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Informatie over geverifieerde clients is beschikbaar als een [claimsprincipal is](/dotnet/api/system.security.claims.claimsprincipal). De Claimsprincipal is is beschikbaar als onderdeel van de context van de aanvraag, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

U kunt de Claimsprincipal is ook gewoon opnemen als een extra para meter in de functie handtekening:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="java"></a>[Java](#tab/java)

De geverifieerde gebruiker is beschikbaar via [http-headers](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

De geverifieerde gebruiker is beschikbaar via [http-headers](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

De geverifieerde gebruiker is beschikbaar via [http-headers](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

De geverifieerde gebruiker is beschikbaar via [http-headers](../app-service/app-service-authentication-how-to.md#access-user-claims).


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Functie toegangs toetsen

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Sleutels verkrijgen

Sleutels worden opgeslagen als onderdeel van de functie-app in Azure en worden op rest versleuteld. Als u de sleutels wilt weer geven, nieuwe wilt maken of sleutels naar nieuwe waarden wilt versleutelen, gaat u naar een van de functies die via HTTP worden geactiveerd in de [Azure Portal](https://portal.azure.com) en selecteert u **functie toetsen**.

U kunt ook host-sleutels beheren. Navigeer naar de functie-app in het [Azure Portal](https://portal.azure.com) en selecteer **app-sleutels**.

U kunt functie-en host-sleutels programmatisch verkrijgen met behulp van de Azure Resource Manager-Api's. Er zijn Api's voor het [weer geven van functie sleutels](/rest/api/appservice/webapps/listfunctionkeys) en het [weer geven van host-sleutels](/rest/api/appservice/webapps/listhostkeys), en wanneer u implementatie-sleuven gebruikt, zijn de equivalente Api's een [lijst functie sleutels sleuf](/rest/api/appservice/webapps/listfunctionkeysslot) en [sleuf voor host-sleutels weer geven](/rest/api/appservice/webapps/listhostkeysslot).

U kunt ook op een programmatische manier nieuwe functie-en host-sleutels maken met behulp van de [functie geheim maken of bijwerken](/rest/api/appservice/webapps/createorupdatefunctionsecret), een [geheime sleuf maken](/rest/api/appservice/webapps/createorupdatefunctionsecretslot)of bijwerken, een geheim voor het [hosten](/rest/api/appservice/webapps/createorupdatehostsecret) maken of bijwerken en de geheime sleuf-api's voor [hosts maken of bijwerken](/rest/api/appservice/webapps/createorupdatehostsecretslot) .

Functie-en host-sleutels kunnen via een programma worden verwijderd met behulp van de [verwijderen functie geheim](/rest/api/appservice/webapps/deletefunctionsecret), [functie geheime sleuf](/rest/api/appservice/webapps/deletefunctionsecretslot)verwijderen, [host Secret verwijderen](/rest/api/appservice/webapps/deletehostsecret)en host-api's van de [geheime sleuf](/rest/api/appservice/webapps/deletehostsecretslot) verwijderen.

U kunt ook de [oudere api's voor sleutel beheer gebruiken om functie sleutels te verkrijgen](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), maar met behulp van de Azure Resource Manager-api's wordt in plaats daarvan aanbevolen.

## <a name="api-key-authorization"></a>Verificatie van API-sleutels

Voor de meeste HTTP-trigger sjablonen is een API-sleutel in de aanvraag vereist. Uw HTTP-aanvraag lijkt daarom normaal te lijken op de volgende URL:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

De sleutel kan worden opgenomen in een query reeks variabele met de naam `code` , zoals hierboven is beschreven. Het kan ook worden opgenomen in een `x-functions-key` http-header. De waarde van de sleutel kan een functie sleutel zijn die is gedefinieerd voor de functie of een wille keurige host-sleutel.

U kunt anonieme aanvragen toestaan waarvoor geen sleutels zijn vereist. U kunt ook vereisen dat de hoofd sleutel wordt gebruikt. U wijzigt het standaard autorisatie niveau met behulp van de `authLevel` eigenschap in de JSON van de binding. Zie [trigger-configuratie](#configuration)voor meer informatie.

> [!NOTE]
> Bij het lokaal uitvoeren van functies wordt de autorisatie uitgeschakeld, ongeacht de opgegeven instelling voor het autorisatie niveau. Na het publiceren naar Azure `authLevel` wordt de instelling in de trigger afgedwongen. Sleutels zijn nog steeds vereist [voor het lokaal uitvoeren in een container](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Een HTTP-eind punt in de productie omgeving beveiligen

Als u uw functie-eind punten in productie volledig wilt beveiligen, kunt u overwegen om een van de volgende functie-beveiligings opties op app-niveau te implementeren. Wanneer u een van deze beveiligings methoden op app-niveau gebruikt, moet u het door HTTP geactiveerde functie autorisatie niveau instellen op `anonymous` .

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>Uw functie-app in isolatie implementeren

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> De webhook-modus is alleen beschikbaar voor versie 1. x van de functions-runtime. Deze wijziging is doorgevoerd om de prestaties van HTTP-triggers in versie 2. x en hoger te verbeteren.

In versie 1. x, webhook-sjablonen bieden extra validatie voor webhook-payloads. In versie 2. x en hoger werkt de HTTP-basis trigger nog steeds en is de aanbevolen benadering voor webhooks. 

### <a name="github-webhooks"></a>GitHub-webhooks

Als u wilt reageren op GitHub-webhooks, maakt u eerst uw functie met een HTTP-trigger en stelt u de eigenschap **webHookType** in op `github` . Kopieer vervolgens de URL en API-sleutel naar de pagina **webhook toevoegen** van uw github-opslag plaats. 

![Scherm afbeelding die laat zien hoe u een webhook voor uw functie kunt toevoegen.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Toegestane webhooks

De toegestane webhook genereert een token voor u in plaats van door u in te stellen. u moet dus een functie-specifieke sleutel met het token configureren van toegestane vertraging. Zie [autorisatie sleutels](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooks en sleutels

Webhook-autorisatie wordt verwerkt door het onderdeel webhook-ontvanger, onderdeel van de HTTP-trigger en het mechanisme varieert op basis van het type webhook. Elk mechanisme is afhankelijk van een sleutel. Standaard wordt de functie sleutel met de naam ' default ' gebruikt. Als u een andere sleutel wilt gebruiken, configureert u de webhook-provider om de sleutel naam met de aanvraag te verzenden op een van de volgende manieren:

* **Query reeks**: de provider geeft de sleutel naam door in de `clientid` query teken reeks parameter, zoals `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` .
* **Aanvraag header**: de provider geeft de sleutel naam in de `x-functions-clientid` header door.

## <a name="content-types"></a>Inhouds typen

Als u binaire en formulier gegevens wilt door geven aan een niet-C #-functie, moet u de juiste content-type-header gebruiken. Ondersteunde inhouds typen zijn `octet-stream` voor binaire gegevens en [meerdelige typen](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart).

### <a name="known-issues"></a>Bekende problemen

In niet-C #-functies worden aanvragen die worden verzonden met het inhouds type, verwerkt `image/jpeg` in een `string` waarde die is door gegeven aan de functie. In dergelijke gevallen kunt u de waarde hand matig converteren `string` naar een byte matrix om toegang te krijgen tot de onbewerkte binaire gegevens.

## <a name="limits"></a>Limieten

De lengte van de HTTP-aanvraag is beperkt tot 100 MB (104.857.600 bytes) en de URL-lengte is beperkt tot 4 KB (4.096 bytes). Deze limieten worden opgegeven door het `httpRuntime` element van het [Web.config-bestand](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config)van de runtime.

Als een functie die de HTTP-trigger gebruikt, niet binnen 230 seconden wordt voltooid, wordt een time-out opgetreden in de [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) en wordt een HTTP 502-fout geretourneerd. De functie wordt nog steeds uitgevoerd, maar kan geen HTTP-antwoord retour neren. Voor langlopende functies wordt u aangeraden async-patronen te volgen en een locatie te retour neren waar u de status van de aanvraag kunt pingen. Zie voor meer informatie over hoe lang een functie kan worden uitgevoerd het [plan voor schalen en hosten](functions-scale.md#timeout).


## <a name="next-steps"></a>Volgende stappen

- [Een HTTP-antwoord retour neren van een functie](./functions-bindings-http-webhook-output.md)