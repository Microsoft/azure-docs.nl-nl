---
title: Zelfstudie - Een Azure IoT Plug en Play-apparaat verbinden met een algemene module | Microsoft Docs
description: Zelfstudie - Gebruik van voorbeeldcode van een C# IoT Plug en play-apparaat in een algemene module.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33eaa1ea928cc0650c91948c70d46daf499f3b4b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831198"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Zelfstudie: Een IoT Plug en Play-apparaat verbinden (C#)

In deze zelfstudie wordt uitgelegd hoe u een algemene IoT Plug en Play-[module](../iot-hub/iot-hub-devguide-module-twins.md) verbindt.

Een apparaat is een IoT Plug en Play-apparaat als het de eigen model-id publiceert wanneer het verbinding maakt met een IoT-hub en de eigenschappen en methoden implementeert die worden beschreven in het DTDL-model (Digital Twins Definition Language) dat wordt aangeduid door de model-id. Zie [Handleiding voor IoT Plug en Play-ontwikkelaars](./concepts-developer-guide-device.md) voor meer informatie over de wijze waarop apparaten een DTDL en een model-id gebruiken. Modules maken op dezelfde wijze gebruik van model-id's en DTDL-modellen.

Deze zelf studie laat zien hoe u het volgende kunt demonstreren om te laten zien hoe u een IoT Plug en Play-module implementeert:

> [!div class="checklist"]
> * Een apparaat met een module toevoegen aan uw IoT-hub.
> * Converteer het voor beeld van een C#-apparaat in een algemene module.
> * Gebruik de Service-SDK om met de module te communiceren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Om deze zelfstudie in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Gebruik de Azure IoT-verkenner om een nieuw apparaat met de naam **my-module-device** toe te voegen aan uw IoT-hub.

Voeg een module met de naam **my-module** toe aan het apparaat **my-module-device**:

1. Ga in de Azure IoT-verkenner naar het apparaat **my-module-device**.

1. Selecteer **Module-id** en selecteer vervolgens **+ Toevoegen**.

1. Voer **my-module** in als de naam van de module-id en selecteer **Opslaan**.

1. Selecteer **my-module** in de lijst met module-id's. Kopieer vervolgens de primaire verbindingsreeks. U gebruikt deze verbindingsreeks van de module later in deze zelfstudie.

1. Selecteer het tabblad **Moduledubbel** en u ziet dat er geen gewenste of gerapporteerde eigenschappen zijn:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>De code downloaden

Als u dit nog niet hebt gedaan, kloont u de GitHub-opslagplaats van de Azure IoT Hub Device-SDK voor C# naar uw lokale computer:

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats voor [Azure IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp) naar de volgende locatie te klonen:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Het project voorbereiden

U kunt als volgt het voorbeeldproject openen en voorbereiden:

1. Open het projectbestand *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio 2019.

1. Ga in Visual Studio naar **Project > Thermostaateigenschappen > Fouten opsporen**. Voeg vervolgens de volgende omgevingsvariabelen toe aan het project:

    | Naam | Waarde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | De verbindingsreeks van de module die u eerder hebt genoteerd |

    Zie het [Leesmij-voorbeeld](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md) voor meer informatie over de voorbeeldconfiguratie.

## <a name="modify-the-code"></a>Past de code aan

Als u de code wilt wijzigen zodat deze als een module wordt gebruikt in plaats van als een apparaat:

1. Open *Parameter.cs* in Visual Studio en wijzig als volgt de regel waarmee de variabele **PrimaryConnectionString** wordt ingesteld:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. Open *Program.cs* in Visual Studio en vervang de zeven instanties van de klasse `DeviceClient` door de klasse `ModuleClient`.

    > [!TIP]
    > Gebruik de functie voor zoeken en vervangen in Visual Studio waarbij **Identieke hoofdletters/kleine letters** en **Volledig woord moet overeenkomen** zijn ingeschakeld om `DeviceClient` te vervangen door `ModuleClient`.

1. Open *Thermostat.cs* in Visual Studio en vervang als volgt beide instanties van de klasse `DeviceClient` door de klasse `ModuleClient`.

1. Sla de wijzigingen op in de bestanden die u hebt aangepast.

Als u de code uitvoert en vervolgens de Azure IoT-verkenner gebruikt om de bijgewerkte moduledubbel weer te geven, ziet u de bijgewerkte apparaatdubbel met de model-id en de gerapporteerde eigenschap voor de module:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Werken met een apparaatmodule

Met de service-SDK's kunt u de model-id van verbonden IoT Plug en Play-apparaten en -modules ophalen. U kunt met de service-SDK's schrijfbare eigenschappen instellen en opdrachten aanroepen:

1. Open in een andere Visual Studio-instantie het project *azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. Ga in Visual Studio naar **Project > Thermostaateigenschappen > Fouten opsporen**. Voeg vervolgens de volgende omgevingsvariabelen toe aan het project:

    | Naam | Waarde |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](set-up-environment.md) hebt uitgevoerd |

    > [!TIP]
    > U kunt de verbindingsreeks voor uw IoT-hub ook vinden in de Azure IoT-verkenner.

1. Open het bestand *Program.cs* en wijzig als volgt de regel waarmee een opdracht wordt aangeroepen:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. Wijzig in het bestand *Program.cs* als volgt de regel waarmee de apparaatdubbel wordt opgehaald:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Controleer of het voorbeeld van de moduleclient nog actief is en voer vervolgens dit servicevoorbeeld uit. In de uitvoer van het servicevoorbeeld worden de model-id van de apparaatdubbel en de opdrachtaanroep vermeld:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    In de uitvoer van de moduleclient wordt het antwoord van de opdrachthandler vermeld:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Converteren naar een IoT Edge-module

Als u dit voorbeeld wilt converteren zodat het als een IoT Plug en Play IoT Edge-module kan worden gebruikt, moet u de app in een container plaatsen. U hoeft verder geen wijzigingen in de code aan te brengen. De omgevingsvariabele voor de verbindingsreeks wordt tijdens het opstarten door de IoT Edge-runtime ingevoegd. Zie [Visual Studio 2019 gebruiken voor het ontwikkelen van en het opsporen van fouten in modules voor Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md) voor meer informatie.

Raadpleeg de volgende artikelen voor meer informatie over het implementeren van uw module die verpakt is in een container:

* [Azure IoT Edge uitvoeren op Ubuntu-VM's](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [De Azure IoT Edge-runtime installeren op Linux-systemen die op Debian zijn gebaseerd](../iot-edge/how-to-install-iot-edge.md).

U kunt met de Azure IoT-verkenner het volgende bekijken:

* De model-id van uw IoT Edge-apparaat in de moduledubbel.
* Telemetrie van het IoT Edge-apparaat.
* Updates van eigenschappen van de IoT Edge-moduledubbel die IoT Plug en Play-meldingen activeren.
* De reactie van de IoT Edge-module op uw IoT Plug en Play-opdrachten.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug en Play-apparaat met modules verbindt met een IoT-hub. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars van IoT Plug en Play-modellen](./concepts-developer-guide-device.md)