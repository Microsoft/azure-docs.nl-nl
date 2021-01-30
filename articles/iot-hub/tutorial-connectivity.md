---
title: Zelf studie-connectiviteit van apparaten controleren met Azure IoT Hub
description: Zelf studie-IoT Hub-hulpprogram ma's gebruiken om problemen op te lossen tijdens de ontwikkeling van problemen met de connectiviteit van apparaten met uw IoT-hub.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom:
- mvc
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: d9ca9dd345be7fb8f1a2b613386bdca7bdcd835a
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070375"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Zelfstudie: Een gesimuleerd apparaat gebruiken om connectiviteit met uw IoT-hub te testen

In deze zelfstudie gebruikt u Azure IoT Hub-portalhulpmiddelen en Azure CLI-opdrachten om de connectiviteit te testen. Deze zelfstudie gebruikt ook een eenvoudige apparaatsimulator die u op uw pc uitvoert.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Uw apparaatverificatie controleren
> * De apparaat-naar-cloud-verbinding controleren
> * De cloud-naar-apparaat-verbinding controleren
> * Synchronisatie van apparaatdubbel controleren

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

De apparaatsimulatietoepassing die u in deze zelfstudie uitvoert is geschreven in Node.js. Node.js v10.x.x of hoger moet zijn geïnstalleerd op uw ontwikkelcomputer.

U kunt Node.js voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

Download het Node.js-voorbeeldapparaatsimulatieproject van https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip en pak het ZIP-archief uit.

Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in deze zelfstudie wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Als u een gratis of IoT Hub met standaardlagen in een eerdere zelfstudie of snelstartgids hebt gemaakt, kunt u deze stap overslaan.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Apparaatverificatie controleren

Een apparaat moet door uw hub worden geverifieerd voordat het gegevens kan uitwisselen met de hub. U kunt het hulpprogramma **IoT-apparaten** gebruiken in de sectie **Apparaatbeheer** van de portal om uw apparaten te beheren en de verificatiesleutels die ze gebruiken te controleren. In deze sectie van de zelfstudie voegt u een nieuw testapparaat toe, haalt u de sleutel op en controleert u dat het testapparaat verbinding kan maken met de hub. Later stelt u de verificatiesleutel opnieuw in om te zien wat er gebeurt wanneer een apparaat een verouderde sleutel probeert te gebruiken. Deze sectie van de zelfstudie gebruikt de Azure Portal om een apparaat te maken, beheren en te controleren en de Node.js-apparaatsimulator.

Meld u aan bij de portal en ga naar uw IoT Hub. Ga vervolgens naar het hulpprogramma **IoT-apparaten**:

![Hulpprogramma IoT-apparaten](media/tutorial-connectivity/iot-devices-tool.png)

Om een nieuw apparaat te registreren, klikt u op **+ Toevoegen**, stelt u het **Apparaat-id** in op **MyTestDevice** en klikt u op **Opslaan**:

![Een nieuw apparaat toevoegen](media/tutorial-connectivity/add-device.png)

Klik op de verbindingsreeks voor **MyTestDevice** in de lijst van apparaten om die op te halen en kopieer vervolgens de waarde van de **Primaire sleutel van de verbindingsreeks**. De verbindingsreeks bevat de *gedeelde toegangssleutel* voor het apparaat.

![De apparaatverbindingsreeks ophalen](media/tutorial-connectivity/copy-connection-string.png)

Voer de Node.js-toepassing Gesimuleerd apparaat uit dat u eerder hebt gedownload om te simuleren dat **MyTestDevice** telemetriegegevens naar uw IoT Hub verzendt.

Navigeer in een terminalvenster op uw ontwikkelcomputer naar de hoofdmap van het voorbeeldproject in Node.js dat u hebt gedownload. Navigeer vervolgens naar de map **iot-hub\Tutorials\ConnectivityTests**.

Voer in het terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de toepassing voor het gesimuleerde apparaat uit te voeren. Gebruik de apparaatverbindingsreeks die u hebt genoteerd toen u het apparaat in de portal toevoegde.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Het terminalvenster geeft gegevens weer terwijl de verbinding naar uw hub wordt gelegd:

