---
title: 'Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI'
description: In deze quickstart wordt beschreven hoe u de Azure CLI gebruikt om een Azure Stream Analytics-taak te maken.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: a3cc4c3d6936a51ca2010209ce23e4d82c9333eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016335"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI

In deze quickstart gebruikt u de Azure CLI om een Stream Analytics-taak te definiëren waarmee realtime-sensorberichten met een temperatuur van meer dan 27 graden worden gefilterd. Met de Stream Analytics-taak worden gegevens van IoT Hub gelezen, en worden de gegevens getransformeerd en teruggeschreven naar een container in een blob-opslag. De invoergegevens in deze quickstart worden gegenereerd via een Raspberry Pi Online Simulator.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Maak een resourcegroep. Alle Azure-resources moeten worden geïmplementeerd in een resourcegroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

   Maak voor deze quickstart een resourcegroep met de naam *streamanalyticsrg* op de locatie *eastus* met behulp van de volgende [az group create](/cli/azure/group#az-group-create)-opdracht:

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>De invoergegevens voorbereiden

Voordat u de Stream Analytics-taak definieert, moet u de gegevens voorbereiden die worden gebruikt voor de invoer van de taak.

Het volgende codeblok van de Azure CLI bestaat uit opdrachten waarmee de invoergegevens die nodig zijn voor de taak worden voorbereid. Bekijk de secties om de code te begrijpen.

1. Maak een IoT Hub met de opdracht [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub). In dit voorbeeld wordt een IoT Hub met de naam **MyASAIoTHub** gemaakt. Omdat namen van IoT Hubs uniek moeten zijn, moet u zelf een naam bedenken voor uw IoT Hub. Stel de SKU in op F1 om de gratis laag te gebruiken als deze beschikbaar is met uw abonnement. Als dat niet het geval is, kiest u de eerstvolgende lagere categorie.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Zodra de IoT Hub is gemaakt, vraagt u de verbindingsreeks voor IoT Hub op met de opdracht [az iot hub show-connection-string](/cli/azure/iot/hub). Kopieer de volledige verbindingsreeks en bewaar deze totdat u de IoT Hub als invoer gaat toevoegen aan uw Stream Analytics-taak.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Voeg een apparaat toe aan IoT Hub met behulp van de opdracht [az iothub-device identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). In dit voorbeeld maakt u een apparaat met de naam **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Vraag de verbindingsreeks van het apparaat op met de opdracht [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string). Kopieer de volledige verbindingsreeks en bewaar deze totdat u de Raspberry Pi-simulator gaat maken.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Voorbeeld van uitvoer:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Een blob-opslagaccount maken

Met de volgende Azure CLI-codeblokken wordt een blob-opslagaccount gemaakt dat wordt gebruikt voor taakuitvoer. Bekijk de secties om de code te begrijpen.

1. Maak een algemeen opslagaccount met de opdracht [az storage account create](/cli/azure/storage/account). Het algemeen opslagaccount kan voor alle vier de services worden gebruikt: blobs, bestanden, tabellen en wachtrijen.

   Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Haal de sleutel voor uw opslagaccount op door de opdracht [az storage account keys list](/cli/azure/storage/account/keys) uit te voeren. Sla deze sleutel op voor gebruik in de volgende stap.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Gebruik de opdracht [az storage container create](/cli/azure/storage/container) om een container te maken voor het opslaan van blobs. U gebruikt de sleutel voor het opslagaccount om de bewerking voor het maken van de container te autoriseren. Zie [Toegang verlenen tot blob- of wachtrijgegevens met Azure CLI](../storage/blobs/authorize-data-operations-cli.md) voor meer informatie over het autoriseren van gegevensbewerkingen met Azure CLI.

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

Met de volgende Azure CLI-codeblokken wordt een Stream Analytics-taak gemaakt. Bekijk de secties om de code te begrijpen

1. Maak een Stream Analytics-taak met de opdracht [az stream-analytics job create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create).

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Invoer voor de taak configureren

Voeg invoer aan uw taak toe met behulp van de cmdlet [az stream-analytics input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create). Deze cmdlet gebruikt de taaknaam, taakinvoernaam, resourcegroepnaam en de taakinvoerdefinitie als parameters. De taakinvoerdefinitie is een JSON-bestand dat de eigenschappen bevat die nodig zijn om de invoer van de taak te configureren. In dit voorbeeld maakt u een IoT Hub als invoer.

Maak op uw lokale machine een bestand met de naam `datasource.json` en voeg er de volgende JSON-gegevens aan toe. Vervang de waarde voor `sharedAccessPolicyKey` door het gedeelte `SharedAccessKey` uit de IoT Hub-verbindingsreeks die u in een eerdere sectie hebt opgeslagen.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Maak op uw lokale machine een bestand met de naam `serialization.json` en voeg er de volgende JSON-gegevens aan toe.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Voer vervolgens de cmdlet `az stream-analytics input create` uit. Zorg ervoor dat u de waarde van de `datasource`-variabele vervangt door het pad waar u het JSON-bestand met de taakinvoerdefinitie hebt opgeslagen en de waarde van de `serialization`-variabele door het pad waar u het JSON-bestand voor serialisatie hebt opgeslagen.

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Uitvoer voor de taak configureren

Voeg uitvoer aan uw taak toe met behulp van de cmdlet [az stream-analytics output create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create). Deze cmdlet gebruikt de taaknaam, taakuitvoernaam, resourcegroepnaam en de taakuitvoerdefinitie als parameters. De taakuitvoerdefinitie is een JSON-bestand dat de eigenschappen bevat die nodig zijn om de uitvoer van de taak te configureren. In dit voorbeeld wordt blobopslag als uitvoer gebruikt.

Maak op uw lokale machine een bestand met de naam `datasink.json` en voeg er de volgende JSON-gegevens aan toe. Let erop dat u de waarde voor `accountKey` vervangt door de toegangssleutel van uw opslagaccount. Deze is opgeslagen in de waarde $storageAccountKey.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Voer vervolgens de cmdlet `az stream-analytics output` uit. Zorg ervoor dat u de waarde van de `datasource`-variabele vervangt door het pad waar u het JSON-bestand met de taakuitvoerdefinitie hebt opgeslagen en de waarde van de `serialization`-variabele door het pad waar u het JSON-bestand voor serialisatie hebt opgeslagen.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>De transformatiequery definiëren

Voeg een transformatie aan uw taak toe met behulp van de cmdlet [az stream-analytics transformation create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create). Deze cmdlet gebruikt de taaknaam, taaktransformatienaam, resourcegroepnaam en de taaktransformatiedefinitie als parameters. 

Voer de cmdlet `az stream-analytics transformation create` uit.

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>De IoT-simulator uitvoeren

1. Open de [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Vervang de tijdelijke aanduiding op regel 15 door de verbindingsreeks van het Azure IoT Hub-apparaat die u hebt opgeslagen in een eerdere sectie.

3. Klik op **Run**. De uitvoer geeft de sensorgegevens en berichten weer die worden verzonden naar de IoT-hub.

    ![Raspberry Pi Azure IoT Online Simulator](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>De Stream Analytics-taak starten en uitvoer controleren

Start de taak met behulp van de cmdlet [az stream-analytics job start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start). Deze cmdlet gebruikt de taaknaam, resourcegroepnaam, uitvoerstartmodus en begintijd als parameters. `OutputStartMode` accepteert waarden van `JobStartTime`, `CustomTime` of `LastOutputEventTime`.

Nadat u de volgende cmdlet hebt uitgevoerd, retourneert deze `True` als uitvoer als de taak wordt gestart. In de opslagcontainer wordt een uitvoermap met de getransformeerde gegevens gemaakt.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u de verwijdering ervan overslaan en de taak nu stoppen. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze quickstart zijn gemaakt door de volgende cmdlet uit te voeren:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige Stream Analytics-taak met behulp van de Azure CLI geïmplementeerd. U kunt Stream Analytics-taken ook implementeren met behulp van de [Azure-portal](stream-analytics-quick-create-portal.md) en [Visual Studio](stream-analytics-quick-create-vs.md).

Voor informatie over het configureren van andere invoerbronnen en het uitvoeren van detectie in realtime gaat u door naar het volgende artikel:

> [!div class="nextstepaction"]
> [Fraudedetectie in realtime met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)