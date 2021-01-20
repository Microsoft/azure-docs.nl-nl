---
title: Een RESTful-eindpunt maken
description: In deze zelfstudie ziet u hoe u een RESTful-eindpunt kunt ontwerpen voor aangepaste providers. Het biedt details over het verwerken van aanvragen en antwoorden voor de ondersteunde RESTful HTTP-methoden.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200206"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Een RESTful-eindpunt ontwerpen voor aangepaste providers

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers kunt u werkstromen in Azure aanpassen. In deze zelfstudie ziet u hoe u een aangepast RESTful-eindpunt kunt ontwerpen. Raadpleeg [het overzicht van aangepaste resourceproviders](overview.md) als u niet bekend bent met Azure Custom-providers.

> [!NOTE]
> Deze zelfstudie bouwt verder op de zelfstudie [Azure Functions instellen voor Azure Custom-providers](./tutorial-custom-providers-function-setup.md). Sommige stappen in deze zelfstudie werken alleen als er een Azure-functie-app is ingesteld voor gebruik met aangepaste providers.

## <a name="work-with-custom-actions-and-custom-resources"></a>Werken met aangepaste acties en aangepaste resources

In deze zelfstudie werkt u de functie-app bij zodat deze werkt als een RESTful-eindpunt voor uw aangepaste provider. Resources en acties in Azure zijn gemodelleerd volgens de volgende standaard-RESTful-specificatie:

- **PUT**: Een nieuwe resource maken
- **GET (instantie)** : Een bestaande resource ophalen
- **DELETE**: Een bestaande resource verwijderen
- **POST**: Een actie activeren
- **GET (verzameling)** : Alle bestaande resources weergeven

 Voor deze zelfstudie gebruikt u Azure-tabelopslag. Maar het werkt met elke database- of opslagservice.

## <a name="partition-custom-resources-in-storage"></a>Aangepaste resources in de opslag partitioneren

Omdat u een RESTful-service maakt, moet u de gemaakte resources opslaan. Voor Azure-tabelopslag moet u partitie- en rijsleutels genereren voor uw gegevens. Voor aangepaste providers moeten gegevens worden gepartitioneerd voor de aangepaste provider. Wanneer een binnenkomende aanvraag wordt verzonden naar de aangepaste provider, voegt de aangepaste provider de header `x-ms-customproviders-requestpath` toe aan uitgaande aanvragen bij het eindpunt.

In het volgende voorbeeld ziet u een header `x-ms-customproviders-requestpath` voor een aangepaste resource:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Op basis van de header `x-ms-customproviders-requestpath` uit het voorbeeld kunt u de parameters *partitionKey* en *rowKey* voor uw opslag maken, zoals wordt weergegeven in de volgende tabel:

Parameter | Template | Beschrijving
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Met de parameter *partitionKey* wordt opgegeven hoe de gegevens worden gepartitioneerd. Meestal worden de gegevens gepartitioneerd met de instantie van de aangepaste provider.
*rowKey* | `{myResourceType}:{myResourceName}` | Met de parameter *rowKey* wordt de afzonderlijke id voor de gegevens opgegeven. Meestal is de id de naam van de resource.

U moet ook een nieuwe klasse maken om de aangepaste resource te modelleren. In deze zelfstudie voegt u de volgende klasse **CustomResource** toe aan de functie-app:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** is een eenvoudige, generieke klasse die alle invoergegevens accepteert. Deze klasse is gebaseerd op **TableEntity**, dat wordt gebruikt om gegevens op te slaan. De klasse **CustomResource** neemt twee eigenschappen over van **TableEntity**: **partitionKey** en **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>RESTful-methoden voor aangepaste provider ondersteunen

> [!NOTE]
> Als u de code niet rechtstreeks vanuit deze zelfstudie kopieert, moet de inhoud van het antwoord een geldige JSON zijn waarmee de header `Content-Type` wordt ingesteld op `application/json`.

Nu u het partitioneren van gegevens hebt ingesteld, maakt u de standaard-CRUD-methode en triggermethode voor aangepaste resources en aangepaste acties. Omdat aangepaste providers als proxy's fungeren, moet het RESTful-eindpunt de aanvraag en het antwoord modelleren en verwerken. De volgende codefragmenten laten zien hoe u de eenvoudige standaard-RESTful-bewerkingen kunt verwerken.

### <a name="trigger-a-custom-action"></a>Een aangepaste actie activeren

Voor aangepaste providers wordt een aangepaste actie geactiveerd via POST-aanvragen. Een aangepaste actie kan optioneel een aanvraagtekst accepteren die een set invoerparameters bevat. De actie retourneert vervolgens een antwoord met het resultaat van de actie en of deze is geslaagd of mislukt.

Voeg de volgende methode **TriggerCustomAction** toe aan de functie-app:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

De methode **TriggerCustomAction** accepteert een binnenkomende aanvraag en weerkaatst simpelweg het antwoord met een statuscode.

### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

Voor aangepaste providers wordt een aangepaste resource gemaakt via PUT-aanvragen. De aangepaste provider accepteert een JSON-aanvraagtekst met een set eigenschappen voor de aangepaste resource. Resources in Azure volgen een RESTful-model. U kunt dezelfde aanvraag-URL gebruiken om een resource te maken, op te halen of te verwijderen.

