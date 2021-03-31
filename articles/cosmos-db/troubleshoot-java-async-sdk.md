---
title: Diagnose en problemen oplossen Azure Cosmos DB async Java SDK v2
description: Gebruik functies als logboek registratie aan client zijde en andere hulpprogram ma's van derden voor het identificeren, vaststellen en oplossen van problemen met Azure Cosmos DB in async Java SDK v2.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: b39a74bd06f942cf21d201c8cef48bc6dfc57d46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548083"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Problemen oplossen met de Azure Cosmos DB async Java SDK v2 met SQL API-accounts
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Dit is *niet* de meest recente Java-SDK voor Azure Cosmos db. U moet een upgrade uitvoeren voor uw project naar [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) en vervolgens de Azure Cosmos DB Java SDK v4- [probleemoplossings gids](troubleshoot-java-sdk-v4-sql.md)lezen. Volg de instructies in de hand leiding [migratie naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) en [reactor versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) om een upgrade uit te voeren. 
>
> In dit artikel vindt u meer informatie over het oplossen van problemen met alleen Azure Cosmos DB asynchrone Java SDK v2. Zie de [opmerkingen](sql-api-sdk-async-java.md)bij de Azure Cosmos DB ASYNC Java SDK v2, [maven opslag plaats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) en [Tips](performance-tips-async-java.md) voor meer informatie.
>

Dit artikel heeft betrekking op veelvoorkomende problemen, tijdelijke oplossingen, diagnostische stappen en hulpprogram ma's voor het gebruik van de [Java ASYNC SDK](sql-api-sdk-async-java.md) met Azure Cosmos DB SQL API-accounts.
De Java Async SDK biedt logische representatie aan de clientzijde om toegang te krijgen tot de SQL-API van Azure Cosmos DB. In dit artikel worden tools en benaderingen beschreven om u te helpen bij eventuele problemen.

Begin met deze lijst:

