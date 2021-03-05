---
title: Cloud-naar-apparaat-berichten met Azure IoT Hub (Java) | Microsoft Docs
description: Cloud-naar-apparaat-berichten verzenden naar een apparaat vanuit een Azure IoT-hub met behulp van de Azure IoT Sdk's voor Java. U wijzigt een gesimuleerde apparaat-app om Cloud-naar-apparaat-berichten te ontvangen en een back-end-app te wijzigen om de Cloud-naar-apparaat-berichten te verzenden.
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
ms.openlocfilehash: 5ae1850add94d83278b0fe1905dfa6e53c71fc8e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217887"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Cloud-naar-apparaat-berichten verzenden met IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die zorgt voor betrouw bare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing. Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-java.md) Quick Start laat zien hoe u een IOT-hub kunt maken, een apparaat-id kunt inrichten en een gesimuleerde apparaat-app kunt coderen die apparaat-naar-Cloud-berichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelf studie is gebaseerd op het [verzenden van telemetrie van een apparaat naar een IOT-hub](quickstart-send-telemetry-java.md). U ziet hoe u het volgende kunt doen:

* Via de back-end van uw oplossing kunt u Cloud-naar-apparaat-berichten verzenden naar één apparaat via IoT Hub.

* Cloud-naar-apparaat-berichten op een apparaat ontvangen.

* Van de back-end van uw oplossing, aanvraag bezorgings bevestiging (*feedback*) aanvragen voor berichten die worden verzonden naar een apparaat vanuit IOT hub.

Meer informatie over [Cloud-naar-apparaat-berichten vindt u in de hand leiding voor IOT hub ontwikkel aars](iot-hub-devguide-messaging.md).

Aan het einde van deze zelf studie voert u twee Java Console-apps uit:

* **gesimuleerd:** een gewijzigde versie van de app die is gemaakt in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-java.md), die verbinding maakt met uw IOT-hub en Cloud-naar-apparaat-berichten ontvangt.

* **Send-C2D: berichten**, waarmee een Cloud-naar-apparaat-bericht wordt verzonden naar de gesimuleerde apparaat-app via IOT hub, en vervolgens de ontvangst bevestiging ontvangt.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel platformen en talen (waaronder C, Java, python en Java script) via Azure IoT-apparaat-Sdk's. Zie het [Azure IOT-ontwikkelaars centrum](https://azure.microsoft.com/develop/iot)voor stapsgewijze instructies voor het verbinden van uw apparaat met de code van deze zelf studie en over het algemeen tot Azure IOT hub.

## <a name="prerequisites"></a>Vereisten

* Een volledige werkende versie van het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-java.md) -Snelstartgids of het [configureren van bericht routering met IOT hub](tutorial-routing.md) zelf studie.

* [Java SE Development Kit 8](/java/azure/jdk/). Zorg ervoor dat u **Java 8** selecteert onder **lange termijn ondersteuning** om down loads voor JDK 8 te downloaden.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie wijzigt u de gesimuleerde apparaat-app die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-java.md) om Cloud-naar-apparaat-berichten van de IOT-hub te ontvangen.

1. Open het bestand simulated-device\src\main\java\com\mycompany\app\App.java met een teksteditor.

2. Voeg de volgende **MessageCallback** -klasse toe als een geneste klasse binnen de **app** -klasse. De methode **Execute** wordt aangeroepen wanneer het apparaat een bericht ontvangt van IOT hub. In dit voor beeld ontvangt het apparaat altijd een melding van de IoT-hub dat het bericht is voltooid:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Wijzig de methode **Main** om een **AppMessageCallback** -exemplaar te maken en roep de **setMessageCallback** -methode aan voordat de client wordt geopend als volgt:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. Als u de app **simulated-device** wilt maken met behulp van Maven, geeft u de volgende opdracht op in het opdrachtvenster in de map simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

De `execute` methode in de `AppMessageCallback` klasse retourneert `IotHubMessageResult.COMPLETE` . Hiermee wordt IoT Hub gemeld dat het bericht is verwerkt en dat het bericht veilig kan worden verwijderd uit de wachtrij van het apparaat. Het apparaat moet deze waarde Retour neren wanneer de verwerking is voltooid, ongeacht het protocol dat wordt gebruikt.

Met AMQP en HTTPS, maar niet MQTT, kan het apparaat ook:

* Een bericht afbreken, wat leidt tot IoT Hub het bericht in de wachtrij van het apparaat behouden voor toekomstig gebruik.
* Een bericht afwijzen waarmee het bericht permanent wordt verwijderd uit de wachtrij van de apparaten.

