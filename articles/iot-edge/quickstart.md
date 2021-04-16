---
title: Quickstart voor het maken van Azure IoT Edge apparaat in Windows | Microsoft Docs
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
ms.openlocfilehash: 9f0562d4471ac1129bf9bc7ecfee058cddac7c61
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533127"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Quickstart: Uw eerste module IoT Edge implementeren op een Windows-apparaat (preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Probeer de Azure IoT Edge in deze quickstart door in een container geplaatste code te implementeren op een Linux-apparaat IoT Edge Windows. Met IoT Edge kunt u code op uw apparaten op afstand beheren zodat u meer van uw workloads naar de rand kunt verzenden. Voor deze quickstart raden we u aan uw eigen apparaat te gebruiken om te zien hoe eenvoudig het is om Azure IoT Edge Linux in Windows te gebruiken.

In deze snelstart leert u het volgende:

* Maak een IoT-hub.
* Een IoT Edge-apparaat registreren in uw IoT-hub.
* Installeer en start de IoT Edge voor Linux in Windows-runtime op uw apparaat.
* Een module op afstand implementeren op een IoT Edge apparaat en telemetrie verzenden.

![Diagram met de architectuur van deze quickstart voor uw apparaat en cloud.](./media/quickstart/install-edge-full.png)

In deze quickstart wordt besturingssysteem voor het instellen van uw Azure IoT Edge linux op Een Windows-apparaat. Vervolgens implementeert u een module van de Azure Portal op uw apparaat. De module die u gebruikt, is een gesimuleerde sensor die temperatuur-, vochtigheids- en drukgegevens genereert. Andere Azure IoT Edge zijn gebaseerd op het werk dat u hier doet door modules te implementeren die de gesimuleerde gegevens analyseren voor zakelijke inzichten.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

