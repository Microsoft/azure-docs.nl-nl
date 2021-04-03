---
title: Id van de Azure IoT Hub-module &-module (Portal en .NET)
description: Informatie over het maken van module-id's en het bijwerken van moduledubbels met de portal en .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 0d16d7a6dbf903ae790015c12e3d34e4a2a553d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139355"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Aan de slag met IoT Hub-module-id's en moduledubbels met behulp van de portal en .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Module-id's en moduledubbels](iot-hub-devguide-module-twins.md) zijn vergelijkbaar met Azure IoT Hub-apparaat-id's en apparaatdubbels, maar bieden een hogere granulariteit. Hoewel Azure IoT Hub apparaat-id en-apparaat twee keer de back-end-toepassing in staat stellen om een apparaat te configureren en inzicht te krijgen in de voor waarden van het apparaat, bieden een module-identiteit en-module twee deze mogelijkheden voor afzonderlijke onderdelen van een apparaat. Op apparaten die geschikt zijn voor meerdere onderdelen, zoals apparaten op het besturings systeem of firmware apparaten, kunnen module-identiteiten en module-apparaatdubbels geïsoleerde configuratie en voor waarden voor elk onderdeel toestaan.
>

In deze zelfstudie leert u het volgende:

* Een module-identiteit maken in de portal.

* Een .NET-apparaat-SDK gebruiken voor het bijwerken van de module, twee vanaf uw apparaat.

> [!NOTE]
> Voor informatie over de Azure IoT-Sdk's die u kunt gebruiken om beide toepassingen te bouwen die worden uitgevoerd op apparaten en de back-end van uw oplossing, raadpleegt u [Azure IOT sdk's](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

## <a name="create-a-hub"></a>Een hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Een nieuw apparaat registreren in de hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Een module-id in de portal maken

Binnen één apparaat-id kunt u 20 module-entiteiten maken. Voer de volgende stappen uit om een identiteit toe te voegen:

1. Voor het apparaat dat u in de vorige sectie hebt gemaakt, kiest u **module-identiteit toevoegen** om uw eerste module-ID te maken.

1. Voer de naam *myFirstModule* in. Sla de module-id op.

    ![Module-identiteit toevoegen](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    De nieuwe module-identiteit wordt onder aan het scherm weer gegeven. Selecteer deze om de module-identiteits gegevens weer te geven.

    ![Details van module-identiteit weer geven](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Sla de **verbindings reeks-primaire sleutel** op. U gebruikt deze in de volgende sectie voor het instellen van uw module op het apparaat.

## <a name="update-the-module-twin-using-net-device-sdk"></a>De moduledubbel bijwerken met de SDK voor .NET-apparaten

U hebt nu de module-id in uw IoT Hub gemaakt. Laten we met de cloud proberen te communiceren vanaf het gesimuleerde apparaat. Door de module-id te maken, wordt impliciet ook een moduledubbel in de IoT Hub gemaakt. In deze sectie maakt u een .NET-consoletoepassing op het gesimuleerde apparaat waarmee de gerapporteerde eigenschappen van de moduledubbel worden bijgewerkt.

### <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Voer de volgende stappen uit om een app te maken die de door de module dubbele gerapporteerde eigenschappen bijwerkt:

1. Selecteer in Visual Studio **een nieuw project maken** en kies vervolgens **console-app (.NET Framework)** en selecteer **volgende**.

1. In **uw nieuwe project configureren** voert u *UpdateModuleTwinReportedProperties* in als de **project naam**. Selecteer **Maken** om door te gaan.

    ![Configureer een Visual Studio-project](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>De nieuwste Azure IoT Hub .NET-apparaat-SDK installeren

Module-identiteit en module dubbele is beschikbaar als open bare preview. Het is alleen beschikbaar in de IoT Hub voorlopige apparaat-Sdk's. Voer de volgende stappen uit om de app te installeren:

1. Open in Visual Studio **tools**  >  **NuGet package manager**  >  **NuGet-pakketten beheren voor oplossing**.

1. Selecteer **Bladeren** en selecteer vervolgens op **include Prerelease**. Zoek naar *micro soft. Azure. devices. client*. Selecteer de nieuwste versie en installeer deze.

    ![De preview-versie van Azure IoT Hub .NET Service SDK installeren](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    U hebt nu toegang tot alle modulefuncties.

### <a name="get-your-module-connection-string"></a>Uw module connection string ophalen

U hebt de module connection string voor uw console-app nodig. Volg deze stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Navigeer naar uw IoT-hub en selecteer **IOT-apparaten**. Open **myFirstDevice** en u ziet dat **myFirstModule** is gemaakt.

1. Selecteer **myFirstModule** onder **module-identiteiten**. Kopieer de **verbindings reeks (primaire sleutel)** in **module-identiteits gegevens**.

    ![Details van de Azure Portal-module](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties-console-app maken

Voer de volgende stappen uit om uw app te maken:

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Voeg de volgende velden toe aan de klasse **Program**: Vervang de waarde van de tijdelijke aanduiding door de moduleverbindingsreeks.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Voeg de methode **OnDesiredPropertyChanged** toe aan de klasse **Program**:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
      {
          Console.WriteLine("desired property change:");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
          Console.WriteLine("Sending current time as reported property");
          TwinCollection reportedProperties = new TwinCollection
          {
              ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
          };
  
          await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
      }
  ```

4. Vervang tot slot de methode **Main** door de volgende code:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
          Console.WriteLine("Sending app start time as reported property");
          TwinCollection reportedProperties = new TwinCollection();
          reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;
  
          Client.UpdateReportedPropertiesAsync(reportedProperties);
      }
      catch (AggregateException ex)
      {
          Console.WriteLine("Error in sample: {0}", ex);
      }
  
      Console.WriteLine("Waiting for Events.  Press enter to exit...");
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  U kunt deze app bouwen en uitvoeren met **F5**.

In dit codevoorbeeld ziet u hoe u de gerapporteerde eigenschappen van de moduledubbel kunt ophalen en bijwerken met het AMQP-protocol. In de openbare preview-versie wordt alleen AMQP ondersteund voor moduledubbelbewerkingen.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan met IoT Hub en andere IoT-scenario's wilt verkennen, leest u deze artikelen:

* [Aan de slag met IoT Hub module identity and module twin met .NET backup and .NET device](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Aan de slag met IoT Edge](../iot-edge/quickstart-linux.md)