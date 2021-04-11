---
title: 'Snelstartgids: verbinding maken met roest-Azure Database for PostgreSQL-één server'
description: Deze Quick Start biedt roest code voorbeelden die u kunt gebruiken om verbinding te maken en gegevens op te vragen van Azure Database for PostgreSQL-één server.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505849"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quick Start: roest gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for PostgreSQL-één server

In dit artikel leert u hoe u het [postgresql-stuur programma voor roest](https://github.com/sfackler/rust-postgres) kunt gebruiken om te communiceren met Azure database for POSTGRESQL door ruwe (Create-, Read-, update-, Delete)-bewerkingen te verkennen die in de voorbeeld code zijn geïmplementeerd. Ten slotte kunt u de toepassing lokaal uitvoeren om deze in actie te zien.

## <a name="prerequisites"></a>Vereisten
Voor deze quickstart hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
- Er is een recente versie van [roest](https://www.rust-lang.org/tools/install) geïnstalleerd.
- Een Azure Database for PostgreSQL enkele server: Maak er een met [Azure Portal](./quickstart-create-server-database-portal.md) <br/> of [Azure cli](./quickstart-create-server-database-azure-cli.md).
- Voltooi **EEN** van de onderstaande acties om connectiviteit in te schakelen, afhankelijk van of u openbare toegang of privétoegang hebt.

  |Bewerking| Verbindingsmethode|Instructiegids|
  |:--------- |:--------- |:--------- |
  | **Firewallregels configureren** | Openbaar | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Service-eindpunt configureren** | Openbaar | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Privékoppeling configureren** | Privé | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) geïnstalleerd.

## <a name="get-database-connection-information"></a>De verbindingsgegevens voor de database ophalen
Voor het maken van verbinding met een Azure Database for PostgreSQL-database zijn de volledig gekwalificeerde servernaam en aanmeldingsreferenties vereist. U kunt deze informatie ophalen uit Azure Portal.

1. Zoek en selecteer in [Azure Portal](https://portal.azure.com/) uw Azure Database for PostgreSQL-servernaam.
1. Kopieer op de pagina **Overzicht** van de server de volledig gekwalificeerde **servernaam** en de **gebruikersnaam met beheerdersrechten**. De volledig gekwalificeerde **servernaam** heeft altijd de vorm *\<my-server-name>.postgres.database.azure.com* en de **gebruikersnaam met beheerdersrechten** heeft altijd van de vorm *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>De code bekijken (optioneel)

Als u wilt weten hoe de code werkt, kunt u de volgende codefragmenten bekijken. Anders slaat u dit over en gaat u naar [De toepassing uitvoeren](#run-the-application).

### <a name="connect"></a>Verbinding maken

De `main` functie begint met het maken van verbinding met Azure database for PostgreSQL en is afhankelijk van de volgende omgevings variabelen voor connectiviteits gegevens `POSTGRES_HOST` , `POSTGRES_USER` `POSTGRES_PASSWORD` en `POSTGRES_DBNAME` . De PostgreSQL-database service is standaard geconfigureerd om verbinding te vereisen `TLS` . U kunt ervoor kiezen om het vereist uit te scha kelen `TLS` als uw client toepassing geen `TLS` connectiviteit ondersteunt. Raadpleeg [Configure TLS Connectivity in azure database for PostgreSQL-Single Server](./concepts-ssl-connection-security.md)voor meer informatie.

De voorbeeld toepassing in dit artikel gebruikt TLS met de [post gres-openssl](https://crates.io/crates/postgres-openssl/). [post gres:: client:: Connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) -functie wordt gebruikt om de verbinding te initiëren en het programma wordt afgesloten als dit mislukt.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Tabel neerzetten en maken

De voorbeeld toepassing maakt gebruik van een eenvoudige `inventory` tabel om de ruwe (maken, lezen, bijwerken, verwijderen)-bewerkingen te demonstreren.

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

De `drop_create_table` functie probeert eerst naar `DROP` de `inventory` tabel om een nieuwe te maken. Dit maakt het gemakkelijker voor leren/experimenteren, omdat u altijd begint met een bekende (schone) status. De methode [Execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) wordt gebruikt voor het maken en neerzetten van bewerkingen. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Gegevens invoegen

`insert_data` Hiermee worden items toegevoegd aan de `inventory` tabel. Hiermee maakt u een [voor bereide instructie](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) met de functie [voorbereiden](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) . 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Let ook op het gebruik van [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) methode, waarmee de typen query parameters expliciet kunnen worden opgegeven.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Ten slotte `for` wordt een lus gebruikt voor het `item-3` toevoegen `item-4` en, `item-5` met een wille keurig gegenereerd aantal voor elke.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Querygegevens

`query_data` de functie demonstreert hoe u gegevens ophaalt uit de `inventory` tabel. De methode [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) wordt gebruikt om een item op te halen `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Alle rijen in de voorraad tabel worden opgehaald met behulp `select * from` van een query met de [query](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) -methode. De geretourneerde rijen worden herhaald om de waarde voor elke kolom op te halen met behulp van [Get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Houd er rekening mee `get` dat het mogelijk is om de kolom op te geven op basis van de numerieke index in de rij of de kolom naam.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Gegevens bijwerken

De `update_date` functie werkt de hoeveelheid voor alle items wille keurig bij. Omdat de `insert_data` functie rijen heeft toegevoegd `5` , wordt in de `for` lus- `for n in 1..=5`

> [!TIP]
> Let op: we gebruiken `query` in plaats van, `execute` omdat we willen terugkeren naar de `id` nieuwe en de zojuist gegenereerde `quantity` (met behulp van de [component retour neren](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Gegevens verwijderen

Ten slotte wordt `delete` gedemonstreerd hoe u een item uit de tabel kunt verwijderen `inventory` met de functie `id` . De `id` wordt wille keurig gekozen: het is een wille keurig geheel getal tussen de `1` `5` ( `5` inclusief), omdat de `insert_data` functie rijen heeft toegevoegd `5` om te beginnen met. 

> [!TIP]
> Let op: we gebruiken `query` in plaats van `execute` omdat we van plan zijn de informatie over het item dat we zojuist hebben verwijderd te herstellen (met behulp van de [component retour neren](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

1. Als u wilt beginnen met, voert u de volgende opdracht uit om de voorbeeld opslagplaats te klonen:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Stel de vereiste omgevings variabelen in met de waarden die u hebt gekopieerd uit de Azure Portal:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Als u de toepassing wilt uitvoeren, gaat u naar de map waar u deze hebt gekloond en voert u de `cargo run` volgende handelingen uit:

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    U moet een uitvoer zien die er ongeveer als volgt uitziet:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Als u wilt bevestigen, kunt u ook verbinding maken met Azure Database for PostgreSQL [met behulp van psql](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) en query's uitvoeren voor de data base, bijvoorbeeld:

    ```sql
    select * from inventory;
    ```

[Ondervindt u problemen? Laat het ons weten](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt opschonen die tijdens deze quickstart zijn gebruikt, verwijdert u de resourcegroep. Dit kan met de volgende opdracht:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL server beheren via de portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL server beheren met CLI](./how-to-manage-server-cli.md)<br/>

[Kunt u niet vinden wat u zoekt? Laat het ons weten.](https://aka.ms/postgres-doc-feedback)
