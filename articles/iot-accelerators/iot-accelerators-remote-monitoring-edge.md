---
title: 'Zelfstudie: afwijkingen aan de rand detecteren in een oplossing - Azure | Microsoft Docs'
description: In deze zelfstudie leert u hoe u uw IoT Edge-apparaten kunt bewaken met behulp van de verbetering voor de externe bewakingsoplossing.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e234cc29589e7b104ea7ef54ab7493e27d11cf27
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460425"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Zelfstudie: Afwijkingen aan de rand detecteren met de verbetering voor de externe bewakingsoplossing

In deze zelfstudie configureert u de externe bewakingsoplossing om te reageren op afwijkingen die worden gedetecteerd door een IoT Edge-apparaat. Met IoT Edge-apparaten kunt u telemetrie aan de rand verwerken om de hoeveelheid telemetrie te verminderen die naar de oplossing wordt verzonden en om sneller te kunnen reageren op gebeurtenissen op apparaten. Voor meer informatie over de voordelen van verwerking aan de rand raadpleegt u [Wat is Azure IoT Edge](../iot-edge/about-iot-edge.md).

Deze zelfstudie gebruikt een gesimuleerde jaknikker om u te laten kennismaken met randverwerking met externe bewaking. De jaknikker wordt beheerd door een organisatie met de naam Contoso en is verbonden met de verbetering voor de externe bewakingsoplossing. Sensoren op de jaknikker meten temperatuur en druk. Operators bij Contoso weten dat een abnormale toename in de temperatuur ervoor kan zorgen dat de jaknikker vertraagt. Operators bij Contoso hoeven de temperatuur van het apparaat niet te bewaken wanneer deze binnen het normale bereik ligt.

Contoso wil een intelligente randmodule voor de jaknikker implementeren waarmee afwijkingen in de temperatuur worden gedetecteerd. Een andere randmodule stuurt waarschuwingen naar de externe bewakingsoplossing. Wanneer een waarschuwing wordt ontvangen, kan een Contoso-operator een onderhoudstechnicus sturen. Contoso kan ook een geautomatiseerde actie configureren, zoals het verzenden van een e-mailbericht, die moet worden uitgevoerd wanneer de oplossing een waarschuwing ontvangt.

In het volgende diagram worden de belangrijke onderdelen in het zelfstudiescenario weergegeven:

![Diagram toont de oliepompconnector die is verbonden met de module IoT Edge-stroomanalyse in het IoT Edge-apparaat voor telemetrie en opdrachten. Gefilterde telemetrie gaat naar het IoT Edge-apparaat in de oplossingsverbetering voor externe bewaking in de cloud. De cloud bevat ook Implementatie en Pakket. Implementatie implementeert de IoT Edge-runtime in het apparaat.](media/iot-accelerators-remote-monitoring-edge/overview.png)

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * Een IoT Edge-apparaat toevoegen aan de oplossing
> * Een Edge-manifest maken
> * Een manifest importeren als pakket dat de modules definieert die op het apparaat moeten worden uitgevoerd
> * Het pakket implementeren op uw IoT Edge-apparaat
> * Waarschuwingen van het apparaten weergeven

Op het Azure IoT Edge-apparaat gebeurt het volgende:

* De runtime ontvangt het pakket en installeert de modules.
* De module die de gegevensstroom analyseert, detecteert temperatuurafwijkingen in de pomp en verzendt opdrachten om het probleem op te lossen.
* De module die de gegevensstroom analyseert, stuurt gefilterde gegevens door naar de oplossingsverbetering.

In deze zelfstudie wordt een Linux-machine als een IoT Edge-apparaat gebruikt. U gaat ook een Edge-module installeren om de jaknikker te simuleren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Een IoT Edge-apparaat toevoegen

Er zijn twee stappen voor het toevoegen van een IoT Edge-apparaat aan de verbetering voor de externe bewakingsoplossing. In deze sectie leest u hoe u ze gebruikt:

* Een IoT Edge-apparaat toevoegen op de pagina **Device Explorer** in de webgebruikersinterface van externe bewaking.
* De IoT Edge-runtime installeren op een Linux-VM.

### <a name="add-an-iot-edge-device-to-your-solution"></a>Een IoT Edge-apparaat toevoegen aan uw oplossing

Als u een IoT Edge-apparaat wilt toevoegen aan de verbetering voor de externe bewakingsoplossing, gaat u naar de pagina **Device Explorer** in de webgebruikersinterface en klikt u op **+ Nieuw apparaat**.

