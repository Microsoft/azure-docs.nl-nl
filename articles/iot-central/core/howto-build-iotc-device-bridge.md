---
title: De Azure IoT Central-apparaats brug bouwen | Microsoft Docs
description: Bouw de IoT Central-apparaats brug om andere IoT-Clouds (Sigfox, partikel, het netwerk van zaken, enzovoort) te verbinden met uw IoT Central-app.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: fc8ea41e804344735cfa2400d5d763622d8811c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026247"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>De IoT Central Device Bridge bouwen om andere IoT-Clouds te verbinden met IoT Central

*Dit onderwerp is van toepassing op beheerders.*

De IoT Central Device Bridge is een open-source oplossing die uw Sigfox, partikel, het netwerk van de zaken en andere Clouds verbindt met uw IoT Central-app. Of u nu apparaten voor het bijhouden van activa gebruikt die zijn verbonden met het lage-energie netwerk van Sigfox, of met behulp van apparaten voor lucht kwaliteit bewaking op het partikel apparaat in de Cloud, of met behulp van bodem vocht bewakings apparaten op TTN, kunt u rechtstreeks gebruikmaken van de kracht van IoT Central met de IoT Central apparaat-brug. De apparaats brug verbindt andere IoT-Clouds met IoT Central door de gegevens die uw apparaten verzenden naar de andere Clouds, door te sturen naar uw IoT Central-app. In uw IoT Central-app kunt u regels maken en analyses uitvoeren op die gegevens, werk stromen maken in Power-automatisering en Azure Logic apps, gegevens exporteren en nog veel meer. De [IOT Central Device Bridge](https://aka.ms/iotcentralgithubdevicebridge) ophalen van github

## <a name="what-is-it-and-how-does-it-work"></a>Wat is het en hoe werkt het?
De IoT Central Device Bridge is een open-source oplossing in GitHub. U kunt nu de knop ' implementeren naar Azure ' gebruiken waarmee u een aangepaste Azure Resource Manager sjabloon met verschillende Azure-resources in uw Azure-abonnement implementeert. De resources zijn onder andere:
-    Azure-functie-app
-    Azure Storage-account
-    Verbruiksabonnement
-    Azure Key Vault

De functie-app is het essentiële onderdeel van de apparaats brug. Het ontvangt HTTP POST-aanvragen van andere IoT-platforms of aangepaste platforms via een eenvoudige integratie van webhooks. Er zijn voor beelden gegeven die laten zien hoe u verbinding maakt met Sigfox-, partikel-en TTN-Clouds. U kunt deze oplossing eenvoudig uitbreiden om verbinding te maken met uw aangepaste IoT-Cloud als uw platform HTTP POST-aanvragen kan verzenden naar uw functie-app.
Met de functie-app worden de gegevens getransformeerd in een indeling die wordt geaccepteerd door IoT Central en doorgestuurd via DPS-Api's.

![Scherm opname van Azure functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Als uw IoT Central-app het apparaat herkent op apparaat-ID in het doorgestuurde bericht, wordt er een nieuwe meting voor dat apparaat weer gegeven. Als de apparaat-ID nog nooit is gezien door uw IoT Central-app, probeert uw functie-app een nieuw apparaat met die apparaat-ID te registreren en wordt het weer gegeven als een ' niet-gekoppeld apparaat ' in uw IoT Central-app. 

## <a name="how-do-i-set-it-up"></a>Hoe kan ik instellen?
De instructies worden gedetailleerd weer gegeven in het Leesmij-bestand in de GitHub opslag plaats. 

## <a name="pricing"></a>Prijzen
De Azure-resources worden gehost in uw Azure-abonnement. Meer informatie over prijzen vindt u in het [Leesmij-bestand](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u de IoT Central Device Bridge bouwt, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw apparaten beheren](howto-manage-devices.md)
