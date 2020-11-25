---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 788aa0ebe9df91caba2ee279df96cbea175975e4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487792"
---
IoT Plug en Play vereenvoudigt IoT en stelt u in staat om gebruik te maken van de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u Java gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Om deze quickstart in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* Java SE Development Kit 8. In [Java-langetermijnondersteuning voor Azure en Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) onder **Langetermijnondersteuning** selecteert u **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Kloon de SDK-opslagplaats met de voorbeeldcode

Als u klaar bent met [Quickstart: Verbind een voorbeeld van een IoT Plug en Play-apparaat-app die in Windows wordt uitgevoerd met IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md). U hebt de opslagplaats al gekloond.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Microsoft Azure IoT SDK's voor Java](https://github.com/Azure/azure-iot-sdk-java) te klonen op deze locatie:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Het voorbeeldapparaat compileren en uitvoeren

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Zie het [Leesmij-voorbeeld](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md) voor meer informatie over de voorbeeldconfiguratie.

In deze quickstart gebruikt u als voorbeeld een thermostaat die in Java is geschreven als het IoT Plug en Play-apparaat. Het voorbeeldapparaat uitvoeren:

1. Open een terminalvenster en navigeer naar de lokale map met de Microsoft Azure IoT SDK voor Java-opslagplaats die u hebt gekloond vanuit GitHub.

1. Dit terminalvenster wordt gebruikt als uw **apparaatterminal**. Ga naar de hoofdmap van uw gekloonde opslagplaats. Installeer de afhankelijkheden door de volgende opdracht uit te voeren:

1. Voer de volgende opdracht uit om het voorbeeldapparaat te maken:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Om de toepassing voor voorbeeldapparaten uit te voeren, gaat u naar de map *device\iot-device-samples\pnp-device-sample\thermostat-device-sample* en voert u de volgende opdracht uit:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeldapparaat draaien tijdens het uitvoeren van de volgende stappen.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In [Quickstarts en zelfstudies voor het instellen van uw omgeving voor IoT Plug en Play](../articles/iot-pnp/set-up-environment.md) hebt u twee omgevingsvariabelen gemaakt om het voorbeeld zo te configureren dat verbinding wordt gemaakt met uw IoT-hub en apparaat:

* **IOTHUB_CONNECTION_STRING**: de verbindingsreeks voor de IoT-hub die u eerder hebt genoteerd.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In deze quickstart gebruikt u een IoT-voorbeeldoplossing geschreven in Java om te communiceren met het voorbeeldapparaat dat u zojuist hebt ingesteld.

> [!NOTE]
> In dit voorbeeld wordt gebruikgemaakt van de naamruimte **com.microsoft.azure.sdk.iot.service** van de **IoT Hub-serviceclient**. Zie de [handleiding voor serviceontwikkelaars](../articles/iot-pnp/concepts-developer-guide-service.md) voor meer informatie over de API's, waaronder de API's voor digitale dubbels.

1. Open een ander terminalvenster om als **serviceterminal** te gebruiken.

1. Ga in de gekloonde Java-SDK-opslag plaats naar de map  *service\iot-service-samples\pnp-service-sample\thermostat-service-sample*.

1. Voer de volgende opdracht uit om de voorbeeldservicetoepassing uit te voeren:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Apparaatdubbel ophalen

In het volgende codefragment ziet u hoe u het dubbel apparaat kunt ophalen in de service:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Een apparaatdubbel bijwerken

Het volgende codefragment laat zien hoe u een *patch* gebruikt om eigenschappen bij te werken via de apparaatdubbel:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

De uitvoer van het apparaat laat zien hoe het apparaat reageert wanneer deze eigenschap wordt bijgewerkt.

### <a name="invoke-a-command"></a>Een opdracht aanroepen

Het volgende codefragment laat zien hoe u een opdracht aanroept op het apparaat:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

De uitvoer van het apparaat laat zien hoe het apparaat reageert op deze opdracht.
