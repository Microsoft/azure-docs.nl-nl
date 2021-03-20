---
title: Aan de slag met Azure IoT Hub Device apparaatdubbels (.NET/.NET) | Microsoft Docs
description: Azure IoT Hub Device apparaatdubbels gebruiken om labels toe te voegen en vervolgens een IoT Hub query te gebruiken. U gebruikt de Azure IoT Device SDK voor .NET voor het implementeren van de gesimuleerde apparaat-app en de Azure IoT Service SDK voor .NET om een service-app te implementeren waarmee de tags worden toegevoegd en de IoT Hub-query wordt uitgevoerd.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 267a69486dc91ef95c0de704346eeb1d1780ef48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89013755"
---
# <a name="get-started-with-device-twins-net"></a>Aan de slag met Device apparaatdubbels (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

In deze zelf studie maakt u deze .NET-console-apps:

* **AddTagsAndQuery**. Met deze back-end-app worden labels en query's voor apparaat-apparaatdubbels toegevoegd.

* **ReportConnectivity**. Deze apparaat-app simuleert een apparaat dat verbinding maakt met uw IoT-hub met de apparaat-id die u eerder hebt gemaakt, en rapporteert de connectiviteits voorwaarde.

> [!NOTE]
> Het artikel [Azure IOT sdk's](iot-hub-devguide-sdks.md) bevat informatie over de Azure IOT-sdk's die u kunt gebruiken om zowel apparaat-als back-end-apps te bouwen.
>

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een .NET-console-app met behulp van C#, waarmee de meta gegevens van een locatie worden toegevoegd aan het apparaat dat is gekoppeld aan **myDeviceId**. Vervolgens wordt een query uitgevoerd op de apparaat-apparaatdubbels die zijn opgeslagen in de IoT-hub en de apparaten die zich in de Verenigde Staten bevinden, en vervolgens die die een mobiele verbinding hebben gerapporteerd.

1. Selecteer **Een nieuw project maken** in Visual Studio. Selecteer in **Nieuw project maken** de optie **console-app (.NET Framework)** en selecteer vervolgens **volgende**.

1. Geef het project de naam **AddTagsAndQuery** in **uw nieuwe project configureren**.

    ![Uw AddTagsAndQuery-project configureren](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. Klik in Solution Explorer met de rechter muisknop op het project **AddTagsAndQuery** en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer **Bladeren** en zoeken naar en selecteer **micro soft. Azure. devices**. Selecteer **Installeren**.

    ![Sluit het venster Nuget Package Manager.](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Met deze stap wordt een verwijzing naar het [Azure IOT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang door `{iot hub connection string}` de IOT hub-Connection String die u hebt gekopieerd in [de IOT Hub-Connection String ophalen](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    De klasse **RegistryManager** toont alle methoden die nodig zijn om te communiceren met de apparaatdubbels van de service. De vorige code initialiseert eerst het **registryManager** -object, haalt vervolgens het apparaat op dat ligt voor **myDeviceId**, en werkt de labels vervolgens bij met de gewenste locatie-informatie.

    Na het bijwerken worden er twee query's uitgevoerd: de eerste selecteert alleen het apparaat apparaatdubbels van apparaten die zich in de **Redmond43** -installatie bevinden, en de tweede verfijnt de query om alleen de apparaten te selecteren die ook zijn verbonden via een mobiel netwerk.

    De vorige code, wanneer deze het **query** -object maakt, geeft een maximum aantal geretourneerde documenten op. Het **query** -object bevat een **HasMoreResults** Boolean-eigenschap die u kunt gebruiken om de **GetNextAsTwinAsync** -methoden meerdere keren aan te roepen om alle resultaten op te halen. Een methode met de naam **GetNextAsJson** is beschikbaar voor resultaten die geen apparaatdubbels zijn, bijvoorbeeld resultaten van aggregatie query's.

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Voer deze toepassing uit door met de rechter muisknop op het **AddTagsAndQuery** -project te klikken en vervolgens **debug** te selecteren, gevolgd door **nieuwe instantie starten**. U ziet één apparaat in de resultaten voor de query die vraagt naar alle apparaten in **Redmond43** en geen voor de query waarmee de resultaten worden beperkt tot apparaten die gebruikmaken van een mobiel netwerk.

    ![Query resultaten in venster](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

In de volgende sectie maakt u een apparaat-app die de connectiviteits gegevens rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een .NET-console-app die als **myDeviceId** verbinding maakt met uw hub, en vervolgens de gerapporteerde eigenschappen bijwerkt om de informatie te bevatten die is verbonden met een mobiel netwerk.

1. Selecteer in Visual Studio **Bestand** > **Nieuw** > **Project**. In **Nieuw project maken** kiest u **console-app (.NET Framework)** en selecteert u vervolgens **volgende**.

1. Geef het project de naam **ReportConnectivity** in **uw nieuwe project configureren**. Kies voor **oplossing** de optie **toevoegen aan oplossing** en selecteer vervolgens **maken**.

1. Klik in Solution Explorer met de rechter muisknop op het project **ReportConnectivity** en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer **Bladeren** en zoek naar en kies **micro soft. Azure. devices. client**. Selecteer **Installeren**.

   Met deze stap wordt een verwijzing naar het [Azure IOT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang door `{device connection string}` het apparaat Connection String dat u hebt genoteerd in [een nieuw apparaat registreren in IOT hub](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Het **client** object bevat alle methoden die u nodig hebt om te communiceren met apparaatdubbels van het apparaat. De hierboven weer gegeven code initialiseert het **client** object en haalt vervolgens het apparaat op voor **myDeviceId**.

1. Voeg de volgende methode toe aan de klasse **Program**:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   De bovenstaande code werkt de gerapporteerde eigenschap van  **myDeviceId** bij met de verbindings gegevens.

1. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Klik in Solution Explorer met de rechter muisknop op uw oplossing en selecteer vervolgens **opstart projecten instellen**.

1. Selecteer in **algemene eigenschappen**  >  **opstart project** **meerdere opstart projecten**. Selecteer voor **ReportConnectivity** de optie **Start** als **actie**. Selecteer **OK** om uw wijzigingen op te slaan.  

1. Voer deze app uit door met de rechter muisknop op het **ReportConnectivity** -project te klikken en vervolgens **debug** te selecteren en vervolgens **nieuw exemplaar te starten**. U ziet dat de app de dubbele gegevens ophaalt en vervolgens de connectiviteit verzendt als een **_gerapporteerde eigenschap_**.

    ![De app van het apparaat uitvoeren voor het rapporteren van de connectiviteit](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Nadat het apparaat de verbindings gegevens heeft gerapporteerd, zou het in beide query's moeten worden weer gegeven.

1. Klik met de rechter muisknop op het **AddTagsAndQuery** -project en selecteer **debug**  >  **starten nieuwe instantie** om de query's opnieuw uit te voeren. Deze keer worden **myDeviceId** weer gegeven in de query resultaten.

    ![De connectiviteit van het apparaat is gerapporteerd](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt meta gegevens van apparaten toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om connectiviteits gegevens van apparaten te rapporteren in het dubbele apparaat. U hebt ook geleerd hoe u deze gegevens kunt zoeken met behulp van de SQL-achtige IoT Hub query taal.

Meer informatie vindt u in de volgende bronnen:

* Zie de zelf studie [telemetrie van een apparaat verzenden naar een IOT-hub](quickstart-send-telemetry-dotnet.md) voor meer informatie over het verzenden van telemetrie van apparaten.

* Zie de zelf studie [gewenste eigenschappen gebruiken om apparaten te configureren](tutorial-device-twins.md) voor meer informatie over het configureren van apparaten met behulp van de gewenste eigenschappen van het apparaat.

* Zie de zelf studie [directe methoden gebruiken](quickstart-control-device-dotnet.md) voor meer informatie over het interactief beheren van apparaten, zoals het inschakelen van een ventilator vanuit een door de gebruiker beheerde app.
