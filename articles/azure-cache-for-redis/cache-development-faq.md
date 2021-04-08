---
title: Veelgestelde vragen over Azure cache voor redis-ontwikkeling
description: Meer informatie over de antwoorden op veelgestelde vragen die u helpen bij het ontwikkelen voor Azure cache voor redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: bafd8a9752d2587ec52fe586e442e3bfc86d7537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585765"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Veelgestelde vragen over Azure cache voor redis-ontwikkeling

In dit artikel vindt u antwoorden op veelgestelde vragen over het ontwikkelen van Azure-cache voor redis.

## <a name="common-questions-and-answers"></a>Veelgestelde vragen en antwoorden
In deze sectie worden de volgende veelgestelde vragen behandeld:

* [Hoe kan ik aan de slag met Azure cache voor redis?](#how-can-i-get-started-with-azure-cache-for-redis)
* [Wat doen de configuratie opties voor stack Exchange. redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Welke Azure-cache voor redis-clients kan ik gebruiken?](#what-azure-cache-for-redis-clients-can-i-use)
* [Is er een lokale emulator voor Azure cache voor redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hoe kan ik redis-opdrachten uitvoeren?](#how-can-i-run-redis-commands)
* [Waarom is Azure cache voor redis geen referentie voor een MSDN Class-bibliotheek?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Kan ik Azure cache gebruiken voor redis als een PHP-sessie cache?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Wat zijn redis-data bases?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hoe kan ik aan de slag met Azure cache voor redis?
Er zijn verschillende manieren om aan de slag te gaan met Azure cache voor redis.

* U kunt een van de zelf studies bekijken die beschikbaar zijn voor [.net](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.net](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)en [python](cache-python-get-started.md).
* U kunt bekijken [hoe u High-Performance-apps bouwt met Microsoft Azure cache voor redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* U kunt de client documentatie raadplegen voor de clients die overeenkomen met de ontwikkelings taal van uw project om te zien hoe u redis kunt gebruiken. Er zijn veel redis-clients die kunnen worden gebruikt met Azure cache voor redis. Zie voor een lijst met redis-clients [https://redis.io/clients](https://redis.io/clients) .

Als u nog geen Azure-account hebt, kunt u het volgende doen:

* [Gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). U ontvangt tegoed dat kan worden gebruikt om betaalde Azure-services te proberen. Zelfs nadat het tegoed is gebruikt, kunt u het account houden en de gratis Azure-services en -functies gebruiken.
* [Uw voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Via uw MSDN-abonnement ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Wat doen de configuratie opties voor stack Exchange. redis?
Stack Exchange. redis heeft veel opties. In deze sectie vindt u enkele van de algemene instellingen. Zie [stack Exchange. redis-configuratie](https://stackexchange.github.io/StackExchange.Redis/Configuration)voor meer gedetailleerde informatie over de opties voor stack Exchange. redis.

| ConfigurationOptions | Beschrijving | Aanbeveling |
| --- | --- | --- |
| AbortOnConnectFail |Als deze eigenschap is ingesteld op True, wordt er geen verbinding gemaakt na een netwerk fout. |Stel deze waarde in op False en laat stack Exchange. redis automatisch opnieuw verbinding maken. |
| ConnectRetry |Het aantal keren dat verbindings pogingen moeten worden herhaald tijdens de eerste verbinding. |Raadpleeg de volgende opmerkingen voor hulp. |
| ConnectTimeout |Time-out in MS voor verbindings bewerkingen. |Raadpleeg de volgende opmerkingen voor hulp. |

Meestal zijn de standaard waarden van de client voldoende. U kunt de opties aanpassen op basis van uw werk belasting.

* **Nieuwe pogingen**
  * De algemene richt lijnen voor ConnectRetry en ConnectTimeout zijn snel en opnieuw proberen. Deze richt lijnen zijn gebaseerd op uw werk belasting en hoe lang het duurt voordat uw client een redis-opdracht verzendt en een antwoord ontvangt.
  * Laat stack Exchange. redis automatisch opnieuw verbinding maken in plaats van de verbindings status te controleren en zelf verbinding te maken. **Vermijd het gebruik van de eigenschap ConnectionMultiplexer. IsConnected**.
  * Snowballing: soms is het mogelijk dat u een probleem ondervindt waarbij u het opnieuw probeert en de nieuwe pogingen Snowball en nooit herstellen. Als snowballing zich voordoet, kunt u het beste een exponentiële uitstel-algoritme gebruiken, zoals beschreven in [algemene richt lijnen voor opnieuw proberen](/azure/architecture/best-practices/transient-faults) die worden gepubliceerd door de micro soft-patronen & practices-groep.
  
* **Time-outwaarden**
  * Denk aan uw werk belasting en stel de waarden dienovereenkomstig in. Als u grote waarden opslaat, stelt u de time-out in op een hogere waarde.
  * Stel deze waarde `AbortOnConnectFail` in op False en laat stack Exchange. redis opnieuw verbinding maken.
  * Gebruik één ConnectionMultiplexer-exemplaar voor de toepassing. U kunt een LazyConnection gebruiken om één exemplaar te maken dat wordt geretourneerd door een eigenschap Connection, zoals wordt weer gegeven in [verbinding maken met de cache met behulp van de klasse ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Stel de `ConnectionMultiplexer.ClientName` eigenschap in op een unieke naam voor het app-exemplaar voor diagnostische doel einden.
  * Gebruik meerdere `ConnectionMultiplexer` instanties voor aangepaste werk belastingen.
    * U kunt dit model volgen als uw toepassing variërend wordt geladen. Bijvoorbeeld:
    * U kunt één multiplexer voor het omgaan met grote sleutels.
    * U kunt één multiplexer voor het omgaan met kleine sleutels hebben.
    * U kunt verschillende waarden instellen voor verbindingstime-outs en pogings logica voor elke ConnectionMultiplexer die u gebruikt.
    * Stel de `ClientName` eigenschap in op elke multiplexer om te helpen met diagnostische gegevens.
    * Deze richt lijnen kunnen leiden tot meer gestroomlijnde latentie per `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Welke Azure-cache voor redis-clients kan ik gebruiken?
Een van de fantastische dingen over redis is dat er veel clients zijn die veel verschillende ontwikkelings talen ondersteunen. Zie [redis-clients](https://redis.io/clients)voor een actuele lijst met clients. Zie [Azure cache gebruiken voor redis](cache-dotnet-how-to-use-azure-redis-cache.md) en de artikelen in de inhouds opgave voor meer zelf studies over verschillende talen en clients.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Is er een lokale emulator voor Azure cache voor redis?
Er is geen lokale emulator voor Azure cache voor redis, maar u kunt de MSOpenTech-versie van redis-server.exe uitvoeren vanaf de [opdracht regel Programma's redis](https://github.com/MSOpenTech/redis/releases/) op uw lokale computer en er verbinding mee maken om een vergelijk bare ervaring te krijgen met een lokale cache-emulator, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

U kunt eventueel een bestand [redis. conf](https://redis.io/topics/config) configureren dat meer nauw keurig overeenkomt met de [standaard cache-instellingen](cache-configure.md#default-redis-server-configuration) voor uw online Azure-cache voor redis, indien gewenst.

### <a name="how-can-i-run-redis-commands"></a>Hoe kan ik redis-opdrachten uitvoeren?
U kunt elk van de opdrachten die worden weer gegeven op [redis opdrachten](https://redis.io/commands#) gebruiken, met uitzonde ring van de opdrachten die worden weer gegeven op [redis-opdrachten die niet worden ondersteund in azure cache voor redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). U hebt verschillende opties om redis-opdrachten uit te voeren.

* Als u een Standard-of Premium-cache hebt, kunt u redis-opdrachten uitvoeren met behulp van de [redis-console](cache-configure.md#redis-console). De redis-console biedt een veilige manier om redis-opdrachten uit te voeren in de Azure Portal.
* U kunt ook de redis-opdracht regel Programma's gebruiken. Als u deze wilt gebruiken, voert u de volgende stappen uit:
* Down load de [redis-opdracht regel Programma's](https://github.com/MSOpenTech/redis/releases/).
* Verbinding maken met de cache met behulp van `redis-cli.exe` . Pass het cache-eind punt met behulp van de-h-switch en de sleutel met-a zoals wordt weer gegeven in het volgende voor beeld:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> De opdracht regel Programma's voor redis werken niet met de TLS-poort, maar u kunt een hulp programma gebruiken `stunnel` om de hulpprogram ma's veilig te verbinden met de TLS-poort door de instructies te volgen in het [opdracht regel programma redis gebruiken met Azure cache for redis](./cache-how-to-redis-cli-tool.md) .
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Waarom is Azure cache voor redis geen referentie voor een MSDN Class-bibliotheek?
Microsoft Azure cache voor redis is gebaseerd op de populaire open-source gegevens opslag in het geheugen, redis. Het kan worden gebruikt door een groot aantal [redis-clients](https://redis.io/clients) voor veel programmeer talen. Elke client heeft zijn eigen API die aanroepen naar de Azure-cache voor redis-exemplaar met behulp van [redis-opdrachten](https://redis.io/commands).

Omdat elke client verschilt, is er niet één gecentraliseerde klassen verwijzing op MSDN en houdt elke client zijn eigen referentie documentatie bij. Naast de referentie documentatie zijn er verschillende zelf studies die laten zien hoe u aan de slag gaat met Azure cache voor redis met behulp van verschillende talen en cache-clients. Voor toegang tot deze zelf studies raadpleegt u [Azure cache gebruiken voor redis](cache-dotnet-how-to-use-azure-redis-cache.md) en de artikelen in de inhouds opgave op hetzelfde niveau.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Kan ik Azure cache gebruiken voor redis als een PHP-sessie cache?
Ja, als u Azure cache voor redis wilt gebruiken als een PHP-sessie cache, geeft u de connection string op voor uw Azure-cache voor redis-instantie in `session.save_path` .

> [!IMPORTANT]
> Wanneer u Azure cache gebruikt voor redis als een PHP-sessie cache, moet u de URL coderen die wordt gebruikt om verbinding te maken met de cache, zoals wordt weer gegeven in het volgende voor beeld:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Als de sleutel geen URL-code ring heeft, ontvangt u mogelijk een uitzonde ring met een bericht zoals: `Failed to parse session.save_path`
>

Zie [php session handler](https://github.com/phpredis/phpredis#php-session-handler)voor meer informatie over het gebruik van Azure cache voor redis als een PHP-sessie cache met de PhpRedis-client.

### <a name="what-are-redis-databases"></a>Wat zijn redis-data bases?

Redis-data bases vormen alleen een logische schei ding van gegevens binnen hetzelfde redis-exemplaar. Het cache geheugen wordt gedeeld tussen alle data bases en het werkelijke geheugen gebruik van een bepaalde data base is afhankelijk van de sleutels/waarden die zijn opgeslagen in die data base. Een C6-cache heeft bijvoorbeeld 53 GB geheugen en een P5 heeft 120 GB. U kunt ervoor kiezen om alle 53 GB/120 GB in één Data Base te plaatsen of u kunt deze verdelen over meerdere data bases. 

> [!NOTE]
> Wanneer u een Premium Azure-cache gebruikt voor redis waarbij Clustering is ingeschakeld, is alleen data base 0 beschikbaar. Deze beperking is een intrinsieke redis-beperking en is niet specifiek voor Azure-cache voor redis. Zie voor meer informatie [moet ik wijzigingen aanbrengen in mijn client toepassing voor het gebruik van clusteren?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Veelgestelde vragen over andere Azure-caches voor redis](cache-faq.md).