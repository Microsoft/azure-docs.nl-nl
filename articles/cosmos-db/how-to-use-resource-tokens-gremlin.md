---
title: Azure Cosmos DB-bron tokens gebruiken met de Gremlin-SDK
description: Meer informatie over het maken van resource tokens en het gebruik ervan om toegang te krijgen tot de Graph-data base.
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 22c048b748806404ccfa580e660552a1744f3781
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93361690"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Azure Cosmos DB-bron tokens gebruiken met de Gremlin-SDK
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In dit artikel wordt uitgelegd hoe u [Azure Cosmos DB-bron tokens](secure-access-to-data.md) gebruikt om toegang te krijgen tot de Graph-data base via de GREMLIN-SDK.

## <a name="create-a-resource-token"></a>Een bron token maken

De Apache TinkerPop Gremlin SDK heeft geen API voor het maken van bron tokens. De term *resource token* is een Azure Cosmos DB concept. Als u resource tokens wilt maken, moet u de [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)downloaden. Als uw toepassing resource tokens moet maken en gebruiken om toegang te krijgen tot de Graph-data base, zijn er twee afzonderlijke Sdk's vereist.

De hiërarchie object model boven bron tokens wordt geïllustreerd in het volgende overzicht:

- **Azure Cosmos DB account** : de entiteit op het hoogste niveau waaraan een DNS is gekoppeld (bijvoorbeeld `contoso.gremlin.cosmos.azure.com` ).
  - **Azure Cosmos DB-Data Base**
    - **Gebruiker**
      - **Machtiging**
        - **Token** : een eigenschap van het machtigings object waarmee wordt aangegeven welke acties worden toegestaan of geweigerd.

Een bron token gebruikt de volgende indeling: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` . Deze teken reeks is ondoorzichtig voor de clients en moet worden gebruikt als, zonder aanpassing of interpretatie.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<primary key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Een resource token gebruiken
U kunt bron tokens rechtstreeks als een ' wacht woord '-eigenschap gebruiken wanneer u de klasse GremlinServer maakt.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a primary key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Dezelfde benadering werkt in alle TinkerPop Gremlin Sdk's.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limiet

Met één Gremlin-account kunt u een onbeperkt aantal tokens uitgeven. U kunt echter Maxi maal 100 tokens gelijktijdig gebruiken binnen één uur. Als een toepassing de token limiet per uur overschrijdt, wordt een verificatie aanvraag geweigerd en wordt het volgende fout bericht weer gegeven: overschrijding van de toegestane limiet voor de resource token van 100 die gelijktijdig kan worden gebruikt. Het is niet mogelijk actieve verbindingen die gebruikmaken van specifieke tokens te sluiten om sleuven voor nieuwe tokens vrij te maken. De Azure Cosmos DB Gremlin data base-engine houdt tijdens het uur direct vóór de verificatie aanvraag unieke tokens bij.

## <a name="permission"></a>Machtiging

Een veelvoorkomende fout die toepassingen tegen komen tijdens het gebruik van resource tokens is ' onvoldoende machtigingen in de autorisatie-header voor de bijbehorende aanvraag. Probeer het opnieuw met een andere autorisatie-header. Deze fout wordt geretourneerd wanneer een Gremlin-passage een rand of een hoek punt probeert te schrijven, maar het bron token alleen *Lees* machtigingen verleent. Inspecteer uw passage om te zien of het een van de volgende stappen bevat: *. addV ()*, *. addE ()*, *. drop ()* of *. eigenschap ()*.

## <a name="next-steps"></a>Volgende stappen
* [Op rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](role-based-access-control.md) in azure Cosmos db
* [Meer informatie over het beveiligen van toegang tot gegevens](secure-access-to-data.md) in azure Cosmos db
