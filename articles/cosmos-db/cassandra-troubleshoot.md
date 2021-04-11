---
title: Veelvoorkomende fouten in het Azure Cosmos DB oplossen Cassandra-API
description: In dit artikel worden veelvoorkomende problemen in de Azure Cosmos DB Cassandra-API beschreven en hoe u deze problemen kunt oplossen.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967350"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Veelvoorkomende problemen in het Azure Cosmos DB oplossen Cassandra-API

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

De Cassandra-API in [Azure Cosmos DB](./introduction.md) is een compatibiliteit slaag die ondersteuning biedt voor [wire-protocol](cassandra-support.md) voor de open source Apache Cassandra-data base.

In dit artikel worden veelvoorkomende fouten en oplossingen beschreven voor toepassingen die gebruikmaken van de Azure Cosmos DB Cassandra-API. Als uw fout niet wordt vermeld en er een fout optreedt wanneer u een [ondersteunde bewerking uitvoert in Cassandra](cassandra-support.md), maar de fout niet aanwezig is bij het gebruik van native Apache Cassandra, moet u [een Azure-ondersteunings aanvraag maken](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>Als volledig beheerde Cloud-native service biedt Azure Cosmos DB [garanties op het Beschik baarheid, door Voer en de consistentie](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) van de Cassandra-API. De Cassandra-API vereenvoudigt ook de platform bewerkingen voor nul onderhoud en de patch voor Zero-uitval tijd.
>
>Deze garanties zijn niet mogelijk in eerdere implementaties van Apache Cassandra, waardoor veel van de Cassandra-API back-end-bewerkingen verschillen van Apache Cassandra. We raden u aan bepaalde instellingen en benaderingen te gebruiken om veelvoorkomende fouten te voor komen.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Deze fout is een wrapper-uitzonde ring op het hoogste niveau met een groot aantal mogelijke oorzaken en interne uitzonde ringen, die veel aan de client gerelateerd kunnen zijn.

Veelvoorkomende oorzaken en oplossingen:

- **Time-out voor inactiviteit van Azure LoadBalancers**: dit probleem kan zich ook voordoen als `ClosedConnectionException` . Om het probleem op te lossen, stelt u de Keep-Alive-instelling in het stuur programma in (Zie [Keep-Alive inschakelen voor het Java-stuur programma](#enable-keep-alive-for-the-java-driver)), de Keep-Alive-instellingen in het besturings systeem verg Roten of de [time-out voor inactiviteit in azure Load Balancer aanpassen](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Bron uitputting van client toepassing**: Zorg ervoor dat de client computers voldoende bronnen hebben om de aanvraag te volt ooien.

## <a name="cant-connect-to-a-host"></a>Kan geen verbinding maken met een host

Mogelijk ziet u deze fout: ' kan geen verbinding maken met een host, de nieuwe poging over 600000 milliseconden plannen. '

Deze fout kan worden veroorzaakt door een bron Network Address Translation SNAT-uitputting aan de client zijde. Volg de stappen op [SNAT voor uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md) om dit probleem op te lossen.

De fout kan ook een probleem met een niet-actieve time-out zijn waarbij de Azure load balancer standaard vier minuten van time-out voor inactiviteit heeft. Zie [time-out voor inactiviteit van Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Schakel Keep-Alive in voor het Java-stuur programma](#enable-keep-alive-for-the-java-driver) en stel het `keepAlive` interval op het besturings systeem in op minder dan vier minuten.

## <a name="overloadedexception-java"></a>OverloadedException (Java)

Aanvragen worden beperkt omdat het totale aantal verbruikte aanvraag eenheden hoger is dan het aantal aanvraag eenheden dat u hebt ingericht voor de code of tabel.

U kunt overwegen de door Voer die is toegewezen aan een spatie of tabel te schalen vanuit de Azure Portal (Zie [elastisch schalen van een Azure Cosmos DB Cassandra-API-account](manage-scale-cassandra.md)) of het implementeren van een beleid voor opnieuw proberen.

Voor Java, zie voor beelden opnieuw proberen voor het stuur [programma v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) en het [stuur programma v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Zie ook [Azure Cosmos Cassandra-uitbrei dingen voor Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>OverloadedException ondanks voldoende door Voer

Het systeem lijkt aanvragen te beperken, zelfs als er voldoende door Voer is ingericht voor het aanvraag volume of de verbruikte aanvraag eenheids kosten. Er zijn twee mogelijke oorzaken:

- **Bewerkingen op schema niveau**: de Cassandra-API implementeert een systeem doorvoer budget voor bewerkingen op schema niveau (Create Table, ALTER TABLE, Drop Table). Dit budget moet voldoende zijn voor schema bewerkingen in een productie systeem. Als u echter een groot aantal bewerkingen op schema niveau hebt, is het mogelijk dat u deze limiet overschrijdt.

  Omdat het budget niet door de gebruiker wordt beheerd, kunt u het aantal schema bewerkingen verlagen dat u uitvoert. Als deze actie het probleem niet oplost of als het niet haalbaar is voor uw workload, [maakt u een ondersteunings aanvraag voor Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Gegevens scheefheid**: wanneer de door Voer is ingericht in de Cassandra-API, wordt deze gelijkmatig verdeeld tussen fysieke partities en heeft elke fysieke partitie een bovengrens. Als u een grote hoeveelheid gegevens invoegt of van een bepaalde partitie opvraagt, is het mogelijk een beperkt aantal, zelfs als u een groot aantal algemene door Voer (aanvraag eenheden) voor die tabel inricht.

  Controleer uw gegevens model en zorg ervoor dat u geen overmatige scheefheid hebt die dynamische partities kan veroorzaken.

## <a name="intermittent-connectivity-errors-java"></a>Onregelmatige verbindings fouten (Java)

De verbinding wordt onverwacht verbroken of geduurd.

De Apache Cassandra-Stuur Programma's voor java bieden twee systeem eigen beleid voor opnieuw verbinden: `ExponentialReconnectionPolicy` en `ConstantReconnectionPolicy` . De standaardwaarde is `ExponentialReconnectionPolicy`. Voor Azure Cosmos DB Cassandra-API wordt echter aangeraden een vertraging van twee seconden op te lossen `ConstantReconnectionPolicy` .

Zie de [documentatie voor het stuur programma Java 4. x](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), de [documentatie voor het Java](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)-stuur programma (Engelstalig), of [ReconnectionPolicy configureren voor de voor beelden van Java-Stuur Programma's](#configure-reconnectionpolicy-for-the-java-driver) .

## <a name="error-with-load-balancing-policy"></a>Fout met taakverdelings beleid

U hebt mogelijk een taakverdelings beleid geïmplementeerd in v3. x van het Java DataStax-stuur programma, met code vergelijkbaar met:

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

Als de waarde voor `withLocalDc()` niet overeenkomt met het Data Center van het contact punt, kan er een onregelmatige fout optreden: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

Implementeer de [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). Om het werk te laten werken, moet u wellicht DataStax bijwerken met behulp van de volgende code:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Het aantal mislukt voor een grote tabel

Wanneer u `select count(*) from table` of soortgelijk voor een groot aantal rijen werkt, loopt de server een time-out.

Als u een lokale CQLSH-client gebruikt, wijzigt u `--connect-timeout` de `--request-timeout` instellingen of. Zie [cqlsh: de CQL-shell](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Als er nog een time-out voor de telling optreedt, kunt u het aantal records van de Azure Cosmos DB back-end-telemetrie ophalen door naar het tabblad metrische gegevens in de Azure Portal te gaan, de metriek te selecteren `document count` en vervolgens een filter toe te voegen voor de data base of verzameling (het analoge van de tabel in azure Cosmos DB). Vervolgens kunt u de muis aanwijzer over de resulterende grafiek bewegen voor het punt in de tijd waarop u het aantal records wilt weten.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="weer gave metrische gegevens":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>ReconnectionPolicy configureren voor het Java-stuur programma

### <a name="version-3x"></a>Versie 3. x

Voor versie 3. x van het Java-stuur programma configureert u het beleid voor opnieuw verbinden wanneer u een cluster object maakt:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Versie 4. x

Voor versie 4. x van het Java-stuur programma configureert u het beleid voor opnieuw verbinden door de instellingen in het bestand te overschrijven `reference.conf` :

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

## <a name="enable-keep-alive-for-the-java-driver"></a>Keep-Alive inschakelen voor het Java-stuur programma

### <a name="version-3x"></a>Versie 3. x

Stel voor versie 3. x van het Java-stuur programma Keep-Alive in wanneer u een cluster object maakt en controleer vervolgens of Keep-Alive is [ingeschakeld in het besturings systeem](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

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

Stel voor versie 4. x van het Java-stuur programma Keep-Alive in door instellingen te overschrijven `reference.conf` en controleer of Keep-Alive is [ingeschakeld in het besturings systeem](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [ondersteunde functies](cassandra-support.md) in de Azure Cosmos DB Cassandra-API.
- Meer informatie over het [migreren van systeem eigen Apache Cassandra naar Azure Cosmos DB Cassandra-API](cassandra-migrate-cosmos-db-databricks.md).
