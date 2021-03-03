---
title: Azure Service Bus-wachtrijen gebruiken met Java
description: In deze zelfstudie leert u hoe u Java-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Azure Service Bus-wachtrij.
ms.devlang: Java
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 1f7c5f71e75a1b683433531a9d95905e9a7d81cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729722"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Quickstart: Azure Service Bus-wachtrijen met Java gebruiken om berichten te verzenden en te ontvangen

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
In deze zelfstudie leert u hoe u Java-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Azure Service Bus-wachtrij. 

> [!WARNING]
>  In deze quickstart worden de oude `azure-servicebus`-pakketten gebruikt. Raadpleeg [Berichten verzenden en ontvangen met behulp van `azure-messaging-servicebus`](service-bus-java-how-to-use-queues.md) voor een quickstart die gebruikmaakt van het nieuwste `azure-messaging-servicebus`-pakket. 


## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [De Azure-portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken.
    1. Lees het beknopte **overzicht** van Service Bus-**wachtrijen**. 
    2. Maak een Service Bus-**naamruimte**. 
    3. Haal de **verbindingsreeks** op.
    4. Maak een Service Bus-**wachtrij**.
3. Installeer de [Azure-SDK voor Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Zorg ervoor dat u de [Azure-SDK voor Java][Azure SDK for Java] hebt geïnstalleerd voordat u dit voorbeeld compileert. 

Als u Eclipse gebruikt, kunt u de [Azure-toolkit voor Eclipse][Azure Toolkit for Eclipse] installeren die de Azure-SDK voor Java bevat. U kunt vervolgens de **Microsoft Azure-bibliotheken voor Java** toevoegen aan uw project. Als u IntelliJ gebruikt, raadpleegt u [De Azure-toolkit voor IntelliJ installeren](/azure/developer/java/toolkit-for-intellij/installation). 

![Microsoft Azure-bibliotheken voor Java toevoegen aan uw Eclipse-project](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Voeg bovenaan het Java-bestand de volgende `import`-instructies toe:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u berichten naar een Service Bus-wachtrij wilt verzenden, wordt met uw toepassing een instantie van een **QueueClient**-object gemaakt en worden berichten asynchroon verzonden. De volgende code laat zien hoe u een bericht verzendt voor een wachtrij die via de portal is gemaakt.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

De berichten die worden verzonden naar en ontvangen van Service Bus-wachtrijen, zijn instanties van de [Message](/java/api/com.microsoft.azure.servicebus.message)-klasse. Message-objecten hebben een aantal standaardeigenschappen (zoals Label en TimeToLive), een woordenlijst die wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren en een hoofdtekst met willekeurige toepassingsgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door elk serialiseerbaar object door te geven aan de constructor van het Message-object waarna de juiste serialisatiefunctie wordt gebruikt om het object te serialiseren. U kunt ook een **java.IO.InputStream**-object opgeven.


Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
De primaire manier om berichten van een wachtrij te ontvangen is het gebruik van een **ServiceBusContract**-object. Ontvangen berichten kunnen in twee verschillende modi werken: **ReceiveAndDelete** en **PeekLock**.

Wanneer u de **ReceiveAndDelete**-modus gebruikt, wordt het ontvangen in één bewerking uitgevoerd; dat wil zeggen als Service Bus een leesaanvraag voor een bericht in een wachtrij ontvangt, wordt voor het bericht aangegeven dat het wordt verbruikt en wordt het bericht naar de toepassing geretourneerd. De **ReceiveAndDelete**-modus (de standaardmodus) is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt.
Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, ontbreekt het bericht dat voor het vastlopen is verbruikt wanneer de toepassing opnieuw wordt gestart en het verbruik van berichten opnieuw begint.

In de **PeekLock**-modus wordt de ontvangst een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen ontbrekende berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of het bericht veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door **complete()** aan te roepen voor het ontvangen bericht. Wanneer Service Bus de aanroep **complete()** waarneemt, wordt voor het bericht aangegeven dat het wordt verbruikt en wordt het bericht uit de wachtrij verwijderd. 

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met de modus **PeekLock** (niet de standaardmodus). In het onderstaande voorbeeld wordt gebruikgemaakt van het callbackmodel met een geregistreerde berichtenhandler en worden berichten verwerkt die aankomen bij de `TestQueue`. In deze modus wordt **complete()** automatisch aangeroepen wanneer de callback normaal wordt geretourneerd en wordt **abandon()** aangeroepen als de callback een uitzondering genereert. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode **abandon()** voor het clientobject worden aangeroepen met het vergrendelingstoken van het ontvangen bericht dat wordt verkregen via **getLockToken()** . Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

In het geval dat de toepassing is vastgelopen na het verwerken van het bericht, maar voordat de aanvraag **complete()** is uitgegeven, wordt het bericht opnieuw bij de toepassing bezorgd wanneer de toepassing opnieuw wordt gestart. Dit wordt vaak *Ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de **getMessageId**-methode van het bericht die gelijk blijft bij meerdere bezorgingspogingen.

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
U vindt Java-voorbeelden in GitHub in de [`azure-service-bus`opslagplaats](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
