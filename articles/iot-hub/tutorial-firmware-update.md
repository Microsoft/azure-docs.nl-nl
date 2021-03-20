---
title: Zelf studie-firmware van het apparaat bijwerken via Azure IoT Hub | Microsoft Docs
description: 'Zelf studie: informatie over het implementeren van een firmware-update proces voor een apparaat dat kan worden geactiveerd vanuit een back-endtoepassing die is verbonden met uw IoT-hub.'
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/28/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 3fc257ff192ccb1bb05b233c6ac802696ece0054
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575718"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Zelfstudie: Een apparaatfirmware-updateproces implementeren

Mogelijk moet u de firmware bijwerken op de apparaten die zijn aangesloten op uw IoT-hub. Bijvoorbeeld als u nieuwe functies aan de firmware wilt toevoegen of beveiligingspatches toepassen. In veel IoT-scenario's is het onpraktisch om apparaten fysiek te bezoeken en handmatig firmware-updates toe te passen. Deze zelfstudie laat zien hoe u het firmware-updateproces op afstand kunt starten en bewaken via een back-endtoepassing die is aangesloten op uw hub.

Om het firmware-updateproces te maken en te controleren, maakt de back-endapplicatie in deze zelfstudie een _configuratie_ in uw IoT-hub. IoT Hub [automatisch apparaatbeheer](./iot-hub-automatic-device-management.md) gebruikt deze configuratie om een ​​set _gewenste apparaatdubbeleigenschappen_ bij te werken op al uw koelinstallaties. De gewenste eigenschappen specificeren de details van de vereiste firmware-update. Terwijl de koelinstallaties het firmware-updateproces uitvoeren, melden ze hun status aan de back-endtoepassing met behulp van _gerapporteerde apparaatdubbeleigenschappen_. De back-endtoepassing kan de configuratie gebruiken om de gerapporteerde eigenschappen te controleren die vanaf het apparaat zijn verzonden en om het firmware-updateproces tot de voltooiing te volgen:

![Firmware-updateproces](media/tutorial-firmware-update/Process.png)

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een IoT hub maken en een testapparaat aan het apparaatidentiteitsregister toevoegen.
> * Een configuratie maken om de firmware-update te definiëren.
> * Het firmware-updateproces op een apparaat simuleren.
> * Statusupdates ontvangen van het apparaat tijdens de voortgang van de firmware-update.

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

Om deze zelfstudie te voltooien moet uw Azure-abonnement een IoT hub hebben met een apparaat toegevoegd aan het apparaatidentiteitsregister. Met de vermelding in het apparaatidentiteitsregister kan het gesimuleerd apparaat dat u in deze zelfstudie uitvoert, verbinding maken met uw hub.

Als u nog geen IoT hub hebt ingesteld in uw abonnement, kunt u er een instellen met het volgende CLI-script. Dit script gebruikt de naam **zelfstudie-iot-hub** voor de IoT hub, u moet deze naam vervangen door een eigen unieke naam wanneer u dit uitvoert. Het script maakt de brongroep en hub in de regio **VS - centraal** die u kunt wijzigen in een regio dichterbij. Met het script wordt uw verbindingsreeks voor de IoT hub, die u gebruikt in de voorbeeldback-endtoepassing om verbinding te maken met uw IoT hub:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create a free-tier IoT Hub. You can have only one free IoT Hub per subscription. Free tier hubs can have only 2 partitions.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later
az iot hub connection-string show --name $hubname --policy-name service -o table