Als er iets gebeurt dat ervoor zorgt dat het apparaat het bericht niet kan volt ooien, afbreken of afwijzen, wordt IoT Hub na een vaste time-outperiode het bericht in de wachtrij geplaatst voor bezorging. Daarom moet de logica voor bericht verwerking in de apparaat-app worden *idempotent*, zodat hetzelfde bericht meerdere keren wordt ontvangen.

Zie [Cloud-naar-apparaat-berichten verzenden vanuit een IOT-hub](iot-hub-devguide-messages-c2d.md)voor meer informatie over hoe IOT hub Cloud-naar-apparaat-berichten verwerkt, met inbegrip van de details van de Cloud-naar-apparaat-bericht levenscyclus.

> [!NOTE]
> Als u HTTPS gebruikt in plaats van MQTT of AMQP als Trans Port, controleert het **DeviceClient** -exemplaar voor berichten van IOT hub zelden (mini maal elke 25 minuten). Zie [communicatie richtlijnen voor Cloud-naar-apparaat](iot-hub-devguide-c2d-guidance.md) en [Kies een communicatie protocol](iot-hub-devguide-protocols.md)voor meer informatie over de verschillen tussen de MQTT-, AMQP-en HTTPS-ondersteuning.

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om Cloud-naar-apparaat-berichten te verzenden via de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-java.md). Als u Cloud-naar-apparaat-berichten wilt verzenden, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een Cloud-naar-apparaat-bericht verzenden

In deze sectie maakt u een Java-Console-app die Cloud-naar-apparaat-berichten naar de gesimuleerde apparaat-app verzendt. U hebt de apparaat-ID van het apparaat dat u hebt toegevoegd in de [telemetrie van een apparaat verzenden naar een IOT hub](quickstart-send-telemetry-java.md) Quick Start. U hebt ook de IoT hub-connection string nodig die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

1. Maak een Maven-project met de naam **Send-C2D-messages** met behulp van de volgende opdracht bij de opdracht prompt. Opmerking deze opdracht is een enkele, lange opdracht:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Ga bij de opdracht prompt naar de nieuwe map Send-C2D-messages.

3. Open met een tekst editor het pom.xml-bestand in de map Send-C2D-messages en voeg de volgende afhankelijkheid toe aan het knoop punt **afhankelijkheden** . Door de afhankelijkheid toe te voegen, kunt u het **iothub-Java-service-client-** pakket in uw toepassing gebruiken om te communiceren met uw IOT hub-service:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-service-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Sla het bestand pom.xml op en sluit het af.

5. Open het send-c2d-messages\src\main\java\com\mycompany\app\App.java-bestand met behulp van een tekst editor.

6. Voeg de volgende **import** instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Voeg de volgende variabelen op klasseniveau toe aan de **app** -klasse, waarbij **{yourhubconnectionstring}** en **{yourdeviceid}** worden vervangen door de waarden die u eerder hebt genoteerd:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Vervang de methode **Main** door de volgende code. Deze code maakt verbinding met uw IoT-hub, verzendt een bericht naar uw apparaat en wacht op een bevestiging dat het apparaat het bericht heeft ontvangen en verwerkt:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Voor het gemak implementeert deze zelf studie geen beleid voor opnieuw proberen. In productie code moet u beleid voor opnieuw proberen implementeren (zoals exponentiële uitstel), zoals wordt voorgesteld in het artikel, [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).

9. Als u de app **simulated-device** wilt maken met behulp van Maven, geeft u de volgende opdracht op in het opdrachtvenster in de map simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij een opdracht prompt in de map gesimuleerde apparaten de volgende opdracht uit om telemetrie te verzenden naar uw IoT-hub en om te Luis teren naar Cloud-naar-apparaat-berichten die vanuit uw hub worden verzonden:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![De app voor een gesimuleerd apparaat uitvoeren](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Voer bij een opdracht prompt in de map Send-C2D-berichten de volgende opdracht uit om een Cloud-naar-apparaat-bericht te verzenden en te wachten op een feedback bevestiging:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Voer de opdracht uit om het Cloud-naar-apparaat-bericht te verzenden](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Cloud-naar-apparaat-berichten kunt verzenden en ontvangen.

Zie [Azure IOT-oplossings Accelerators](https://azure.microsoft.com/documentation/suites/iot-suite/)voor een overzicht van de volledige end-to-end-oplossingen die gebruikmaken van IOT hub.

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IOT hub.