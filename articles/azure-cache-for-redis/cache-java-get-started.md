---
title: 'Quickstart: Azure Cache voor Redis in Java gebruiken'
description: In deze snelstart maakt u een nieuwe Java-app die gebruikmaakt van Azure Cache voor Redis
author: yegu-ms
ms.service: cache
ms.devlang: java
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: yegu
ms.custom: mvc, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 856f33875316eca8b3c16f28c6c222f42e0a0be5
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121266"
---
# <a name="quickstart-use-azure-cache-for-redis-in-java"></a>Quickstart: Azure Cache voor Redis in Java gebruiken

In deze quickstart neemt u Azure Cache voor Redis op in een Java-app met de [Jedis](https://github.com/xetorthio/jedis) Redis-client voor toegang tot een veilige, toegewezen cache dat toegankelijk is vanuit elke toepassing binnen Azure.

## <a name="skip-to-the-code-on-github"></a>Ga naar de code op GitHub

Als u direct naar de code wilt gaan, raadpleegt u de [Java Quick](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/java) start op github.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis-instantie maken

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

## <a name="setting-up-the-working-environment"></a>De werkomgeving instellen 

Afhankelijk van het besturingssysteem, voegt u omgevingsvariabelen toe voor **Hostnaam** en **Primaire toegangssleutel**. Open een opdrachtprompt, of een terminalvenster, en stel de volgende waarden in:

```CMD 
set REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
set REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

```bash
export REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
export REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

Vervang de plaatsaanduidingen door de volgende waarden:

- `<YOUR_HOST_NAME>`: De DNS-hostnaam, op te halen uit de sectie *Eigenschappen* van de resource voor Azure Cache voor Redis in Azure Portal.
- `<YOUR_PRIMARY_ACCESS_KEY>`: De primaire toegangssleutel, op te halen uit de sectie *Toegangssleutels* van de resource voor Azure Cache voor Redis in Azure Portal.

## <a name="create-a-new-java-app"></a>Een nieuwe Java-app maken

Met behulp van Maven een nieuwe snelstartgids-app maken:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Ga naar de nieuwe projectmap *redistest*.

Open het bestand *pom.xml* en voeg een afhankelijkheid toe voor [Jedis](https://github.com/xetorthio/jedis):

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Sla het bestand *pom.xml* op.

Open *App.java* en vervang de code door de volgende code:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the TLS/SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Deze code laat zien hoe u verbinding kunt maken met een instantie van Azure Cache voor Redis via de omgevingsvariabelen voor hostnaam en sleutel. Met de code wordt ook een tekenreekswaarde opgeslagen in de cache en daaruit opgehaald. De opdrachten `PING` en `CLIENT LIST` worden ook uitgevoerd. 

Sla *App.java* op.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Voer de volgende Maven-opdracht uit om de app te bouwen en uit te voeren:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

In het onderstaande voorbeeld ziet u dat de `Message`-sleutel eerder een waarde in de cache had, die was ingesteld met behulp van de Redis Console in Azure Portal. De app heeft die waarde in de cache bijgewerkt. De app heeft ook de opdrachten `PING` en `CLIENT LIST` uitgevoerd.

![Azure Cache voor Redis-app voltooid](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die in deze snelstart zijn gemaakt behouden en opnieuw gebruiken.

Als u niet verder wilt met de voorbeeldtoepassing uit de snelstart, kunt u de Azure-resources verwijderen die in deze snelstart zijn gemaakt om kosten te voorkomen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.
>

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

1. Typ de naam van de resourcegroep in het tekstvak **Filteren op naam**. In de instructies voor dit artikel is een resourcegroep met de naam *TestResources* gebruikt. Selecteer **...** in de resourcegroep in de lijst met resultaten en vervolgens **Resourcegroep verwijderen**.

   ![Azure-resourcegroep verwijderd](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ ter bevestiging de naam van de resourcegroep nogmaals en selecteer **Verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u Azure Cache voor Redis gebruikt vanuit een Java-toepassing. Ga verder met de volgende snelstart om Azure Cache voor Redis te gebruiken met een ASP.NET-web-app.

> [!div class="nextstepaction"]
> [Maak een ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)
