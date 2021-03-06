---
title: 'Snelstartgids: Een Azure IoT Edge-apparaat maken in Linux | Microsoft Docs'
description: In deze quickstart leert u hoe u een IoT Edge-apparaat op Linux maakt en daarna kant-en-klare code op afstand implementeert vanuit de Azure-portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc5dd2df0dc6ac682d37aea3328545fcb7e5ad2
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728604"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Quickstart: Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Probeer Azure IoT Edge uit in deze quickstart door containercode te implementeren op een virtueel Linux IoT Edge-apparaat. Met IoT Edge kunt u code op uw apparaten op afstand beheren zodat u meer van uw workloads naar de rand kunt verzenden. Voor deze quickstart raden we u aan een virtuele Machine van Azure te gebruiken voor uw IoT Edge-apparaat, waarmee u snel een testmachine kunt maken en deze vervolgens kunt verwijderen wanneer u klaar bent.

In deze snelstart leert u de volgende zaken:

* Een IoT Hub maken.
* Een IoT Edge-apparaat registreren in uw IoT-hub.
* Installeer en start de IoT Edge runtime op een virtueel apparaat.
* Op afstand een module implementeren op een IoT Edge-apparaat.

![Diagram - Snelstartarchitectuur voor apparaat en cloud](./media/quickstart-linux/install-edge-full.png)

In deze quickstart leert u hoe u een virtuele Linux-machine maakt die is geconfigureerd als een IoT Edge-apparaat. Vervolgens implementeert u een module vanuit Azure Portal op uw apparaat. De module die in deze quickstart wordt gebruikt, is een gesimuleerde sensor waarmee temperatuur-, luchtvochtigheids- en drukgegevens worden gegenereerd. De andere Azure IoT Edge-zelfstudies bouwen voort op het werk dat u hier doet door aanvullende modules te implementeren waarmee de gesimuleerde gegevens worden geanalyseerd voor zakelijke inzichten.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Cloudresources:

* Een resourcegroep voor het beheren van alle resources die u in deze snelstart maakt. We gebruiken de voorbeeldnaam van de resourcegroep **IoTEdgeResources** in deze quickstart en de volgende zelfstudies.

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

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Gebruik de volgende CLI-opdracht om uw IoT Edge-apparaat te maken op basis van de vooraf gemaakte sjabloon [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy).

* Bash- of Cloud Shell-gebruikers kunnen de volgende opdracht naar een teksteditor kopiëren, de tekst van de tijdelijke aanduiding vervangen door hun eigen informatie en de tijdelijke aanduiding kopiëren naar het Bash- of Cloud Shell-venster:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
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

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Gebruik de volgende CLI-opdracht om uw IoT Edge-apparaat te maken op basis van de vooraf gemaakte sjabloon [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0).

* Bash- of Cloud Shell-gebruikers kunnen de volgende opdracht naar een teksteditor kopiëren, de tekst van de tijdelijke aanduiding vervangen door hun eigen informatie en de tijdelijke aanduiding kopiëren naar het Bash- of Cloud Shell-venster:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell-gebruiken kunnen de volgende opdracht naar hun PowerShell-venster kopiëren en vervolgens de tekst van de tijdelijke aanduiding vervangen door uw eigen informatie:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

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

<!--1.1 -->
:::moniker range="iotedge-2018-06"

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
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Controleer of de IoT Edge wordt uitgevoerd. De volgende opdracht moet de status OK retourneren **als** IoT Edge wordt uitgevoerd of servicefouten op te geven.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >U hebt verhoogde bevoegdheden nodig om `iotedge`-opdrachten uit te voeren. Nadat u zich de eerste keer na de installatie van de IoT Edge-runtime hebt afgemeld en opnieuw hebt aangemeld, worden uw machtigingen automatisch bijgewerkt. Gebruik tot die tijd `sudo` voorafgaand aan de opdrachten.

2. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

   ```bash
   sudo iotedge system logs
   ```

3. Bekijk alle modules die op uw IoT Edge-apparaat worden uitgevoerd. Aangezien de service net voor het eerst is gestart, zou u moeten zien dat alleen de **edgeAgent**-module actief is. De edgeAgent-module wordt standaard uitgevoerd en helpt bij het installeren en starten van aanvullende modules die u op uw apparaat implementeert.

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

Uw IoT Edge-apparaat is nu geconfigureerd. Het is gereed voor de uitvoering van modules die in de cloud zijn geïmplementeerd.

## <a name="deploy-a-module"></a>Een module implementeren

Beheer uw Azure IoT Edge-apparaat vanuit de cloud om een module te implementeren waarmee telemetriegegevens worden verzonden naar IoT Hub.

![Diagram - Module implementeren vanuit cloud op apparaat](./media/quickstart-linux/deploy-module.png)

