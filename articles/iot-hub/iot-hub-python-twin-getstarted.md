---
title: Aan de slag met Azure IoT Hub Device apparaatdubbels (python) | Microsoft Docs
description: Azure IoT Hub Device apparaatdubbels gebruiken om labels toe te voegen en vervolgens een IoT Hub query te gebruiken. U gebruikt de Azure IoT Sdk's voor python voor het implementeren van de gesimuleerde apparaat-app en een service-app waarmee de tags worden toegevoegd en de IoT Hub query wordt uitgevoerd.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87875469"
---
# <a name="get-started-with-device-twins-python"></a>Aan de slag met Device apparaatdubbels (python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Aan het einde van deze zelf studie hebt u twee python-console-apps:

* **AddTagsAndQuery.py**, een python back-end-app, waarmee labels en query's apparaat apparaatdubbels worden toegevoegd.

* **ReportConnectivity.py**, een python-app, die een apparaat simuleert dat verbinding maakt met uw IOT-hub met de apparaat-id die u eerder hebt gemaakt, en rapporteert de connectiviteits voorwaarde.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een python-console-app waarmee de meta gegevens van de locatie worden toegevoegd aan het apparaat dat is gekoppeld aan uw **{apparaat-id}**. Vervolgens wordt een query uitgevoerd op het apparaat apparaatdubbels dat is opgeslagen in de IoT-hub en vervolgens de apparaten selecteert die zich in Redmond bevinden. vervolgens wordt een mobiele verbinding gerapporteerd.

1. Open in uw werkmap een opdracht prompt en installeer de **Azure IOT hub Service SDK voor python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Maak een nieuw **AddTagsAndQuery.py** -bestand met behulp van een tekst editor.

3. Voeg de volgende code toe om de vereiste modules uit de service-SDK te importeren:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Voeg de volgende code toe. Vervang door `[IoTHub Connection String]` de IOT hub-Connection String die u hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string). Vervang door `[Device Id]` de apparaat-id die u hebt geregistreerd in [een nieuw apparaat registreren in de IOT-hub](#register-a-new-device-in-the-iot-hub).
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Voeg de volgende code toe aan het **AddTagsAndQuery.py** -bestand:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    Het **IoTHubRegistryManager** -object bevat alle methoden die nodig zijn om te communiceren met de apparaatdubbels van de service. De code initialiseert eerst het **IoTHubRegistryManager** -object, werkt het apparaat vervolgens bij met de dubbele voor **DEVICE_ID** en voert ten slotte twee query's uit. De eerste selecteert alleen het apparaat apparaatdubbels van apparaten die zich in de **Redmond43** -installatie bevinden en de tweede verfijnt de query om alleen de apparaten te selecteren die ook zijn verbonden via een mobiel netwerk.

6. Voeg de volgende code toe aan het einde van  **AddTagsAndQuery.py** om de **iothub_service_sample_run** -functie te implementeren:

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Voer de toepassing uit met:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    U ziet één apparaat in de resultaten voor de query die vraagt naar alle apparaten in **Redmond43** en geen voor de query waarmee de resultaten worden beperkt tot apparaten die gebruikmaken van een mobiel netwerk.

    ![eerste query waarin alle apparaten in Redmond worden weer gegeven](./media/iot-hub-python-twin-getstarted/service-1.png)

In de volgende sectie maakt u een apparaat-app die de connectiviteits gegevens rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een python-console-app die verbinding maakt met uw hub als uw **{apparaat-id}** en vervolgens de gerapporteerde eigenschappen van het apparaat bijwerkt om de gegevens te bevatten die zijn verbonden met behulp van een mobiel netwerk.

1. Installeer in de werkmap van een opdracht prompt de **Azure IOT hub Device SDK voor python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Maak een nieuw **ReportConnectivity.py** -bestand met behulp van een tekst editor.

3. Voeg de volgende code toe om de vereiste modules uit de SDK van het apparaat te importeren:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Voeg de volgende code toe. Vervang de `[IoTHub Device Connection String]` waarde van de tijdelijke aanduiding door het apparaat Connection String dat u hebt gekopieerd in [een nieuw apparaat registreren in de IOT-hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Voeg de volgende code toe aan het **ReportConnectivity.py** -bestand om de apparaatdubbels-functionaliteit van het apparaat te implementeren:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    Het **IoTHubModuleClient** -object bevat alle methoden die u nodig hebt om te communiceren met de apparaatdubbels van het apparaat. De vorige code, na het initialiseren van het **IoTHubModuleClient** -object, haalt het apparaat op voor uw apparaat en werkt de gerapporteerde eigenschap bij met de verbindings gegevens.

6. Voeg de volgende code toe aan het einde van  **ReportConnectivity.py** om de **iothub_client_sample_run** -functie te implementeren:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. De apparaat-app uitvoeren:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    U ziet dat er een bevestiging is gegeven dat de dubbele gerapporteerde eigenschappen van het apparaat zijn bijgewerkt.

    ![gerapporteerde eigenschappen van apparaat-app bijwerken](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Nu het apparaat de verbindings gegevens heeft gerapporteerd, zou het in beide query's moeten worden weer gegeven. Ga terug en voer de query's opnieuw uit:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Deze keer dat uw **{device id}** in beide query resultaten moet worden weer gegeven.

    ![tweede query op service-app](./media/iot-hub-python-twin-getstarted/service-2.png)

    In de app van uw apparaat ziet u een bevestiging dat de gewenste eigenschappen dubbele patch die door de service-app is verzonden, is ontvangen.

    ![gewenste eigenschappen ontvangen voor de apparaat-app](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt meta gegevens van apparaten toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om connectiviteits gegevens van apparaten te rapporteren in het dubbele apparaat. U hebt ook geleerd hoe u deze gegevens in het REGI ster kunt opvragen.

Gebruik de volgende bronnen voor meer informatie over:

* Verzend telemetrie van apparaten met de zelf studie [aan de slag met IOT hub](quickstart-send-telemetry-python.md) .

* Configureer apparaten met behulp van de gewenste eigenschappen van het apparaat met de zelf studie [gewenste eigenschappen gebruiken om apparaten te configureren](tutorial-device-twins.md) .

* Apparaten interactief beheren (bijvoorbeeld door een ventilator in te scha kelen vanuit een door de gebruiker beheerde app), met de zelf studie [directe methoden gebruiken](quickstart-control-device-python.md) .
