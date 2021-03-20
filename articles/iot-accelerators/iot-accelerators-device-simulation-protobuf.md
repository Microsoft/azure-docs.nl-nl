---
title: Protocol buffers gebruiken met Device simulatie-Azure | Microsoft Docs
description: In deze hand leiding vindt u informatie over het gebruik van protocol buffers voor het serialiseren van telemetrie die wordt verzonden vanuit de Device simulatie Solution Accelerator.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852146"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Telemetrie serialiseren met protocol buffers

Protocol buffers (protobuf) is een binaire serialisatie-indeling voor gestructureerde gegevens. Protobuf is ontworpen om de eenvoud en prestaties te benadrukken met een doel van kleiner en sneller dan XML.

Device simulatie ondersteunt de **proto3** -versie van de taal protocol buffers.

Omdat protobuf gecompileerde code vereist om de gegevens te serialiseren, moet u een aangepaste versie van de apparaat-simulatie maken.

Met de stappen in deze hand leiding leert u het volgende:

1. Een ontwikkel omgeving voorbereiden
1. Opgeven met behulp van de protobuf-indeling in een model van een apparaat
1. Uw protobuf-indeling definiëren
1. Protobuf-klassen genereren
1. Lokaal testen

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt volgen, hebt u het volgende nodig:

