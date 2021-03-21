---
title: Voorbeelden voor Azure Cache voor Redis
description: 'Leer hoe u hoe u Azure Cache voor Redis kunt gebruiken met deze codevoorbeelden: verbinding maken met een cache, gegevens in een cache lezen en naar een cache schrijven, ASP.NET Azure Cache voor Redis-providers.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88212291"
---
# <a name="azure-cache-for-redis-samples"></a>Voorbeelden voor Azure Cache voor Redis
In dit onderwerp vindt u een lijst met voorbeelden voor Azure Cache voor Redis. Deze kunnen worden gebruikt in scenario's zoals voor het maken van verbinding met een cache, het lezen en schrijven van gegevens van en naar een cache en voor het gebruik van de ASP.NET Azure Cache voor Redis-providers. Sommige van de voorbeelden zijn downloadbare projecten en sommige bieden stapsgewijze instructies. Ze bevatten codefragmenten, maar zijn niet gekoppeld aan een downloadbaar project.

## <a name="hello-world-samples"></a>Hallo wereld-voorbeelden
De voorbeelden in deze sectie demonstreren de basisbeginselen van het maken van verbinding met een instantie van Azure Cache voor Redis en van het lezen en schrijven van gegevens naar de cache met behulp van diverse talen en Redis-clients.

In het [Hallo wereld](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-voorbeeld ziet u hoe u verschillende cachebewerkingen kunt uitvoeren met de .NET-client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

Dit voorbeeld laat zien hoe u het volgende kunt doen:

* Verschillende verbindingsopties gebruiken
* Objecten van en naar de cache lezen en schrijven met synchrone en asynchrone bewerkingen
* Redis MGET/MSET-opdrachten gebruiken om waarden van opgegeven sleutels te retourneren
* Transactionele bewerkingen in Redis uitvoeren
* Werken met Redis-lijsten en gesorteerde sets
* .NET-objecten opslaan met JsonConvert-serialisatiefuncties
* Redis-sets gebruiken voor het implementeren van tags
* Werken met een Redis-cluster

Zie de documentatie over [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) op GitHub voor meer informatie en raadpleeg de eenheidstesten [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) voor meer gebruiksscenario's.

[Azure Cache voor Redis gebruiken met Python](cache-python-get-started.md) toont hoe u met behulp van Python en de [redis-py](https://github.com/andymccurdy/redis-py)-client aan de slag kunt met Azure Cache voor Redis.

[Werken met .NET-objecten in de cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) toont u een manier voor het serialiseren van .NET-objecten, zodat u deze kunt schrijven naar en lezen vanuit een instantie van Azure Cache voor Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Azure Cache voor Redis gebruiken als een backplane voor het uitschalen van ASP.NET SignalR
Het voorbeeld [Azure Cache voor Redis gebruiken als een backplane voor het uitschalen van ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) laat zien hoe u Azure Cache voor Redis kunt gebruiken als backplane voor SignalR. Zie [Signalr uitschalen met Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)voor meer informatie over backplanes.

## <a name="azure-cache-for-redis-customer-query-sample"></a>Voorbeeld van klantquery over Azure Cache voor Redis
Met dit voorbeeld worden de prestaties vergeleken bij het gebruik van gegevens uit een cache en bij het gebruik van gegevens in een persistentie opslag. Dit voorbeeld heeft twee projecten.

* [Demo van hoe Azure Cache voor Redis de prestaties kan verbeteren door gegevens in de cache op te slaan](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [De database en cache voor de demo seeden](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET--sessiestatus en uitvoercaching
Het voorbeeld [Azure Cache voor Redis gebruiken voor het opslaan van de ASP.NET ASP.NET SessionState and OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) toont hoe u Azure Cache voor Redis kunt gebruiken om ASP.NET Session and Output Cache op te slaan met behulp van de SessionState- en OutputCache-providers voor Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Azure Cache voor Redis beheren met MAML
Het voorbeeld [Azure Cache voor Redis beheren met behulp van Azure-beheerbibliotheken](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) laat zien hoe u Azure-beheerbibliotheken kunt gebruiken voor het beheren van uw cache (maken/bijwerken/verwijderen). 

## <a name="custom-monitoring-sample"></a>Voorbeeld voor aangepaste bewaking
Het voorbeeld [Toegang tot bewakingsgegevens van Azure Cache voor Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) toont hoe u buiten Azure Portal toegang kunt krijgen tot bewakingsgegevens voor uw Azure Cache voor Redis.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Een kloon in Twitter-stijl geschreven met behulp van PHP en Redis
Het voorbeeld [Retwis](https://github.com/SyntaxC4-MSFT/retwis) is de Redis Hallo wereld. Het is een minimalistische kloon van een sociaal netwerk in Twitter-stijl die is geschreven met Redis en PHP en waarbij de [Predis](https://github.com/nrk/predis)-client is gebruikt. De broncode is ontworpen met eenvoud in gedachte, maar die tegelijkertijd de verschillende Redis-gegevensstructuren laat zien.

## <a name="bandwidth-monitor"></a>Bandbreedtebewaking
Met het voorbeeld [Bandbreedtebewaking](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) kunt u de bandbreedte bewaken die op de client wordt gebruikt. Als u de bandbreedte wilt meten, voert u het voorbeeld op de cache-clientcomputer uit, zorgt u dat de cache wordt aangeroepen en bekijkt u de bandbreedte die wordt gerapporteerd door het voorbeeld Bandbreedtebewaking.
