---
title: JMS-toepassingen (Java Message Service) migreren van Apache ActiveMQ naar Azure Service Bus | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u bestaande JMS-toepassingen die communiceren met Apache ActiveMQ, kunt migreren om te communiceren met Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: 4160a9ab4edbac8584eab2d4e5b9bf1ba11a9aec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568780"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Bestaande JMS-toepassingen (Java Message Service 2,0) migreren van Apache ActiveMQ naar Azure Service Bus

In dit artikel wordt beschreven hoe u een bestaande JMS (Java Message Service) 2,0-toepassing wijzigt die samenwerkt met een JMS-Broker om te communiceren met Azure Service Bus in plaats daarvan. In het artikel worden met name de migratie van Apache ActiveMQ of Amazon MQ beschreven.

Azure Service Bus ondersteunt Java 2-platform-, Enter prise Edition-en lente-workloads die gebruikmaken van de JMS 2,0-API via Advanced Message queueing Protocol (AMQP).

## <a name="before-you-start"></a>Voordat u begint

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Verschillen tussen Azure Service Bus en Apache ActiveMQ

Azure Service Bus en Apache ActiveMQ zijn beide bericht Brokers, functioneren als JMS-providers voor client toepassingen om berichten te verzenden naar en berichten te ontvangen van. Ze bieden beide de punt-naar-punt semantiek met wacht rijen en publiceren/abonneren op semantiek met onderwerpen en abonnementen. 

Er zijn ook enkele verschillen tussen de twee, zoals in de volgende tabel wordt weer gegeven:

| Categorie | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| Toepassings lagen | Geclusterde op | Twee lagen <br> (gateway + back-end) |
| Protocolondersteuning | <ul> <li>AMQP</li> <li> STOMP </li> <li> Openkabel </li> </ul> | AMQP |
| Inrichtingsmodus | <ul> <li> Infrastructure as a Service (IaaS), on-premises </li> <li> Amazon MQ (beheerd platform as a Service) </li> | Beheerd platform as a Service (PaaS) |
| Berichtgrootte | Klant configureerbaar | 1 MB (Premium-laag) |
| Hoge beschikbaarheid | Door de klant beheerd | Platform beheerd |
| Herstel na noodgeval | Door de klant beheerd | Platform beheerd | 

### <a name="current-supported-and-unsupported-features"></a>Huidige ondersteunde en niet-ondersteunde functies

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Overwegingen

De twee lagen van Azure Service Bus bieden verschillende mogelijkheden voor bedrijfs continuïteit (hoge Beschik baarheid en herstel na nood gevallen). Er zijn echter enkele aandachtspunten bij het gebruik van JMS-functies.

#### <a name="service-upgrades"></a>Service-upgrades

In het geval van service bus-upgrades en opnieuw starten, worden tijdelijke wacht rijen of onderwerpen verwijderd. Als uw toepassing gevoelig is voor gegevens verlies op tijdelijke wacht rijen of onderwerpen, gebruikt u geen tijdelijke wacht rijen of onderwerpen. Gebruik in plaats daarvan duurzame wacht rijen, onderwerpen en abonnementen.

#### <a name="data-migration"></a>Gegevensmigratie

Als onderdeel van het migreren en aanpassen van uw client toepassingen om met Azure Service Bus te communiceren, worden de gegevens die in ActiveMQ worden bewaard, niet gemigreerd naar Service Bus. Mogelijk hebt u een aangepaste toepassing nodig om de ActiveMQ-wacht rijen,-onderwerpen en-abonnementen te verbreken en vervolgens de berichten opnieuw af te spelen voor de wacht rijen, onderwerpen en abonnementen van Service Bus.

#### <a name="authentication-and-authorization"></a>Verificatie en autorisatie

Op rollen gebaseerd toegangs beheer van Azure (Azure RBAC), ondersteund door Azure Active Directory, is het voorkeurs verificatie mechanisme voor Service Bus. Omdat Azure RBAC, of op claims gebaseerde verificatie, momenteel niet wordt ondersteund door Apache QPID JMS, moet u echter SAS-sleutels gebruiken voor verificatie.

## <a name="pre-migration"></a>Premigratie

### <a name="version-check"></a>Versie controle

U gebruikt de volgende onderdelen en versies terwijl u de JMS-toepassingen schrijft: 

| Onderdeel | Versie |
|---|---|
| API voor Java-berichten service (JMS) | 1,1 of hoger |
| AMQP-Protocol | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Zorg ervoor dat de AMQP-poorten zijn geopend

Service Bus ondersteunt communicatie via het AMQP-protocol. Voor dit doel schakelt u communicatie in via poorten 5671 (AMQP) en 443 (TCP). Afhankelijk van waar de client toepassingen worden gehost, hebt u mogelijk een ondersteunings ticket nodig om communicatie via deze poorten mogelijk te maken.

> [!IMPORTANT]
> Service Bus ondersteunt alleen AMQP 1,0-protocol.

### <a name="set-up-enterprise-configurations"></a>Bedrijfs configuraties instellen

