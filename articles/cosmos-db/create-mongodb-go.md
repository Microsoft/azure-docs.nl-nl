---
title: Een Go-toepassing koppelen aan de API van Azure Cosmos DB voor MongoDB
description: In deze quickstart wordt beschreven hoe u een bestaande Go-toepassing kunt koppelen aan de API van Azure Cosmos DB voor MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 3f6ff0f81c1979297ed0144eca336cb252c2e787
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574357"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Een Go-toepassing koppelen aan de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden. In deze quickstart gaat u een Azure Cosmos DB-account maken en beheren met behulp van Azure Cloud Shell, een bestaande voorbeeldtoepassing klonen vanuit GitHub en deze configureren voor gebruik met Azure Cosmos DB. 

De voorbeeldtoepassing is een op opdrachtregels gebaseerd `todo`-beheerprogramma dat is geschreven in Go. De API van Azure Cosmos DB voor MongoDB is [compatibel met het wire-protocol van MongoDB](./mongodb-introduction.md#wire-protocol-compatibility), waardoor elk MongoDB-clientstuurprogramma er verbinding mee kan maken. Deze toepassing maakt op transparante wijze gebruik van het [Go-stuurprogramma voor MongoDB](https://github.com/mongodb/mongo-go-driver) om de gegevens in een Azure Cosmos DB-database op te slaan.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U kunt ook de [Azure Cosmos DB-emulator](https://aka.ms/cosmosdb-emulator) gebruiken met de verbindingsreeks `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Go](https://golang.org/) moet zijn geïnstalleerd op uw computer en u moet praktische kennis van Go hebben.
- [Git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen.

1. Open een opdrachtprompt, maak een nieuwe map met de naam `git-samples` en sluit vervolgens de opdrachtprompt.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de toepassing werkt, kunt u de volgende codefragmenten bekijken. Anders slaat u dit over en gaat u naar [De toepassing uitvoeren](#run-the-application). De indeling van de toepassing is als volgt:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

De volgende codefragmenten zijn allemaal afkomstig uit het bestand `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>De Go-app verbinden met Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) bevat de verbindingsreeks voor Azure Cosmos DB, die wordt doorgegeven via een omgevingsvariabele (details staan in de komende sectie). De verbinding wordt geïnitialiseerd met behulp van [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient), waaraan het `clientOptions`-exemplaar is doorgegeven. [De `Ping`-functie](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) wordt aangeroepen om de connectiviteit te bevestigen (het is een fail-fast-strategie)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Het gebruik van de [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect)-configuratie is belangrijk. Zonder de configuratie krijgt u de volgende verbindingsfout: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Een `todo`-item maken

Als u een `todo` wilt maken, moet u een koppeling naar een [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) maken en de [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne)-functie aanroepen. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Voer een `Todo`-struct door die de beschrijving en de status (die in eerste instantie is ingesteld op `pending`) bevat

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>`todo`-items weergeven

U kunt TODO's weergeven op basis van criteria. Een [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) wordt gemaakt om de filtercriteria te omvatten

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) wordt gebruikt om te zoeken naar documenten op basis van het filter, en het resultaat wordt geconverteerd naar een segment van `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Ten slotte worden de gegevens in een tabel weergegeven

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Een `todo`-item bijwerken

Een `todo` kan worden bijgewerkt op basis van de bijbehorende `_id`. Er wordt een [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)-filter gemaakt op basis van de `_id` en er wordt nog een gemaakt voor de bijgewerkte informatie. Dit is in dit geval een nieuwe status (`completed` of `pending`). Ten slotte wordt de [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne)-functie aangeroepen met het filter en het bijgewerkte document

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Een `todo` verwijderen

Een `todo` wordt verwijderd op basis van de bijbehorende `_id` en wordt ingekapseld in de vorm van een [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)-exemplaar. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) wordt aangeroepen om het document te verwijderen.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>De toepassing bouwen

Ga naar de map waarnaar u de toepassing hebt gekloond en bouw deze (met behulp van `go build`).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Om te controleren of de toepassing correct is gemaakt.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Azure Cosmos DB instellen

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI installeren]. 

Als u een geïnstalleerde Azure CLI gebruikt, meldt u zich aan bij uw Azure-abonnement met de opdracht [az login](/cli/azure/reference-index#az-login) en volgt u de instructies op het scherm. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>De Azure Cosmos DB-module toevoegen

Als u van een geïnstalleerde Azure CLI gebruikmaakt, controleert u of het onderdeel `cosmosdb` al is geïnstalleerd door de opdracht `az` uit te voeren. Als `cosmosdb` in de lijst met basisopdrachten staat, gaat u verder met de volgende opdracht. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

Als `cosmosdb` niet in de lijst met basisopdrachten staat, installeert u [Azure CLI](/cli/azure/install-azure-cli) opnieuw.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources, zoals web-apps, databases en opslagaccounts, worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio Europa - west. Kies een unieke naam voor de resourcegroep.

Als u van Azure Cloud Shell gebruikmaakt, selecteert u **Uitproberen**, volgt u de aanwijzingen op het scherm om u aan te melden en kopieert u de opdracht naar de opdrachtprompt.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Maak een Cosmos-account met de opdracht [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

Vervang in de volgende opdracht waar u de plaatsaanduiding `<cosmosdb-name>` ziet staan, de accountnaam met uw unieke Cosmos-accountnaam. Deze unieke naam wordt gebruikt als onderdeel van uw Cosmos DB-eindpunt (`https://<cosmosdb-name>.documents.azure.com/`). De naam moet daarom uniek zijn binnen alle Cosmos-accounts in Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

De parameter `--kind MongoDB` maakt MongoDB-clientverbindingen mogelijk.

Wanneer de Azure Cosmos DB-account wordt gemaakt toont de Azure CLI informatie die lijkt op het volgende voorbeeld. 

> [!NOTE]
> In dit voorbeeld wordt JSON gebruikt als de Azure CLI-uitvoerindeling. Dit is standaardindeling. Zie [Output formats for Azure CLI commands](/cli/azure/format-output-azure-cli) (Uitvoerindelingen voor Azure CLI-opdrachten) als u een andere uitvoerindeling wilt gebruiken.

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>De databasesleutel ophalen

U hebt de databasesleutel nodig om verbinding te kunnen maken met een Cosmos-database. Gebruik de opdracht [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) om de primaire sleutel op te halen.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

De Azure CLI voert informatie uit die lijkt op het volgende voorbeeld. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>De toepassing configureren 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exporteer de verbindingsreeks, de MongoDB-database en de verzamelingsnamen als omgevingsvariabelen. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> De optie `ssl=true` is belangrijk vanwege de vereisten van Cosmos DB. Zie [Connection string requirements](connect-mongodb-account.md#connection-string-requirements) (Vereisten voor verbindingsreeksen) voor meer informatie.
>

Voor de omgevingsvariabele `MONGODB_CONNECTION_STRING` vervangt u de tijdelijke aanduidingen door `<COSMOSDB_ACCOUNT_NAME>` en `<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: de naam van het Cosmos Azure DB-account dat u hebt gemaakt
2. `<COSMOSDB_PASSWORD>`: de databasesleutel die u in de vorige stap hebt geëxtraheerd

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

U kunt uw voorkeurswaarden voor `MONGODB_DATABASE` en `MONGODB_COLLECTION` kiezen of deze laten staan.

## <a name="run-the-application"></a>De toepassing uitvoeren

Een `todo` maken

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Als dit lukt, ziet u een uitvoer met de MongoDB-`_id` van het nieuwe document:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Maak nog een `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Geef alle `todo`'s weer

```bash
./todo --list all
```

U zou de items die u zojuist hebt toegevoegd in een tabel moeten zien

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Als u de status van een `todo` wilt bijwerken (bijvoorbeeld wijzigen in de status `completed`), gebruikt u de `todo`-id

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Geef alleen de voltooide `todo`'s weer

```bash
./todo --list completed
```

U zou nu het item dat u zojuist hebt bijgewerkt moeten zien

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Gegevens bekijken in Data Explorer

Gegevens die zijn opgeslagen in een Azure Cosmos DB-database kunnen via de Azure-portal worden bekeken en er kunnen vanuit de portal query's op worden uitgevoerd.

Meld u aan bij de [Azure Portal](https://portal.azure.com) in uw webbrowser om de gebruikersgegevens die u in de vorige stap hebt gemaakt, te bekijken, query’s erop uit te voeren of andere taken ermee uit te voeren.

Voer **Azure Cosmos DB** in het bovenste zoekvak in. Wanneer uw Cosmos-accountblade wordt geopend, selecteert u uw Cosmos-account. Selecteer in het linker navigatiegedeelte **Data Explorer**. Vouw uw verzameling uit in het venster Verzamelingen. Dan kunt u de documenten in de verzameling zien, query’s op de gegevens uitvoeren en zelfs opgeslagen procedures, triggers en UDF’s maken en uitvoeren. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Data Explorer waarin het zojuist gemaakte document wordt weergegeven":::


Verwijder een `todo` met de bijbehorende id

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Geef de `todo`'s weer om te bevestigen

```bash
./todo --list all
```

De `todo` die u zojuist hebt verwijderd, mag niet aanwezig zijn

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB MongoDB API-account maakt met behulp van Azure Cloud Shell en hoe u een app met Go-opdrachtregels kunt maken en uitvoeren om `todo`'s te beheren. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)