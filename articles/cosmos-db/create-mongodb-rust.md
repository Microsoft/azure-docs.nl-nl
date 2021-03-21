---
title: Een roest-toepassing verbinden met de API van Azure Cosmos DB voor MongoDB
description: In deze Quick start ziet u hoe u een roest-toepassing kunt maken die wordt ondersteund door de API van Azure Cosmos DB voor MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 91e7bafe98b1aceaf8fe27b07029291a48a31351
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555649"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Quick Start: een roest-toepassing verbinden met de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases kunt maken en er query's op kunt uitvoeren met globale distributie en horizontale schaalmogelijkheden. Het voor beeld in dit artikel is een eenvoudige toepassing op basis van een opdracht regel die gebruikmaakt [van het roest-stuur programma voor MongoDb](https://github.com/mongodb/mongo-rust-driver). Omdat de API van Azure Cosmos DB voor MongoDB [compatibel is met het MongoDb wire-protocol](./mongodb-introduction.md#wire-protocol-compatibility), is het mogelijk dat een MongoDb-client stuur programma er verbinding mee maakt.

U leert hoe u het MongoDBe roest-stuur programma gebruikt om te communiceren met de API van Azure Cosmos DB voor MongoDB door ruwe (Create-, Read-, update-, Delete)-bewerkingen te verkennen die in de voorbeeld code zijn geïmplementeerd. Ten slotte kunt u de toepassing lokaal uitvoeren om deze in actie te zien.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U kunt ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met de verbindingsreeks `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Rust](https://www.rust-lang.org/tools/install) (versie 1.39 of hoger)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB instellen

Als u een Azure Cosmos DB account wilt instellen, volgt u de [instructies hier](create-mongodb-dotnet.md). De toepassing heeft de MongoDB-connection string nodig die u kunt ophalen met behulp van de Azure Portal. Zie [Get the MongoDB connection string to Customize](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize)voor meer informatie.

## <a name="run-the-application"></a>De toepassing uitvoeren

### <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen.

1. Open een opdrachtprompt, maak een nieuwe map met de naam `git-samples` en sluit vervolgens de opdrachtprompt.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>De toepassing bouwen

Het binaire bestand bouwen:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>De toepassing configureren 

Exporteer de connection string, MongoDB-data base en verzamelings namen als omgevings variabelen. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> De optie `ssl=true` is belangrijk vanwege de vereisten van Cosmos DB. Zie [Connection string requirements](connect-mongodb-account.md#connection-string-requirements) (Vereisten voor verbindingsreeksen) voor meer informatie.
>

Voor de omgevingsvariabele `MONGODB_URL` vervangt u de tijdelijke aanduidingen door `<COSMOSDB_ACCOUNT_NAME>` en `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: de naam van het Cosmos Azure DB-account dat u hebt gemaakt
- `<COSMOSDB_PASSWORD>`: de databasesleutel die u in de vorige stap hebt geëxtraheerd

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

U kunt uw voorkeurswaarden voor `MONGODB_DATABASE` en `MONGODB_COLLECTION` kiezen of deze laten staan.

Als u de toepassing wilt uitvoeren, gaat u naar de juiste map (waar de binaire toepassing bestaat):

```bash
cd target/release
```

