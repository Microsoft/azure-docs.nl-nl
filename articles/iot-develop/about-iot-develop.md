---
title: Inleiding tot Azure IoT-apparaten en-toepassingen ontwikkelen
description: Meer informatie over het gebruik van Azure IoT voor het ontwikkelen van Inge sloten apparaten en het bouwen van Cloud toepassingen op basis van apparaten.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100654951"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Wat is Azure IoT-apparaat en toepassings ontwikkeling?

Azure IoT is een verzameling beheerde en platform services die uw IoT-apparaten verbinden, bewaken en beheren. Azure IoT biedt ontwikkel aars een uitgebreide set opties. Uw opties zijn onder andere apparaat platforms, ondersteuning van Cloud Services, Sdk's en hulpprogram ma's voor het bouwen van Cloud toepassingen met een apparaat.

In dit artikel worden enkele belang rijke aandachtspunten voor ontwikkel aars weer gegeven die aan de slag gaan met Azure IoT. Deze concepten richten u als een IoT-apparaats ontwikkelaar naar uw Azure IoT-opties en hoe u aan de slag gaat. In het bijzonder worden deze concepten in het artikel weer gegeven:
- [Informatie over de ontwikkelings rollen van apparaten](#device-development-roles)
- [Uw hardware kiezen](#choosing-your-hardware)
- [Een SDK kiezen](#choosing-an-sdk)
- [Verbindings opties selecteren](#selecting-connection-options)

## <a name="device-development-roles"></a>Rollen voor het ontwikkelen van apparaten
In dit artikel worden twee algemene rollen besproken die u kunt zien onder ontwikkel aars van apparaten. Zoals hier wordt gebruikt, is een rol een verzameling van gerelateerde ontwikkel taken. Het is handig om te begrijpen met welk type Development Role u momenteel werkt. Uw rol is van invloed op een groot aantal ontwikkel keuzes die u maakt.

* **Toepassings ontwikkeling van apparaten:** Is afgestemd op moderne ontwikkel procedures, streeft veel van de hogere talen en voert uit op een algemeen besturings systeem, zoals Windows of Linux.

* **Ontwikkeling van Inge sloten apparaten:** Beschrijft de ontwikkeling gericht op gebeperkte apparaten. Een resource beperkt apparaat wordt vaak gebruikt om de kosten per eenheid, het energie verbruik of de grootte van het apparaat te verminderen. Deze apparaten hebben direct controle over het hardwareplatform waarop ze worden uitgevoerd.

### <a name="device-application-development"></a>Toepassings ontwikkeling van apparaten
Application ontwikkel aars van apparaten passen bestaande apparaten aan om verbinding te maken met de Cloud en te integreren in hun IoT-oplossingen. Deze apparaten kunnen betere talen ondersteunen, zoals C# of python, en bieden vaak ondersteuning voor een robuust besturings systeem voor algemene doel einden, zoals Windows of Linux. Algemene doel apparaten zijn Pc's, containers, Raspberry Pis en mobiele apparaten. 

In plaats van beperkte apparaten op schaal te ontwikkelen, zijn deze ontwikkel aars gericht op het inschakelen van een specifiek IoT-scenario dat vereist is voor de Cloud oplossing. Sommige van deze ontwikkel aars werken ook op beperkte apparaten voor hun Cloud oplossing. Zie voor ontwikkel aars die werken met beperkte apparaten hieronder het pad voor het [ontwikkelen van Inge sloten apparaten](#embedded-device-development) .

> [!TIP]
> Bekijk de Sdk's van het niet- [beperkde apparaat](about-iot-sdks.md#unconstrained-device-sdks) om aan de slag te gaan.

### <a name="embedded-device-development"></a>Ontwikkeling van Inge sloten apparaten
Inge sloten ontwikkeling is gericht op apparaten met beperkte geheugen en verwerking. Beperkte apparaten beperken wat kan worden bereikt in vergelijking met een traditioneel ontwikkel platform.

Inge sloten apparaten gebruiken meestal een realtime besturings systeem (RTO'S) of helemaal geen besturings systeem. Inge sloten apparaten hebben volledige controle over hun hardware vanwege het ontbreken van een besturings systeem voor algemene doel einden. Dit betekent dat Inge sloten apparaten een goede keuze zijn voor realtime-systemen.

De huidige ingebouwde Sdk's richten zich op de **C** -taal. De Inge sloten Sdk's bieden een besturings systeem of ondersteuning voor Azure RTO'S. Ze zijn ontworpen met Inge sloten doelen in het oog. De overwegingen voor het ontwerpen omvatten de nood zaak van een minimale footprint en een ontwerp dat niet geheugen wordt toegewezen.

Als uw apparaat een besturings systeem voor algemeen gebruik kan uitvoeren, kunt u het beste het ontwikkelings traject van de [Device Application](#device-application-development) volgen. Het biedt een uitgebreidere reeks ontwikkel opties.

> [!TIP]
> Bekijk de [sdk's van beperkt apparaat](about-iot-sdks.md#constrained-device-sdks) om aan de slag te gaan.

## <a name="choosing-your-hardware"></a>Uw hardware kiezen
Azure IoT-apparaten zijn de basis bouwstenen van een IoT-oplossing en zijn verantwoordelijk voor het observeren en communiceren met hun omgeving. Er zijn veel verschillende typen IoT-apparaten en het is handig om te begrijpen welke soorten apparaten er bestaan en hoe dit van invloed kan zijn op uw ontwikkelings proces.

Meer informatie over het verschil tussen de typen apparaten die in dit artikel worden behandeld, vindt u in de [typen IOT-apparaten](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Een SDK kiezen
Als Azure IoT-apparaat-ontwikkelaar beschikt u over een gevarieerde set Sdk's voor apparaten en Azure-service-Sdk's, om u te helpen bij het bouwen van Cloud toepassingen met een apparaat. De Sdk's stroom lijnen uw ontwikkelings inspanning en vereenvoudigen veel van de complexiteit van het aansluiten en beheren van apparaten. 

Zoals aangegeven in de sectie met de functies voor het [ontwikkelen van apparaten](#device-development-roles) , zijn er drie soorten IOT-sdk's voor het ontwikkelen van apparaten:
- Sdk's voor Inge sloten apparaten (voor beperkte apparaten)
- Sdk's voor apparaten (voor het gebruik van hogere bestel talen om bestaande apparaten te verbinden met IoT-toepassingen)
- Service-Sdk's (voor het bouwen van Azure IoT-oplossingen waarmee apparaten worden verbonden met Services)

Zie [overzicht van de sdk's van Azure IOT-apparaten](about-iot-sdks.md)voor meer informatie over het kiezen van een Azure IOT-apparaat of Service-SDK.

## <a name="selecting-connection-options"></a>Verbindings opties selecteren
Een belang rijke stap in het ontwikkelings proces is het kiezen van de set opties die u gaat gebruiken om verbinding te maken met uw apparaten en deze te beheren. Er zijn twee belang rijke aspecten waarmee u rekening moet houden:
- Een IoT-toepassings platform kiezen om uw apparaten te hosten. Voor Azure IoT betekent dit het kiezen van IoT Hub of IoT Central.
- Hulp middelen voor ontwikkel aars kiezen om apparaten te verbinden, te beheren en te bewaken.

Zie [overzicht: verbindings opties voor Azure IOT-apparaat-ontwikkel aars](concepts-overview-connection-options.md)voor meer informatie over het selecteren van een toepassings platform en-hulpprogram ma's.

## <a name="next-steps"></a>Volgende stappen
Selecteer een van de volgende Quick Start-serie die het meest relevant is voor uw ontwikkelings functie. In deze artikelen wordt gedemonstreerd hoe u een Azure IoT-toepassing kunt maken om apparaten te hosten, een SDK te gebruiken, een apparaat te verbinden en telemetrie te verzenden.  
- Voor het ontwikkelen van toepassings apparaten:  [Quick Start: verzend telemetrie van een apparaat naar Azure IOT Central](quickstart-send-telemetry-python.md)
- Voor het ontwikkelen van Inge sloten apparaten: [aan de slag met het ontwikkelen van Azure IOT embedded-apparaten](quickstart-device-development.md)
