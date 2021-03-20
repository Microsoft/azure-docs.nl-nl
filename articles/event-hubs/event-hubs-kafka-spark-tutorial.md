---
title: Verbinding maken met uw Apache Spark-app - Azure Event Hubs | Microsoft Docs
description: In dit artikel vindt u informatie over het gebruik van Apache Spark met Azure Event Hubs voor Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84184ed3dffee97863b93c592d1cd577df313605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913735"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Uw Apache Spark-toepassing verbinden met Azure Event Hubs
In deze zelf studie wordt u begeleid bij het koppelen van uw Spark-toepassing aan Event Hubs voor realtime-streaming. Met deze integratie kunt u streaming maken zonder uw protocol-clients te wijzigen of uw eigen Kafka-of Zookeeper-clusters uit te voeren. Voor deze zelf studie is Apache Spark v 2.4 + en Apache Kafka v 2.0 + vereist.

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Spark uitvoeren
> * Lezen van Event Hubs voor Kafka
> * Schrijven naar Event Hubs voor Kafka

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u het volgende hebt voordat u aan deze zelfstudie begint:
-   Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/).
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> De Spark-Kafka-adapter is vanaf Spark v2.4 bijgewerkt ter ondersteuning van Kafka v2.0. In vorige release van Spark werden Kafka v0.10 en nieuwere versies door de adapter ondersteund, maar werd specifiek vertrouwd op Kafka v0.10-API's. Omdat Event Hubs voor Kafka geen ondersteuning biedt voor Kafka v0.10, worden de Spark-Kafka-adapters van Spark-versies ouder dan v2.4 niet ondersteund door Event Hubs voor Kafka Ecosystems.


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [een event hub maken](event-hubs-create.md) voor instructies voor het maken van een naam ruimte en een event hub. Haal de Event Hubs-verbindingsreeks en de Fully Qualified Domain Name (FQDN) op voor later gebruik. Zie [Get an Event Hubs connection string](event-hubs-get-connection-string.md). (Een Event Hubs-verbindingsreeks ophalen). 

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen
Kloon de Azure Event Hubs-opslagplaats en ga naar submap `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Lezen van Event Hubs voor Kafka
Na enkele configuratiewijzigingen kunt u lezen van Event Hubs voor Kafka. Werk **BOOTSTRAP_SERVERS** en **EH_SASL** bij met details van uw naamruimte. Vervolgens kunt u met Event Hubs met streamen beginnen, net zoals u dat met Kafka zou doen. Zie het bestand sparkConsumer.scala op GitHub voor de volledige voorbeeldcode. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Schrijven naar Event Hubs voor Kafka
U kunt op dezelfde manier naar Event Hubs schrijven als u dat naar Kafka zou doen. Vergeet niet uw configuratie bij te werken door **BOOTSTRAP_SERVERS** en **EH_SASL** te wijzigen met gegevens uit uw Event Hubs-naamruimte.  Zie het bestand sparkProducer.scala op GitHub voor de volledige voorbeeldcode. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md)
- [Kafka integreren met een Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
- [Akka-streams verbinden met een Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)

