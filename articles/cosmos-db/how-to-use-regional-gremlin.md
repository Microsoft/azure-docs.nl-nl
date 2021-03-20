---
title: Regionale eind punten voor de Azure Cosmos DB Graph-data base
description: Meer informatie over hoe u verbinding maakt met het dichtstbijzijnde Graph-database eindpunt voor uw toepassing
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/09/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3e30252d8f5e80538139f8100f1070385c1b6016
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93361784"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionale eind punten voor Azure Cosmos DB Graph-account
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB Graph-data base is [wereld wijd gedistribueerd](distribute-data-globally.md) zodat toepassingen meerdere Lees eindpunten kunnen gebruiken. Toepassingen waarvoor schrijf toegang op meerdere locaties nodig is, moeten schrijf mogelijkheden voor [meerdere regio's](how-to-multi-master.md) inschakelen.

Redenen voor het kiezen van meer dan één regio:
1. **Horizontale Lees schaal baarheid** : als de toepassings belasting wordt verhoogd, kan het verstandig zijn om Lees verkeer te routeren naar verschillende Azure-regio's.
2. **Lagere latentie** : u kunt de netwerk latentie overhead van elk door sturen beperken door het lezen en schrijven van verkeer naar de dichtstbijzijnde Azure-regio.

De vereiste voor **gegevens locatie** wordt bereikt door Azure Resource Manager beleid in te stellen op Cosmos DB-account. De klant kan regio's beperken waarin Cosmos DB gegevens repliceert.

## <a name="traffic-routing"></a>Verkeers routering

Cosmos DB Graph-data base-engine wordt uitgevoerd in meerdere regio's, die elk meerdere clusters bevatten. Elk cluster heeft honderden computers. Cosmos DB Graph-account DNS CNAME *AccountName.Gremlin.Cosmos.Azure.com* wordt omgezet in DNS a-record van een cluster. Eén IP-adres van een Load Balancer verbergt de interne cluster topologie.

Er wordt een regionale DNS CNAME-record gemaakt voor elke regio van Cosmos DB Graph-account. De indeling van het regionale eind punt is *AccountName-Region.Gremlin.Cosmos.Azure.com*. Regio segment van regionaal eind punt wordt verkregen door alle spaties uit de [Azure-regio](https://azure.microsoft.com/global-infrastructure/regions) naam te verwijderen. `"East US 2"`De regio voor `"contoso"` het globale database account zou bijvoorbeeld een DNS CNAME- *Contoso-eastus2.Gremlin.Cosmos.Azure.com* hebben

TinkerPop Gremlin-client is ontworpen voor gebruik met één server. De toepassing kan globale Beschrijf bare DNS CNAME gebruiken voor lees-en schrijf verkeer. Regio-bewuste toepassingen moeten regionaal eind punt gebruiken voor het lezen van verkeer. Gebruik regionaal eind punt voor het schrijven van verkeer alleen als specifieke regio is geconfigureerd voor het accepteren van schrijf bewerkingen. 

> [!NOTE]
> Cosmos DB Graph-engine kan een schrijf bewerking in een lees regio accepteren door verkeer van de proxy naar de regio schrijven. Het is niet raadzaam om schrijf bewerkingen naar de regio alleen-lezen te verzenden als deze de doorvoer latentie verhogen en onderhevig is aan beperkingen in de toekomst.

Het globale database account CNAME wijst altijd naar een geldige schrijf regio. Tijdens de failover van de schrijf regio aan de server zijde Cosmos DB het globale database account van de CNAME bijgewerkt om naar de nieuwe regio te verwijzen. Als de toepassing het omleiden van verkeer na een failover niet kan verwerken, moet deze gebruikmaken van het globale database account DNS CNAME.

> [!NOTE]
> Cosmos DB stuurt geen verkeer op basis van de geografische nabijheid van de oproepende functie. Het is aan elke toepassing de juiste regio te selecteren op basis van unieke toepassings behoeften.

## <a name="portal-endpoint-discovery"></a>Portal-eindpunt detectie

De eenvoudigste manier om de lijst met regio's te verkrijgen voor Azure Cosmos DB Graph-account is overzicht Blade in Azure Portal. Het werkt voor toepassingen die regio's niet vaak wijzigen of een manier hebben om de lijst bij te werken via toepassings configuratie.

:::image type="content" source="./media/how-to-use-regional-gremlin/get-end-point-portal.png " alt-text="Regio's van Cosmos DB Graph-account ophalen uit de portal":::

Hieronder ziet u een voor beeld van algemene principes van toegang tot het regionale Gremlin-eind punt. De toepassing moet het aantal regio's overwegen om het verkeer naar en het aantal overeenkomende Gremlin-clients te kunnen worden gezonden.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>SDK-eindpunt detectie

De toepassing kan [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) gebruiken om Lees-en schrijf locaties voor een grafiek account te detecteren. Deze locaties kunnen op elk gewenst moment worden gewijzigd via hand matig opnieuw configureren aan de server zijde of automatische failover.

TinkerPop Gremlin SDK heeft geen API voor het detecteren van Cosmos DB Graph-database account regio's. Toepassingen waarvoor runtime-eind punten moeten worden gedetecteerd, moeten twee afzonderlijke Sdk's in de proces ruimte hosten.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Volgende stappen
* [Beheer van database accounts](how-to-manage-database-account.md) in azure Cosmos db
* [Hoge Beschik baarheid](high-availability.md) in azure Cosmos db
* [Wereld wijde distributie met Azure Cosmos DB-onder de motorkap](global-dist-under-the-hood.md)
* Voor [beelden van Azure cli](cli-samples.md) voor Azure Cosmos db
