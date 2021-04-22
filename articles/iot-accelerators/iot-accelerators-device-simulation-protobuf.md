---
title: Protocolbuffers gebruiken met apparaatsimulatie - Azure| Microsoft Docs
description: In deze handleiding leert u hoe u protocolbuffers gebruikt om telemetrie te serialiseren die is verzonden vanuit de oplossingsversneller apparaatsimulatie.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 2d2c33d0b6f86bc1a779361b86d242cde4c5df38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873663"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Telemetrie serialiseren met protocolbuffers

Protocolbuffers (Protobuf) is een binaire serialisatie-indeling voor gestructureerde gegevens. Protobuf is ontworpen om eenvoud en prestaties te benadrukken met als doel kleiner en sneller te zijn dan XML.

Apparaatsimulatie ondersteunt de **proto3-versie** van de taal voor protocolbuffers.

Omdat Protobuf gecompileerde code vereist om de gegevens te serialiseren, moet u een aangepaste versie van Apparaatsimulatie bouwen.

De stappen in deze handleiding laten zien hoe u het volgende kunt doen:

1. Een ontwikkelomgeving voorbereiden
1. Opgeven met behulp van de Protobuf-indeling in een apparaatmodel
1. Uw Protobuf-indeling definiëren
1. Protobuf-klassen genereren
1. Lokaal testen

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt volgen, hebt u het volgende nodig:

