---
title: IoT-Plug en Play architectuur | Microsoft Docs
description: Als opbouw functie voor oplossingen begrijpt u belang rijke onderdelen van IoT Plug en Play.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 2cccb1fdfe775250f80da6cc2ecdcc4ddaa3d88e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95484080"
---
# <a name="iot-plug-and-play-architecture"></a>IoT Plug and Play-architectuur

Met IoT Plug en Play kunnen ontwikkelaars slimme apparaten in hun oplossingen integreren zonder handmatige configuratie. De kern van IoT Plug en Play is een _model_ apparaat waarmee de mogelijkheden van een apparaat worden beschreven in een IoT-Plug en Play toepassing. Dit model is gestructureerd als een reeks interfaces die het volgende definiëren:

- _Eigenschappen_ die de alleen-lezen- of schrijfbare status van apparaat of andere entiteit vertegenwoordigen. Een serienummer van een apparaat kan bijvoorbeeld een alleen-lezeneigenschap zijn en een doeltemperatuur op een thermostaat kan een schrijfbare eigenschap zijn.
- _Telemetrie_ ofwel de gegevens die door een apparaat worden verzonden, ongeacht of het gaat om gegevens van een normale stroom met sensorwaarden, een incidentele fout of een informatiebericht.
- _Opdrachten_ waarmee functies of bewerkingen die op het apparaat kunnen worden uitgevoerd, worden beschreven. Met een opdracht kan bijvoorbeeld een gateway opnieuw worden opgestart of een foto worden gemaakt met een externe camera.

Elk model en elke interface heeft een unieke ID.

In het volgende diagram ziet u de belangrijkste elementen van een IoT Plug en Play-oplossing:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Plug and Play-architectuur":::

## <a name="model-repository"></a>Modelopslagplaats

De [model opslagplaats](./concepts-model-repository.md) is een archief voor model-en Interface definities. U definieert modellen en interfaces met behulp van de [Digital Apparaatdubbels Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl).

Met de Web-UI kunt u de modellen en interfaces beheren.

De model opslagplaats heeft ingebouwde op rollen gebaseerde toegangs beheer functies waarmee u de toegang tot Interface definities kunt beperken.

## <a name="devices"></a>Apparaten

Een apparaat Builder implementeert de code om uit te voeren op een IoT-Smart Device met behulp van een van de [Azure IOT-apparaat-sdk's](./libraries-sdks.md). De Sdk's van het apparaat helpen de opbouw functie voor apparaten bij het volgende:

- Veilig verbinding maken met een IoT-hub.
- Registreer het apparaat bij uw IoT-hub en kondigt de model-ID aan waarmee de verzameling DTDL-interfaces wordt geïdentificeerd die het apparaat implementeert.
- Synchroniseer de eigenschappen die zijn gedefinieerd in de DTDL-interfaces tussen het apparaat en uw IoT-hub.
- Opdracht-handlers toevoegen voor de opdrachten die zijn gedefinieerd in de DTDL-interfaces.
- Telemetrie verzenden naar de IoT-hub.

## <a name="iot-edge-gateway"></a>IoT Edge gateway

Een IoT Edge gateway fungeert als een intermediair voor het verbinden van IoT-Plug en Play apparaten die niet rechtstreeks verbinding kunnen maken met een IoT-hub. Zie [hoe een IOT edge apparaat kan worden gebruikt als een gateway](../iot-edge/iot-edge-as-gateway.md)voor meer informatie.

## <a name="iot-edge-modules"></a>IoT Edge-modules

Met een _IOT Edge module_ kunt u bedrijfs logica op de rand implementeren en beheren. Azure IoT Edge modules zijn de kleinste reken eenheid die wordt beheerd door IoT Edge en kunnen Azure-Services (zoals Azure Stream Analytics) of uw eigen oplossings code bevatten.

De _IOT Edge hub_ is een van de modules waaruit de Azure IOT Edge-runtime bestaat. Het fungeert als een lokale proxy voor IoT Hub door dezelfde protocol eindpunten als IoT Hub weer te geven. Deze consistentie betekent dat clients (ongeacht of apparaten of modules) verbinding kunnen maken met de IoT Edge runtime, net zoals ze zouden IoT Hub.

De Sdk's van het apparaat helpen een module Builder:

- Gebruik de IoT Edge hub om veilig verbinding te maken met uw IoT-hub.
- De module registreren bij uw IoT-hub en de model-ID aankondigen die de verzameling DTDL-interfaces identificeert die het apparaat implementeert.
- Synchroniseer de eigenschappen die zijn gedefinieerd in de DTDL-interfaces tussen het apparaat en uw IoT-hub.
- Opdracht-handlers toevoegen voor de opdrachten die zijn gedefinieerd in de DTDL-interfaces.
- Telemetrie verzenden naar de IoT-hub.

## <a name="iot-hub"></a>IoT Hub

[IOT hub](../iot-hub/about-iot-hub.md) is een in de Cloud gehoste service die fungeert als een centrale Message hub voor bidirectionele communicatie tussen uw IOT-oplossing en de apparaten die worden beheerd.

Een IoT-hub:

- Hiermee wordt de model-ID geïmplementeerd door een apparaat dat beschikbaar is voor een back-end-oplossing.
- Houdt de digitale twee apparaten bij die zijn gekoppeld aan elk Plug en Play apparaat dat is verbonden met de hub.
- Stuurt telemetrie-stromen naar andere services voor verwerking of opslag.
- Hiermee worden digitale dubbele wijzigings gebeurtenissen naar andere services gerouteerd om de bewaking van apparaten mogelijk te maken.

## <a name="backend-solution"></a>Back-end-oplossing

Met een back-end-oplossing worden verbonden apparaten bewaakt en beheerd door interactie met Digital apparaatdubbels in de IoT-hub. Gebruik een van de service-Sdk's om uw back-end-oplossing te implementeren. Om inzicht te krijgen in de mogelijkheden van een verbonden apparaat, de back-end van de oplossing:

1. Hiermee wordt de model-ID opgehaald van het apparaat dat is geregistreerd bij de IoT hub.
1. Maakt gebruik van de model-ID voor het ophalen van de interface definities uit een model opslagplaats.
1. Maakt gebruik van de model-parser om informatie uit de interface definities te extra heren.

De back-end-oplossing kan de informatie van de interface definities gebruiken voor het volgende:

- Lees eigenschaps waarden die zijn gerapporteerd door apparaten.
- Beschrijf bare eigenschappen op een apparaat bijwerken.
- Aanroep opdrachten die worden geïmplementeerd door een apparaat.
- Meer informatie over de indeling van de telemetrie die door een apparaat wordt verzonden.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van de architectuur van een IoT-Plug en Play oplossing, moeten de volgende stappen worden uitgevoerd om meer te weten te komen over:

- [De model opslagplaats](./concepts-model-repository.md)
- [Digitale dubbele model integratie](./concepts-model-discovery.md)
- [Ontwikkelen voor IoT-Plug en Play](./concepts-developer-guide-device.md)
