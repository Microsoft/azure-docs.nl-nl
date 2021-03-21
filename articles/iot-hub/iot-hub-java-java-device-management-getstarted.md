---
title: Aan de slag met Azure IoT Hub Device Management (Java) | Microsoft Docs
description: Azure IoT Hub Apparaatbeheer gebruiken om het opnieuw opstarten van een extern apparaat te initiëren. U gebruikt de Azure IoT Device SDK voor Java om een gesimuleerde apparaat-app te implementeren die een directe methode en de Azure IoT Service SDK voor Java bevat om een service-app te implementeren die de directe methode aanroept.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: f05e1a458bc83fe4042c4b6cf35d9aa2095868ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217955"
---
# <a name="get-started-with-device-management-java"></a>Aan de slag met Apparaatbeheer (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze zelfstudie ontdekt u hoe u:

* Gebruik de Azure Portal voor het maken van een IoT Hub en het maken van een apparaat-id in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode implementeert om het apparaat opnieuw op te starten. Directe methoden worden vanuit de Cloud aangeroepen.

* Maak een app die de directe methode voor opnieuw opstarten aanroept in de gesimuleerde apparaat-app via uw IoT-hub. Deze app controleert vervolgens de gerapporteerde eigenschappen van het apparaat om te zien wanneer de opstart bewerking is voltooid.

Aan het einde van deze zelf studie hebt u twee Java Console-apps:

**gesimuleerd apparaat**. Deze app:

* Maakt verbinding met uw IoT-hub met de apparaat-id die u eerder hebt gemaakt.

* Ontvangt een direct-aanroepen methode voor het opnieuw opstarten.

* Simuleert fysiek opnieuw opstarten.

* Hiermee wordt de tijd gerapporteerd waarop de laatste keer opnieuw is opgestart via een gerapporteerde eigenschap.

**trigger-opnieuw opstarten**. Deze app:

* Roept een directe methode aan in de gesimuleerde apparaat-app.

* Hiermee geeft u het antwoord op de directe methode aanroep verzonden door het gesimuleerde apparaat.

* Hiermee worden de bijgewerkte gerapporteerde eigenschappen weer gegeven.

