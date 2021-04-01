---
title: Gebruik AMQP met de API voor Java-berichten service en Azure Service Bus
description: Gebruik de Java-berichten service (JMS) met Azure Service Bus en de Advanced Message Queueing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90086688"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Gebruik de Java-berichten service met Azure Service Bus en AMQP 1,0

> [!WARNING]
> Dit artikel heeft betrekking op *beperkte ondersteuning* voor de JMS-API (Java Message Service) 1,1 en bestaat alleen voor de laag Azure service bus Standard.
>
> Volledige ondersteuning voor de API Java Message Service 2,0 is alleen beschikbaar op de [Azure service bus Premium-laag in Preview](how-to-use-java-message-service-20.md). U wordt aangeraden deze laag te gebruiken.
>

In dit artikel wordt uitgelegd hoe u Service Bus Messa ging-functies van Java-toepassingen gebruikt met behulp van de populaire JMS API-standaard. Deze berichten functies omvatten wacht rijen en publiceren of abonneren op onderwerpen. In een [aanvullende artikel](service-bus-amqp-dotnet.md) wordt uitgelegd hoe u hetzelfde kunt doen met behulp van de Azure service bus .net API. U kunt deze twee artikelen samen gebruiken om meer te weten te komen over platformoverschrijdende berichten met behulp van de Advanced Message Queueing Protocol (AMQP) 1,0.

AMQP 1,0 is een efficiënt, betrouwbaar, Wire-level berichten protocol dat u kunt gebruiken voor het bouwen van robuuste, platform onafhankelijke toepassingen voor bericht verwerking.

Ondersteuning voor AMQP 1,0 in Service Bus houdt in dat u de wachtrij kunt gebruiken om Brokered Messaging-functies te publiceren of te abonneren vanuit diverse platformen met behulp van een efficiënt binair protocol. U kunt ook toepassingen bouwen die bestaan uit onderdelen die zijn gebouwd met behulp van een combi natie van talen, frameworks en besturings systemen.

## <a name="get-started-with-service-bus"></a>Aan de slag met Service Bus