* Bekijk de sectie [veelvoorkomende problemen en tijdelijke oplossingen] in dit artikel.
* Bekijk de SDK, de beschik bare [open source op github](https://github.com/Azure/azure-cosmosdb-java). Er is een [sectie met problemen](https://github.com/Azure/azure-cosmosdb-java/issues) die actief worden bewaakt. Controleer of er een soortgelijk probleem met een tijdelijke oplossing al is ingediend.
* Bekijk de [Tips voor prestaties](performance-tips-async-java.md)en volg de aanbevolen procedures.
* Lees de rest van dit artikel als u geen oplossing hebt gevonden. Bestand vervolgens een [github-probleem](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Veelvoorkomende problemen en tijdelijke oplossingen

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Netwerk problemen, time-out bij lezen van Netty, lage door Voer, hoge latentie

#### <a name="general-suggestions"></a>Algemene suggesties
* Zorg ervoor dat de app wordt uitgevoerd in dezelfde regio als uw Azure Cosmos DB-account. 
* Controleer het CPU-gebruik op de host waarop de app wordt uitgevoerd. Als het CPU-gebruik 90 procent of meer is, voert u uw app uit op een host met een hogere configuratie. U kunt ook de belasting op meer machines distribueren.

#### <a name="connection-throttling"></a>Verbindings beperking
De verbindings beperking kan worden veroorzaakt door een [verbindings limiet op een hostcomputer of een overschrijding] van een [Azure SNAT-poort].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Verbindings limiet op een hostcomputer
Sommige Linux-systemen, zoals Red Hat, hebben een bovenlimiet van het totale aantal geopende bestanden. Sockets in Linux worden geïmplementeerd als bestanden, zodat dit aantal ook het totale aantal verbindingen beperkt.
Voer de volgende opdracht uit.

```bash
ulimit -a
```
Het maximum aantal geopende bestanden dat wordt aangeduid als ' geen bestand ' moet mini maal gelijk zijn aan de grootte van de verbindings groep. Zie voor meer informatie [prestatie tips](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Uitputting van de poort van Azure SNAT (PAT)

Als uw app is geïmplementeerd op Azure Virtual Machines zonder een openbaar IP-adres, worden de standaard [Azure SNAT-poorten](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) gebruikt om verbinding te maken met een wille keurig eind punt buiten uw VM. Het aantal verbindingen dat van de virtuele machine naar het Azure Cosmos DB-eind punt is toegestaan, wordt beperkt door de [Azure SNAT-configuratie](../load-balancer/load-balancer-outbound-connections.md#preallocatedports).

 Azure SNAT-poorten worden alleen gebruikt als uw virtuele machine een privé-IP-adres heeft en een proces van de virtuele machine probeert verbinding te maken met een openbaar IP-adres. Er zijn twee oplossingen om Azure SNAT-beperking te voor komen:

* Voeg uw Azure Cosmos DB Service-eind punt toe aan het subnet van uw virtuele Azure Virtual Machines-netwerk. Zie [Azure Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie. 

    Wanneer het service-eind punt is ingeschakeld, worden de aanvragen niet langer verzonden vanuit een openbaar IP-adres naar Azure Cosmos DB. In plaats daarvan worden de identiteit van het virtuele netwerk en het subnet verzonden. Deze wijziging kan ertoe leiden dat de firewall wordt neergezet als alleen open bare IP-adressen zijn toegestaan. Als u een firewall gebruikt en u het service-eind punt inschakelt, voegt u een subnet toe aan de firewall door gebruik te maken van [Virtual Network acl's](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Wijs een openbaar IP-adres toe aan uw Azure-VM.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Kan de service niet bereiken-firewall
``ConnectTimeoutException`` geeft aan dat de SDK de service niet kan bereiken.
Wanneer u de directe modus gebruikt, treedt er een fout op die vergelijkbaar is met de volgende:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Als u een firewall hebt die wordt uitgevoerd op uw app-computer, opent u poort bereik 10.000 tot 20.000 die worden gebruikt door de directe modus.
Volg ook de [verbindings limiet op een hostmachine](#connection-limit-on-host).

#### <a name="http-proxy"></a>HTTP-proxy

Als u een HTTP-proxy gebruikt, moet u ervoor zorgen dat het het aantal verbindingen dat in de SDK is geconfigureerd kan ondersteunen `ConnectionPolicy` .
Anders worden er verbindings problemen beschreven.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Ongeldig coderings patroon: Netty IO-thread blok keren

De SDK gebruikt de [Netty](https://netty.io/) io-bibliotheek om te communiceren met Azure Cosmos db. De SDK heeft async-Api's en maakt gebruik van niet-blokkerende IO-Api's van Netty. Het IO-werk van de SDK wordt uitgevoerd op IO Netty-threads. Het aantal i/o-Netty-threads is geconfigureerd om hetzelfde te zijn als het aantal CPU-kernen van de app-computer. 

De Netty-IO-threads zijn alleen bedoeld voor gebruik voor niet-blokkerende Netty IO-werk. De SDK retourneert het aanroep resultaat van de API op een van de Netty IO-threads naar de code van de app. Als de app een langdurige bewerking uitvoert nadat deze resultaten heeft ontvangen op de Netty-thread, heeft de SDK mogelijk onvoldoende IO-threads om het interne IO-werk te kunnen uitvoeren. Deze app-code ring kan leiden tot een lage door Voer, hoge latentie en `io.netty.handler.timeout.ReadTimeoutException` fouten. De tijdelijke oplossing is om de thread over te scha kelen wanneer u weet dat de bewerking tijd kost.

Bekijk bijvoorbeeld het volgende code fragment. U kunt langdurige werkzaamheden uitvoeren die meer dan een paar milliseconden in de Netty-thread duren. Als dat het geval is, kunt u uiteindelijk een status krijgen waarin geen Netty IO-thread aanwezig is om IO-werk te verwerken. Als gevolg hiervan krijgt u een ReadTimeoutException-fout.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
De tijdelijke oplossing is om de thread te wijzigen waarop u werk uitvoert die tijd kost. Definieer een singleton-exemplaar van de Scheduler voor uw app.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Mogelijk moet u werk doen die tijd kost, bijvoorbeeld reken kundige, zware werk of het blok keren van IO. In dit geval kunt u de thread overschakelen naar een werk nemer die is voorzien van `customScheduler` de `.observeOn(customScheduler)` API.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Met `observeOn(customScheduler)` kunt u de NETTY io-thread vrijgeven en overschakelen naar uw eigen aangepaste thread van de aangepaste planner. Met deze wijziging wordt het probleem opgelost. Er wordt geen `io.netty.handler.timeout.ReadTimeoutException` fout meer weer geven.

### <a name="connection-pool-exhausted-issue"></a>Uitputtend probleem in verbindings groep

`PoolExhaustedException` is een fout aan de client zijde. Deze fout geeft aan dat uw app-werk belasting hoger is dan waarmee de SDK-verbindings groep kan dienen. Verhoog de grootte van de verbindings groep of distribueer de belasting op meerdere apps.

### <a name="request-rate-too-large"></a>Aanvraag frequentie te groot
Deze fout wordt veroorzaakt door storingen aan de server zijde. Dit geeft aan dat u uw ingerichte door Voer hebt verbruikt. Probeer het later opnieuw. Als deze fout vaak optreedt, kunt u een toename van de door Voer van de verzameling overwegen.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Kan geen verbinding maken met de Azure Cosmos DB-emulator

Het HTTPS-certificaat van de Azure Cosmos DB emulator is zelfondertekend. Als u de SDK wilt gebruiken met de emulator, importeert u het Emulator-certificaat naar een Java-TrustStore. Zie [Azure Cosmos DB-emulator certificaten exporteren](local-emulator-export-ssl-certificates.md)voor meer informatie.

### <a name="dependency-conflict-issues"></a>Problemen met afhankelijkheids conflicten

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

De bovenstaande uitzonde ring wordt voorgesteld door een afhankelijkheid van een oudere versie van RxJava lib (bijvoorbeeld 1.2.2). Onze SDK is afhankelijk van RxJava 1.3.8 met Api's die niet beschikbaar zijn in een eerdere versie van RxJava. 

De tijdelijke oplossing voor dergelijke problemen is het identificeren van de andere afhankelijkheid in RxJava-1.2.2 en de transitieve afhankelijkheid uit te sluiten op RxJava-1.2.2 en CosmosDB SDK de nieuwere versie te laten halen.

Als u wilt weten welke bibliotheek in RxJava-1.2.2 wordt geplaatst, voert u de volgende opdracht uit naast uw project pom.xml-bestand:
```bash
mvn dependency:tree
```
Zie de hand leiding voor de [maven-afhankelijkheids structuur](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)voor meer informatie.

Zodra u hebt vastgesteld dat RxJava-1.2.2 de transitieve afhankelijkheid is van de andere afhankelijkheid van uw project, kunt u de afhankelijkheid van die lib in uw pom-bestand wijzigen en RxJava transitieve afhankelijkheid uitsluiten:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Zie de [hand leiding voor transitieve afhankelijkheden uitsluiten](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)voor meer informatie.


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Logboek registratie van client-SDK inschakelen

De Java async SDK maakt gebruik van SLF4j als de logboek registratie-gevel die logboek registratie ondersteunt in populaire logboek registratie raamwerken, zoals Log4J en logback.

Als u bijvoorbeeld log4j als het Framework voor logboek registratie wilt gebruiken, voegt u de volgende bibliotheken in uw Java-klassenpad toe.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Voeg ook een log4j-configuratie toe.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Zie de [hand leiding voor sfl4j-logboek registratie](https://www.slf4j.org/manual.html)voor meer informatie.

## <a name="os-network-statistics"></a><a name="netstats"></a>OS-netwerk statistieken
Voer de opdracht netstat uit om een beeld te krijgen van het aantal verbindingen in Staten, zoals `ESTABLISHED` en `CLOSE_WAIT` .

Op Linux kunt u de volgende opdracht uitvoeren.
```bash
netstat -nap
```
Filter het resultaat tot alleen verbindingen met het Azure Cosmos DB-eind punt.

Het aantal verbindingen met het Azure Cosmos DB-eind punt in de `ESTABLISHED` status kan niet groter zijn dan de geconfigureerde grootte van de verbindings groep.

Veel verbindingen met het Azure Cosmos DB-eind punt hebben mogelijk de `CLOSE_WAIT` status. Er kan meer dan 1.000 zijn. Een getal dat hoog betekent dat de verbindingen tot stand zijn gebracht en snel worden afgebroken. Dit kan problemen veroorzaken. Zie de sectie [veelvoorkomende problemen en tijdelijke oplossingen] voor meer informatie.

 <!--Anchors-->
[Veelvoorkomende problemen en tijdelijke oplossingen]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Verbindings limiet op een hostcomputer]: #connection-limit-on-host
[Uitputting van de poort van Azure SNAT (PAT)]: #snat
