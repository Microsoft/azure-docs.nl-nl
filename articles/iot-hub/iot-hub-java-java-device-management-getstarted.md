---
title: Aan de slag met Azure IoT Hub-apparaatbeheer (Java) | Microsoft Docs
description: Het gebruik van Azure IoT Hub om het opnieuw opstarten van een extern apparaat te starten. U gebruikt de Apparaat-SDK voor Azure IoT voor Java om een gesimuleerde apparaat-app te implementeren die een directe methode bevat en de Azure IoT-service-SDK voor Java om een service-app te implementeren die de directe methode aanroept.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 797c795b6c936e2f117a96fed4b4a483876f1c47
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478077"
---
# <a name="get-started-with-device-management-java"></a>Aan de slag met apparaatbeheer (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze zelfstudie ontdekt u hoe u:

* Gebruik de Azure Portal om een IoT Hub en maak een apparaat-id in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode implementeert om het apparaat opnieuw op te starten. Directe methoden worden aangeroepen vanuit de cloud.

* Maak een app die de directe methode voor opnieuw opstarten aanroept in de gesimuleerde apparaat-app via uw IoT-hub. Deze app controleert vervolgens de gerapporteerde eigenschappen van het apparaat om te zien wanneer de herstartbewerking is voltooid.

Aan het einde van deze zelfstudie hebt u twee Java-console-apps:

**simulated-device**. Deze app:

* Maakt verbinding met uw IoT-hub met de apparaat-id die u eerder hebt gemaakt.

* Ontvangt een aanroep van de directe methode voor opnieuw opstarten.

* Simuleert een fysieke herstart.

* Rapporteert het tijdstip van de laatste keer opnieuw opstarten via een gerapporteerde eigenschap.

**trigger-reboot.** Deze app:

* Roept een directe methode aan in de gesimuleerde apparaat-app.

* Geeft het antwoord weer op de aanroep van de directe methode die is verzonden door het gesimuleerde apparaat.

* Geeft de bijgewerkte gerapporteerde eigenschappen weer.

