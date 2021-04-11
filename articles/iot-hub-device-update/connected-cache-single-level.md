---
title: Voor beelden van micro soft Connected cache preview-implementatie scenario | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Zelf studies voor voor beelden van micro soft Connected cache preview-implementatie
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664630"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Voor beelden van micro soft Connected cache preview-implementatie scenario's

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Azure IoT Edge gateway geen proxy op één niveau

In het onderstaande diagram wordt het scenario beschreven waarbij een Azure IoT Edge gateway die directe toegang heeft tot CDN-resources en er een Azure IoT-blad apparaat is, zoals een Raspberry PI met een Internet geïsoleerde onderliggende apparaten van de Azure IoT Edge gateway. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Update van met micro soft verbonden cache verbroken apparaten" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Voeg de micro soft-module verbonden cache toe aan de implementatie van het Azure IoT Edge gateway-apparaat in azure IoT Hub (Zie `MCC concepts` voor meer informatie over het ophalen van de module).
2. Voeg de omgevings variabelen voor de implementatie toe. Hieronder ziet u een voor beeld van omgevings variabelen.

    **Omgevings variabelen**
    
    | Name                 | Waarde                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Zie beschrijving van omgevings variabele hierboven. |
    | CUSTOMER_ID                   | Zie beschrijving van omgevings variabele hierboven. |
    | CUSTOMER_KEY                  | Zie beschrijving van omgevings variabele hierboven. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Voeg de opties voor het maken van de container voor de implementatie toe. Hieronder ziet u een voor beeld van de opties voor het maken van een container.

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

Voer de volgende opdracht uit in de terminal van het IoT Edge-apparaat dat als host fungeert voor de module of een apparaat in het netwerk voor een validatie van de juiste werking van micro soft Connected cache.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Azure IoT Edge gateway op één niveau met uitgaande niet-geverifieerde proxy

In dit scenario is er een Azure IoT Edge gateway die toegang heeft tot CDN-bronnen via een uitgaand niet-geverifieerde proxy. Micro soft Connected cache wordt geconfigureerd om inhoud van een aangepaste opslag plaats in de cache op te slaan en het overzichts rapport is zichtbaar gemaakt voor iedereen in het netwerk. Hieronder ziet u een voor beeld van de MCC-omgevings variabelen die worden ingesteld.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Micro soft Connected cache op één niveau proxy" lightbox="media/connected-cache-overview/single-level-proxy.png":::

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
```

Voer de volgende opdracht uit in de terminal van het Azure IoT Edge-apparaat dat als host fungeert voor de module of een apparaat in het netwerk voor een validatie van de juiste werking van micro soft Connected cache.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
