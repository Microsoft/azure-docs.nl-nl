---
title: Meer informatie over het beveiligen van toegang tot gegevens in Azure Cosmos DB
description: Meer informatie over toegangs beheer concepten in Azure Cosmos DB, waaronder primaire sleutels, alleen-lezen sleutels, gebruikers en machtigingen.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 6dd95fc8fd0ab0099ac7404d4ca4e4b1851f650f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359602"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Beveiligde toegang tot gegevens in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dit artikel bevat een overzicht van het beveiligen van de toegang tot gegevens die zijn opgeslagen in [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB gebruikt twee typen sleutels om gebruikers te verifiëren en toegang te bieden tot de gegevens en bronnen van de gebruiker. 

|Type sleutel|Resources|
|---|---|
|[Primaire sleutels](#primary-keys) |Gebruikt voor administratieve resources: database accounts, data bases, gebruikers en machtigingen|
|[Bron tokens](#resource-tokens)|Gebruikt voor toepassings resources: containers, documenten, bijlagen, opgeslagen procedures, triggers en Udf's|

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Primaire sleutels

Primaire sleutels bieden toegang tot alle beheer resources voor het database account. Elk account bestaat uit twee primaire sleutels: een primaire sleutel en secundaire sleutel. Het doel van dubbele sleutels is dat u de sleutel opnieuw kunt genereren of sleutels moet voorzien van een continue toegang tot uw account en gegevens. Zie het artikel over de [Data Base-beveiliging](database-security.md#primary-keys) voor meer informatie over primaire sleutels.

### <a name="key-rotation"></a>Sleutel rotatie<a id="key-rotation"></a>

Het proces voor het draaien van uw primaire sleutel is eenvoudig. 

1. Navigeer naar het Azure Portal om uw secundaire sleutel op te halen.
2. Vervang uw primaire sleutel door de secundaire sleutel in uw toepassing. Zorg ervoor dat alle Cosmos DB-clients in alle implementaties onmiddellijk opnieuw worden opgestart en dat deze de bijgewerkte sleutel gaan gebruiken.
3. De primaire sleutel in de Azure Portal draaien.
4. Valideer of de nieuwe primaire sleutel geschikt is voor alle resources. Het proces voor het draaien van sleutels kan van minder dan een minuut tot uren duren, afhankelijk van de grootte van het Cosmos DB-account.
5. Vervang de secundaire sleutel door de nieuwe primaire sleutel.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Rotatie van primaire sleutel in de Azure Portal-demonstring NoSQL data base Security" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Code voorbeeld voor het gebruik van een primaire sleutel

In het volgende code voorbeeld ziet u hoe u een Cosmos DB account-eind punt en een primaire sleutel kunt gebruiken om een DocumentClient te instantiëren en een Data Base te maken:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

In het volgende code voorbeeld ziet u hoe u het Azure Cosmos DB account-eind punt en de primaire sleutel kunt gebruiken om een object te instantiëren `CosmosClient` :

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>Bron tokens <a id="resource-tokens"></a>

Bron tokens bieden toegang tot de toepassings resources binnen een Data Base. Bron tokens:

- Toegang bieden tot specifieke containers, partitie sleutels, documenten, bijlagen, opgeslagen procedures, triggers en Udf's.
- Worden gemaakt wanneer aan een [gebruiker](#users) [machtigingen](#permissions) worden verleend voor een specifieke resource.
- Worden opnieuw gemaakt wanneer een machtigings resource wordt toegepast op de aanroep POST, GET of PUT.
- Gebruik een hash-bron token dat specifiek is gemaakt voor de gebruiker, de resource en de machtiging.
- Zijn tijd gebonden aan een aanpas bare geldigheids periode. De standaard geldige tijds duur is een uur. De levens duur van het token kan echter expliciet worden opgegeven, Maxi maal vijf uur.
- Geef een veilig alternatief op voor het geven van de primaire sleutel.
- Hiermee kunnen clients resources in het Cosmos DB-account lezen, schrijven en verwijderen op basis van de machtigingen die ze hebben verleend.

U kunt een bron token gebruiken (door Cosmos DB gebruikers en machtigingen te maken) wanneer u toegang wilt verlenen tot resources in uw Cosmos DB account aan een client die niet kan worden vertrouwd met de primaire sleutel.  

Cosmos DB bron tokens bieden een veilig alternatief waarmee clients resources in uw Cosmos DB account kunnen lezen, schrijven en verwijderen op basis van de machtigingen die u hebt verleend, en zonder een primaire of alleen-lezen sleutel.

Hier volgt een typisch ontwerp patroon waarbij bron tokens kunnen worden aangevraagd, gegenereerd en geleverd aan clients:

1. Een mid-tier-service is ingesteld voor het gebruik van een mobiele toepassing om gebruikers Foto's te delen.
2. De mid-tier-service beschikt over de primaire sleutel van het Cosmos DB-account.
3. De foto-app is geïnstalleerd op mobiele apparaten van eind gebruikers.
4. Bij het aanmelden brengt de foto-app de identiteit van de gebruiker met de mid-tier-service tot stand. Dit mechanisme voor identiteits inrichting is uitsluitend voor de toepassing.
5. Zodra de identiteit tot stand is gebracht, vraagt de mid-tier-service machtigingen aan op basis van de identiteit.
6. De mid-tier-service stuurt een bron token terug naar de telefoon-app.
7. De telefoon-app kan het bron token blijven gebruiken voor rechtstreekse toegang tot Cosmos DB resources met de machtigingen die zijn gedefinieerd door het bron token en voor het interval dat is toegestaan door het bron token.
8. Wanneer het bron token verloopt, ontvangen volgende aanvragen een ongeautoriseerde uitzonde ring van 401.  Op dit moment brengt de telefoon-app de identiteit opnieuw tot stand en wordt er een nieuw bron token aangevraagd.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Werk stroom voor Azure Cosmos DB resource tokens" border="false":::

Het genereren en beheren van bron tokens worden verwerkt door de systeem eigen Cosmos DB-client bibliotheken. Als u echter REST gebruikt, moet u de aanvraag-en verificatie headers maken. Zie [Access Control op Cosmos DB resources](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) of de bron code voor onze [.net SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) of [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts)voor meer informatie over het maken van verificatie headers voor rest.

Zie de [resource token Broker-app](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)voor een voor beeld van een middle-tier service die wordt gebruikt om resource tokens te genereren of Broker.

## <a name="users"></a>Gebruikers<a id="users"></a>

Azure Cosmos DB gebruikers zijn gekoppeld aan een Cosmos-data base.  Elke Data Base kan nul of meer Cosmos DB gebruikers bevatten. In het volgende code voorbeeld ziet u hoe u een Cosmos DB gebruiker maakt met behulp van de [Azure Cosmos db .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Elke Cosmos DB gebruiker heeft een ReadAsync ()-methode die kan worden gebruikt voor het ophalen van de lijst met [machtigingen](#permissions) die aan de gebruiker zijn gekoppeld.

## <a name="permissions"></a>Machtigingen<a id="permissions"></a>

Een machtigings resource is gekoppeld aan een gebruiker en toegewezen aan de container en het niveau van de partitie sleutel. Elke gebruiker kan nul of meer machtigingen bevatten. Een machtigings bron biedt toegang tot een beveiligings token dat de gebruiker nodig heeft om toegang te krijgen tot een specifieke container of gegevens in een specifieke partitie sleutel. Er zijn twee toegangs niveaus beschikbaar die kunnen worden gegeven door een machtigings Bron:

- Alle: de gebruiker heeft volledige machtigingen voor de resource.
- Lezen: de gebruiker kan alleen de inhoud van de resource lezen, maar kan geen schrijf-, bijwerk-of verwijderings bewerkingen uitvoeren op de resource.

> [!NOTE]
> Voor het uitvoeren van opgeslagen procedures moet de gebruiker beschikken over de machtiging all voor de container waarin de opgeslagen procedure wordt uitgevoerd.

Als u de [Diagnostische logboeken op gegevens vlak-aanvragen](cosmosdb-monitor-resource-logs.md)inschakelt, worden de volgende twee eigenschappen die overeenkomen met de machtiging, vastgelegd:

* **resourceTokenPermissionId** : met deze eigenschap geeft u de machtigings-id op die u hebt opgegeven. 

* **resourceTokenPermissionMode** : deze eigenschap geeft de machtigings modus aan die u hebt ingesteld bij het maken van het bron token. De machtigings modus kan waarden bevatten zoals "all" of "Read".

### <a name="code-sample-to-create-permission"></a>Code voorbeeld voor het maken van machtigingen

In het volgende code voorbeeld ziet u hoe u een machtigings bron maakt, het bron token van de machtigings resource leest en de machtigingen koppelt aan de [gebruiker](#users) die hierboven is gemaakt.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Code voorbeeld voor het lezen van de machtiging voor gebruiker

In het volgende code fragment ziet u hoe u de machtigingen kunt ophalen die zijn gekoppeld aan de gebruiker die hierboven is gemaakt en een nieuwe CosmosClient namens de gebruiker maakt, binnen het bereik van één partitie sleutel.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Gebruikers toevoegen en rollen toewijzen

Als u Azure Cosmos DB account lezer-toegang wilt toevoegen aan uw gebruikers account, moet u de eigenaar van een abonnement hebben om de volgende stappen uit te voeren in de Azure Portal.

1. Open de Azure Portal en selecteer uw Azure Cosmos DB-account.
2. Klik op het tabblad **toegangs beheer (IAM)** en klik vervolgens op  **+ roltoewijzing toevoegen**.
3. In het deel venster **toewijzing van rol toevoegen** selecteert u **Cosmos DB rol van account lezer** in het vak **rol** .
4. Selecteer in het **vak toegang toewijzen aan** de optie **Azure AD-gebruiker,-groep of-toepassing**.
5. Selecteer de gebruiker, groep of toepassing in de map waaraan u toegang wilt verlenen.  U kunt in de map zoeken op de weergave naam, het e-mail adres of de object-id's.
    De geselecteerde gebruiker, groep of toepassing wordt weer gegeven in de lijst geselecteerde leden.
6. Klik op **Opslaan**.

De entiteit kan nu Azure Cosmos DB resources lezen.

## <a name="delete-or-export-user-data"></a>Gebruikers gegevens verwijderen of exporteren

Als database service kunt u met Azure Cosmos DB zoeken, selecteren, wijzigen en verwijderen van gegevens die zich in uw data base of containers bevinden. Het is echter uw verantwoordelijkheid om de opgegeven Api's te gebruiken en om zo nodig de logica te definiëren die vereist is voor het zoeken en wissen van persoonlijke gegevens. Elke API voor meerdere modellen (SQL, MongoDB, Gremlin, Cassandra, tabel) biedt verschillende taal-Sdk's die methoden bevatten voor het zoeken en verwijderen van gegevens op basis van aangepaste predikaten. U kunt ook de [TTL-functie (time to Live)](time-to-live.md) zo instellen dat gegevens na een bepaalde periode automatisch worden verwijderd, zonder dat er extra kosten in rekening worden gebracht.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [Cosmos DB Data Base Security](database-security.md)(Engelstalig) voor meer informatie over de beveiliging van Cosmos-data bases.
- Zie [Access Control op Azure Cosmos DB resources](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)voor meer informatie over het maken van Azure Cosmos DB autorisatie tokens.
- Voor beelden van gebruikers beheer met gebruikers en machtigingen, voor [beelden van .NET SDK v3-gebruikers beheer](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
