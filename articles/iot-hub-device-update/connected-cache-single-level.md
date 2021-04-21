---
title: Voorbeelden van Verbonden cache preview-implementatie van Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Zelfstudies Verbonden cache voor voorbeeldimplementatiescenario's voor Microsoft Verbonden cache
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811720"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Voorbeelden van Verbonden cache preview-implementatie van Microsoft

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Eén niveau Azure IoT Edge gateway geen proxy

In het onderstaande diagram wordt het scenario beschreven waarin een Azure IoT Edge-gateway met directe toegang tot CDN-resources en een Leaf-apparaat van Azure IoT, zoals een Raspberry PI, een internetisoleerde onderliggende apparaten van de Azure IoT Edge-gateway is. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Apparaatupdate van Microsoft Verbonden cache verbroken" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Voeg de Microsoft Verbonden cache-module toe aan de implementatie van uw Azure IoT Edge-gatewayapparaat in Azure IoT Hub (zie Ondersteuning voor niet-verbonden apparaten voor meer informatie over het krijgen van de module). [](connected-cache-disconnected-device-update.md)
2. Voeg de omgevingsvariabelen voor de implementatie toe. Hieronder vindt u een voorbeeld van de omgevingsvariabelen.

    **Omgevingsvariabelen**
    
    | Name                          | Waarde                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Beschrijvingen [van omgevingsvariabelen](connected-cache-configure.md) bekijken |
    | CUSTOMER_ID                   | Beschrijvingen [van omgevingsvariabelen](connected-cache-configure.md) bekijken |
    | CUSTOMER_KEY                  | Beschrijvingen [van omgevingsvariabelen](connected-cache-configure.md) bekijken |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Voeg de opties voor het maken van containers toe voor de implementatie. Hieronder vindt u een voorbeeld van de opties voor het maken van containers.

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

Voor een validatie van goed functionerende Microsoft Verbonden cache voert u de volgende opdracht uit in de terminal van het IoT Edge-apparaat dat als host voor de module of een apparaat in het netwerk wordt uitgevoerd. Vervang \<Azure IoT Edge Gateway IP\> door het IP-adres of de hostnaam van uw IoT Edge gateway. (Zie details van de omgevingsvariabele voor informatie over de zichtbaarheid van dit rapport).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Gateway met Azure IoT Edge één niveau met uitgaande niet-gemachtigde proxy

In dit scenario is er een Azure IoT Edge-gateway die toegang heeft tot CDN-resources via een uitgaande niet-gemachtigde proxy. Microsoft Verbonden cache geconfigureerd om inhoud uit een aangepaste opslagplaats in de cache op te slaan en het overzichtsrapport is zichtbaar gemaakt voor iedereen in het netwerk. Hieronder vindt u een voorbeeld van de MCC-omgevingsvariabelen die worden ingesteld.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft Verbonden cache Proxy op één niveau" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Voeg de Microsoft Verbonden cache-module toe aan uw Azure IoT Edge-gatewayapparaatimplementatie in Azure IoT Hub.
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
    | UPSTREAM_PROXY                | Het IP-adres of de FQDN van uw proxyserver                                          |

3. Voeg de opties voor het maken van containers toe voor de implementatie. Er is geen verschil in opties voor het maken van MCC-containers in het vorige voorbeeld. Hieronder vindt u een voorbeeld van de opties voor het maken van containers.

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

Voor een validatie van een goed functionerende Microsoft Verbonden cache voert u de volgende opdracht uit in de terminal van het Azure IoT Edge-apparaat dat als host voor de module of een apparaat in het netwerk wordt gebruikt. Vervang \<Azure IoT Edge Gateway IP\> door het IP-adres of de hostnaam van uw IoT Edge gateway. (Zie details van de omgevingsvariabele voor informatie over de zichtbaarheid van dit rapport).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
