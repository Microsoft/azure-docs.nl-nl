---
title: 'Snelstartgids: een Azure IoT Edge-apparaat maken in Windows | Microsoft Docs'
description: In deze snelstartgids leert u hoe u een IoT Edge-apparaat maakt en daarna kant-en-klare code op afstand implementeert vanuit Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 547bf111e73813c939caa917c0117dac6c8989e9
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922462"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Quickstart: Uw eerste IoT Edge-module implementeren in een virtueel Windows-apparaat

Probeer Azure IoT Edge uit in deze quickstart door code in een container te implementeren op een virtueel Windows IoT Edge-apparaat. Met IoT Edge kunt u code op uw apparaten op afstand beheren zodat u meer van uw workloads naar de rand kunt verzenden. Voor deze quickstart kunt u het beste een virtuele Azure-machine gebruiken voor uw IoT Edge-apparaat. Met een virtuele machine kunt u snel een testmachine maken, de vereisten installeren en deze vervolgens verwijderen wanneer u klaar bent.

In deze snelstart leert u de volgende zaken:

* Maak een IoT-hub.
* Een IoT Edge-apparaat registreren in uw IoT-hub.
* De IoT Edge-runtime op uw virtuele apparaat installeren en starten.
* Op afstand een module op een IoT Edge-apparaat implementeren en telemetrie naar IoT Hub verzenden.

![Diagram - Snelstartarchitectuur voor apparaat en cloud](./media/quickstart/install-edge-full.png)

In deze quickstart leert u hoe u een Windows VM maakt die is geconfigureerd als een IoT Edge-apparaat. Vervolgens implementeert u een module vanuit Azure Portal op uw apparaat. De module die in deze quickstart wordt gebruikt, is een gesimuleerde sensor waarmee temperatuur-, luchtvochtigheids- en drukgegevens worden gegenereerd. De andere Azure IoT Edge-zelfstudies bouwen voort op het werk dat u hier doet door aanvullende modules te implementeren waarmee de gesimuleerde gegevens worden geanalyseerd voor zakelijke inzichten.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Bereid uw omgeving voor op Azure CLI.

