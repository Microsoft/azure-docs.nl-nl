---
title: 'Snelstartgids: een Azure IoT Edge-apparaat maken in Windows | Microsoft Docs'
description: In deze snelstartgids leert u hoe u een IoT Edge-apparaat maakt en daarna kant-en-klare code op afstand implementeert vanuit Azure Portal.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f3af2b7839465f886d1edba01eb9988419761dac
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630892"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Quick Start: uw eerste IoT Edge-module implementeren op een Windows-apparaat (preview)

Probeer Azure IoT Edge in deze Snelstartgids door container code te implementeren naar een Linux op Windows IoT Edge-apparaat. Met IoT Edge kunt u code op uw apparaten op afstand beheren zodat u meer van uw workloads naar de rand kunt verzenden. Voor deze Snelstartgids kunt u het beste uw eigen apparaat gebruiken om te zien hoe eenvoudig het is om Azure IoT Edge te gebruiken voor Linux in Windows.

In deze snelstart leert u de volgende zaken:

* Maak een IoT-hub.
* Een IoT Edge-apparaat registreren in uw IoT-hub.
* Installeer en start de IoT Edge voor Linux in Windows runtime op uw apparaat.
* Een module op afstand implementeren op een IoT Edge apparaat en telemetrie verzenden.

![Diagram - Snelstartarchitectuur voor apparaat en cloud](./media/quickstart/install-edge-full.png)

In deze snelstartgids vindt u informatie over het instellen van uw Azure IoT Edge voor Linux op een Windows-apparaat. Vervolgens implementeert u een module vanuit Azure Portal op uw apparaat. De module die in deze quickstart wordt gebruikt, is een gesimuleerde sensor waarmee temperatuur-, luchtvochtigheids- en drukgegevens worden gegenereerd. De andere Azure IoT Edge-zelfstudies bouwen voort op het werk dat u hier doet door aanvullende modules te implementeren waarmee de gesimuleerde gegevens worden geanalyseerd voor zakelijke inzichten.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

