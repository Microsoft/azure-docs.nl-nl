---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: e5b85b31f0aed3a5e8df6bace5bf06c450bf91ff
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026927"
---
## <a name="automatic-device-management-in-azure-iot-hub"></a>Automatische Apparaatbeheer in azure IoT Hub
Automatische Apparaatbeheer in azure IoT Hub automatiseert veel van de herhaalde en complexe taken van het beheer van grote apparaat vloots in de hele levens cyclus. Met automatisch Apparaatbeheer kunt u een set apparaten op basis van hun eigenschappen richten, een gewenste configuratie definiëren en apparaten IoT Hub bijwerken wanneer deze binnen het bereik vallen.  Bestaat uit [automatische configuraties van apparaten](../articles/iot-hub/iot-hub-automatic-device-management.md) en [IOT Edge automatische implementaties](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge maakt cloud-gebaseerde implementatie van Azure-Services en oplossingen-specifieke code op on-premises apparaten mogelijk. IoT Edge-apparaten kunnen gegevens van andere apparaten samen voegen om computers en analyses uit te voeren voordat de gegevens naar de cloud worden verzonden. Zie [Azure IOT Edge](../articles/iot-edge/index.yml)voor meer informatie.

## <a name="iot-edge-agent"></a>IoT Edge-agent
Het deel van de IoT Edge runtime dat verantwoordelijk is voor het implementeren en bewaken van modules.

## <a name="iot-edge-device"></a>IoT Edge-apparaat
IoT Edge-apparaten hebben de IoT Edge-runtime geïnstalleerd en als **IOT edge apparaat** in de details van het apparaat gemarkeerd. Meer informatie over het [implementeren van Azure IOT Edge op een gesimuleerd apparaat in Linux-preview](../articles/iot-edge/quickstart-linux.md).

## <a name="iot-edge-automatic-deployment"></a>Automatische implementatie IoT Edge
Een IoT Edge automatische implementatie configureert een doelset van IoT Edge apparaten om een set IoT Edge modules uit te voeren. Elke implementatie zorgt er continu voor dat alle apparaten die overeenkomen met de doel voorwaarde, de opgegeven set modules uitvoeren, zelfs wanneer er nieuwe apparaten worden gemaakt of gewijzigd in overeenstemming met de doel voorwaarde. Elk IoT Edge apparaat krijgt alleen de implementatie met de hoogste prioriteit waarvan de doel voorwaarde voldoet. Meer informatie over [IOT Edge automatische implementatie](../articles/iot-edge/module-deployment-monitoring.md).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge implementatie manifest
Een JSON-document met de gegevens die moeten worden gekopieerd in een of meer IoT Edge apparaten module dubbele (s) voor het implementeren van een set modules, routes en de gewenste eigenschappen van de module.

## <a name="iot-edge-gateway-device"></a>IoT Edge gateway-apparaat
Een IoT Edge apparaat met het downstream-apparaat. Het downstream-apparaat kan IoT Edge of niet IoT Edge apparaat zijn.

## <a name="iot-edge-hub"></a>IoT Edge hub
Het deel van de IoT Edge runtime dat verantwoordelijk is voor de module communicatie, upstream (naar IoT Hub) en downstream (weg van IoT Hub)-communicatie. 

## <a name="iot-edge-leaf-device"></a>IoT Edge blad apparaat
Een IoT Edge apparaat zonder downstream-apparaat. 

## <a name="iot-edge-module"></a>Module IoT Edge
Een IoT Edge module is een docker-container die u op IoT Edge apparaten kunt implementeren. Hiermee wordt een specifieke taak uitgevoerd, zoals het opnemen van een bericht van een apparaat, het transformeren van een bericht of het verzenden van een bericht naar een IoT-hub. De service communiceert met andere modules en verzendt gegevens naar de IoT Edge runtime. Meer [informatie over de vereisten en hulpprogram ma's voor het ontwikkelen van IOT Edge modules](../articles/iot-edge/module-development.md).

## <a name="iot-edge-module-identity"></a>Identiteit van IoT Edge-module
Een record in het IoT Hub module-id-REGI ster met gedetailleerde informatie over het bestaan en de beveiligings referenties die door een module moeten worden gebruikt om te verifiëren met een Edge hub of IoT Hub.

## <a name="iot-edge-module-image"></a>Afbeelding van de module IoT Edge
De docker-installatie kopie die door de IoT Edge-runtime wordt gebruikt om module-exemplaren te instantiëren.

## <a name="iot-edge-module-twin"></a>IoT Edge-module dubbele
Een JSON-document is persistent gemaakt in de IoT Hub waarin de status informatie voor een module-exemplaar wordt opgeslagen.

## <a name="iot-edge-priority"></a>IoT Edge prioriteit
Wanneer twee IoT Edge implementaties gericht zijn op hetzelfde apparaat, wordt de implementatie met een hogere prioriteit toegepast. Als twee implementaties dezelfde prioriteit hebben, wordt de implementatie met de latere aanmaak datum toegepast. Meer informatie over [prioriteit](../articles/iot-edge/module-deployment-monitoring.md#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-runtime
IoT Edge runtime bevat alles wat micro soft distribueert om te worden geïnstalleerd op een IoT Edge apparaat. Het bevat Edge-agent, Edge hub en de IoT Edge Security daemon.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Modules IoT Edge op één apparaat instellen
Een bewerking waarmee de inhoud van een IoT Edge-manifest wordt gekopieerd op één apparaat ' module dubbele. De onderliggende API is een algemene ' toepassings configuratie ', waarbij simpelweg een IoT Edge-manifest als invoer wordt gebruikt.

## <a name="iot-edge-target-condition"></a>IoT Edge doel voorwaarde
In een IoT Edge-implementatie is doel voorwaarde elke Booleaanse voor waarde op apparaatdubbels van het apparaat om de doel apparaten van de implementatie te selecteren, bijvoorbeeld **tag. Environment = Prod**. De doel voorwaarde wordt continu geëvalueerd om nieuwe apparaten op te halen die voldoen aan de vereisten of om apparaten te verwijderen die niet meer beschikbaar zijn. Meer informatie over [doel voorwaarde](../articles/iot-edge/module-deployment-monitoring.md#target-condition)