- Gebruik [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell) met behulp van de PowerShell-environment.

   [![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)   
- [Installeer](/cli/azure/install-azure-cli) de Azure CLI, indien gewenst, om CLI-referentieopdrachten uit te voeren.
   - Als u een lokale installatie gebruikt, meldt u zich aan bij Azure CLI met behulp van de opdracht [AZ login](/cli/azure/reference-index#az-login).  Volg de stappen die worden weergegeven in de terminal, om het verificatieproces te voltooien.  Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli) voor aanvullende aanmeldingsopties.
  - Installeer de Azure CLI-extensie bij het eerste gebruik, wanneer u hierom wordt gevraagd.  Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies.
  - Voer [az version](/cli/azure/reference-index?#az_version) uit om de geïnstalleerde versie en afhankelijke bibliotheken te vinden. Voer [az upgrade](/cli/azure/reference-index?#az_upgrade) uit om te upgraden naar de nieuwste versie.

Cloudresources:

- Een resourcegroep voor het beheren van alle resources die u in deze snelstart maakt.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-apparaat:

- Een Windows VM die fungeert als uw IoT Edge-apparaat. U kunt deze virtuele machine maken met behulp van de volgende opdracht, waarbij `{password}` wordt vervangen door een beveiligd wachtwoord:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Het maken en starten van de nieuwe virtuele machine kan een paar minuten duren.

  Zodra uw virtuele machine start, kunt u vervolgens een RDP-bestand downloaden dat moet worden gebruikt om verbinding te maken met uw virtuele machine:

  1. Navigeer naar uw nieuwe Windows VM in Azure Portal.
  1. Selecteer **Verbinden**.
  1. Selecteer op het tabblad **RDP** de optie **RDP-bestand downloaden**.

  Open dit bestand met Verbinding met extern bureaublad om verbinding te maken met uw virtuele Windows-machine. Gebruik de beheerdersnaam en het beheerderswachtwoord die u hebt opgegeven met de opdracht `az vm create`.

> [!NOTE]
> Uw virtuele Windows-machine wordt gestart met Windows versie 1809 (build 17763). Dit is de nieuwste [Windows-build met langetermijnondersteuning](/windows/release-information/). In Windows wordt standaard elke 22 uur automatisch op updates gecontroleerd. Na een controle op uw virtuele machine wordt door Windows een versie-update gepusht die niet compatibel is met IoT Edge voor Windows, waardoor IoT Edge voor Windows-functies niet meer kunnen worden gebruikt. U wordt aangeraden om uw virtuele machine niet langer dan 22 uur te gebruiken of [Windows-updates tijdelijk te onderbreken](https://support.microsoft.com/help/4028233/windows-10-manage-updates).
>
> Voor het gemak wordt in deze quickstart gebruikgemaakt van een Windows VM. Zie [Systemen met ondersteuning voor Azure IoT Edge](support.md) voor meer informatie over welke Windows-besturingssystemen algemeen beschikbaar zijn voor productiescenario's.
>
> Als u uw eigen Windows-apparaat wilt configureren voor IoT Edge, met inbegrip van apparaten met IoT Core, volgt u de stappen in [De Azure IoT Edge-runtime installeren](how-to-install-iot-edge.md).

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Begin met de snelstart door een IoT Hub met Azure CLI te maken.

![Diagram - Een IoT-hub maken in de cloud](./media/quickstart/create-iot-hub.png)

Het gratis niveau van IoT Hub werkt voor deze snelstart. Als u in het verleden IoT Hub hebt gebruikt en al een hub hebt gemaakt, kunt u die IoT-hub gebruiken.

Met de volgende code wordt een gratis **F1**-hub gemaakt in de resourcegroep `IoTEdgeResources`. Vervang `{hub_name}` door een unieke naam voor uw IoT-hub. Het kan enkele minuten duren voordat een IoT-hub is gemaakt.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Als er een fout optreedt omdat er al één gratis hub in uw abonnement is, wijzigt u de SKU in **S1**. Als u het foutbericht ontvangt dat de naam van de IoT Hub niet beschikbaar is, betekent dit dat iemand anders al een hub met die naam heeft. Probeer een andere naam.

## <a name="register-an-iot-edge-device"></a>Een IoT Edge-apparaat registreren

Registreer een IoT Edge-apparaat bij uw net gemaakte IoT Hub.
![Diagram - Een apparaat registreren met een IoT Hub-entiteit](./media/quickstart/register-device.png)

Maak een apparaat-id voor uw gesimuleerde apparaat, zodat het met uw IoT-hub kan communiceren. De apparaat-id is opgeslagen in de cloud, en u gebruikt een unieke apparaatverbindingsreeks om een fysiek apparaat te koppelen aan een apparaat-id.

Omdat IoT Edge-apparaten zich anders gedragen en anders kunnen worden beheerd dan gewone IoT-apparaten, declareert u deze identiteit met een `--edge-enabled`-vlag als een identiteit van een IoT Edge-apparaat.

1. Voer in Azure Cloud Shell de volgende opdracht in om een ​​apparaat met de naam **myEdgeDevice** in uw hub te maken.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Als u een foutbericht over iothubowner-beleidssleutels ontvangt, controleert u of in Cloud Shell de meest recente versie van de azure-iot-extensie wordt uitgevoerd.

2. Bekijk de verbindingsreeks voor uw apparaat. Hiermee wordt uw fysieke apparaat aan de bijbehorende identiteit in IoT Hub gekoppeld. De verbindingsreeks bevat de naam van uw IoT-hub, de naam van uw apparaat en vervolgens een gedeelde sleutel waarmee verbindingen tussen de twee worden geverifieerd.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Kopieer de waarde van de sleutel `connectionString` uit de JSON-uitvoer en sla deze op. Deze waarde is de verbindingsreeks van het apparaat. In de volgende sectie gaat u deze verbindingsreeks gebruiken om de IoT Edge-runtime te configureren.

   ![Verbindingsreeks ophalen uit de CLI-uitvoer](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren en starten

Installeer de Azure IoT Edge-runtime op uw IoT Edge-apparaat en configureer deze met een apparaatverbindingsreeks.
![Diagram - De runtime op apparaat starten](./media/quickstart/start-runtime.png)

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. Deze bevat drie onderdelen. De *IoT Edge-beveiligingsdaemon* wordt telkens gestart wanneer een IoT Edge-apparaat wordt opgestart en start het apparaat op door de IoT Edge-agent te starten. De *IoT Edge-agent* beheert de implementatie en bewaking van modules op het IoT Edge-apparaat, inclusief de IoT Edge-hub. De *IoT Edge-hub* verzorgt de communicatie tussen modules op het IoT Edge-apparaat en tussen het apparaat en IoT Hub.

Het installatiescript bevat ook de containerengine Moby waarmee de containerinstallatiekopieën op uw IoT Edge-apparaat worden beheerd.

Tijdens de installatie van de runtime wordt u naar de apparaatverbindingsreeks gevraagd. Gebruik de tekenreeks die u hebt opgehaald via de Azure CLI. Deze tekenreeks koppelt uw fysieke apparaat aan de IoT Edge-apparaat-id in Azure.

### <a name="connect-to-your-iot-edge-device"></a>Verbinding maken met uw IoT Edge-apparaat

Aangezien de stappen in deze sectie allemaal plaatsvinden op uw IoT Edge-apparaat, maakt u nu via extern bureaublad verbinding met de desbetreffende virtuele machine.

### <a name="install-and-configure-the-iot-edge-service"></a>De IoT Edge-service installeren en configureren

Gebruik PowerShell om de IoT Edge-runtime te downloaden en te installeren. Gebruik de apparaatverbindingsreeks die u hebt opgehaald via IoT Hub om uw apparaat te configureren.

1. Voer PowerShell uit op de virtuele machine als beheerder.

   >[!NOTE]
   >Gebruik een AMD64-sessie van PowerShell om IoT Edge, niet PowerShell (x86), te installeren. Als u niet zeker weet welk sessietype u gebruikt, voert u de volgende opdracht uit:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

2. Met de opdracht **Deploy-IoTEdge** wordt gecontroleerd of de Windows-computer een ondersteunde versie heeft, wordt de containersfunctie ingeschakeld en worden achtereenvolgens de Moby- en IoT Edge-runtime gedownload.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

3. De machine wordt mogelijk automatisch opnieuw opgestart. Als u door de opdracht Deploy-IoTEdge wordt gevraagd opnieuw op te starten, doet u dat.

4. Voer PowerShell weer uit als beheerder.

5. Met de opdracht **Initialize-IoTEdge** configureert u de IoT Edge-runtime op uw machine. De opdracht wordt standaard ingesteld op handmatig inrichten met Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

6. Als u wordt gevraagd naar een **DeviceConnectionString**, geeft u de tekenreeks op die u in de vorige sectie hebt gekopieerd. Plaats geen aanhalingstekens rond de verbindingsreeks.

### <a name="view-the-iot-edge-runtime-status"></a>De IoT Edge runtime-status bekijken

Controleer of de runtime goed is geïnstalleerd en geconfigureerd. Het kan enkele minuten duren voordat de installatie is voltooid en de IoT Edge-agentmodule wordt gestart.

1. Controleer de status van de IoT Edge-service.

   ```powershell
   Get-Service iotedge
   ```

2. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Bekijk alle modules die op uw IoT Edge-apparaat worden uitgevoerd. Aangezien de service net voor het eerst is gestart, zou u moeten zien dat alleen de **edgeAgent**-module actief is. De edgeAgent-module wordt standaard uitgevoerd en helpt bij het installeren en starten van aanvullende modules die u op uw apparaat implementeert.

    ```powershell
    iotedge list
    ```

   ![Eén module op uw apparaat bekijken](./media/quickstart/iotedge-list-1.png)

Uw IoT Edge-apparaat is nu geconfigureerd. Het is gereed voor de uitvoering van modules die in de cloud zijn geïmplementeerd.

## <a name="deploy-a-module"></a>Een module implementeren

Beheer uw Azure IoT Edge-apparaat vanuit de cloud om een module te implementeren waarmee telemetriegegevens worden verzonden naar IoT Hub.

![Diagram - Module implementeren vanuit cloud op apparaat](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

In deze snelstart hebt u een nieuw IoT Edge-apparaat gemaakt en de IoT Edge-runtime erop geïnstalleerd. Vervolgens hebt u de Azure-portal gebruikt om een IoT Edge-module te implementeren om te worden uitgevoerd op het apparaat zonder dat er wijzigingen aan het apparaat zelf hoefden te worden aangebracht.

In dit geval worden met de module die u hebt gepusht, gegevens voor een voorbeeldomgeving gegenereerd die u later voor testen kunt gebruiken. De gesimuleerde sensor bewaakt zowel een machine als de omgeving rond die machine. Deze sensor kan zich bijvoorbeeld in een serverruimte, in een fabriek of op een windturbine bevinden. Het bericht bevat informatie over de omgevingstemperatuur, de luchtvochtigheid, de machinetemperatuur en de druk, evenals een tijdstempel. In de IoT Edge-zelfstudies worden gegevens van deze module gebruikt als testgegevens voor analyses.

Bevestig dat de module die vanuit de cloud is geïmplementeerd, op uw IoT Edge -apparaat wordt uitgevoerd.

```powershell
iotedge list
```

   ![Drie modules op uw apparaat bekijken](./media/quickstart/iotedge-list-2.png)

Bekijk de berichten die vanaf de temperatuursensormodule naar de cloud worden verzonden.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >IoT Edge-opdrachten zijn hoofdlettergevoelig wanneer u naar modulenamen verwijst.

   ![De gegevens van uw module bekijken](./media/quickstart/iotedge-logs.png)

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
> [Beginnen met het ontwikkelen van IoT Edge-modules voor Windows-apparaten](tutorial-develop-for-windows.md)