![Gesimuleerd apparaat verbinden](media/tutorial-connectivity/sim-1-connected.png)

U bent nu geverifieerd vanaf een apparaat dat een apparaatsleutel gebruikt die is gegenereerd door uw IoT hub.

### <a name="reset-keys"></a>Sleutels opnieuw instellen

In deze sectie stelt u de apparaatsleutel opnieuw in en bekijkt u de foutmelding wanneer het gesimuleerd apparaat verbinding probeert te maken.

Om de primaire sleutel van het apparaat opnieuw in te stellen voor **MyTestDevice** voert u de volgende opdrachten uit:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-iot

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

In het terminalvenster op uw ontwikkelcomputer, voert u de toepassing Gesimuleerd apparaat opnieuw uit:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Deze keer ziet u een verificatiefout wanneer de toepassing verbinding probeert te maken:

![Verbindingsfout](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>SAS-token genereren (Shared Access Signature)

Als uw apparaat een van de SDK’s van de IoT Hub gebruikt, genereert de SDK-bibliotheekcode het SAS-toeken dat wordt gebruikt voor verificatie met de hub. Een SAS-token wordt gegenereerd uit de naam van uw hub, de naam van uw apparaat en de apparaatsleutel.

In bepaalde scenario’s, zoals in een cloud-protocolgateway of als onderdeel van een aangepast verificatieschema, moet u mogelijk zelf het SAS-token genereren. Om problemen met uw SAS-generatiecode op te lossen, is het handig om een bekend correct SAS-token te genereren voor gebruik tijdens het testen.

> [!NOTE]
> Het voorbeeld SimulatedDevice-2.js omvat voorbeelden van het genereren van een SAS-token, zowel met als zonder de SDK.

Om een bekend correct SAS-token te genereren met de CLI, voert u de volgende opdracht uit:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Noteer de volledige tekst van het gegenereerde SAS-token. Een SAS-token ziet er als volgt uit: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

Navigeer in een terminalvenster op uw ontwikkelcomputer naar de hoofdmap van het voorbeeldproject in Node.js dat u hebt gedownload. Navigeer vervolgens naar de map **iot-hub\Tutorials\ConnectivityTests**.

Voer in het terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de toepassing voor het gesimuleerde apparaat uit te voeren:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

Het terminalvenster geeft gegevens weer terwijl de verbinding naar uw hub wordt gelegd met het SAS-token:

![Gesimuleerd apparaat verbinden met token](media/tutorial-connectivity/sim-2-connected.png)

U bent nu geverifieerd vanaf een apparaat dat een test-SAS-token gebruikt dat is gegenereerd door een CLI-opdracht. Het bestand **SimulatedDevice-2.js** bevat voorbeeldcode die laat zien hoe u een SAS-token in code genereert.

### <a name="protocols"></a>Protocollen

Een apparaat kan elk van onderstaande protocollen gebruiken om verbinding te maken met uw IoT hub:

| Protocol | Uitgaande poort |
| --- | --- |
| MQTT |8883 |
| MQTT via WebSockets |443 |
| AMQP |5671 |
| AMQP via WebSockets |443 |
| HTTPS |443 |

Als de uitgaande poort wordt geblokkeerd door een firewall kan het apparaat geen verbinding maken:

![Poort is geblokkeerd](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>De apparaat-naar-cloud-verbinding controleren

Nadat een apparaat verbinding heeft gemaakt, worden er normaal gesproken telemetriegegevens naar uw IoT hub verzonden. Deze sectie laat u zien hoe u kunt verifiëren dat de telemetriegegevens die door het apparaat worden verzonden, aankomen bij uw hub.

Haal eerst de huidige verbindingsreeks op voor uw gesimuleerd apparaat met de volgende opdracht:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Navigeer naar de map **iot-hub\Tutorials\ConnectivityTests** in de code die u hebt gedownload om een gesimuleerd apparaat uit te voeren dat berichten verzendt.

Voer in het terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de toepassing voor het gesimuleerde apparaat uit te voeren:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

Het terminalvenster geeft gegevens weer terwijl het telemetrie naar uw hub verzendt:

![Gesimuleerd apparaat dat berichten verzendt](media/tutorial-connectivity/sim-3-sending.png)

U kunt **Metrische gegevens** in de portal gebruiken om te verifiëren dat de telemetrieberichten uw IoT hub bereiken. Selecteer in de vervolgkeuzelijst **Resource** uw IoT hub, selecteer **Verzonden telemetrieberichten** als metriek en stel het tijdsbereik in op **Afgelopen uur**. De kaart toont het totaal aantal berichten dat is verzonden door het gesimuleerd apparaat:

![Metrische gegevens voor IoT Hub weergeven](media/tutorial-connectivity/metrics-portal.png)

Het duurt enkele minuten voordat de metrische gegevens beschikbaar zijn nadat u het gesimuleerd apparaat hebt gestart.

## <a name="check-cloud-to-device-connectivity"></a>De cloud-naar-apparaat-verbinding controleren

Deze sectie laat zien hoe u een testaanroep via de directe methode naar een apparaat kunt maken om de cloud-naar-apparaat-connectiviteit te controleren. U voert een gesimuleerd apparaat uit op uw ontwikkelcomputer om te luisteren naar aanroepen via de directe methode vanuit uw hub.

Gebruik in een terminalvenster de volgende opdracht om de toepassing voor het gesimuleerde apparaat uit te voeren:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Gebruik een CLI-opdracht om een directe methode aan te roepen op het apparaat:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

Het gesimuleerd apparaat drukt een bericht af op de console wanneer het een aanroep via directe methode ontvangt:

![Gesimuleerd apparaat ontvangt aanroep via directe methode](media/tutorial-connectivity/receive-method-call.png)

Wanneer een gesimuleerd apparaat een aanroep via directe methode ontvangt, zendt het een bevestiging terug naar de hub:

![Bevestiging directe methode ontvangen](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Synchronisatie van dubbel controleren

Apparaten gebruiken dubbels om de staat tussen het apparaat en de hub te synchroniseren. In deze sectie gebruikt u CLI-opdrachten om _gewenste eigenschappen_ naar een apparaat te verzenden en de _gerapporteerde eigenschappen_ die door het apparaat zijn verzonden te lezen.

Het gesimuleerd apparaat dat u in deze sectie gebruikt zend gerapporteerde eigenschappen naar de hub wanneer het opstart en drukt gewenste eigenschappen af op de console wanneer die worden ontvangen.

Gebruik in een terminalvenster de volgende opdracht om de toepassing voor het gesimuleerde apparaat uit te voeren:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Gebruik de volgende CLI-opdracht om te verifiëren dat de hub de gerapporteerde eigenschappen van het apparaat heeft ontvangen:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

In de uitvoer van de opdracht kunt u de eigenschap **devicelaststarted** zien in de sectie gerapporteerde eigenschappen. Deze eigenschap toont de datum en tijd dat u het gesimuleerd apparaat voor het laatst hebt opgestart.

![Gerapporteerde eigenschap weergeven](media/tutorial-connectivity/reported-properties.png)

Gebruik de volgende CLI-opdracht om te verifiëren dat de hub de waarden van gewenste eigenschappen naar het apparaat kan verzenden:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Het gesimuleerd apparaat drukt een bericht af wanneer het een update van een gewenste eigenschap ontvangt van de hub:

![Gewenste eigenschappen ontvangen](media/tutorial-connectivity/desired-properties.png)

Naast het ontvangen van wijzigingen van gewenste eigenschappen wanneer die worden aangebracht, controleert het gesimuleerd apparaat automatisch op gewenste eigenschappen wanneer het opstart.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de IoT-hub niet langer nodig hebt, verwijdert u deze en de resourcegroep in de portal. Dit doet u door de resourcegroep **zelfstudie-iot-hub-rg** met de IoT-hub te selecteren en op **Verwijderen** te klikken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u uw apparaatsleutels, de apparaat-naar-cloud-connectiviteit, de cloud-naar-apparaat-connectiviteit en de synchronisatie van apparaatdubbel controleert. Ga naar het Hoe-artikel voor IoT Hub-bewaking voor meer informatie over het controleren van uw IoT Hub.

> [!div class="nextstepaction"]
> [IoT Hub bewaken](monitor-iot-hub.md)
