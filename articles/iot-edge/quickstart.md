---
title: Quick start voor het maken van een Azure IoT Edge apparaat in Windows | Microsoft Docs
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
ms.openlocfilehash: 6170f91d11b47a43e15488bcbb0e91ff3f7c906e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616091"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Quick Start: uw eerste IoT Edge-module implementeren op een Windows-apparaat (preview)

Probeer Azure IoT Edge in deze Snelstartgids door container code te implementeren naar een Linux op Windows IoT Edge-apparaat. Met IoT Edge kunt u code op uw apparaten op afstand beheren zodat u meer van uw workloads naar de rand kunt verzenden. Voor deze Snelstartgids kunt u het beste uw eigen apparaat gebruiken om te zien hoe eenvoudig het is om Azure IoT Edge te gebruiken voor Linux in Windows.

In deze snelstart leert u het volgende:

* Maak een IoT-hub.
* Een IoT Edge-apparaat registreren in uw IoT-hub.
* Installeer en start de IoT Edge voor Linux in Windows runtime op uw apparaat.
* Een module op afstand implementeren op een IoT Edge apparaat en telemetrie verzenden.

![Diagram waarin de architectuur van deze Snelstartgids voor uw apparaat en de Cloud wordt weer gegeven.](./media/quickstart/install-edge-full.png)

In deze snelstartgids vindt u informatie over het instellen van uw Azure IoT Edge voor Linux op een Windows-apparaat. Vervolgens implementeert u een module van de Azure Portal naar uw apparaat. De module die u gebruikt, is een gesimuleerde sensor die de Tempe ratuur, vochtigheid en druk gegevens genereert. Andere Azure IoT Edge zelf studies maken op het werk dat u hier doet door modules te implementeren waarmee de gesimuleerde gegevens voor zakelijke inzichten worden geanalyseerd.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

