---
title: Azure IoT Hub module-identiteit en-module-id (python)
description: Meer informatie over het maken van module-identiteits-en update module, met behulp van IoT Sdk's voor python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139406"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Aan de slag met IoT Hub module identiteit en module dubbele (python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Module-identiteiten en module apparaatdubbels](iot-hub-devguide-module-twins.md) zijn vergelijkbaar met de apparaat-Id's van Azure IOT hub en apparaat-apparaatdubbels, maar bieden een nauw keurige granulatie. Hoewel Azure IoT Hub apparaat-id's en apparaatdubbels een back-end-toepassing in staat stellen om een apparaat te configureren en inzicht te geven in de voor waarden, module-identiteiten en module-apparaatdubbels bieden deze mogelijkheden voor afzonderlijke onderdelen van een apparaat. Op apparaten met meerdere onderdelen, zoals apparaten op basis van een besturings systeem of firmware-apparaten, kunnen ze geïsoleerde configuratie en voor waarden voor elk onderdeel toestaan.
>

Aan het einde van deze zelf studie hebt u drie python-apps:

* **CreateModule**, waarmee een apparaat-id, een module-ID en de bijbehorende beveiligings sleutels worden gemaakt om uw apparaat en module-clients te verbinden.

* **UpdateModuleTwinDesiredProperties**, die bijgewerkte module dubbele gewenste eigenschappen naar uw IOT hub verzendt.

* **ReceiveModuleTwinDesiredPropertiesPatch**, die de module dubbele gewenste eigenschappen patch op het apparaat ontvangt.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service die een apparaat toevoegt in het identiteits register en vervolgens een module toevoegt aan dat apparaat. Voor deze service is de schrijf machtiging voor het **REGI ster** vereist (ook wel **REGI ster lezen** heeft). U maakt ook een service waarmee gewenste eigenschappen worden toegevoegd aan de module dubbele voor de zojuist gemaakte module. Deze service heeft de machtiging voor het maken van een **service** nodig. Hoewel er standaard beleid voor gedeelde toegang is waarmee deze machtigingen afzonderlijk worden verleend, kunt u in deze sectie een aangepast beleid voor gedeelde toegang maken dat beide machtigingen bevat.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Een apparaat-id en een module-ID maken in IoT Hub

In deze sectie maakt u een python-service-app die een apparaat-id en een module-ID maakt in het identiteits register van uw IoT-hub. Een apparaat of module kan geen verbinding maken met IoT hub, tenzij er een vermelding is in het identiteits register. Zie [inzicht in het identiteits register in uw IOT-hub](iot-hub-devguide-identity-registry.md)voor meer informatie. Als u deze consoletoepassing uitvoert, worden er een unieke id en een unieke sleutel gemaakt voor zowel het apparaat als de module. Deze waarden worden door het apparaat en de module gebruikt om zichzelf te identificeren bij het verzenden van apparaat-naar-cloud-berichten naar IoT Hub. De id's zijn hoofdlettergevoelig.

1. Voer bij de opdracht prompt de volgende opdracht uit om het **Azure-IOT-hub-** pakket te installeren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Voer bij de opdracht prompt de volgende opdracht uit om het **msrest** -pakket te installeren. U hebt dit pakket nodig om **HTTPOperationError** -uitzonde ringen te ondervangen.

    ```cmd/sh
    pip install msrest
    ```

1. Maak met behulp van een tekst editor een bestand met de naam **CreateModule.py** in uw werkmap.

