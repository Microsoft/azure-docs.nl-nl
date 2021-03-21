---
title: HTTP-functies in Durable Functions-Azure Functions
description: Meer informatie over de geïntegreerde HTTP-functies in de Durable Functions-extensie voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 64d40de50f21811a56318971de1836abc8fbf8c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027258"
---
# <a name="http-features"></a>HTTP-functies

Durable Functions heeft verschillende functies waarmee u eenvoudig duurzame Orchestrations en entiteiten kunt opnemen in HTTP-werk stromen. In dit artikel vindt u informatie over een aantal van deze functies.

## <a name="exposing-http-apis"></a>HTTP-Api's weer geven

Orchestrations en entiteiten kunnen worden opgeroepen en beheerd met behulp van HTTP-aanvragen. Met de uitbrei ding Durable Functions worden ingebouwde HTTP-Api's weer gegeven. Het biedt ook Api's voor interactie met Orchestrations en entiteiten vanuit in HTTP geactiveerde functies.

### <a name="built-in-http-apis"></a>Ingebouwde HTTP-Api's

Met de extensie Durable Functions wordt automatisch een set HTTP-Api's aan de Azure Functions-host toegevoegd. Met deze Api's kunt u communiceren met en beheren van Orchestrations en entiteiten zonder dat u code hoeft te schrijven.

De volgende ingebouwde HTTP-Api's worden ondersteund.