>[!NOTE]
>IoT Edge voor Linux in Windows is in [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Cloudresources:

* Een resourcegroep voor het beheren van alle resources die u in deze snelstart maakt.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-apparaat:

* Uw apparaat moet een Windows-PC of-server zijn, versie 1809 of hoger
* Ten minste 4 GB geheugen, aanbevolen 8 GB geheugen
* 10 GB beschikbare schijfruimte

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

![Diagram - Een apparaat registreren met een IoT Hub-id](./media/quickstart/register-device.png)

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

Installeer IoT Edge voor Linux in Windows op uw apparaat en configureer dit met een apparaat connection string.

![Diagram: de IoT Edge runtime starten op het apparaat](./media/quickstart/start-runtime.png)

1. [Down load Windows-beheer centrum](https://aka.ms/WACDownloadEFLOW).
2. Volg de installatie wizard om Windows-beheer centrum op uw apparaat in te stellen.
3. Zodra u zich in het Windows-beheer centrum bevindt, selecteert u rechtsboven in het scherm het **tandwiel pictogram instellingen**  
4. Klik in het menu instellingen onder gateway op **extensies** .
5. Selecteer in de lijst met **beschik bare uitbrei dingen** **Azure IOT Edge**
6. De uitbrei ding **installeren**

7. Zodra de uitbrei ding is geïnstalleerd, gaat u naar de hoofd pagina van het dash board door **Windows-beheer centrum** linksboven op het scherm te selecteren.

8. U ziet de verbinding van de lokale host waarmee de PC wordt aangegeven waarop u Windows-beheer centrum uitvoert.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Scherm opname-start pagina van Windows-beheerder":::

9. Selecteer **Toevoegen**.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Scherm opname-start pagina toevoegen van de Windows-beheerder":::

10. Zoek de tegel Azure IoT Edge en selecteer **nieuwe maken**. Hiermee wordt de installatie wizard gestart.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Scherm opname-Azure IoT Edge voor Linux op Windows-tegel":::

11. Ga door met de installatie wizard om de gebruiksrecht overeenkomst te accepteren en kies **volgende**

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Scherm opname-welkom van de wizard":::

12. Kies de **optionele diagnostische gegevens** om uitgebreide diagnostische gegevens op te geven waarmee micro soft de kwaliteit van de service kan controleren en onderhouden, en klik op **volgende: implementeren**

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Scherm opname-diagnostische gegevens":::

13. Selecteer op het scherm **doel apparaat selecteren** het gewenste doel apparaat om te valideren dat het voldoet aan de minimale vereisten. Voor deze Quick Start installeren we IoT Edge op het lokale apparaat. Kies daarom de localhost-verbinding. Nadat dit is bevestigd, klikt u op **volgende** om door te gaan

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Scherm opname-doel apparaat selecteren":::

14. Accepteer de standaard instellingen door **volgende** te kiezen.

15. Het scherm implementatie toont het proces van het downloaden van het pakket, het installeren van het pakket, het configureren van de host en het laatste instellen van de virtuele Linux-machine.  Een geslaagde implementatie ziet er als volgt uit:

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Scherm opname-de wizard implementatie is voltooid":::

16. Klik op **volgende: verbinding maken** om door te gaan naar de laatste stap om uw Azure IOT edge apparaat in te richten met de apparaat-id van de IOT hub-instantie.

17. Kopieer de connection string van het apparaat in azure IoT Hub en plak het in het veld apparaat connection string. Kies vervolgens **inrichten met de geselecteerde methode**.

    > [!NOTE]
    > Zie stap 3 in de vorige sectie [een IOT edge apparaat registreren](#register-an-iot-edge-device)om uw Connection String op te halen.

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Scherm afbeelding-inrichting van wizard":::

18. Zodra de inrichting is voltooid, selecteert u **volt** ooien om te volt ooien en gaat u terug naar het Start scherm van Windows-beheer centrum. U kunt uw apparaat nu weer geven als een IoT Edge apparaat.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Scherm opname-Windows-beheer centrum Azure IoT Edge apparaat":::

19. Selecteer uw Azure IoT Edge-apparaat om het dash board ervan weer te geven. U ziet dat de werk belastingen van uw apparaat tussen Azure IoT Hub zijn geïmplementeerd. In de **lijst IOT Edge module** moet één module worden weer gegeven, **edgeAgent**, en de **IOT Edge status** moet **actief (actief)** zijn.

Uw IoT Edge-apparaat is nu geconfigureerd. Het is gereed voor de uitvoering van modules die in de cloud zijn geïmplementeerd.

## <a name="deploy-a-module"></a>Een module implementeren

Beheer uw Azure IoT Edge-apparaat vanuit de cloud om een module te implementeren waarmee telemetriegegevens worden verzonden naar IoT Hub.

![Diagram - Module implementeren vanuit cloud op apparaat](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

In deze snelstart hebt u een nieuw IoT Edge-apparaat gemaakt en de IoT Edge-runtime erop geïnstalleerd. Vervolgens hebt u de Azure-portal gebruikt om een IoT Edge-module te implementeren om te worden uitgevoerd op het apparaat zonder dat er wijzigingen aan het apparaat zelf hoefden te worden aangebracht.

In dit geval worden met de module die u hebt gepusht, gegevens voor een voorbeeldomgeving gegenereerd die u later voor testen kunt gebruiken. De gesimuleerde sensor bewaakt zowel een machine als de omgeving rond die machine. Deze sensor kan zich bijvoorbeeld in een serverruimte, in een fabriek of op een windturbine bevinden. Het bericht bevat informatie over de omgevingstemperatuur, de luchtvochtigheid, de machinetemperatuur en de druk, evenals een tijdstempel. In de IoT Edge-zelfstudies worden gegevens van deze module gebruikt als testgegevens voor analyses.

Controleer of de module die is geïmplementeerd vanuit de Cloud wordt uitgevoerd op uw IoT Edge-apparaat door te navigeren naar de opdracht shell in het Windows-beheer centrum.

1. Verbinding maken met uw nieuw gemaakte IoT Edge-apparaat

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Scherm opname-apparaat verbinden":::

2. Op de **overzichts** pagina ziet u de **lijst met IoT Edge modules** en **IOT Edge status** waar u de verschillende modules kunt zien die zijn geïmplementeerd, evenals de status van het apparaat.  

3. Onder **extra** selecteert u **opdracht shell**. De opdracht shell is een Power shell-terminal die automatisch SSH (Secure Shell) gebruikt om verbinding te maken met de Linux-VM van uw Azure IoT Edge apparaat op uw Windows-PC.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Scherm afbeelding-opdracht shell":::

4. Als u de drie modules op uw apparaat wilt controleren, voert u de volgende **bash-opdracht** uit:

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Scherm opname-opdracht shell-lijst":::

5. Bekijk de berichten die vanaf de temperatuursensormodule naar de cloud worden verzonden.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge-opdrachten zijn hoofdlettergevoelig wanneer u naar modulenamen verwijst.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Scherm opname-temperatuur sensor":::

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

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Schone verwijdering van Azure IoT Edge voor Linux in Windows

U kunt Azure IoT Edge voor Linux in Windows verwijderen van uw IoT Edge-apparaat via de dash board-extensie in het Windows-beheer centrum.

1. Maak verbinding met de Azure IoT Edge voor Linux op Windows-apparaat-verbinding in het Windows-beheer centrum. De extensie van het Azure dash board-hulp programma wordt geladen.
2. Selecteer **Verwijderen**. Als Azure IoT Edge voor Linux in Windows is verwijderd, gaat u naar de start pagina van het Windows-beheer centrum en verwijdert u de vermelding van het Azure IoT Edge apparaat uit de lijst.

Een andere manier om Azure IOT Edge van uw Windows-systeem te verwijderen, is door naar de apps voor de **Start**  >  **instellingen** te gaan  >    >  **Azure IOT Edge**  >  de installatie op uw IOT edge apparaat te **verwijderen** . Hiermee verwijdert u Azure IoT Edge van uw IoT Edge apparaat, maar sluit u de verbinding achter in het Windows-beheer centrum. Het Windows-beheer centrum kan ook worden verwijderd uit het menu instellingen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Edge-apparaat gemaakt en de Azure IoT Edge-cloudinterface gebruikt om code te implementeren op het apparaat. U hebt nu een testapparaat waarmee ruwe gegevens over de omgeving worden gegenereerd.

De volgende stap bestaat uit het instellen van uw lokale ontwikkelomgeving, zodat u kunt beginnen met het maken van IoT Edge-modules die uw bedrijfslogica uitvoeren.

> [!div class="nextstepaction"]
> [Beginnen met het ontwikkelen van IoT Edge modules](tutorial-develop-for-linux.md)
