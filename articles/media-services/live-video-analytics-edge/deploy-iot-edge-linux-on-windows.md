---
title: Implementeren in een IoT Edge voor Linux op Windows-Azure
description: Dit artikel bevat richt lijnen voor het implementeren van een IoT Edge voor Linux op een Windows-apparaat.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: d5c3d89ae7447b062714ad90be117a6426a39581
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561080"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Implementeren op een IoT Edge voor Linux op Windows (EFLOW)

In dit artikel leert u hoe u live video Analytics kunt implementeren op een edge-apparaat dat [IOT Edge voor Linux op Windows (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md)heeft. Zodra u klaar bent met het volgen van de stappen in dit document, kunt u een [Media grafiek](media-graph-concept.md) uitvoeren waarmee beweging in een video wordt gedetecteerd en dergelijke gebeurtenissen worden verzonden naar de IOT-hub in de Cloud. U kunt vervolgens de media grafiek voor geavanceerde scenario's uitschakelen en de kracht van live video analyse naar uw Windows-IoT Edge apparaat brengen.

## <a name="prerequisites"></a>Vereisten 

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) als u nog geen account hebt.

    > [!NOTE]
    > U hebt een Azure-abonnement met machtigingen nodig voor het maken van service-principals (de **rol van eigenaar** biedt dit). Als u niet over de juiste machtigingen beschikt, kunt u contact met uw account beheerder maken om u de juiste machtigingen te verlenen.
* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer. Zorg ervoor dat u beschikt over de [Azure IoT Tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Lees [Wat is EFLOW](../../iot-edge/iot-edge-for-linux-on-windows.md).

## <a name="deployment-steps"></a>Implementatiestappen

Hieronder ziet u de algemene stroom van het document en in vijf eenvoudige stappen die u moet uitvoeren om live video-analyses uit te voeren op een Windows-apparaat met EFLOW:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Diagram van IoT Edge voor Linux op Windows (EFLOW)":::

1. [Installeer EFLOW](../../iot-edge/how-to-install-iot-edge-on-windows.md) op uw Windows-apparaat. 

    1. Als u uw Windows-PC gebruikt, ziet u op de start pagina van het [Windows-beheer centrum](/windows-server/manage/windows-admin-center/overview) , onder de lijst met verbindingen, een lokale host waarmee de PC wordt weer geven waarop Windows-beheer centrum wordt uitgevoerd. 
    1. Eventuele extra servers, Pc's of clusters die u beheert, worden hier ook weer gegeven.
    1. U kunt Windows-beheer centrum gebruiken voor het installeren en beheren van Azure-EFLOW op uw lokale apparaat of op externe beheerde apparaten. In deze hand leiding wordt de verbinding van de lokale host geleverd als het doel apparaat voor de implementatie van Azure IoT Edge voor Linux in Windows. Daarom ziet u dat de localhost ook wordt vermeld als een IoT Edge apparaat.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Implementaties tappen-Windows-beheer centrum":::
1. Klik op het IoT Edge apparaat om er verbinding mee te maken. u ziet nu een tabblad Overzicht en opdracht shell. Op het tabblad opdracht shell kunt u opdrachten verlenen aan uw edge-apparaat.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Implementaties tappen-Azure IoT Edge Manager":::
1. Ga naar de opdracht shell en typ de volgende opdracht:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    De live video Analytics-module wordt uitgevoerd op het edge-apparaat met niet-geautoriseerde [lokale gebruikers accounts](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment). Daarnaast heeft de IT-afdeling [bepaalde lokale mappen nodig](deploy-iot-edge-device.md#granting-permissions-to-device-storage) om toepassings configuratie gegevens op te slaan. Ten slotte gebruiken we voor deze hand leiding een [RTSP-Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) die een video-feed in realtime doorstuurt naar de LVA-module voor analyse. Deze simulator neemt de invoer van vooraf opgenomen video bestanden op uit een invoer Directory. 
    
    Met het voor bereide prep-Device-script dat hiervoor wordt gebruikt, worden deze taken verwijderd, zodat u één opdracht kunt uitvoeren en alle relevante invoer-en configuratie mappen, video-invoer bestanden en gebruikers accounts met bevoegdheden probleemloos hebt gemaakt. Zodra de opdracht is voltooid, ziet u de volgende mappen die op uw edge-apparaat zijn gemaakt. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Noteer de video bestanden (*. MKV) in de map/Home/lvaedgeuser/samples/input, die fungeren als invoer bestanden die moeten worden geanalyseerd. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Bepaling":::
1. Nu u het edge-apparaat hebt ingesteld, geregistreerd op de hub en correct uitvoert met de juiste mapstructuren, is de volgende stap het instellen van de volgende extra Azure-resources en het implementeren van de LVA-module. 

    * Storage-account
    * Azure Media Services-account

    Daarom raden we u aan het [installatie script van live video Analytics-bronnen](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) te gebruiken voor het implementeren van de vereiste resources in uw Azure-abonnement. Voer hiervoor de volgende stappen uit:

    1. Open [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud shell":::
    1. Als u Cloud Shell voor het eerst gebruikt, wordt u gevraagd een abonnement te selecteren voor het maken van een opslagaccount en een Microsoft Azure-bestandsshare. Selecteer **Opslag maken** om een opslagaccount te maken voor de gegevens van uw Cloud Shell-sessie. Dit opslagaccount is gescheiden van het account dat door het script wordt gemaakt voor gebruik bij uw Azure Media Services-account.
    1. Selecteer in de vervolgkeuzelijst aan de linkerkant van het Cloud Shell-venster Bash als uw omgeving.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash omgeving":::
    1. Voer de volgende opdracht uit.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Kies **Y** wanneer u wordt gevraagd om uw eigen edge-apparaat te kiezen als IOT edge apparaat sinds u het apparaat en de IOT hub eerder hebt gemaakt. U wordt ook gevraagd om uw IoT Hub naam en IoT Edge apparaat-id. U kunt beide toegang krijgen door u aan te melden bij de Azure Portal en op uw IoT Hub te klikken en vervolgens naar de IoT Edge optie op de Blade IoT Hub portal te gaan.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Zie IoT Edge-apparaten":::

    Als u klaar bent, kunt u zich weer aanmelden bij de opdracht shell van IoT Edge apparaat en de volgende opdracht uitvoeren.
    
    `sudo iotedge list`
    
    U moet de volgende vier modules zien die zijn geïmplementeerd en worden uitgevoerd op uw edge-apparaat. Houd er rekening mee dat het script voor het maken van bronnen de LVA-module implementeert in combi natie met IoT Edge modules (edgeAgent en edgeHub) en een RTSP Simulator-module om de gesimuleerde RTSP-videofeed te bieden.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Status weer geven":::
1. Met de modules die zijn geïmplementeerd en ingesteld, bent u klaar om uw eerste LVA-media grafiek op EFLOW uit te voeren. U kunt een eenvoudige grafiek voor detectie van bewegingen uitvoeren, zoals hieronder, en de resultaten visualiseren door de volgende stappen uit te voeren:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Grafiek voor bewegings detectie":::

    1. [Configureer](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) de Azure IOT tools-extensie.
    
        > [!Note]
        > Gebruik het volgende pad: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. De topologie instellen, een grafiek instantiëren en deze activeren via deze [directe-methode aanroepen](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Bekijk de resultaten](get-started-detect-motion-emit-events-quickstart.md#observe-results) op de hub.
    1. [Opschoon methoden](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)aanroepen.
    1. Verwijder uw resources als u deze niet meer nodig hebt.

        > [!IMPORTANT]
        > Niet-verouderde resources kunnen nog steeds actief zijn en Azure-kosten in behalen.
    
## <a name="next-steps"></a>Volgende stappen

* Probeer bewegings detectie samen met het opnemen van relevante Video's in de Cloud. Volg de stappen in [detectie bewegingen detecteren, video clips opnemen om Quick Start te Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) .
* [AI uitvoeren op live video](use-your-model-quickstart.md#overview) (u kunt de vereiste instellingen overs Laan, omdat deze al eerder is uitgevoerd)
* Gebruik onze [VS code-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) om aanvullende media grafieken weer te geven.
* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera)  die RTSP ondersteunt in plaats van de RTSP-Simulator te gebruiken. U vindt IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.