---
title: Een verbinding met een Cosmos DB account instellen met behulp van een beheerde identiteit
titleSuffix: Azure Cognitive Search
description: Meer informatie over het instellen van een verbinding met een Indexeer functie met een Cosmos DB account met behulp van een beheerde identiteit
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422090"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Een Indexeer functie verbinding met een Cosmos DB-Data Base instellen met behulp van een beheerde identiteit

Op deze pagina wordt beschreven hoe u een Indexeer functie verbinding kunt instellen met een Azure Cosmos DB-Data Base met behulp van een beheerde identiteit in plaats van referenties op te geven in het gegevens bron object connection string.

Voor meer informatie over deze functie kunt u het beste inzicht krijgen in wat een indexer is en hoe u een Indexeer functie instelt voor uw gegevens bron. Meer informatie vindt u op de volgende koppelingen:

* [Overzicht van de indexeerfunctie](search-indexer-overview.md)
* [Indexeerfunctie voor Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Een verbinding instellen met behulp van een beheerde identiteit

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-door het systeem toegewezen beheerde identiteit inschakelen

Wanneer een door het systeem toegewezen beheerde identiteit is ingeschakeld, maakt Azure een identiteit voor uw zoek service die kan worden gebruikt om te verifiëren bij andere Azure-Services binnen dezelfde Tenant en hetzelfde abonnement. U kunt deze identiteit vervolgens gebruiken in azure RBAC-toewijzingen (op rollen gebaseerd toegangs beheer) waarmee toegang tot gegevens tijdens het indexeren wordt toegestaan.

![Door het systeem toegewezen beheerde identiteit inschakelen](./media/search-managed-identities/turn-on-system-assigned-identity.png "Door het systeem toegewezen beheerde identiteit inschakelen")

Nadat u **Opslaan** hebt geselecteerd, ziet u een object-id die is toegewezen aan uw zoek service.

![Object-id](./media/search-managed-identities/system-assigned-identity-object-id.png "Object-id")
 
### <a name="2---add-a-role-assignment"></a>2-een roltoewijzing toevoegen

In deze stap geeft u uw Azure Cognitive Search-service toestemming om gegevens uit uw Cosmos DB-Data Base te lezen.

1. Navigeer in het Azure Portal naar het Cosmos DB-account dat de gegevens bevat die u wilt indexeren.
2. Selecteer **Toegangsbeheer (IAM)**
3. Selecteer **toevoegen** en vervolgens **functie toewijzing toevoegen**

    ![Roltoewijzing toevoegen](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Roltoewijzing toevoegen")

4. De rol van de **Cosmos DB-account lezer** selecteren
5. **Wijs toegang toe** als **Azure AD-gebruiker,-groep of-Service-Principal**
6. Zoek naar uw zoek service, Selecteer deze en selecteer vervolgens **Opslaan** .

    ![Toewijzing van de rol lezer en gegevens toegang toevoegen](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Toewijzing van de rol lezer en gegevens toegang toevoegen")

### <a name="3---create-the-data-source"></a>3: de gegevens bron maken

De [rest API](/rest/api/searchservice/create-data-source), Azure Portal en de [.NET-SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) ondersteunen de beheerde identiteits Connection String. Hieronder ziet u een voor beeld van hoe u een gegevens bron maakt om gegevens te indexeren van Cosmos DB met behulp van de [rest API](/rest/api/searchservice/create-data-source) en een beheerde identiteit Connection String. De indeling van de beheerde identiteits connection string is hetzelfde voor de REST API, de .NET-SDK en de Azure Portal.

Wanneer beheerde identiteiten worden gebruikt voor verificatie, bevat de **referenties** geen account sleutel.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

De hoofd tekst van de aanvraag bevat de definitie van de gegevens bron, die de volgende velden moet bevatten:

| Veld   | Beschrijving |
|---------|-------------|
| **name** | Vereist. Kies een wille keurige naam voor uw gegevens bron object. |
|**type**| Vereist. Moet zijn `cosmosdb` . |
|**aanmeldingsgegevens** | Vereist. <br/><br/>Wanneer u verbinding maakt met behulp van een beheerde identiteit, moet de indeling van de **referenties** zijn: *Data base = [database naam]; ResourceId = [resource-id-String];(soort = [API-kind];)*<br/> <br/>De ResourceId-indeling: *ResourceID =/Subscriptions/**uw abonnements-id**/resourceGroups/**de naam van de resource groep**/providers/Microsoft.DocumentDB/databaseAccounts/**uw Cosmos DB-account naam**/;*<br/><br/>Voor SQL-verzamelingen is voor de connection string geen soort vereist.<br/><br/>Voeg voor MongoDB-verzamelingen **soort = MongoDb** toe aan de Connection String. <br/><br/>Voor Gremlin-grafieken en Cassandra-tabellen meldt u zich aan voor de preview-versie van de [Indexeer functie](https://aka.ms/azure-cognitive-search/indexer-preview) om toegang te krijgen tot de preview-versie en informatie over het format teren van de referenties.<br/>|
| **verpakking** | Bevat de volgende elementen: <br/>**naam**: vereist. Geef de ID op van de database verzameling die moet worden geïndexeerd.<br/>**query**: optioneel. U kunt een query opgeven voor het afvlakken van een wille keurig JSON-document in een plat schema dat door Azure Cognitive Search kan worden geïndexeerd.<br/>Query's worden niet ondersteund voor de MongoDB-API, de Gremlin-API en de Cassandra-API. |
| **dataChangeDetectionPolicy** | Aanbevolen |
|**dataDeletionDetectionPolicy** | Optioneel |

### <a name="4---create-the-index"></a>4-de index maken

De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen.

Ga als volgt te werk om een index te maken met een doorzoekbaar `booktitle` veld:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Zie [index maken](/rest/api/searchservice/create-index) voor meer informatie over het maken van indexen

### <a name="5---create-the-indexer"></a>5: de Indexeer functie maken

Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een planning voor het automatiseren van de gegevens vernieuwing.

Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie maken.

Voor beeld van de definitie van de Indexeer functie:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Deze Indexeer functie wordt elke twee uur uitgevoerd (schema-interval is ingesteld op "PT2H"). Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is 5 minuten. Het schema is optioneel: als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt echter op elk gewenst moment een Indexeer functie op aanvraag uitvoeren.   

Bekijk [Indexeer functie maken](/rest/api/searchservice/create-indexer)voor meer informatie over het maken van Indexeer functie-API.

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

## <a name="troubleshooting"></a>Problemen oplossen

Als u vindt dat u geen gegevens van Cosmos DB kunt indexeren, moet u rekening houden met het volgende:

1. Als u onlangs uw Cosmos DB-account sleutels hebt gedraaid, moet u tot 15 minuten wachten totdat de beheerde identiteit connection string werkt.

1. Controleer of voor het Cosmos DB-account de toegang is beperkt tot het selecteren van netwerken. Als dit het geval is, raadpleegt u de toegang tot de [Indexeer functie voor inhoud die wordt beveiligd door Azure-netwerk beveiligings functies](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Volgende stappen

* [Indexeerfunctie voor Azure Cosmos DB](search-howto-index-cosmosdb.md)