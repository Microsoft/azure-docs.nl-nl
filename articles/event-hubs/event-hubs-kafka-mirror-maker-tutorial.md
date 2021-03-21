---
title: Apache Kafka MirrorMaker gebruiken-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het gebruik van Kafka MirrorMaker om een Kafka-cluster in evenementen hubs te spie gelen.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 654e9e19dfde0d0c58d00e41cf8ab0ba8e1484d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97860993"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Apache Kafka MirrorMaker gebruiken met Event Hubs

Deze zelf studie laat zien hoe u een Kafka-Broker kunt spie gelen in een Azure Event hub met behulp van Kafka MirrorMaker. Als u Apache Kafka op Kubernetes host met behulp van de CNCF Strimzi-operator, raadpleegt u de zelf studie in [dit blog bericht](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/) voor meer informatie over het instellen van Kafka met Strimzi en mirror Maker 2. 

   ![Kafka MirrorMaker met Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *whitelist*, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Een Kafka-cluster instellen
> * Kafka MirrorMaker configureren
> * Kafka MirrorMaker uitvoeren

## <a name="introduction"></a>Inleiding
In deze zelf studie ziet u hoe een Event Hub-en Kafka-MirrorMaker een bestaande Kafka-pijp lijn kan integreren in azure door de Kafka-invoer stroom te spie gelen in de Event Hubs-service, waarmee Apache Kafka-streams met verschillende [Federatie patronen](event-hubs-federation-overview.md)kunnen worden geïntegreerd. 

Met een Azure Event Hubs Kafka-eind punt kunt u verbinding maken met Azure Event Hubs met behulp van het Kafka-Protocol (dat wil zeggen Kafka-clients). Door minimale wijzigingen aan te brengen in een Kafka-toepassing, kunt u verbinding maken met Azure Event Hubs en profiteren van de voor delen van het Azure-ecosysteem. Event Hubs ondersteunt momenteel het Protocol van Apache Kafka versie 1,0 en hoger.

U kunt de MirrorMaker 1 van Apache Kafka van Apache Kafka naar Event Hubs gebruiken. MirrorMaker 2 kan in beide richtingen worden gebruikt, maar de en geconfigureerde [ `MirrorCheckpointConnector` en te `MirrorHeartbeatConnector` configureren in MirrorMaker 2](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) moeten beide worden geconfigureerd om naar de Apache Kafka Broker te verwijzen en niet naar Event hubs. In deze zelf studie wordt de configuratie van MirrorMaker 1 beschreven.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* Een binair archief van Maven [downloaden](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html)
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [een event hub maken](event-hubs-create.md) voor instructies voor het maken van een naam ruimte en een event hub. Zorg ervoor dat u de Event Hubs-connection string kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u een Event Hubs connection string hebt, kloont u de Azure Event Hubs voor de Kafka-opslag plaats en navigeert u naar de `mirror-maker` submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Een Kafka-cluster instellen

Gebruik de [Kafka Snelstartgids](https://kafka.apache.org/quickstart) om een cluster in te stellen met de gewenste instellingen (of gebruik een bestaand Kafka-cluster).

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker configureren

Kafka MirrorMaker maakt het spie gelen van een stroom mogelijk. Op basis van de bron-en doel-Kafka-clusters zorgt MirrorMaker ervoor dat berichten die naar het bron cluster worden verzonden, zowel door de bron-als de doel clusters worden ontvangen. In dit voor beeld ziet u hoe u een bron Kafka-cluster spiegelt met een doel Event Hub. Dit scenario kan worden gebruikt om gegevens te verzenden van een bestaande Kafka-pijp lijn naar Event Hubs zonder de stroom van gegevens te onderbreken. 

Zie de [hand leiding Kafka mirroring/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)voor meer gedetailleerde informatie over Kafka MirrorMaker.

Als u Kafka MirrorMaker wilt configureren, geeft u het een Kafka-cluster als consument/herkomst en een Event Hub als producent/bestemming.

#### <a name="consumer-configuration"></a>Consumenten configuratie

Werk het configuratie bestand van de gebruiker `source-kafka.config` bij, waarmee MirrorMaker de eigenschappen van het Kafka cluster worden aangegeven.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producer-configuratie

Werk nu het configuratie bestand van de producent bij `mirror-eventhub.config` , waarmee wordt aangegeven dat MirrorMaker de gedupliceerde (of gespiegelde) gegevens naar de Event hubs-service verzendt. Wijzig `bootstrap.servers` en `sasl.jaas.config` Ga met name naar uw event hubs Kafka-eind punt. De Event Hubs-service vereist beveiligde (SASL) communicatie. dit wordt bereikt door de laatste drie eigenschappen in de volgende configuratie in te stellen: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker uitvoeren

Voer het script Kafka MirrorMaker uit vanuit de hoofdmap van Kafka met de zojuist bijgewerkte configuratie bestanden. Kopieer de configuratie bestanden naar de hoofdmap Kafka of werk hun paden bij in de volgende opdracht.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Als u wilt controleren of gebeurtenissen de Event Hub bereiken, raadpleegt u de ingangs statistieken in de [Azure Portal](https://azure.microsoft.com/features/azure-portal/)of voert u een consument uit op basis van de Event hub.

Wanneer MirrorMaker wordt uitgevoerd, worden alle gebeurtenissen die worden verzonden naar het bron Kafka-cluster ontvangen door het Kafka-cluster en de gespiegelde Event Hub. Door MirrorMaker en een Event Hubs Kafka-eind punt te gebruiken, kunt u een bestaande Kafka-pijp lijn migreren naar de beheerde Azure Event Hubs-service zonder dat u het bestaande cluster wijzigt of doorlopende gegevens stroom onderbreekt.

## <a name="samples"></a>Voorbeelden
Bekijk de volgende voorbeelden op GitHub:

- [Voorbeeld code voor deze zelf studie op GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker uitgevoerd op een Azure-container exemplaar](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs voor Kafka:  

- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md)
- [Kafka integreren met een Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
- [Akka-streams verbinden met een Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)