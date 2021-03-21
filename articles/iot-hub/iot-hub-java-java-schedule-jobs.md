---
title: Taken plannen met Azure IoT Hub (Java) | Microsoft Docs
description: Een Azure IoT Hub-taak plannen om een directe methode aan te roepen en een gewenste eigenschap op meerdere apparaten in te stellen. U gebruikt de Azure IoT Device SDK voor Java voor het implementeren van de gesimuleerde apparaat-apps en de Azure IoT Service SDK voor Java om een service-app te implementeren om de taak uit te voeren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 3e98cfc2d8c7fb8d40c8565a1c620f123ce171ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217836"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Taken plannen en uitzenden (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Gebruik Azure IoT Hub om taken te plannen en bij te houden waarmee miljoenen apparaten worden bijgewerkt. Taken gebruiken voor:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

Een taak verpakt een van deze acties en houdt de uitvoering bij van een set apparaten. Met een dubbele query van een apparaat wordt de set apparaten gedefinieerd waarop de taak wordt uitgevoerd. Een back-end-app kan bijvoorbeeld een taak gebruiken om een directe methode aan te roepen op 10.000-apparaten die de apparaten opnieuw opstarten. U geeft de set apparaten met een dubbele query voor een apparaat op en plant de taak op een later tijdstip. De taak houdt de voortgang bij wanneer elk apparaat wordt ontvangen en de methode voor opnieuw opstarten direct wordt uitgevoerd.

Zie voor meer informatie over elk van deze mogelijkheden:

* Dubbele en eigenschappen van apparaat: [aan de slag met apparaat apparaatdubbels](iot-hub-java-java-twin-getstarted.md)

