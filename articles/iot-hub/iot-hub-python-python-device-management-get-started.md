---
title: Aan de slag met Azure IoT Hub -apparaatbeheer (Python) | Microsoft Docs
description: Het gebruik van IoT Hub om het opnieuw opstarten van een extern apparaat te starten. U gebruikt de Azure IoT SDK voor Python om een gesimuleerde apparaat-app te implementeren die een directe methode en een service-app bevat die de directe methode aanroept.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: c07d26715aff2c8dd5e00a7d7adbb548adf00a28
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482497"
---
# <a name="get-started-with-device-management-python"></a>Aan de slag met apparaatbeheer (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze zelfstudie ontdekt u hoe u:

* Gebruik de Azure Portal om een IoT Hub en maak een apparaat-id in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat waarmee dat apparaat opnieuw wordt opgestart. Directe methoden worden aangeroepen vanuit de cloud.

* Maak een Python-console-app die de directe methode voor opnieuw opstarten aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het einde van deze zelfstudie hebt u twee Python-console-apps:

* **dmpatterns_getstarted_device.py,** dat verbinding maakt met uw IoT-hub met de apparaat-id die u eerder hebt gemaakt, een directe methode voor opnieuw opstarten ontvangt, een fysieke herstart simuleert en de tijd rapporteert voor de laatste keer opnieuw opstarten.

* **dmpatterns_getstarted_service.py,** waarmee een directe methode wordt aanroept in de gesimuleerde apparaat-app, het antwoord wordt weergegeven en de bijgewerkte gerapporteerde eigenschappen worden weergegeven.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in dit artikel wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* Een Python-console-app maken die reageert op een directe methode met de naam door de cloud

* Opnieuw opstarten van een apparaat simuleren

* Gebruik de gerapporteerde eigenschappen om query's voor apparaat dubbels in te stellen om apparaten te identificeren en wanneer ze voor het laatst opnieuw zijn opgestart

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **pakket azure-iot-device te** installeren:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Maak met een teksteditor een bestand met de **naam dmpatterns_getstarted_device.py** in uw werkmap.

3. Voeg de volgende `import` -instructies toe aan het begin **van dmpatterns_getstarted_device.py-bestand.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Voeg de **CONNECTION_STRING** toe. Vervang de `{deviceConnectionString}` waarde van de tijdelijke aanduiding door de connection string. U hebt deze connection string gekopieerd in [Een nieuw apparaat registreren in de IoT-hub](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Voeg de volgende functie-callbacks toe om de directe methode op het apparaat te implementeren.

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

6. Start de directe methodelistener en wacht.

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

7. Sla het bestand **dmpatterns_getstarted_device.py op en** sluit het.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u beleidsregels voor opnieuw proberen implementeren (zoals exponentieel uitvalt), zoals wordt voorgesteld in het artikel Transient Fault Handling ( [Afhandeling van tijdelijke fouten).](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>De IoT Hub-connection string

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe herstart op het apparaat activeren met behulp van een directe methode

In deze sectie maakt u een Python-console-app die extern opnieuw opstarten op een apparaat start met behulp van een directe methode. De app maakt gebruik van query's voor apparaattwee om de laatste keer dat het apparaat opnieuw wordt opgestart te achterhalen.

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **pakket azure-iot-hub te** installeren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Maak met behulp van een teksteditor een **bestand dmpatterns_getstarted_service.py** in uw werkmap.

3. Voeg de volgende `import` -instructies toe aan het begin van **dmpatterns_getstarted_service.py-bestand.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Voeg de volgende variabeledeclaraties toe. Vervang de waarde van de tijdelijke aanduiding door de IoT-hub connection string u eerder hebt gekopieerd in De `{IoTHubConnectionString}` [IoT-hub connection string.](#get-the-iot-hub-connection-string) Vervang de `{deviceId}` waarde van de tijdelijke aanduiding door de apparaat-id die u hebt geregistreerd in Een nieuw apparaat registreren in de [IoT-hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Voeg de volgende functie toe om de apparaatmethode aan te roepen om het doelapparaat opnieuw op te starten, zoek vervolgens naar de apparaattweeling en haal de laatste keer dat het apparaat opnieuw wordt opgestart op.

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

6. Sla het bestand **dmpatterns_getstarted_service.py op en** sluit het.

## <a name="run-the-apps"></a>De apps uitvoeren

U bent nu klaar om de apps uit te voeren.

1. Voer bij de opdrachtprompt de volgende opdracht uit om te luisteren naar de directe methode voor opnieuw opstarten.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Voer bij een andere opdrachtprompt de volgende opdracht uit om het extern opnieuw opstarten te activeren en voer een query uit voor de apparaat dubbel om de laatste keer opnieuw op te starten.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. U ziet de reactie van het apparaat op de directe methode in de console.

   Hieronder ziet u de reactie van het apparaat op de directe methode voor opnieuw opstarten:

   ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-python-python-device-management-get-started/device.png)

   Hieronder ziet u de service die de directe methode voor opnieuw opstarten aanroept en de apparaattweeling peilt op status:

   ![Service-uitvoer voor opnieuw opstarten activeren](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
