---
title: Veelvoorkomende fouten in Azure Cosmos DB oplossen Cassandra-API
description: Dit document bevat informatie over de manieren om veelvoorkomende problemen op te lossen die optreden in Azure Cosmos DB Cassandra-API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: f5f2cb5ac8c354df38310cdcb47b98e1da5b6cfa
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521823"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Veelvoorkomende problemen in Azure Cosmos DB Cassandra-API oplossen
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra-API in Azure Cosmos DB is een compatibiliteits laag, die [ondersteuning](cassandra-support.md) biedt voor wire-protocol voor de populaire open-source Apache Cassandra-data base, en wordt aangestuurd door [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). Als volledig beheerde Cloud-eigen service biedt Azure Cosmos DB [garanties op het Beschik baarheid, door Voer en de consistentie](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) van Cassandra-API. Deze garanties zijn niet mogelijk in verouderde implementaties van Apache Cassandra. Cassandra-API vereenvoudigt ook de platform bewerkingen voor nul-onderhoud en zero-downtime-patches. Als zodanig zijn veel van de back-end-bewerkingen verschillen van Apache Cassandra, daarom raden we specifieke instellingen en benaderingen aan om veelvoorkomende fouten te voor komen. 

In dit artikel worden veelvoorkomende fouten en oplossingen beschreven voor toepassingen die Azure Cosmos DB Cassandra-API gebruiken.

## <a name="common-errors-and-solutions"></a>Veelvoorkomende fouten en oplossingen

| Fout               |  Beschrijving             | Oplossing  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | Het totale aantal verbruikte aanvraag eenheden is hoger dan de aanvraag eenheden die zijn ingericht voor de code of tabel. Daarom worden de aanvragen beperkt. | U kunt de door Voer die is toegewezen aan een spatie of tabel, schalen op basis van de Azure Portal (Zie [hier](manage-scale-cassandra.md) voor schaal bewerkingen in Cassandra-API) of als u een beleid voor opnieuw proberen wilt implementeren. Voor Java, zie voor beelden opnieuw proberen voor het stuur programma [v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) en het [stuur programma v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Zie ook [Azure Cosmos Cassandra-uitbrei dingen voor Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| OverloadedException (Java), zelfs met voldoende door Voer | Het systeem lijkt te vereisen dat er aanvragen worden beperkt, ondanks dat er voldoende door Voer is ingericht voor aanvraag volume en/of verbruikte aanvraag eenheids kosten  | Cassandra-API implementeert een systeem doorvoer budget voor bewerkingen op schema niveau (CREATE TABLE, ALTER TABLE, DROP TABLE). Dit budget moet voldoende zijn voor schema bewerkingen in een productie systeem. Als u echter een groot aantal bewerkingen op schema niveau hebt, is het mogelijk dat u deze limiet overschrijdt. Aangezien dit budget niet door de gebruiker wordt beheerd, moet u overwegen om het aantal uitgevoerde schema bewerkingen te verlagen. Als u met deze actie het probleem niet kunt verhelpen of als het niet haalbaar is voor uw workload, [maakt u een ondersteunings aanvraag voor Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | Na een periode van niet-actieve verbindingen, kan de toepassing geen verbinding maken| Deze fout kan worden veroorzaakt door een time-out voor inactiviteit van Azure LoadBalancers, die 4 minuten is. Stel Keep Alive-instelling in het stuur programma in (zie hieronder) en verbeter Keep-Alive-instellingen in het besturings systeem of [Stel time-out voor inactiviteit in azure Load Balancer in](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Andere onregelmatige verbindings fouten (Java) | De verbinding wordt onverwacht verbroken of geduurd | De Apache Cassandra-Stuur Programma's voor java bieden twee systeem eigen beleid voor opnieuw verbinden: `ExponentialReconnectionPolicy` en `ConstantReconnectionPolicy` . De standaardwaarde is `ExponentialReconnectionPolicy`. Voor Azure Cosmos DB Cassandra-API wordt echter `ConstantReconnectionPolicy` een vertraging van 2 seconden aangeraden. Raadpleeg de [documentatie van het stuur programma](https://docs.datastax.com/developer/java-driver/4.9/manual/core/reconnection/)  voor Java v4. x-stuur programma en [hier](https://docs.datastax.com/developer/java-driver/3.7/manual/reconnection/) voor Java 3. x-richt lijnen (Zie ook de voor beelden hieronder).|

Als uw fout niet hierboven wordt vermeld en er een fout optreedt bij het uitvoeren van een [ondersteunde bewerking in Cassandra-API](cassandra-support.md), waarbij de fout *niet aanwezig is bij het gebruik van native Apache Cassandra*, [maakt u een ondersteunings aanvraag voor Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)

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