* [Nieuwe indeling starten](durable-functions-http-api.md#start-orchestration)
* [Query Orchestrator-exemplaar](durable-functions-http-api.md#get-instance-status)
* [Orchestrator-exemplaar beëindigen](durable-functions-http-api.md#terminate-instance)
* [Een externe gebeurtenis naar een indeling verzenden](durable-functions-http-api.md#raise-event)
* [Orchestration-geschiedenis opschonen](durable-functions-http-api.md#purge-single-instance-history)
* [Een bewerkings gebeurtenis naar een entiteit verzenden](durable-functions-http-api.md#signal-entity)
* [De status van een entiteit ophalen](durable-functions-http-api.md#get-entity)
* [De lijst met entiteiten opvragen](durable-functions-http-api.md#list-entities)

Zie het [artikel http-api's](durable-functions-http-api.md) voor een volledige beschrijving van alle ingebouwde http-api's die door de Durable functions-extensie worden weer gegeven.

### <a name="http-api-url-discovery"></a>URL-detectie van HTTP API

De [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) stelt api's beschikbaar die handige nettoladingen voor HTTP-antwoorden kunnen genereren. Het kan bijvoorbeeld een antwoord met koppelingen naar beheer-Api's maken voor een specifiek Orchestration-exemplaar. In de volgende voor beelden ziet u een HTTP-trigger-functie die laat zien hoe u deze API gebruikt voor een nieuw exemplaar van Orchestration:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

---

Het starten van een Orchestrator-functie met behulp van de eerder weer gegeven HTTP-trigger functies kan worden uitgevoerd met behulp van een HTTP-client. Met de volgende krul opdracht start u een Orchestrator-functie met de naam `DoWork` :

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Hier volgt een voor beeld van een antwoord voor een indeling met `abc123` de id. Sommige gegevens zijn verwijderd voor duidelijkheid.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

In het vorige voor beeld is elk van de velden die eindigen op, `Uri` overeenkomen met een ingebouwde HTTP API. U kunt deze Api's gebruiken om het doel exemplaar van Orchestrator te beheren.

> [!NOTE]
> De indeling van de webhook-Url's is afhankelijk van de versie van de Azure Functions host die u uitvoert. Het vorige voor beeld is voor de Azure Functions 2,0-host.

Zie de [http API-verwijzing](durable-functions-http-api.md)voor een beschrijving van alle ingebouwde http-api's.

### <a name="async-operation-tracking"></a>Asynchrone bewerkingen bijhouden

Het HTTP-antwoord dat eerder is vermeld, is ontworpen om te helpen met het implementeren van langlopende HTTP async-Api's met Durable Functions. Dit patroon wordt soms ook wel het *polling Consumer-patroon* genoemd. De client/server-stroom werkt als volgt:

1. De client geeft een HTTP-aanvraag om een langlopend proces te starten, zoals een Orchestrator-functie.
1. De HTTP-trigger van het doel retourneert een HTTP 202-antwoord met een locatie header met de waarde ' statusQueryGetUri '.
1. De client pollt de URL in de locatie-header. De client blijft HTTP 202-antwoorden zien met een locatie header.
1. Wanneer het exemplaar is voltooid of mislukt, retourneert het eind punt in de locatie header HTTP 200.

Dit protocol staat coördinatie toe van langlopende processen met externe clients of services die een HTTP-eind punt kunnen navragen en de locatie header volgen. De client-en server implementaties van dit patroon zijn ingebouwd in de Durable Functions HTTP-Api's.

> [!NOTE]
> Standaard worden alle op HTTP gebaseerde acties die worden geboden door [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) het standaard asynchrone bewerkings patroon ondersteunen. Deze mogelijkheid maakt het mogelijk een langdurige, duurzame functie in te sluiten als onderdeel van een Logic Apps werk stroom. Meer informatie over Logic Apps ondersteuning voor asynchrone HTTP-patronen vindt u in de [Azure Logic apps werk stroom acties en triggers-documentatie](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> U kunt interactie met Orchestrations uitvoeren vanuit elk functie type, niet alleen met HTTP geactiveerde functies.

Zie het [artikel instance Management](durable-functions-instance-management.md)voor meer informatie over het beheren van Orchestrations en entiteiten met behulp van client-api's.

## <a name="consuming-http-apis"></a>HTTP-Api's gebruiken

Zoals beschreven in de [functie code beperkingen van Orchestrator](durable-functions-code-constraints.md), kunnen Orchestrator-functies niet rechtstreeks I/O-bewerkingen uitvoeren. In plaats daarvan roepen ze meestal [activiteit functies](durable-functions-types-features-overview.md#activity-functions) aan die I/O-bewerkingen uitvoeren.

Met ingang van Durable Functions 2,0 kunnen Orchestrations HTTP-Api's systeem eigen gebruiken met behulp van de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger).

In de volgende voorbeeld code ziet u een Orchestrator-functie waarmee een uitgaande HTTP-aanvraag wordt gedaan:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

---

Met behulp van de actie ' HTTP aanroepen ' kunt u de volgende acties uitvoeren in de Orchestrator-functies:

* Roep HTTP-Api's rechtstreeks aan vanuit Orchestration-functies, met enkele beperkingen die later worden beschreven.
* Biedt automatisch ondersteuning voor HTTP 202-status polling-patronen aan de client zijde.
* Gebruik [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md) om geautoriseerde http-aanroepen naar andere Azure-eind punten te maken.

De mogelijkheid om HTTP-Api's rechtstreeks van Orchestrator-functies te gebruiken, is bedoeld als gemak voor een bepaalde reeks veelvoorkomende scenario's. U kunt al deze functies zelf implementeren met behulp van activiteiten functies. In veel gevallen kunt u met activiteit functies meer flexibiliteit bieden.

### <a name="http-202-handling"></a>HTTP 202-verwerking

De API call HTTP kan automatisch de client zijde van het polling Consumer-patroon implementeren. Als een aangeroepen API een HTTP 202-antwoord retourneert met een locatie header, pollt de Orchestrator-functie automatisch de locatie bron totdat er een antwoord wordt ontvangen dat verschilt van 202. Dit antwoord is het antwoord dat wordt geretourneerd door de functie code van Orchestrator.

> [!NOTE]
> Orchestrator functions biedt ook systeem eigen ondersteuning voor het polling-consument patroon aan de server zijde, zoals beschreven in [async-bewerkingen bijhouden](#async-operation-tracking). Deze ondersteuning betekent dat indelingen in één functie-app de Orchestrator-functies in andere functie-apps eenvoudig kunnen coördineren. Dit is vergelijkbaar met het principe van de [Sub-Orchestration](durable-functions-sub-orchestrations.md) , maar met ondersteuning voor communicatie tussen apps. Deze ondersteuning is vooral nuttig voor de ontwikkeling van micro service-apps.

### <a name="managed-identities"></a>Beheerde identiteiten

Durable Functions ondersteunt systeem eigen aanroepen naar Api's die Azure Active Directory-tokens (Azure AD) voor autorisatie accepteren. Deze ondersteuning maakt gebruik van door [Azure beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) voor het verkrijgen van deze tokens.

De volgende code is een voor beeld van een .NET Orchestrator-functie. De functie maakt geverifieerde aanroepen om een virtuele machine opnieuw op te starten met behulp van de Azure Resource Manager [virtuele machines rest API](/rest/api/compute/virtualmachines).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}/restart?api-version={api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

---

In het vorige voor beeld `tokenSource` is de para meter geconfigureerd voor het verkrijgen van Azure AD-tokens voor [Azure Resource Manager](../../azure-resource-manager/management/overview.md). De tokens worden geïdentificeerd door de resource-URI `https://management.core.windows.net/.default` . In het voor beeld wordt ervan uitgegaan dat de huidige functie-app lokaal wordt uitgevoerd of is geïmplementeerd als een functie-app met een beheerde identiteit. Er wordt van uitgegaan dat de lokale identiteit of de beheerde identiteit gemachtigd is voor het beheren van virtuele machines in de opgegeven resource groep `myRG` .

Tijdens runtime retourneert de geconfigureerde token bron automatisch een OAuth 2,0-toegangs token. De bron voegt vervolgens het token toe als een Bearer-token aan de autorisatie-header van de uitgaande aanvraag. Dit model is een verbetering ten aanzien van het hand matig toevoegen van autorisatie headers aan HTTP-aanvragen om de volgende redenen:

* Het vernieuwen van tokens wordt automatisch afgehandeld. U hoeft zich geen zorgen te maken over verlopen tokens.
* Tokens worden nooit opgeslagen in de duurzame indelings status.
* U hoeft geen code te schrijven om het ophalen van tokens te beheren.

In het [vooraf gecompileerde](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)voor beeld van C# RestartVMs vindt u meer informatie over een vollediger.

Beheerde identiteiten zijn niet beperkt tot Azure resource management. U kunt beheerde identiteiten gebruiken om toegang te krijgen tot elke API die Azure AD Bearer-tokens accepteert, waaronder Azure-Services van micro soft en web apps van partners. Een web-app van een partner kan zelfs een andere functie-app zijn. Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor een lijst met Azure-Services van micro soft die ondersteuning bieden voor verificatie met Azure AD.

### <a name="limitations"></a>Beperkingen

De ingebouwde ondersteuning voor het aanroepen van HTTP-Api's is een gebruiks vriendelijke functie. Het is niet geschikt voor alle scenario's.

HTTP-aanvragen die worden verzonden door Orchestrator-functies en de bijbehorende antwoorden, worden geserialiseerd en blijvend als wachtrij berichten. Dit gedrag van de wachtrij zorgt ervoor dat HTTP-aanroepen [betrouwbaar en veilig zijn voor Orchestration replay](durable-functions-orchestrations.md#reliability). Het wachtrij gedrag heeft echter ook beperkingen:

* Elke HTTP-aanvraag omvat extra latentie in vergelijking met een systeem eigen HTTP-client.
* Grote aanvraag-of antwoord berichten die niet in een wachtrij bericht passen, kunnen de Orchestration-prestaties aanzienlijk afnemen. De overhead van het offloaden van bericht payloads naar Blob-opslag kan leiden tot verminderde prestaties.
* Het streamen, chunked en binaire payloads worden niet ondersteund.
* De mogelijkheid om het gedrag van de HTTP-client aan te passen, is beperkt.

Als een van deze beperkingen van invloed kan zijn op uw use-case, kunt u overwegen om de activiteit functies en taalspecifieke HTTP-client bibliotheken te gebruiken om uitgaande HTTP-aanroepen te maken.

> [!NOTE]
> Als u een .NET-ontwikkelaar bent, kunt u zich afvragen waarom deze functie gebruikmaakt van de typen **DurableHttpRequest** en **DurableHttpResponse** in plaats van de ingebouwde .net **HttpRequestMessage** -en **HttpResponseMessage** -typen.
>
> Dit is een opzettelijke ontwerpkeuze. De belangrijkste reden hiervoor is dat gebruikers bij aangepaste typen ervoor zorgen dat ze geen onjuiste veronderstellingen doen over het ondersteunde gedrag van de interne HTTP-client. De typen die specifiek zijn voor Durable Functions, maken het mogelijk om het API-ontwerp te vereenvoudigen. Ze kunnen ook eenvoudiger beschik bare speciale functies, zoals [beheerde identiteits integratie](#managed-identities) en het [polling Consumer-patroon](#http-202-handling)maken. 

### <a name="extensibility-net-only"></a>Uitbreid baarheid (alleen .NET)

Het aanpassen van het gedrag van de interne HTTP-client van de Orchestration kan worden uitgevoerd met behulp van [Azure functions .net-afhankelijkheids injectie](../functions-dotnet-dependency-injection.md). Deze mogelijkheid kan nuttig zijn om kleine gedrags wijzigingen aan te brengen. Het kan ook handig zijn voor het testen van de HTTP-client door het injecteren van object-objecten.

In het volgende voor beeld wordt gedemonstreerd met behulp van afhankelijkheids injectie voor het uitschakelen van TLS/SSL-certificaat validatie voor Orchestrator-functies die externe HTTP-eind punten aanroepen.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over duurzame entiteiten](durable-functions-entities.md)
