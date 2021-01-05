---
title: Azure IoT Edge en Azure IoT Central | Microsoft Docs
description: Meer informatie over het gebruik van Azure IoT Edge met een IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 9a7c886ba4dd6e7ab4bd62700f5437855a16a5ad
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796564"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge-apparaten verbinden met een Azure IoT Central-toepassing

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

IoT Edge bestaat uit drie onderdelen:

* **IOT Edge modules** zijn containers waarop Azure-Services, partner services of uw eigen code worden uitgevoerd. Modules worden op IoT Edge apparaten geïmplementeerd en lokaal op deze apparaten uitgevoerd.
* De **IOT Edge runtime** wordt op elk IOT edge apparaat uitgevoerd en beheert de modules die op elk apparaat zijn geïmplementeerd.
* Met een **cloudinterface** kunt u op afstand de IoT Edge-apparaten controleren en beheren. IoT Central is de Cloud interface.

Een **Azure IOT Edge** apparaat kan een gateway apparaat zijn, met downstream-apparaten die verbinding maken met het IOT edge apparaat. In dit artikel wordt meer informatie over verbindings patronen voor downstream-apparaten gedeeld.

Met een **apparaatprofiel** worden de mogelijkheden van uw apparaat en IOT Edge modules gedefinieerd. Mogelijkheden zijn onder andere telemetrie die de module verzendt, module-eigenschappen en de opdrachten die een module beantwoordt.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>De relaties van downstream-apparaten met een gateway en modules

Downstream-apparaten kunnen via de module verbinding maken met een IoT Edge gateway-apparaat `$edgeHub` . Dit IoT Edge apparaat wordt in dit scenario een transparante gateway.

![Diagram van transparante gateway](./media/concepts-iot-edge/gateway-transparent.png)

Downstream-apparaten kunnen ook via een aangepaste module verbinding maken met een IoT Edge gateway-apparaat. In het volgende scenario maken downstream-apparaten verbinding via een aangepaste Modbus-module.

![Diagram van de verbinding van de aangepaste module](./media/concepts-iot-edge/gateway-module.png)

In het volgende diagram ziet u een verbinding met een IoT Edge gateway apparaat via beide typen modules (aangepast en `$edgeHub` ).  

![Diagram van verbinding maken via beide verbindings modules](./media/concepts-iot-edge/gateway-module-transparent.png)

Ten slotte kunnen downstream-apparaten via meerdere aangepaste modules verbinding maken met een IoT Edge gateway-apparaat. In het volgende diagram ziet u downstream-apparaten die verbinding maken via een aangepaste Modbus-module, een afgeleide aangepaste module en de `$edgeHub` module. 

![Diagram van verbinding maken via meerdere aangepaste modules](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Implementatie manifesten en Device-sjablonen

In IoT Edge kunt u bedrijfs logica implementeren en beheren in de vorm van modules. IoT Edge modules zijn de kleinste reken eenheid die wordt beheerd door IoT Edge en kunnen Azure-Services (zoals Azure Stream Analytics) of uw eigen oplossings code bevatten. Zie [IOT Edge modules](../../iot-edge/iot-edge-modules.md)voor informatie over hoe modules worden ontwikkeld, geïmplementeerd en onderhouden.

Op hoog niveau is een implementatie manifest een lijst met module apparaatdubbels die zijn geconfigureerd met de gewenste eigenschappen. Een implementatie manifest vertelt een IoT Edge apparaat (of een groep apparaten) welke modules moeten worden geïnstalleerd en hoe ze kunnen worden geconfigureerd. Implementatie manifesten bevatten de gewenste eigenschappen voor elke module dubbele. IoT Edge-apparaten melden de gerapporteerde eigenschappen voor elke module terug.

Visual Studio code gebruiken om een implementatie manifest te maken. Zie [Azure IOT Edge voor Visual Studio code voor](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)meer informatie.

In azure IoT Central kunt u een implementatie manifest importeren om een sjabloon voor een apparaat te maken. In het volgende stroom diagram ziet u de levens cyclus van het implementatie manifest in IoT Central.

![De levens cyclus van het stroom diagram van het implementatie manifest](./media/concepts-iot-edge/dmflow.png)

IoT Central modellen een IoT Edge apparaat als volgt:

* Elke IoT Edge-apparaatprofiel heeft een model apparaat.
* Voor elke aangepaste module die wordt vermeld in het implementatie manifest, wordt een module mogelijkheidsprofiel gegenereerd.
* Er wordt een relatie tot stand gebracht tussen elk module functionaliteits model en een model apparaat.
* Een module-functionaliteits model implementeert module interfaces.
* Elke module interface bevat telemetrie, eigenschappen en opdrachten.

![Diagram van IoT Edge modellen](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge gateway-apparaten

Als u een IoT Edge apparaat hebt geselecteerd om een gateway apparaat te zijn, kunt u downstream-relaties toevoegen aan apparaten modellen voor apparaten die u wilt verbinden met het gateway apparaat.

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u de volgende stap vinden [in IOT Central](./tutorial-define-gateway-device-type.md).
