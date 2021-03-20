---
title: Quickstart - Gebruik Node.js om een query uit te zoeken vanuit een Azure Cosmos DB SQL-API-account
description: Node.js gebruiken om een app te maken die verbinding maakt met Azure Cosmos DB SQL-API-account en query-gegevens.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: b4ed27e1515e898a71fb503bb0f260c608ef9f6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090202"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Node.js gebruiken om verbinding te maken en gegevens op te vragen uit Azure Cosmos DB SQL-API-account
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze quickstart maakt en beheert u een SQL API-account van Azure Cosmos DB via de Azure-portal en met een Node.js-app die is gekloond van GitHub. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

## <a name="walkthrough-video"></a>Walkthrough-video

Bekijk deze video voor een volledig overzicht van de inhoud in dit artikel.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U kunt ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Een Azure Cosmos-account maken

Voor deze quickstart kunt u de optie [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) gebruiken om een Azure Cosmos-account te maken.

1. Ga naar de pagina [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/).

1. Kies het **SQL**-API-account en selecteer **Maken**. Aanmelden met uw Microsoft-account.

1. Nadat het aanmelden is gelukt, moet uw Azure Cosmos-account gereed zijn. Selecteer **Openen in het Azure-portal** om het zojuist gemaakte account te openen.

Voor de optie "Probeer Azure Cosmos DB gratis" hebt u geen Azure-abonnement nodig en deze optie biedt u een Azure Cosmos-account voor een beperkte periode van 30 dagen. Als u de Azure Cosmos-account langer wilt gebruiken, moet u [de account maken](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) in uw Azure-abonnement.

## <a name="add-a-container"></a>Een container toevoegen

U kunt nu het hulpprogramma Data Explorer in de Azure-portal gebruiken om een database en een container te maken.

1. Selecteer **Data Explorer** > **Nieuwe container**.

   Uiterst rechts wordt het gedeelte **Container toevoegen** weergegeven. Mogelijk moet u naar rechts scrollen om het te kunnen zien.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Data Explorer in Azure Portal, het deelvenster Container toevoegen":::

2. Geef op de pagina **Container toevoegen** de instellingen voor de nieuwe container op.

   | Instelling           | Voorgestelde waarde | Beschrijving                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Database-id**   | Taken           | Voer _Taken_ in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **Doorvoer voor databases inrichten** in, zodat u de doorvoer die is ingericht voor de database, kunt delen in alle containers in de database. Met deze optie bespaart u bovendien op de kosten. |
   | **Doorvoer**    | 400             | Wijzig de doorvoer in 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.                                                                                                                                                                                                                                                    |
   | **Container-id**  | Items           | Voer _Items_ in als de naam voor de nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.                                                                                                                                                                                                                                                               |
   | **Partitiesleutel** | /category       | In het voorbeeld dat in dit artikel wordt beschreven, wordt _/category_ als de partitiesleutel gebruikt.                                                                                                                                                                                                                                                                                                           |

   Naast de voornoemde instellingen kunt u indien gewenst **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een beleid met unieke sleutels te maken als u een container maakt, zorgt u ervoor dat een of meer waarden per partitiesleutel uniek zijn. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.

   Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Node.js-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources van Azure Cosmos in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string).

Als u bekend bent met de vorige versie van SQL JavaScript SDK, heeft u misschien de termen _verzameling_ en _document_ gezien terwijl u ermee werkte. Azure Cosmos DB ondersteunt [meerdere API-modellen](introduction.md). Daarom maakt [versie 2.0+ van de JavaScript SDK gebruik](https://www.npmjs.com/package/@azure/cosmos) van de generieke termen _verzameling_, wat een collectie, een grafiek of tabel kan zijn, en _item_, om de inhoud van de container te beschrijven.

De Cosmos DB JavaScript SDK wordt "@azure/cosmos" genoemd en kan worden geïnstalleerd via NPM...

```bash
npm install @azure/cosmos
```

De volgende codefragmenten zijn allemaal afkomstig uit het bestand _app.js_.

- De `CosmosClient` wordt geïmporteerd uit het `@azure/cosmos` NPM-pakket.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Er wordt een nieuw `CosmosClient`-object geïnitialiseerd.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Selecteer de database "Taken".

  ```javascript
  const database = client.database(databaseId);
  ```

- Selecteer de container/verzameling "Items".

  ```javascript
  const container = database.container(containerId);
  ```

- Selecteer alle items in de container "Items".

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Een nieuw item maken

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Een item bijwerken

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Een item verwijderen

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> In de methoden "Bijwerken" en "Verwijderen" moet het item worden geselecteerd in de database door het aanroepen van `container.item()`. De twee doorgegeven parameters zijn de id van het item en de partitiesleutel van het item. In dit geval is de partitiesleutel de waarde van het veld "Categorie".

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar de Azure-portal om de details van de verbindingsreeks van uw Azure Cosmos-account op te halen. Kopieer de verbindingsreeks in de app zodat deze verbinding kan maken met uw database.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar uw Azure Cosmos DB-account en klik in de linkernavigatie op **Sleutels**. Selecteer vervolgens **Sleutels voor lezen/schrijven**. Gebruik de knoppen voor kopiëren aan de rechterkant van het scherm om de URI en Primaire sleutel in het _app.js_-bestand in de volgende stap te kopiëren.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Een toegangssleutel weergeven en kopiëren in Azure Portal, blade Sleutels":::

2. In Open het bestand _config.js_.

3. Kopieer uw URI-waarde vanaf de portal (met de kopieerknop) en geef deze als waarde aan de eindpuntsleutel in _config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Kopieer vervolgens de waarde voor PRIMAIRE SLEUTEL vanuit de portal en geef deze op als waarde voor `config.key` in _config.js_. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>De app uitvoeren

1. Voer `npm install` uit op een terminal om het "@azure/cosmos" NPM-pakket te installeren

2. Voer `node app.js` uit op een terminal om uw knooppunttoepassing te starten.

3. De twee items die u eerder in deze quickstart hebt gemaakt, worden weergegeven. Er wordt een nieuw item gemaakt. De vlag "isComplete" voor dat item is bijgewerkt naar "true" en vervolgens wordt het item verwijderd.

U kunt doorgaan met het experimenteren met deze voorbeeldtoepassing of terugkeren naar Data Explorer, om verder te werken met uw data of deze te wijzigen.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een container maakt met Data Explorer en een Node.js-app uitvoert. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [gegevens importeren in azure cosmos db](import-data.md)
