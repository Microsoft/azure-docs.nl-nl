---
title: Microsoft Verbonden cache twee niveau genest Azure IoT Edge Gateway met uitgaande niet-| Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Verbonden cache twee niveau genest Azure IoT Edge Gateway met uitgaande niet-gemachtigde proxyzelfstudie
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811882"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Voorbeeld van Verbonden cache preview-implementatie van Microsoft: Twee niveau geneste Azure IoT Edge Gateway met uitgaande niet-gemachtigde proxy

In het onderstaande diagram wordt het scenario beschreven waarin de ene Azure IoT Edge gateway directe toegang heeft tot CDN-resources en als bovenliggende gateway voor een andere Azure IoT Edge gateway. De onderliggende IoT Edge gateway werkt als het bovenliggende azure IoT Leaf-apparaat, zoals een Raspberry Pi. Zowel het Azure IoT Edge onderliggende apparaat als het Azure IoT-apparaat zijn geïsoleerd van internet. In het onderstaande voorbeeld wordt de configuratie voor twee niveaus van Azure IoT Edge-gateways gedemonstreerd, maar er is geen limiet voor de diepte van upstreamhosts die door Microsoft Verbonden cache worden ondersteund. Er is geen verschil in microsoft Verbonden cache container maken van de vorige voorbeelden.

Raadpleeg de documentatie [Connect downstream IoT Edge devices - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) (Downstream-verbinding maken met Azure IoT Edge-apparaten) voor meer informatie over het configureren van gelaagde implementaties van Azure IoT Edge gateways. Houd er ook rekening mee dat bij het Azure IoT Edge, Microsoft Verbonden cache en aangepaste modules alle modules zich in hetzelfde containerregister moeten bevinden.

>[!Note]
>Bij het implementeren Azure IoT Edge, Microsoft Verbonden cache en aangepaste modules, moeten alle modules zich in hetzelfde containerregister bevinden.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft Verbonden cache Genest" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configuratie van bovenliggende gateway
1. Voeg de Microsoft Verbonden cache-module toe aan de implementatie van uw Azure IoT Edge-gatewayapparaat in Azure IoT Hub (zie Ondersteuning voor niet-verbonden apparaten voor meer informatie over het krijgen van de module). [](connected-cache-disconnected-device-update.md)
2. Voeg de omgevingsvariabelen voor de implementatie toe. Hieronder vindt u een voorbeeld van de omgevingsvariabelen.

    **Omgevingsvariabelen**

    | Name                          | Waarde                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Beschrijvingen [van omgevingsvariabelen](connected-cache-configure.md) bekijken |
    | CUSTOMER_ID                   | Beschrijvingen [van omgevingsvariabelen](connected-cache-configure.md) bekijken |
    | CUSTOMER_KEY                  | Beschrijvingen [van omgevingsvariabelen](connected-cache-configure.md) bekijken |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. Voeg de opties voor het maken van de container toe voor de implementatie. Er is geen verschil in opties voor het maken van MCC-containers uit het vorige voorbeeld. Hieronder vindt u een voorbeeld van de opties voor het maken van containers.

### <a name="container-create-options"></a>Opties voor het maken van containers

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>Configuratie van onderliggende gateway

>[!Note]
>Als u containers hebt gerepliceerd die worden gebruikt in uw configuratie in uw eigen privéregister, moet u de config.toml-instellingen en runtime-instellingen in uw module-implementatie wijzigen. Zie Verbinding maken met [downstreamapparaten](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) met IoT Edge - Azure IoT Edge meer informatie voor meer informatie.


1. Wijzig het pad naar de afbeelding voor de Edge-agent, zoals wordt gedemonstreerd in het onderstaande voorbeeld:

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Wijzig de Runtime-instellingen van de Edge Hub- en Edge-agent in Azure IoT Edge implementatie zoals in dit voorbeeld wordt gedemonstreerd:
    
    * Voer onder Edge Hub in het afbeeldingsveld in ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Voer onder Edge Agent in het afbeeldingsveld in ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Voeg de Microsoft Verbonden cache-module toe aan uw Azure IoT Edge-gatewayapparaatimplementatie in Azure IoT Hub.

   * Kies een naam voor uw module: ```ConnectedCache```
   * Wijzig de URI van de afbeelding: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Voeg dezelfde set omgevingsvariabelen en opties voor het maken van containers toe die worden gebruikt in de bovenliggende implementatie.
>[!Note]
>De CACHE_NODE_ID moet uniek zijn.  De CUSTOMER_ID en CUSTOMER_KEY zijn identiek aan de bovenliggende waarden. (Zie [Microsoft-Verbonden cache](connected-cache-configure.md)

Voer voor een validatie van goed functionerende Microsoft Verbonden cache de volgende opdracht uit in de terminal van het IoT Edge-apparaat dat als host voor de module of een apparaat in het netwerk wordt gebruikt. Vervang \<Azure IoT Edge Gateway IP\> door het IP-adres of de hostnaam van uw IoT Edge gateway. (Zie details van omgevingsvariabele voor informatie over de zichtbaarheid van dit rapport).

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```