In dit artikel wordt ervan uitgegaan dat u al een Service Bus naam ruimte hebt die een wachtrij met de naam bevat `basicqueue` . Als dat niet het geval is, kunt u [de naam ruimte en wachtrij maken](service-bus-create-namespace-portal.md) met behulp van de [Azure Portal](https://portal.azure.com). Zie [aan de slag met Service Bus wachtrijen](service-bus-dotnet-get-started-with-queues.md)voor meer informatie over het maken van service bus naam ruimten en wacht rijen.

> [!NOTE]
> Gepartitioneerde wacht rijen en onderwerpen bieden ook ondersteuning voor AMQP. Zie [gepartitioneerde Messa ging-entiteiten](service-bus-partitioning.md) en [AMQP 1,0-ondersteuning voor service bus gepartitioneerde wacht rijen en onderwerpen](./service-bus-amqp-protocol-guide.md)voor meer informatie.
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>De AMQP 1,0 JMS-client bibliotheek downloaden

Voor informatie over waar u de nieuwste versie van de Apache Qpid JMS AMQP 1,0-client bibliotheek kunt downloaden, raadpleegt u de [Apache Qpid-download site](https://qpid.apache.org/download.html).

U moet de volgende JAR-bestanden van het Apache Qpid JMS AMQP 1,0-distributie archief toevoegen aan de omgevings variabele Java CLASSPATH wanneer u JMS-toepassingen bouwt en uitvoert met Service Bus:

* geronimo-jms \_ 1,1 \_ spec-1.0. jar
* QPid-JMS-client-[versie]. jar

> [!NOTE]
> JMSe JAR-namen en-versies zijn mogelijk gewijzigd. Zie [QPID JMS AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)voor meer informatie.

## <a name="code-java-applications"></a>Code Java-toepassingen

### <a name="java-naming-and-directory-interface"></a>Java-naamgeving en Directory interface

JMS maakt gebruik van de Java-naamgevings-en Directory interface (JNDI) om een schei ding tussen logische namen en fysieke namen te maken. Twee typen JMS-objecten worden omgezet met behulp van JNDI: **ConnectionFactory** en **Destination**. JNDI maakt gebruik van een provider model waarin u verschillende Directory Services kunt koppelen om taken voor naam omzetting af te handelen. De Apache Qpid JMS AMQP 1,0-bibliotheek wordt geleverd met een eenvoudige JNDI provider op basis van een bestand dat is geconfigureerd met behulp van een eigenschappen bestand van de volgende indeling:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>JNDI-context instellen en het ConnectionFactory-object configureren

De connection string waarnaar wordt verwezen, is de versie die beschikbaar is in het beleid voor gedeelde toegang in de [Azure Portal](https://portal.azure.com) onder **primaire verbindings reeks**.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Producer-en consumenten doel wachtrijen configureren

De vermelding die wordt gebruikt voor het definiëren van een bestemming in het QPid properties file JNDI-provider heeft de volgende indeling.

Een doel wachtrij maken voor de producent:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

Een doel wachtrij maken voor de Consumer:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>De JMS-toepassing schrijven

Er zijn geen speciale Api's of opties vereist wanneer u JMS gebruikt met Service Bus. Er zijn enkele beperkingen die later worden besproken. Net als bij elke JMS-toepassing is het vereist dat de configuratie van de JNDI-omgeving een **ConnectionFactory** -object en-doelen kan omzetten.

#### <a name="configure-the-jndi-initialcontext-object"></a>Het JNDI InitialContext-object configureren

De JNDI-omgeving wordt geconfigureerd door een hash-tabel met configuratie-informatie door te geven aan de constructor van de klasse javax.naming.InitialContext. De twee vereiste elementen in de hash-tabel zijn de klassenaam van de eerste context-Factory en de provider-URL. De volgende code laat zien hoe u de JNDI-omgeving configureert voor het gebruik van de QPid eigenschappen bestand op basis van de JNDI-provider met een eigenschappen bestand met de naam **servicebus. Properties**.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Een eenvoudige JMS-toepassing die gebruikmaakt van een Service Bus wachtrij

Het volgende voorbeeld programma verzendt JMS-tekst berichten naar een Service Bus wachtrij met de logische JNDI-naam van de wachtrij en ontvangt de berichten terug.

U hebt toegang tot alle bron code-en configuratie gegevens van de Azure Service Bus-voor [beelden JMS-wachtrij Quick](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)start.

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Geef de **verbindings reeks** op uit het beleid voor gedeelde toegang om de toepassing uit te voeren.
De volgende uitvoer is van het formulier waarop de toepassing wordt uitgevoerd:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Toewijzing van AMQP-toestand en Service Bus-bewerking

Hier ziet u hoe een AMQP-toestand wordt omgezet in een Service Bus bewerking:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Onderwerpen over JMS en Service Bus

Het gebruik van Service Bus-onderwerpen en-abonnementen via de JMS-API biedt eenvoudige mogelijkheden voor het verzenden en ontvangen van berichten. Het is een handige keuze bij het poort gebruiken van toepassingen van andere bericht brokers met JMS-compatibele Api's, zelfs als Service Bus-onderwerpen verschillen van JMS-onderwerpen en een paar aanpassingen vereisen.

Met Service Bus onderwerpen worden berichten gerouteerd naar benoemde, gedeelde en duurzame abonnementen die worden beheerd via de Azure resource management-interface, de opdracht regel Programma's van Azure of de Azure Portal. Elk abonnement staat Maxi maal 2.000 selectie regels toe, die elk een filter voorwaarde kunnen hebben en, voor SQL-filters, ook een transformatie actie voor meta gegevens. Elke overeenkomst voor de filter voorwaarde komt overeen met het invoer bericht dat in het abonnement moet worden gekopieerd.  

Het ontvangen van berichten van abonnementen is identiek aan het ontvangen van berichten uit wacht rijen. Elk abonnement beschikt over een gekoppelde wachtrij met onbestelbare berichten en de mogelijkheid om bericht automatisch door te sturen naar een andere wachtrij of onderwerpen.

Met JMS-onderwerpen kunnen clients dynamisch niet-duurzame en duurzame abonnees maken die optioneel filteren op berichten met bericht selectie vakjes. Deze niet-gedeelde entiteiten worden niet ondersteund door Service Bus. De syntaxis van de SQL-filter regel voor Service Bus is vergelijkbaar met de syntaxis van de bericht kiezer die wordt ondersteund door JMS.

Het JMS-onderwerp Uitgever is compatibel met Service Bus, zoals in dit voor beeld wordt weer gegeven, maar dynamische abonnees niet. De volgende topologie-gerelateerde JMS-Api's worden niet ondersteund met Service Bus.

## <a name="unsupported-features-and-restrictions"></a>Niet-ondersteunde functies en beperkingen

De volgende beperkingen zijn van toepassing wanneer u JMS gebruikt via AMQP 1,0 met Service Bus, namelijk:

* Er is slechts één **MessageProducer** -of **MessageConsumer** -object per sessie toegestaan. Als u meerdere **MessageProducer** -of **MessageConsumer** -objecten in een toepassing wilt maken, maakt u een speciale sessie voor elk object.
* Tijdelijke abonnementen op onderwerpen worden momenteel niet ondersteund.
* **MessageSelector** -objecten worden momenteel niet ondersteund.
* Gedistribueerde trans acties worden niet ondersteund, maar transactionele sessies worden ondersteund.

Service Bus splitst het besturings vlak van het gegevens vlak, waardoor het geen ondersteuning biedt voor verschillende dynamische topologie functies van JMS.

| Niet-ondersteunde methode          | Vervangen door                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Maak een onderwerp-abonnement dat de bericht kiezer poort.                                |
| createDurableConsumer       | Maak een onderwerp-abonnement dat de bericht kiezer poort.                                |
| createSharedConsumer        | Service Bus onderwerpen zijn altijd deelbaar. Zie de sectie ' JMS onderwerpen en Service Bus topics '.                                    |
| createSharedDurableConsumer | Service Bus onderwerpen zijn altijd deelbaar. Zie de sectie ' JMS onderwerpen en Service Bus topics '.                                      |
| createTemporaryTopic        | Maak een onderwerp via de beheer-API, hulpprogram ma's of de portal met *AutoDeleteOnIdle* ingesteld op een verloop periode. |
| createTopic                 | Maak een onderwerp via de beheer-API, hulpprogram ma's of de portal.                                         |
| afmelden                 | Verwijder de API, hulpprogram ma's of portal van het onderwerps beheer.                                            |
| createBrowser               | Niet-ondersteunde. Gebruik de functie Peek () van de Service Bus-API.                         |
| createQueue                 | Maak een wachtrij via de beheer-API, hulpprogram ma's of de portal.                                           | 
| createTemporaryQueue        | Maak een wachtrij via de beheer-API, hulpprogram ma's of de portal met *AutoDeleteOnIdle* ingesteld op een verloop periode. |
| receiveNoWait               | Gebruik de methode receive () die is opgegeven door de Service Bus SDK en geef een zeer lage of nul time-out op. |

## <a name="summary"></a>Samenvatting

In dit artikel vindt u meer informatie over het gebruik van Service Bus Brokered Messaging functies, zoals wacht rijen en onderwerpen over publiceren of abonneren, vanuit Java met behulp van de populaire JMS-API en AMQP 1,0.

U kunt ook Service Bus AMQP 1,0 gebruiken uit andere talen, zoals .NET, C, python en PHP. Onderdelen die zijn gebouwd met behulp van deze verschillende talen kunnen berichten op betrouw bare wijze en met volledige betrouw baarheid uitwisselen met behulp van de AMQP 1,0-ondersteuning in Service Bus.

## <a name="next-steps"></a>Volgende stappen

* [Ondersteuning voor AMQP 1,0 in Azure Service Bus](service-bus-amqp-overview.md)
* [AMQP 1,0 gebruiken met de Service Bus .NET API](./service-bus-amqp-dotnet.md)
* [Service Bus AMQP 1,0-hand leiding voor ontwikkel aars](service-bus-amqp-dotnet.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Java-ontwikkelaars centrum](https://azure.microsoft.com/develop/java/)