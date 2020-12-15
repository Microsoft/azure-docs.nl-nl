---
title: 'Snelstartgids: Een Azure IoT Edge-apparaat maken in Linux | Microsoft Docs'
description: In deze quickstart leert u hoe u een IoT Edge-apparaat op Linux maakt en daarna kant-en-klare code op afstand implementeert vanuit de Azure-portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/02/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9ba73e71e4525fe56a3cbb54626030f57e990b
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920806"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Quickstart: Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat

Probeer Azure IoT Edge uit in deze quickstart door containercode te implementeren op een virtueel Linux IoT Edge-apparaat. Met IoT Edge kunt u code op uw apparaten op afstand beheren zodat u meer van uw workloads naar de rand kunt verzenden. Voor deze quickstart kunt u het beste een virtuele machine van Azure voor uw IoT Edge-apparaat gebruiken, zodat u snel een testmachine kunt maken waarop de IoT Edge-service is geïnstalleerd en deze kunt verwijderen wanneer u klaar bent.

In deze snelstart leert u de volgende zaken:

* Een IoT Hub maken.
* Een IoT Edge-apparaat registreren in uw IoT-hub.
* De IoT Edge-runtime op uw virtuele apparaat installeren en starten.
* Op afstand een module implementeren op een IoT Edge-apparaat.

![Diagram - Snelstartarchitectuur voor apparaat en cloud](./media/quickstart-linux/install-edge-full.png)

In deze quickstart leert u hoe u een virtuele Linux-machine maakt die is geconfigureerd als een IoT Edge-apparaat. Vervolgens implementeert u een module vanuit Azure Portal op uw apparaat. De module die in deze quickstart wordt gebruikt, is een gesimuleerde sensor waarmee temperatuur-, luchtvochtigheids- en drukgegevens worden gegenereerd. De andere Azure IoT Edge-zelfstudies bouwen voort op het werk dat u hier doet door aanvullende modules te implementeren waarmee de gesimuleerde gegevens worden geanalyseerd voor zakelijke inzichten.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Cloudresources:

- Een resourcegroep voor het beheren van alle resources die u in deze snelstart maakt. We gebruiken de voorbeeldnaam van de resourcegroep **IoTEdgeResources** in deze quickstart en de volgende zelfstudies.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Begin met de snelstart door een IoT Hub met Azure CLI te maken.

![Diagram - Een IoT-hub maken in de cloud](./media/quickstart-linux/create-iot-hub.png)

Het gratis niveau van IoT Hub werkt voor deze snelstart. Als u in het verleden IoT Hub hebt gebruikt en al een hub hebt gemaakt, kunt u die IoT-hub gebruiken.

Met de volgende code wordt een gratis **F1**-hub gemaakt in de resourcegroep **IoTEdgeResources**. Vervang `{hub_name}` door een unieke naam voor uw IoT-hub. Het kan enkele minuten duren voordat een IoT-hub is gemaakt.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Als er een fout optreedt omdat er al één gratis hub in uw abonnement is, wijzigt u de SKU in **S1**. Elk abonnement biedt toegang tot slechts één gratis IoT-hub. Als u het foutbericht ontvangt dat de naam van de IoT Hub niet beschikbaar is, betekent dit dat iemand anders al een hub met die naam heeft. Probeer een andere naam.

## <a name="register-an-iot-edge-device"></a>Een IoT Edge-apparaat registreren

Registreer een IoT Edge-apparaat bij uw net gemaakte IoT Hub.

![Diagram - Een apparaat registreren met een IoT Hub-id](./media/quickstart-linux/register-device.png)

Maak een apparaat-id voor uw IoT Edge-apparaat, zodat het met uw IoT Hub kan communiceren. De apparaat-id is opgeslagen in de cloud, en u gebruikt een unieke apparaatverbindingsreeks om een fysiek apparaat te koppelen aan een apparaat-id.

Omdat IoT Edge-apparaten zich anders gedragen en anders kunnen worden beheerd dan gewone IoT-apparaten, declareert u deze identiteit met een `--edge-enabled`-vlag als een identiteit van een IoT Edge-apparaat.

1. Voer in Azure Cloud Shell de volgende opdracht in om een ​​apparaat met de naam **myEdgeDevice** in uw hub te maken.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Als u een foutbericht over iothubowner-beleidssleutels ontvangt, controleert u of in Cloud Shell de meest recente versie van de azure-iot-extensie wordt uitgevoerd.

