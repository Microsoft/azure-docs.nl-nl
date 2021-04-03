---
title: Aan de slag met Azure IoT Hub Device Management (.NET/.NET) | Microsoft Docs
description: Azure IoT Hub Apparaatbeheer gebruiken om het opnieuw opstarten van een extern apparaat te initiëren. U gebruikt de Azure IoT Device SDK voor .NET voor het implementeren van een gesimuleerde apparaat-app die een directe methode bevat en de Azure IoT Service SDK voor .NET voor het implementeren van een service-app die de directe methode aanroept.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89018396"
---
# <a name="get-started-with-device-management-net"></a>Aan de slag met Apparaatbeheer (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze zelfstudie ontdekt u hoe u:

* Gebruik de Azure Portal om een IoT-hub te maken en een apparaat-id te maken in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat waarmee dat apparaat opnieuw wordt opgestart. Directe methoden worden vanuit de Cloud aangeroepen.

* Maak een .NET-console-app die de directe methode voor opnieuw opstarten aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het eind van deze zelfstudie beschikt u over twee .NET-consoletoepassingen:

* **SimulateManagedDevice**. Met deze app maakt u verbinding met uw IoT-hub met de apparaat-id die u eerder hebt gemaakt, ontvangt u een directe methode voor opnieuw opstarten, simuleert u fysiek opnieuw opstarten en rapporteert de tijd voor de laatste keer opnieuw opstarten.

* **TriggerReboot**. Met deze app wordt een directe methode aangeroepen in de gesimuleerde apparaat-app, wordt het antwoord weer gegeven en worden de bijgewerkte gerapporteerde eigenschappen weer gegeven.

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe keer opnieuw opstarten op het apparaat activeren met behulp van een directe methode

In deze sectie maakt u een .NET-console-app met behulp van C#, waarmee een apparaat op afstand opnieuw wordt opgestart met behulp van een directe methode. De app maakt gebruik van Device-dubbele query's om de tijd van de laatste keer opnieuw opstarten voor dat apparaat te detecteren.

1. Selecteer **Een nieuw project maken** in Visual Studio.

1. Zoek in **een nieuw project maken** en selecteer de project sjabloon **console-app (.NET Framework)** en selecteer vervolgens **volgende**.

1. Geef in **uw nieuwe project** de naam project *TriggerReboot* en selecteer versie 4.5.1 of hoger .NET Framework. Selecteer **Maken**.

    ![Nieuw Windows Classic Desktop-project in Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. Klik in **Solution Explorer** met de rechter muisknop op het project **TriggerReboot** en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer **Bladeren**, zoek naar en selecteer **micro soft. Azure. devices**. Selecteer **installeren** om het **micro soft. Azure. devices** -pakket te installeren.

    ![Sluit het venster Nuget Package Manager.](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Met deze stap wordt een verwijzing naar het [Azure IOT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de `{iot hub connection string}` waarde van de tijdelijke aanduiding door de IoT Hub Connection String u eerder hebt gekopieerd in [de IOT Hub-Connection String ophalen](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Voeg de volgende methode toe aan de klasse **Program**.  Met deze code wordt het apparaat voor het opnieuw opstarten van het apparaat opgehaald en worden de gerapporteerde eigenschappen uitgevoerd.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Voeg de volgende methode toe aan de klasse **Program**.  Met deze code wordt de computer opnieuw opgestart op het apparaat met behulp van een directe methode.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Selecteer **Bouwen** > **Oplossing bouwen**.

> [!NOTE]
> In deze zelf studie wordt slechts één query uitgevoerd voor de gerapporteerde eigenschappen van het apparaat. In productie code kunt u het beste navragen om wijzigingen in de gerapporteerde eigenschappen te detecteren.

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* Maak een .NET-console-app die reageert op een directe methode die wordt aangeroepen door de Cloud.

* Activeer een gesimuleerd apparaat opnieuw opstarten.

* Gebruik de gerapporteerde eigenschappen om Device-dubbele query's in te scha kelen om apparaten te identificeren en wanneer ze voor het laatst opnieuw zijn opgestart.

Als u de gesimuleerde apparaat-app wilt maken, volgt u deze stappen:

1. Selecteer in Visual Studio het **bestand**  >  **Nieuw**  >  **project** in de TriggerReboot-oplossing die u al hebt gemaakt. Zoek in **een nieuw project maken** en selecteer de project sjabloon **console-app (.NET Framework)** en selecteer vervolgens **volgende**.

1. In **uw nieuwe project configureren**, de naam van het project *SimulateManagedDevice* en voor de **oplossing**, selecteert **u toevoegen aan oplossing**. Selecteer **Maken**.

    ![Geef uw project een naam en voeg het toe aan de oplossing](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. Klik in Solution Explorer met de rechter muisknop op het nieuwe **SimulateManagedDevice** -project en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer **Bladeren** en zoek naar **micro soft. Azure. devices. client** en selecteer deze. Selecteer **Installeren**.

    ![NuGet Package Manager-venster Client-App](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Met deze stap wordt een verwijzing naar het [Azure IOT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de `{device connection string}` waarde van de tijdelijke aanduiding door de Connection String van het apparaat die u eerder hebt genoteerd in [een nieuw apparaat registreren bij de IOT-hub](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Voeg het volgende toe om de directe methode op het apparaat te implementeren:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Voeg tot slot de volgende code toe aan de methode **Main** om de verbinding met uw IOT-hub te openen en de methode-listener te initialiseren:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. Klik in Solution Explorer met de rechter muisknop op uw oplossing en selecteer vervolgens **opstart projecten instellen**.

1.   >  Selecteer **één opstart project** voor algemene eigenschappen voor het opstarten van het **project** en selecteer vervolgens het project **SimulateManagedDevice** . Selecteer **OK** om uw wijzigingen op te slaan.

1. Selecteer **Bouwen** > **Oplossing bouwen**.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productie code moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële uitstel), zoals wordt voorgesteld in [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de apps uit te voeren.

1. Als u de .NET- **SimulateManagedDevice** wilt uitvoeren, klikt u in Solution Explorer met de rechter muisknop op het project **SimulateManagedDevice** , selecteert u **fout opsporing** en selecteert u vervolgens **nieuw exemplaar starten**. De app moet beginnen met Luis teren naar methode aanroepen vanuit uw IoT-hub.

1. Klik met de rechter muisknop op het **TriggerReboot** -project nadat het apparaat is verbonden en er wordt gewacht op de aanroep van de methode, selecteer **fout opsporing** en selecteer vervolgens **nieuw exemplaar starten**.

   Als het goed is, wordt ' opnieuw opstarten ' weer geven. geschreven in de **SimulatedManagedDevice** -console en de gerapporteerde eigenschappen van het apparaat, waaronder de laatste keer opnieuw opstarten, geschreven in de **TriggerReboot** -console.

    ![App voor service en apparaat uitvoeren](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
