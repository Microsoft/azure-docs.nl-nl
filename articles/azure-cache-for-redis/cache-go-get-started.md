---
title: Azure Cache voor Redis met Go gebruiken
description: In deze quickstart leert u een Go-app maken die gebruikmaakt van Azure Cache voor Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 04b582b5ef31e61039c5513ea2a4aa60f1c638e7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121334"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Quickstart: Azure Cache voor Redis met Go gebruiken

In dit artikel leert u hoe u een REST API in Go bouwt waarmee gebruikersgegevens worden opgeslagen en opgehaald. Deze worden ondersteund door een [HASH](https://redis.io/topics/data-types-intro#redis-hashes)-gegevensstructuur in [Azure Cache voor Redis](./cache-overview.md). 

## <a name="skip-to-the-code-on-github"></a>Ga naar de code op GitHub

Als u direct naar de code wilt gaan, raadpleegt u de [Go Quick Start](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/) op github.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (bij voorkeur versie 1.13 of hoger)
- [Git](https://git-scm.com/downloads)
- Een HTTP-client, bijvoorbeeld [curl](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Een instantie van Azure Cache voor Redis maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>De code beoordelen (optioneel)

Als u wilt weten hoe de code werkt, kunt u de volgende codefragmenten bekijken. Anders slaat u dit over en gaat u naar [De toepassing uitvoeren](#run-the-application).

De opensourcebibliotheek [go-redis](https://github.com/go-redis/redis) wordt gebruikt om te communiceren met Azure Cache voor Redis.

De functie `main` wordt gestart door de hostnaam en het wachtwoord (toegangssleutel) te lezen voor de instantie van Azure Cache voor Redis.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Vervolgens wordt verbinding gemaakt met Azure Cache voor Redis. Er wordt gebruikgemaakt van [tls.Config](https://golang.org/pkg/crypto/tls/#Config). Azure Cache voor Redis accepteert alleen beveiligde verbindingen met [TLS 1.2 als minimaal vereiste versie](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Als de verbinding tot stand is gebracht, worden [HTTP-handlers](https://golang.org/pkg/net/http/#HandleFunc) geconfigureerd om `POST` -en `GET`-bewerkingen te verwerken en wordt de HTTP-server gestart. 

> [!NOTE] 
> [de gorilla mux-bibliotheek](https://github.com/gorilla/mux) wordt gebruikt voor routering (hoewel deze niet strikt noodzakelijk is en ook de standaardbibliotheek voor deze voorbeeldtoepassing had kunnen worden gebruikt).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

De struct `userHandler` kapselt een [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client) in, die wordt gebruikt in de methoden `createUser` en `getUser`. Code voor deze methoden is vanwege de ruimte niet opgenomen. 

- `createUser`: hiermee wordt een JSON-nettolading (met gebruikersgegevens) geaccepteerd en in Azure Cache voor Redis opgeslagen als `HASH`.
- `getUser`: hiermee worden gebruikersgegevens opgehaald uit `HASH` of wordt een HTTP `404`-antwoord geretourneerd als de gegevens niet worden gevonden.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

De toepassing accepteert connectiviteit en referenties in de vorm van omgevingsvariabelen. 

1. Haal in [Azure Portal](https://portal.azure.com/) de **hostnaam** en **toegangssleutels** op (beschikbaar via Toegangssleutels) voor de instantie van Azure Cache voor Redis

1. Stel deze in op de respectieve omgevingsvariabelen:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Ga in het venster Terminal naar de juiste map. Bijvoorbeeld:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. Voer in het terminal de volgende opdracht uit om de toepassing te starten.

    ```shell
    go run main.go
    ```

De HTTP-server wordt gestart op poort `8080`.

## <a name="test-the-application"></a>De toepassing testen

1. Maak een paar gebruikersvermeldingen. In het onderstaande voorbeeld wordt curl gebruikt:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Haal een bestaande gebruiker op met de bijbehorende `id`:

    ```bash
    curl -i localhost:8080/users/1
    ```

    U moet een JSON-antwoord krijgen dat er als volgt uitziet:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Als u een gebruiker probeert op te halen die niet bestaat, krijgt u een HTTP `404`-fout. Bijvoorbeeld:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
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

In deze quickstart hebt u geleerd hoe u met Go aan de slag gaat met Azure Cache voor Redis. U hebt een eenvoudige op een REST API gebaseerde toepassing geconfigureerd en uitgevoerd voor het maken en ophalen van gebruikersgegevens, die door een Redis `HASH`-gegevensstructuur worden ondersteund.

> [!div class="nextstepaction"]
> [Maak een eenvoudige ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)
