---
title: Telemetrie van apparaat verzenden naar Azure IoT Central Snelstartgids (python)
description: In deze Quick Start gebruikt u de Azure IoT Hub Device SDK voor python voor het verzenden van telemetrie van een apparaat naar IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047164"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Quick Start: verzend telemetrie van een apparaat naar Azure IoT Central (python)

**Van toepassing op**: [toepassings ontwikkeling van apparaten](about-iot-develop.md#device-application-development)

In deze Quick Start leert u een eenvoudige IoT Device Application Development-werk stroom. Eerst gebruikt u Azure IoT Central om een Cloud toepassing te maken. Vervolgens gebruikt u de Azure IoT python-SDK om een gesimuleerd apparaat te maken, verbinding te maken met IoT Central en apparaat-naar-Cloud-telemetrie te verzenden. 

## <a name="prerequisites"></a>Vereisten
- [Python 3.7+](https://www.python.org/downloads/). Zie [Functies van Azure IoT-apparaten](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features) voor andere versies van Python die worden ondersteund.
    
    Voer uit om ervoor te zorgen dat uw python-versie up-to-date is `python --version` . Als u zowel python 2 als python 3 hebt geïnstalleerd en een python 3-omgeving gebruikt, installeert u alle bibliotheken met `pip3` . Met wordt `pip3` gegarandeerd dat de bibliotheken zijn geïnstalleerd in uw python 3-runtime.
    > [!IMPORTANT]
    > Selecteer in het installatie programma python de optie om **python toe te voegen aan het pad**. Als u python 3,7 of hoger al hebt geïnstalleerd, controleert u of u de installatie-map python hebt toegevoegd aan de `PATH` omgevings variabele.

## <a name="create-an-application"></a>Een app maken
In deze sectie maakt u een IoT Central-toepassing. IoT Central is een op een portal gebaseerd IoT-toepassings platform dat helpt de complexiteit en kosten van het ontwikkelen en beheren van IoT-oplossingen te verminderen.

Een Azure IoT Central-toepassing maken:
1. Blader naar [Azure IOT Central](https://apps.azureiotcentral.com/) en meld u aan met een persoonlijk, werk-of school account van micro soft.
1. Navigeer naar **Build** en selecteer **aangepaste apps**.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="Start pagina IoT Central":::
1. Voer in **toepassings naam** een unieke naam in of gebruik de gegenereerde naam.
1. Voer in **URL** een voor voegsel voor het onthouden van de toepassings-URL in of gebruik het GEGENEREERDe URL-voor voegsel.
1. Houd de **toepassings sjabloon** ingesteld op de *aangepaste toepassing*. In de vervolg keuzelijst kunnen andere opties worden weer gegeven, als er al sjablonen in uw account bestaan.
1. Selecteer een **prijs plan** optie. 
    - Als u de toepassing zeven dagen gratis wilt gebruiken, selecteert u **gratis**. U kunt een gratis toepassing converteren naar de standaard prijs voordat deze verloopt.
    - Optioneel kunt u een Standard-prijs plan selecteren. Als u standaard prijzen selecteert, worden er meer opties weer gegeven. u moet een **Directory**, een **Azure-abonnement** en een **locatie** instellen. Zie [prijzen van Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/)voor meer informatie over prijzen. 
        - De **directory** is de Azure Active Directory waarin uw toepassing wordt gemaakt. Een Azure Active Directory bevat gebruikers-id's, referenties en andere organisatiegegevens. Als u nog geen Azure Active Directory hebt, wordt er een gemaakt wanneer u een Azure-abonnement maakt.
        - Met een **Azure-abonnement** kunt u exemplaren van Azure-Services maken. IoT Central zorgt ervoor dat resources in uw abonnement worden ingericht. Als u geen Azure-abonnement hebt, kunt u [er gratis een maken](https://azure.microsoft.com/free/). Nadat u het abonnement hebt gemaakt, keert u terug naar de pagina **nieuwe toepassing** IOT Central. Uw nieuwe abonnement wordt weer gegeven in de vervolg keuzelijst van het **Azure-abonnement** .
        - **Locatie** is de [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waarin u een toepassing maakt. Selecteer een locatie die zich het dichtst in de buurt van uw apparaten bevindt om optimaal te pres teren. Nadat u een locatie hebt gekozen, kunt u de toepassing niet naar een andere locatie verplaatsen.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Dialoog venster IoT Central nieuwe toepassing":::
1. Selecteer **Maken**.
    
    Nadat IoT Central de toepassing hebt gemaakt, wordt u omgeleid naar het dash board van de toepassing.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="Nieuw toepassings dashboard IoT Central":::

## <a name="add-a-device"></a>Een apparaat toevoegen
In deze sectie voegt u een nieuw apparaat toe aan uw IoT Central-toepassing. Het apparaat is een exemplaar van een apparaatprofiel dat een echt of gesimuleerd apparaat vertegenwoordigt dat u met de toepassing gaat verbinden. 

Een nieuw apparaat maken:
1. Selecteer **apparaten** in het linkerdeel venster en selecteer **+ Nieuw**. Hiermee opent u het dialoog venster nieuw apparaat.
1. Zorg ervoor dat de **device Temp late** is ingesteld op niet- *toegewezen*.

    > [!NOTE]
    > In deze Quick Start hebt u een gesimuleerd apparaat verbonden dat gebruikmaakt van een niet-toegewezen sjabloon. Als u IoT Central blijven gebruiken voor het beheren van apparaten, leert u hoe u Device-sjablonen gebruikt. Zie [Quick Start: een gesimuleerd apparaat toevoegen aan uw IOT Central-toepassing](../iot-central/core/quick-create-simulated-device.md)voor een overzicht van het werken met sjablonen voor apparaten.
1. Stel een beschrijvende **apparaatnaam** en **apparaat-id** in. Gebruik eventueel de gegenereerde waarden.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Dialoog venster IoT Central nieuw apparaat":::
1. Selecteer **Maken**.

    Het gemaakte apparaat wordt weer gegeven in de lijst **alle apparaten** .
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Lijst met alle apparaten IoT Central":::
    
Verbindings Details ophalen voor het nieuwe apparaat:
1. Dubbel klik in de lijst **alle apparaten** op de naam van het gekoppelde apparaat om de details weer te geven. 
1. Selecteer **Verbinding maken** in het bovenste menu.

    In het dialoog venster **verbinding met apparaat** worden de verbindings details weer gegeven:  :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="verbindings Details van IOT Central apparaat":::
1. Kopieer de volgende waarden van het dialoog venster **verbinding met apparaat** naar een veilige locatie. U gebruikt deze in de volgende sectie om uw apparaat te verbinden met IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Berichten verzenden en telemetrie controleren
In deze sectie gaat u de python-SDK gebruiken om een gesimuleerd apparaat te bouwen en telemetrie naar uw IoT Central-toepassing te verzenden. 

1. Open een Terminal met Windows CMD of Power shell of bash (voor Windows of Linux). U gebruikt de terminal om de python-SDK te installeren, omgevings variabelen bij te werken en het python-code voorbeeld uit te voeren.

1. Kopieer de voor [beelden van Azure IOT PYTHON SDK-apparaten](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) naar uw lokale machine.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Navigeer naar de map *Azure-IOT-SDK-python/Azure-IOT-device/samples* .

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Installeer de Azure IoT python SDK.

    ```console
    pip install azure-iot-device
    ```

1. Stel de volgende omgevings variabelen in om het gesimuleerde apparaat in te scha kelen om verbinding te maken met IoT Central. Voor `ID_SCOPE` , `DEVICE_ID` , en `DEVICE_KEY` gebruikt u de waarden die u hebt opgeslagen in het dialoog venster IOT Central *apparaat verbinding* .

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Voor Windows-CMD zijn er geen aanhalings tekens rond de connection string of andere variabele waarden.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux of Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. Voer in de Terminal de code uit voor het voorbeeld bestand * simple_send_temperature. py. Met deze code wordt het gesimuleerde IoT-apparaat geopend en wordt een bericht verzonden naar IoT Central.

    Het python-voor beeld uitvoeren vanuit de terminal:
    ```console
    python ./simple_send_temperature.py
    ```

    U kunt eventueel de python-code uitvoeren vanuit het voor beeld in uw python IDE:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Als de python-code een bericht van uw apparaat naar uw IoT Central-toepassing verzendt, worden de berichten weer gegeven op het tabblad **onbewerkte gegevens** van uw apparaat in IOT Central. Mogelijk moet u de pagina vernieuwen om recente berichten weer te geven.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Scherm opname van IoT Central onbewerkte gegevens uitvoer":::

Uw apparaat is nu veilig verbonden met en verzenden van telemetrie naar Azure IoT.

## <a name="clean-up-resources"></a>Resources opschonen
Als u de IoT Central resources die in deze zelf studie zijn gemaakt niet meer nodig hebt, kunt u deze uit de IoT Central Portal verwijderen. Als u van plan bent om verder te gaan met de documentatie in deze hand leiding, kunt u de toepassing die u hebt gemaakt, behouden en opnieuw gebruiken voor andere voor beelden.

De voorbeeld toepassing van Azure IoT Central en alle bijbehorende apparaten en resources verwijderen:
1. Selecteer **beheer**  >  **uw toepassing**.
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een eenvoudige Azure IoT-toepassings werk stroom geleerd voor het veilig verbinden van een apparaat met de Cloud en het verzenden van apparaat-naar-Cloud-telemetrie. U hebt de Azure IoT Central gebruikt om een toepassing en een apparaat te maken. vervolgens hebt u de Azure IoT python SDK gebruikt om een gesimuleerd apparaat te maken en telemetrie te verzenden. U hebt ook IoT Central gebruikt voor het bewaken van de telemetrie.

Als volgende stap moet u de Azure IoT python SDK verkennen via toepassings voorbeelden.

- [Asynchrone voor beelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): deze map bevat asynchrone python-voor beelden voor aanvullende IOT hub scenario's.
- [Synchrone voor beelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): deze map bevat python-voor beelden voor gebruik met python 2,7-of synchrone compatibiliteits Scenario's voor python 3.6 +
- [IOT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)-voor beelden: deze map bevat python-voor beelden voor het werken met Edge-modules en downstream-apparaten.
