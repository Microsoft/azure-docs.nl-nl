---
title: Bestanden van apparaten uploaden naar Azure IoT Hub met Java | Microsoft Docs
description: Het uploaden van bestanden van een apparaat naar de Cloud met Azure IoT Device SDK voor Java. Geüploade bestanden worden opgeslagen in een Azure Storage-BLOB-container.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 3529361cacf0890b7c4752bbd745a9240020b4f3
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217819"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In deze zelf studie wordt gebruikgemaakt van de code in de IoT Hub zelf studie [Cloud-naar-apparaat-berichten verzenden](iot-hub-java-java-c2d.md) om u te laten zien hoe u de functies voor het [uploaden van bestanden van IOT hub](iot-hub-devguide-file-upload.md) kunt gebruiken om een bestand te uploaden naar [Azure Blob Storage](../storage/index.yml). In deze zelfstudie leert u het volgende:

* Een apparaat veilig voorzien van een Azure Blob-URI voor het uploaden van een bestand.

* Gebruik de IoT Hub bestands upload meldingen om de verwerking van het bestand in de back-end van de app te activeren.

Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-java.md) Quick Start en het [verzenden van Cloud-naar-apparaat-berichten met IOT hub](iot-hub-java-java-c2d.md) zelf studie geven de basis functionaliteit van het apparaat-naar-Cloud-en Cloud-naar-apparaat-bericht van IOT hub weer. In de zelf studie [bericht routering configureren met IOT hub](tutorial-routing.md) wordt beschreven hoe u apparaat-naar-Cloud-berichten betrouwbaar kunt opslaan in Azure Blob-opslag. In sommige gevallen kunt u de gegevens die uw apparaten verzenden echter niet eenvoudig toewijzen aan de relatief kleine apparaat-naar-Cloud-berichten die IoT Hub accepteren. Bijvoorbeeld:

* Grote bestanden die installatie kopieën bevatten
* Video's
* Bemonsterde trillings gegevens met hoge frequentie
* Een vorm van voorverwerkte gegevens.

Deze bestanden worden meestal batch verwerkt in de Cloud met behulp van hulpprogram ma's als [Azure Data Factory](../data-factory/introduction.md) of de [Hadoop](../hdinsight/index.yml) -stack. Wanneer u bestanden van een apparaat nodig hebt, kunt u nog steeds gebruikmaken van de beveiliging en betrouw baarheid van IoT Hub.

Aan het einde van deze zelf studie voert u twee Java Console-apps uit:

* **gesimuleerd:** er is een aangepaste versie van de app gemaakt in [Cloud-naar-apparaat-berichten verzenden met IOT hub] zelf studie. Met deze app wordt een bestand geüpload naar Storage met behulp van een SAS-URI van uw IoT-hub.

* **read-file-upload-melding**, waarmee berichten over het uploaden van bestanden van uw IOT-hub worden ontvangen.

> [!NOTE]
> IoT Hub ondersteunt veel platformen en talen (waaronder C, .NET en Java script) via Azure IoT-apparaat-Sdk's. Raadpleeg het [Azure IOT-ontwikkelaars centrum](https://azure.microsoft.com/develop/iot) voor stapsgewijze instructies voor het verbinden van uw apparaat met Azure IOT hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Java SE Development Kit 8](/java/azure/jdk/). Zorg ervoor dat u **Java 8** selecteert onder **lange termijn ondersteuning** om down loads voor JDK 8 te downloaden.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie wijzigt u de apparaat-app die u hebt gemaakt in [Cloud-naar-apparaat-berichten verzenden met IOT hub](iot-hub-java-java-c2d.md) om een bestand te uploaden naar IOT hub.

1. Kopieer een afbeeldings bestand naar de `simulated-device` map en wijzig de naam ervan `myimage.png` .

2. Open het bestand met behulp van een tekst editor `simulated-device\src\main\java\com\mycompany\app\App.java` .

3. Voeg de variabele-declaratie toe aan de **app** -klasse:

    ```java
    private static String fileName = "myimage.png";
    ```

4. Voeg de volgende geneste klasse toe aan de **app** -klasse om de upload status berichten voor het uploaden van bestanden te verwerken:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Als u afbeeldingen naar IoT Hub wilt uploaden, voegt u de volgende methode toe aan de **app** -klasse om installatie kopieën naar IOT hub te uploaden:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Wijzig de methode **Main** zo dat de **Upload File** wordt aangeroepen, zoals wordt weer gegeven in het volgende code fragment:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Gebruik de volgende opdracht om de **gesimuleerde apparaat-** app te bouwen en te controleren op fouten:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om meldings berichten over het uploaden van bestanden te ontvangen van de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-java.md). Als u meldings berichten over het uploaden van bestanden wilt ontvangen, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Een melding over het uploaden van een bestand ontvangen

In deze sectie maakt u een Java-Console-app die meldings berichten voor het uploaden van bestanden ontvangt van IoT Hub.

1. Maak een Maven-project met de naam **read-file-upload-melding** met de volgende opdracht bij de opdracht prompt. Opmerking deze opdracht is een enkele, lange opdracht:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Ga bij de opdracht prompt naar de nieuwe `read-file-upload-notification` map.

3. Open het `pom.xml` bestand in de map met een tekst editor `read-file-upload-notification` en voeg de volgende afhankelijkheden toe aan het knoop punt **afhankelijkheden** . Door de afhankelijkheid toe te voegen, kunt u het **iothub-Java-service-client-** pakket in uw toepassing gebruiken om te communiceren met uw IOT hub-service:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-service-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Sla het bestand `pom.xml` op en sluit het vervolgens.

5. Open het bestand met behulp van een tekst editor `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` .

6. Voeg de volgende **import** instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang de `{Your IoT Hub connection string}` waarde van de tijdelijke aanduiding door de IOT hub-Connection String die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string):

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Als u informatie over het uploaden van bestanden naar de-console wilt afdrukken, voegt u de volgende geneste klasse toe aan de **app** -klasse:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Als u de thread wilt starten die luistert naar meldingen over het uploaden van bestanden, voegt u de volgende code toe aan de methode **Main** :

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Sla het bestand `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` op en sluit het vervolgens.

11. Gebruik de volgende opdracht om de app **read-file-upload-melding** te maken en te controleren op fouten:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

Voer de volgende opdracht uit vanaf een opdracht prompt in de `read-file-upload-notification` map:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Voer de volgende opdracht uit vanaf een opdracht prompt in de `simulated-device` map:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

In de volgende scherm afbeelding ziet u de uitvoer van de **gesimuleerde apparaat-** app:

![Uitvoer van gesimuleerde apparaat-app](media/iot-hub-java-java-upload/simulated-device.png)

In de volgende scherm afbeelding ziet u de uitvoer van de app **read-file-upload-notification** :

![Uitvoer van een read-file-upload-meldings-app](media/iot-hub-java-java-upload/read-file-upload-notification.png)

U kunt de portal gebruiken om het geüploade bestand weer te geven in de opslag container die u hebt geconfigureerd:

![Bestand geüpload](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de functies voor het uploaden van bestanden van IoT Hub kunt gebruiken om Bestands uploads van apparaten te vereenvoudigen. U kunt IoT hub-functies en-scenario's blijven verkennen met de volgende artikelen:

* [Een IoT-hub maken via een programma](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Een apparaat simuleren met IoT Edge](../iot-edge/quickstart-linux.md)