>[!NOTE]
>IoT Edge voor Linux in Windows is in [openbare preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Vereisten

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Maak een cloudresourcegroep voor het beheren van alle resources die u in deze quickstart gaat gebruiken.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Zorg ervoor dat IoT Edge apparaat voldoet aan de volgende vereisten:

* Edities
  * Windows 10 versie 1809 of hoger; build 17763 of hoger
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 build 17763 of hoger

* Hardwarevereisten
  * Minimaal vrij geheugen: 1 GB
  * Minimale vrije schijfruimte: 10 GB

>[!NOTE]
>In deze quickstart wordt gebruikgemaakt Windows Admin Center om een implementatie van IoT Edge voor Linux in Windows te maken. U kunt ook PowerShell gebruiken. Als u PowerShell wilt gebruiken om uw implementatie te maken, volgt u de stappen in de handleiding voor het installeren en inrichten van Azure IoT Edge voor Linux op een [Windows-apparaat.](how-to-install-iot-edge-on-windows.md)

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

Begin met het maken van een IoT-hub met de Azure CLI.

![Diagram met de stap voor het maken van een IoT-hub.](./media/quickstart/create-iot-hub.png)

Het gratis niveau van Azure IoT Hub werkt voor deze quickstart. Als u in het verleden IoT Hub hebt gebruikt en al een hub hebt gemaakt, kunt u die IoT-hub gebruiken.

Met de volgende code wordt een gratis **F1**-hub gemaakt in de resourcegroep `IoTEdgeResources`. Vervang `{hub_name}` door een unieke naam voor uw IoT-hub. Het kan enkele minuten duren voordat een IoT-hub is gemaakt.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Als er een foutbericht wordt weergegeven omdat u al één gratis hub in uw abonnement hebt, wijzigt u de SKU in `S1` . Als er een foutbericht wordt weergegeven dat de naam van de IoT-hub niet beschikbaar is, heeft iemand anders al een hub met die naam. Probeer een andere naam.

## <a name="register-an-iot-edge-device"></a>Een IoT Edge-apparaat registreren

Registreer een IoT Edge-apparaat bij uw net gemaakte IoT Hub.

![Diagram met de stap voor het registreren van een apparaat met een IoT Hub-identiteit.](./media/quickstart/register-device.png)

Maak een apparaat-id voor uw gesimuleerde apparaat, zodat het met uw IoT-hub kan communiceren. De apparaat-id is opgeslagen in de cloud, en u gebruikt een unieke apparaatverbindingsreeks om een fysiek apparaat te koppelen aan een apparaat-id.

IoT Edge apparaten gedragen zich en kunnen anders worden beheerd dan gewone IoT-apparaten. Gebruik de `--edge-enabled` vlag om aan te geven dat deze identiteit voor een IoT Edge is.

1. Voer Azure Cloud Shell opdracht in om een apparaat met de naam **myEdgeDevice** te maken in uw hub.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Als u een foutbericht over beleidssleutels krijgt, moet u ervoor zorgen Cloud Shell de nieuwste versie van de `iothubowner` Azure IoT-extensie wordt uitgevoerd.

1. Bekijk de verbindingsreeks voor uw apparaat. Hiermee wordt uw fysieke apparaat aan de bijbehorende identiteit in IoT Hub gekoppeld. Het bevat de naam van uw IoT-hub, de naam van uw apparaat en een gedeelde sleutel die verbindingen tussen de twee verifieert.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Kopieer de waarde van de sleutel `connectionString` uit de JSON-uitvoer en sla deze op. Deze waarde is de verbindingsreeks van het apparaat. U gebruikt deze om de runtime IoT Edge configureren in de volgende sectie.

     ![Schermopname van de uitvoer van de connectionString in Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren en starten

Installeer IoT Edge linux in Windows op uw apparaat en configureer deze met de connection string.

![Diagram met de stap voor het starten van de IoT Edge runtime.](./media/quickstart/start-runtime.png)

1. [Download Windows Admin Center](https://aka.ms/wacdownload).

1. Volg de aanwijzingen in de installatiewizard om een Windows Admin Center uw apparaat in te stellen.

1. Open Windows Admin Center.

1. Selecteer het **tandwielpictogram** Instellingen in de rechterbovenhoek en selecteer vervolgens **Extensies.**

1. Selecteer op **het tabblad** Feeds de optie **Toevoegen.**

1. Voer `https://aka.ms/wac-insiders-feed` in het tekstvak in en selecteer vervolgens **Toevoegen.**

1. Nadat de feed is toegevoegd, gaat u naar het tabblad Beschikbare **extensies** en wacht u tot de lijst met extensies is bijgewerkt.

1. Selecteer in de lijst **met beschikbare extensies** **Azure IoT Edge**.

1. Installeer de extensie.

1. Wanneer de extensie is geïnstalleerd, selecteert **Windows Admin Center** in de linkerbovenhoek om naar de hoofddashboardpagina te gaan.

     De **localhost-verbinding** vertegenwoordigt de pc waarop u de Windows Admin Center.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Schermopname van de startpagina van Windows-beheerder.":::

1. Selecteer **Toevoegen**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Schermopname van het selecteren van de knop Toevoegen in Windows Admin Center.":::

1. Selecteer op Azure IoT Edge tegel Nieuwe maken **om** de installatiewizard te starten.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Schermopname van het maken van een nieuwe implementatie in de Azure IoT Edge til.":::

1. Ga door met de installatiewizard om de Licentievoorwaarden voor Microsoft-software te accepteren en selecteer vervolgens **Volgende.**

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Schermopname van het selecteren van Volgende om door te gaan met de installatiewizard.":::

1. Selecteer **Optionele diagnostische gegevens** en selecteer **vervolgens Volgende: Implementeren.** Deze selectie biedt uitgebreide diagnostische gegevens waarmee Microsoft de kwaliteit van de service kan bewaken en onderhouden.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Schermopname met de opties voor diagnostische gegevens.":::

1. Selecteer op **het scherm Doelapparaat** selecteren het gewenste doelapparaat om te controleren of het voldoet aan de minimale vereisten. Voor deze quickstart installeren we een IoT Edge op het lokale apparaat, dus kies de **localhost-verbinding.** Als het doelapparaat aan de vereisten voldoet, selecteert **u Volgende om** door te gaan.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Schermopname van de lijst Doelapparaat.":::

1. Selecteer **Volgende om** de standaardinstellingen te accepteren. Het implementatiescherm toont het proces van het downloaden van het pakket, het installeren van het pakket, het configureren van de host en het uiteindelijk instellen van de virtuele Linux-machine (VM). Een geslaagde implementatie ziet er als volgende uit:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Schermopname van een geslaagde implementatie.":::

1. Selecteer **Volgende: Maak verbinding om** door te gaan naar de laatste stap om uw Azure IoT Edge apparaat in terichten met de apparaat-id van uw IoT Hub-exemplaar.

1. Plak de connection string u eerder in deze [quickstart hebt](#register-an-iot-edge-device) gekopieerd in het **veld Connection string** apparaat. Selecteer vervolgens **Inrichten met de geselecteerde methode**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Schermopname van de connection string in het veld Connection string apparaat.":::

1. Nadat het inrichten is voltooid, selecteert **u Voltooien** om te voltooien en keert u terug naar Windows Admin Center startscherm. Als het goed is, wordt uw apparaat weergegeven als IoT Edge apparaat.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Schermopname van alle verbindingen in Windows Admin Center.":::

1. Selecteer uw Azure IoT Edge om het dashboard ervan weer te geven. U ziet dat de werkbelastingen van uw apparaat dubbel in Azure IoT Hub zijn geïmplementeerd. In **IoT Edge modulelijst moet** één module met **edgeAgent** worden weergegeven, en de **IoT Edge Status** moet actief **zijn (actief)**.

Uw IoT Edge-apparaat is nu geconfigureerd. Het is gereed voor de uitvoering van modules die in de cloud zijn geïmplementeerd.

## <a name="deploy-a-module"></a>Een module implementeren

Beheer uw Azure IoT Edge-apparaat vanuit de cloud om een module te implementeren waarmee telemetriegegevens worden verzonden naar IoT Hub.

![Diagram met de stap voor het implementeren van een module.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

Een van de belangrijkste mogelijkheden van Azure IoT Edge is het implementeren van code op uw IoT Edge apparaten vanuit de cloud. *IoT Edge-modules* zijn uitvoerbare pakketten die zijn geïmplementeerd als containers. In deze sectie implementeert u een vooraf gebouwde module uit de sectie [IoT Edge Modules van Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) rechtstreeks vanuit Azure IoT Hub.

De module die u in deze sectie implementeert, simuleert een sensor en verzendt gegenereerde gegevens. Deze module is een handig stukje code wanneer u aan de slag gaat met IoT Edge, omdat u de gesimuleerde gegevens kunt gebruiken voor ontwikkel- en testdoeleinden. Als u precies wilt zien wat deze module doet, kunt u de [broncode van de gesimuleerde temperatuursensor bekijken](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Volg deze stappen om uw eerste module te implementeren vanuit Azure Marketplace.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en ga naar uw IoT-hub.

1. Selecteer in het menu aan de linkerkant onder **Automatisch apparaatbeheer** de **optie IoT Edge.**

1. Selecteer de apparaat-id van het doelapparaat in de lijst met apparaten.

1. Selecteer op de bovenste balk **Modules instellen**.

   ![Schermopname van het selecteren van Modules instellen.](./media/quickstart/select-set-modules.png)

1. Open **IoT Edge modules** de  vervolgkeuzelijst Toevoegen en selecteer **vervolgens Marketplace-module**.

   ![Schermopname van de vervolgkeuzelijst Toevoegen.](./media/quickstart/add-marketplace-module.png)

1. Zoek **IoT Edge module marketplace** naar de module en selecteer `Simulated Temperature Sensor` deze.

   De module wordt toegevoegd aan de sectie IoT Edge Modules met de gewenste **status.**

1. Selecteer **Volgende: Routes** om door te gaan naar de volgende stap in de wizard.

   ![Schermopname van het vervolg op de volgende stap nadat de module is toegevoegd.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. Verwijder op het tabblad **Routes** de standaardroute, **route** en selecteer vervolgens **Volgende: Beoordelen en maken** om door te gaan naar de volgende stap van de wizard.

   >[!Note]
   >Routes worden samengesteld met behulp van naam- en waardeparen. Op deze pagina ziet u twee routes. De standaardroute, **route**, verzendt alle berichten naar IoT Hub (dit wordt `$upstream` genoemd). Er is automatisch een tweede route, **SimulatedTemperatureSensorToIoTHub,** gemaakt toen u de module vanuit Azure Marketplace. Deze route verzendt alle berichten van de gesimuleerde temperatuurmodule naar IoT Hub. U kunt de standaardroute verwijderen, omdat deze in dit geval overbodig is.

   ![Schermopname van het verwijderen van de standaardroute en vervolgens het verplaatsen naar de volgende stap.](./media/quickstart/delete-route-next-review-create.png)

1. Controleer het JSON-bestand en selecteer vervolgens **Maken.** Het JSON-bestand definieert alle modules die u op uw IoT Edge implementeert. U ziet de **module SimulatedTemperatureSensor** en de twee runtimemodules **edgeAgent** en **edgeHub.**

   >[!Note]
   >Wanneer u een nieuwe implementatie bij een IoT Edge-apparaat indient, wordt er niets naar uw apparaat gepusht. In plaats daarvan voert het apparaat regelmatig query’s uit naar eventuele nieuwe instructies. Als het apparaat een manifest van een bijgewerkte implementatie vindt, wordt de informatie over de nieuwe implementatie gebruikt om installatiekopieën van de module op te halen uit de cloud en wordt een lokale uitvoering van de modules gestart. Dit proces kan enkele minuten duren.

1. Nadat u de informatie over de implementatie van de module hebt gemaakt, gaat de wizard terug naar de pagina met apparaatdetails. Bekijk de implementatiestatus op het **tabblad Modules.**

   U ziet drie modules: **$edgeAgent**, **$edgeHub** en **SimulatedTemperatureSensor.** Als voor een of  meer van de modules JA is opgegeven onder OPGEGEVEN **IN** IMPLEMENTATIE, maar niet onder GERAPPORTEERD DOOR **APPARAAT,** wordt IoT Edge apparaat nog steeds met de modules. Wacht enkele minuten en vernieuw vervolgens de pagina.

   ![Schermopname van Gesimuleerde temperatuursensor in de lijst met geïmplementeerde modules.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

In deze snelstart hebt u een nieuw IoT Edge-apparaat gemaakt en de IoT Edge-runtime erop geïnstalleerd. Vervolgens hebt u de Azure Portal gebruikt om een IoT Edge-module te implementeren die op het apparaat kan worden uitgevoerd zonder dat u wijzigingen in het apparaat zelf moet aanbrengen.

De module die u hebt ge pusht, genereert voorbeeldomgevingsgegevens die u later kunt gebruiken voor het testen. De gesimuleerde sensor bewaakt zowel een machine als de omgeving rond die machine. Deze sensor kan zich bijvoorbeeld in een serverruimte, in een fabriek of op een windturbine bevinden. De berichten die worden verzonden, zijn omgevingstemperatuur en vochtigheid, machinetemperatuur en -druk en een tijdstempel. IoT Edge zelfstudies gebruiken de gegevens die door deze module zijn gemaakt als testgegevens voor analyse.

Controleer vanuit de opdrachtshell in Windows Admin Center of de module die u vanuit de cloud hebt geïmplementeerd, wordt uitgevoerd op IoT Edge apparaat.

1. Maak verbinding met het zojuist gemaakte IoT Edge apparaat.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Schermopname van het selecteren van Verbinding maken in Windows Admin Center.":::

     Op de **pagina** Overzicht ziet u de lijst IoT Edge **module en** **IoT Edge status**. U ziet de modules die zijn geïmplementeerd en de apparaatstatus.  

1. Selecteer **onder Extra** de optie **Opdrachtshell.** De opdrachtshell is een PowerShell-terminal die automatisch gebruikmaakt van Secure Shell (SSH) om verbinding te maken met de Linux-VM van uw Azure IoT Edge-apparaat op uw Windows-pc.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Schermopname van het openen van de opdrachtshell.":::

1. Voer de volgende Bash-opdracht uit om de drie modules op uw apparaat te controleren:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Schermopname met de uitvoer van de opdrachtshell I o T edge list.":::

1. Bekijk de berichten die vanaf de temperatuursensormodule naar de cloud worden verzonden.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge-opdrachten zijn zeer gevoelig wanneer ze naar modulenamen verwijzen.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Schermopname van de lijst met berichten die vanuit de module naar de cloud zijn verzonden.":::

U kunt ook de extensie [Azure IoT Hub voor Visual Studio Code gebruiken](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) om berichten te bekijken die binnenkomen bij uw IoT-hub.

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt doorgaan naar de IoT Edge zelfstudies, slaat u deze stap over. U kunt het apparaat gebruiken dat u hebt geregistreerd en ingesteld in deze quickstart. Anders kunt u de Azure-resources die u hebt gemaakt verwijderen om kosten te voorkomen.

Als u uw virtuele machine en IoT-hub in een nieuwe resourcegroep hebt gemaakt, kunt u die groep en alle bijbehorende resources verwijderen. Als u niet de hele groep wilt verwijderen, kunt u in plaats daarvan afzonderlijke resources verwijderen.

> [!IMPORTANT]
> Controleer de inhoud van de resourcegroep om te controleren of er niets is dat u wilt behouden. Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt.

Gebruik de volgende opdracht om de **groep IoTEdgeResources te** verwijderen. Het verwijderen kan enkele minuten duren.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

U kunt controleren of de resourcegroep is verwijderd met behulp van deze opdracht om de lijst met resourcegroepen weer te geven.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>De Azure IoT Edge voor Linux in Windows verwijderen

Gebruik de dashboardextensie in Windows Admin Center om de Azure IoT Edge voor Linux in Windows te verwijderen.

1. Maak verbinding met IoT Edge apparaat in Windows Admin Center. De extensie voor het Azure-dashboardhulpprogramma wordt geladen.

1. Selecteer **Verwijderen**. Nadat Azure IoT Edge is verwijderd, Windows Admin Center de verbindingsinvoer Azure IoT Edge apparaat verwijderd van de **startpagina.**

>[!Note]
>Een andere manier om de Azure IoT Edge van uw Windows-systeem te verwijderen, is door Instellingen  >    >  **starten-apps** Azure IoT Edge verwijderen op uw  >    >   apparaat IoT Edge selecteren. Met deze methode worden Azure IoT Edge van uw IoT Edge verwijderd, maar blijft de verbinding achter in Windows Admin Center. Als u het verwijderen wilt voltooien, verwijdert Windows Admin Center ook uit **het** menu Instellingen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Edge-apparaat gemaakt en de Azure IoT Edge-cloudinterface gebruikt om code te implementeren op het apparaat. U hebt nu een testapparaat dat onbewerkte gegevens over de omgeving genereert.

Stel vervolgens uw lokale ontwikkelomgeving in, zodat u kunt beginnen met het maken van IoT Edge modules die uw bedrijfslogica uitvoeren.

> [!div class="nextstepaction"]
> [Beginnen met het ontwikkelen IoT Edge modules](tutorial-develop-for-linux.md)
