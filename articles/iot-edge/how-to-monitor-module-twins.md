---
title: Module-tweelingen bewaken - Azure IoT Edge
description: Apparaat-tweelingen en module-tweelingen interpreteren om de connectiviteit en status te bepalen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5a31e15c88cef588c93f44c8fe5303d930b5b2c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479369"
---
# <a name="monitor-module-twins"></a>Dubbele modules bewaken

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Module-tweelingen in Azure IoT Hub bewaking van de connectiviteit en status van uw IoT Edge-implementaties. Module-tweelingen slaan nuttige informatie op in uw IoT-hub over de prestaties van uw modules die worden uitgevoerd. De [IoT Edge-agent](iot-edge-runtime.md#iot-edge-agent) en [de IoT Edge hub-runtime-modules](iot-edge-runtime.md#iot-edge-hub) behouden elk hun module-tweelingen, `$edgeAgent` respectievelijk en `$edgeHub` :

* `$edgeAgent` bevat status- en connectiviteitsgegevens over de IoT Edge-agent en IoT Edge hub-runtimemodules en uw aangepaste modules. De IoT Edge agent is verantwoordelijk voor het implementeren van de modules, het bewaken ervan en het rapporteren van de verbindingsstatus aan uw Azure IoT-hub.
* `$edgeHub` bevat gegevens over de communicatie tussen de IoT Edge hub die wordt uitgevoerd op een apparaat en uw Azure IoT-hub. Dit omvat het verwerken van binnenkomende berichten van downstreamapparaten. IoT Edge hub is verantwoordelijk voor het verwerken van de communicatie tussen de Azure IoT Hub en de IoT Edge apparaten en modules.

De gegevens zijn ingedeeld in metagegevens, tags, samen met de gewenste en gerapporteerde eigenschappensets in de JSON-structuren van de module-tweelingen. De gewenste eigenschappen die u hebt opgegeven in deployment.jsbestand, worden gekopieerd naar de module-tweelingen. De IoT Edge agent en de IoT Edge hub werken elk de gerapporteerde eigenschappen voor hun modules bij.

Op dezelfde manier worden de gewenste eigenschappen die zijn opgegeven voor uw aangepaste modules in het deployment.json-bestand gekopieerd naar de module-dubbel, maar is uw oplossing verantwoordelijk voor het leveren van de gerapporteerde eigenschapswaarden.

In dit artikel wordt beschreven hoe u de module-tweelingen bekijkt in de Azure Portal, Azure CLI en in Visual Studio Code. Zie Monitor IoT Edge deployments (Implementaties bewaken) voor meer informatie over het controleren hoe uw apparaten [de implementaties ontvangen.](how-to-monitor-iot-edge-deployments.md) Zie Module-tweelingen begrijpen en gebruiken in IoT Hub voor een overzicht [van het concept van module-IoT Hub.](../iot-hub/iot-hub-devguide-module-twins.md)

> [!TIP]
> De gerapporteerde eigenschappen van een runtimemodule kunnen verouderd zijn als een IoT Edge wordt losgekoppeld van de IoT-hub. U kunt [de](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` module pingen om te bepalen of de verbinding is verloren.

## <a name="monitor-runtime-module-twins"></a>Runtimemodule-tweelingen bewaken

Als u problemen met de implementatieconnectiviteit wilt oplossen, bekijkt u IoT Edge agent en IoT Edge hub runtime module twins en zoomt u vervolgens in op andere modules.

### <a name="monitor-iot-edge-agent-module-twin"></a>Module IoT Edge agent bewaken

De volgende JSON toont de module dubbel in Visual Studio Code met de meeste `$edgeAgent` JSON-secties samengevouwen.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
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
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

De JSON kan worden beschreven in de volgende secties, vanaf de bovenkant:

* Metagegevens: bevat connectiviteitsgegevens. Interessant is dat de verbindingstoestand voor de IoT Edge agent altijd de status Verbroken heeft: `"connectionState": "Disconnected"` . De reden hiervoor is dat de verbindingstoestand betrekking heeft op D2C-berichten (device-to-cloud) en de IoT Edge-agent verzendt geen D2C-berichten.
* Eigenschappen: bevat de `desired` `reported` subsecties en .
* Properties.desired: (samengevouwen weergegeven) Verwachte eigenschapswaarden die zijn ingesteld door de operator in deployment.jsbestand.
* Properties.reported: meest recente eigenschapswaarden die zijn gerapporteerd door IoT Edge agent.

Zowel de secties als hebben een vergelijkbare structuur en bevatten aanvullende metagegevens voor `properties.desired` `properties.reported` schema-, versie- en runtime-informatie. Ook is de sectie opgenomen voor aangepaste modules (zoals ) en de `modules` sectie voor en de `SimulatedTemperatureSensor` `systemModules` `$edgeAgent` `$edgeHub` runtimemodules.

Door de gerapporteerde eigenschapswaarden te vergelijken met de gewenste waarden, kunt u discrepanties vaststellen en verbroken verbindingen identificeren die u kunnen helpen bij het oplossen van problemen. Als u deze vergelijkingen maakt, controleert u `$lastUpdated` de gerapporteerde waarde in de `metadata` sectie voor de eigenschap die u onderzoekt.

De volgende eigenschappen zijn belangrijk voor het oplossen van problemen:

* **exitcode:** elke andere waarde dan nul geeft aan dat de module is gestopt met een fout. Foutcodes 137 of 143 worden echter gebruikt als een module opzettelijk is ingesteld op een gestopte status.

* **lastStartTimeUtc:** toont de **Datum/tijd** waarop de container voor het laatst is gestart. Deze waarde is 0001-01-01T00:00:00Z als de container niet is gestart.

* **lastExitTimeUtc:** toont de **Datum/tijd** waarop de container voor het laatst is voltooid. Deze waarde is 0001-01-01T00:00:00Z als de container wordt uitgevoerd en nooit is gestopt.

* **runtimeStatus:** dit kan een van de volgende waarden zijn:

    | Waarde | Beschrijving |
    | --- | --- |
    | unknown | De standaardtoestand totdat de implementatie is gemaakt. |
    | back-off | De module is gepland om te starten, maar wordt momenteel niet uitgevoerd. Deze waarde is handig voor een module die statuswijzigingen ondergaat bij het opnieuw opstarten. Wanneer een mislukte module wacht op opnieuw opstarten tijdens de afkoelperiode, heeft de module een back-off-status. |
    | Met | Geeft aan dat de module momenteel wordt uitgevoerd. |
    | Ongezonde | Geeft aan dat een statustest is mislukt of er een time-out is. |
    | Gestopt | Geeft aan dat de module is afgesloten (met een nuluitgangscode). |
    | mislukt | Geeft aan dat de module is afgesloten met een foutuitgangscode (niet-nul). De module kan vanuit deze status teruggaan naar de back-off, afhankelijk van het beleid voor opnieuw opstarten van kracht. Deze status kan erop wijzen dat de module een onherkenbare fout heeft ondervonden. Fout treedt op wanneer de MMA (Microsoft Monitoring Agent) de module niet meer kan resusciteren, waardoor een nieuwe implementatie is vereist. |

Zie [Gerapporteerde eigenschappen van EdgeAgent](module-edgeagent-edgehub.md#edgeagent-reported-properties) voor meer informatie.

### <a name="monitor-iot-edge-hub-module-twin"></a>De module IoT Edge hub bewaken

De volgende JSON toont de module dubbel in Visual Studio Code met de meeste `$edgeHub` JSON-secties samengevouwen.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
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
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

De JSON kan worden beschreven in de volgende secties, vanaf de bovenkant:

* Metagegevens: bevat connectiviteitsgegevens.

* Eigenschappen: bevat de `desired` `reported` subsecties en .
* Properties.desired: (samengevouwen weergegeven) Verwachte eigenschapswaarden die zijn ingesteld door de operator in deployment.jsbestand.
* Properties.reported: meest recente eigenschapswaarden die zijn gerapporteerd door IoT Edge hub.

Als u problemen ondervindt met uw downstreamapparaten, is het onderzoeken van deze gegevens een goede plek om te beginnen.

## <a name="monitor-custom-module-twins"></a>Aangepaste module-tweelingen bewaken

De informatie over de connectiviteit van uw aangepaste modules wordt bijgehouden in de module IoT Edge agent. De module dubbel voor uw aangepaste module wordt voornamelijk gebruikt voor het onderhouden van gegevens voor uw oplossing. De gewenste eigenschappen die u hebt gedefinieerd in deployment.json-file worden weergegeven in de module dubbel en uw module kan gerapporteerde eigenschapswaarden zo nodig bijwerken.

U kunt de programmeertaal van uw voorkeur gebruiken met [de Azure IoT Hub Device SDK's](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) om gerapporteerde eigenschapswaarden in de module dubbel bij te werken, op basis van de toepassingscode van uw module. In de volgende procedure wordt de Azure SDK voor .NET gebruikt om dit te doen, met behulp van code uit de module [SimulatedTemperatureSensor:](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs)

1. Maak een exemplaar van de [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) met [de methode CreateFromEnvironment Extranc.](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)

1. Haal een verzameling van de eigenschappen van de module dubbel op met de [getTwinAsync-methode.](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync)

1. Maak een listener (door een callback door te geven) om wijzigingen in de gewenste eigenschappen te ondervangen met de [methode SetDesiredPropertyUpdateCallbackAsync.](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync)

1. Werk in de callback-methode de gerapporteerde eigenschappen in de module dubbel bij met de [methode UpdateReportedPropertiesAsync,](/dotnet/api/microsoft.azure.devices.client.moduleclient) waarbij u een [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) door geeft van de eigenschapswaarden die u wilt instellen.

## <a name="access-the-module-twins"></a>Toegang tot de module-tweelingen

U kunt de JSON voor module-tweelingen bekijken in de Azure IoT Hub, in Visual Studio Code en met Azure CLI.

### <a name="monitor-in-azure-iot-hub"></a>Bewaken in Azure IoT Hub

De JSON voor de module dubbel weergeven:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.
1. Selecteer **IoT Edge** in het menu van het linkerdeelvenster.
1. Selecteer op **IoT Edge tabblad** Apparaten de **apparaat-id** van het apparaat met de modules die u wilt bewaken.
1. Selecteer de modulenaam op het **tabblad Modules** en selecteer **module-id-tweeling** in de bovenste menubalk.

  ![Selecteer een module dubbel om weer te Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Als u het bericht 'A module identity doesn't exist for this module' (Een module-id bestaat niet voor deze module) ziet, geeft deze fout aan dat de back-endoplossing niet meer beschikbaar is die de identiteit oorspronkelijk heeft gemaakt.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Module-tweelingen bewaken in Visual Studio Code

Een module dubbel controleren en bewerken:

1. Als dit nog niet is geïnstalleerd, installeert [u Azure IoT Tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio Code.
1. Vouw in **explorer** de **Azure IoT Hub** uit en vouw vervolgens het apparaat uit met de module die u wilt bewaken.
1. Klik met de rechtermuisknop op de module en **selecteer Module twin bewerken.** Er wordt een tijdelijk bestand van de module dubbel gedownload naar uw computer en weergegeven in Visual Studio Code.

  ![Een moduletwee krijgen om te bewerken in Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Als u wijzigingen aanwerkt, **selecteert u Module twin** bijwerken boven de code in de editor om wijzigingen in uw IoT-hub op te slaan.

  ![Een module twin bijwerken in Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Module-tweelingen bewaken in Azure CLI

Als u wilt zien IoT Edge wordt uitgevoerd, gebruikt u [de az iot hub invoke-module-method om](how-to-edgeagent-direct-method.md#ping) de agent IoT Edge pingen.

De [az iot hub module-twin](/cli/azure/iot/hub/module-twin) structure biedt deze opdrachten:

* **az iot hub module-twin show** - Show a module twin definition.
* **az iot hub module-twin update** - Update a module twin definition.
* **az iot hub module-twin replace** - Vervang de definitie van een module twin door een doel-JSON.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het communiceren met EdgeAgent met behulp van ingebouwde directe methoden](how-to-edgeagent-direct-method.md).
