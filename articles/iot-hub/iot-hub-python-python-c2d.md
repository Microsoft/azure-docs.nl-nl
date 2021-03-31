---
title: Cloud-naar-apparaat-berichten met Azure IoT Hub (python) | Microsoft Docs
description: Cloud-naar-apparaat-berichten verzenden naar een apparaat vanuit een Azure IoT-hub met behulp van de Azure IoT Sdk's voor python. U wijzigt een gesimuleerde apparaat-app om Cloud-naar-apparaat-berichten te ontvangen en een back-end-app te wijzigen om de Cloud-naar-apparaat-berichten te verzenden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: ad6399a4713520ca0550d143cf3f19f87d55337c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87876798"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Cloud-naar-apparaat-berichten verzenden met IoT Hub (python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die zorgt voor betrouw bare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing. Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-python.md) Quick Start laat zien hoe u een IOT-hub kunt maken, een apparaat-id kunt inrichten en een gesimuleerde apparaat-app kunt coderen die apparaat-naar-Cloud-berichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelf studie is gebaseerd op het [verzenden van telemetrie van een apparaat naar een IOT-hub](quickstart-send-telemetry-python.md). U ziet hoe u het volgende kunt doen:

* Via de back-end van uw oplossing kunt u Cloud-naar-apparaat-berichten verzenden naar één apparaat via IoT Hub.

* Cloud-naar-apparaat-berichten op een apparaat ontvangen.

Meer informatie over Cloud-naar-apparaat-berichten vindt u in de [hand leiding voor IOT hub ontwikkel aars](iot-hub-devguide-messaging.md).

Aan het einde van deze zelf studie voert u twee python-console-apps uit:

* **SimulatedDevice.py**, een gewijzigde versie van de app die is gemaakt in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-python.md), die verbinding maakt met uw IOT-hub en Cloud-naar-apparaat-berichten ontvangt.

* **SendCloudToDeviceMessage.py**, waarmee Cloud-naar-apparaat-berichten worden verzonden naar de gesimuleerde apparaat-app via IOT hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie maakt u een python-console-app voor het simuleren van het apparaat en het ontvangen van Cloud-naar-apparaat-berichten van de IoT-hub.

1. Installeer in de werkmap van een opdracht prompt de **Azure IOT hub Device SDK voor python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Maak een bestand met de naam **SimulatedDevice.py** met behulp van een tekst editor.

1. Voeg de volgende `import` instructies en variabelen toe aan het begin van het **SimulatedDevice.py** -bestand:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Voeg de volgende code toe aan het **SimulatedDevice.py** -bestand. Vervang de `{deviceConnectionString}` waarde van de tijdelijke aanduiding door het apparaat Connection String voor het apparaat dat u hebt gemaakt in de Quick Start [van een apparaat naar een IOT hub](quickstart-send-telemetry-python.md) -Snelstartgids:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Voeg de volgende functie toe om ontvangen berichten af te drukken naar de-console:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Voeg de volgende code toe om de client te initialiseren en te wachten op het ontvangen van het Cloud-naar-apparaat-bericht:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Voeg de volgende hoofd functie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Sla het bestand **SimulatedDevice.py** op en sluit het.

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om Cloud-naar-apparaat-berichten te verzenden via de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-python.md). Als u Cloud-naar-apparaat-berichten wilt verzenden, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een Cloud-naar-apparaat-bericht verzenden

In deze sectie maakt u een python-console-app die Cloud-naar-apparaat-berichten naar de gesimuleerde apparaat-app verzendt. U hebt de apparaat-ID van het apparaat dat u hebt toegevoegd in de [telemetrie van een apparaat verzenden naar een IOT hub](quickstart-send-telemetry-python.md) Quick Start. U hebt ook de IoT hub-connection string nodig die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

1. Open in uw werkmap een opdracht prompt en installeer de **Azure IOT hub Service SDK voor python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Maak een bestand met de naam **SendCloudToDeviceMessage.py** met behulp van een tekst editor.

1. Voeg de volgende `import` instructies en variabelen toe aan het begin van het **SendCloudToDeviceMessage.py** -bestand:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Voeg de volgende code toe aan het **SendCloudToDeviceMessage.py** -bestand. Vervang de `{iot hub connection string}` waarden en de `{device id}` tijdelijke aanduiding door de Connection String van de IOT-hub en de apparaat-id die u eerder hebt genoteerd:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Voeg de volgende code toe om berichten te verzenden naar uw apparaat:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Voeg de volgende hoofd functie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Sla het **SendCloudToDeviceMessage.py** -bestand op en sluit het.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdracht prompt in uw werkmap de volgende opdracht uit om te Luis teren naar Cloud-naar-apparaat-berichten:

    ```shell
    python SimulatedDevice.py
    ```

    ![De app voor een gesimuleerd apparaat uitvoeren](./media/iot-hub-python-python-c2d/device-1.png)

1. Open een nieuwe opdracht prompt in de werkmap en voer de volgende opdracht uit om Cloud-naar-apparaat-berichten te verzenden:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Voer de app uit om de Cloud-naar-apparaat-opdracht te verzenden](./media/iot-hub-python-python-c2d/service.png)

1. Noteer de berichten die door het apparaat worden ontvangen.

    ![Bericht ontvangen](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Cloud-naar-apparaat-berichten kunt verzenden en ontvangen.

Voor voor beelden van complete end-to-end-oplossingen die gebruikmaken van IoT Hub, raadpleegt u de [Azure IOT-oplossing voor externe controle](https://azure.microsoft.com/documentation/suites/iot-suite/).

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IOT hub.