>[!NOTE]
>IoT Edge voor Linux in Windows is in [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Maak een resource groep voor de Cloud voor het beheren van alle resources die u in deze Quick Start gebruikt.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Zorg ervoor dat uw IoT Edge-apparaat voldoet aan de volgende vereisten:

* Edities
  * Windows 10 versie 1809 of hoger; Build 17763 of hoger
    * Professional, Enter prise, IoT Enter prise
  * Windows Server 2019 build 17763 of hoger

  
* Hardwarevereisten
  * Mini maal beschikbaar geheugen: 2 GB
  * Minimale vrije schijf ruimte: 10 GB


>[!NOTE]
>Deze Snelstartgids maakt gebruik van Windows-beheer centrum voor het maken van een implementatie van IoT Edge voor Linux in Windows. U kunt ook Power shell gebruiken. Als u Power shell wilt gebruiken om uw implementatie te maken, volgt u de stappen in de hand leiding voor het [installeren en inrichten van Azure IOT Edge voor Linux op een Windows-apparaat](how-to-install-iot-edge-on-windows.md).

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

Begin met het maken van een IoT-hub met de Azure CLI.

![Diagram waarin de stap wordt weer gegeven voor het maken van een I o T-hub.](./media/quickstart/create-iot-hub.png)

Het gratis niveau van Azure IoT Hub werkt voor deze Quick Start. Als u in het verleden IoT Hub hebt gebruikt en al een hub hebt gemaakt, kunt u die IoT-hub gebruiken.

Met de volgende code wordt een gratis **F1**-hub gemaakt in de resourcegroep `IoTEdgeResources`. Vervang `{hub_name}` door een unieke naam voor uw IoT-hub. Het kan enkele minuten duren voordat een IoT-hub is gemaakt.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Als er een fout optreedt omdat u al één gratis hub in uw abonnement hebt, wijzigt u de SKU in `S1` . Als er een fout bericht wordt weer geven dat de naam van de IoT-hub niet beschikbaar is, heeft iemand anders al een hub met die naam. Probeer een andere naam.

## <a name="register-an-iot-edge-device"></a>Een IoT Edge-apparaat registreren

Registreer een IoT Edge-apparaat bij uw net gemaakte IoT Hub.

![Diagram waarin de stap wordt weer gegeven voor het registreren van een apparaat met een IoT hub-identiteit.](./media/quickstart/register-device.png)

Maak een apparaat-id voor uw gesimuleerde apparaat, zodat het met uw IoT-hub kan communiceren. De apparaat-id is opgeslagen in de cloud, en u gebruikt een unieke apparaatverbindingsreeks om een fysiek apparaat te koppelen aan een apparaat-id.

IoT Edge-apparaten gedragen zich en kunnen anders worden beheerd dan typische IoT-apparaten. Gebruik de `--edge-enabled` markering om te declareren dat deze identiteit voor een IOT edge apparaat is.

1. Voer in Azure Cloud Shell de volgende opdracht in om een apparaat met de naam **myEdgeDevice** in uw hub te maken.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Als er een fout bericht over `iothubowner` beleids sleutels wordt weer geven, moet u ervoor zorgen dat Cloud shell de nieuwste versie van de Azure IOT-extensie uitvoert.

1. Bekijk de verbindingsreeks voor uw apparaat. Hiermee wordt uw fysieke apparaat aan de bijbehorende identiteit in IoT Hub gekoppeld. Het bevat de naam van uw IoT-hub, de naam van uw apparaat en een gedeelde sleutel waarmee verbindingen tussen de twee worden geverifieerd.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Kopieer de waarde van de sleutel `connectionString` uit de JSON-uitvoer en sla deze op. Deze waarde is de verbindingsreeks van het apparaat. U gebruikt deze voor het configureren van de IoT Edge runtime in de volgende sectie.

     ![Scherm opname van de uitvoer van de Connections Tring in Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren en starten

Installeer IoT Edge voor Linux in Windows op uw apparaat en configureer dit met het apparaat connection string.

![Diagram waarin de stap voor het starten van de IoT Edge-runtime wordt weer gegeven.](./media/quickstart/start-runtime.png)

1. [Down load Windows-beheer centrum](https://aka.ms/wacdownload).

1. Volg de aanwijzingen in de installatie wizard om Windows-beheer centrum op uw apparaat in te stellen.

1. Open Windows-beheer centrum.

1. Selecteer het **tandwiel pictogram instellingen** in de rechter bovenhoek en selecteer vervolgens **uitbrei dingen**.

1. Selecteer **toevoegen** op het tabblad **feeds** .

1. Voer `https://aka.ms/wac-insiders-feed` in het tekstvak in en selecteer vervolgens **toevoegen**.

1. Nadat de feed is toegevoegd, gaat u naar het tabblad **beschik bare uitbrei dingen** en wacht u totdat de lijst met extensies is bijgewerkt.

1. Selecteer **Azure IOT Edge** in de lijst met **beschik bare uitbrei dingen**.

1. Installeer de extensie.

1. Wanneer de uitbrei ding is geïnstalleerd, selecteert u in de linkerbovenhoek van **Windows-beheer centrum** om naar de hoofd pagina van het dash board te gaan.

     De **localhost** -verbinding vertegenwoordigt de pc waarop u Windows-beheer centrum uitvoert.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Scherm opname van de start pagina van de Windows-beheerder.":::

1. Selecteer **Toevoegen**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Scherm opname van het selecteren van de knop toevoegen in het Windows-beheer centrum.":::

1. Selecteer op de tegel Azure IoT Edge de optie **nieuwe maken** om de installatie wizard te starten.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Scherm opname van het maken van een nieuwe implementatie in de Azure IoT Edge til.":::

1. Ga door met de installatie wizard om de licentie voorwaarden voor micro soft-software te accepteren en selecteer **volgende**.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Scherm afbeelding met de optie volgende om door te gaan met de installatie wizard.":::

1. Selecteer **optionele diagnostische gegevens** en selecteer vervolgens **volgende: implementeren**. Deze selectie biedt uitgebreide diagnostische gegevens waarmee micro soft de kwaliteit van de service kan controleren en onderhouden.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Scherm opname van de opties voor diagnostische gegevens.":::

1. Selecteer op het scherm **doel apparaat selecteren** het gewenste doel apparaat om te valideren dat het voldoet aan de minimale vereisten. Voor deze Quick Start installeren we IoT Edge op het lokale apparaat. Kies daarom de **localhost** -verbinding. Als het doel apparaat aan de vereisten voldoet, selecteert u **volgende** om door te gaan.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Scherm opname van de lijst met doel apparaten.":::

1. Selecteer **volgende** om de standaard instellingen te accepteren. Het scherm implementatie toont het proces van het downloaden van het pakket, het installeren van het pakket, het configureren van de host en de laatste installatie van de virtuele Linux-machine (VM). Een geslaagde implementatie ziet er als volgt uit:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Scherm opname van een geslaagde implementatie.":::

1. Selecteer **volgende: verbinding maken** om door te gaan naar de laatste stap om uw Azure IOT edge apparaat in te richten met de apparaat-id van de IOT hub-instantie.

1. Plak de connection string die u [eerder in deze Quick](#register-an-iot-edge-device) start hebt gekopieerd naar het veld **apparaat Connection String** . Selecteer vervolgens **inrichten met de geselecteerde methode**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Scherm opname van de connection string in het veld apparaat connection string.":::

1. Nadat het inrichten is voltooid, selecteert u **volt** ooien om te volt ooien en gaat u terug naar het Start scherm van Windows-beheer centrum. Het apparaat wordt weer gegeven als een IoT Edge apparaat.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Scherm opname van alle verbindingen in het Windows-beheer centrum.":::

1. Selecteer uw Azure IoT Edge-apparaat om het dash board ervan weer te geven. U ziet dat de werk belastingen van uw apparaat tussen Azure IoT Hub zijn geïmplementeerd. In de **lijst IOT Edge module** moet een module met **edgeAgent** worden weer gegeven en de **IOT Edge status** moet **actief zijn (uitgevoerd)**.

Uw IoT Edge-apparaat is nu geconfigureerd. Het is gereed voor de uitvoering van modules die in de cloud zijn geïmplementeerd.

## <a name="deploy-a-module"></a>Een module implementeren

Beheer uw Azure IoT Edge-apparaat vanuit de cloud om een module te implementeren waarmee telemetriegegevens worden verzonden naar IoT Hub.

![Diagram waarin de stap voor het implementeren van een module wordt weer gegeven.](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

In deze snelstart hebt u een nieuw IoT Edge-apparaat gemaakt en de IoT Edge-runtime erop geïnstalleerd. Vervolgens hebt u de Azure Portal gebruikt om een IoT Edge module te implementeren die op het apparaat moet worden uitgevoerd zonder dat het apparaat zelf moet worden gewijzigd.

De module die u hebt gepusht, genereert voorbeeld omgevings gegevens die u kunt gebruiken om later te testen. De gesimuleerde sensor bewaakt zowel een machine als de omgeving rond die machine. Deze sensor kan zich bijvoorbeeld in een serverruimte, in een fabriek of op een windturbine bevinden. De berichten die worden verzonden, zijn omgevings temperatuur en vochtigheid, computer temperatuur en druk en een tijds tempel. IoT Edge zelf studies gebruiken de gegevens die door deze module zijn gemaakt als test gegevens voor analyse.

Controleer in de opdracht shell in het Windows-beheer centrum of de module die u hebt geïmplementeerd vanuit de Cloud wordt uitgevoerd op uw IoT Edge-apparaat.

1. Maak verbinding met uw nieuw gemaakte IoT Edge-apparaat.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Scherm opname van het selecteren van verbinding maken in het Windows-beheer centrum.":::

     Op de pagina **overzicht** ziet u de **lijst met IOT Edge modules** en de **IOT Edge status**. U kunt de geïmplementeerde modules en de apparaatstatus bekijken.  

1. Onder **extra** selecteert u **opdracht shell**. De opdracht shell is een Power shell-terminal die automatisch gebruikmaakt van Secure Shell (SSH) om verbinding te maken met de Linux-VM van uw Azure IoT Edge apparaat op uw Windows-PC.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Scherm afbeelding waarop de opdracht shell wordt geopend.":::

1. Als u de drie modules op uw apparaat wilt controleren, voert u de volgende bash-opdracht uit:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Scherm afbeelding van de uitvoer van de opdracht shell-rand lijst.":::

1. Bekijk de berichten die vanaf de temperatuursensormodule naar de cloud worden verzonden.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge opdrachten zijn hoofdletter gevoelig wanneer ze verwijzen naar module namen.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Scherm opname van de lijst met berichten die vanuit de module naar de cloud worden verzonden.":::

U kunt ook de [Azure IOT hub-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) gebruiken om berichten te bekijken die binnenkomen op uw IOT-hub.

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt door gaan met de IoT Edge zelf studies, slaat u deze stap over. U kunt het apparaat gebruiken dat u in deze Quick Start hebt geregistreerd en ingesteld. Anders kunt u de Azure-resources die u hebt gemaakt verwijderen om kosten te voorkomen.

Als u uw virtuele machine en IoT-hub in een nieuwe resourcegroep hebt gemaakt, kunt u die groep en alle bijbehorende resources verwijderen. Als u niet de hele groep wilt verwijderen, kunt u in plaats daarvan afzonderlijke resources verwijderen.

> [!IMPORTANT]
> Controleer de inhoud van de resource groep om er zeker van te zijn dat er niets is dat u wilt blijven gebruiken. Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt.

Gebruik de volgende opdracht om de groep **IoTEdgeResources** te verwijderen. Het verwijderen kan enkele minuten duren.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

U kunt controleren of de resource groep is verwijderd met behulp van deze opdracht om de lijst met resource groepen weer te geven.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge voor Linux op Windows verwijderen

Gebruik de extensie dash board in Windows-beheer centrum om Azure IoT Edge voor Linux te verwijderen in Windows.

1. Maak verbinding met het IoT Edge apparaat in het Windows-beheer centrum. De extensie van het Azure dash board-hulp programma wordt geladen.

1. Selecteer **Verwijderen**. Nadat Azure IoT Edge is verwijderd, verwijdert Windows-beheer centrum de vermelding van het Azure IoT Edge apparaat van de **Start** pagina.

>[!Note]
>Een andere manier om Azure IOT Edge van uw Windows-systeem te verwijderen, is door apps voor **Start**  >  **instellingen** te selecteren  >    >  **Azure IOT Edge**  >  op uw IOT edge apparaat te **verwijderen** . Met deze methode wordt Azure IoT Edge van uw IoT Edge apparaat verwijderd, maar blijft de verbinding achter in het Windows-beheer centrum. Als u het verwijderen wilt volt ooien, verwijdert u ook Windows-beheer centrum uit het menu **instellingen** .

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Edge-apparaat gemaakt en de Azure IoT Edge-cloudinterface gebruikt om code te implementeren op het apparaat. U hebt nu een test apparaat waarmee onbewerkte gegevens over de omgeving worden gegenereerd.

Stel vervolgens uw lokale ontwikkel omgeving in, zodat u kunt beginnen met het maken van IoT Edge-modules die uw bedrijfs logica uitvoeren.

> [!div class="nextstepaction"]
> [Beginnen met het ontwikkelen van IoT Edge modules](tutorial-develop-for-linux.md)