```

Deze zelfstudie gebruikt een gesimuleerd apparaat genaamd **MyFirmwareUpdateDevice**. Het volgende script voegt dit apparaat toe aan uw apparaatidentiteitsregister, stelt een tagwaarde in en haalt de verbindingsreeks op:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"device type":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity connection-string show --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Als u deze opdrachten uitvoert bij een Windows-opdrachtprompt of Powershell-prompt, raadpleegt u de pagina [azure-iot-cli-extensietips](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips) voor informatie over het citeren van JSON-strings.

## <a name="start-the-firmware-update"></a>De firmware-update starten

U maakt een [automatische configuratie voor Apparaatbeheer](iot-hub-automatic-device-management.md#create-a-configuration) in de back-end-toepassing om het firmware-update proces te starten op alle apparaten met het **Apparaattype** Chiller. In deze sectie kunt u zien hoe u:

* Een configuratie maakt vanuit een back-endtoepassing.
* De taak bewaakt tot deze is uitgevoerd.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Gebruik gewenste eigenschappen om de firmware-upgrade te starten vanuit de back-endtoepassing

Als u de back-endtoepassingscode wilt bekijken waarmee de configuratie wordt gemaakt, gaat u naar de map **iot-hub/Tutorials/FirmwareUpdate** in het voorbeeld-Node.js-project dat u hebt gedownload. Open vervolgens het bestand ServiceClient.js in een teksteditor.

De back-endtoepassing maakt de volgende configuratie:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

De configuratie bevat de volgende secties:

* `content` geeft de gewenste eigenschappen van de firmware op die naar de geselecteerde apparaten worden verzonden.
* `metrics` geeft de uit te voeren query's op voor het rapporteren van de status van de firmware-update.
* `targetCondition` selecteert de apparaten die de firmware-update moeten ontvangen.
* `priorty` stelt de relatieve prioriteit van deze configuratie in ten opzichte van andere configuraties.

De back-endapplicatie gebruikt de volgende code om de configuratie voor het instellen van de gewenste eigenschappen te maken:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Nadat de configuratie is gemaakt, controleert de toepassing de firmware-update:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Een configuratie rapporteert twee soorten metrische gegevens:

* Systeemgegevens die rapporteren hoeveel apparaten zijn getarget en op hoeveel apparaten de update is toegepast.
* Aangepaste metrische gegevens die worden gegenereerd door de query's die u toevoegt aan de configuratie. In deze zelfstudie rapporteren de query's hoeveel apparaten zich in elke fase van het updateproces bevinden.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Reageren op de firmware-update-aanvraag op het apparaat

Als u de gesimuleerde-apparaatcode wilt zien waarmee de gewenste firmware-eigenschappen die vanuit de back-endtoepassing worden verzonden worden afgehandeld, gaat u naar de map **iot-hub/Tutorials/FirmwareUpdate** in het voorbeeld-Node.js-project dat u hebt gedownload. Open vervolgens het bestand SimulatedDevice.js in een teksteditor.

De gesimuleerde apparaattoepassing maakt een handler voor updates van de gewenste eigenschappen van **properties.desired.firmware** in de apparaatdubbel. In de handler worden enkele basiscontroles uitgevoerd op de gewenste eigenschappen voordat het updateproces wordt gestart:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>De firmware bijwerken

De update wordt uitgevoerd met de functie **initiateFirmwareUpdateFlow**. Deze functie gebruikt de functie **waterfall** om de fasen van het updateproces op volgorde uit te voeren. In dit voorbeeld heeft de firmware-update vier fasen. In de eerste fase wordt de installatiekopie gedownload, in de tweede fase wordt de installatiekopie gecontroleerd met behulp van een controlesom, in de derde fase wordt de installatiekopie toegepast en de laatste fase wordt het apparaat opnieuw gestart:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Tijdens het updateproces rapporteert het gesimuleerde apparaat over de voortgang met behulp van gerapporteerde eigenschappen:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

Het volgende codefragment toont de implementatie van de downloadfase. Tijdens de downloadfase gebruikt het gesimuleerde apparaat gerapporteerde eigenschappen om statusinformatie naar de back-endtoepassing te verzenden:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

In deze sectie voert u twee voorbeeldtoepassingen uit om te observeren hoe back-endtoepassing de configuratie maakt om het firmware-updateproces op het gesimuleerde apparaat te beheren.

Om de gesimuleerd apparaat- en back-endtoepassingen uit te voeren, hebt u de verbindingsreeksen van het apparaat en de service nodig. U hebt de verbindingsreeksen genoteerd toen u aan het begin van deze zelfstudie de bronnen hebt gemaakt.

Om de gesimuleerd apparaattoepassing uit te voeren, opent u een shell of opdrachtpromptvenster en navigeert u naar de map **iot-hub/Tutorials/FirmwareUpdate** in het Node.js-project dat u hebt gedownload. Voer vervolgens de volgende opdrachten uit:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Om de back-endtoepassing uit te voeren, opent u een ander(e) shell of opdrachtpromptvenster. Navigeer dan naar de map **iot-hub/Tutorials/FirmwareUpdate** in het Node.js-project dat u hebt gedownload. Voer vervolgens de volgende opdrachten uit:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

De volgende schermafbeelding toont de uitvoer van de gesimuleerde apparaattoepassing en laat zien hoe deze reageert op de update van de gewenste firmware-eigenschappen van de back-endtoepassing:

![Gesimuleerd apparaat](./media/tutorial-firmware-update/SimulatedDevice.png)

De volgende schermafbeelding toont de uitvoer van de back-endtoepassing en benadrukt hoe de configuratie voor het bijwerken van de gewenste eigenschappen van de firmware wordt gemaakt:

![Schermopname van de uitvoer van de back-endtoepassing.](./media/tutorial-firmware-update/BackEnd1.png)

De volgende schermafbeelding toont de uitvoer van de back-endtoepassing en benadrukt hoe deze de metrische gegevens van de firmware-update van het gesimuleerde apparaat bewaakt:

![Back-endtoepassing](./media/tutorial-firmware-update/BackEnd2.png)

Omdat automatische apparaatconfiguraties worden uitgevoerd tijdens de aanmaak en vervolgens elke vijf minuten, ziet u mogelijk niet elke statusupdate die naar de back-endtoepassing wordt verzonden. U kunt de metrische gegevens ook bekijken in de portal in de sectie **Apparaatbeheer -> IoT-apparaatconfiguratie** van uw IoT-hub:

![Configuratie weergeven in portal](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om de volgende zelfstudie uit te voeren, moet u de resourcegroep en IoT-hub verlaten en ze later opnieuw gebruiken.

Als u de IoT-hub niet langer nodig hebt, verwijdert u deze en de resourcegroep in de portal. Dit doet u door de resourcegroep **zelfstudie-iot-hub-rg** met de IoT-hub te selecteren en op **Verwijderen** te klikken.

Of gebruik de CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een firmware-updateproces voor uw verbonden apparaten kunt implementeren. Ga naar de volgende zelfstudie om te leren hoe u Azure IoT Hub-portalhulpmiddelen en Azure CLI-opdrachten kunt gebruiken om de apparaatconnectiviteit te testen.

> [!div class="nextstepaction"]
> [Een gesimuleerd apparaat gebruiken om connectiviteit met uw IoT-hub te testen](tutorial-connectivity.md)