* Visual Studio Code. U kunt Visual Studio [code voor Mac, Linux en Windows downloaden.](https://code.visualstudio.com/download)
* .NET Core. U kunt [.NET Core downloaden voor Mac, Linux en Windows.](https://dotnet.microsoft.com/download)
* Postman. U kunt [Postman downloaden voor Mac, Windows of Linux.](https://www.getpostman.com/apps)
* Een [IoT-hub die is geïmplementeerd in uw Azure-abonnement](../iot-hub/iot-hub-create-through-portal.md). U hebt de connection string IoT-hub nodig om de stappen in deze handleiding uit te voeren. U kunt de connection string op de Azure Portal.
* Een [Cosmos DB die is geïmplementeerd in uw Azure-abonnement](../cosmos-db/create-sql-api-dotnet.md#create-account) en die gebruikmaakt van de SQL API en die is geconfigureerd voor sterke [consistentie](../cosmos-db/how-to-manage-database-account.md). U hebt de Cosmos DB database nodig om connection string stappen in deze handleiding uit te voeren. U kunt de connection string op de Azure Portal.
* Een [Azure-opslagaccount dat is geïmplementeerd in uw Azure-abonnement](../storage/common/storage-account-create.md). U hebt de connection string van het opslagaccount nodig om de stappen in deze handleiding uit te voeren. U kunt de connection string op de Azure Portal.

## <a name="prepare-your-development-environment"></a>Uw ontwikkelomgeving voorbereiden

Voltooi de volgende taken om uw ontwikkelomgeving voor te bereiden:

* Download de bron voor de microservice voor apparaatsimulatie.
* Download de bron voor de microservice van de opslagadapter.
* Voer de microservice van de opslagadapter lokaal uit.

In de instructies in dit artikel wordt ervan uitgenomen dat u Windows gebruikt. Als u een ander besturingssysteem gebruikt, moet u mogelijk enkele bestandspaden en opdrachten aanpassen aan uw omgeving.

### <a name="download-the-microservices"></a>De microservices downloaden

Download de [Microservices](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) voor externe bewaking van GitHub en download deze uit een geschikte locatie op uw lokale computer. Deze opslagplaats bevat de opslagadaptermicroservice die u nodig hebt voor deze gebruiksdeseed.

Download de microservice voor [apparaatsimulatie](https://github.com/Azure/azure-iot-pcs-device-simulation/archive/master.zip) van GitHub en los deze uit naar een geschikte locatie op uw lokale computer.

### <a name="run-the-storage-adapter-microservice"></a>De microservice voor de opslagadapter uitvoeren

Open Visual Studio code **remote-monitoring-services-dotnet-master\storage-adapter.** Klik op de **knoppen Herstellen** om niet-opgeloste afhankelijkheden op te lossen.

Open het **bestand .vscode/launch.js** bestand en wijs uw Cosmos DB connection string toe aan de **PCS \_ STORAGEADAPTER \_ DOCUMENTDB CONNSTRING-omgevingsvariabele. \_**

> [!NOTE]
> Wanneer u de microservice lokaal op uw computer hebt uitgevoerd, is er nog steeds een Cosmos DB in Azure nodig om goed te werken.

Als u de microservice van de opslagadapter lokaal wilt uitvoeren, klikt u **op \> Foutopsporing Starten met foutopsporing.**

Het **venster Terminal** in Visual Studio Code toont uitvoer van de microservice die wordt uitgevoerd, inclusief een URL voor de statuscontrole van de webservice: <http://127.0.0.1:9022/v1/status> . Wanneer u naar dit adres navigeert, moet de status 'OK: Springlevend en goed' zijn.

Laat de microservice van de opslagadapter actief in dit Visual Studio Code terwijl u de volgende stappen voltooit.

## <a name="define-your-device-model"></a>Uw apparaatmodel definiëren

Open de **map device-simulation-dotnet-master** die u hebt gedownload van GitHub in een nieuw exemplaar van Visual Studio Code. Klik op de **knoppen Herstellen** om eventuele niet-opgeloste afhankelijkheden op te lossen.

In deze handleiding maakt u een nieuw apparaatmodel voor een assettracker:

1. Maak een nieuw apparaatmodelbestand met **assettracker-01.jsin** de map **Services\data\devicemodels.**

1. Definieer de apparaatfunctionaliteit in het **apparaatmodelassettracker-01.jsin het** bestand. De telemetriesectie van een Protobuf-apparaatmodel moet:

   * Neem de naam op van de Protobuf-klasse die u voor uw apparaat genereert. In de volgende sectie ziet u hoe u deze klasse genereert.
   * Geef Protobuf op als berichtindeling.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Script voor apparaatgedrag maken

Schrijf het gedragsscript dat definieert hoe het apparaat zich gedraagt. Zie Een geavanceerd gesimuleerd apparaat maken [voor meer informatie.](iot-accelerators-device-simulation-advanced-device.md)

## <a name="define-your-protobuf-format"></a>Uw Protobuf-indeling definiëren

Wanneer u een apparaatmodel hebt en uw berichtindeling hebt bepaald, kunt u een **protobestand** maken. In het **proto-bestand** voegt u het volgende toe:

* Een `csharp_namespace` die overeenkomt met de eigenschap **ClassName** in uw apparaatmodel.
* Een bericht voor elke gegevensstructuur die moet worden geser serialiseren.
* Een naam en type voor elk veld in het bericht.

1. Maak een nieuw bestand met de **naam assettracker.proto** in de map **Services\Models\Protobuf\proto.**

1. Definieer de syntaxis, naamruimte en berichtschema als volgt in het **protobestand:**

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

De `=1` `=2` markeringen , voor elk element geven een unieke tag op die het veld gebruikt in de binaire codering. Voor getallen van 1-15 is één byte minder nodig om te coderen dan hogere getallen.

## <a name="generate-the-protobuf-class"></a>De Protobuf-klasse genereren

Wanneer u een **protobestand** hebt, is de volgende stap het genereren van de klassen die nodig zijn om berichten te lezen en te schrijven. U hebt de **protoc** Protobuf-compiler nodig om deze stap te voltooien.

1. [Download de Protobuf-compiler van GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Voer de compiler uit en geef de bronmap, de doelmap en de naam van het **protobestand** op. Bijvoorbeeld:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Met deze opdracht wordt een **bestand Assettracker.cs** gegenereerd in de **map Services\Models\Protobuf.**

## <a name="test-protobuf-locally"></a>Protobuf lokaal testen

In deze sectie test u het assettrackerapparaat dat u in de vorige secties lokaal hebt gemaakt.

### <a name="run-the-device-simulation-microservice"></a>De microservice voor apparaatsimulatie uitvoeren

Open het **bestand .vscode/launch.jsbestand** en wijs het volgende toe:

* IoT Hub connection string naar de **\_ omgevingsvariabele PCS IOTHUB \_ CONNSTRING.**
* Opslagaccounts connection string de **omgevingsvariabele \_ AZURE \_ STORAGE ACCOUNT \_ van PCS.**
* Cosmos DB connection string aan de **omgevingsvariabele \_ PCS STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING.**

Open het **bestand WebService/Properties/launchSettings.jsbestand** en wijs het volgende toe:

* IoT Hub connection string aan de **omgevingsvariabele \_ PCS IOTHUB \_ CONNSTRING.**
* Opslagaccounts connection string de **omgevingsvariabele \_ AZURE \_ STORAGE ACCOUNT \_ van PCS.**
* Cosmos DB connection string aan de **omgevingsvariabele \_ PCS STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING.**

Open het **WebService\appsettings.ini** en wijzig de instellingen als volgt:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>De oplossing configureren om uw nieuwe apparaatmodelbestanden op te nemen

Standaard worden de JSON- en JS-bestanden van uw nieuwe apparaatmodel niet gekopieerd naar de ingebouwde oplossing. U moet deze expliciet opnemen.

Voeg een vermelding toe aan het **bestand services\services.csproj** voor elk bestand dat u wilt toevoegen. Bijvoorbeeld:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Als u de microservice lokaal wilt uitvoeren, klikt u **op \> Foutopsporing Starten met foutopsporing.**

In **het venster Terminal** in Visual Studio Code wordt uitvoer weergegeven van de microservice die wordt uitgevoerd.

Laat de microservice voor apparaatsimulatie actief in dit exemplaar van Visual Studio Code terwijl u de volgende stappen voltooit.

### <a name="set-up-a-monitor-for-device-events"></a>Een monitor instellen voor apparaatgebeurtenissen

In deze sectie gebruikt u de Azure CLI om een gebeurtenismonitor in te stellen om de telemetrie weer te laten zien die wordt verzonden vanaf de apparaten die zijn verbonden met uw IoT-hub.

In het volgende script wordt ervan uitgenomen dat de naam van uw IoT-hub **apparaatsimulatie-test is.**

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Laat de gebeurtenismonitor actief terwijl u de gesimuleerde apparaten test.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Een simulatie maken met het assettrackerapparaattype

In deze sectie gebruikt u het Postman-hulpprogramma om de microservice voor apparaatsimulatie aan te vragen om een simulatie uit te voeren met behulp van het assettrackerapparaattype. Postman is een hulpprogramma waarmee u REST-aanvragen naar een webservice kunt verzenden.

Postman instellen:

1. Open Postman op uw lokale computer.

1. Klik **op Bestand \> importeren.** Klik vervolgens **op Bestanden kiezen.**

1. Selecteer **azure IoT Device Simulation solution accelerator.postman \_ collection** en **Azure IoT Device Simulation solution accelerator.postman \_ environment** en klik op **Open.**

1. Vouw de **oplossingsversneller voor Azure IoT-apparaatsimulatie uit om** de aanvragen weer te geven die u kunt verzenden.

1. Klik **op Geen omgeving** en selecteer **Oplossingsversneller voor Azure IoT-apparaatsimulatie.**

U hebt nu een verzameling en omgeving geladen in uw Postman-werkruimte die u kunt gebruiken om te communiceren met de microservice voor apparaatsimulatie.

De simulatie configureren en uitvoeren:

1. Selecteer in de Postman-verzameling **De assettrackersimulatie maken** en klik op **Verzenden.** Met deze aanvraag worden vier exemplaren van het gesimuleerde assettrackerapparaattype gemaakt.

1. De uitvoer van de gebeurtenismonitor in het Azure CLI-venster toont de telemetrie van de gesimuleerde apparaten.

Als u de simulatie wilt stoppen, selecteert u **de aanvraag Simulatie** stoppen in Postman en klikt u op **Verzenden.**

### <a name="clean-up-resources"></a>Resources opschonen

U kunt de twee lokaal uitvoerende microservices stoppen in hun Visual Studio Code-exemplaren (**Foutopsporing \> stoppen met foutopsporing).**

Als u de instanties IoT Hub en Cosmos DB niet meer nodig hebt, verwijdert u deze uit uw Azure-abonnement om onnodige kosten te voorkomen.

## <a name="iot-hub-support"></a>IoT Hub-ondersteuning

Veel IoT Hub bieden geen systeemeigen ondersteuning voor Protobuf of andere binaire indelingen. U kunt bijvoorbeeld niet routen op basis van de nettolading van het bericht omdat IoT Hub de nettolading van het bericht niet kan verwerken. U kunt echter wel een route maken op basis van berichtheaders.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Apparaatsimulatie kunt aanpassen om Protobuf te gebruiken om telemetrie te verzenden, gaat u in de volgende stap naar de GitHub-opslagplaats voor meer informatie over [Apparaatsimulatie.](https://github.com/Azure/azure-iot-pcs-device-simulation)
