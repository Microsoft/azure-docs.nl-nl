---
title: Veelvoorkomende fouten in Azure Cosmos DB oplossen Cassandra-API
description: Dit document bevat informatie over de manieren om veelvoorkomende problemen op te lossen die optreden in Azure Cosmos DB Cassandra-API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658568"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Veelvoorkomende problemen in Azure Cosmos DB Cassandra-API oplossen
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra-API in Azure Cosmos DB is een compatibiliteits laag, die [ondersteuning](cassandra-support.md) biedt voor wire-protocol voor de populaire open-source Apache Cassandra-data base, en wordt aangestuurd door [Azure Cosmos DB](./introduction.md). Als volledig beheerde Cloud-eigen service biedt Azure Cosmos DB [garanties op het Beschik baarheid, door Voer en de consistentie](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) van Cassandra-API. Deze garanties zijn niet mogelijk in verouderde implementaties van Apache Cassandra. Cassandra-API vereenvoudigt ook de platform bewerkingen voor nul-onderhoud en zero-downtime-patches. Als zodanig zijn veel van de back-end-bewerkingen verschillen van Apache Cassandra, daarom raden we specifieke instellingen en benaderingen aan om veelvoorkomende fouten te voor komen. 

In dit artikel worden veelvoorkomende fouten en oplossingen beschreven voor toepassingen die Azure Cosmos DB Cassandra-API gebruiken. Als uw fout niet hieronder wordt weer gegeven en er een fout optreedt bij het uitvoeren van een [ondersteunde bewerking in Cassandra-API](cassandra-support.md), waarbij de fout *niet aanwezig is bij het gebruik van native Apache Cassandra*, moet u [een Azure-ondersteunings aanvraag maken](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Dit is een wrapper-uitzonde ring op het hoogste niveau met een groot aantal mogelijke oorzaken en interne uitzonde ringen, die veel aan de client gerelateerd kunnen zijn. 
### <a name="solution"></a>Oplossing
Enkele populaire oorzaken en oplossingen zijn als volgt: 
- Time-out voor inactiviteit van Azure LoadBalancers: dit kan ook als `ClosedConnectionException` . Om dit probleem op te lossen, stelt u de instelling Keep Alive in het stuur programma in (Zie [hieronder](#enable-keep-alive-for-java-driver)) en neemt u Keep-Alive-instellingen in het besturings systeem op of [past u time-out voor inactiviteit in azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal) 
- **Bron uitputting van client toepassing:** zorg ervoor dat de client computers voldoende bronnen hebben om de aanvraag te volt ooien. 

## <a name="cannot-connect-to-host"></a>Kan geen verbinding maken met host
Deze fout kan worden weer geven: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Oplossing
Dit kan leiden tot een SNAT-uitputting aan de client zijde. Volg de stappen op [SNAT voor uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md) om dit probleem op te lossen. Dit kan ook een probleem met een niet-actieve time-out zijn waarbij de Azure-load balancer standaard 4 minuten time-out voor inactiviteit heeft. Zie de documentatie in de [time-out voor inactiviteit van Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). Schakel TCP-keep in voor de instellingen van het stuur programma (Zie [hieronder](#enable-keep-alive-for-java-driver)) en stel het `keepAlive` interval van het besturings systeem in op minder dan 4 minuten.

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
Het totale aantal verbruikte aanvraag eenheden is hoger dan de aanvraag eenheden die zijn ingericht voor de code of tabel. Daarom worden de aanvragen beperkt.
### <a name="solution"></a>Oplossing
U kunt de door Voer die is toegewezen aan een spatie of tabel, schalen op basis van de Azure Portal (Zie [hier](manage-scale-cassandra.md) voor schaal bewerkingen in Cassandra-API) of als u een beleid voor opnieuw proberen wilt implementeren. Voor Java, zie voor beelden opnieuw proberen voor het stuur programma [v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) en het [stuur programma v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Zie ook [Azure Cosmos Cassandra-uitbrei dingen voor Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>OverloadedException zelfs met voldoende door Voer 
Het systeem lijkt te vereisen dat er aanvragen worden beperkt, ondanks dat er voldoende door Voer is ingericht voor aanvraag volume en/of verbruikte aanvraag eenheids kosten. Er zijn twee mogelijke oorzaken van een onverwachte frequentie beperking:
- **Bewerkingen op schema niveau:** Cassandra-API implementeert een systeem doorvoer budget voor bewerkingen op schema niveau (CREATE TABLE, ALTER TABLE, DROP TABLE). Dit budget moet voldoende zijn voor schema bewerkingen in een productie systeem. Als u echter een groot aantal bewerkingen op schema niveau hebt, is het mogelijk dat u deze limiet overschrijdt. Aangezien dit budget niet door de gebruiker wordt beheerd, moet u overwegen om het aantal uitgevoerde schema bewerkingen te verlagen. Als u met deze actie het probleem niet kunt verhelpen of als het niet haalbaar is voor uw workload, [maakt u een ondersteunings aanvraag voor Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Gegevens scheefheid:** wanneer de door Voer is ingericht in Cassandra-API, wordt het gelijkmatig verdeeld over fysieke partities en heeft elke fysieke partitie een bovengrens. Als er een grote hoeveelheid gegevens wordt ingevoegd of van een bepaalde partitie wordt opgehaald, is het mogelijk dat deze beperkt is, ondanks het inrichten van een grote hoeveelheid algemene door Voer (aanvraag eenheden) voor die tabel. Controleer uw gegevens model en zorg ervoor dat u geen buitensporige scheefheid hebt die mogelijk dynamische partities veroorzaakt. 

## <a name="intermittent-connectivity-errors-java"></a>Onregelmatige verbindings fouten (Java) 
De verbinding wordt onverwacht verbroken of geduurd.

### <a name="solution"></a>Oplossing 
De Apache Cassandra-Stuur Programma's voor java bieden twee systeem eigen beleid voor opnieuw verbinden: `ExponentialReconnectionPolicy` en `ConstantReconnectionPolicy` . De standaardwaarde is `ExponentialReconnectionPolicy`. Voor Azure Cosmos DB Cassandra-API wordt echter `ConstantReconnectionPolicy` een vertraging van 2 seconden aangeraden. Raadpleeg de [documentatie](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  over het stuur programma voor Java v4. x en [hier](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) voor Java 3. x-richt lijnen Zie ook [configureren van ReconnectionPolicy voor Java-stuur programma](#configuring-reconnectionpolicy-for-java-driver) -voor beelden hieronder.

## <a name="error-with-load-balancing-policy"></a>Fout met taakverdelings beleid

Als u een taakverdelings beleid hebt geïmplementeerd in v3. x van het Java Datastax-stuur programma, met de code zoals hieronder wordt beschreven:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Als de waarde voor `withLocalDc()` niet overeenkomt met het Data Center van het contact punt, treedt er een zeer terugkerende fout op: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Oplossing 
[CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) implementeren (mogelijk moet u een upgrade uitvoeren van de datastax-secundaire versie om deze te kunnen gebruiken):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Aantal mislukt in grote tabel
Wanneer `select count(*) from table` de server wordt uitgevoerd of vergelijkbaar is met een groot aantal rijen, wordt er een time-out opgelopen.

### <a name="solution"></a>Oplossing 
Als u een lokale CQLSH-client gebruikt, kunt u proberen om de `--connect-timeout` of `--request-timeout` -instellingen te wijzigen (Zie [hier](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)meer informatie). Als dit niet voldoende is en er nog steeds een time-out optreedt, kunt u een aantal records ophalen uit de Azure Cosmos DB back-end-telemetrie door naar het tabblad metrische gegevens in Azure Portal te gaan, de metriek te selecteren `document count` en vervolgens een filter toe te voegen voor de data base of verzameling (de tabel analoog van Azure Cosmos DB). Vervolgens kunt u de muis aanwijzer over de resulterende grafiek bewegen voor het punt in de tijd waarop u het aantal records wilt weten.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="weer gave metrische gegevens":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>ReconnectionPolicy voor Java-stuur programma configureren

### <a name="version-3x"></a>Versie 3. x

Voor versie 3. x van het Java-stuur programma configureert u het beleid voor opnieuw verbinden bij het maken van een cluster object:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Versie 4. x

Voor versie 4. x van het Java-stuur programma configureert u het beleid voor opnieuw verbinden door de instellingen in het bestand te vervangen `reference.conf` :

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Keep-Alive voor Java-stuur programma inschakelen

### <a name="version-3x"></a>Versie 3. x

Stel voor versie 3. x van het Java-stuur programma Keep-Alive in bij het maken van een cluster object en zorg ervoor dat Keep-Alive is [ingeschakeld in het besturings systeem](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Versie 4. x

Stel voor versie 4. x van het Java-stuur programma Keep-Alive in als u de instellingen wilt overschrijven `reference.conf` en zorg ervoor dat Keep-Alive is [ingeschakeld in het besturings systeem](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ondersteunde functies](cassandra-support.md) in azure Cosmos DB Cassandra-API.
- Meer informatie over het [migreren van systeem eigen Apache Cassandra naar Azure Cosmos DB Cassandra-API](cassandra-migrate-cosmos-db-databricks.md)
