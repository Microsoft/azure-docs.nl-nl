---
title: Overzicht van Device simulatie-Azure | Microsoft Docs
description: Een beschrijving van de apparaat simulatie oplossings versneller en de mogelijkheden ervan.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 7be0278fd8870395b8495168eb9723780eba4b26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852367"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Overzicht van de oplossingsversneller voor apparaatsimulatie

In een op de cloud gebaseerde IoT-oplossing maken uw apparaten verbinding met een Cloud-eind punt voor het verzenden van telemetrie, zoals de Tempe ratuur, locatie en status. Uw oplossing verbruikt deze telemetrie, zodat u acties kunt ondernemen of inzichten kunt afleiden.

Wanneer u een IoT-oplossing ontwikkelt, zijn experimenten en testen essentiële onderdelen van dat proces. Simulatie is een belang rijk hulp middel tijdens dit proces. Met Device simulatie kunt u het volgende doen:

* U kunt snel een prototype maken en uitvoeren en vervolgens herhalen door het gedrag van het gesimuleerde apparaat te wijzigen. Met dit proces kunt u het idee aantonen voordat u een dure hardware hebt. U kunt aangepaste apparaten maken via de Web-UI om in een paar seconden een prototype apparaat te genereren.
* Valideer of de oplossing op basis van het apparaat naar de oplossing verwacht werkt door het gedrag van het apparaat in de praktijk te simuleren. U kunt een script maken voor complex gedrag van apparaten met behulp van Java script om realistische gesimuleerde telemetrie te genereren.
* Schaal test uw oplossing door normale, pieken en meer belasting voorwaarden te simuleren. Schaal tests helpen u ook om de juiste grootte te geven aan de Azure-resources die nodig zijn om uw oplossing uit te voeren.

![Voor beeld van drone simulatie](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Met Device simulatie kunt u apparaat modellen definiëren om uw echte apparaten te simuleren. Dit model bevat bericht indelingen, dubbele eigenschappen en methoden. U kunt ook het gedrag van complexe apparaten simuleren met Java script.

U kunt simulaties uitvoeren voor één tot duizenden apparaten die verbinding maken met een IoT-hub. Voor hulp bij het testen kunt u optioneel een IoT-hub en Device simulatie implementeren voor een zelfstandige omgeving.

Simulatie van apparaten is gratis. Device simulatie wordt echter geïmplementeerd in uw Azure-abonnement in de Cloud en verbruikt Azure-resources. Als Device simulatie niet aan uw vereisten voldoet, [is de bron code ook beschikbaar op github](https://github.com/Azure/device-simulation-dotnet) om deze te kopiëren en te wijzigen.

## <a name="sample-simulations"></a>Voorbeeld simulaties

Wanneer u Device simulatie implementeert, krijgt u enkele voor beelden van simulaties en voorbeeld apparaten. U kunt deze voor beelden gebruiken voor meer informatie over het gebruik van simulatie van apparaten. Voer een [voorbeeld simulatie](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)uit om aan de slag te gaan. U kunt ook [uw eigen simulatie maken met behulp van een van de beschik bare voorbeeld apparaten](iot-accelerators-device-simulation-create-simulation.md).

![Simulatieconfiguratie](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Aangepaste gesimuleerde apparaten

U kunt de simulatie van het apparaat gebruiken om [aangepaste modellen te maken](iot-accelerators-device-simulation-create-custom-device.md) voor gebruik in uw simulaties. U kunt bijvoorbeeld een nieuw model voor koel apparaten definiëren dat telemetrie van de Tempe ratuur en de vochtigheid verzendt. Aangepaste gesimuleerde apparaten zijn ideaal voor eenvoudig gedrag van apparaten met een wille keurige, incrementele of decrementing-telemetrie.

![Apparaatmodel maken](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Geavanceerd gesimuleerde apparaten

Als u meer controle nodig hebt over de telemetrie-waarden die een apparaat verzendt, kunt u een geavanceerd model voor apparaten gebruiken. Geavanceerde apparaten bieden ondersteuning voor Java script om de verzonden telemetriegegevens te bewerken. U kunt bijvoorbeeld de inwendige Tempe ratuur van een geparkde auto simuleren op een warme, zonnige dag, als de buiten temperatuur stijging, de inwendige Tempe ratuur exponentieel toeneemt.

Met geavanceerde apparaten kunt u [uw eigen modellen maken en uploaden](iot-accelerators-device-simulation-advanced-device.md) die bestaan uit een definitie bestand van een JSON-apparaat en de bijbehorende java script-bestanden.

Met geavanceerde Device-modellen kunt u:

* Geef de bericht indeling op die van het apparaat wordt verzonden, samen met de typen telemetrie.
* Aangepaste scripting gebruiken om telemetrie-waarden te genereren waarmee de status van het apparaat in de loop van de tijd wordt gehandhaafd.
* Gebruik aangepaste scripting om op te geven hoe het gesimuleerde apparaat reageert op methoden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de apparaat simulatie oplossings versneller en de mogelijkheden ervan. Ga naar de GitHub-opslag plaats om de oplossings versneller te implementeren:

> [!div class="nextstepaction"]
> [een IoT-apparaatsimulatie in Azure implementeren en uitvoeren](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)
