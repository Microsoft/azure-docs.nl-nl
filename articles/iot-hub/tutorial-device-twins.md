---
title: Apparaatstatus synchroniseren vanuit Azure IoT Hub | Microsoft Docs
description: Ontdek hoe u apparaatdubbels kunt gebruiken om uw apparaten te configureren vanuit de cloud en om status- en nalevingsgegevens van uw apparaten te ontvangen.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 9ec2c51f01d6b13f33bc2d537a8f73a6721967d4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572521"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Zelfstudie: Uw apparaten configureren vanaf een back-endservice

Naast het ontvangen van telemetriegegevens van uw apparaten, moet u mogelijk vanaf een back-endservice uw apparaten configureren. Wanneer u een gewenste configuratie naar uw apparaten verzendt, wilt u mogelijk ook status- en nalevingsupdates van die apparaten ontvangen. U stelt bijvoorbeeld een doelbereik in voor de operationele temperatuur voor een apparaat of verzamelt firmwareversiegegevens van uw apparaten.

Om de statusgegevens tussen een apparaat en een IoT hub te synchroniseren, gebruikt u a _pparaatdubbels_. Een [apparaatdubbel](iot-hub-devguide-device-twins.md) is een JSON-document dat is gekoppeld aan een specifiek apparaat en door IoT Hub wordt opgeslagen in de cloud waar u ze kunt [doorzoeken](iot-hub-devguide-query-language.md). Een apparaatdubbel bevat _gewenste eigenschappen_, _gerapporteerde eigenschappen_ en _labels_. Een gewenste eigenschap wordt ingesteld door een back-endtoepassing en gelezen door een apparaat. Een gerapporteerde eigenschap wordt ingesteld door een apparaat en gelezen door een back-endtoepassing. Een label wordt ingesteld door een back-endtoepassing en nooit naar een apparaat verzonden. U gebruikt labels om uw apparaten te organiseren. In deze zelfstudie ziet u hoe u gewenste en gerapporteerde eigenschappen kunt gebruiken om statusgegevens te synchroniseren:

![Overzicht dubbels](media/tutorial-device-twins/DeviceTwins.png)

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een IoT hub maken en een testapparaat aan het identiteitsregister toevoegen.
> * Gewenste eigenschappen gebruiken om statusgegevens naar uw gesimuleerd apparaat te zenden.
> * Gerapporteerde eigenschappen gebruiken om statusgegevens van uw gesimuleerd apparaat te ontvangen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

De twee voorbeeldtoepassingen die u uitvoert in deze snelstartgids zijn geschreven in Node.js. Node.js v10.x.x of hoger moet zijn geïnstalleerd op uw ontwikkelcomputer.

U kunt Node.js voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

Download het Node.js-voorbeeldproject van https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip en pak het ZIP-archief uit.

Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in deze zelfstudie wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="set-up-azure-resources"></a>Azure-resources instellen

Om deze zelfstudie te voltooien moet uw Azure-abonnement een IoT hub bevatten met een apparaat toegevoegd aan het apparaatidentiteitsregister. Met de vermelding in het apparaatidentiteitsregister kan het gesimuleerd apparaat dat u in deze zelfstudie uitvoert, verbinding maken met uw hub.

Als u nog geen IoT-hub hebt ingesteld in uw abonnement, kunt u er een instellen met het volgende CLI-script. Dit script gebruikt de naam **zelfstudie-iot-hub** voor de IoT hub, u moet deze naam vervangen door een eigen unieke naam wanneer u dit uitvoert. Het script maakt de brongroep en hub in de regio **VS - centraal** die u kunt wijzigen in een regio dichterbij. Met het script wordt uw verbindingsreeks voor de IoT hub, die u gebruikt in het back-endvoorbeeld om verbinding te maken met uw IoT hub:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-iot

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --name $hubname --policy-name service -o table

