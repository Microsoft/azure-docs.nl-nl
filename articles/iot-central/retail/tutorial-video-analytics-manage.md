---
title: 'Zelfstudie: Video bewaken met behulp van de Azure IoT Central Video Analytics-toepassingssjabloon voor object- en bewegingsdetectie'
description: In deze zelfstudie ziet u hoe u de dashboards in de Video Analytics-toepassingssjabloon voor object- en bewegingsdetectie kunt gebruiken om camera’s te beheren en video te bewaken.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: fbfef094cd062e437f2a28369162de96631ef41b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832603"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Zelfstudie: Een Video Analytics-toepassingssjabloon voor object- en bewegingsdetectie bewaken en beheren

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Voeg object- en bewegingsdetectiecamera’s toe aan een IoT Central-toepassing.
> * Beheer videostromen en speel ze af wanneer interessante gebeurtenissen worden gedetecteerd.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende voltooien:

* een van de vorige zelfstudies: [Een toepassing voor livevideoanalyse maken in Azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) of [Een videoanalyse maken in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).
* Een van de vorige zelfstudies: [Een IoT Edge-exemplaar voor Live Video Analytics maken (Linux-VM)](tutorial-video-analytics-iot-edge-vm.md) of [Een IoT Edge-exemplaar voor Live Video Analytics maken (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md).

[Docker](https://www.docker.com/products/docker-desktop) moet zijn geïnstalleerd op de lokale machine om de Video Viewer-toepassing uit te voeren.

## <a name="add-an-object-detection-camera"></a>Een objectdetectiecamera toevoegen

Ga in de IoT Central-toepassing naar het **LVA Gateway 001**-apparaat dat u eerder hebt gemaakt. Selecteer vervolgens het tabblad **Opdrachten**.

Gebruik de waarden in de volgende tabel als de parameterwaarden voor de opdracht **Camera-aanvraag toevoegen**. Bij de waarden in de tabel wordt ervan uitgegaan dat u de gesimuleerde camera op de Azure-VM gebruikt. Pas de waarden op de juiste manier aan als u een echte camera gebruikt:

| Veld| Beschrijving| Voorbeeldwaarde|
|---------|---------|---------|
| Camera-id      | Apparaat-id voor inrichting | camera-003 |
| Cameranaam    | Beschrijvende naam           | Objectdetectiecamera |
| RTSP-URL       | Adres van de stroom   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP-gebruikersnaam  |                         | gebruiker    |
| RTSP-wachtwoord  |                         | wachtwoord    |
| Detectietype | Vervolgkeuzelijst                | Objectdetectie       |

Selecteer **Uitvoeren** om de camera toe te voegen:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Camera toevoegen":::

> [!NOTE]
> De **LVA Edge Object Detector**-apparaatsjabloon bestaat al in de toepassing.

## <a name="add-a-motion-detection-camera-optional"></a>Een bewegingsdetectiecamera toevoegen (optioneel)

Als u twee camera's hebt aangesloten op uw IoT Edge-gatewayapparaat, herhaalt u de vorige stappen om een bewegingsdetectiecamera toe te voegen aan de toepassing. Gebruik andere waarden voor de parameters **Camera-id**, **Camera naam** en **RTSP-URL**.

## <a name="view-the-downstream-devices"></a>De downstreamapparaten weergeven

Selecteer het tabblad **Downstreamapparaten** voor het **LVA Gateway 001**-apparaat om de camera-apparaten te zien die u zojuist hebt toegevoegd:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Inspecteren":::

De camera-apparaten worden ook weergegeven in de lijst op de pagina **Apparaten** in de toepassing.

## <a name="configure-and-manage-the-camera"></a>De camera configureren en beheren

Ga naar het **camera-003**-apparaat en selecteer het tabblad **Beheren**.

Gebruik de standaardwaarden of wijzig de waarden als u de apparaateigenschappen wilt aanpassen:

**Camera-instellingen**

| Eigenschap | Beschrijving | Voorgestelde waarde |
|-|-|-|
| Host voor video afspelen | Host voor de Azure Media Player-viewer | http://localhost:8094 |

**Bewerkingen en diagnostische gegevens voor LVA**

| Eigenschap | Beschrijving | Voorgestelde waarde |
|-|-|-|
| Automatisch starten | De objectdetectie starten wanneer de LVA-gateway opnieuw wordt gestart | Ingeschakeld |
| Telemetrie voor foutopsporing | Gebeurtenistraceringen | Optioneel |
|Time-out voor deducties (sec.)| De hoeveelheid tijd die het kost om vast te stellen of deducties zijn gestopt | 20 |

**AI-objectdetectie**

| Eigenschap | Beschrijving | Voorgestelde waarde |
|-|-|-|
| Maximale opnametijd voor deducties (sec) | Maximale opnametijd | 15 |
| Detectieklassen | Tekenreeksen, gescheiden door komma's, met de detectietags. Bekijk de [lijst met ondersteunde tags](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) voor meer informatie | vrachtwagen, bus |
| Betrouwbaarheidsdrempel | Kwalificatiepercentage om te bepalen of de objectdetectie geldig is | 70 |
| Voorbeeldsnelheid voor deductieframes (fps) | [Beschrijving] | 2 |

Selecteer **Opslaan**.

Na enkele seconden ziet u het bevestigingsbericht **Geaccepteerd** voor elke instelling:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Objectdetectie":::

## <a name="start-lva-processing"></a>LVA-verwerking starten

Ga naar het **camera-003**-apparaat en selecteer het tabblad **Opdrachten**.

Voer de opdracht **LVA-verwerking starten** uit.

Wanneer de opdracht is voltooid, bekijkt u de opdrachtgeschiedenis om te controleren op eventuele fouten:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="Opdracht LVA-verwerking starten":::

## <a name="monitor-the-cameras"></a>De camera’s bewaken

Ga naar het **camera-003**-apparaat en selecteer het tabblad **Dashboard**:

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Cameradashboard":::

Op de tegel **Aantal detecties** wordt het gemiddelde aantal detecties weergegeven voor elk geselecteerde detectieklasseobject gedurende een detectie-interval van één seconde.

In het cirkeldiagram **Detectieklassen** ziet u het percentage detecties voor elk klassetype.

De **Video voor deductiegebeurtenis** is een lijst met koppelingen naar de assets in Azure Media Services die detecties bevatten. De koppeling gebruikt de hostspeler die in de volgende sectie wordt beschreven.

## <a name="start-the-streaming-endpoint"></a>Het streaming-eindpunt starten

Start het Media Services-eindpunt om de Media Player-toepassing van uw client in staat te stellen de opgenomen video te streamen:

* Ga in Azure Portal naar de resourcegroep **lva-rg**.
* Open de resource **Streaming-eindpunt**.
* Selecteer op de pagina **Details van streaming-eindpunt** de optie **Starten**. U ziet een waarschuwing waarin staat dat de facturering begint wanneer het eindpunt wordt gestart.

## <a name="view-stored-video"></a>Opgeslagen video weergeven

U hoeft voortaan geen camerabeelden meer te controleren op verdachte activiteiten. Met automatische tagging van gebeurtenissen en rechtstreekse koppelingen naar de opgeslagen video met de gededuceerde detectie, kunnen beveiligingsoperators interessante gebeurtenissen vinden in een lijst en vervolgens de koppeling volgen om de video weer te geven.

U kunt de [AMP-videospeler](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) gebruiken om de video te bekijken die is opgeslagen in uw Azure Media Services-account.

Met de IoT Central-toepassing wordt de video opgeslagen in Azure Media Services, vanwaaruit u deze kunt streamen. U hebt een videospeler nodig om de video af te spelen die is opgeslagen in Azure Media Services.

Zorg ervoor dat **Docker** wordt uitgevoerd op de lokale computer.

Open een opdrachtprompt en gebruik de volgende opdracht om de videospeler uit te voeren in een Docker-container op de lokale computer. Vervang de tijdelijke aanduidingen in de opdracht door de waarden die u eerder hebt genoteerd in het bestand *scratchpad.txt*. U hebt een notitie van `amsAadClientId`, `amsAadSecret`, `amsAadTenantId`, `amsSubscriptionId` en `amsAccountName` gemaakt toen u de service-principal voor uw Media Services-account maakte:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker-parameter | AMS API-toegang (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Ga naar het **camera-003**-apparaat en selecteer het tabblad **Dashboard**. Klik vervolgens op een van de hyperlinks naar de vastgelegde objectdetectie op de tegel **Video voor deductiegebeurtenis**. De video wordt vertoond op een pagina die wordt weergegeven met de lokale videospeler:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Videofragment":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>De gesimuleerde apparaten op het toepassingsdashboard wijzigen

De toepassingsdashboards zijn oorspronkelijk gevuld met telemetrie en eigenschappen die zijn gegenereerd op basis van de met IoT Central gesimuleerde apparaten. Voer deze stappen uit om de tegels te configureren in telemetrie van echte camera´s of de Live555-simulator:

1. Ga naar de het dashboard van de **(voorbeeld) Real camera monitor**-toepassing.
1. Selecteer **Bewerken**.
1. Selecteer de tegel **Notitie** en verwijder deze.
1. Wijzig de titel van het dashboard in *Real Camera Monitor*.
1. Selecteer op de tegel **Aantal deducties** het pictogram Configureren.
1. Selecteer in de sectie **Grafiek configureren** een of meer echte camera's in de apparaatgroep **LVA Edge Object Detector**.
1. Selecteer het telemetrieveld voor `AI Inference Interface/Inference Count`.
1. Selecteer **Update**.

1. Herhaal de stappen voor de volgende tegels:
    1. Het cirkeldiagram **Detectie** gebruikt het telemetrietype `AI Inference Interface/Inference/entity/tag/value`.
    1. **Deductie** gebruikt de laatste bekende waarde van `AI Inference Interface/Inference/entity/tag/value`.
    1. **Betrouwbaarheidspercentage** gebruikt de laatste bekende waarde van `AI Inference Interface/Inference/entity/tag/confidence`.
    1. **Momentopname** gebruikt `AI Inference Interface/Inference Image`, weergegeven als een afbeelding.
    1. **Video voor deductiegebeurtenis** gebruikt `AI Inference Interface/Inference Event Video`, weergegeven als een koppeling.
1. Selecteer **Opslaan**.

Het dashboard **Monitor van echte camera** geeft nu waarden weer van het echte camera-apparaat:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Toepassingsdashboard voor echte camera’s":::

## <a name="pause-processing"></a>Verwerking onderbreken

U kunt Live Video Analytics-verwerking in de toepassing onderbreken:

1. Ga in de toepassing naar de pagina **Opdrachten** voor de objectdetectiecamera. Voer vervolgens de opdracht **LVA-verwerking stoppen** uit.

1. Het streaming-eindpunt voor uw Media Services-account stoppen:
    * Ga in Azure Portal naar de resourcegroep **lva-rg**.
    * Klik op de resource **Streaming-eindpunt**.
    * Selecteer op de pagina **Details van streaming-eindpunt** de optie **Stoppen**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de toepassing, kunt u alle resources die u hebt gemaakt, als volgt verwijderen:

1. Ga in de IoT Central-toepassing naar de pagina **Uw toepassing** in de sectie **Beheer**. Selecteer vervolgens **Verwijderen**.
1. Verwijder in Azure Portal de resourcegroep **lva-rg**.
1. Stop op de lokale machine de Docker-container **amp-viewer**.

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u camera's toevoegt aan de IoT Central-toepassing en ze configureert voor object- en bewegingsdetectie.

Meer informatie over het aanpassen van de broncode voor de IoT Edge-modules:

> [!div class="nextstepaction"]
> [De gatewaymodules voor Live Video Analytics wijzigen en samenstellen](./tutorial-video-analytics-build-module.md)
