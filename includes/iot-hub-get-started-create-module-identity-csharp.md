---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68883752"
---
## <a name="create-a-module-identity"></a>Een module-id maken

In deze sectie maakt u een .NET-console-app die een apparaat-id en een module-ID maakt in het identiteits register van uw hub. Een apparaat of module kan geen verbinding maken met hub, tenzij er een vermelding is in het identiteits register. Zie het gedeelte [Id-register in de ontwikkelaarshandleiding voor IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) voor meer informatie.

Als u deze consoletoepassing uitvoert, worden er een unieke id en een unieke sleutel gemaakt voor zowel het apparaat als de module. Uw apparaat en module gebruiken deze waarden om zichzelf te identificeren bij het verzenden van apparaat-naar-Cloud-berichten naar IoT Hub. De id's zijn hoofdlettergevoelig.

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Selecteer **console-app (.NET Framework)** in **een nieuw project maken**.

1. Selecteer **volgende** om **het nieuwe project configureren** te openen. Geef het project de naam *CreateIdentities* en geef de oplossing de naam *IoTHubGetStarted*. Zorg ervoor dat de versie van .NET Framework minimaal 4.6.1 is.

    ![Voer de naam en het framework in voor uw Visual Studio-oplossing](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. Open in Visual Studio **tools**  >  **NuGet package manager**  >  **NuGet-pakketten beheren voor oplossing**. Selecteer het tabblad **Bladeren**.

1. Zoek naar **micro soft. Azure. devices**. Selecteer deze en selecteer vervolgens **installeren**.

    ![De huidige versie van de Azure IoT Hub .NET Service SDK installeren](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduidingswaarde met de IoT Hub-verbindingsreeks voor de hub die u hebt gemaakt in de vorige sectie.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Voeg de volgende code toe aan de **hoofd** klasse.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Voeg de volgende methoden toe aan de klasse **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    De `AddDeviceAsync` -methode maakt een apparaat-id met de id **myFirstDevice**. Als deze apparaat-ID al bestaat in het identiteits register, haalt de code gewoon de bestaande apparaatgegevens op. De app geeft vervolgens de primaire sleutel voor die identiteit weer. U gebruikt deze sleutel in de gesimuleerde apparaat-app om verbinding te maken met uw hub.

    De `AddModuleAsync` -methode maakt een module-identiteit met id **myFirstModule** onder apparaat **myFirstDevice**. Als die module-ID al bestaat in het identiteits register, haalt de code gewoon de bestaande module-informatie op. De app geeft vervolgens de primaire sleutel voor die identiteit weer. U gebruikt deze sleutel in de gesimuleerde module-app om verbinding te maken met uw hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Voer deze app uit en noteer de sleutel en de module sleutel van het apparaat.

> [!NOTE]
> Het IoT Hub identiteits register slaat apparaat-en module-id's op om beveiligde toegang tot de hub mogelijk te maken. In het identiteitsregister worden apparaat-id's en -sleutels opgeslagen die als beveiligingsreferenties worden gebruikt. In het identiteitsregister wordt ook een vlag ingeschakeld/uitgeschakeld voor elk apparaat opgeslagen die u kunt gebruiken om de toegang tot dat apparaat uit te schakelen. Als uw app andere apparaatspecifieke meta gegevens moet opslaan, moet deze een toepassingsspecifiek archief gebruiken. Er is geen vlag voor ingeschakeld/uitgeschakeld voor module-id's. Zie [IOT hub ontwikkelaars handleiding](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie.
