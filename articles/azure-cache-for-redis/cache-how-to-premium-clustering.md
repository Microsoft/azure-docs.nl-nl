---
title: Redis-clustering configureren-Premium Azure cache voor redis
description: Meer informatie over het maken en beheren van redis-Clustering voor uw Azure-cache voor de Premium-laag voor redis-instanties
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f1e84c838d310721cba604274388ae2767eb1502
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389668"
---
# <a name="configure-redis-clustering-for-a-premium-azure-cache-for-redis-instance"></a>Redis-clustering configureren voor een Premium Azure-cache voor het redis-exemplaar

Azure cache voor redis biedt redis-cluster zoals [geïmplementeerd in redis](https://redis.io/topics/cluster-tutorial). Met redis-cluster profiteert u van de volgende voor delen: 

* De mogelijkheid om uw gegevensset automatisch te verdelen over meerdere knoop punten. 
* De mogelijkheid om bewerkingen voort te zetten wanneer een subset van de knoop punten fouten ondervindt of niet in staat is te communiceren met de rest van het cluster. 
* Meer door Voer: de door Voer wordt lineair verhoogd naarmate u het aantal Shards verhoogt. 
* Meer geheugen grootte: er wordt lineair verhoogd naarmate u het aantal Shards verhoogt.  

Bij clustering wordt het aantal beschikbare verbindingen voor een geclusterde cache niet verhoogd. Zie [de juiste laag kiezen](cache-overview.md#choosing-the-right-tier) voor meer informatie over de grootte, de door Voer en de band breedte met Premium-caches.

In azure wordt redis-cluster aangeboden als een primair/replica-model, waarbij elke Shard een primair/replica-paar heeft met replicatie waarbij de replicatie wordt beheerd door Azure cache voor de redis-service. 

## <a name="set-up-clustering"></a>Clustering instellen

Clustering is ingeschakeld op de Blade **nieuwe Azure-cache voor redis** tijdens het maken van de cache. 

1. Als u een Premium-cache wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com) en selecteert u **een resource maken**. U kunt caches niet alleen in Azure Portal maken. U kunt ze ook maken met Resource Manager-sjablonen, PowerShell of Azure CLI. Zie [Een cache maken](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) voor meer informatie over het maken van een Azure Cache voor Redis.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Resource maken.":::
   
2. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecteer Azure Cache voor Redis.":::

3. Configureer op de pagina **nieuw redis cache** de instellingen voor uw nieuwe Premium-cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Vervolg keuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Vervolg keuzelijst en selecteer een resource groep of selecteer **nieuwe maken** en voer een nieuwe naam voor de resource groep in. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Vervolg keuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Cachetype** | En selecteer een Premium-cache om Premium-functies te configureren. Zie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/)voor meer informatie. |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |

4. Selecteer het tabblad **Netwerken** of klik op de knop **Netwerken** onderaan de pagina.

5. Selecteer uw verbindingsmethode op het tabblad **Netwerk**. Voor Premium-cache-instanties kunt u openbaar, via open bare IP-adressen of service-eind punten, of privé, verbinding maken met behulp van een persoonlijk eind punt.

6. Selecteer het tabblad **Volgende: Geavanceerd** of klik op de knop **Volgende: Geavanceerd** onderaan de pagina.