Service Bus biedt verschillende functies voor beveiliging en hoge Beschik baarheid voor ondernemingen. Zie voor meer informatie: 

  * [Service-eindpunten voor virtueel netwerk](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Versleuteling aan de service zijde met de door de klant beheerde sleutel (BYOK)](configure-customer-managed-key.md)
  * [Privé-eindpunten](private-link-service.md)
  * [Verificatie en autorisatie](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Bewaking, waarschuwingen en tracering

Voor elke Service Bus naam ruimte publiceert u metrische gegevens op Azure Monitor. U kunt deze metrische gegevens gebruiken voor waarschuwingen en dynamische schaling van resources die zijn toegewezen aan de naam ruimte.

Zie [Service Bus metrische gegevens in azure monitor](service-bus-metrics-azure-monitor.md)voor meer informatie over de verschillende metrische gegevens en over het instellen van waarschuwingen. U kunt ook meer te weten komen over [tracering aan de client zijde voor gegevens bewerkingen](service-bus-end-to-end-tracing.md) en [operationele/diagnostische logboek registratie voor beheer bewerkingen](service-bus-diagnostic-logs.md).

### <a name="metrics---new-relic"></a>Metrische gegevens-nieuwe Relic

U kunt correleren welke metrische gegevens in de ActiveMQ worden toegewezen aan de metrische gegevens in Azure Service Bus. Zie het volgende van de nieuwe Relic-website:

  * [ActiveMQ/Amazon MQ nieuwe Relic-metrische gegevens](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Nieuwe Relic-metrische gegevens Azure Service Bus](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Nieuwe Relic heeft momenteel geen rechtstreekse en naadloze integratie met ActiveMQ, maar er zijn metrische gegevens beschikbaar voor Amazon MQ. Omdat Amazon MQ is afgeleid van ActiveMQ, wijst de volgende tabel de nieuwe Relic-metrische gegevens van Amazon MQ toe aan Azure Service Bus.
>

|Metrische groepering| Amazon MQ/ActiveMQ metric | Azure Service Bus metriek |
|------------|---------------------------|--------------------------|
|Connection|`CpuUtilization`|`CPUXNS`|
|Connection|`MemoryUsage`|`WSXNS`|
|Connection|`CurrentConnectionsCount`|`activeConnections`|
|Connection|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Connection|`InactiveDurableTopicSubscribersCount`|Metrische gegevens van abonnement gebruiken|
|Connection|`TotalMessageCount`|Wachtrij/onderwerp/abonnements niveau gebruiken `activeMessages`|
|Wachtrij/onderwerp|`EnqueueCount`|`incomingMessages`|
|Wachtrij/onderwerp|`DequeueCount`|`outgoingMessages`|
|Wachtrij|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migratie

Volg de stappen in de volgende secties om uw bestaande JMS 2,0-toepassing te migreren om te communiceren met Service Bus.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>De topologie van ActiveMQ exporteren en de entiteiten in Service Bus maken (optioneel)

Om ervoor te zorgen dat client toepassingen probleemloos verbinding kunnen maken met Service Bus, migreert u de topologie (met inbegrip van wacht rijen, onderwerpen en abonnementen) van Apache ActiveMQ naar Service Bus.

> [!NOTE]
> Voor JMS-toepassingen maakt u wacht rijen, onderwerpen en abonnementen als runtime-bewerking. De meeste JMS-providers (bericht Brokers) bieden u de mogelijkheid deze te maken tijdens runtime. Daarom wordt deze stap voor exporteren als optioneel beschouwd. Gebruik de connection string met SAS-machtigingen om ervoor te zorgen dat uw toepassing beschikt over de machtigingen voor het maken van de topologie tijdens runtime `Manage` .

Om dit te doen:

1. Gebruik de [ActiveMQ-opdracht regel Programma's](https://activemq.apache.org/activemq-command-line-tools-reference) voor het exporteren van de topologie.
1. Maak dezelfde topologie opnieuw met behulp van een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
1. Voer de Azure Resource Manager-sjabloon uit.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>De Maven-afhankelijkheid voor Service Bus JMS-implementatie importeren

Als u wilt zorgen voor naadloze connectiviteit met Service Bus, voegt `azure-servicebus-jms` u het pakket als volgt toe als afhankelijkheid voor het maven `pom.xml` -bestand:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Wijzigingen in de toepassings server configuratie

Dit onderdeel is aangepast aan de toepassings server die als host fungeert voor uw client toepassingen die verbinding maken met ActiveMQ.

#### <a name="spring-applications"></a>Lente toepassingen

##### <a name="update-the-applicationproperties-file"></a>Het `application.properties` bestand bijwerken

Als u een Spring boot-toepassing gebruikt om verbinding te maken met ActiveMQ, wilt u de ActiveMQ-specifieke eigenschappen uit het `application.properties` bestand verwijderen.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Voeg vervolgens de Service Bus-specifieke eigenschappen toe aan het `application.properties` bestand.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Vervang `ActiveMQConnectionFactory` door `ServiceBusJmsConnectionFactory`

De volgende stap is het vervangen van de instantie van `ActiveMQConnectionFactory` met `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> De werkelijke code wijzigingen zijn specifiek voor de toepassing en de wijze waarop afhankelijkheden worden beheerd, maar het volgende voor beeld bevat de richt lijnen voor het wijzigen van wat er moet worden gewijzigd.
>

Eerder hebt u mogelijk een object van, als volgt, een exemplaar van `ActiveMQConnectionFactory` :

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Nu wijzigt u deze voor het instantiëren van een object van `ServiceBusJmsConnectionFactory` , als volgt:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Postmigratie

Nu u de toepassing hebt gewijzigd om berichten van Service Bus te verzenden en te ontvangen, moet u controleren of deze werkt zoals verwacht. Wanneer u klaar bent, kunt u door gaan met het verder verfijnen en moderniseren van uw toepassings stack.

## <a name="next-steps"></a>Volgende stappen

Gebruik de [Spring boot starter voor Azure service bus JMS](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) voor naadloze integratie met Service Bus.

Voor meer informatie over Service Bus Messa ging en JMS raadpleegt u:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
