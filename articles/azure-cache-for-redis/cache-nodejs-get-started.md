---
title: 'Quickstart: Azure Cache voor Redis in Node.js gebruiken'
description: In deze snelstartgids leert u hoe u Azure Cache voor Redis gebruikt met Node.js en node_redis.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: e4c58d67668a67eee38a73d46a2a40ca29c1dfd8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121249"
---
# <a name="quickstart-use-azure-cache-for-redis-in-nodejs"></a>Quickstart: Azure Cache voor Redis in Node.js gebruiken

In deze quickstart neemt u Azure Cache voor Redis op in een Node.js-app voor toegang tot een veilige, toegewezen cache die toegankelijk is vanuit elke toepassing binnen Azure.

## <a name="skip-to-the-code-on-github"></a>Ga naar de code op GitHub

Als u direct naar de code wilt gaan, raadpleegt u de [Node.js Quick](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/nodejs) start op github.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis), dat u met de opdracht `npm install redis` kunt installeren. 

Voor voorbeelden van het gebruik van andere Node.js-clients, verwijzen wij u naar de afzonderlijke documentatie voor de Node.js-clients die staat vermeld op [Node.js Redis-clients](https://redis.io/clients#nodejs).

## <a name="create-a-cache"></a>Een cache maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Voeg omgevingsvariabelen toe voor **HOST NAME** en **Primary** (primaire toegangssleutel). U gebruikt deze variabelen vanuit uw code in plaats van de gevoelige gegevens rechtstreeks op te nemen in uw code.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Verbinding maken met de cache

De meest recente versies van [node_redis](https://github.com/mranney/node_redis) bieden ondersteuning voor het via TLS maken van verbinding met Azure Cache voor Redis. Het volgende voorbeeld laat zien hoe u via het TLS-eindpunt 6380 verbinding maakt met Azure Cache voor Redis. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Maak geen nieuwe verbinding voor elke bewerking in uw code. Probeer verbindingen zo veel mogelijk te hergebruiken. 

## <a name="create-a-new-nodejs-app"></a>Een nieuwe Node.js-app maken

Maak een nieuw scriptbestand met de naam *redistest.js*. Gebruik de opdracht `npm install redis bluebird` om de vereiste pakketten te installeren.

Voeg het volgende voorbeeld van JavaScript toe aan het bestand. Deze code laat zien hoe u verbinding kunt maken met een instantie van Azure Cache voor Redis via de omgevingsvariabelen voor hostnaam en sleutel. Met de code wordt ook een tekenreekswaarde opgeslagen in de cache en daaruit opgehaald. De opdrachten `PING` en `CLIENT LIST` worden ook uitgevoerd. Zie [https://redis.js.org/](https://redis.js.org/) voor meer voorbeelden van het gebruik van Redis met de [node_redis](https://github.com/mranney/node_redis)-client.

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the TLS port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Voer het script uit met Node.js.

```
node redistest.js
```

In het onderstaande voorbeeld ziet u dat de `Message`-sleutel eerder een waarde in de cache had, die was ingesteld met behulp van de Redis Console in Azure Portal. De app heeft die waarde in de cache bijgewerkt. De app heeft ook de opdrachten `PING` en `CLIENT LIST` uitgevoerd.

![Redis Cache-app voltooid](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die in deze snelstart zijn gemaakt behouden en opnieuw gebruiken.

Als u niet verder wilt met de voorbeeldtoepassing uit de snelstart, kunt u de Azure-resources verwijderen die in deze snelstart zijn gemaakt om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.
>

Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

Voer de naam van de resourcegroep in, in het tekstvak **Filteren op naam**. In de instructies voor dit artikel is een resourcegroep met de naam *TestResources* gebruikt. Selecteer **...** in de resourcegroep in de lijst met resultaten en vervolgens **Resourcegroep verwijderen**.

![Azure-resourcegroep verwijderen](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Voer de naam van de resourcegroep in ter bevestiging en selecteer **Verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u geleerd hoe u Azure Cache voor Redis gebruikt vanuit een Node.js-toepassing. Ga verder met de volgende snelstart om Azure Cache voor Redis te gebruiken met een ASP.NET-web-app.

> [!div class="nextstepaction"]
> [Maak een ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)
