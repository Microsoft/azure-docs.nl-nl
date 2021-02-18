---
title: Problemen vaststellen en oplossen Azure Cosmos DB Java SDK v4
description: Gebruik functies als logboek registratie aan client zijde en andere hulpprogram ma's van derden voor het identificeren, vaststellen en oplossen van problemen met Azure Cosmos DB in Java SDK v4.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: cba8b97adb40ca2c277268188ff6ad541c7e9676
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596472"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Problemen oplossen met Azure Cosmos DB Java SDK v4 met SQL API-accounts
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> In dit artikel wordt alleen beschreven hoe u problemen oplost voor Azure Cosmos DB Java SDK v4. Raadpleeg de Azure Cosmos DB Java SDK v4- [opmerkingen](sql-api-sdk-java-v4.md), [maven opslag plaats](https://mvnrepository.com/artifact/com.azure/azure-cosmos)en [Tips voor betere prestaties](performance-tips-java-sdk-v4-sql.md) voor meer informatie. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.
>

Dit artikel heeft betrekking op veelvoorkomende problemen, tijdelijke oplossingen, diagnostische stappen en hulpprogram ma's voor het gebruik van Azure Cosmos DB Java SDK v4 met Azure Cosmos DB SQL API-accounts.
Azure Cosmos DB Java SDK v4 biedt client-side logische weer gave om toegang te krijgen tot de Azure Cosmos DB SQL-API. In dit artikel worden tools en benaderingen beschreven om u te helpen bij eventuele problemen.

Begin met deze lijst:

* Bekijk de sectie [veelvoorkomende problemen en tijdelijke oplossingen] in dit artikel.
* Bekijk de Java-SDK in de Azure Cosmos DB Central opslag plaats, die beschikbaar is [open source op github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). Er is een [sectie met problemen](https://github.com/Azure/azure-sdk-for-java/issues) die actief worden bewaakt. Controleer of er een soortgelijk probleem met een tijdelijke oplossing al is ingediend. Een handige tip is het filteren van problemen door de tag *Cosmos: v4-item* .
* Bekijk de [Tips voor prestaties](performance-tips-java-sdk-v4-sql.md) voor Azure Cosmos DB Java SDK v4 en volg de aanbevolen procedures.
* Lees de rest van dit artikel als u geen oplossing hebt gevonden. Bestand vervolgens een [github-probleem](https://github.com/Azure/azure-sdk-for-java/issues). Als er een optie is om tags toe te voegen aan uw GitHub-probleem, voegt u een tag *Cosmos: v4-item* toe.

### <a name="retry-logic"></a>Logica voor opnieuw proberen <a id="retry-logics"></a>
Cosmos DB SDK bij een IO-fout probeert de mislukte bewerking opnieuw uit te voeren als opnieuw proberen in de SDK kans van slagen heeft. Als er een nieuwe poging wordt gedaan om fouten te voor komen, is het een goed idee. Het is raadzaam om de nieuwste SDK te gebruiken als de logica voor nieuwe pogingen voortdurend wordt verbeterd.

1. Bij lees-en query-i/o-fouten wordt opnieuw geprobeerd door de SDK zonder de halen aan de eind gebruiker.
2. Schrijf bewerkingen (maken, Upsert, vervangen, verwijderen) zijn ' not ' idempotent en daarom kan SDK niet altijd een blinde schrijf bewerking uitvoeren. De toepassings logica van de gebruiker is vereist voor het afhandelen van de fout en het opnieuw proberen.
3. [Problemen met SDK-Beschik baarheid](troubleshoot-sdk-availability.md) legt nieuwe pogingen voor Cosmos DB accounts met meerdere regio's uit.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Veelvoorkomende problemen en tijdelijke oplossingen

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Netwerk problemen, time-out bij lezen van Netty, lage door Voer, hoge latentie

#### <a name="general-suggestions"></a>Algemene suggesties
Voor de beste prestaties:
* Zorg ervoor dat de app wordt uitgevoerd in dezelfde regio als uw Azure Cosmos DB-account. 
* Controleer het CPU-gebruik op de host waarop de app wordt uitgevoerd. Als het CPU-gebruik 50 procent of meer is, voert u uw app uit op een host met een hogere configuratie. U kunt ook de belasting op meer machines distribueren.
    * Als u uw toepassing uitvoert op de Azure Kubernetes-service, kunt u [Azure monitor gebruiken om het CPU-gebruik te bewaken](../azure-monitor/containers/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Verbindings beperking
De verbindings beperking kan worden veroorzaakt door een [verbindings limiet op een hostcomputer of een overschrijding] van een [Azure SNAT-poort].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Verbindings limiet op een hostcomputer
Sommige Linux-systemen, zoals Red Hat, hebben een bovenlimiet van het totale aantal geopende bestanden. Sockets in Linux worden geïmplementeerd als bestanden, zodat dit aantal ook het totale aantal verbindingen beperkt.
Voer de volgende opdracht uit.

```bash
ulimit -a
```
Het maximum aantal geopende bestanden dat wordt aangeduid als ' geen bestand ' moet mini maal gelijk zijn aan de grootte van de verbindings groep. Zie de Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md)voor meer informatie.

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

De SDK gebruikt de [Netty](https://netty.io/) io-bibliotheek om te communiceren met Azure Cosmos db. De SDK heeft een async API en maakt gebruik van niet-blokkerende IO-Api's van Netty. Het IO-werk van de SDK wordt uitgevoerd op IO Netty-threads. Het aantal i/o-Netty-threads is geconfigureerd om hetzelfde te zijn als het aantal CPU-kernen van de app-computer. 

De Netty-IO-threads zijn alleen bedoeld voor gebruik voor niet-blokkerende Netty IO-werk. De SDK retourneert het aanroep resultaat van de API op een van de Netty IO-threads naar de code van de app. Als de app een langdurige bewerking uitvoert nadat deze resultaten heeft ontvangen op de Netty-thread, heeft de SDK mogelijk onvoldoende IO-threads om het interne IO-werk te kunnen uitvoeren. Deze app-code ring kan leiden tot een lage door Voer, hoge latentie en `io.netty.handler.timeout.ReadTimeoutException` fouten. De tijdelijke oplossing is om de thread over te scha kelen wanneer u weet dat de bewerking tijd kost.

Bekijk bijvoorbeeld het volgende code fragment waarmee items worden toegevoegd aan een container ( [hier](create-sql-api-java.md) vindt u informatie over het instellen van de data base en de container.) U kunt langdurige werkzaamheden uitvoeren die meer dan een paar milliseconden in de Netty-thread duren. Als dat het geval is, kunt u uiteindelijk een status krijgen waarin geen Netty IO-thread aanwezig is om IO-werk te verwerken. Als gevolg hiervan krijgt u een ReadTimeoutException-fout.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

De tijdelijke oplossing is om de thread te wijzigen waarop u werk uitvoert die tijd kost. Definieer een singleton-exemplaar van de Scheduler voor uw app.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Mogelijk moet u werk doen die tijd kost, bijvoorbeeld reken kundige, zware werk of het blok keren van IO. In dit geval kunt u de thread overschakelen naar een werk nemer die is voorzien van `customScheduler` de `.publishOn(customScheduler)` API.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

Met `publishOn(customScheduler)` kunt u de NETTY io-thread vrijgeven en overschakelen naar uw eigen aangepaste thread van de aangepaste planner. Met deze wijziging wordt het probleem opgelost. Er wordt geen `io.netty.handler.timeout.ReadTimeoutException` fout meer weer geven.

### <a name="request-rate-too-large"></a>Aanvraag frequentie te groot
Deze fout wordt veroorzaakt door storingen aan de server zijde. Dit geeft aan dat u uw ingerichte door Voer hebt verbruikt. Probeer het later opnieuw. Als deze fout vaak optreedt, kunt u een toename van de door Voer van de verzameling overwegen.

* **Uitstel implementeren met getRetryAfterInMilliseconds-intervallen**

    Tijdens prestatie tests moet u de belasting verg Roten tot een klein aantal aanvragen wordt beperkt. Als deze beperking is ingesteld, moet de client toepassing uitstel voor het interval voor nieuwe pogingen van de server. Door de uitstel te respecteren, zorgt u ervoor dat u de minimale hoeveelheid tijd die wacht tussen nieuwe pogingen.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Kan geen verbinding maken met de Azure Cosmos DB-emulator

Het HTTPS-certificaat van de Azure Cosmos DB emulator is zelfondertekend. Als u de SDK wilt gebruiken met de emulator, importeert u het Emulator-certificaat naar een Java-TrustStore. Zie [Azure Cosmos DB-emulator certificaten exporteren](local-emulator-export-ssl-certificates.md)voor meer informatie.

### <a name="dependency-conflict-issues"></a>Problemen met afhankelijkheids conflicten

De Azure Cosmos DB Java SDK haalt een aantal afhankelijkheden op; in het algemeen is het zo dat als de structuur van de project afhankelijkheid een oudere versie bevat van een artefact waarvan Azure Cosmos DB Java SDK afhankelijk is. Dit kan leiden tot onverwachte fouten die worden gegenereerd wanneer u de toepassing uitvoert. Als u fouten opspoort waarom de toepassing onverwacht een uitzonde ring genereert, is het een goed idee om te controleren of uw afhankelijkheids structuur niet per ongeluk wordt opgehaald in een oudere versie van een of meer van de Azure Cosmos DB Java SDK-afhankelijkheden.

De tijdelijke oplossing voor dit probleem is om te bepalen welke van de Project afhankelijkheden in de oude versie worden geplaatst en de transitieve afhankelijkheid uit te sluiten van die oudere versie en Azure Cosmos DB Java SDK in staat te stellen de nieuwere versie te halen.

Voer de volgende opdracht uit voor uw project pom.xml bestand om te bepalen welke van de Project afhankelijkheden in een oudere versie van iets worden geplaatst waarvan Azure Cosmos DB Java SDK afhankelijk is:
```bash
mvn dependency:tree
```
Zie de hand leiding voor de [maven-afhankelijkheids structuur](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)voor meer informatie.

Zodra u weet welke afhankelijkheid van uw project afhankelijk is van een oudere versie, kunt u de afhankelijkheid van die lib in uw pom-bestand wijzigen en de transitieve afhankelijkheid uitsluiten, volgens het onderstaande voor beeld (waarbij wordt aangenomen dat de dependency *core* de verouderde afhankelijkheid is):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Zie de [hand leiding voor transitieve afhankelijkheden uitsluiten](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)voor meer informatie.


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Logboek registratie van client-SDK inschakelen

Azure Cosmos DB Java SDK v4 maakt gebruik van SLF4j als de logboek registratie-gevel die logboek registratie ondersteunt in populaire logboek registratie raamwerken, zoals Log4J en logback.

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

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
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

In Windows kunt u dezelfde opdracht uitvoeren met verschillende argument vlaggen:
```bash
netstat -abn
```

Filter het resultaat tot alleen verbindingen met het Azure Cosmos DB-eind punt.

Het aantal verbindingen met het Azure Cosmos DB-eind punt in de `ESTABLISHED` status kan niet groter zijn dan de geconfigureerde grootte van de verbindings groep.

Veel verbindingen met het Azure Cosmos DB-eind punt hebben mogelijk de `CLOSE_WAIT` status. Er kan meer dan 1.000 zijn. Een getal dat hoog betekent dat de verbindingen tot stand zijn gebracht en snel worden afgebroken. Dit kan problemen veroorzaken. Zie de sectie [veelvoorkomende problemen en tijdelijke oplossingen] voor meer informatie.

 <!--Anchors-->
[Veelvoorkomende problemen en tijdelijke oplossingen]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Verbindings limiet op een hostcomputer]: #connection-limit-on-host
[Uitputting van de poort van Azure SNAT (PAT)]: #snat
