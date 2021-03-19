---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "69558695"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Back-end-apps kunnen gebruikmaken van Azure IoT Hub primitieven, zoals [dubbele][lnk-devtwin] en [directe methoden][lnk-c2dmethod]voor apparaten, om de acties voor Apparaatbeheer op afstand te starten en te bewaken. In deze zelf studie ziet u hoe een back-end-app en een apparaat-app kunnen samen werken om het opnieuw opstarten van een extern apparaat te initiëren en te bewaken met behulp van IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Gebruik een directe methode om acties voor Apparaatbeheer te initiëren (zoals opnieuw opstarten, fabrieks instellingen en firmware-update) van een back-end-app in de Cloud. Het apparaat is verantwoordelijk voor:

* De methode aanvraag afhandelen die vanuit IoT Hub is verzonden.

* De bijbehorende apparaatspecifieke actie op het apparaat starten.

* Het leveren van status updates via *gerapporteerde eigenschappen* aan IOT hub.

U kunt een back-end-app in de Cloud gebruiken om Device-dubbele query's uit te voeren om te rapporteren over de voortgang van de acties voor Apparaatbeheer.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