* Directe methoden: [IOT hub ontwikkelaars handleiding-directe methoden](iot-hub-devguide-direct-methods.md) en [zelf studie: directe methoden gebruiken](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze zelfstudie ontdekt u hoe u:

* Maak een apparaat-app die een directe methode met de naam **lockDoor** implementeert. De apparaat-app ontvangt ook gewenste wijzigingen in de eigenschappen van de back-end-app.

* Maak een back-end-app die een taak maakt om de **lockDoor** direct-methode aan te roepen op meerdere apparaten. Een andere taak verzendt gewenste eigenschaps updates naar meerdere apparaten.

Aan het einde van deze zelf studie hebt u een app voor het maken van een Java-Console en een back-end-app van een Java-Console:

**gesimuleerd: apparaat** dat verbinding maakt met uw IOT-hub, implementeert de **lockDoor** direct-methode en verwerkt de gewenste eigenschaps wijzigingen.

**Schedule-Jobs** die gebruikmaken van taken om de **lockDoor** direct-methode aan te roepen en de dubbele gewenste eigenschappen van het apparaat op meerdere apparaten bij te werken.

> [!NOTE]
> Het artikel [Azure IOT sdk's](iot-hub-devguide-sdks.md) bevat informatie over de Azure IOT-sdk's die u kunt gebruiken om zowel apparaat-als back-end-apps te bouwen.

## <a name="prerequisites"></a>Vereisten

* [Java SE Development Kit 8](/java/azure/jdk/). Zorg ervoor dat u **Java 8** selecteert onder **lange termijn ondersteuning** om down loads voor JDK 8 te downloaden.

* [Maven 3](https://maven.apache.org/download.cgi)

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

U kunt ook het hulp programma [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om een apparaat toe te voegen aan uw IOT-hub.

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een Java-Console-app die gebruikmaakt van taken:

* De **lockDoor** direct-methode aanroepen op meerdere apparaten.

* Gewenste eigenschappen naar meerdere apparaten verzenden.

De app maken:

1. Maak op de ontwikkel computer een lege map met de naam **IOT-Java-Schedule-Jobs**.

2. Maak in de map **IOT-Java-Schedule-Jobs** een Maven-project met de naam **Schedule-Jobs** met de volgende opdracht bij de opdracht prompt. Let op: dit is één enkele, lange opdracht:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. Ga bij de opdracht prompt naar de map **Schedule-taken** .

4. Open met een tekst editor het **pom.xml** -bestand in de map **Schedule Jobs** en voeg de volgende afhankelijkheden toe aan het knoop punt **afhankelijkheden** . Met deze afhankelijkheid kunt u het **IOT-service-client-** pakket in uw app gebruiken om te communiceren met uw IOT-hub:

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

7. Open het **Schedule-jobs\src\main\java\com\mycompany\app\App.java** -bestand met behulp van een tekst editor.

8. Voeg de volgende **import** instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang door `{youriothubconnectionstring}` uw IOT hub-Connection String die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Voeg de volgende methode toe aan de **app** -klasse om een taak te plannen voor het bijwerken van de gewenste eigenschappen voor **bouwen** en **vloeren** op het apparaat dubbele:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Als u een taak wilt plannen om de **lockDoor** -methode aan te roepen, voegt u de volgende methode toe aan de **app** -klasse:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Als u een taak wilt bewaken, voegt u de volgende methode toe aan de **app** -klasse:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Als u een query wilt uitvoeren voor de details van de taken die u hebt uitgevoerd, voegt u de volgende methode toe:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Werk de hand tekening van de **hoofd** methode bij zodat deze de volgende `throws` component bevat:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Als u twee taken opeenvolgend wilt uitvoeren en controleren, vervangt u de code in de methode **Main** door de volgende code:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Het **Schedule-jobs\src\main\java\com\mycompany\app\App.java** -bestand opslaan en sluiten

17. Bouw de app **Schedule Jobs** en corrigeer eventuele fouten. Ga bij de opdracht prompt naar de map **Schedule Jobs** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Een apparaat-app maken

In deze sectie maakt u een Java-Console-app die de gewenste eigenschappen van IoT Hub verwerkt en de directe methode aanroep implementeert.

1. Maak in de map **IOT-Java-Schedule-Jobs** een Maven-project met de naam **gesimuleerd apparaat** met behulp van de volgende opdracht bij de opdracht prompt. Let op: dit is één enkele, lange opdracht:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. Ga bij de opdracht prompt naar de map **met gesimuleerde apparaten** .

3. Open met een tekst editor het **pom.xml** -bestand in de map **gesimuleerde apparaten** en voeg de volgende afhankelijkheden toe aan het knoop punt **afhankelijkheden** . Met deze afhankelijkheid kunt u het **IOT-apparaat-client-** pakket in uw app gebruiken om te communiceren met uw IOT-hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > U vindt de meest recente versie van **iot-device-client** met [Maven zoeken](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Voeg de volgende afhankelijkheden toe aan het knoop punt **afhankelijkheden** . Met deze afhankelijkheid wordt een NOP geconfigureerd voor de Apache [SLF4J](https://www.slf4j.org/) logging-gevel, die wordt gebruikt door de client-SDK voor het implementeren van logboek registratie. Deze configuratie is optioneel, maar als u deze weglaat, wordt er mogelijk een waarschuwing weer gegeven in de console wanneer u de app uitvoert. Zie [logboek registratie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)in de *voor beelden voor het* leesmij-bestand voor Azure IOT Device SDK voor Java voor meer informatie over logboek registratie in de client-SDK.

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

7. Open het **simulated-device\src\main\java\com\mycompany\app\App.java** -bestand met behulp van een tekst editor.

8. Voeg de volgende **import** instructies toe aan het bestand:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang door `{yourdeviceconnectionstring}` het apparaat Connection String dat u eerder hebt gekopieerd in het gedeelte [een nieuw apparaat registreren in de IOT-hub](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Deze voorbeeld-app gebruikt de **protocol**-variabele bij het maken van een **DeviceClient**-object.

10. Als u dubbele meldingen naar de console wilt afdrukken, voegt u de volgende geneste klasse toe aan de **app** -klasse:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Als u directe-methode meldingen wilt afdrukken naar de-console, voegt u de volgende geneste klasse toe aan de **app** -klasse:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Als u directe-methode aanroepen van IoT Hub wilt verwerken, voegt u de volgende geneste klasse toe aan de **app** -klasse:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. Werk de hand tekening van de **hoofd** methode bij zodat deze de volgende `throws` component bevat:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Vervang de code in de methode **Main** door de volgende code in:
    * Maak een apparaatclient om te communiceren met IoT Hub.
    * Maak een **apparaatobject** om de dubbele eigenschappen van het apparaat op te slaan.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Als u de client services van het apparaat wilt starten, voegt u de volgende code toe aan de methode **Main** :

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Als u wilt wachten tot de gebruiker op **Enter** drukt voordat u het programma afsluit, voegt u de volgende code toe aan het einde van de methode **Main** :

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Sla het **simulated-device\src\main\java\com\mycompany\app\App.java** -bestand op en sluit het.

18. Bouw de **gesimuleerde apparaat-** app op en corrigeer eventuele fouten. Ga bij de opdracht prompt naar de map **gesimuleerd apparaat** en voer de volgende opdracht uit:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de console-apps uit te voeren.

1. Voer bij een opdracht prompt in de map met **gesimuleerde apparaten** de volgende opdracht uit om de apparaat-app te laten Luis teren naar gewenste eigenschaps wijzigingen en directe-methode aanroepen:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![De apparaatclient wordt gestart](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Voer bij een opdracht prompt in de `schedule-jobs` map de volgende opdracht uit om de service **-app Schedule-taken** uit te voeren om twee taken uit te voeren. Met de eerste worden de gewenste eigenschaps waarden ingesteld, de tweede roept de directe methode aan:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Er worden twee taken gemaakt met de Java IoT Hub service-app](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. De apparaat-app handelt de gewenste eigenschaps wijziging en de aanroep van de directe methode af:

   ![De apparaatclient reageert op de wijzigingen](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een taak gebruikt voor het plannen van een directe methode op een apparaat en het bijwerken van de eigenschappen van het apparaat.

Gebruik de volgende bronnen voor meer informatie over:

* Verzend telemetrie van apparaten met de zelf studie [aan de slag met IOT hub](quickstart-send-telemetry-java.md) .

* Apparaten interactief beheren (bijvoorbeeld door een ventilator in te scha kelen vanuit een door de gebruiker beheerde app) met de zelf studie [directe methoden gebruiken](quickstart-control-device-java.md) . s