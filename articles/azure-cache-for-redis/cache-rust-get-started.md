---
title: Azure Cache voor Redis met Rust gebruiken
description: In deze quickstart leert u hoe u Rust gebruikt voor interactie met Azure Cache voor Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121096"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Quickstart: Azure Cache voor Redis met Rust gebruiken

In dit artikel wordt beschreven hoe u de [programmeertaal Rust](https://www.rust-lang.org/) gebruikt voor interactie met [Azure Cache voor Redis](./cache-overview.md). Hierin worden voorbeelden gegeven van veelgebruikte Redis-gegevensstructuren (zoals een [tekenreeks](https://redis.io/topics/data-types-intro#redis-strings), [hash](https://redis.io/topics/data-types-intro#redis-hashes), [lijst](https://redis.io/topics/data-types-intro#redis-lists), enzovoort) met behulp van de bibliotheek [redis-rs](https://github.com/mitsuhiko/redis-rs) voor Redis. Deze client geeft API's op hoog en laag niveau weer en u kunt zien hoe beide stijlen in de praktijk werken met behulp van de voorbeeldcode in dit artikel.

## <a name="skip-to-the-code-on-github"></a>Ga naar de code op GitHub

Als u direct naar de code wilt gaan, raadpleegt u de [handroest Quick](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) start op github.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (versie 1.39 of hoger)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Een instantie van Azure Cache voor Redis maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>De code bekijken (optioneel)

Als u wilt weten hoe de code werkt, kunt u de volgende codefragmenten bekijken. Anders slaat u dit over en gaat u naar [De toepassing uitvoeren](#run-the-application).

De functie `connect` wordt gebruikt om verbinding te maken met Azure Cache voor Redis. De hostnaam en het wachtwoord (toegangssleutel) moeten respectievelijk worden doorgegeven via de omgevingsvariabelen `REDIS_HOSTNAME` en `REDIS_PASSWORD`. De indeling voor de verbindings-URL is `rediss://<username>:<password>@<hostname>`: Azure Cache voor Redis accepteert alleen beveiligde verbindingen met [TLS 1.2 als minimaal vereiste versie](cache-remove-tls-10-11.md).

De basisvalidatie wordt uitgevoerd door [redis::Client::open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) aan te roepen. De verbinding wordt tot stand gebracht met [get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection). Het programma stopt als dat om wat voor reden dan ook (bijvoorbeeld een onjuist wachtwoord) mislukt.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

De functie `basics` omvat opdrachten als [SET](https://redis.io/commands/set), [GET](https://redis.io/commands/get) en [INCR](https://redis.io/commands/incr). De API op laag niveau wordt gebruikt voor `SET` en `GET`, waarmee de waarde voor een sleutel met de naam `foo` wordt ingesteld en opgehaald. De opdracht `INCRBY` wordt uitgevoerd met een API op hoog niveau. Dat wil zeggen, met [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) wordt de waarde van een sleutel (met de naam `counter`) met `2` verhoogd, waarna [get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) wordt aangeroepen om de sleutel op te halen.

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

Het onderstaande codefragment toont de functionaliteit van een Redis `HASH`-gegevensstructuur. [HSET](https://redis.io/commands/hset) wordt aangeroepen met behulp van de API op laag niveau om informatie (`name`, `version`, `repo`) over Redis-stuurprogramma's (clients) op te slaan. Zo worden bijvoorbeeld details voor het Rust-stuurprogramma (dat wordt gebruikt in deze voorbeeldcode) vastgelegd in de vorm van een [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) en vervolgens doorgegeven aan de API op laag niveau. Deze worden vervolgens opgehaald met [HGETALL](https://redis.io/commands/hgetall).

`HSET` kan ook worden uitgevoerd met een API op hoog niveau met behulp van [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) die een matrix met tuples accepteert. [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) wordt vervolgens uitgevoerd om de waarde voor één kenmerk op te halen (`repo` in dit geval).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

In de onderstaande functie ziet u hoe u een `LIST`-gegevensstructuur kunt gebruiken. [LPUSH](https://redis.io/commands/lpush) wordt uitgevoerd (met de API op laag niveau) om een vermelding aan de lijst toe te voegen en de [lpop-](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) methode op hoog niveau wordt gebruikt om die uit de lijst op te halen. Vervolgens wordt de methode [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) gebruikt om een aantal vermeldingen aan de lijst toe te voegen, die vervolgens worden opgehaald met de methode [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) op laag niveau.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Hier ziet u een aantal voorbeelden van `SET`-bewerkingen. De methode [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (API op hoog niveau) wordt gebruikt om een aantal vermeldingen toe te voegen aan een `SET` met de naam `users`. Vervolgens wordt [SISMEMBER](https://redis.io/commands/hset) uitgevoerd (API op laag niveau) om te controleren of `user1` bestaat. Ten slotte wordt [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) gebruikt voor het ophalen en herhalen van alle ingestelde vermeldingen in de vorm van een vector ([Vec<String>](https://doc.rust-lang.org/std/vec/struct.Vec.html)).

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

De onderstaande `sorted_set`-functie demonstreert de gesorteerde setgegevensstructuur. [ZADD](https://redis.io/commands/zadd) wordt aangeroepen (met de API op laag niveau) om een willekeurige score voor een speler (`player-1`) in de vorm van een geheel getal toe te voegen. Vervolgens wordt de methode [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (API op hoog niveau) gebruikt om meer spelers (`player-2` tot `player-5`) en hun respectieve (willekeurig gegenereerde) scores toe te voegen. Het aantal vermeldingen in de gesorteerde set wordt bepaald met behulp van [ZCARD](https://redis.io/commands/zcard) en gebruikt als de limiet voor de [ZRANGE](https://redis.io/commands/zrange)-opdracht (aangeroepen met de API op laag niveau) om de spelers en de bijbehorende scores weer te geven in oplopende volgorde.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Begin met klonen van de toepassing vanuit GitHub.

1. Open een opdrachtprompt en maak een nieuwe map met de naam `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

1. Open een venster in een git-terminal zoals git bash. Gebruik de opdracht `cd` om naar de nieuwe map te gaan, waar u de voorbeeld-app gaat klonen.

    ```bash
    cd "C:\git-samples"
    ```

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

De toepassing accepteert connectiviteit en referenties in de vorm van omgevingsvariabelen. 

1. Haal in [Azure Portal](https://portal.azure.com/) de **hostnaam** en **toegangssleutels** op (beschikbaar via Toegangssleutels) voor de instantie van Azure Cache voor Redis.

1. Stel deze in op de respectieve omgevingsvariabelen:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Ga in het venster Terminal naar de juiste map. Bijvoorbeeld:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. Voer in het terminal de volgende opdracht uit om de toepassing te starten.

    ```shell
    cargo run
    ```
    
    U ziet nu uitvoer zoals dit:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Als u een specifieke functie wilt uitvoeren, moet u andere functies in de functie `main` markeren als commentaar:
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de Azure-resourcegroep en -resources die u hebt gemaakt in deze quickstart, kunt u ze verwijderen om kosten te voorkomen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Als u uw Azure Cache voor Redis-exemplaar hebt gemaakt in een bestaande resourcegroep die u wilt behouden, kunt u alleen de cache wissen door **Wissen** te selecteren op de pagina **Overzicht** van de cache. 

U kunt de resourcegroep en het bijbehorende Redis Cache voor Azure exemplaar als volgt verwijderen:

1. Zoek en selecteer in [Azure Portal](https://portal.azure.com) de optie **Resourcegroepen**.
1. Voer in het tekstvak **Filteren op naam** de naam in van de resourcegroep met uw cache-exemplaar en selecteer de naam vervolgens in de zoekresultaten. 
1. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep.
1. Typ de naam van de resourcegroep en selecteer vervolgens **Verwijderen**.
   
   ![Uw resourcegroep voor Azure Cache voor Redis verwijderen](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u het Rust-stuurprogramma voor Redis kunt gebruiken om verbinding te maken met en bewerkingen uit te voeren in Azure Cache voor Redis.

> [!div class="nextstepaction"]
> [Maak een eenvoudige ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)
