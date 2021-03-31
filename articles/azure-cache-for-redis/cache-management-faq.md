---
title: Veelgestelde vragen over Azure cache voor redis Management
description: Meer informatie over de antwoorden op veelgestelde vragen die u helpen bij het beheren van Azure cache voor redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537657"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Veelgestelde vragen over Azure cache voor redis Management
In dit artikel vindt u antwoorden op veelgestelde vragen over het beheren van Azure cache voor redis.

## <a name="common-questions-and-answers"></a>Veelgestelde vragen en antwoorden
In deze sectie worden de volgende veelgestelde vragen behandeld:

* [Wanneer moet ik de niet-TLS/SSL-poort inschakelen om verbinding te maken met redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Wat zijn de aanbevolen procedures voor productie?](#what-are-some-production-best-practices)
* [Wat zijn enkele aandachtspunten bij het gebruik van algemene redis-opdrachten?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hoe kan ik Bench Mark en test de prestaties van mijn cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Belang rijke informatie over de groei van de thread pool](#important-details-about-threadpool-growth)
* [Schakel Server GC in om meer door voer te krijgen op de client wanneer stack Exchange. redis wordt gebruikt](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestatie overwegingen rond verbindingen](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Wanneer moet ik de niet-TLS/SSL-poort inschakelen om verbinding te maken met redis?
Redis server biedt geen systeem eigen ondersteuning voor TLS, maar Azure cache voor redis wel. Als u verbinding maakt met Azure cache voor redis en uw client TLS ondersteunt, zoals stack Exchange. redis, moet u TLS gebruiken.

>[!NOTE]
>De niet-TLS-poort is standaard uitgeschakeld voor nieuwe Azure-cache voor redis-exemplaren. Als uw client geen TLS ondersteunt, moet u de niet-TLS-poort inschakelen door de instructies in de sectie [toegangs poorten](cache-configure.md#access-ports) van het artikel [een cache in azure-cache configureren voor redis](cache-configure.md) te volgen.
>
>

Redis-hulpprogram ma's zoals `redis-cli` niet werken met de TLS-poort, maar u kunt een hulp programma gebruiken `stunnel` om de hulpprogram ma's veilig te verbinden met de TLS-poort door de instructies in de aankondiging van de [ASP.net-sessie status provider voor redis preview release](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) -blog post te volgen.

Voor instructies over het downloaden van de redis-hulpprogram ma's, zie de sectie [Hoe kan ik redis-opdrachten uitvoeren?](cache-development-faq.md#how-can-i-run-redis-commands)

### <a name="what-are-some-production-best-practices"></a>Wat zijn de aanbevolen procedures voor productie?
* [Best practices voor stack Exchange. redis](#stackexchangeredis-best-practices)
* [Configuratie en concepten](#configuration-and-concepts)
* [Prestaties testen](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Best practices voor stack Exchange. redis
* Stel `AbortConnect` deze waarde in op False en laat de ConnectionMultiplexer automatisch opnieuw verbinding maken. [Zie hier voor meer informatie](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* De ConnectionMultiplexer opnieuw gebruiken: Maak geen nieuwe voor elke aanvraag. Het `Lazy<ConnectionMultiplexer>` patroon dat [hier wordt weer gegeven](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) , wordt aanbevolen.
* Redis werkt het beste met kleinere waarden. u kunt dus grotere gegevens in meerdere sleutels afzetten. In [deze redis-discussie](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)wordt 100 kB als groot beschouwd. Lees [dit artikel](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) voor een voor beeld van een probleem dat kan worden veroorzaakt door grote waarden.
* Configureer de [instellingen van uw thread pool](#important-details-about-threadpool-growth) om time-outs te voor komen.
* Gebruik ten minste de standaard connectTimeout van 5 seconden. Dit interval geeft stack Exchange. redis voldoende tijd om de verbinding te herstellen in het geval van een netwerk Blip.
* Houd rekening met de prestatie kosten die zijn gekoppeld aan de verschillende bewerkingen die u uitvoert. De `KEYS` opdracht is bijvoorbeeld een O (n) bewerking en moet worden vermeden. De [redis.io-site](https://redis.io/commands/) bevat details over de tijd complexiteit voor elke bewerking die wordt ondersteund. Klik op elke opdracht om de complexiteit van elke bewerking weer te geven.

#### <a name="configuration-and-concepts"></a>Configuratie en concepten
* Gebruik de standaard-of Premium-laag voor productie systemen. De laag Basic is een systeem met één knooppunt zonder gegevensreplicatie en zonder SLA. Gebruik minimaal een C1-cache. C0-caches worden doorgaans gebruikt voor eenvoudige ontwikkel-en test scenario's.
* Houd er rekening mee dat redis een **in-Memory-** gegevens archief is. Lees [dit artikel](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) zodat u op de hoogte bent van scenario's waarin gegevens verlies zich kan voordoen.
* Ontwikkel uw systeem zodat het verbinding problemen kan verwerken [vanwege patches en failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestaties testen
* Begin met `redis-benchmark.exe` om een mogelijke door voer te krijgen voordat u uw eigen prestatie tests schrijft. Omdat `redis-benchmark` TLS niet wordt ondersteund, moet u [de niet-TLS-poort inschakelen via de Azure Portal](cache-configure.md#access-ports) voordat u de test uitvoert. Zie [Hoe kan ik Bench Mark en de prestaties van mijn cache testen](#how-can-i-benchmark-and-test-the-performance-of-my-cache) voor voor beelden.
* De client-VM die wordt gebruikt voor het testen moet zich in dezelfde regio bevinden als uw Azure-cache voor redis-exemplaar.
* We raden u aan om een dv2-VM-reeks te gebruiken voor uw client, omdat deze betere hardware heeft en de beste resultaten moeten bieden.
* Zorg ervoor dat uw client-VM die u kiest, ten minste zoveel computer-en bandbreedte capaciteit heeft als de cache die u wilt testen.
* Schakel VRSS in op de client computer als u zich in Windows bevindt. [Zie hier voor meer informatie](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* Redis-exemplaren van de Premium-laag hebben een betere netwerk latentie en door Voer omdat ze worden uitgevoerd op betere hardware voor zowel CPU als netwerk.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Wat zijn enkele aandachtspunten bij het gebruik van algemene redis-opdrachten?

* Vermijd het gebruik van bepaalde redis-opdrachten die veel tijd in beslag nemen, tenzij u de impact van deze opdrachten volledig begrijpt. Voer bijvoorbeeld niet de opdracht [sleutels](https://redis.io/commands/keys) in productie uit. Afhankelijk van het aantal sleutels kan het lang duren om te retour neren. Redis is een server met één thread en verwerkt opdrachten een voor een. Als u andere opdrachten hebt uitgegeven na sleutels, worden ze niet verwerkt totdat redis de opdracht sleutels verwerkt. De [redis.io-site](https://redis.io/commands/) bevat details over de tijd complexiteit voor elke bewerking die wordt ondersteund. Klik op elke opdracht om de complexiteit van elke bewerking weer te geven.
* Sleutel grootten: moet ik kleine sleutel/waarden of grote sleutel/waarden gebruiken? Dit is afhankelijk van het scenario. Als voor uw scenario grotere sleutels zijn vereist, kunt u de ConnectionTimeout aanpassen, vervolgens waarden opnieuw proberen en de logica voor opnieuw proberen aan te passen. In een redis-server perspectief bieden kleinere waarden betere prestaties.
* Dit betekent niet dat u geen grotere waarden in redis kunt opslaan. u moet rekening houden met de volgende overwegingen. De latentie is hoger. Als u een set gegevens hebt die groter is en een kleiner aantal, kunt u meerdere ConnectionMultiplexer-instanties gebruiken, die allemaal zijn geconfigureerd met een andere set time-out-en nieuwe waarden, zoals wordt beschreven in de vorige sectie [de configuratie opties voor de stack Exchange. redis](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) .

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hoe kan ik Bench Mark en test de prestaties van mijn cache?
* [Schakel de diagnostische gegevens van de cache in](cache-how-to-monitor.md#enable-cache-diagnostics), zodat u de status van de cache kunt [bewaken](cache-how-to-monitor.md). U kunt de metrische gegevens weer geven in de Azure Portal en u kunt ze ook [downloaden en bekijken](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) met de hulpprogram ma's van uw keuze.
* U kunt redis-benchmark.exe gebruiken om test uw redis-server te laden.
* Zorg ervoor dat de belasting test-client en de Azure-cache voor redis zich in dezelfde regio bevinden.
* Gebruik redis-cli.exe en bewaak de cache met behulp van de opdracht INFO.
* Als uw belasting een hoge geheugen fragmentatie veroorzaakt, moet u omhoog schalen naar een grotere cache grootte.
* Voor instructies over het downloaden van de redis-hulpprogram ma's, zie de sectie [Hoe kan ik redis-opdrachten uitvoeren?](cache-development-faq.md#how-can-i-run-redis-commands)

De volgende opdrachten bieden een voor beeld van het gebruik van redis-benchmark.exe. Voor nauw keurige resultaten voert u deze opdrachten uit vanaf een virtuele machine in dezelfde regio als uw cache.

* SET-aanvragen met pipeline testen met behulp van een nettolading van 1 KB

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test aanvragen voor het ophalen van pijp lijnen met een payload van 1 KB.

>[!NOTE]
> Voer eerst de SET-test hierboven uit om de cache te vullen
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Belang rijke informatie over de groei van de thread pool
De CLR-thread pool heeft twee soorten threads: "worker" en "I/O-voltooiings poort" (IOCP).

* Worker-threads worden gebruikt voor zaken zoals het verwerken van de `Task.Run(…)` -of- `ThreadPool.QueueUserWorkItem(…)` methoden. Deze threads worden ook gebruikt door verschillende onderdelen in de CLR wanneer het werk moet plaatsvinden op een achtergrond thread.
* IOCP-threads worden gebruikt wanneer asynchrone IO plaatsvindt, bijvoorbeeld bij het lezen van het netwerk.

De thread pool biedt nieuwe werkthreads of I/O-voltooiings threads op aanvraag (zonder enige beperking) totdat de instelling ' minimum ' voor elk type thread wordt bereikt. Standaard is het minimum aantal threads ingesteld op het aantal processors op een systeem.

Zodra het aantal bestaande (bezette) threads het ' minimale ' aantal threads aankomt, wordt door de thread pool de snelheid beperkt waarmee nieuwe threads worden geinjecteerd op één thread per 500 milliseconden. Als uw systeem bijvoorbeeld een burst van een IOCP-thread krijgt, wordt dat proces snel uitgevoerd. Als de burst van work echter meer is dan de geconfigureerde minimum instelling, zal er enige vertraging optreden bij het verwerken van een deel van het werk wanneer de thread pool wacht om een van twee dingen te doen.

* Een bestaande thread wordt gratis om het werk te verwerken.
* Er wordt geen bestaande thread gratis voor 500 MS, dus er wordt een nieuwe thread gemaakt.

In principe betekent dit dat wanneer het aantal bezette threads groter is dan de minimale threads, u waarschijnlijk een vertraging van 500 MS betaalt voordat het netwerk verkeer door de toepassing wordt verwerkt. Het is ook belang rijk te weten dat wanneer een bestaande thread langer dan 15 seconden inactief blijft (op basis van wat ik weet), deze wordt opgeschoond en deze groei en krimping kan worden herhaald.

Als we een voor beeld van een fout bericht van stack Exchange. redis (build 1.0.450 of hoger) bekijken, ziet u dat er nu thread pool-statistieken worden afgedrukt (Zie de details van IOCP en WORKer hieronder).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

In het vorige voor beeld ziet u dat er voor IOCP-thread zes bezette threads zijn en het systeem zo is geconfigureerd dat vier minimale threads zijn toegestaan. In dit geval zou de client waarschijnlijk 2 500-MS vertragingen hebben gezien, omdat 6 > 4.

Houd er rekening mee dat stack Exchange. redis time-outs kan opraken als de groei van IOCP-of WORKer-threads wordt beperkt.

#### <a name="recommendation"></a>Aanbeveling

Op basis van deze informatie raden we u ten zeerste aan dat klanten de minimale configuratie waarde voor IOCP-en WORKer-threads instellen op iets groter dan de standaard waarde. We bieden geen ondersteuning voor het formaat van één grootte, zoals de juiste waarde voor de ene toepassing waarschijnlijk te hoog of laag is voor een andere toepassing. Deze instelling kan ook van invloed zijn op de prestaties van andere onderdelen van gecompliceerde toepassingen, zodat elke klant deze instelling moet aanpassen aan hun specifieke behoeften. Een goede start locatie is 200 of 300 en vervolgens naar behoefte testen en verfijnen.

Deze instelling configureren:

* U wordt aangeraden deze instelling programmatisch te wijzigen met behulp van de methode [thread pool. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) in `global.asax.cs` . Bijvoorbeeld:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > De waarde die wordt opgegeven met deze methode is een algemene instelling die van invloed is op het hele AppDomain. Als u bijvoorbeeld een computer met vier kernen hebt en *minWorkerThreads* en *minIoThreads* wilt instellen op 50 per CPU tijdens runtime, gebruikt u **thread pool. SetMinThreads (200, 200)**.

* Het is ook mogelijk om de instelling minimale threads op te geven met behulp van de [configuratie-instelling *MinIoThreads* of *minWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) onder het `<processModel>` configuratie-element in `Machine.config` , meestal bevindt zich op `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **Het instellen van het aantal minimale threads op deze manier wordt doorgaans niet aanbevolen, omdat het een instelling voor het hele systeem is.**

  > [!NOTE]
  > De waarde die is opgegeven in dit configuratie-element is een instelling *per kern* . Als u bijvoorbeeld een computer met vier kernen hebt en u wilt dat uw *minIoThreads* -instelling 200 tijdens runtime, gebruikt u `<processModel minIoThreads="50"/>` .
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Schakel Server GC in om meer door voer te krijgen op de client wanneer stack Exchange. redis wordt gebruikt
Als server GC wordt ingeschakeld, kan de client worden geoptimaliseerd en kunnen de prestaties en door voer worden verbeterd wanneer stack Exchange. redis wordt gebruikt. Raadpleeg de volgende artikelen voor meer informatie over server GC en hoe u deze kunt inschakelen:

* [Server GC inschakelen](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Basis principes van garbagecollection](/dotnet/standard/garbage-collection/fundamentals)
* [Garbagecollection en prestaties](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Prestatie overwegingen rond verbindingen

Elke prijs categorie heeft verschillende limieten voor client verbindingen, geheugen en band breedte. Hoewel elke grootte van de cache een bepaald aantal *verbindingen toestaat,* is er aan elke verbinding met redis overhead gekoppeld. Een voor beeld van een dergelijke overhead is het CPU-en geheugen gebruik als gevolg van TLS/SSL-versleuteling. Voor de maximum verbindings limiet voor een bepaalde cache grootte wordt uitgegaan van een licht geladen cache. Als de belasting van de verbindings overhead *plus* de belasting van client bewerkingen de capaciteit voor het systeem overschrijdt, kan de cache capaciteits problemen ondervinden, zelfs als u de verbindings limiet voor de huidige cache grootte niet overschrijdt.

Zie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/)voor meer informatie over de verschillende verbindings limieten voor elke laag. Zie [standaard redis-server configuratie](cache-configure.md#default-redis-server-configuration)voor meer informatie over verbindingen en andere standaard configuraties.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Veelgestelde vragen over andere Azure-caches voor redis](cache-faq.md).