In het deelvenster **Nieuw apparaat** kiest u **IoT Edge-apparaat** en voert u **oil-pump** in als de apparaat-id. Voor de overige instellingen kunt u de standaardwaarden gebruiken. Klik vervolgens op **Toepassen**:

[![IoT Edge-apparaat toevoegen](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Noteer de apparaatverbindingsreeks. U hebt deze nodig in de volgende sectie van de zelfstudie.

Wanneer u een apparaat registreert bij de IoT-hub in de verbetering voor de externe bewakingsoplossing, wordt het apparaat weergegeven op de pagina **Device Explorer** in de webgebruikersinterface:

[![Nieuw IoT Edge-apparaat](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Om het beheer van de IoT Edge-apparaten in de oplossing te vergemakkelijken, maakt u een apparaatgroep en voegt u daaraan het IoT Edge-apparaat toe:

1. Selecteer het apparaat **oil-pump** in de lijst op de pagina **Device Explorer** en klik op **Taken**.

1. Maak een taak om de tag **IsEdge** aan het apparaat toe te voegen met behulp van de volgende instellingen:

    | Instelling | Waarde |
    | ------- | ----- |
    | Taak     | Tags  |
    | Taaknaam | AddEdgeTag |
    | Sleutel     | IsOilPump |
    | Waarde   | J     |
    | Type    | Tekst  |

    [![Tag toevoegen](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Klik op **Toepassen** en op **Sluiten**.

1. Klik op de pagina **Device Explorer** op **Apparaatgroepen beheren**.

1. Klik op **Nieuwe apparaatgroep maken**. Maak een nieuwe apparaatgroep met de volgende instellingen:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam    | OilPumps |
    | Veld   | Tags.IsOilPump |
    | Operator | = Is gelijk aan |
    | Waarde    | J |
    | Type     | Tekst |

    [![Apparaatgroep maken](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Klik op **Opslaan**.

Het IoT Edge-apparaat staat nu in de groep **OilPumps**.

### <a name="install-the-edge-runtime"></a>De Edge-runtime installeren

Een Edge-apparaat vereist dat de Edge-runtime is geïnstalleerd. In deze zelfstudie installeert u de Edge-runtime in een virtuele Linux-machine in Azure om het scenario te testen. In de volgende stappen wordt de Azure Cloud Shell gebruikt om de virtuele machine te installeren en te configureren:

1. Voer de volgende opdrachten uit om een virtuele Linux-machine in Azure te maken. U kunt een locatie bij u in de buurt gebruiken:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Voor het configureren van de Edge-runtime met de apparaatverbindingsreeks, voert u de volgende opdracht uit met behulp van de apparaatverbindingsreeks die u eerder hebt genoteerd:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Zorg dat u de verbindingsreeks tussen dubbele aanhalingstekens plaatst.

U hebt nu de IoT Edge-runtime geïnstalleerd en geconfigureerd op een Linux-apparaat. Verderop in deze zelfstudie gebruikt u de externe bewakingsoplossing om IoT Edge-modules te implementeren op dit apparaat.

## <a name="create-an-edge-manifest"></a>Een Edge-manifest maken

Om de jaknikker te simuleren, moet u de volgende modules toevoegen aan uw Edge-apparaat:

* Temperatuursimulatiemodule;
* Anomaliedetectie in Azure Stream Analytics.

De volgende stappen laten zien hoe u een Edge-implementatiemanifest maakt dat deze modules bevat. Verderop in deze zelfstudie importeert u dit manifest als een pakket in de verbetering voor de externe bewakingsoplossing.

### <a name="create-the-azure-stream-analytics-job"></a>De Azure Stream Analytics-taak maken

U definieert de Stream Analytics-taak in de portal voordat u deze verpakt als een Edge-module.

1. Maak in de Azure-portal een Azure-opslagaccount met behulp van de standaardopties in de resourcegroep **IoTEdgeDevices**. Noteer de naam die u hebt gekozen.

1. Maak in de Azure-portal een **Stream Analytics-taak** in de resourcegroep **IoTEdgeDevices**. Gebruik de volgende configuratiewaarden:

    | Optie | Waarde |
    | ------ | ----- |
    | Taaknaam | EdgeDeviceJob |
    | Abonnement | Uw Azure-abonnement |
    | Resourcegroep | IoTEdgeDevices |
    | Locatie | VS - oost |
    | Hostingomgeving | Edge |
    | Streaming-eenheden | 1 |

1. Open de Stream Analytics-taak **EdgeDeviceJob** in de portal. Klik op Invoer en voeg een **Edge Hub**-invoerstroom toe met de naam **telemetry**.

1. Klik in de Stream Analytics-taak **EdgeDeviceJob** in de portal op **Uitvoer** en voeg een **Edge Hub**-uitvoer toe met de naam **output**.

1. Klik in de Stream Analytics-taak **EdgeDeviceJob** in de portal op **Uitvoer** en voeg een tweede **Edge Hub**-uitvoer toe met de naam **alert**.

1. Klik in de Stream Analytics-taak **EdgeDeviceJob** in de portal op **Query** en voeg de volgende **select**-instructie toe:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. Klik in de Stream Analytics-taak **EdgeDeviceJob** in de portal op **Opslagaccountinstellingen**. Voeg het opslagaccount dat u aan de resourcegroep **IoTEdgeDevices** hebt toegevoegd toe als het begin van deze sectie. Maak een nieuwe container met de naam **edgeconfig**.

In de volgende schermafbeelding ziet u de opgeslagen Stream Analytics-taak:

[![Stream Analytics-taak](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

U hebt nu een Stream Analytics-taak gedefinieerd die wordt gevoerd op uw Edge-apparaat. De taak berekent een gemiddelde temperatuur binnen een venster van 5 seconden. De taak verzendt ook een waarschuwing als de gemiddelde temperatuur in een venster van 3 seconden hoger is dan 400.

### <a name="create-the-iot-edge-deployment"></a>De IoT Edge-implementatie maken

Vervolgens maakt u een IoT Edge-implementatiemanifest dat de modules definieert die op uw Edge-apparaat moeten worden uitgevoerd. In de volgende sectie importeert u dit manifest als een pakket in de verbetering voor de externe bewakingsoplossing.

1. Navigeer in de Azure-portal naar de IoT Hub in uw externe bewakingsoplossing. U vindt de IoT Hub in de resourcegroep met dezelfde naam als uw externe bewakingsoplossing.

1. Klik in de IoT Hub op **IoT Edge** in de sectie **Automatisch apparaatbeheer**. Klik op **Een IoT Edge-implementatie toevoegen**.

1. Voer op de pagina **Implementatie maken > Naam en Label** de naam **oil-pump-device** in. Klik op **Volgende**.

1. Klik op de pagina **Implementatie maken > Modules toevoegen** op **+ Toevoegen**. Kies **IoT Edge-module**.

1. Voer in het deelvenster **Aangepaste IoT Edge-modules****temperatureSensor** in als de naam en **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** als de URI van de installatiekopie. Klik op **Opslaan**.

1. Klik op de pagina **Implementatie maken > Modules toevoegen** op **+ Toevoegen** om nog een module toe te voegen. Kies **Azure Stream Analytics-module**.

1. Selecteer in het deelvenster **Edge-implementatie** uw abonnement en de **EdgeDeviceJob** die u in de vorige sectie hebt gemaakt. Klik op **Opslaan**.

1. Klik op de pagina **Implementatie maken > Modules toevoegen** op **Volgende**.

1. Voeg op de pagina **Implementatie maken > Routes opgeven** de volgende code toe:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Deze code leidt de uitvoer van de Stream Analytics-module om naar de juiste locaties.

    Klik op **Volgende**.

1. Klik op de pagina **Implementatie maken > Metrische gegevens opgeven** op **Volgende**.

1. Voer op de pagina **Implementatie maken > Doelapparaten** 10 in als de prioriteit. Klik op **Volgende**.

1. Klik op de pagina **Implementatie maken > Implementatie controleren** op **Indienen**:

    [![Implementatie controleren](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Klik op de hoofdpagina van **IoT Edge** op **IoT Edge-implementaties**. U ziet **oil-pump-device** in de lijst met implementaties staan.

1. Klik op de implementatie **oil-pump-device** en vervolgens op **IoT Edge-manifest downloaden**. Sla het bestand op als **oil-pump-device.json** op een geschikte locatie op uw lokale computer. U hebt dit bestand nodig in de volgende sectie van deze zelfstudie.

U hebt nu een IoT Edge-manifest gemaakt dat u als een pakket kunt importeren in de externe bewakingsoplossing. Gewoonlijk maakt een ontwikkelaar de IoT Edge-modules en het manifestbestand.

## <a name="import-a-package"></a>Een pakket importeren

In deze sectie importeert u het Edge-manifest als een pakket in de verbetering voor de externe bewakingsoplossing.

1. Navigeer in de webgebruikersinterface van Externe bewaking naar de pagina **Pakketten** en klik op **+ Nieuw pakket**:

    [![Nieuw pakket](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Kies in het deelvenster **Nieuw pakket****Edge-manifest** als het pakkettype. Klik op **Bladeren** om het bestand **oil-pump-device.json** op uw lokale computer te zoeken en klik op **Uploaden**:

    [![Pakket uploaden](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    De lijst met pakketten bevat nu het pakket **oil-pump-device.json**.

In de volgende sectie maakt u een implementatie die het pakket toepast op uw Edge-apparaat.

## <a name="deploy-a-package"></a>Een pakket implementeren

Nu kunt u het pakket implementeren op uw apparaat.

1. Navigeer in de webgebruikersinterface van Externe bewaking naar de pagina **Implementaties** en klik op **+ Nieuwe implementatie**:

    [![Nieuwe implementatie](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Maak in het deelvenster **Nieuwe implementatie** een implementatie met de volgende instellingen:

    | Optie | Waarde |
    | ------ | ----- |
    | Naam   | OilPumpDevices |
    | Pakkettype | Edge-manifest |
    | Pakket | oil-pump-device.json |
    | Apparaatgroep | OilPumps |
    | Prioriteit | 10 |

    [![Implementatie maken](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Klik op **Toepassen**.

U moet enkele minuten wachten voordat het pakket op uw apparaat is geïmplementeerd en telemetrie van het apparaat begint te stromen.

[![Implementatie actief](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

De pagina **Implementaties** bevat de volgende metrische gegevens:

* **Gericht** toont het aantal apparaten in de groep met apparaten.
* **Toegepast** toont het aantal apparaten waarop de implementatie-inhoud is toegepast.
* **Geslaagd** bevat het aantal Edge-apparaten in de implementatie waarvoor de status Geslaagd is gemeld vanuit de runtime van de IoT Edge-client.
* **Mislukt** bevat het aantal Edge-apparaten in de implementatie waarvoor de status Mislukt is gemeld vanuit de runtime van de IoT Edge-client.

## <a name="monitor-the-device"></a>Het apparaat controleren

U kunt de temperatuurtelemetrie van de jaknikker bekijken in de webgebruikersinterface van Externe bewaking:

1. Ga naar de pagina **Device Explorer** en selecteer de jaknikker.
1. Klik in de sectie **Telemetrie** van het deelvenster **Apparaatdetails** op **Temperatuur**:

    [![Telemetrie bekijken](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

U kunt zien hoe de temperatuur oploopt totdat deze een drempelwaarde bereikt. De Stream Analytics Edge-module detecteert wanneer de temperatuur deze drempelwaarde bereikt en verzendt een opdracht naar het apparaat om de temperatuur onmiddellijk te verlagen. Deze verwerking vindt geheel plaats op het apparaat, zonder te communiceren met de cloud.

Als u operators wilt waarschuwen wanneer de drempelwaarde is bereikt, kunt u een regel maken in de webgebruikersinterface van Externe controle:

1. Navigeer naar de pagina **Regels** en klik op **+ Nieuwe regel**.
1. Maak een nieuwe regel met de volgende instellingen:

    | Optie | Waarde |
    | ------ | ----- |
    | Regelnaam | Oliepomptemperatuur |
    | Beschrijving | Temperatuur van oliepomp hoger dan 300 |
    | Apparaatgroep | OilPumps |
    | Berekening | Direct |
    | Veld | temperatuur |
    | Operator | > |
    | Waarde | 300 |
    | Ernstniveau | Info |

    [![Regel maken](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Klik op **Toepassen**.

1. Navigeer naar de pagina **Dashboard**. Er wordt een waarschuwing in het deelvenster **Waarschuwingen** weergegeven wanneer de temperatuur in het apparaat **oil-pump** hoger is dan 300.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd u hoe u een IoT Edge-apparaat toevoegt en configureert in de verbetering voor de externe bewakingsoplossing. Zie de volgende instructiegids voor meer informatie over het werken met IoT Edge-pakketten in de externe bewakingsoplossing:

> [!div class="nextstepaction"]
> [Een IoT Edge-pakket importeren in de verbetering voor de externe bewakingsoplossing](iot-accelerators-remote-monitoring-import-edge-package.md)

Zie [De Azure IoT Edge-runtime installeren in Linux (x64)](../iot-edge/how-to-install-iot-edge.md) voor meer informatie over het installeren van de IoT Edge-runtime.

Zie [Azure Stream Analytics implementeren als een IoT Edge-module](../iot-edge/tutorial-deploy-stream-analytics.md) voor meer informatie over Azure Stream Analytics op Edge-apparaten.