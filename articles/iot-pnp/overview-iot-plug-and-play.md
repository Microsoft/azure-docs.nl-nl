---
title: Inleiding tot IoT Plug en Play | Microsoft Docs
description: Informatie over IoT Plug en Play. IoT Plug en Play is gebaseerd op een open modeltaal waarmee slimme IoT-apparaten hun mogelijkheden kunnen declareren. IoT-apparaten presenteren die declaratie, een apparaatmodel genoemd, wanneer er verbinding wordt gemaakt met cloudoplossingen. Vervolgens kan met de cloudoplossing automatisch inzicht worden verkregen in het apparaat en kan interactie worden gestart zonder dat u code hoeft te schrijven.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: d6cba4bcb76094ed156ef35b1ea76cea7001c372
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95486293"
---
# <a name="what-is-iot-plug-and-play"></a>Wat is IoT Plug en Play?

Met IoT Plug en Play kunnen ontwikkelaars slimme apparaten in hun oplossingen integreren zonder handmatige configuratie. De kern van IoT Plug en Play omvat een apparaat _model_ dat kan worden gebruikt voor een apparaat om de mogelijkheden van het apparaat aan te kondigen bij een IoT Plug en Play-toepassing. Dit model is gestructureerd als een set elementen waarmee het volgende wordt gedefinieerd:

- _Eigenschappen_ die de alleen-lezen- of schrijfbare status van apparaat of andere entiteit vertegenwoordigen. Een serienummer van een apparaat kan bijvoorbeeld een alleen-lezeneigenschap zijn en een doeltemperatuur op een thermostaat kan een schrijfbare eigenschap zijn.
- _Telemetrie_ ofwel de gegevens die door een apparaat worden verzonden, ongeacht of het gaat om gegevens van een normale stroom met sensorwaarden, een incidentele fout of een informatiebericht.
- _Opdrachten_ waarmee functies of bewerkingen die op het apparaat kunnen worden uitgevoerd, worden beschreven. Met een opdracht kan bijvoorbeeld een gateway opnieuw worden opgestart of een foto worden gemaakt met een externe camera.

U kunt deze elementen groeperen in interfaces om de elementen opnieuw te gebruiken in verschillende modellen waardoor samenwerking eenvoudiger wordt en ontwikkeling sneller verloopt.

Als u IoT Plug en Play wilt gebruiken met [Azure Digital Twins](../digital-twins/overview.md), definieert u modellen en interfaces met behulp van [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). IoT Plug en Play en DTDL zijn beschikbaar voor de community en Microsoft verwelkomt de samenwerking met klanten, partners en de branche. Beide zijn gebaseerd op open W3C-standaarden, zoals JSON-LD en RDF, de voorzien in vereenvoudiging van ingebruikname in verschillende services en hulpprogramma's.

Er zijn geen extra kosten verbonden aan het gebruik van IoT Plug en Play en DTDL. De standaardtarieven voor [Azure IoT Hub](../iot-hub/about-iot-hub.md) en andere Azure-services blijven gelijk.

In dit artikel wordt een overzicht gegeven van:

- De gebruikelijke rollen die zijn gekoppeld aan een project waarvoor IoT Plug en Play wordt gebruikt.
- De wijze waarop u IoT Plug en Play-apparaten kunt gebruiken in uw toepassing.
- De wijze waarop u een IoT-apparaattoepassing kunt ontwikkelen die ondersteuning biedt voor IoT Plug en Play.

## <a name="user-roles"></a>Gebruikersrollen

IoT Plug en Play is handig voor twee typen ontwikkelaars:

- Een _oplossingsontwikkelaar_ is verantwoordelijk voor het ontwikkelen van een IoT-oplossing met behulp van Azure IoT Hub en andere Azure-resources en voor het identificeren van IoT-apparaten die moeten worden geïntegreerd.
- Een _apparaatontwikkelaar_ schrijft de code die wordt uitgevoerd op een apparaat dat is verbonden met uw toepassing.

## <a name="use-iot-plug-and-play-devices"></a>IoT Plug en Play-apparaten gebruiken

Als oplossingsontwikkelaar kunt u een in de cloud gehoste IoT-oplossing ontwikkelen die gebruikmaakt van IoT Plug en Play-apparaten. U kunt gebruikmaken van [IoT Hub](../iot-hub/about-iot-hub.md), een beheerde service die fungeert als een centrale berichtenhub voor veilige bidirectionele communicatie tussen uw IoT-toepassing en apparaten.

Wanneer u een IoT Plug and Play-apparaat verbindt met een IoT-hub, kunt u het hulpprogramma [Azure IoT Explorer](./howto-use-iot-explorer.md) gebruiken voor het weergeven de telemetrie, eigenschappen en opdrachten die zijn gedefinieerd in de interfaces waarmee het model is samengesteld.

Als u bestaande sensoren hebt aangesloten op een Windows- of Linux-gateway, kunt u [IoT Plug en Play Bridge](./concepts-iot-pnp-bridge.md) gebruiken om deze sensoren te verbinden en IoT Plug en Play-apparaten te maken zonder dat u de software/firmware van het apparaat (voor [ondersteunde protocollen](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors) ) hoeft te schrijven.

## <a name="develop-an-iot-device-application"></a>Een IoT-apparaattoepassing ontwikkelen

Als apparaatontwikkelaar kunt u een IoT-hardwareproduct ontwikkelen dat ondersteuning biedt voor IoT Plug en Play. Het proces omvat drie belangrijke stappen:

1. Definieer het apparaatmodel. U maakt een set JSON-bestanden waarmee de mogelijkheden van uw apparaat worden gedefinieerd met behulp van [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Met een model wordt een volledige entiteit, bijvoorbeeld een fysiek product, beschreven en wordt de set met interfaces gedefinieerd die door die entiteit worden geïmplementeerd. Interfaces zijn gedeelde contracten voor unieke identificatie van de telemetrie, eigenschappen en opdrachten die door een apparaat worden ondersteund. Interfaces kunnen opnieuw worden gebruikt voor verschillende modellen.

1. Zorg dat u de software of firmware van het apparaat zo ontwerpt dat de telemetrie, eigenschappen en opdrachten de IoT Plug en Play-conventies volgen. Als u bestaande sensoren verbindt die gekoppeld zijn aan een Windows- of Linux-gateway, kan de [IoT Plug en Play Bridge](./concepts-iot-pnp-bridge.md) deze stap vereenvoudigen.

1. Het apparaat kondigt de model-id aan als onderdeel van de MQTT-verbinding. De Azure IoT SDK bevat nieuwe constructies waarmee de model-id op het moment van verbinding kan worden opgegeven.

> [!Important]
> IoT Plug en Play-apparaten moeten gebruikmaken van MQTT of MQTT via WebSockets. Andere protocollen, zoals AMQP of HTTP, zijn niet geldig voor het implementeren van IoT Plug en Play-apparaten.

## <a name="device-certification"></a>Apparaatcertificering

Het [programma voor IoT Plug and Play-apparaatcertificering](howto-certify-device.md) verifieert dat een apparaat voldoet aan de IoT Plug and Play-certificeringsvereisten. U kunt een gecertificeerd apparaat toevoegen aan de openbare [catalogus Gecertificeerd voor Azure IoT-apparaten](https://aka.ms/devicecatalog).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Plug en Play hebt gekregen, is de voorgestelde volgende stap het uitproberen van een van de quickstart:

- [Een apparaat verbinden met IoT Hub](./quickstart-connect-device.md)
- [Interactie met een apparaat vanuit uw oplossing](./quickstart-service.md)