* Visual Studio Code. U kunt [Visual Studio code voor Mac, Linux en Windows](https://code.visualstudio.com/download)downloaden.
* .NET core. U kunt [.net core voor Mac, Linux en Windows](https://www.microsoft.com/net/download)downloaden.
* Postman. U kunt [postman downloaden voor Mac, Windows of Linux](https://www.getpostman.com/apps).
* Een [IOT-hub die is geïmplementeerd in uw Azure-abonnement](../iot-hub/iot-hub-create-through-portal.md). U hebt de connection string van de IoT-hub nodig om de stappen in deze hand leiding uit te voeren. U kunt de connection string ophalen via de Azure Portal.
* Een [Cosmos DB-Data Base die is geïmplementeerd in uw Azure-abonnement](../cosmos-db/create-sql-api-dotnet.md#create-account) dat gebruikmaakt van de SQL-API en die is geconfigureerd voor [sterke consistentie](../cosmos-db/how-to-manage-database-account.md). U hebt de connection string van de Cosmos DB-data base nodig om de stappen in deze hand leiding uit te voeren. U kunt de connection string ophalen via de Azure Portal.
* Een [Azure-opslag account dat is geïmplementeerd in uw Azure-abonnement](../storage/common/storage-account-create.md). U hebt de connection string van het opslag account nodig om de stappen in deze hand leiding uit te voeren. U kunt de connection string ophalen via de Azure Portal.

## <a name="prepare-your-development-environment"></a>Uw ontwikkelomgeving voorbereiden

Voer de volgende taken uit om uw ontwikkel omgeving voor te bereiden:

* Down load de bron voor de micro service Device simulatie.
* Down load de bron voor de micro service Storage Adapter.
* Voer de micro service Storage Adapter lokaal uit.

In de instructies in dit artikel wordt ervan uitgegaan dat u Windows gebruikt. Als u een ander besturings systeem gebruikt, moet u mogelijk enkele bestands paden en opdrachten aanpassen aan uw omgeving.

### <a name="download-the-microservices"></a>De micro services downloaden

Down load en pak de [externe bewakings micro Services](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) van github naar een geschikte locatie op de lokale computer. Deze opslag plaats bevat de micro service Storage adapter die u nodig hebt voor deze procedure.

Down load en pak de [device simulatie micro service](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) vanuit github naar een geschikte locatie op de lokale computer.

### <a name="run-the-storage-adapter-microservice"></a>De micro service Storage Adapter uitvoeren

Open de map **Remote-Monitoring-Services-DotNet-master\storage-adapter** in Visual Studio code. Klik op alle **herstel** knoppen om niet-opgeloste afhankelijkheden op te lossen.

Open de **vscode/launch.jsin** het bestand en wijs uw Cosmos DB connection string toe aan de omgevings variabele **PCs \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

> [!NOTE]
> Wanneer u de micro service lokaal op uw computer uitvoert, moet er nog een Cosmos DB-exemplaar in azure goed werken.

Als u de micro service voor de opslag adapter lokaal wilt uitvoeren, klikt u op **fout \> opsporing starten**.

Het **Terminal** venster in Visual Studio code toont uitvoer van de actieve micro service, inclusief een URL voor de status controle van de webservice: <http://127.0.0.1:9022/v1/status> . Wanneer u naar dit adres navigeert, moet de status ' OK: Alive en well ' zijn.

Verlaat de micro service voor de opslag adapter die wordt uitgevoerd in dit exemplaar van Visual Studio code wanneer u de volgende stappen uitvoert.

## <a name="define-your-device-model"></a>Het model van uw apparaat definiëren

Open de map **device-simulatie-DotNet-Master** die u hebt gedownload van github in een nieuw exemplaar van Visual Studio code. Klik op alle **herstel** knoppen om eventuele onopgeloste afhankelijkheden op te lossen.

In deze hand leiding maakt u een nieuw apparaat model voor een Asset Tracker:

1. Maak een nieuw apparaat model bestand met de naam **assettracker-01.js** in de map **Services\data\devicemodels** .

1. Definieer de functionaliteit van het apparaat in het **assettracker-01.jsvan** het apparaat in het bestand. Het gedeelte telemetrie van een protobuf-apparaat moet:

   * Neem de naam op van de protobuf-klasse die u voor uw apparaat genereert. In de volgende sectie ziet u hoe u deze klasse kunt genereren.
   * Geef protobuf op als de bericht indeling.

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

### <a name="create-device-behaviors-script"></a>Script voor het gedrag van apparaten maken

Schrijf het gedrags script dat definieert hoe uw apparaat werkt. Zie [een geavanceerd gesimuleerd apparaat maken](iot-accelerators-device-simulation-advanced-device.md)voor meer informatie.

## <a name="define-your-protobuf-format"></a>Uw protobuf-indeling definiëren

Wanneer u een model voor apparaten hebt en uw bericht indeling hebt bepaald, kunt u een **proto** -bestand maken. In het **proto** -bestand voegt u het volgende toe:

* Een `csharp_namespace` die overeenkomt met de eigenschap **className** in het model van uw apparaat.
* Een bericht voor elke gegevens structuur die moet worden geserialiseerd.
* Een naam en type voor elk veld in het bericht.

1. Maak een nieuw bestand met de naam **assettracker. proto** in de map **Services\Models\Protobuf\proto** .

1. Definieer de syntaxis, de naam ruimte en het bericht schema in het **proto** -bestand als volgt:

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

De `=1` `=2` markeringen voor elk element geven een unieke code op die door het veld wordt gebruikt in de binaire code ring. Voor getallen 1-15 is één byte vereist die moet worden gecodeerd dan een hoger nummer.

## <a name="generate-the-protobuf-class"></a>De protobuf-klasse genereren

Wanneer u een **proto** -bestand hebt, is de volgende stap het genereren van de klassen die nodig zijn om berichten te lezen en te schrijven. U hebt de **protoc** protobuf-compiler nodig om deze stap te volt ooien.

1. [De protobuf-compiler downloaden van GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Voer de compiler uit, waarbij u de bronmap, de doelmap en de naam van het **proto** -bestand opgeeft. Bijvoorbeeld:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Met deze opdracht wordt een bestand **Assettracker. cs** in de map **Services\Models\Protobuf** gegenereerd.

## <a name="test-protobuf-locally"></a>Protobuf lokaal testen

In deze sectie gaat u het Asset tracker-apparaat dat u in de vorige gedeelten hebt gemaakt, lokaal testen.

### <a name="run-the-device-simulation-microservice"></a>De Device simulatie micro service uitvoeren

Open de **vscode/launch.jsin** het bestand en wijs het volgende toe:

* IoT Hub connection string aan de omgevings variabele **PCs \_ IOTHUB \_ CONNSTRING** .
* Het opslag account connection string de omgevings variabele **pc's \_ Azure \_ Storage- \_ account** .
* Cosmos DB connection string aan de omgevings variabele **PCs \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

Open het bestand **webservice/eigenschappen/launchSettings.js** en wijs het volgende toe:

* IoT Hub connection string aan de omgevings variabele **PCs \_ IOTHUB \_ CONNSTRING** .
* Het opslag account connection string de omgevings variabele **pc's \_ Azure \_ Storage- \_ account** .
* Cosmos DB connection string aan de omgevings variabele **PCs \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

Open het **WebService\appsettings.ini** -bestand en wijzig de instellingen als volgt:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>De oplossing configureren voor het toevoegen van uw nieuwe model bestanden voor apparaten

Standaard worden uw nieuwe JSON-en JS-bestanden van het apparaat model niet gekopieerd naar de ingebouwde oplossing. U moet deze expliciet toevoegen.

Voeg een vermelding toe aan het **services\services.csproj** -bestand voor elk bestand dat u wilt opnemen. Bijvoorbeeld:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Als u de micro service lokaal wilt uitvoeren, klikt u op **fout \> opsporing starten**.

In het **Terminal** venster in Visual Studio code wordt uitvoer van de actieve micro service weer gegeven.

Zorg ervoor dat de micro service Device simulatie niet wordt uitgevoerd in dit exemplaar van Visual Studio code terwijl u de volgende stappen uitvoert.

### <a name="set-up-a-monitor-for-device-events"></a>Een monitor instellen voor faxgebeurtenissen

In deze sectie gebruikt u de Azure CLI om een gebeurtenis controle in te stellen voor het weer geven van de telemetrie die is verzonden vanaf de apparaten die zijn verbonden met uw IoT-hub.

In het volgende script wordt ervan uitgegaan dat de naam van uw IoT hub **apparaat-simulatie-test** is.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Zorg ervoor dat de controle van gebeurtenissen actief blijft terwijl u de gesimuleerde apparaten test.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Een simulatie maken met het apparaattype voor Asset tracker

In deze sectie gebruikt u het hulp programma postman om de Device simulatie micro service aan te vragen om een simulatie uit te voeren met behulp van het apparaat type Asset tracker. Postman is een hulp programma waarmee u REST-aanvragen kunt verzenden naar een webservice.

Postman instellen:

1. Open postman op uw lokale machine.

1. Klik op **bestand \> importeren**. Klik vervolgens op **bestanden kiezen**.

1. Selecteer de **Azure IOT-oplossings versneller voor apparaat simulatie. postman \_ verzameling** en **Azure IOT- \_ oplossings versneller.** 

1. Vouw de **Azure IOT Device simulatie Solution Accelerator** uit om de aanvragen weer te geven die u kunt verzenden.

1. Klik op **geen omgeving** en selecteer **Azure IOT Device simulatie Solution Accelerator**.

U hebt nu een verzameling en omgeving geladen in uw postman-werk ruimte die u kunt gebruiken om te communiceren met de micro service Device simulatie.

De simulatie configureren en uitvoeren:

1. Selecteer in de Postman-verzameling de optie **simulatie van Asset tracker maken** en klik op **verzenden**. Met deze aanvraag worden vier exemplaren van het apparaattype van het gesimuleerde Asset tracering gemaakt.

1. De gebeurtenis monitor uitvoer in het Azure CLI-venster toont de telemetrie van de gesimuleerde apparaten.

Als u de simulatie wilt stoppen, selecteert u de aanvraag voor het stoppen van de **simulatie** in postman en klikt u op **verzenden**.

### <a name="clean-up-resources"></a>Resources opschonen

U kunt de twee lokaal uitgevoerde micro Services in hun Visual Studio code-instanties stoppen (fouten opsporen **\> Stop fout opsporing**).

Als u de IoT Hub en Cosmos DB instanties niet meer nodig hebt, verwijdert u deze uit uw Azure-abonnement om overbodige kosten te voor komen.

## <a name="iot-hub-support"></a>Ondersteuning voor IoT Hub

Veel IoT Hub-functies bieden geen systeem eigen ondersteuning voor protobuf of andere binaire indelingen. U kunt bijvoorbeeld niet routeren op basis van de nettolading van berichten omdat IoT Hub de bericht lading niet kan verwerken. U kunt echter route ring gebaseerd op bericht koppen.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaat simulatie kunt aanpassen om protobuf te gebruiken om telemetrie te verzenden, gaat u de volgende stap naar de GitHub-opslag plaats voor meer informatie over het [simuleren van apparaten](https://github.com/Azure/device-simulation-dotnet).