Voeg de volgende methode **CreateCustomResource** toe om nieuwe resources te maken:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Met de methode **CreateCustomResource** wordt de binnenkomende aanvraag bijgewerkt met de Azure-specifieke velden **id**, **naam** en **type**. Deze velden zijn eigenschappen op het hoogste niveau die worden gebruikt voor services in Azure. Ze zorgen ervoor dat de aangepaste provider kan samenwerken met andere services, zoals Azure Policy, Azure Resource Manager-sjablonen en Azure-activiteitenlogboek.

Eigenschap | Voorbeeld | Beschrijving
---|---|---
**name** | {myCustomResourceName} | De naam van de aangepaste resource
**type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | De naamruimte resource-type
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | De resource-id

Naast het toevoegen van de eigenschappen hebt u het JSON-document ook opgeslagen in Azure-tabelopslag.

### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

Voor aangepaste providers wordt een aangepaste resource opgehaald via GET-aanvragen. Een aangepaste provider accepteert een JSON-aanvraagtekst *niet*. Voor GET-aanvragen gebruikt het eindpunt de header `x-ms-customproviders-requestpath` om de al gemaakte resource te retourneren.

Voeg de volgende methode **RetrieveCustomResource** toe om bestaande resources op te halen:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Resources in Azure volgen een RESTful-model. De aanvraag-URL waarmee een resource wordt gemaakt, retourneert ook de resource als een GET-aanvraag wordt uitgevoerd.

### <a name="remove-a-custom-resource"></a>Een aangepaste resource verwijderen

Voor aangepaste providers wordt een aangepaste resource verwijderd via DELETE-aanvragen. Een aangepaste provider accepteert een JSON-aanvraagtekst *niet*. Voor DELETE-aanvragen gebruikt het eindpunt de header `x-ms-customproviders-requestpath` om de al gemaakte resource te verwijderen.

Voeg de volgende methode **RemoveCustomResource** toe om bestaande resources te verwijderen:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Resources in Azure volgen een RESTful-model. De aanvraag-URL waarmee een resource wordt gemaakt, verwijdert de resource ook als een DELETE-aanvraag wordt uitgevoerd.

### <a name="list-all-custom-resources"></a>Alle aangepaste resources weergeven

Voor aangepaste providers kunt u een lijst met bestaande aangepaste resources opsommen met behulp van GET-verzamelaanvragen. Een aangepaste provider accepteert een JSON-aanvraagtekst *niet*. Voor een verzameling GET-aanvragen gebruikt het eindpunt de header `x-ms-customproviders-requestpath` om de al gemaakte resource op te sommen.

Voeg de volgende methode **EnumerateAllCustomResources** toe om de bestaande resources op te sommen:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> De RowKey QueryComparisons.GreaterThan en QueryComparisons.LessThan vormen syntaxis voor Azure-tabelopslag voor het uitvoeren van een startswith-query voor tekenreeksen.

Als u alle bestaande resources wilt weergeven, genereert u een Azure-tabelopslagquery die ervoor zorgt dat de resources bestaan onder uw aangepaste providerpartitie. De query controleert vervolgens of de rij begint met dezelfde waarde `{myResourceType}`.

## <a name="integrate-restful-operations"></a>RESTful-bewerkingen integreren

Nadat alle RESTful-methoden zijn toegevoegd aan de functie-app, werkt u de methode **Run** bij waarmee de functies worden aangeroepen voor het verwerken van de verschillende REST-aanvragen:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

De bijgewerkte methode **Run** omvat nu de invoerbinding *tableStorage* die u hebt toegevoegd voor Azure-tabelopslag. Met het eerste deel van de methode wordt de header `x-ms-customproviders-requestpath` gelezen en de bibliotheek `Microsoft.Azure.Management.ResourceManager.Fluent` gebruikt om de waarde te parseren als een resource-id. De header `x-ms-customproviders-requestpath` wordt verzonden via de aangepaste provider en geeft het pad op van de binnenkomende aanvraag.

Door de geparseerde resource-id te gebruiken kunt u de waarden **partitionKey** en **rowKey** genereren voor de gegevens, om aangepaste resources te zoeken of op te slaan.

Nadat u de methoden en klassen hebt toegevoegd, moet u de **gebruiks** methoden bijwerken voor de functie-app. Voeg de volgende code toe bovenaan het C#-bestand:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Als u op enig moment tijdens deze zelfstudie de draad kwijtraakt, kunt u het volledige codevoorbeeld vinden in de [referentie voor het C# RESTful-eindpunt van de aangepaste provider](./reference-custom-providers-csharp-endpoint.md). Nadat u de functie-app hebt voltooid, slaat u de URL van de functie-app op. Deze kan worden gebruikt om de functie-app in latere zelfstudies te activeren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een RESTful-eindpunt ontworpen om te werken met een eindpunt voor een Azure Custom-provider. Als u wilt leren hoe u een aangepaste provider maakt, gaat u naar het artikel [Zelfstudie: Een aangepaste provider maken](./tutorial-custom-providers-create.md).
