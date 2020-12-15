---
title: 'Zelfstudie: een toepassing Videoanalyse: object- en bewegingsdetectie maken in Azure IoT Central (OpenVINO)'
description: In deze zelfstudie leert u hoe u een videoanalysetoepassing maakt in IoT Central. U maakt de toepassing, past deze aan en verbindt deze met andere Azure-services. In deze zelfstudie wordt gebruikgemaakt van de toolkit Intel OpenVINO&trade; voor objectdetectie in realtime.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: fbe1e84525eed47127a08abc9fb7ec5d1144d02f
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763605"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>Zelfstudie: Een toepassing Videoanalyse: object- en bewegingsdetectie maken in Azure IoT Central (OpenVINO&trade;)

Als ontwikkelaar van oplossingen leert u hoe u een videoanalysetoepassing maakt met de IoT Central-toepassingssjabloon *Videoanalyse: object- en bewegingsdetectie*, Azure IoT Edge-apparaten, Azure Media Services en het voor hardware geoptimaliseerde OpenVINO&trade; van Intel voor object- en bewegingsdetectie. Voor de oplossing wordt een detailhandel gebruikt om te laten zien hoe u voldoet aan de algemene bedrijfsbehoefte om beveiligingscamera’s te bewaken. De oplossing maakt gebruik van automatische objectdetectie in een videofeed om snel interessante gebeurtenissen te identificeren en zoeken.

> [!TIP]
> Als u YOLO v3 wilt gebruiken in plaats van OpenVINO&trade; voor object- en bewegingsdetectie, raadpleegt u [Zelfstudie: Een toepassing Videoanalyse: object- en bewegingsdetectie maken in Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt): dit bestand helpt u bij het vastleggen van de verschillende configuratieopties die u nodig hebt tijdens deze zelfstudies.
- [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json): u hoeft alleen dit bestand te downloaden als u het Intel NUC-apparaat in de tweede zelfstudie wilt gebruiken.

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Het implementatiemanifest bewerken

U implementeert een IoT Edge-module met behulp van een implementatiemanifest. In IoT Central kunt u het manifest als een apparaatsjabloon importeren en vervolgens IoT Central de module-implementatie laten beheren.

Het implementatiemanifest voorbereiden:

1. Open het bestand *deployment.openvino.amd64.json*, dat u in de map *lva-configuration* hebt opgeslagen, in een teksteditor.

1. Zoek de instellingen voor `LvaEdgeGatewayModule` en controleer of de afbeeldingsnaam gelijk is aan de naam in het volgende fragment:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Voeg de naam van uw Media Services-account toe aan het `env`-knooppunt in de sectie `LvaEdgeGatewayModule`. U hebt de naam van uw Media Services-account in het bestand *scratchpad.txt* opgeschreven:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. De sjabloon stelt deze eigenschappen niet beschikbaar in IoT Central, dus u moet de Media Services-configuratiewaarden aan het implementatiemanifest toevoegen. Zoek de `lvaEdge`-module en vervang de tijdelijke aanduidingen door de waarden die u in het bestand *scratchpad.txt* had genoteerd toen u uw Media Services-account maakte.

    De `azureMediaServicesArmId` is de **Resource-id** die u in het bestand *scratchpad.txt* had genoteerd toen u het Media Services-account maakte.

    In de volgende tabel ziet u de waarden uit de **Verbinding maken met Media Services API (JSON)** in het bestand *scratchpad.txt* dat u moet gebruiken in het implementatiemanifest:

    | Distributiemanifest       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Gebruik de vorige tabel om ervoor te zorgen dat u de juiste waarden toevoegt in het implementatiemanifest, anders werkt het apparaat niet.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Sla de wijzigingen op.

In deze zelfstudie wordt uw oplossing geconfigureerd voor gebruik van de OpenVINO&trade;-module voor object- en bewegingsdetectie. In het volgende codefragment wordt de configuratie van de module getoond:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Het manifest vervangen

Selecteer op de pagina **LVA Edge Gateway v2** de optie **+ Manifest vervangen**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="Manifest vervangen":::

Ga naar de map *lva-configuration* en selecteer het manifestbestand *deployment.openvino.amd64.json*, dat u eerder hebt bewerkt. Selecteer **Uploaden**. Wanneer de validatie is voltooid, selecteert u **Vervangen**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