```

Deze zelfstudie gebruikt een gesimuleerd apparaat genaamd **MyTwinDevice**. Het volgende script voegt dit apparaat toe aan uw identiteitsregister en haalt de verbindingsreeks op:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Statusinformatie verzenden

U gebruikt gewenste eigenschappen om statusgegevens vanuit een back-endsysteem naar een apparaat te zenden. In deze sectie kunt u zien hoe u:

* Gewenste eigenschappen op een apparaat ontvangt en verwerkt.
* Gewenste eigenschappen vanuit een back-endtoepassing zend.

Om de voorbeeldcode van het gesimuleerd apparaat te zien die gewenste eigenschappen ophaalt, navigeert u naar de map **iot-hub/Tutorials/DeviceTwins** in het voorbeeldproject in Node.js dat uw hebt gedownload. Open vervolgens het bestand SimulatedDevice.js in een teksteditor.

In de volgende secties wordt de code beschreven die wordt uitgevoerd op het gesimuleerde apparaat dat reageert op wijzigingen in de gewenste eigenschappen die met de back-endtoepassing worden verzonden:

### <a name="retrieve-the-device-twin-object"></a>Haal het object apparaatdubbel op

De volgende code verbindt met uw IoT hub met een apparaatverbindingsreeks:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

De volgende code haalt een dubbel op van het clientobject:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Voorbeeld gewenste eigenschappen

U kunt uw gewenste eigenschappen organiseren op elke manier die handig is voor uw toepassing. Dit voorbeeld gebruikt een eigenschap van het hoogste niveau genaamd **fanOn** en groepeert de overige eigenschappen in aparte **componenten**. Het volgende JSON-fragment toont de structuur van de gewenste eigenschappen die in deze zelfstudie worden gebruikt:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Handlers maken

U kunt handlers maken voor updates voor gewenste eigenschappen die reageren op updates op verschillende niveaus in de JSON-hiërarchie. Deze handler zorgt er bijvoorbeeld voor dat alle wijzigingen voor gewenste eigenschappen naar het apparaat worden verzonden vanuit een back-endtoepassing. De **delta**-variabele bevat de gewenste eigenschappen die zijn verzonden vanaf de back-endoplossing:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

De volgende handler reageert alleen op wijzigingen die worden aangebracht aan de gewenste eigenschap **fanOn**:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Handlers voor meerdere eigenschappen

In het eerder getoonde voorbeeld van gewenste JSON-eigenschappen bevat de node **klimaat** onder **componenten** twee eigenschappen, **minTemperature** en **maxTemperature**.

Een lokaal **dubbel**-object van een apparaat slaat een volledige reeks van gewenste en gerapporteerde eigenschappen op. De **delta** gezonden door het back-end updatet mogelijk maar een subset van gewenste eigenschappen. In het volgende codefragment, als het gesimuleerd apparaat een update ontvangt voor maar een van de **minTemperature** en **maxTemperature**, gebruikt het de waarde in de lokale dubbel voor de andere waarde om het apparaat te configureren:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

Het lokale **dubbel**-object van een apparaat slaat een volledige reeks van gewenste en gerapporteerde eigenschappen op. De **delta** gezonden door het back-end updatet mogelijk maar een subset van gewenste eigenschappen.

### <a name="handle-insert-update-and-delete-operations"></a>Invoeg-, update- en verwijderbewerkingen verwerken

De gewenste eigenschappen die vanaf het back-end zijn verzonden geven niet aan welke bewerking wordt uitgevoerd op een bepaalde gewenste eigenschap. Uw code moet de bewerking afleiden uit de huidige set van gewenste eigenschappen die lokaal zijn opgeslagen en de wijzigingen die vanuit de hub zijn verzonden.

Het volgende fragment toont aan hoe het gesimuleerd apparaat invoeg-, update- en verwijderbewerkingen in de lijst van **componenten** in de gewenste eigenschappen verwerkt. U kunt zien hoe u **null**-waarden gebruikt om aan te geven dat een component moet worden verwijderd:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Gewenste eigenschappen naar een apparaat verzenden vanuit het back-end

U hebt gezien hoe een apparaat handlers implementeert voor het ontvangen van updates van gewenste eigenschappen. Deze sectie laat zien hoe u wijzigingen van gewenste eigenschappen naar een apparaat zend vanuit een back-endtoepassing.

Om de voorbeeldcode van het gesimuleerd apparaat te zien die gewenste eigenschappen ophaalt, navigeert u naar de map **iot-hub/Tutorials/DeviceTwins** in het voorbeeldproject in Node.js dat uw hebt gedownload. Open vervolgens het bestand ServiceClient.js in een teksteditor.

Het volgende codefragment toont hoe u verbinding maakt met een apparaatidentiteitsregister en de dubbel opent voor een specifiek apparaat:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

Het volgende fragment toont verschillende *patches* voor gewenste eigenschappen die de back-endtoepassing naar het apparaat zend:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

Het volgende fragment toont hoe de back-endtoepassing een update van een gewenste eigenschap naar een apparaat verzend:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>De toepassingen uitvoeren

In deze sectie voert u twee voorbeeldtoepassingen uit om te zien dat een back-endtoepassing updates voor gewenste eigenschappen naar een gesimuleerd apparaattoepassing verzendt.

Om de gesimuleerd apparaat- en back-endtoepassingen uit te voeren, hebt u de verbindingsreeksen van het apparaat en de service nodig. U hebt de verbindingsreeksen genoteerd toen u aan het begin van deze zelfstudie de bronnen hebt gemaakt.

Om de gesimuleerd apparaattoepassing uit te voeren, opent u een shell of opdrachtpromptvenster en navigeert u naar de map **iot-hub/Tutorials/DeviceTwins** in het Node.js-project dat u hebt gedownload. Voer vervolgens de volgende opdrachten uit:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Om de back-endtoepassing uit te voeren, opent u een ander(e) shell of opdrachtpromptvenster. Navigeer dan naar de map **iot-hub/Tutorials/DeviceTwins** in het Node.js-project dat u hebt gedownload. Voer vervolgens de volgende opdrachten uit:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

De volgende schermafbeelding toont de uitvoer van de gesimuleerd apparaattoepassing en accentueert hoe het een update voor de gewenste eigenschap **maxTemperature** verwerkt. U kunt zien hoe zowel de handler van het hoogste niveau als de klimaatcomponenthandlers worden uitgevoerd:

![Schermopname die laat zien hoe zowel de handler van het hoogste niveau als de klimaatcomponenthandlers worden uitgevoerd.](./media/tutorial-device-twins/SimulatedDevice1.png)

De volgende schermafbeelding laat de uitvoer van de back-endtoepassing zien en accentueert hoe die een update zendt naar de gewenste eigenschap **maxTemperature**:

![Schermopname die de uitvoer van de back-endtoepassing laat zien, met het verzenden van een update gemarkeerd.](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Statusinformatie ontvangen

Uw back-endtoepassing ontvangt statusgegevens van een apparaat als gerapporteerde eigenschappen. Een apparaat stelt de gerapporteerde eigenschappen in en zendt die terug naar uw hub. Een back-endtoepassing kan de huidige waarden van de gerapporteerde eigenschappen lezen van de apparaatdubbel die in uw hub is opgeslagen.

### <a name="send-reported-properties-from-a-device"></a>Gerapporteerde eigenschappen vanaf een apparaat verzenden

U kunt updates voor gerapporteerde eigenschappen verzenden als patch. Het volgende fragment toont een sjabloon voor de patch die het gesimuleerd apparaat verzendt. Het gesimuleerd apparaat updatet de velden in de patch voordat ze naar de hub worden verzonden:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

Het gesimuleerd apparaat gebruikt de volgende functie om de patch die de gerapporteerde eigenschappen bevat naar de hub te zenden:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Gerapporteerde eigenschap verwerken

Een back-endtoepassing opent de huidige waarden van gerapporteerde eigenschappen van een apparaat via de apparaatdubbel. Het volgende fragment toont hoe de back-endtoepassing de waarden leest van de gerapporteerde eigenschappen voor het gesimuleerd apparaat:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>De toepassingen uitvoeren

In deze sectie voert u twee voorbeeldtoepassingen uit om te zien dat een gesimuleerd apparaattoepassing updates voor gerapporteerde eigenschappen naar een back-endtoepassing verzendt.

U voert dezelfde twee voorbeeldtoepassingen uit die u uitvoerde om te zien hoe gewenste eigenschappen naar een apparaat worden verzonden.

Om de gesimuleerd apparaat- en back-endtoepassingen uit te voeren, hebt u de verbindingsreeksen van het apparaat en de service nodig. U hebt de verbindingsreeksen genoteerd toen u aan het begin van deze zelfstudie de bronnen hebt gemaakt.

Om de gesimuleerd apparaattoepassing uit te voeren, opent u een shell of opdrachtpromptvenster en navigeert u naar de map **iot-hub/Tutorials/DeviceTwins** in het Node.js-project dat u hebt gedownload. Voer vervolgens de volgende opdrachten uit:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Om de back-endtoepassing uit te voeren, opent u een ander(e) shell of opdrachtpromptvenster. Navigeer dan naar de map **iot-hub/Tutorials/DeviceTwins** in het Node.js-project dat u hebt gedownload. Voer vervolgens de volgende opdrachten uit:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

De volgende schermafbeelding toont de uitvoer van de gesimuleerd apparaattoepassing en accentueert hoe het een update voor de gerapporteerde eigenschap naar uw hub verzendt:

![Gesimuleerd apparaat](./media/tutorial-device-twins/SimulatedDevice2.png)

De volgende schermafbeelding toont de uitvoer van de back-endtoepassing en laat zien hoe deze een update voor de gerapporteerde eigenschap van een apparaat ontvangt en verwerkt:

![Back-endtoepassing](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om de volgende zelfstudie uit te voeren, moet u de resourcegroep en IoT-hub verlaten en ze later opnieuw gebruiken.

Als u de IoT-hub niet langer nodig hebt, verwijdert u deze en de resourcegroep in de portal. Dit doet u door de resourcegroep **zelfstudie-iot-hub-rg** met de IoT-hub te selecteren en op **Verwijderen** te klikken.

Of gebruik de CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u statusgegevens synchroniseert tussen uw apparaten en uw IoT hub. Ga naar de volgende zelfstudie om te leren hoe u apparaatdubbels gebruikt om een updateproces voor firmware te implementeren.

> [!div class="nextstepaction"]
> [Een apparaatfirmware-updateproces implementeren](tutorial-firmware-update.md)
