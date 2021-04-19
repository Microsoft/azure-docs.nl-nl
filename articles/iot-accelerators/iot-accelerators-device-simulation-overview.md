---
title: Overzicht van apparaatsimulatie - Azure | Microsoft Docs
description: Een beschrijving van de oplossingsversneller voor apparaatsimulatie en de mogelijkheden ervan.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 27a23ff924c2fa9e9e35fec010ca2a177868eacc
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713908"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Overzicht van de oplossingsversneller voor apparaatsimulatie

In een cloudgebaseerde IoT-oplossing maken uw apparaten verbinding met een cloud-eindpunt om telemetrie te verzenden, zoals temperatuur, locatie en status. Uw oplossing gebruikt deze telemetrie, zodat u er acties op kunt ondernemen of er inzichten uit kunt afleiden.

Wanneer u een IoT-oplossing ontwikkelt, zijn experimenten en testen essentiële onderdelen van dat proces. Simulatie is een belangrijk hulpmiddel tijdens dit proces. Met apparaatsimulatie kunt u het volgende doen:

* Snel een prototype in bedrijf krijgen en vervolgens itereren door het gedrag van gesimuleerde apparaten op elk apparaat aan te passen. Met dit proces kunt u het idee bewijzen voordat u in dure hardware investeert. U kunt aangepaste apparaten maken via de webinterface om binnen enkele seconden een prototypeapparaat te genereren.
* Controleer of de oplossing werkt zoals verwacht van apparaat tot oplossing door het gedrag van echte apparaten te simuleren. U kunt complexe apparaatgedragingen scripten met behulp van JavaScript om realistische gesimuleerde telemetrie te genereren.
* Schaal uw oplossing te testen door de normale, pieken en na piekbelastingen te simuleren. Schaaltests helpen u ook bij het juiste formaat van de Azure-resources die nodig zijn om uw oplossing uit te voeren.

![Voorbeeld van dronesimulatie](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Met Apparaatsimulatie kunt u apparaatmodellen definiëren om uw echte apparaten te simuleren. Dit model bevat berichtindelingen, dubbele eigenschappen en methoden. U kunt ook complex apparaatgedrag simuleren met JavaScript.

U kunt simulaties uitvoeren voor één tot duizenden apparaten die verbinding maken met een IoT-hub. Als hulp bij het testen kunt u eventueel een IoT-hub en Apparaatsimulatie implementeren voor een zelfstandige omgeving.

Apparaatsimulatie is gratis. Apparaatsimulatie wordt echter geïmplementeerd in uw Azure-abonnement in de cloud en verbruikt Azure-resources. Als apparaatsimulatie niet aan uw vereisten voldoet, is de broncode ook beschikbaar op [GitHub](https://github.com/Azure/azure-iot-pcs-device-simulation) om te kopiëren en te wijzigen.

## <a name="sample-simulations"></a>Voorbeeldsimulaties

Wanneer u Apparaatsimulatie implementeert, krijgt u enkele voorbeeldsimulaties en voorbeeldapparaten. U kunt deze voorbeelden gebruiken om te leren hoe u Apparaatsimulatie gebruikt. Voer een voorbeeldsimulatie uit om aan de [slag te gaan.](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) U kunt ook [uw eigen simulatie maken met behulp van een van de vele voorbeeldapparaten die zijn opgegeven.](iot-accelerators-device-simulation-create-simulation.md)

![Simulatieconfiguratie](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Aangepaste gesimuleerde apparaten

U kunt de apparaatsimulatie gebruiken om [aangepaste apparaatmodellen te maken voor](iot-accelerators-device-simulation-create-custom-device.md) gebruik in uw simulaties. U kunt bijvoorbeeld een nieuw koelapparaatmodel definiëren dat telemetrie over temperatuur en vochtigheid verzendt. Aangepaste gesimuleerde apparaten zijn ideaal voor eenvoudig apparaatgedrag met willekeurige, incrementele of aflopende telemetriewaarden.

![Apparaatmodel maken](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Geavanceerde gesimuleerde apparaten

Wanneer u meer controle nodig hebt over de telemetriewaarden die een apparaat verzendt, kunt u een geavanceerd apparaatmodel gebruiken. Geavanceerde apparaatmodellen maken JavaScript-ondersteuning mogelijk om de verzonden telemetriewaarden te bewerken. U kunt bijvoorbeeld de binnentemperatuur van een parkwagen simuleren op een warme, zonnige dag: naarmate de temperatuur van de lucht stijgt, neemt de temperatuur in de lucht exponentieel toe.

Met geavanceerde apparaatmodellen kunt [u uw eigen](iot-accelerators-device-simulation-advanced-device.md) apparaatmodellen maken en uploaden die bestaan uit een JSON-apparaatdefinitiebestand en bijbehorende JavaScript-bestanden.

Met geavanceerde apparaatmodellen kunt u:

* Geef de berichtindeling op die vanaf het apparaat wordt verzonden, samen met de telemetrietypen.
* Gebruik aangepaste scripts om telemetriewaarden te genereren die de status van het apparaat gedurende een periode behouden.
* Gebruik aangepaste scripts om op te geven hoe het gesimuleerde apparaat reageert op methoden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de oplossingsversneller apparaatsimulatie en de mogelijkheden ervan. Als u de oplossingsversneller wilt implementeren, gaat u naar de GitHub-opslagplaats:

> [!div class="nextstepaction"]
> [een IoT-apparaatsimulatie in Azure implementeren en uitvoeren](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md)
