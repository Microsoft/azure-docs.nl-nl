---
title: 'Zelfstudie: Apparaatgegevens verzenden via transparante gateway - Machine Learning in Azure IoT Edge'
description: Deze zelfstudie laat zien hoe u uw ontwikkelcomputer als een gesimuleerd IoT Edge-apparaat kunt gebruiken om gegevens te verzenden naar de IoT Hub via een apparaat dat is geconfigureerd als een transparante gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: f0695af6922182aa8be7acfb4b0a931bed35ef7d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959302"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Zelfstudie: Gegevens verzenden via een transparante gateway

In dit artikel gebruiken we nogmaals de ontwikkel-VM als een gesimuleerd apparaat. In plaats van gegevens rechtstreeks naar de IoT Hub te verzenden, verzendt het apparaat gegevens naar het IoT Edge-apparaat dat is geconfigureerd als een transparante gateway.

We controleren de werking van het IoT Edge apparaat terwijl het gesimuleerde apparaat gegevens verzendt. Zodra de uitvoering van het apparaat is voltooid, bekijken we de gegevens in het opslagaccount om te valideren of alles naar verwachting heeft gewerkt.

Deze stap wordt doorgaans uitgevoerd door een cloud- of apparaatontwikkelaar.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel terecht bent gekomen, gaat u naar de [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="review-device-harness"></a>Apparaatharnas beoordelen

Hergebruik het [DeviceHarness-project](tutorial-machine-learning-edge-03-generate-data.md) om het downstream (of leaf-) apparaat te simuleren. Voor het maken van verbinding met de transparante gateway moet u nog twee dingen doen:

* Registreer het certificaat om een vertrouwensrelatie te verzorgen tussen het downstream IoT-apparaat en de certificeringsinstantie die wordt gebruikt door de IoT Edge-runtime. In ons geval is het downstream-apparaat de ontwikkel-VM.
* Voeg de FQDN (Fully Qualified Domain Name) van de edge-gateway toe aan de verbindingsreeks van het apparaat.

Bekijk de code om te zien hoe deze twee items worden geïmplementeerd.

1. Open Visual Studio Code op uw ontwikkelcomputer.

1. Gebruik **Bestand** > **Map openen...** om C:\\source\\IoTEdgeAndMlSample\\DeviceHarness te openen.

1. Bekijk de methode InstallCertificate() in Program.cs.

1. Als de code het certificaatpad vindt, wordt de methode CertificateManager.InstallCACert aangeroepen om het certificaat op de computer te installeren.

1. Bekijk nu de methode GetIotHubDevice in de klasse TurbofanDevice.

1. Wanneer de gebruiker de FQDN van de gateway specificeert met de optie -g, wordt die waarde als de variabele `gatewayFqdn` aan deze methode doorgegeven, die vervolgens wordt toegevoegd aan de verbindingsreeks van het apparaat.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Het leaf-apparaat bouwen en uitvoeren

1. Bouw het project terwijl het DeviceHarness-project nog in Visual Studio Code is geopend. Selecteer in het menu **Terminal** de optie **Buildtaak uitvoeren** en selecteer **Build**.

1. Zoek de FQDN (Fully Qualified Domain Name) voor uw edge-gateway door in Azure Portal naar uw IoT Edge-apparaat (Linux-VM) te navigeren en de waarde voor **DNS-naam** vanaf de overzichtspagina te kopiëren.

1. Start uw IoT-apparaat (Linux-VM) als dit nog niet wordt uitgevoerd.

1. Open de Visual Studio Code-terminal. Selecteer in het menu **Terminal** de optie **Nieuwe terminal** en voer de volgende opdracht uit, waarbij u `<edge_device_fqdn>` vervangt door de DNS-naam die u hebt gekopieerd vanaf het IoT Edge-apparaat (Linux-VM):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. De toepassing probeert het certificaat te installeren op uw ontwikkelcomputer. Als dit het geval is, accepteert u de beveiligingswaarschuwing.

1. Wanneer u wordt gevraagd naar de IoT Hub-verbindingsreeks, klikt u op het beletselteken ( **...** ) in het apparatenpaneel van Azure IoT Hub en selecteert u **IOT Hub-verbindingsreeks kopiëren**. Plak de waarde in de terminal.

1. De uitvoer ziet er als volgt uit:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Let op de toevoeging GatewayHostName aan de apparaatverbindingsreeks, waardoor het apparaat via de IoT Hub via de transparante IoT Edge-gateway kan communiceren.

## <a name="check-output"></a>Uitvoer controleren

### <a name="iot-edge-device-output"></a>IoT Edge-apparaatuitvoer

De uitvoer van de avroFileWriter-module kan gemakkelijk worden bekeken door naar het IoT Edge-apparaat te kijken.

1. Maak via SSH verbinding met de virtuele IoT Edge-machine.

1. Zoek naar bestanden die naar schijf zijn geschreven.

   ```bash
   find /data/avrofiles -type f
   ```

1. De uitvoer van de opdracht ziet eruit als in het volgende voorbeeld:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Mogelijk hebt u meer dan één bestand, afhankelijk van de timing van de uitvoering.

1. Let op de tijdstempels. Zodra de laatste wijzigingstijd langer dan tien minuten geleden is (zie MODIFIED\_FILE\_TIMEOUT in uploader.py in de avroFileWriter-module), worden de bestanden door de avroFileWriter-module naar de cloud geüpload.

1. Als er tien minuten zijn verstreken, worden de bestanden door de module geüpload. Als het uploaden is voltooid, worden de bestanden van de schijf verwijderd.

### <a name="azure-storage"></a>Azure Storage

We kunnen de resultaten van het verzenden van gegevens door het leaf-apparaat bekijken door naar de opslagaccounts te kijken, waar de gegevens naar verwachting worden gerouteerd.

1. Open Visual Studio Code op de ontwikkelcomputer.

1. Ga in het verkennervenster van het paneel AZURE STORAGE naar de structuur om uw opslagaccount te zoeken.

1. Vouw het knooppunt **Blobcontainers** uit.

1. Uit het werk dat we in het vorige deel van de zelfstudie hebben gedaan, mogen we verwachten dat de container met **ruldata** berichten met RUL bevat. Vouw het knooppunt **ruldata** uit.

1. U ziet een of meer blobbestanden met namen als: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

1. Klik met de rechtermuisknop op een van de bestanden en kies **Blob downloaden** om het bestand op de ontwikkelcomputer op te slaan.

1. Vouw vervolgens het knooppunt **uploadturbofanfiles** uit. In het vorige artikel hebben we deze locatie ingesteld als het doel voor bestanden die door de avroFileWriter-module zijn geüpload.

1. Klik met de rechtermuisknop op de bestanden en kies **Blob downloaden** om deze op de ontwikkelcomputer op te slaan.

### <a name="read-avro-file-contents"></a>Inhoud van Avro-bestanden lezen

Er is een eenvoudig opdrachtregelhulpprogramma opgenomen voor het lezen van Avro-bestanden en het retourneren van een JSON-tekenreeks met de berichten in het bestand. In deze sectie wordt het bestand geïnstalleerd en uitgevoerd.

1. Open een terminalvenster in Visual Studio Code (**Terminal** > **Nieuwe terminal**).

1. Installeer hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Gebruik hubavroreader om het Avro-bestand te lezen dat u van **ruldata** hebt gedownload.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. U ziet dat de hoofdtekst van het bericht eruit ziet als verwacht, met apparaat-id en voorspelde RUL.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Voer dezelfde opdracht uit terwijl u het Avro-bestand doorgeeft dat u van **uploadturbofanfiles** hebt gedownload.

1. Zoals verwacht bevatten deze berichten alle sensorgegevens en operationele instellingen van het oorspronkelijke bericht. Deze gegevens kunnen worden gebruikt om het RUL-model op het edge-apparaat te verbeteren.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om de resources te bekijken die in deze end-to-endzelfstudie worden gebruikt, wacht u tot u klaar bent om de resources op te schonen die u hebt gemaakt. Gebruik anders de volgende stappen ze te verwijderen:

1. Verwijder de resourcegroep(en) die zijn gemaakt voor het bewaren van de ontwikkel-VM, IoT Edge-VM, IoT Hub, opslagaccount, Machine Learning-werkruimteservice (en gemaakte resources: containerregister, Application Insights, sleutelkluis, opslagaccount).

1. Verwijder het Machine Learning-project in [Azure Notebooks](https://notebooks.azure.com).

1. Als u de opslagplaats lokaal hebt gekloond, sluit u alle PowerShell- of VS Code-vensters die verwijzen naar de lokale opslagplaats en verwijdert u vervolgens de map voor de opslagplaats.

1. Als u certificaten lokaal hebt gemaakt, verwijdert u de map c:\\edgeCertificates.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we onze ontwikkel-VM gebruikt voor het simuleren van een leaf-apparaat dat sensor- en operationele gegevens naar ons IoT Edge-apparaat stuurt. We hebben gevalideerd dat de modules op het apparaat de gegevens hebben gerouteerd, geclassificeerd, bewaard en de geüpload door de realtime-bewerking van het edge-apparaat te controleren en door te kijken naar de bestanden die zijn geüpload naar het opslagaccount.

Meer informatie is te vinden op de volgende pagina's:

> [!div class="nextstepaction"]
> [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)

> [!div class="nextstepaction"]
> [Gegevens aan de rand opslaan met Azure Blob Storage in IoT Edge (preview)](how-to-store-data-blob.md)