1. Voeg de volgende code toe aan het python-bestand. Vervang *YourIoTHubConnectionString* door de Connection String u hebt gekopieerd in [de IoT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Voer bij de opdracht prompt de volgende opdracht uit:

    ```cmd/sh
    python CreateModule.py
    ```

Met deze app maakt u een apparaat-id met de naam **myFirstDevice** en een module-ID met id **MyFirstModule** onder apparaat **myFirstDevice**. (Als het apparaat of de module-ID al bestaat in het identiteits register, haalt de code gewoon de bestaande apparaat-of module gegevens op.) In de app worden de ID en de primaire sleutel voor elke identiteit weer gegeven.

> [!NOTE]
> In het identiteitsregister van IoT Hub worden alleen apparaat- en module-id's opgeslagen waarmee veilig toegang tot de IoT-hub kan worden verkregen. In het identiteitsregister worden apparaat-id's en -sleutels opgeslagen die als beveiligingsreferenties worden gebruikt. In het identiteitsregister wordt ook een vlag ingeschakeld/uitgeschakeld voor elk apparaat opgeslagen die u kunt gebruiken om de toegang tot dat apparaat uit te schakelen. Als uw toepassing andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke opslagmethode gebruiken. Er is geen vlag voor ingeschakeld/uitgeschakeld voor module-id's. Zie [inzicht in het identiteits register in uw IOT-hub](iot-hub-devguide-identity-registry.md)voor meer informatie.
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>De module bijwerken met behulp van de python-Service-SDK

In deze sectie maakt u een python-service-app die de module dubbele gewenste eigenschappen bijwerkt.

1. Voer bij de opdracht prompt de volgende opdracht uit om het **Azure-IOT-hub-** pakket te installeren. U kunt deze stap overs Laan als u het pakket **Azure-IOT-hub** hebt geïnstalleerd in de vorige sectie.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Maak met behulp van een tekst editor een bestand met de naam **UpdateModuleTwinDesiredProperties.py** in uw werkmap.

1. Voeg de volgende code toe aan het python-bestand. Vervang *YourIoTHubConnectionString* door de Connection String u hebt gekopieerd in [de IoT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Updates aan het apparaat zijde ophalen

In deze sectie maakt u een python-app om de module dubbele gewenste eigenschappen op uw apparaat bij te werken.

1. Haal uw module connection string op. Ga in [Azure Portal](https://portal.azure.com/)naar uw IOT hub en selecteer **IOT-apparaten** in het linkerdeel venster. Selecteer **myFirstDevice** in de lijst met apparaten en open het. Onder **module-identiteiten** selecteert u **myFirstModule**. Kopieer de moduleverbindingsreeks. U hebt deze nodig in een volgende stap.

   ![Details van de Azure Portal-module](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Voer bij de opdracht prompt de volgende opdracht uit om het **Azure-IOT-Device-** pakket te installeren:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Maak met behulp van een tekst editor een bestand met de naam **ReceiveModuleTwinDesiredPropertiesPatch.py** in uw werkmap.

1. Voeg de volgende code toe aan het python-bestand. Vervang *YourModuleConnectionString* door de module Connection String die u in stap 1 hebt gekopieerd.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

In deze sectie voert u de **ReceiveModuleTwinDesiredPropertiesPatch** -app uit en voert u de **UpdateModuleTwinDesiredProperties** service-app uit om de gewenste eigenschappen van uw module bij te werken.

1. Open een opdracht prompt en voer de apparaat-app uit:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Initiële uitvoer van apparaat-app](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Open een afzonderlijke opdracht prompt en voer de service-app uit:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    U ziet dat de gewenste eigenschap **TelemetryInterval** wordt weer gegeven in de bijgewerkte module, dubbele in uw service-app-uitvoer:

   ![Service-app-uitvoer](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Dezelfde eigenschap wordt weer gegeven in de gewenste eigenschappen patch die is ontvangen in de app-uitvoer van uw apparaat:

   ![Apparaat-app-uitvoer toont gewenste eigenschappen patch](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan met IoT Hub en andere IoT-scenario's wilt verkennen, leest u deze artikelen:

* [Aan de slag met apparaatbeheer](iot-hub-node-node-device-management-get-started.md)

* [Aan de slag met IoT Edge](../iot-edge/quickstart-linux.md)