7. Configureer op het tabblad **Geavanceerd** voor een Premium-cache-exemplaar de instellingen voor niet-TLS-poort, clustering en gegevens persistentie. Als u Clustering wilt inschakelen, klikt u op **inschakelen**.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="Scha kelen tussen clusters.":::

    U kunt Maxi maal 10 Shards in het cluster hebben. Nadat u op **inschakelen** hebt geklikt, schuift u de schuif regelaar of typt u een getal tussen 1 en 10 voor **Shard aantal** en klikt u op **OK**.

    Elke Shard is een primair/replica-cache paar dat wordt beheerd door Azure en de totale grootte van de cache wordt berekend door het aantal Shards te vermenigvuldigen met de cache grootte die is geselecteerd in de prijs categorie.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="Scha kelen tussen clusters geselecteerd.":::

    Nadat de cache is gemaakt, maakt u er verbinding mee en gebruikt u deze net als een niet-geclusterde cache en redis distribueert de gegevens in de cache-Shards. Als diagnostische gegevens zijn [ingeschakeld](cache-how-to-monitor.md#enable-cache-diagnostics), worden metrische gegevens afzonderlijk voor elk Shard vastgelegd en kunnen ze worden [weer gegeven](cache-how-to-monitor.md) in de Blade Azure-cache voor redis. 

8. Selecteer het tabblad **Volgende: Tags** of klik op de knop **Volgende: Tags** onderaan de pagina.

9. Voer desgewenst in het tabblad **Tags** de naam en waarde in om de resource te categoriseren. 

10. Selecteer **Controleren + maken**. Het tabblad Beoordelen + maken wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

11. Selecteer **Maken** nadat het groene bericht Validatie geslaagd verschijnt.

Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken. 

> [!NOTE]
> 
> Er zijn enkele kleine verschillen die in uw client toepassing zijn vereist bij het configureren van clustering. Zie voor meer informatie [moet ik wijzigingen aanbrengen in mijn client toepassing voor het gebruik van clusters?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Zie het gedeelte [clustering. cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) van het [Hallo wereld](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) -voor beeld voor voorbeeld code over het werken met clustering met de client stack Exchange. redis.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>De cluster grootte wijzigen in een actieve Premium-cache
Als u de cluster grootte wilt wijzigen voor een actieve Premium-cache met clustering ingeschakeld, klikt u op **cluster grootte** in het **menu resource**.

![Grootte van redis-cluster][redis-cache-redis-cluster-size]

Als u de cluster grootte wilt wijzigen, gebruikt u de schuif regelaar of typt u een getal tussen 1 en 10 in het tekstvak **Shard aantal** en klikt u op **OK** om op te slaan.

Het verg Roten van de cluster grootte neemt de maximale doorvoer-en cache grootte toe. Als u de cluster grootte verhoogt, wordt het maximum aantal verbindingen dat beschikbaar is voor clients niet verhoogd.

> [!NOTE]
> Wanneer u een cluster schaalt, wordt de [migratie](https://redis.io/commands/migrate) opdracht uitgevoerd. Dit is een dure opdracht, zodat u deze bewerking kunt uitvoeren tijdens niet-piek uren. Tijdens het migratie proces ziet u een piek in de belasting van de server. Het schalen van een cluster is een langlopend proces en de hoeveelheid tijd die nodig is, is afhankelijk van het aantal sleutels en de grootte van de waarden die aan deze sleutels zijn gekoppeld.
> 
> 

## <a name="clustering-faq"></a>Veelgestelde vragen over clustering

De volgende lijst bevat antwoorden op veelgestelde vragen over Azure cache voor redis-clustering.

* [Moet ik wijzigingen aanbrengen in mijn client toepassing voor het gebruik van clusters?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hoe worden sleutels gedistribueerd in een cluster?](#how-are-keys-distributed-in-a-cluster)
* [Wat is de grootste cache grootte die ik kan maken?](#what-is-the-largest-cache-size-i-can-create)
* [Ondersteunen alle redis-clients clusteren?](#do-all-redis-clients-support-clustering)
* [Hoe kan ik verbinding maken met mijn cache als Clustering is ingeschakeld?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Kan ik rechtstreeks verbinding maken met de afzonderlijke Shards van mijn cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Kan ik Clustering voor een eerder gemaakte cache configureren?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Kan ik Clustering voor een Basic-of Standard-cache configureren?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Kan ik clustering gebruiken met de redis ASP.NET-sessie status-en uitvoer cache providers?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Ik krijg uitzonde ringen bij het gebruik van stack Exchange. redis en clustering, wat moet ik doen?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Moet ik wijzigingen aanbrengen in mijn client toepassing voor het gebruik van clusters?
* Als Clustering is ingeschakeld, is alleen data base 0 beschikbaar. Als uw client toepassing meerdere data bases gebruikt en probeert te lezen van of schrijven naar een andere data base dan 0, wordt de volgende uitzonde ring gegenereerd. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Zie voor meer informatie [redis cluster specificatie-geïmplementeerde subset](https://redis.io/topics/cluster-spec#implemented-subset).
* Als u [stack Exchange. redis](https://www.nuget.org/packages/StackExchange.Redis/)gebruikt, moet u 1.0.481 of hoger gebruiken. U maakt verbinding met de cache met behulp van dezelfde [eind punten, poorten en sleutels](cache-configure.md#properties) die u gebruikt wanneer u verbinding maakt met een cache waarvoor geen clustering is ingeschakeld. Het enige verschil is dat alle Lees-en schrijf bewerkingen moeten worden uitgevoerd naar data base 0.
  
  Andere clients hebben mogelijk andere vereisten. Zie [alle redis-clients clustering ondersteunen?](#do-all-redis-clients-support-clustering)
* Als uw toepassing meerdere sleutel bewerkingen in een enkele opdracht gebruikt, moeten alle sleutels zich in dezelfde Shard bevinden. Zie [Hoe worden sleutels gedistribueerd in een cluster?](#how-are-keys-distributed-in-a-cluster)
* Als u de redis ASP.NET-sessie status provider gebruikt, moet u 2.0.1 of hoger gebruiken. Zie [kan ik clustering gebruiken met de Redis ASP.net-sessie status en uitvoer cache providers?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hoe worden sleutels gedistribueerd in een cluster?
Volgens de redis-documentatie over het [distributie model voor sleutels](https://redis.io/topics/cluster-spec#keys-distribution-model) : de sleutel ruimte is opgesplitst in 16384 sleuven. Elke sleutel wordt gehasht en toegewezen aan een van deze sleuven, die over de knoop punten van het cluster worden gedistribueerd. U kunt configureren welk deel van de sleutel wordt gehasht om ervoor te zorgen dat meerdere sleutels zich in dezelfde Shard bevinden via hash-tags.

* Sleutels met een hash-code: als een deel van de sleutel is inge sloten in `{` en `}` , wordt alleen dat gedeelte van de sleutel gehasht om de hash-sleuf van een sleutel te bepalen. De volgende drie sleutels bevinden zich bijvoorbeeld in dezelfde Shard: `{key}1` , `{key}2` en `{key}3` omdat alleen het `key` deel van de naam wordt gehasht. Zie [hash-tags voor sleutels](https://redis.io/topics/cluster-spec#keys-hash-tags)voor een volledige lijst met de specificaties van hash-labels.
* Sleutels zonder hash-code-de volledige sleutel naam wordt gebruikt voor hashing. Dit resulteert in een statistische, gelijkmatige verdeling over de Shards van de cache.

Voor de beste prestaties en door Voer wordt u aangeraden de sleutels gelijkmatig te verdelen. Als u sleutels met een hash-code gebruikt, is het de verantwoordelijkheid van de toepassing om ervoor te zorgen dat de sleutels gelijkmatig worden gedistribueerd.

Zie voor meer informatie [sleutels Distribution model](https://redis.io/topics/cluster-spec#keys-distribution-model), [redis cluster data sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)en [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags).

Zie het gedeelte [clustering. cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) van het [Hallo wereld](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) voor beeld van code voor het werken met clustering en het vinden van sleutels in dezelfde Shard met de client stack Exchange. redis.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Wat is de grootste cache grootte die ik kan maken?
De grootste Premium-cache grootte is 120 GB. U kunt Maxi maal 10 Shards maken met een maximale grootte van 1,2 TB GB. Als u een grotere grootte nodig hebt, kunt u [meer aanvragen](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Zie [Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) voor meer informatie.

### <a name="do-all-redis-clients-support-clustering"></a>Ondersteunen alle redis-clients clusteren?
Niet alle clients bieden ondersteuning voor redis-clustering. Raadpleeg de documentatie voor de bibliotheek die u gebruikt om te controleren of u een bibliotheek en versie gebruikt die ondersteuning biedt voor clustering. Stack Exchange. redis is een bibliotheek die clustering ondersteunt in de nieuwere versies. Zie de sectie [afspelen met het cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) in de [redis-cluster zelf studie](https://redis.io/topics/cluster-tutorial)voor meer informatie over andere clients. 

Het redis-cluster protocol vereist dat elke client rechtstreeks verbinding maakt met elke Shard in de cluster modus en dat er ook nieuwe fout reacties worden gedefinieerd, zoals ' verplaatste ' n.v.t. ' CROSSSLOTS '. Het gebruik van een client die geen ondersteuning biedt voor clustering met een cluster modus cache, kan leiden tot een groot aantal [verplaatsings uitzonderingen](https://redis.io/topics/cluster-spec#moved-redirection), of alleen uw toepassing afwijzen als u multi-Key-aanvragen van meerdere sleuven uitvoert.

> [!NOTE]
> Als u stack Exchange. redis als uw client gebruikt, moet u ervoor zorgen dat u de nieuwste versie van [stack Exchange. redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 of hoger gebruikt, anders werkt clusteren niet goed. Zie [uitzonde ringen verplaatsen](#move-exceptions) voor meer informatie als u problemen ondervindt met het verplaatsen van uitzonde ringen.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hoe kan ik verbinding maken met mijn cache als Clustering is ingeschakeld?
U kunt verbinding maken met uw cache met behulp van dezelfde [eind punten](cache-configure.md#properties), [poorten](cache-configure.md#properties)en [sleutels](cache-configure.md#access-keys) die u gebruikt wanneer u verbinding maakt met een cache waarvoor geen clustering is ingeschakeld. Redis beheert de Clustering op de back-end zodat u deze niet hoeft te beheren vanaf uw client.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Kan ik rechtstreeks verbinding maken met de afzonderlijke Shards van mijn cache?
Voor het clustering-protocol moet de client de juiste Shard-verbindingen maken. Daarom moet de client dit op de juiste manier uitvoeren. In dat geval bestaat elk Shard uit een primair/replica-cache paar, gezamenlijk bekend als een cache-exemplaar. U kunt verbinding maken met deze cache-instanties met behulp van het redis-cli-hulp programma in de [instabiele](https://redis.io/download) vertakking van de redis-opslag plaats op github. Deze versie implementeert Basic-ondersteuning wanneer u begint met de `-c` Switch. Zie voor meer informatie [afspelen met het cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) op [https://redis.io](https://redis.io) in de [zelf studie redis-cluster](https://redis.io/topics/cluster-tutorial).

Voor niet-TLS, gebruikt u de volgende opdrachten.

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

Vervang voor TLS door `1300N` `1500N` .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Kan ik Clustering voor een eerder gemaakte cache configureren?
Ja. Zorg er eerst voor dat uw cache Premium is door te schalen als dat niet het geval is. Vervolgens zou u de cluster configuratie opties moeten kunnen zien, met inbegrip van een optie voor het inschakelen van het cluster. U kunt de cluster grootte wijzigen nadat de cache is gemaakt, of nadat u Clustering voor de eerste keer hebt ingeschakeld.

   >[!IMPORTANT]
   >Het inschakelen van clusters kan niet ongedaan worden gemaakt. En een cache waarbij Clustering is ingeschakeld en slechts één Shard gedraagt *anders* dan een *cache met dezelfde grootte zonder clusters.*

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Kan ik Clustering voor een Basic-of Standard-cache configureren?
Clustering is alleen beschikbaar voor Premium-caches.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Kan ik clustering gebruiken met de redis ASP.NET-sessie status-en uitvoer cache providers?
* **Redis-uitvoer cache provider** : er zijn geen wijzigingen vereist.
* **Redis-sessie status provider** : als u Clustering wilt gebruiken, moet u [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 of hoger gebruiken, anders wordt er een uitzonde ring gegenereerd. Dit is een belang rijke wijziging; Zie [v 2.0.0 break Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)(Engelstalig) voor meer informatie.

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Ik krijg uitzonde ringen bij het gebruik van stack Exchange. redis en clustering, wat moet ik doen?
Als u stack Exchange. redis gebruikt en `MOVE` uitzonde ringen ontvangt wanneer u Clustering gebruikt, moet u ervoor zorgen dat u [stack Exchange. redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) of hoger gebruikt. Zie [Configure the cache clients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)(Engelstalig) voor instructies over het configureren van uw .NET-toepassingen om stack Exchange. redis te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
