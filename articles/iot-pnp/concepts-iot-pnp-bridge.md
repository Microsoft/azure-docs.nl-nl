---
title: IoT Plug en Play-brug | Microsoft Docs
description: Begrijp de IoT Plug en Play-brug en hoe u deze kunt gebruiken voor het verbinden van bestaande apparaten die zijn gekoppeld aan een Windows-of Linux-gateway als IoT Plug en Play-apparaten.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a45efd90043ecb4d457db7ed39651f1a9b5bbd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98890604"
---
# <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-brug

De IoT Plug en Play Bridge is een open-source toepassing voor het verbinden van bestaande apparaten die zijn gekoppeld aan Windows of Linux gateway als IoT Plug en Play-apparaten. Nadat u de toepassing hebt geïnstalleerd en geconfigureerd op uw Windows-of Linux-computer, kunt u deze gebruiken om aangesloten apparaten te verbinden met een IoT-hub. U kunt de Bridge gebruiken om IoT Plug en Play-interfaces toe te wijzen aan de telemetrie die de aangesloten apparaten verzenden, werken met apparaateigenschappen en opdrachten aanroepen.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Aan de linkerkant zijn er een aantal bestaande Sens oren aangesloten (zowel bekabeld als draadloos) aan een Windows-of Linux-computer met IoT Plug en Play-brug. De IoT Plug en Play-brug maakt vervolgens verbinding met een IoT-hub aan de rechter kant":::

IoT Plug en Play Bridge kan worden geïmplementeerd als een zelfstandig uitvoerbaar bestand op een IoT-apparaat, een industriële PC, een server of een gateway met Windows 10 of Linux. Het kan ook worden gecompileerd in de code van uw toepassing. Een eenvoudig configuratie-JSON-bestand vertelt de IoT Plug en Play-brug waarmee aangesloten apparaten/rand apparatuur moet worden blootgesteld aan Azure.

## <a name="supported-protocols-and-sensors"></a>Ondersteunde protocollen en Sens oren

IoT Plug en Play Bridge ondersteunt standaard de volgende typen rand apparatuur, met koppelingen naar de adapter documentatie:

|Rand apparatuur|Windows|Linux|
|---------|---------|---------|
|[Bluetooth-sensor adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) verbindt gedetecteerde Sens oren met Bluetooth-laag energie () ingeschakeld.       |Ja|Nee|
|[Camera adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) verbindt camera's met een Windows 10-apparaat.               |Ja|Nee|
|[Modbus adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) verbindt Sens oren op een Modbus-apparaat.              |Ja|Ja|
|[MQTT-adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) verbindt apparaten die gebruikmaken van een MQTT-Broker.                  |Ja|Ja|
|De [SerialPnP-adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) verbindt apparaten die communiceren via een seriële verbinding.               |Ja|Ja|
|[Windows USB-rand apparatuur](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) maakt gebruik van een lijst met adapters die worden ondersteund door de adapter, om apparaten te verbinden die een specifieke hardware-id hebben.  |Yes|Niet van toepassing|

Zie voor meer informatie over het uitbreiden van de IoT Plug en Play-brug voor het ondersteunen van extra apparaten protocollen [de iot Plug en Play-brug uitbreiden](howto-author-pnp-bridge-adapter.md). Zie voor meer informatie over het bouwen en implementeren van de IoT Plug en Play-brug [de iot Plug en Play-brug bouwen en implementeren](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT Plug en Play Bridge-architectuur

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Aan de linkerkant ziet u verschillende vakken met verschillende rand apparatuur die is gekoppeld aan een Windows-of Linux-PC met IoT Plug en Play-brug. Vanaf de bovenkant wordt een vak met configuratie punten in de richting van de brug aangeduid. De Bridge maakt vervolgens verbinding met een IoT-hub aan de rechter kant van het diagram.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT Plug en Play-brug adapters

IoT Plug en Play Bridge ondersteunt een set IoT Plug en Play Bridge-adapters voor verschillende typen apparaten. Een *adapter manifest* definieert de adapters statisch in een brug.

Bridge-adapter beheer gebruikt het manifest om adapter functies te identificeren en aan te roepen. Adapter beheer roept alleen de functie Create aan op de brug adapters die vereist zijn voor de interface-onderdelen die worden vermeld in het configuratie bestand. Voor elk IoT-Plug en Play onderdeel wordt een adapter exemplaar gemaakt.

Een brug adapter maakt en verkrijgt een digitale twee ledige interface-ingang. De adapter gebruikt deze ingang om de functionaliteit van het apparaat te binden aan de digitale twee.

Met behulp van de informatie in het configuratie bestand gebruikt de brug adapter de volgende technieken om het volledige apparaat in te scha kelen voor digitale dubbele communicatie via de Bridge:

- Hiermee wordt rechtstreeks een communicatie kanaal tot stand gebracht.
- Hiermee maakt u een Watcher waarmee wordt gewacht tot een communicatie kanaal beschikbaar wordt.

### <a name="configuration-file"></a>Configuratiebestand

De IoT Plug en Play Bridge maakt gebruik van een JSON-configuratie bestand waarin het volgende wordt opgegeven:

- Verbinding maken met een IoT hub of IoT Central toepassing: de opties zijn onder andere verbindings reeksen, verificatie parameters of Device Provisioning Service (DPS).
- De locatie van de IoT Plug en Play-mogelijkheden modellen die de Bridge gebruikt. Het model definieert de mogelijkheden van een IoT Plug en Play-apparaat en is statisch en onveranderbaar.
- Een lijst met IoT Plug en Play-interface onderdelen en de volgende informatie voor elk onderdeel:
- De interface-ID en onderdeel naam.
- De brug adapter die is vereist voor de interactie met het onderdeel.
- Apparaatgegevens die de brug adapter nodig heeft om communicatie met het apparaat tot stand te brengen. Bijvoorbeeld hardware-ID of specifieke informatie voor een adapter, Interface of protocol.
- Een optionele brug adapter subtype of interface configuratie als de adapter meerdere communicatie typen met vergelijk bare apparaten ondersteunt. In het voor beeld ziet u hoe een Bluetooth-sensor onderdeel kan worden geconfigureerd:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Een optionele lijst met para meters van de Global Bridge-adapter. De Bluetooth sensor Bridge-adapter heeft bijvoorbeeld een woorden lijst met ondersteunde configuraties. Een interface onderdeel dat de Bluetooth-sensor adapter vereist, kan een van de volgende configuraties kiezen als `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>IoT Plug en Play-brug downloaden

U kunt een vooraf ontwikkelde versie van de brug downloaden met ondersteunde adapters in [IoT Plug en Play Bridge-releases](https://github.com/Azure/iot-plug-and-play-bridge/releases) en de lijst met assets voor de meest recente release uitvouwen. Down load de meest recente versie van de toepassing voor uw besturings systeem.

U kunt ook de bron code van [IoT Plug en Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge)downloaden en weer geven op github.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van de architectuur van IoT Plug en Play Bridge, moeten de volgende stappen worden uitgevoerd om meer te weten te komen over:

- [Verbinding maken met een IoT Plug en Play Bridge-voor beeld dat wordt uitgevoerd in Linux of Windows naar IoT Hub](./howto-use-iot-pnp-bridge.md)
- [IoT Plug en Play-brug bouwen en implementeren](howto-build-deploy-extend-pnp-bridge.md)
- [IoT Plug en Play-brug uitbreiden](howto-author-pnp-bridge-adapter.md)
- [IoT Plug en Play-brug op GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
