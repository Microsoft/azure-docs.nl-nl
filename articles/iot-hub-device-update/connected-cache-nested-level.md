---
title: Door micro soft verbonden cache op twee niveaus geneste Azure IoT Edge gateway met uitgaande niet-geverifieerde proxy | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Micro soft Connected cache op twee niveaus geneste Azure IoT Edge gateway met uitgaand niet-geverifieerde proxy zelf studie
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0128d0de4f078b62bc9571c8758d80cb26585354
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615377"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Voor beeld van micro soft Connected cache preview Deployment scenario: geneste Azure IoT Edge gateway op twee niveaus met uitgaande niet-geverifieerde proxy

Op basis van het onderstaande diagram is er in dit scenario een Azure IoT Edge gateway en een downstream Azure IoT Edge apparaat, een Azure IoT Edge gateway die is geparent op een andere Azure IoT Edge gateway en een proxy server op de IT-DMZ. Hieronder ziet u een voor beeld van de micro soft Connected cache environments Varia bles die in de Azure Portal UX worden ingesteld voor beide MCC-modules die zijn geïmplementeerd op de Azure IoT Edge gateways. In het voor beeld ziet u de configuratie voor twee niveaus van Azure IoT Edge gateways, maar er is geen limiet voor de diepte van upstream-hosts die door micro soft Connected cache worden ondersteund. Er is geen verschil in de MCC-container maken opties uit de vorige voor beelden.

Raadpleeg voor meer informatie over het configureren van gelaagde implementaties van Azure IoT Edge gateways op de documentatie [verbinding maken downstream IOT edge apparaten-Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-connect-downstream-iot-edge-device?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true) . Houd er ook rekening mee dat bij het implementeren van Azure IoT Edge, micro soft Connected cache en aangepaste modules, alle modules zich in hetzelfde container register moeten bevinden.

In het onderstaande diagram wordt het scenario beschreven waarbij één Azure IoT Edge gateway als rechtstreekse toegang tot CDN-bronnen fungeert als de bovenliggende site voor een andere Azure IoT Edge gateway die fungeert als bovenliggend item voor een Azure IoT-blad apparaat, zoals een Raspberry pi. Alleen de bovenliggende Azure IoT Edge gateway heeft Internet verbinding met CDN-bronnen en zowel het Azure IoT Edge onderliggende als het Azure IoT-apparaat zijn Internet geïsoleerd. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Door micro soft verbonden cache genest" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configuratie van bovenliggende gateway

1. Voeg de micro soft-module verbonden cache toe aan de implementatie van uw Azure IoT Edge gateway apparaat in azure IoT Hub.
2. Voeg de omgevings variabelen voor de implementatie toe. Hieronder ziet u een voor beeld van omgevings variabelen.

    **Omgevings variabelen**

    | Name                 | Waarde                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Zie beschrijving van omgevings variabele hierboven. |
    | CUSTOMER_ID                   | Zie beschrijving van omgevings variabele hierboven. |
    | CUSTOMER_KEY                  | Zie beschrijving van omgevings variabele hierboven. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | IP-adres of FQDN van proxy server                     |

3. Voeg de opties voor het maken van de container voor de implementatie toe. Er is geen verschil in de MCC-container Create-opties in het vorige voor beeld. Hieronder ziet u een voor beeld van de opties voor het maken van een container.

### <a name="container-create-options"></a>Opties voor het maken van containers

```markdown
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
```

## <a name="child-gateway-configuration"></a>Configuratie van onderliggende gateway

>[!Note]
>Als u containers in uw eigen persoonlijke REGI ster hebt gebruikt in uw configuratie, moet u een wijziging aanbrengen in de configuratie. toml-instellingen en runtime-instellingen in de module-implementatie. Raadpleeg voor meer informatie [zelf studie: een hiërarchie van IOT edge apparaten maken-Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-nested-iot-edge?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true#deploy-modules-to-the-lower-layer-device) voor meer informatie.

1. Wijzig het pad naar de afbeelding voor de Edge-agent, zoals wordt getoond in het volgende voor beeld:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. Wijzig de runtime-instellingen voor de Edge hub en de Edge-agent in de implementatie van Azure IoT Edge, zoals wordt geïllustreerd in dit voor beeld:
    
    * Voer onder Edge hub in het veld installatie kopie ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Onder Edge agent, in het veld afbeelding, voert u ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Voeg de micro soft-module verbonden cache toe aan de implementatie van uw Azure IoT Edge gateway apparaat in azure IoT Hub.

   * Kies een naam voor de module: ```ConnectedCache```
   * Wijzig de afbeeldings-URI: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Voeg dezelfde omgevings variabelen en container Create-opties toe die in de bovenliggende implementatie worden gebruikt.

Voer de volgende opdracht uit in de terminal van het IoT Edge-apparaat dat als host fungeert voor de module of een apparaat in het netwerk voor een validatie van de juiste werking van micro soft Connected cache.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```