---
title: Een Go-app bouwen met Azure Cosmos DB Cassandra-API met behulp van de gocql-client
description: In deze quickstart ziet u hoe u een Go-client gebruikt om te communiceren met Azure Cosmos DB Cassandra-API
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93076398"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Quickstart: Een Go-app bouwen met de client `gocql` om Azure Cosmos DB Cassandra-API-gegevens te beheren
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden. In deze quickstart begint u met het maken van een Azure Cosmos DB Cassandra-API-account. Vervolgens voert u een Go-toepassing uit om een Cassandra-keyspace en een tabel te maken en een aantal bewerkingen uit te voeren. Deze Go-app maakt gebruik van [gocql](https://github.com/gocql/gocql), een Cassandra-client voor de Go-taal. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) zonder Azure-abonnement.
- [Go](https://golang.org/) moet zijn geïnstalleerd op uw computer en u moet praktische kennis van Go hebben.
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een database kunt maken, moet u een Cassandra-account maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Begin met klonen van de toepassing vanuit GitHub.

1. Open een opdrachtprompt en maak een nieuwe map met de naam `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

2. Open een venster in een git-terminal zoals git bash. Gebruik de opdracht `cd` om dit in de nieuwe map te veranderen en installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources met de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Anders slaat u dit over en gaat u naar [De toepassing uitvoeren](#run-the-application)

De functie `GetSession` (onderdeel van `utils\utils.go`) retourneert een [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) die wordt gebruikt voor het uitvoeren van clusterbewerkingen, zoals invoegen, zoeken, enzovoort.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

De Azure Cosmos DB Cassandra-host wordt door gegeven aan de [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster)-functie om een struct [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) te verkrijgen die vervolgens is geconfigureerd voor het gebruik van de gebruikersnaam, het wachtwoord, de poort en de juiste TLS-versie ([vereiste voor de beveiliging van HTTPS/SSL/TLS-versleuteling](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database))

De functie `GetSession` wordt vervolgens aangeroepen vanuit de functie `main` (`main.go`).

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

De verbindingsgegevens en referenties worden geaccepteerd in de vorm van omgevingsvariabelen (opgelost in de methode `init`)

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Deze wordt vervolgens gebruikt voor het uitvoeren van verschillende bewerkingen (onderdeel van `operations\setup.go`) op Azure Cosmos DB die beginnen met `keyspace` en het maken van `table`.

Zoals de naam wordt voorgesteld, wordt de `keyspace` alleen door de functie `DropKeySpaceIfExists` neergezet als deze bestaat.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

De functie `CreateKeySpace` wordt gebruikt voor het maken van de `keyspace` (`user_profile`)

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Dit wordt gevolgd door het maken van tabellen (`user`), waarbij functie `CreateUserTable` wordt gebruikt

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

Zodra de keyspace en tabel zijn gemaakt, worden er CRUD-bewerkingen aanroepen (onderdeel van `operations\crud.go`). 

`InsertUser` wordt gebruikt om een `User` te maken. De gebruikersgegevens (ID, naam en plaats) worden ingesteld als de query-argumenten met [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` wordt gebruikt om te zoeken naar een gebruiker (`model\user.go`) met behulp van een specifieke gebruikers-id terwijl [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) de gebruikers kenmerken (geretourneerd door Cassandra) koppelt aan afzonderlijke variabelen (`userid`, `name`, `city`). Dit is slechts een van de manieren waarop u het resultaat van de zoekquery kunt gebruiken

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` wordt gebruikt om alle gebruikers op te halen. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) wordt gebruikt als een steno om alle gegevens van de gebruiker op te halen in de vorm van een segment van `map`s. U kunt elk `map` beschouwen als sleutelwaardeparen waarbij de kolomnaam (bijvoorbeeld `user_id`) de sleutel is en de bijbehorende waarde.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Elk `map` van gebruikersgegevens wordt geconverteerd naar een `User` met behulp van functie `mapToUser` waarmee de waarde van de desbetreffende kolom wordt geëxtraheerd en wordt gebruikt om een instantie van de struct `User` te maken

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Zoals eerder vermeld, accepteert de toepassing connectiviteit en referenties in de vorm van de omgevingsvariabelen. 

1. Selecteer in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/) de optie **Verbindingsreeks**. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Details uit de pagina Verbindingsreeks bekijken en kopiëren via Azure Portal":::

Kopieer de waarden voor de volgende kenmerken (`CONTACT POINT`, `PORT`, `USERNAME` en `PRIMARY PASSWORD`) en stel deze in op de respectieve omgevingsvariabelen

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

Ga in het venster Terminal naar de juiste map. Bijvoorbeeld:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. Voer in het terminal de volgende opdracht uit om de toepassing te starten.

```shell
go run main.go
```

3. In het venster terminal worden meldingen weergegeven voor de verschillende bewerkingen, zoals het instellen van de instellingen van de keyspace en tabel, maken van gebruikers, enz.

4. Open **Data Explorer** in de Azure-portal om deze nieuwe gegevens te bekijken, te wijzigen, een query erop uit te voeren of er iets anders mee te doen. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="De gegevens bekijken in Data Explorer - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt met Cassandra-API, en een Go-app uitvoert waarmee een Cassandra-database en -container wordt gemaakt. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Cassandra-gegevens importeren in Azure Cosmos DB](cassandra-import-data.md)