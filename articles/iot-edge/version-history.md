---
title: IoT Edge versie navigatie en geschiedenis-Azure IoT Edge
description: Ontdek wat er nieuw is in IoT Edge met informatie over nieuwe functies en mogelijkheden in de nieuwste releases.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 877b488fc888b4ced8165c19bcd5c51b5f982745
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492503"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge versies en opmerkingen bij de release

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge is een product dat is gebaseerd op het open source-IoT Edge project dat wordt gehost op GitHub. Alle nieuwe releases worden beschikbaar gesteld in het [Azure IOT Edge-project](https://github.com/Azure/azure-iotedge). Bijdragen en fouten rapporten kunnen worden gemaakt op het [open-source IOT Edge-project](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Gedocumenteerde versies

De IoT Edge documentatie op deze site is beschikbaar voor twee verschillende versies van het product, zodat u de inhoud kunt kiezen die van toepassing is op uw IoT Edge omgeving. Momenteel zijn de twee ondersteunde versies:

* **IoT Edge 1,1 (LTS)** is de eerste LTS-versie (Long-term support) van IOT Edge. De documentatie voor deze versie omvat alle functies en mogelijkheden van alle vorige versies tot en met 1,1. Deze documentatie versie zal stabiel worden door de ondersteunde levens duur van versie 1,1 en bevat geen nieuwe functies die in latere versies worden uitgebracht. De 1,1-release is de meest recente, algemeen beschik bare versie van IoT Edge.
* **IoT Edge 1,2 (preview)** bevat aanvullende inhoud voor functies en mogelijkheden van de nieuwste preview-versie, [1,2-RC4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4).

Zie [Azure IOT Edge-ondersteunde systemen](support.md)voor meer informatie over IOT Edge releases.

## <a name="version-history"></a>Versiegeschiedenis

Deze tabel bevat recente versie geschiedenis voor IoT Edge pakket releases en markeert documentatie-updates voor elke versie.

| Release opmerkingen en assets | Type | Datum | Hoogtepunten |
| ------------------------ | ---- | ---- | ---------- |
| [1,2-RC4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Preview | 2021 maart | Nieuwe IoT Edge-pakketten geïntroduceerd, met nieuwe installatie-en configuratie stappen. Zie [Update van 1,0 of 1,1 naar 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)voor meer informatie.
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Langetermijnondersteuning (LTS) | Februari 2021 | [Langlopend ondersteunings plan en ondersteunde updates van systemen](support.md) |
| [1,2-RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Preview | November 2020 | [Apparaten IoT Edge achter gateways](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT Broker](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stabiel | Oktober 2020 | [UploadSupportBundle directe methode](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Runtime-metrische gegevens uploaden](how-to-access-built-in-metrics.md)<br>[Route prioriteit en time-to-Live](module-composition.md#priority-and-time-to-live)<br>[Opstart volgorde van module](module-composition.md#configure-modules)<br>[X. 509 hand matig inrichten](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stabiel | Maart 2020 | [X. 509 automatisch inrichten met DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule directe methode](how-to-edgeagent-direct-method.md#restart-module)<br>[ondersteunings bundel opdracht](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Volgende stappen

* [Alle Azure IoT Edge releases weer geven](https://github.com/Azure/azure-iotedge/releases)
* [Functie aanvragen maken of controleren in het feedback forum](https://feedback.azure.com/forums/907045-azure-iot-edge)