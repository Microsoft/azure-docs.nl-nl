---
title: Verbindingen beheren in Azure Functions
description: Meer informatie over hoe u prestatie problemen in Azure Functions kunt voor komen met behulp van statische clients voor verbindingen.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: ec16ce3e7f9793be2a012a029bcca31c9a7ea4cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936699"
---
# <a name="manage-connections-in-azure-functions"></a>Verbindingen beheren in Azure Functions

Functies in een functie-app delen resources. Onder deze gedeelde bronnen staan verbindingen: HTTP-verbindingen, database verbindingen en verbindingen met Services, zoals Azure Storage. Wanneer er veel functies gelijktijdig worden uitgevoerd, is het mogelijk dat er geen beschik bare verbindingen meer beschikbaar zijn. In dit artikel wordt uitgelegd hoe u uw functies codeert om te voor komen dat u meer verbindingen gebruikt dan ze nodig hebben.

## <a name="connection-limit"></a>Verbindingslimiet

Het aantal beschik bare verbindingen is deels beperkt omdat een functie-app in een [sandbox-omgeving](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)wordt uitgevoerd. Een van de beperkingen die de sandbox voor uw code oplegt, is een limiet voor het aantal uitgaande verbindingen dat momenteel 600 actief is (1.200 totaal) verbindingen per exemplaar. Wanneer u deze limiet bereikt, schrijft de functions-runtime het volgende bericht naar de logboeken: `Host thresholds exceeded: Connections` . Zie de [functies service limieten](functions-scale.md#service-limits)voor meer informatie.

Deze limiet is per instantie. Wanneer de [schaal controller functie-app-exemplaren toevoegt](event-driven-scaling.md) om meer aanvragen te verwerken, heeft elk exemplaar een onafhankelijke verbindings limiet. Dit betekent dat er geen algemene verbindings limiet is en dat u veel meer dan 600 actieve verbindingen voor alle actieve instanties kunt hebben.

Als u problemen wilt oplossen, moet u ervoor zorgen dat u Application Insights hebt ingeschakeld voor uw functie-app. Met Application Insights kunt u metrische gegevens weer geven voor uw functie-apps, zoals uitvoeringen. Zie [telemetrie in Application Insights weer geven](analyze-telemetry-data.md#view-telemetry-in-application-insights)voor meer informatie.  

## <a name="static-clients"></a>Statische clients

Om te voor komen dat u meer verbindingen dan nodig hebt, moet u client exemplaren opnieuw gebruiken in plaats van nieuwe te maken met elke functie aanroep. We raden u aan om client verbindingen opnieuw te gebruiken voor elke taal waarin u uw functie kunt schrijven. Zo kunnen bijvoorbeeld .NET-clients zoals de [httpclient maakt](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true)-, [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)-en Azure Storage-clients verbindingen beheren als u één statische client gebruikt.

Hieronder vindt u enkele richt lijnen die u moet volgen wanneer u een service-specifieke client in een Azure Functions-toepassing gebruikt:

- Maak *geen* nieuwe client bij elke functie aanroep.
- *Maak een* enkele statische client die door elke functie aanroep kan worden gebruikt.
- *Overweeg* het maken van een enkele statische client in een gedeelde helperklasse als verschillende functies gebruikmaken van dezelfde service.

## <a name="client-code-examples"></a>Voor beelden van client code

In deze sectie worden de aanbevolen procedures voor het maken en gebruiken van-clients uit uw functie code gedemonstreerd.

### <a name="httpclient-example-c"></a>Httpclient maakt-voor beeld (C#)

Hier volgt een voor beeld van een C#-functie code waarmee een statisch [httpclient maakt](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) -exemplaar wordt gemaakt:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Een veelgestelde vraag over [httpclient maakt](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) in .net is ' moet ik mijn client verwijderen? ' Over het algemeen kunt u objecten verwijderen die worden geïmplementeerd `IDisposable` Wanneer u klaar bent met het gebruik ervan. Maar u kunt een statische client niet verwijderen omdat u deze niet hebt gebruikt wanneer de functie eindigt. U wilt dat de statische client Live voor de duur van uw toepassing.

### <a name="http-agent-examples-javascript"></a>Voor beelden van HTTP-agents (Java script)

Omdat het betere opties voor verbindings beheer biedt, moet u de systeem eigen [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) klasse gebruiken in plaats van niet-systeem eigen methoden, zoals de `node-fetch` module. Verbindings parameters worden geconfigureerd via opties in de- `http.agent` klasse. Zie [nieuwe agent ( \[ opties \] )](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)voor gedetailleerde opties die beschikbaar zijn in de http-agent.

Voor de globale `http.globalAgent` klasse die door wordt gebruikt, `http.request()` zijn al deze waarden ingesteld op de respectievelijke standaarden. De aanbevolen manier om verbindings limieten in functies te configureren is door een maximum aantal globaal in te stellen. In het volgende voor beeld wordt het maximum aantal sockets voor de functie-app ingesteld:

```js
http.globalAgent.maxSockets = 200;
```

 In het volgende voor beeld wordt een nieuwe HTTP-aanvraag met een aangepaste HTTP-agent alleen voor die aanvraag gemaakt:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Voor beeld van DocumentClient-code (C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) maakt verbinding met een Azure Cosmos DB-exemplaar. De Azure Cosmos DB-documentatie raadt u [aan om een singleton Azure Cosmos DB-client te gebruiken voor de levens duur van uw toepassing](../cosmos-db/performance-tips.md#sdk-usage). In het volgende voor beeld ziet u een patroon om dit te doen in een functie:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
Als u werkt met functions v3. x, moet u een verwijzing naar Microsoft.Azure.DocumentDB. core hebben. Voeg een verwijzing toe in de code:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Maak ook een bestand met de naam ' function. proj ' voor de trigger en voeg de onderstaande inhoud toe:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a>Voor beeld van CosmosClient-code (Java script)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) maakt verbinding met een Azure Cosmos DB-exemplaar. De Azure Cosmos DB-documentatie raadt u [aan om een singleton Azure Cosmos DB-client te gebruiken voor de levens duur van uw toepassing](../cosmos-db/performance-tips.md#sdk-usage). In het volgende voor beeld ziet u een patroon om dit te doen in een functie:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient-verbindingen

Uw functie code kan gebruikmaken van de .NET Framework Data Provider voor SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) om verbindingen te maken met een relationele SQL-data base. Dit is ook de onderliggende provider voor gegevens raamwerken die afhankelijk zijn van ADO.NET, zoals [Entity Framework](/ef/ef6/). In tegens telling tot [httpclient maakt](/dotnet/api/system.net.http.httpclient) -en [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) -verbindingen implementeert ADO.net standaard verbindings groepen. Maar omdat u nog steeds verbinding kunt maken, moet u de verbindingen met de data base optimaliseren. Zie [SQL Server Connection Pooling (ADO.net) (Engelstalig)](/dotnet/framework/data/adonet/sql-server-connection-pooling)voor meer informatie.

> [!TIP]
> Sommige gegevens raamwerken, zoals Entity Framework, krijgen meestal verbindings reeksen uit de sectie **Connections Tring** van een configuratie bestand. In dit geval moet u SQL database verbindings reeksen expliciet toevoegen aan de **verbindingsteken reeks** verzameling van de instellingen van de functie-app en in de [local.settings.jsvoor het bestand](functions-run-local.md#local-settings-file) in uw lokale project. Als u een instantie van [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) in uw functie code maakt, moet u de Connection String waarde in **Toepassings instellingen** opslaan met uw andere verbindingen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de reden voor het aanbevelen van statische clients, [ongeoorloofde instantiëring Anti patroon](/azure/architecture/antipatterns/improper-instantiation/).

Zie [de prestaties en betrouw baarheid van Azure functions optimaliseren](functions-best-practices.md)voor meer Azure functions tips voor prestaties.
