---
title: Aan de slag met Azure IoT Hub Device Management (python) | Microsoft Docs
description: IoT Hub Apparaatbeheer gebruiken om het opnieuw opstarten van een extern apparaat te initiëren. U gebruikt de Azure IoT SDK voor python voor het implementeren van een gesimuleerde apparaat-app die een directe methode en een service-app bevat die de directe methode aanroept.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: d4c514042b89341b90b0bb9c939ef4b463741916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87872715"
---
# <a name="get-started-with-device-management-python"></a>Aan de slag met Apparaatbeheer (python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze zelfstudie ontdekt u hoe u:

* Gebruik de Azure Portal voor het maken van een IoT Hub en het maken van een apparaat-id in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat waarmee dat apparaat opnieuw wordt opgestart. Directe methoden worden vanuit de Cloud aangeroepen.

* Maak een python-console-app die de directe methode voor opnieuw opstarten aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het einde van deze zelf studie hebt u twee python-console-apps:

* **dmpatterns_getstarted_device. py**, dat verbinding maakt met uw IOT-hub met de apparaat-id die u eerder hebt gemaakt, ontvangt een directe methode voor opnieuw opstarten, simuleert fysieke opnieuw opstarten en rapporteert de tijd voor de laatste keer opnieuw opstarten.

* **dmpatterns_getstarted_service. py**, waarmee een directe methode wordt aangeroepen in de gesimuleerde apparaat-app, het antwoord wordt weer gegeven en de bijgewerkte gerapporteerde eigenschappen worden weer gegeven.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* Een python-console-app maken die reageert op een directe methode die wordt aangeroepen door de Cloud

* Opnieuw opstarten van een apparaat simuleren

* Gebruik de gerapporteerde eigenschappen om Device-dubbele query's in te scha kelen om apparaten te identificeren en wanneer deze voor het laatst opnieuw zijn opgestart

1. Voer bij de opdracht prompt de volgende opdracht uit om het **Azure-IOT-Device-** pakket te installeren:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Maak met behulp van een tekst editor een bestand met de naam **dmpatterns_getstarted_device. py** in uw werkmap.

3. Voeg de volgende- `import` instructies toe aan het begin van het bestand **dmpatterns_getstarted_device. py** .

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Voeg de variabele **CONNECTION_STRING** toe. Vervang de `{deviceConnectionString}` waarde van de tijdelijke aanduiding door de Connection String van uw apparaat. U hebt dit connection string eerder in [de IOT-hub van een nieuw apparaat geregistreerd](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Voeg de volgende functie-call backs toe om de directe methode op het apparaat te implementeren.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Start de listener voor directe methoden en wacht.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Sla het bestand **dmpatterns_getstarted_device. py** op en sluit het.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productie code moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële uitstel), zoals wordt voorgesteld in het artikel, [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe keer opnieuw opstarten op het apparaat activeren met behulp van een directe methode

In deze sectie maakt u een python-console-app die extern opnieuw opstarten op een apparaat initieert via een directe methode. De app maakt gebruik van Device-dubbele query's om de tijd van de laatste keer opnieuw opstarten voor dat apparaat te detecteren.

1. Voer bij de opdracht prompt de volgende opdracht uit om het **Azure-IOT-hub-** pakket te installeren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Maak met behulp van een tekst editor een bestand met de naam **dmpatterns_getstarted_service. py** in uw werkmap.

3. Voeg de volgende- `import` instructies toe aan het begin van het bestand **dmpatterns_getstarted_service. py** .

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Voeg de volgende variabelen declaraties toe. Vervang de `{IoTHubConnectionString}` waarde van de tijdelijke aanduiding door de IOT hub-Connection String die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string). Vervang de `{deviceId}` waarde van de tijdelijke aanduiding door de apparaat-id die u hebt geregistreerd in [Registreer een nieuw apparaat in de IOT-hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Voeg de volgende functie toe om de methode van het apparaat aan te roepen om het doel apparaat opnieuw op te starten en zoek vervolgens naar het apparaat apparaatdubbels en ontvang de laatste keer opnieuw opstarten.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Sla het bestand **dmpatterns_getstarted_service. py** op en sluit het.

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de apps uit te voeren.

1. Voer bij de opdracht prompt de volgende opdracht uit om te beginnen met Luis teren naar de methode voor direct opnieuw opstarten.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Voer bij een andere opdracht prompt de volgende opdracht uit om het apparaat op afstand opnieuw op te starten en de query uit te voeren op de tijd van de laatste keer opnieuw opstarten.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. U ziet de reactie van het apparaat op de directe methode in de-console.

   Hieronder ziet u de reactie van het apparaat op de directe methode voor opnieuw opstarten:

   ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-python-python-device-management-get-started/device.png)

   Hieronder ziet u de service die de directe methode voor opnieuw opstarten aanroept en het apparaat navraagt naar de status:

   ![Uitvoer van start service activeren](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