2. Bekijk de verbindingsreeks voor uw apparaat. Hiermee wordt uw fysieke apparaat aan de bijbehorende identiteit in IoT Hub gekoppeld. De verbindingsreeks bevat de naam van uw IoT-hub, de naam van uw apparaat en vervolgens een gedeelde sleutel waarmee verbindingen tussen de twee worden geverifieerd. Deze verbindingsreeks wordt in de volgende sectie opnieuw gebruikt om uw IoT Edge-apparaat in te stellen.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Verbindingsreeks uit de CLI-uitvoer weergeven](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Een IoT Edge-apparaat configureren

Maak een virtuele machine met de Azure IoT Edge-runtime.

![Diagram - De runtime op apparaat starten](./media/quickstart-linux/start-runtime.png)

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. Deze bevat drie onderdelen. De *IoT Edge-beveiligingsdaemon* wordt telkens gestart wanneer een IoT Edge-apparaat wordt opgestart en start het apparaat op door de IoT Edge-agent te starten. De *IoT Edge-agent* faciliteert de implementatie en bewaking van modules op het IoT Edge-apparaat, inclusief de IoT Edge-hub. Met de *IoT Edge-hub* wordt de communicatie tussen modules op het IoT Edge-apparaat en tussen het apparaat en IoT Hub beheerd.

Tijdens de installatie van de runtime geeft u een apparaatverbindingsreeks op. Dit is de tekenreeks die u hebt opgehaald via de Azure CLI. Deze tekenreeks koppelt uw fysieke apparaat aan de IoT Edge-apparaat-id in Azure.

### <a name="deploy-the-iot-edge-device"></a>Het IoT Edge-apparaat implementeren

In deze sectie wordt een Azure Resource Manager-sjabloon gebruikt om een nieuwe virtuele machine te maken en de IoT Edge-runtime hierop te installeren. Als u in plaats daarvan uw eigen Linux-apparaat wilt gebruiken, kunt u de installatiestappen in [De Azure IoT Edge-runtime installeren](how-to-install-iot-edge.md) volgen en vervolgens terugkeren naar deze quickstart.

Gebruik de volgende CLI-opdracht om uw IoT Edge-apparaat te maken op basis van de vooraf gemaakte sjabloon [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy).

* Bash- of Cloud Shell-gebruikers kunnen de volgende opdracht naar een teksteditor kopiëren, de tekst van de tijdelijke aanduiding vervangen door hun eigen informatie en de tijdelijke aanduiding kopiëren naar het Bash- of Cloud Shell-venster:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name
   <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell-gebruiken kunnen de volgende opdracht naar hun PowerShell-venster kopiëren en vervolgens de tekst van de tijdelijke aanduiding vervangen door uw eigen informatie:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

Voor deze sjabloon worden de volgende parameters gebruikt:

| Parameter | Beschrijving |
| --------- | ----------- |
| **resource-group** | De resourcegroep waarin de resources worden gemaakt. Gebruik de standaardinstelling **IoTEdgeResources** die in dit artikel is gebruikt of geef de naam op van een bestaande resourcegroep in uw abonnement. |
| **template-uri** | Een verwijzing naar de Resource Manager-sjabloon die we gebruiken. |
| **dnsLabelPrefix** | Een tekenreeks die wordt gebruikt om de hostnaam van de virtuele machine te maken. Vervang de tijdelijke aanduiding voor tekst door een naam voor uw virtuele machine. |
| **adminUsername** | Een gebruikersnaam voor het beheerdersaccount van de virtuele machine. Gebruik het voorbeeld **azureUser** of geef een nieuwe gebruikersnaam op. |
| **deviceConnectionString** | De verbindingsreeks van de apparaat-id in IoT Hub, die wordt gebruikt om de IoT Edge-runtime op de virtuele machine te configureren. De CLI-opdracht in deze parameter haalt de verbindingsreeks voor u op. Vervang de tekst van de tijdelijke aanduiding door de naam van uw IoT-hub. |
| **authenticationType** | De verificatiemethode voor het beheerdersaccount. Deze quickstart maakt gebruik van **wachtwoordverificatie**, maar u kunt deze parameter ook instellen op **sshPublicKey**. |
| **adminPasswordOrKey** | Het wachtwoord of de waarde voor de SSH-sleutel voor het beheerdersaccount. Vervang de tekst van de tijdelijke aanduiding door een veilig wachtwoord. Uw wachtwoord moet ten minste 12 tekens lang zijn en drie van de volgende vier typen tekens bevatten: kleine letters, hoofdletters, cijfers en speciale tekens. |

Zodra de implementatie is voltooid, ontvangt u de uitvoer met JSON-indeling in de CLI die de SSH-gegevens bevat om verbinding te maken met de virtuele machine. Kopieer de waarde van de **openbare SSH-vermelding** in het gedeelte met **uitvoer**:

   ![De openbare SSH-waarde ophalen uit de uitvoer](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>De IoT Edge runtime-status bekijken

De overige opdrachten in deze snelstart worden toegepast op uw IoT Edge-apparaat zelf, zodat u kunt zien wat er op het apparaat gebeurt. Als u een virtuele machine gebruikt, maakt u nu verbinding met die machine. Gebruik hiervoor de gebruikersnaam van de beheerder die u hebt ingesteld en de DNS-naam die door de implementatieopdracht is uitgevoerd. U kunt de DNS-naam ook vinden op de overzichtspagina van de virtuele machine in Azure Portal. Gebruik de volgende opdracht om verbinding te maken met uw virtuele machine. Vervang `{admin username}` en `{DNS name}` door uw eigen waarden.

   ```console
   ssh {admin username}@{DNS name}
   ```

Zodra er verbinding met uw virtuele machine is gemaakt, controleert u of de runtime goed is geïnstalleerd en geconfigureerd op uw IoT Edge-apparaat.

1. Controleer of de IoT Edge-beveiligingsdaemon wordt uitgevoerd als een systeemservice.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Zie hoe de IoT Edge-beveiligingsdeamon wordt uitgevoerd als een systeemservice](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >U hebt verhoogde bevoegdheden nodig om `iotedge`-opdrachten uit te voeren. Nadat u zich de eerste keer na de installatie van de IoT Edge-runtime hebt afgemeld en opnieuw hebt aangemeld, worden uw machtigingen automatisch bijgewerkt. Gebruik tot die tijd `sudo` voorafgaand aan de opdrachten.

2. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

   ```bash
   journalctl -u iotedge
   ```

3. Bekijk alle modules die op uw IoT Edge-apparaat worden uitgevoerd. Aangezien de service net voor het eerst is gestart, zou u moeten zien dat alleen de **edgeAgent**-module actief is. De edgeAgent-module wordt standaard uitgevoerd en helpt bij het installeren en starten van aanvullende modules die u op uw apparaat implementeert.

   ```bash
   sudo iotedge list
   ```

   ![Eén module op uw apparaat bekijken](./media/quickstart-linux/iotedge-list-1.png)

Uw IoT Edge-apparaat is nu geconfigureerd. Het is gereed voor de uitvoering van modules die in de cloud zijn geïmplementeerd.

## <a name="deploy-a-module"></a>Een module implementeren

Beheer uw Azure IoT Edge-apparaat vanuit de cloud om een module te implementeren waarmee telemetriegegevens worden verzonden naar IoT Hub.

![Diagram - Module implementeren vanuit cloud op apparaat](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

In deze snelstart hebt u een nieuw IoT Edge-apparaat gemaakt en de IoT Edge-runtime erop geïnstalleerd. Vervolgens hebt u de Azure-portal gebruikt om een IoT Edge-module te implementeren om te worden uitgevoerd op het apparaat zonder dat er wijzigingen aan het apparaat zelf hoefden te worden aangebracht.

In dit geval worden met de module die u hebt gepusht, gegevens voor een voorbeeldomgeving gegenereerd die u later voor testen kunt gebruiken. De gesimuleerde sensor bewaakt zowel een machine als de omgeving rond die machine. Deze sensor kan zich bijvoorbeeld in een serverruimte, in een fabriek of op een windturbine bevinden. Het bericht bevat informatie over de omgevingstemperatuur, de luchtvochtigheid, de machinetemperatuur en de druk, evenals een tijdstempel. In de IoT Edge-zelfstudies worden gegevens van deze module gebruikt als testgegevens voor analyses.

Open opnieuw de opdrachtprompt op uw IoT Edge-apparaat of gebruik de SSH-verbinding vanuit de Azure CLI. Bevestig dat de module die vanuit de cloud is geïmplementeerd, op uw IoT Edge -apparaat wordt uitgevoerd:

   ```bash
   sudo iotedge list
   ```

   ![Drie modules op uw apparaat bekijken](./media/quickstart-linux/iotedge-list-2.png)

De berichten bekijken die vanuit de temperatuursensormodule worden verzonden:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge-opdrachten zijn hoofdlettergevoelig wanneer u naar modulenamen verwijst.

   ![De gegevens van uw module bekijken](./media/quickstart-linux/iotedge-logs.png)

U kunt de berichten ook zien binnenkomen bij uw IoT Hub door de [Azure IoT Hub-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt doorgaan met de IoT Edge-zelfstudies, kunt u het apparaat gebruiken dat u hebt geregistreerd en ingesteld in deze snelstart. Anders kunt u de Azure-resources die u hebt gemaakt verwijderen om kosten te voorkomen.

Als u uw virtuele machine en IoT-hub in een nieuwe resourcegroep hebt gemaakt, kunt u die groep en alle bijbehorende resources verwijderen. Controleer de inhoud van de resourcegroep zorgvuldig om te na te gaan of er niets is dat u wilt behouden. Als u niet de hele groep wilt verwijderen, kunt u in plaats daarvan afzonderlijke resources verwijderen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt.

Verwijder de groep **IoTEdgeResources**. Het kan enkele minuten duren voordat een resourcegroep is verwijderd.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

U kunt controleren of de resourcegroep is verwijderd door de lijst met resourcegroepen te bekijken.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Edge-apparaat gemaakt en de Azure IoT Edge-cloudinterface gebruikt om code te implementeren op het apparaat. U hebt nu een testapparaat waarmee ruwe gegevens over de omgeving worden gegenereerd.

De volgende stap bestaat uit het instellen van uw lokale ontwikkelomgeving, zodat u kunt beginnen met het maken van IoT Edge-modules die uw bedrijfslogica uitvoeren.

> [!div class="nextstepaction"]
> [Beginnen met het ontwikkelen van IoT Edge-modules voor Linux-apparaten](tutorial-develop-for-linux.md)
