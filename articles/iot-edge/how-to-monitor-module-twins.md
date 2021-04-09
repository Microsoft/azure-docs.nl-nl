---
title: Bewaak module apparaatdubbels-Azure IoT Edge
description: Apparaatdubbels en module apparaatdubbels interpreteren om de connectiviteit en status te bepalen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b7013979199eefa873a651d99e87dc8b2c47856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201606"
---
# <a name="monitor-module-twins"></a>Dubbele modules bewaken

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Met de module apparaatdubbels in IoT Hub Azure kunt u de connectiviteit en status van uw IoT Edge-implementaties controleren. Module apparaatdubbels bevatten nuttige informatie over de prestaties van uw actieve modules in uw IoT-hub. De [IOT Edge-agent](iot-edge-runtime.md#iot-edge-agent) en de runtime modules van [IOT Edge hub](iot-edge-runtime.md#iot-edge-hub) onderhouden hun module apparaatdubbels `$edgeAgent` en `$edgeHub` respectievelijk:

* `$edgeAgent` bevat status-en connectiviteits gegevens over de IoT Edge agent en IoT Edge hub runtime modules en uw aangepaste modules. De IoT Edge-agent is verantwoordelijk voor het implementeren van de modules, het bewaken ervan en het rapporteren van de verbindings status aan uw Azure IoT hub.
* `$edgeHub` bevat gegevens over de communicatie tussen de IoT Edge hub die op een apparaat wordt uitgevoerd en uw Azure IoT hub. Dit omvat het verwerken van inkomende berichten van downstream-apparaten. IoT Edge hub is verantwoordelijk voor het verwerken van de communicatie tussen de Azure-IoT Hub en de IoT Edge apparaten en modules.

De gegevens zijn ingedeeld in meta gegevens, tags en de gewenste en gerapporteerde eigenschappen sets in de apparaatdubbels JSON-structuren van de module. De gewenste eigenschappen die u hebt opgegeven in uw deployment.jsvoor het bestand worden gekopieerd naar de module apparaatdubbels. Met de IoT Edge-agent en de IoT Edge-hub worden de gerapporteerde eigenschappen voor hun modules bijgewerkt.

Op dezelfde manier worden de gewenste eigenschappen voor uw aangepaste modules in de deployment.jsin het bestand gekopieerd naar de bijbehorende module, maar is uw oplossing verantwoordelijk voor het leveren van de gerapporteerde eigenschaps waarden.

In dit artikel wordt beschreven hoe u de module apparaatdubbels in de Azure Portal, Azure CLI en Visual Studio code kunt controleren. Zie [IOT Edge-implementaties bewaken](how-to-monitor-iot-edge-deployments.md)voor meer informatie over het controleren van de manier waarop uw apparaten de implementaties ontvangen. Zie voor een overzicht van het concept van module apparaatdubbels [begrijpen en module apparaatdubbels gebruiken in IOT hub](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> De gerapporteerde eigenschappen van een runtime-module kunnen worden verouderd als een IoT Edge apparaat wordt losgekoppeld van de IoT-hub. U kunt de module [pingen](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` om te bepalen of de verbinding is verbroken.

## <a name="monitor-runtime-module-twins"></a>Runtime module apparaatdubbels bewaken

Bekijk de IoT Edge agent en IoT Edge hub runtime module apparaatdubbels en zoom in op andere modules voor het oplossen van verbindings problemen met de implementatie.

### <a name="monitor-iot-edge-agent-module-twin"></a>IoT Edge-Agent module dubbele controle bewaken

De volgende JSON toont de `$edgeAgent` module twee in Visual Studio code met de meeste JSON-secties samengevouwen.

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

* Meta gegevens-bevat verbindings gegevens. De verbindings status voor de IoT Edge-agent is in het gepaarte toestand altijd een status die niet verbonden is: `"connectionState": "Disconnected"` . De reden hiervoor is dat de verbindings status van toepassing is op D2C-berichten (apparaat-naar-Cloud) en dat de IoT Edge-agent geen D2C-berichten verzendt.
* Eigenschappen: bevat de `desired` `reported` subsecties en.
* Eigenschappen. desired: verwachte eigenschaps waarden die zijn ingesteld door de operator in het deployment.jsin het bestand (samengevouwen).
* Properties. gerapporteerd-meest recente eigenschaps waarden die zijn gerapporteerd door IoT Edge agent.

Zowel de- `properties.desired` als- `properties.reported` secties hebben een vergelijk bare structuur en bevatten aanvullende meta gegevens voor schema-, versie-en runtime-informatie. Is ook opgenomen `modules` in de sectie voor alle aangepaste modules (zoals `SimulatedTemperatureSensor` ) en de `systemModules` sectie voor `$edgeAgent` en de `$edgeHub` runtime modules.

Door de gerapporteerde eigenschaps waarden te vergelijken met de gewenste waarden, kunt u verschillen vaststellen en ontkoppelingen identificeren die u kunnen helpen bij het oplossen van problemen. In deze vergelijkingen controleert `$lastUpdated` u de gerapporteerde waarde in de `metadata` sectie voor de eigenschap die u wilt onderzoeken.

De volgende eigenschappen zijn belang rijk voor het oplossen van problemen:

* **ExitCode** -een andere waarde dan nul geeft aan dat de module is gestopt met een fout. Fout codes 137 en 143 worden echter gebruikt als een module opzettelijk is ingesteld op status gestopt.

* **lastStartTimeUtc** : hier wordt de **datum/tijd** weer gegeven waarop de container voor het laatst is gestart. Deze waarde is 0001-01-01T00:00:00Z als de container niet is gestart.

* **lastExitTimeUtc** : toont de **datum/tijd** waarop de container de laatste keer is voltooid. Deze waarde is 0001-01-01T00:00:00Z als de container actief is en nooit is gestopt.

* **runtimeStatus** -kan een van de volgende waarden hebben:

    | Waarde | Beschrijving |
    | --- | --- |
    | unknown | Standaard status totdat de implementatie is gemaakt. |
    | uitstel | De module is gepland om te starten, maar wordt momenteel niet uitgevoerd. Deze waarde is handig voor het wijzigen van de status van een module bij het opnieuw opstarten. Wanneer een niet-werkende module tijdens de koel periode opnieuw moet worden opgestart, heeft de module de status uitstel. |
    | Voer | Geeft aan dat de module op dit moment wordt uitgevoerd. |
    | slechte | Hiermee wordt aangegeven dat een status controle is mislukt of er een time-out is opgetreden. |
    | gestopt | Geeft aan dat de module is afgesloten (met een afsluit code van nul). |
    | mislukt | Geeft aan dat de module is afgesloten met een fout bij een afsluit code (niet nul). De module kan van deze status terugvallen op uitstel, afhankelijk van het beleid voor opnieuw opstarten. Deze status kan erop duiden dat er een onherstelbare fout is opgetreden in de module. Er treedt een fout op wanneer de MMA (micro soft Monitoring Agent) de module niet meer kan resuscitate, waardoor er een nieuwe implementatie is vereist. |

Zie [EdgeAgent-gerapporteerde eigenschappen](module-edgeagent-edgehub.md#edgeagent-reported-properties) voor meer informatie.

### <a name="monitor-iot-edge-hub-module-twin"></a>IoT Edge hub-module bewaken dubbele

De volgende JSON toont de `$edgeHub` module twee in Visual Studio code met de meeste JSON-secties samengevouwen.

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

* Meta gegevens-bevat verbindings gegevens.

* Eigenschappen: bevat de `desired` `reported` subsecties en.
* Eigenschappen. desired: verwachte eigenschaps waarden die zijn ingesteld door de operator in het deployment.jsin het bestand (samengevouwen).
* Properties. gerapporteerd-meest recente eigenschaps waarden die zijn gerapporteerd door IoT Edge hub.

Als u problemen ondervindt met uw downstream-apparaten, is het controleren van deze gegevens een goede plaats om te starten.

## <a name="monitor-custom-module-twins"></a>Aangepaste module apparaatdubbels bewaken

De informatie over de connectiviteit van uw aangepaste modules wordt onderhouden in de module IoT Edge agent. De module twee voor uw aangepaste module wordt voornamelijk gebruikt voor het onderhouden van gegevens voor uw oplossing. De gewenste eigenschappen die u in uw deployment.jsin het bestand hebt gedefinieerd, worden weer gegeven in de module dubbele en de module kan de gerapporteerde eigenschaps waarden naar wens bijwerken.

U kunt uw favoriete programmeer taal gebruiken met de [sdk's van het Azure IOT hub apparaat](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) om de gerapporteerde eigenschaps waarden in de module twee bij te werken, op basis van de toepassings code van uw module. De volgende procedure maakt gebruik van de Azure SDK voor .NET om dit te doen met behulp van code uit de [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) -module:

1. Maak een instantie van de [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) met de methode [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) .

1. Haal een verzameling van de eigenschappen van de module twee op met de methode [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) .

1. Een listener maken (een call back door geven) om wijzigingen aan de gewenste eigenschappen te ondervangen met de methode [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) .

1. Werk in uw call back-methode de gerapporteerde eigenschappen bij in de module, met de methode [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) , waarbij een [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) wordt door gegeven van de eigenschaps waarden die u wilt instellen.

## <a name="access-the-module-twins"></a>Toegang tot de module apparaatdubbels

U kunt de JSON voor module apparaatdubbels bekijken in azure IoT Hub, Visual Studio code en Azure CLI.

### <a name="monitor-in-azure-iot-hub"></a>Bewaken in azure IoT Hub

De JSON weer geven voor de module dubbele:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.
1. Selecteer **IOT Edge** in het menu van het linkerdeel venster.
1. Selecteer op het tabblad **IOT edge apparaten** de **apparaat-id** van het apparaat met de modules die u wilt bewaken.
1. Selecteer de module naam op het tabblad **modules** en selecteer vervolgens **module-identiteit, twee** in de bovenste menu balk.

  ![Selecteer een module die u wilt weer geven in de Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Als u het bericht ' er bestaat geen module-identiteit voor deze module ' wordt weer gegeven, geeft deze fout aan dat de back-end-oplossing niet langer beschikbaar is waardoor de identiteit oorspronkelijk is gemaakt.

### <a name="monitor-module-twins-in-visual-studio-code"></a>De module apparaatdubbels in Visual Studio code bewaken

Ga als volgt te werk om een module te controleren en te bewerken:

1. Als dat nog niet is gebeurd, installeert u de [Azure IOT tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio code.
1. Vouw in de **Explorer** het **Azure-IOT hub** uit en vouw vervolgens het apparaat uit met de module die u wilt bewaken.
1. Klik met de rechter muisknop op de module en selecteer **module bewerken dubbele**. Er wordt een tijdelijk bestand van de module twee naar uw computer gedownload en weer gegeven in Visual Studio code.

  ![Een module vinden die is verdubbeld voor bewerken in Visual Studio code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Als u wijzigingen aanbrengt, selecteert u **Update module** nadaarboven boven de code in de editor om de wijzigingen op te slaan in uw IOT-hub.

  ![Een module dubbele in Visual Studio code bijwerken](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Module apparaatdubbels in azure CLI bewaken

Als u wilt zien of IoT Edge wordt uitgevoerd, gebruikt u de [methode AZ IOT hub invoke-module](how-to-edgeagent-direct-method.md#ping) om de IOT Edge agent te pingen.

De [AZ IOT hub-module-dubbele](/cli/azure/ext/azure-iot/iot/hub/module-twin) structuur biedt de volgende opdrachten:

* **AZ IOT hub module-dubbele weer gave** -een module definitie weer geven.
* **AZ IOT hub module-dubbele update** -een module definitie bijwerken.
* **AZ IOT hub module-dubbel vervangen** -een module definitie vervangen door een doel-JSON.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u kunt [communiceren met EdgeAgent met behulp van ingebouwde directe methoden](how-to-edgeagent-direct-method.md).