> [!NOTE]
> Zie Azure [IoT SDK's](iot-hub-devguide-sdks.md)voor meer informatie over de SDK's die u kunt gebruiken om toepassingen te bouwen die kunnen worden uitgevoerd op apparaten en de back-end van uw oplossing.

## <a name="prerequisites"></a>Vereisten

* [Java SE Development Kit 8](/java/azure/jdk/). Zorg ervoor dat u **Java 8 selecteert** onder **Langetermijnondersteuning om** downloads voor JDK 8 te krijgen.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in dit artikel wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT Hub-connection string

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe herstart op het apparaat activeren met behulp van een directe methode

In deze sectie maakt u een Java-console-app die:

1. Hiermee wordt de directe methode voor opnieuw opstarten in de gesimuleerde apparaat-app aanroepen.

2. Geeft het antwoord weer.

3. Pollt de gerapporteerde eigenschappen die vanaf het apparaat zijn verzonden om te bepalen wanneer het opnieuw opstarten is voltooid.

Deze console-app maakt verbinding met uw IoT Hub om de directe methode aan te roepen en de gerapporteerde eigenschappen te lezen.

1. Maak een lege map met de **naam dm-get-started.**

2. Maak in **de map dm-get-started** een Maven-project met de naam **trigger-reboot** met behulp van de volgende opdracht bij de opdrachtprompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Navigeer bij de opdrachtprompt naar de **map trigger-reboot.**

4. Open met behulp van  een teksteditor hetpom.xmlin de map **trigger-reboot** en voeg de volgende afhankelijkheid toe aan het **knooppunt** afhankelijkheden. Met deze afhankelijkheid kunt u het iot-service-client-pakket in uw app gebruiken om te communiceren met uw IoT-hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-service-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Voeg het volgende **build-knooppunt** toe na **het knooppunt afhankelijkheden.** Deze configuratie geeft Maven de instructie om Java 1.8 te gebruiken om de app te bouwen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Sla het bestand **pom.xml** op en sluit het.

7. Open met een teksteditor het bronbestand **trigger-reboot\src\main\java\com\mycompany\app\App.java.**

8. Voeg de volgende **import** instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang `{youriothubconnectionstring}` door de IoT Hub connection string die u eerder hebt gekopieerd in De [IoT-hub connection string:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Als u een thread wilt implementeren die elke 10 seconden de gerapporteerde eigenschappen van de apparaatt twin leest, voegt u de volgende geneste klasse toe aan de **App-klasse:**

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Wijzig de handtekening van de **hoofdmethode** om de volgende uitzondering te geven:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Als u de directe methode voor opnieuw opstarten op het gesimuleerde apparaat wilt aanroepen, vervangt u de code in de **hoofdmethode** door de volgende code:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Als u de thread wilt starten om de gerapporteerde eigenschappen van het gesimuleerde apparaat te peilen, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Als u de app wilt stoppen, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Sla het bestand **trigger-reboot\src\main\java\com\mycompany\app\App.java** op en sluit het.

16. Bouw de **back-end-app trigger-reboot** en corrigeert eventuele fouten. Navigeer bij de opdrachtprompt naar de **map trigger-reboot** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een Java-console-app die een apparaat simuleert. De app luistert naar de aanroep van de directe methode voor opnieuw opstarten vanuit uw IoT-hub en reageert onmiddellijk op die aanroep. De app gaat vervolgens een tijdje in de slaapstand om het opstartproces te simuleren voordat een gerapporteerde eigenschap wordt gebruikt om de **back-end-app** die de trigger opnieuw opstart te melden dat het opnieuw opstarten is voltooid.

1. Maak in **de map dm-get-started** een Maven-project met de naam **simulated-device** met behulp van de volgende opdracht bij de opdrachtprompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer bij de opdrachtprompt naar **de map simulated-device.**

3. Open met behulp van  een teksteditor hetpom.xmlin de map **simulated-device** en voeg de volgende afhankelijkheid toe aan het **knooppunt** afhankelijkheden. Met deze afhankelijkheid kunt u het iot-service-client-pakket in uw app gebruiken om te communiceren met uw IoT-hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-device-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Voeg de volgende afhankelijkheid toe aan het **knooppunt afhankelijkheden.** Met deze afhankelijkheid configureert u een NOP voor de Apache [SLF4J-façade](https://www.slf4j.org/) voor logboekregistratie, die wordt gebruikt door de client-SDK van het apparaat voor het implementeren van logboekregistratie. Deze configuratie is optioneel, maar als u deze weglaten, ziet u mogelijk een waarschuwing in de console wanneer u de app uitvoeren. Zie Logging [in](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) the Samples for the Azure IoT device SDK for Java readme file (Logboekregistratie in de voorbeelden voor het Azure IoT Device SDK voor Java-leesmij-bestand) voor meer informatie over logboekregistratie in de *apparaatclient-SDK.*

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Voeg het volgende **build-knooppunt** toe na **het knooppunt afhankelijkheden.** Deze configuratie geeft Maven de instructie om Java 1.8 te gebruiken om de app te bouwen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Sla het bestand **pom.xml** op en sluit het.

7. Open met behulp van een teksteditor het bronbestand **simulated-device\src\main\java\com\mycompany\app\App.java.**

8. Voeg de volgende **import** instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang door de apparaat-connection string u hebt genoteerd in de sectie Een nieuw apparaat `{yourdeviceconnectionstring}` [registreren in de IoT-hub:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Als u een callback-handler wilt implementeren voor statusgebeurtenissen van de directe methode, voegt u de volgende geneste klasse toe aan de **App-klasse:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Als u een callback-handler wilt implementeren voor statusgebeurtenissen van apparaattwee, voegt u de volgende geneste klasse toe aan de **App-klasse:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Als u een callback-handler wilt implementeren voor eigenschapsgebeurtenissen, voegt u de volgende geneste klasse toe aan de **App-klasse:**

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Als u een thread wilt implementeren om het opnieuw opstarten van het apparaat te simuleren, voegt u de volgende geneste klasse toe aan de **App-klasse.** De thread wordt vijf seconden in de slaapstand geplaatst en stelt vervolgens de **eigenschap lastReboot** reported in:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Als u de directe methode op het apparaat wilt implementeren, voegt u de volgende geneste klasse toe aan de **App-klasse.** Wanneer de gesimuleerde app een  aanroep van de directe methode voor opnieuw opstarten ontvangt, wordt er een bevestiging naar de aanroeper terugverdiend en wordt vervolgens een thread gestart om het opnieuw opstarten te verwerken:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Wijzig de handtekening van de **hoofdmethode** om de volgende uitzonderingen op te geven:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Als u een **DeviceClient wilt instantieren,** vervangt u de code in de **main-methode** door de volgende code:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Als u wilt luisteren naar aanroepen van directe methoden, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Als u de apparaatsimulator wilt afsluiten, voegt u de volgende code toe aan de **hoofdmethode:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Sla het bestand simulated-device\src\main\java\com\mycompany\app\App.java op en sluit het.

20. Bouw de **app simulated-device** en corrigeert eventuele fouten. Navigeer bij de opdrachtprompt naar de **map simulated-device** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de apps uit te voeren.

1. Voer bij een opdrachtprompt in de map **simulated-device** de volgende opdracht uit om te luisteren naar aanroepen van de methode voor opnieuw opstarten vanuit uw IoT-hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub gesimuleerde apparaat-app om te luisteren naar aanroepen van de directe methode voor opnieuw opstarten](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Voer bij een opdrachtprompt in de map **trigger-reboot** de volgende opdracht uit om de methode voor opnieuw opstarten op uw gesimuleerde apparaat aan te roepen vanuit uw IoT-hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-service-app voor het aanroepen van de directe methode voor opnieuw opstarten](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Het gesimuleerde apparaat reageert op de aanroep van de directe methode voor opnieuw opstarten:

    ![Java IoT Hub gesimuleerde apparaat-app reageert op de aanroep van de directe methode](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
