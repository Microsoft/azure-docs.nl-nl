---
title: Bestanden van apparaten uploaden naar Azure IoT Hub met python | Microsoft Docs
description: Het uploaden van bestanden van een apparaat naar de Cloud met behulp van Azure IoT Device SDK voor python. Geüploade bestanden worden opgeslagen in een Azure Storage-BLOB-container.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 77d51b2c839a64567838fa4d6308d203a6bb8b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501138"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In dit artikel wordt beschreven hoe u de functies voor het [uploaden van bestanden van IOT hub](iot-hub-devguide-file-upload.md) gebruikt om een bestand te uploaden naar [Azure Blob Storage](../storage/index.yml). In deze zelfstudie leert u het volgende:

* Een opslag container veilig bieden voor het uploaden van een bestand.

* Gebruik de python-client om een bestand te uploaden via uw IoT-hub.

Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-python.md) Quick Start toont de basis functionaliteit voor het uitwisselen van apparaten van IOT hub. In sommige gevallen kunt u de gegevens die uw apparaten verzenden echter niet eenvoudig toewijzen aan de relatief kleine apparaat-naar-Cloud-berichten die IoT Hub accepteren. Wanneer u bestanden van een apparaat nodig hebt, kunt u nog steeds gebruikmaken van de beveiliging en betrouw baarheid van IoT Hub.

Aan het einde van deze zelf studie voert u de python-console-app uit:

* **FileUpload.py**, dat een bestand uploadt naar Storage met behulp van de python-apparaat-SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie maakt u de app apparaat om een bestand te uploaden naar IoT hub.

1. Voer bij de opdracht prompt de volgende opdracht uit om het **Azure-IOT-Device-** pakket te installeren. U gebruikt dit pakket voor het coördineren van het uploaden van bestanden met uw IoT-hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Voer bij de opdracht prompt de volgende opdracht uit om het pakket [**Azure. storage. blob**](https://pypi.org/project/azure-storage-blob/) te installeren. U gebruikt dit pakket om het bestand te uploaden.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Maak een test bestand dat u wilt uploaden naar de Blob-opslag.

1. Maak met behulp van een tekst editor een **FileUpload.py** -bestand in de werkmap.

1. Voeg de volgende `import` instructies en variabelen toe aan het begin van het **FileUpload.py** -bestand.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. Vervang in het bestand door `[Device Connection String]` de Connection String van uw IOT hub-apparaat. Vervang door `[Full path to local file]` het pad naar het test bestand dat u hebt gemaakt of een bestand op het apparaat dat u wilt uploaden.

1. Maak een functie om het bestand naar de Blob-opslag te uploaden:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Met deze functie wordt de door gegeven *blob_info* -structuur geparseerd voor het maken van een URL die wordt gebruikt voor het initialiseren van een [Azure. storage. blob. BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient). Vervolgens wordt het bestand geüpload naar Azure Blob-opslag met behulp van deze client.

1. Voeg de volgende code toe om verbinding te maken met de client en het bestand te uploaden:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Met deze code wordt een asynchroon **IoTHubDeviceClient** gemaakt en worden de volgende api's gebruikt voor het beheren van het bestand uploaden met uw IOT-hub:

    * **get_storage_info_for_blob** haalt informatie op uit uw IOT-hub over het gekoppelde opslag account dat u eerder hebt gemaakt. Deze informatie omvat de hostnaam, container naam, naam van de BLOB en een SAS-token. De opslag gegevens worden door gegeven aan de functie **store_blob** (gemaakt in de vorige stap), zodat de **BlobClient** in die functie kan verifiëren met Azure Storage. De methode **get_storage_info_for_blob** retourneert ook een correlation_id, dat wordt gebruikt in de methode **notify_blob_upload_status** . De correlation_id is IoT Hub manier om te markeren met welke BLOB u werkt.

    * **notify_blob_upload_status** meldt IOT hub van de status van de Blob Storage-bewerking. U geeft deze door aan de correlation_id die is verkregen door de methode **get_storage_info_for_blob** . Het wordt gebruikt door IoT Hub om een service te melden die mogelijk luistert naar een melding over de status van de upload taak voor het bestand.

1. Sla het **UploadFile.py** -bestand op en sluit het.

## <a name="run-the-application"></a>De toepassing uitvoeren

U bent nu klaar om de toepassing uit te voeren.

1. Voer bij een opdracht prompt in de werkmap de volgende opdracht uit:

    ```cmd/sh
    python FileUpload.py
    ```

2. Op de volgende scherm afbeelding ziet u de uitvoer van de app **fileupload** :

    ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. U kunt de portal gebruiken om het geüploade bestand weer te geven in de opslag container die u hebt geconfigureerd:

    ![Bestand geüpload](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de functies voor het uploaden van bestanden van IoT Hub kunt gebruiken om Bestands uploads van apparaten te vereenvoudigen. U kunt IoT hub-functies en-scenario's blijven verkennen met de volgende artikelen:

* [Een IoT-hub maken via een programma](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)

Meer informatie over Azure Blob Storage met de volgende koppelingen:

* [Documentatie voor Azure Blob Storage](../storage/blobs/index.yml)

* [Azure Blob Storage voor python API-documentatie](/python/api/overview/azure/storage-blob-readme)