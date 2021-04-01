---
title: Taken plannen met Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Een Azure IoT Hub-taak plannen voor het aanroepen van een directe methode op meerdere apparaten. U gebruikt de Azure IoT Device SDK voor .NET voor het implementeren van de gesimuleerde apparaat-apps en een service-app om de taak uit te voeren.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 0391afe5562238f488fde7e50d914c46a5c32944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142464"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Taken plannen en uitzenden (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Gebruik Azure IoT Hub om taken te plannen en bij te houden waarmee miljoenen apparaten worden bijgewerkt. Taken gebruiken voor:

* Gewenste eigenschappen bijwerken

* Tags bijwerken

* Directe methoden aanroepen

Een taak verpakt een van deze acties en traceert de uitvoering op basis van een set apparaten die is gedefinieerd door een dubbele query voor een apparaat. Een back-end-app kan bijvoorbeeld een taak gebruiken om een directe methode aan te roepen op 10.000-apparaten die de apparaten opnieuw opstarten. U geeft de set apparaten met een dubbele query voor een apparaat op en plant de taak op een later tijdstip. De taak houdt de voortgang bij wanneer elk apparaat wordt ontvangen en de methode voor opnieuw opstarten direct wordt uitgevoerd.

Zie voor meer informatie over elk van deze mogelijkheden:

* Apparaat-dubbele en eigenschappen: [aan de slag met apparaatdubbels](iot-hub-csharp-csharp-twin-getstarted.md) en [zelf studie: een dubbele eigenschappen van het apparaat gebruiken](tutorial-device-twins.md)

* Directe methoden: [IOT hub ontwikkelaars handleiding-directe methoden](iot-hub-devguide-direct-methods.md) en [zelf studie: directe methoden gebruiken](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze zelfstudie ontdekt u hoe u:

* Maak een apparaat-app die een directe methode met de naam **LockDoor** implementeert, die kan worden aangeroepen door de back-end-app.

* Maak een back-end-app die een taak maakt om de **LockDoor** direct-methode aan te roepen op meerdere apparaten. Een andere taak verzendt gewenste eigenschaps updates naar meerdere apparaten.

Aan het einde van deze zelf studie hebt u twee .NET (C#)-console-apps:

* **SimulateDeviceMethods**. Deze app maakt verbinding met uw IoT-hub en implementeert de **LockDoor** direct-methode.

* **ScheduleJob**. Deze app maakt gebruik van taken om de **LockDoor** direct-methode aan te roepen en de dubbele gewenste eigenschappen van het apparaat op meerdere apparaten bij te werken.

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een .NET-console-app die reageert op een directe methode die wordt aangeroepen door de back-end van de oplossing.

1. Selecteer in Visual Studio **een nieuw project maken** en kies vervolgens de project sjabloon **console-app (.NET Framework)** . Selecteer **Volgende** om door te gaan.

1. Geef in **uw nieuwe project** de naam project *SimulateDeviceMethods* en selecteer vervolgens **maken**.

    ![Uw SimulateDeviceMethods-project configureren](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. Klik in Solution Explorer met de rechter muisknop op het project **SimulateDeviceMethods** en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer in **NuGet package manager** **Bladeren** en zoek naar en kies **micro soft. Azure. devices. client**. Selecteer **Installeren**.

    ![NuGet Package Manager-venster Client-App](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Met deze stap wordt een verwijzing naar het [Azure IOT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de waarde van de tijdelijke aanduiding door het apparaat connection string dat u in de vorige sectie hebt genoteerd:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Voeg de volgende code toe om de directe methode op het apparaat te implementeren:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Voeg de volgende methode toe om de apparaatdubbels-listener op het apparaat te implementeren:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Voeg tot slot de volgende code toe aan de methode **Main** om de verbinding met uw IOT-hub te openen en de methode-listener te initialiseren:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Sla uw werk op en bouw uw oplossing.

> [!NOTE]
> Deze zelf studie implementeert geen beleids regels voor opnieuw proberen om dingen eenvoudig te slaan. In productie code moet u beleid voor opnieuw proberen implementeren (zoals opnieuw proberen van verbinding), zoals wordt voorgesteld in [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Taken plannen voor het aanroepen van een directe methode en het verzenden van dubbele updates voor een apparaat

In deze sectie maakt u een .NET-console-app (met C#) die gebruikmaakt van taken om de **LockDoor** direct-methode aan te roepen en gewenste eigenschaps updates naar meerdere apparaten te verzenden.

1. Selecteer in Visual Studio **Bestand** > **Nieuw** > **Project**. Kies in **een nieuw project maken de** optie **console-app (.NET Framework)** en selecteer vervolgens **volgende**.

1. Geef het project de naam *ScheduleJob* in **uw nieuwe project configureren**. Kies voor **oplossing** de optie **toevoegen aan oplossing** en selecteer vervolgens **maken**.

    ![Een naam en ScheduleJob-project opgeven](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. Klik in Solution Explorer met de rechter muisknop op het project **ScheduleJob** en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer in de **NuGet-pakket manager** de optie **Bladeren**, zoek naar en kies **micro soft. Azure. devices**, en selecteer vervolgens **installeren**.

   Met deze stap wordt een verwijzing naar het [Azure IOT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Voeg de volgende `using` instructie toe als deze niet al aanwezig is in de standaard-instructies.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduidingen door de IoT Hub connection string die u eerder hebt gekopieerd in [de IOT hub-Connection String](#get-the-iot-hub-connection-string) en de naam van uw apparaat ophalen.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Voeg een andere methode toe aan de klasse **Program** :

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Zie [IOT hub query language (Engelstalig](./iot-hub-devguide-query-language.md)) voor meer informatie over de syntaxis van query's.
    >

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Sla uw werk op en bouw uw oplossing.

## <a name="run-the-apps"></a>De apps uitvoeren

U kunt nu de apps uitvoeren.

1. Klik in Visual Studio Solution Explorer met de rechter muisknop op uw oplossing en selecteer vervolgens **opstart projecten instellen**.

1. Selecteer **algemene eigenschappen**  >  **Start project** en selecteer vervolgens **meerdere opstart projecten**.

1. Zorg ervoor dat `SimulateDeviceMethods` boven aan de lijst wordt gevolgd door `ScheduleJob` . Stel beide acties in op **Start** en selecteer **OK**.

1. Voer de projecten uit door te klikken op **Start** of ga naar het menu **fout opsporing** en klik op **fout opsporing starten**.

   U ziet de uitvoer van zowel apparaat-als back-end-apps.

    ![De apps uitvoeren om taken te plannen](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een taak gebruikt voor het plannen van een directe methode op een apparaat en het bijwerken van de eigenschappen van het apparaat.

* Lees [zelf studie: instructies voor het uitvoeren van een firmware-update](tutorial-firmware-update.md)om door te gaan met IOT hub en patronen voor Apparaatbeheer, zoals extern via de Air firmware-update.

* Zie aan de slag [met IOT Edge](../iot-edge/quickstart-linux.md)voor meer informatie over het implementeren van AI naar edge-apparaten met Azure IOT Edge.