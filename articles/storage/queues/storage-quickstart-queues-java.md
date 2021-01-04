---
title: 'Quickstart: Azure Queue Storage-clientbibliotheek v12 - Java'
description: Leer hoe u de Azure Queue Storage-clientbibliotheek v12 voor Java kunt gebruiken om een wachtrij te maken en er berichten aan toe te voegen. Leer vervolgens hoe u berichten uit de wachtrij kunt lezen en verwijderen. U leert ook hoe u een wachtrij kunt verwijderen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 814531adc9dafa524797d0c2674a1e600e407bed
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588213"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Quickstart: Azure Queue Storage-clientbibliotheek v12 voor Java

Ga aan de slag met de Azure Queue Storage-clientbibliotheek v12 voor Java. Azure Queue Storage is een service waarmee u grote aantallen berichten kunt opslaan om later op te halen en te verwerken. Volg deze stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Gebruik de Azure Queue Storage-clientbibliotheek v12 voor Java om:

- Een wachtrij maken
- Berichten aan een wachtrij toevoegen
- Berichten in een wachtrij bekijken
- Een bericht in een wachtrij bijwerken
- Berichten uit een wachtrij ontvangen en verwijderen
- Een wachtrij verwijderen

Aanvullende bronnen:

- [API-referentiedocumentatie](/java/api/overview/azure/storage-queue-readme)
- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Pakket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Voorbeelden](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

- [Java Development Kit (JDK)](/java/azure/jdk/)-versie 8 of hoger
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- Azure Storage-account: [maak een opslagaccount](../common/storage-account-create.md)

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-clientbibliotheek v12 voor Java.

### <a name="create-the-project"></a>Het project maken

Maak een Java-toepassing met de naam `queues-quickstart-v12`.

1. Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) Maven om een nieuwe console-app te maken met de naam `queues-quickstart-v12`. Typ de volgende `mvn`-opdracht om een Hallo wereld!-project in Java te maken.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. De uitvoer van het project zou er ongeveer als volgt moeten uitzien:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Schakel over naar de zojuist gemaakte map `queues-quickstart-v12`.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand `pom.xml` in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep met afhankelijkheden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Ga naar de map `/src/main/java/com/queues/quickstart`
1. Open het bestand `App.java` in de editor
1. Verwijder de `System.out.println("Hello, world");`-instructie
1. Voeg `import`-instructies toe

Hier volgt de code:

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot aan de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een voorraad werk te maken dat asynchroon moet worden verwerkt. Queue Storage biedt drie typen resources:

- Het opslagaccount
- Een wachtrij in het opslagaccount
- Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de Queue Storage-architectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende Java-klassen om te communiceren met deze resources:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): De `QueueClientBuilder`-klasse configureert en instantieert een `QueueClient`-object.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): Met de `QueueServiceClient` kunt u alle wachtrijen in uw opslagaccount beheren.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): Met de `QueueClient`-klasse kunt u een afzonderlijke wachtrij en de bijbehorende berichten beheren en bewerken.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): De `QueueMessageItem`-klasse vertegenwoordigt de afzonderlijke objecten die worden geretourneerd wanneer [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) wordt aangeroepen voor een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-clientbibliotheek voor Java:

- [De verbindingsreeks ophalen](#get-the-connection-string)
- [Een wachtrij maken](#create-a-queue)
- [Berichten aan een wachtrij toevoegen](#add-messages-to-a-queue)
- [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
- [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
- [Berichten uit een wachtrij ontvangen en verwijderen](#receive-and-delete-messages-from-a-queue)
- [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

Met de volgende code wordt de verbindingsreeks voor het opslagaccount opgehaald. De verbindingsreeks is opgeslagen in de omgevingsvariabele die is gemaakt in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in de methode `main`:

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Een wachtrij maken

Verzin een naam voor de nieuwe wachtrij. Met de volgende code wordt een GUID-waarde aan de wachtrijnaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en afbreekstreepjes bevatten en moeten beginnen met een letter of cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet bovendien tussen 3 en 63 tekens lang zijn. Zie [Naamgeving van wachtrijen en metagegevens](/rest/api/storageservices/naming-queues-and-metadata) voor meer informatie over de naamgeving van wachtrijen.

Maak een exemplaar van de [`QueueClient`](/java/api/com.azure.storage.queue.queueclient)-klasse. Roep vervolgens de methode [`Create`](/java/api/com.azure.storage.queue.queueclient.create) aan om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe aan het einde van de `main`-methode:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Berichten aan een wachtrij toevoegen

Met het volgende codefragment worden berichten aan de wachtrij toegevoegd door de methode [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) aan te roepen. Ook wordt een [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) opgeslagen die met een `sendMessage`-aanroep wordt geretourneerd. De resultaat wordt gebruikt om het bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van de `main`-methode:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de methode [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) aan te roepen. Met deze methode worden één of meer berichten vooraan in de wachtrij opgehaald, maar wordt de zichtbaarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van de `main`-methode:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

Werk de inhoud van een bericht bij door de methode [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) aan te roepen. Deze methode kan de time-out voor zichtbaarheid en de inhoud van een bericht wijzigen. De inhoud van het bericht moet een UTF-8-gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Gebruik de `SendMessageResult` die eerder werd opgeslagen in de code om het bericht-ID en de POP-ontvangstbevestiging samen met de nieuwe inhoud voor het bericht door te geven. De bericht-ID en de POP-ontvangstbevestiging identificeren het bericht dat bijgewerkt moet worden.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Berichten uit een wachtrij ontvangen en verwijderen

Download eerder toegevoegde berichten door de methode [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) aan te roepen. De voorbeeldcode verwijdert ook berichten uit de wachtrij nadat ze zijn ontvangen en verwerkt. In dit geval betekent ‘verwerken’ gewoon dat het bericht wordt weergegeven in de console.

De app pauzeert voor gebruikersinvoer door `System.console().readLine();` aan te roepen voordat deze de berichten ontvangt en verwijdert. Verifieer in uw [Azure-portal](https://portal.azure.com) dat de resources correct zijn gemaakt, voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij, zodat u nog een kans hebt om ze te verwerken.

Voeg deze code toe aan het einde van de `main`-methode:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code worden de resources opgeschoond die met de app zijn gemaakt, door de wachtrij te verwijderen met de methode [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete).

Voeg deze code toe aan het einde van de `main`-methode:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en aan een Azure-wachtrij toegevoegd. De code toont de berichten in de wachtrij, haalt ze op en verwijdert ze, en verwijdert uiteindelijk de wachtrij.

Navigeer in uw consolevenster naar de toepassingsmap, bouw de toepassing en voer deze uit.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de volgende opdracht `mvn` uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Wanneer de app pauzeert voordat deze berichten ontvangt, controleert u uw opslagaccount in de [Azure-portal](https://portal.azure.com). Verifieer dat de berichten in de wachtrij staan.

Druk op de `Enter`-toets om de berichten te ontvangen en verwijderen. Druk nogmaals op de `Enter`-toets wanneer u daarom wordt gevraagd, om de wachtrij te verwijderen en de demo te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een wachtrij maakt en berichten eraan toevoegt met behulp van Java-code. Vervolgens leerde u hoe u berichten kunt bekijken, ophalen en verwijderen. Tot slot leerde u hoe u een berichtenwachtrij verwijdert.

Voor zelfstudies, voorbeelden, quickstarts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Azure voor Java-cloudontwikkelaars](/azure/developer/java/)

- Raadpleeg [Azure Queue Storage-clientbibliotheek v12 voor Java - voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue) voor meer Azure Queue Storage-voorbeeld-apps.