Een `todo` maken

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Als dit lukt, ziet u een uitvoer met de MongoDB-`_id` van het nieuwe document:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Maak nog een `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Geef alle `todo`'s weer

```bash
./todo list all
```

U ziet de items die u zojuist hebt toegevoegd:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Als u de status van een wilt bijwerken `todo` (bijvoorbeeld wijzigen in `completed` status), gebruikt u de `todo` id als volgt:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Geef alleen de voltooide `todo`'s weer

```bash
./todo list completed
```

U zou nu het item dat u zojuist hebt bijgewerkt moeten zien

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Verwijder een `todo` met de bijbehorende id

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Geef de `todo`'s weer om te bevestigen

```bash
./todo list all
```

Het `todo` zojuist verwijderde deel moet niet aanwezig zijn.

### <a name="view-data-in-data-explorer"></a>Gegevens bekijken in Data Explorer

Gegevens die zijn opgeslagen in een Azure Cosmos DB-database kunnen via de Azure-portal worden bekeken en er kunnen vanuit de portal query's op worden uitgevoerd.

Meld u aan bij de [Azure Portal](https://portal.azure.com) in uw webbrowser om de gebruikersgegevens die u in de vorige stap hebt gemaakt, te bekijken, query’s erop uit te voeren of andere taken ermee uit te voeren.

Voer **Azure Cosmos DB** in het bovenste zoekvak in. Wanneer uw Cosmos-accountblade wordt geopend, selecteert u uw Cosmos-account. Selecteer in het linker navigatiegedeelte **Data Explorer**. Vouw uw verzameling uit in het venster Verzamelingen. Dan kunt u de documenten in de verzameling zien, query’s op de gegevens uitvoeren en zelfs opgeslagen procedures, triggers en UDF’s maken en uitvoeren.

## <a name="review-the-code-optional"></a>De code bekijken (optioneel)

Als u wilt weten hoe de toepassing werkt, kunt u de code fragmenten in deze sectie bekijken. De volgende code fragmenten zijn afkomstig uit het `src/main.rs` bestand.

De `main` functie is het toegangs punt voor de `todo` toepassing. Verwacht wordt dat de verbindings-URL voor de API van Azure Cosmos DB voor MongoDB door de `MONGODB_URL` omgevings variabele wordt weer gegeven. Er wordt een nieuw exemplaar van `TodoManager` gemaakt, gevolgd door een [ `match` expressie](https://doc.rust-lang.org/book/ch06-02-match.html) die wordt overgedragen aan de juiste `TodoManager` methode op basis van de bewerking die is gekozen door de gebruiker `create` ,, `update` `list` , of `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` is een `struct` waarmee een [MongoDb:: Sync:](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html):-verzameling wordt ingekapseld. Wanneer u probeert een te `TodoManager` maken met behulp van de `new` functie, initieert het een verbinding met de API van Azure Cosmos DB voor MongoDb.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

Het belangrijkste is dat `TodoManager` u beschikt over de methoden die u kunt gebruiken voor het beheren van `todo` s. Laten we één voor één door lopen.

De- `add_todo` methode krijgt een `todo` Beschrijving van de gebruiker en maakt een exemplaar van `Todo` struct, die er als volgt uitziet. Het [serde](https://github.com/serde-rs/serde) -Framework wordt gebruikt om BSON gegevens toe te wijzen (serialiseren/deserialiseren) in exemplaren van `Todo` structs. U ziet hoe `serde` veld kenmerken worden gebruikt voor het aanpassen van het serialisatie/de serialzation-proces. `todo_id`Het veld in de TODO is bijvoorbeeld `struct` een `ObjectId` en wordt opgeslagen in MongoDb als `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) accepteert een [document](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) met de `todo` Details die moeten worden toegevoegd. Houd er rekening mee dat de conversie van `Todo` naar a een `Document` proces met twee stappen is, behaald met een combi natie van [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) en [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Verzameling. Find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) wordt gebruikt om de *Alles* op te halen en te `todo` filteren op basis van de door de gebruiker gegeven status ( `pending` of `completed` ). U ziet in de `while` lus dat elke `Document` verkregen als resultaat van de zoek opdracht wordt omgezet in een `Todo` struct met behulp van [bson:: from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). Dit is het tegenovergestelde van wat er in de methode is gebeurd `add_todo` .

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

Een `todo` status kan worden bijgewerkt (van `pending` naar `completed` of andersom). De `todo` wordt geconverteerd naar een [bson:: OID:: ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) die vervolgens wordt gebruikt door de methode[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) om het document te vinden dat moet worden bijgewerkt.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

Het verwijderen `todo` van een is eenvoudig met behulp van de [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) methode.


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB MongoDB API-account maakt met behulp van de Azure Cloud Shell en een roest opdracht regel-app kunt maken en uitvoeren voor het beheren van `todo` s. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