> [!NOTE]
> Voor informatie over de Sdk's die u kunt gebruiken om toepassingen te bouwen die worden uitgevoerd op apparaten en de back-end van uw oplossing, raadpleegt u [Azure IOT sdk's](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Vereisten

* [Java SE Development Kit 8](/java/azure/jdk/). Zorg ervoor dat u **Java 8** selecteert onder **lange termijn ondersteuning** om down loads voor JDK 8 te downloaden.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe keer opnieuw opstarten op het apparaat activeren met behulp van een directe methode

In deze sectie maakt u een Java-Console-app die:

1. Roept de methode voor opnieuw opstarten direct aan in de app gesimuleerde apparaten.

2. Hiermee wordt het antwoord weer gegeven.

3. Hiermee worden de gerapporteerde eigenschappen die van het apparaat zijn verzonden, gecontroleerd om te bepalen wanneer het opnieuw opstarten is voltooid.

Met deze console-app maakt u verbinding met uw IoT Hub om de directe methode aan te roepen en de gerapporteerde eigenschappen te lezen.

1. Maak een lege map met de naam **DM-Get-Started**.

2. Maak in de map **DM-Get-Started** een Maven-project met de naam **trigger-reboot** met de volgende opdracht bij de opdracht prompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Ga bij de opdracht prompt naar de map **trigger-reboot** .

4. Open met een tekst editor het **pom.xml** bestand in de map **trigger-reboot** en voeg de volgende afhankelijkheden toe aan het knoop punt **afhankelijkheden** . Met deze afhankelijkheid kunt u het IOT-service-client-pakket in uw app gebruiken om te communiceren met uw IoT-hub:

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

5. Voeg het volgende **Build** -knoop punt toe na het knoop punt **afhankelijkheden** . Deze configuratie zorgt ervoor dat maven Java 1,8 wordt gebruikt om de app te bouwen:

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

7. Open met een tekst editor het bron bestand **trigger-reboot\src\main\java\com\mycompany\app\App.java** .

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

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang door `{youriothubconnectionstring}` de IoT Hub Connection String u eerder hebt gekopieerd in [de IOT Hub-Connection String ophalen](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Voeg de volgende geneste klasse toe aan de **app** -klasse om een thread te implementeren die de gerapporteerde eigenschappen van het apparaat twee keer elke tien seconden leest:

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

11. Wijzig de hand tekening van de methode **Main** om de volgende uitzonde ring te genereren:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Als u de methode voor het opnieuw opstarten direct wilt aanroepen op het gesimuleerde apparaat, vervangt u de code in de methode **Main** door de volgende code:

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

13. Voeg de volgende code toe aan de methode **Main** om de thread te starten om de gerapporteerde eigenschappen van het gesimuleerde apparaat te controleren:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Als u de app wilt stoppen, voegt u de volgende code toe aan de methode **Main** :

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Sla het **trigger-reboot\src\main\java\com\mycompany\app\App.java** -bestand op en sluit het.

16. Bouw de back-end-app voor het starten van de **trigger** en corrigeer eventuele fouten. Ga bij de opdracht prompt naar de map **trigger-reboot** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een Java-Console-app die een apparaat simuleert. De app luistert naar de herstart directe methode aanroep van uw IoT-hub en reageert onmiddellijk op die aanroep. De app wordt vervolgens gedurende een tijdje geslapen om het opstart proces te simuleren voordat er een gerapporteerde eigenschap wordt gebruikt om de back-end-app voor het starten van de **trigger** te melden dat de computer opnieuw moet worden opgestart.

1. Maak in de map **DM-Get-Started** een Maven-project met de naam **gesimuleerd apparaat** met behulp van de volgende opdracht bij de opdracht prompt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Ga bij de opdracht prompt naar de map **met gesimuleerde apparaten** .

3. Open met een tekst editor het **pom.xml** -bestand in de map **gesimuleerde apparaten** en voeg de volgende afhankelijkheden toe aan het knoop punt **afhankelijkheden** . Met deze afhankelijkheid kunt u het IOT-service-client-pakket in uw app gebruiken om te communiceren met uw IoT-hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-device-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Voeg de volgende afhankelijkheden toe aan het knoop punt **afhankelijkheden** . Met deze afhankelijkheid wordt een NOP geconfigureerd voor de Apache [SLF4J](https://www.slf4j.org/) logging-gevel, die wordt gebruikt door de client-SDK voor het implementeren van logboek registratie. Deze configuratie is optioneel, maar als u deze weglaat, wordt er mogelijk een waarschuwing weer gegeven in de console wanneer u de app uitvoert. Zie [logboek registratie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) in de *voor beelden voor het* leesmij-bestand voor Azure IOT Device SDK voor Java voor meer informatie over logboek registratie in de client-SDK.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Voeg het volgende **Build** -knoop punt toe na het knoop punt **afhankelijkheden** . Deze configuratie zorgt ervoor dat maven Java 1,8 wordt gebruikt om de app te bouwen:

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

7. Open met een tekst editor het bron bestand **simulated-device\src\main\java\com\mycompany\app\App.java** .

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

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang door `{yourdeviceconnectionstring}` het apparaat Connection String u hebt genoteerd in het gedeelte [een nieuw apparaat registreren in de IOT-hub](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Als u een call back-handler voor directe methode status gebeurtenissen wilt implementeren, voegt u de volgende geneste klasse toe aan de **app** -klasse:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Als u een call back-handler wilt implementeren voor gebeurtenis gebeurtenissen met een dubbele status, voegt u de volgende geneste klasse toe aan de **app** -klasse:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Als u een call back-handler voor eigenschaps gebeurtenissen wilt implementeren, voegt u de volgende geneste klasse toe aan de **app** -klasse:

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

13. Als u een thread wilt implementeren om het opnieuw opstarten van het apparaat te simuleren, voegt u de volgende geneste klasse toe aan de **app** -klasse. De thread slaapt vijf seconden en stelt vervolgens de gerapporteerde eigenschap **lastReboot** in:

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

14. Als u de directe methode op het apparaat wilt implementeren, voegt u de volgende geneste klasse toe aan de **app** -klasse. Wanneer de gesimuleerde app een aanroep van de direct-methode voor **opnieuw opstarten** ontvangt, wordt er een bevestiging voor de aanroeper geretourneerd en wordt vervolgens een thread gestart voor het verwerken van de herstart:

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

15. Wijzig de hand tekening van de methode **Main** om de volgende uitzonde ringen te genereren:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Als u een **DeviceClient** wilt instantiëren, vervangt u de code in de methode **Main** door de volgende code:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Als u wilt Luis teren naar directe-methode aanroepen, voegt u de volgende code toe aan de methode **Main** :

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

18. Als u de Device Simulator wilt afsluiten, voegt u de volgende code toe aan de methode **Main** :

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Sla het simulated-device\src\main\java\com\mycompany\app\App.java-bestand op en sluit het.

20. Bouw de **gesimuleerde apparaat-** app op en corrigeer eventuele fouten. Ga bij de opdracht prompt naar de map **gesimuleerd apparaat** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de apps uit te voeren.

1. Voer bij een opdracht prompt in de map met **gesimuleerde apparaten** de volgende opdracht uit om te beginnen met Luis teren naar de aanroepen van de methode reboot van uw IOT-hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub gesimuleerde apparaat-app om te Luis teren naar directe methode aanroepen voor opnieuw opstarten](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Voer bij een opdracht prompt in de map **trigger-reboot** de volgende opdracht uit om de methode voor opnieuw opstarten aan te roepen op uw gesimuleerde apparaat vanuit uw IOT-hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub service-app voor het aanroepen van de methode voor opnieuw opstarten direct](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Het gesimuleerde apparaat reageert op de methode voor direct opnieuw opstarten:

    ![Java IoT Hub gesimuleerde apparaat-app reageert op de aanroep van de directe methode](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]