<!-- [!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2 -->

Een van de belangrijkste mogelijkheden van Azure IoT Edge is het implementeren van code op uw IoT Edge apparaten vanuit de cloud. *IoT Edge-modules* zijn uitvoerbare pakketten die zijn geïmplementeerd als containers. In deze sectie implementeert u een vooraf gebouwde module uit de sectie [IoT Edge Modules van Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) rechtstreeks vanuit Azure IoT Hub.

De module die u in deze sectie implementeert, simuleert een sensor en verzendt gegenereerde gegevens. Deze module is een handig stukje code wanneer u aan de slag gaat met IoT Edge, omdat u de gesimuleerde gegevens kunt gebruiken voor ontwikkel- en testdoeleinden. Als u precies wilt zien wat deze module doet, kunt u de [broncode van de gesimuleerde temperatuursensor bekijken](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Volg deze stappen om de wizard **Modules instellen te** starten om uw eerste module te implementeren vanuit Azure Marketplace.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en ga naar uw IoT-hub.

1. Selecteer in het menu aan de linkerkant onder **Automatisch apparaatbeheer** de **optie IoT Edge.**

1. Selecteer de apparaat-id van het doelapparaat in de lijst met apparaten.

1. Selecteer op de bovenste balk **Modules instellen**.

   ![Schermopname van het selecteren van Modules instellen.](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>Modules

De eerste stap van de wizard bestaat uit het kiezen van de modules die u op uw apparaat wilt uitvoeren.

Open **IoT Edge modules** de  vervolgkeuzelijst Toevoegen en selecteer **marketplace-module.**

   ![Schermopname van de vervolgkeuzelijst Toevoegen.](./media/quickstart/add-marketplace-module.png)

Zoek **IoT Edge Module Marketplace** naar de module en selecteer `Simulated Temperature Sensor` deze. De module wordt toegevoegd aan de IoT Edge Modules met de gewenste **status.**

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Selecteer **Runtime-instellingen** om de instellingen voor de edgeHub- en edgeAgent-modules te openen. In deze sectie met instellingen kunt u de runtimemodules beheren door omgevingsvariabelen toe te voegen of de opties voor maken te wijzigen.

Werk het **veld Image** bij voor zowel de edgeHub- als edgeAgent-modules om versietag 1.2 te gebruiken. Bijvoorbeeld:

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

Selecteer **Opslaan om** uw wijzigingen toe te passen op de runtimemodules.

:::moniker-end
<!--end 1.2-->

Selecteer **Volgende: Routes** om door te gaan naar de volgende stap in de wizard.

   ![Schermopname van het doorgaan naar de volgende stap nadat de module is toegevoegd.](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>Routes

Verwijder op het tabblad **Routes** de standaardroute, **route** en selecteer vervolgens **Volgende: Beoordelen en maken** om door te gaan naar de volgende stap van de wizard.

   >[!Note]
   >Routes worden samengesteld met behulp van naam- en waardeparen. Op deze pagina ziet u twee routes. De standaardroute, **route**, verzendt alle berichten naar IoT Hub (dit wordt `$upstream` genoemd). Er is automatisch een tweede route, **SimulatedTemperatureSensorToIoTHub,** gemaakt toen u de module vanuit Azure Marketplace. Deze route verzendt alle berichten van de gesimuleerde temperatuurmodule naar IoT Hub. U kunt de standaardroute verwijderen, omdat deze in dit geval overbodig is.

   ![Schermopname van het verwijderen van de standaardroute en vervolgens het verplaatsen naar de volgende stap.](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>Controleren en maken

Controleer het JSON-bestand en selecteer vervolgens **Maken.** Het JSON-bestand definieert alle modules die u op uw IoT Edge implementeert. U ziet de **module SimulatedTemperatureSensor** en de twee runtimemodules **edgeAgent** en **edgeHub.**

   >[!Note]
   >Wanneer u een nieuwe implementatie bij een IoT Edge-apparaat indient, wordt er niets naar uw apparaat gepusht. In plaats daarvan voert het apparaat regelmatig query’s uit naar eventuele nieuwe instructies. Als het apparaat een manifest van een bijgewerkte implementatie vindt, wordt de informatie over de nieuwe implementatie gebruikt om installatiekopieën van de module op te halen uit de cloud en wordt een lokale uitvoering van de modules gestart. Dit proces kan enkele minuten duren.

Nadat u de informatie over de implementatie van de module hebt gemaakt, gaat de wizard terug naar de pagina met apparaatdetails. Bekijk de implementatiestatus op het **tabblad Modules.**

U ziet drie modules: **$edgeAgent**, **$edgeHub** en **SimulatedTemperatureSensor.** Als voor een of  meer van de modules JA is opgegeven onder OPGEGEVEN **IN** IMPLEMENTATIE, maar niet onder GERAPPORTEERD DOOR **APPARAAT,** wordt IoT Edge apparaat nog steeds met de modules. Wacht enkele minuten en vernieuw de pagina.

   ![Schermopname van Gesimuleerde temperatuursensor in de lijst met geïmplementeerde modules.](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

In deze snelstart hebt u een nieuw IoT Edge-apparaat gemaakt en de IoT Edge-runtime erop geïnstalleerd. Vervolgens hebt u de Azure-portal gebruikt om een IoT Edge-module te implementeren om te worden uitgevoerd op het apparaat zonder dat er wijzigingen aan het apparaat zelf hoefden te worden aangebracht.

In dit geval worden met de module die u hebt gepusht, gegevens voor een voorbeeldomgeving gegenereerd die u later voor testen kunt gebruiken. De gesimuleerde sensor bewaakt zowel een machine als de omgeving rond die machine. Deze sensor kan zich bijvoorbeeld in een serverruimte, in een fabriek of op een windturbine bevinden. Het bericht bevat informatie over de omgevingstemperatuur, de luchtvochtigheid, de machinetemperatuur en de druk, evenals een tijdstempel. In de IoT Edge-zelfstudies worden gegevens van deze module gebruikt als testgegevens voor analyses.

Open opnieuw de opdrachtprompt op uw IoT Edge-apparaat of gebruik de SSH-verbinding vanuit de Azure CLI. Bevestig dat de module die vanuit de cloud is geïmplementeerd, op uw IoT Edge -apparaat wordt uitgevoerd:

   ```bash
   sudo iotedge list
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
   ![Drie modules op uw apparaat bekijken](./media/quickstart-linux/iotedge-list-2-version-201806.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
   ![Drie modules op uw apparaat bekijken](./media/quickstart-linux/iotedge-list-2-version-202011.png)
:::moniker-end

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
az group delete --name IoTEdgeResources --yes
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
