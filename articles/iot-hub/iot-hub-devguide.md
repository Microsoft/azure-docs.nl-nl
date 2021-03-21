---
title: Ontwikkelaars handleiding voor Azure IoT Hub | Microsoft Docs
description: De ontwikkelaars handleiding voor Azure IoT Hub bevat discussies over eind punten, beveiliging, het identiteits register, Apparaatbeheer, directe methoden, apparaatdubbels, uploads van bestanden, taken, de IoT Hub query taal en berichten.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 1fcf560ba1fef137a28ab1872635cc2182cd07e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009195"
---
# <a name="azure-iot-hub-developer-guide"></a>Ontwikkelaars handleiding voor Azure IoT Hub

Azure IoT Hub is een volledig beheerde service die zorgt voor betrouw bare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub biedt u het volgende:

* Beveiligde communicaties met behulp van beveiligings referenties per apparaat en toegangs beheer.

* Meerdere communicatie opties voor apparaat-naar-Cloud en Cloud-naar-apparaat.

* Een query uitvoeren op de status informatie en meta gegevens per apparaat.

* Eenvoudige connectiviteit van apparaten met Apparaatbeheer voor de populairste talen en platformen.

Deze IoT Hub ontwikkelaars handleiding bevat de volgende artikelen:

* [Met apparaat-naar-Cloud-communicatie richtlijnen](iot-hub-devguide-d2c-guidance.md) kunt u kiezen tussen apparaat-naar-Cloud-berichten, de gerapporteerde eigenschappen van het apparaat en het uploaden van bestanden.

* Via [Cloud-naar-apparaat communicatie richtlijnen](iot-hub-devguide-c2d-guidance.md) kunt u kiezen tussen directe methoden, de gewenste eigenschappen van het apparaat en de Cloud-naar-apparaat-berichten.

* [Met het apparaat-naar-Cloud-en Cloud-naar-apparaat-berichten met IOT hub](iot-hub-devguide-messaging.md) worden de berichten functies (apparaat-naar-Cloud en Cloud-naar-apparaat) beschreven die IOT hub beschikbaar maakt.

  * [Apparaat-naar-Cloud-berichten naar IOT hub verzenden](iot-hub-devguide-messages-d2c.md).

  * [Apparaat-naar-Cloud-berichten lezen van het ingebouwde eind punt](iot-hub-devguide-messages-read-builtin.md).

  * [Gebruik aangepaste eind punten en routerings regels voor apparaat-naar-Cloud-berichten](iot-hub-devguide-messages-read-custom.md).

  * [Cloud-naar-apparaat-berichten verzenden vanuit IOT hub](iot-hub-devguide-messages-c2d.md).

  * [IOT hub berichten maken en lezen](iot-hub-devguide-messages-construct.md).

* [Bestanden uploaden vanaf een apparaat](iot-hub-devguide-file-upload.md) beschrijft hoe u bestanden van een apparaat kunt uploaden. Het artikel bevat ook informatie over onderwerpen, zoals de meldingen die het upload proces kan verzenden.

* [Apparaat-Id's beheren in IOT hub](iot-hub-devguide-identity-registry.md) wordt beschreven welke gegevens elk id-REGI ster van de IOT-hub bevat. In dit artikel wordt ook beschreven hoe u deze kunt openen en wijzigen.

* [Toegang tot IOT hub beheersen](iot-hub-devguide-security.md) het beveiligings model dat wordt gebruikt om toegang te verlenen tot IOT hub functionaliteit voor zowel apparaten als Cloud onderdelen. Het artikel bevat informatie over het gebruik van tokens en X. 509-certificaten en Details van de machtigingen die u kunt verlenen.

* [Apparaatdubbels gebruiken om status en configuraties te synchroniseren,](iot-hub-devguide-device-twins.md) wordt het *apparaat-dubbele* concept beschreven. In dit artikel wordt ook de functionaliteit van het apparaat apparaatdubbels beschikbaar weer gegeven, zoals het synchroniseren van een apparaat met het apparaat twee. Het artikel bevat informatie over de gegevens die zijn opgeslagen op een apparaat, twee.

* [Een rechtstreekse methode aanroepen op een apparaat](iot-hub-devguide-direct-methods.md) Hiermee wordt de levens cyclus van een directe methode beschreven. In dit artikel wordt beschreven hoe u methoden kunt aanroepen op een apparaat vanuit uw back-end-app en de directe methode op het apparaat kunt afhandelen.

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md) beschrijft hoe u taken op meerdere apparaten kunt plannen. In het artikel wordt beschreven hoe u taken kunt verzenden waarmee taken worden uitgevoerd als het uitvoeren van een directe methode, het bijwerken van een apparaat met behulp van een apparaat twee. Ook wordt beschreven hoe u de status van een taak doorzoekt.

* [Referentie-Kies een communicatie protocol](iot-hub-devguide-protocols.md) beschrijft de communicatie protocollen die IOT hub ondersteunt voor de communicatie van apparaten en geeft een lijst van de poorten die moeten worden geopend.

* [Referentie-IOT hub-eind punten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eind punten die elke IOT-hub beschikbaar maakt voor runtime-en beheer bewerkingen. In dit artikel wordt ook beschreven hoe u extra eind punten kunt maken in uw IoT-hub en hoe u een veld Gateway gebruikt om connectiviteit met uw IoT Hub-eind punten in niet-standaard scenario's in te scha kelen.

* Met [referentie-IOT hub-query taal voor apparaatdubbels, Jobs en bericht routering](iot-hub-devguide-query-language.md) wordt aangegeven dat IOT hub query taal waarmee u informatie kunt ophalen van uw hub over de apparaatdubbels en taken van uw apparaat.

* [Referentie-quota en beperking](iot-hub-devguide-quotas-throttling.md) zijn een samen vatting van de quota's die zijn ingesteld in de IOT hub-service en de beperking die optreedt wanneer u een quotum overschrijdt.

* [Referentie-prijzen](iot-hub-devguide-pricing.md) bieden algemene informatie over de verschillende sku's en prijzen voor IOT hub en Details over de manier waarop de verschillende IOT hub functionaliteiten als berichten worden gemeten door IOT hub.

* [Naslag informatie over apparaat-en service-sdk's](iot-hub-devguide-sdks.md) bevat de Azure IOT-sdk's voor het ontwikkelen van apparaat-en service-apps die communiceren met uw IOT-hub. Het artikel bevat koppelingen naar online API-documentatie.

* [Referentie-IOT hub MQTT-ondersteuning](iot-hub-mqtt-support.md) bevat gedetailleerde informatie over hoe IOT hub het MQTT-protocol ondersteunt. In dit artikel wordt de ondersteuning voor het MQTT-protocol ingebouwd in de Azure IoT-Sdk's beschreven en vindt u informatie over het rechtstreeks gebruik van het MQTT-protocol.
