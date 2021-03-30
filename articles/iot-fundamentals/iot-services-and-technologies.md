---
title: Technologieën en oplossingen voor Azure IoT (Internet of Things)
description: Beschrijft de verzameling technologieën en services die u kunt gebruiken om een Azure IoT-oplossing te bouwen.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0cd98705d2d6947ff766f528e4e92f1e51bcc42d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852809"
---
# <a name="what-azure-technologies-and-services-can-you-use-to-create-iot-solutions"></a>Welke Azure-technologieën en -services kunt u gebruiken om IoT-oplossingen te maken?

Azure IoT-technologieën en -services bieden u opties voor het maken van een breed scala aan IoT-oplossingen waarmee digitale transformatie voor uw organisatie mogelijk wordt. U kunt bijvoorbeeld:

* Gebruik [Azure IoT Central](https://apps.azureiotcentral.com), een beheerd IoT-toepassingsplatform voor het bouwen en implementeren van een veilige IoT-oplossing op bedrijfsniveau. IoT Central bevat een verzameling branchespecifieke toepassingssjablonen, zoals voor de detailhandel en gezondheidszorg, om het ontwikkelingsproces van uw oplossing te versnellen.
* Gebruik Azure IoT-platformservices zoals [Azure IoT Hub](../iot-hub/about-iot-hub.md) en de [Azure IoT-apparaat-SDK's](../iot-hub/iot-hub-devguide-sdks.md) om een aangepaste IoT-oplossing helemaal zelf te bouwen.

![Azure IoT-technologieën, -services en -oplossingen](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

Het [IoT Central toepassingsplatform](https://apps.azureiotcentral.com) verlaagt de inzet en kosten die gepaard gaan met het ontwikkelen, beheren en onderhouden van IoT-oplossingen op bedrijfsniveau. Via de aanpasbare webinterface van IoT Central kunt u apparaattoestanden bewaken, regels maken en miljoenen apparaten en hun gegevens beheren gedurende de volledige levenscyclus. Het API-gebied in IoT Central biedt u programmatische toegang om uw IoT-oplossing te configureren en te gebruiken.

Azure IoT Central is een volledig beheerd toepassingsplatform dat u kunt gebruiken om aangepaste IoT-oplossingen te maken. IoT Central gebruikt toepassingssjablonen om oplossingen te maken. Er zijn sjablonen voor algemene oplossingen en voor specifieke branches, zoals energie, gezondheidszorg, overheid en detailhandel. Met toepassingssjablonen van IoT Central kunt u in slechts enkele minuten een IoT Central-toepassing implementeren die u vervolgens met behulp van thema's, dashboards en weergaven kunt aanpassen.

Kies apparaten in de [Catalogus voor Azure Certified for IoT-apparaten](https://catalog.azureiotsolutions.com) om snel verbinding te maken met uw oplossing. Gebruik de gebruikersinterface van IoT Central om uw apparaten te controleren en beheren om deze gezond en verbonden te houden. Gebruik connectors en API's om uw IoT Central-toepassing te integreren met andere zakelijke toepassingen.

Als volledig beheerd toepassingsplatform heeft IoT Central een eenvoudig, voorspelbaar prijsmodel.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT-oplossingsversnellers

De [Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com) zijn een verzameling aanpasbare oplossingen op bedrijfsniveau. U kunt deze oplossingen implementeren zoals ze zijn, of een aangepaste IoT-oplossing ontwikkelen met behulp van de open-source Java- of .NET-broncode.

Azure IoT-oplossingsverbeteringen bieden een hoog niveau van controle over uw IoT-oplossing. De oplossingsverbeteringen bevatten vooraf ontwikkelde oplossingen voor veelvoorkomende IoT-scenario's die u binnen enkele minuten kunt implementeren in uw Azure-abonnement. De scenario's omvatten:

* Verbonden factory
* Apparaatsimulatie

De opensource-codebasis voor alle oplossingsverbeteringen is beschikbaar op GitHub. Download de code om een oplossingsverbetering aan te passen om te voldoen aan uw specifieke IoT-vereisten.

De oplossingsverbeteringen gebruiken Azure-services zoals Azure IoT Hub en Azure Storage die u moet beheren in uw Azure-abonnement.

## <a name="custom-solutions"></a>Aangepaste oplossingen

Gebruik een of meer van de volgende Azure IoT-technologieën en -services om een volledig nieuwe IoT-oplossing te bouwen of een oplossing uit te breiden die is gemaakt met IoT Central of een oplossingsverbetering:

### <a name="devices"></a>Apparaten

Ontwikkel uw IoT-apparaten met behulp van een van de [Azure IoT Starter Kits](https://catalog.azureiotsolutions.com/kits) of kies een apparaat dat u wilt gebruiken vanuit de [Azure Certified voor IoT-apparaatcatalogus](https://catalog.azureiotsolutions.com). Implementeer uw invoegcode met behulp van de opensource-[apparaat-SDK’s](../iot-hub/iot-hub-devguide-sdks.md). De SDK's van het apparaat ondersteunen meerdere besturingssystemen, zoals Linux, Windows en real-time besturingssystemen. Er zijn SDK's voor meerdere programmeertalen, zoals [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp)en [Python](https://github.com/Azure/azure-iot-sdk-python).

U kunt de manier waarop u de invoegcode voor uw apparaten maakt verder vereenvoudigen door de [IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md)-service te gebruiken. Met IoT Plug en Play kunnen ontwikkelaars van oplossingen apparaten met hun oplossingen integreren zonder invoegcode te hoeven schrijven. De kern van IoT Plug en Play is een schema voor het _apparaatondersteuningsmodel_ dat de mogelijkheden van het apparaat beschrijft. Gebruik het apparaatondersteuningsmodel om uw ingesloten apparaatcode te genereren en een cloudoplossing te configureren, zoals een IoT Central-toepassing.

Met [Azure IoT Edge](../iot-edge/about-iot-edge.md) kunt u delen van uw IoT-werkbelasting overzetten van uw Azure-cloudservices naar uw apparaten. IoT Edge kan de latentie in uw oplossing verminderen, de hoeveelheid gegevens die door uw apparaten worden uitgewisseld met de cloud verminderen en offline-scenario's inschakelen. U kunt IoT Edge-apparaten beheren vanuit IoT Central en enkele oplossingsverbeteringen.

[Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) is een beveiligd toepassingsplatform op hoog niveau met ingebouwde communicatie- en beveiligingsfuncties voor apparaten die zijn verbonden met internet. Het bevat een beveiligde microcontroller-eenheid, een aangepast Linux-besturingssysteem en een beveiligingsservice in de cloud die continue, hernieuwbare beveiliging biedt.

### <a name="cloud-connectivity"></a>Cloudconnectiviteit

De [Azure IoT Hub](../iot-hub/about-iot-hub.md)-service zorgt voor stabiele en veilige tweerichtingscommunicatie tussen miljoenen IoT-apparaten en een cloudoplossing. [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) is een ondersteuningsservice voor IoT Hub. De service voorziet in zero-touch, Just-In-Time inrichting van apparaten naar de juiste IoT Hub zonder tussenkomst van de gebruiker. Met deze mogelijkheden kunnen klanten miljoenen apparaten op een veilige en schaalbare manier inrichten.

IoT Hub is een kernonderdeel van de oplossingsverbeteringen en u kunt het gebruiken om te voldoen aan IoT-implementatieuitdagingen, zoals:

* Connectiviteit en beheer van grote volumes aan apparaten.
* Telemetrieopname van groot volume.
* Opdracht en besturing van apparaten.
* Afdwinging van apparaatbeveiliging.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Het overbruggen van de ruimte tussen de fysieke en de digitale wereld

[Azure Digital Twins](../digital-twins/overview.md) is een IoT-service waarmee u een model van een fysieke omgeving kunt maken. Het maakt gebruik van een grafiek op basis van ruimtelijke intelligentie om de relaties tussen mensen, ruimten en apparaten te modelleren. Door gegevens uit de digitale en fysieke wereld te correleren, kunt u contextbewuste oplossingen maken.

IoT Central maakt gebruik van digitale dubbelen om apparaten en gegevens in de echte wereld te synchroniseren met de digitale modellen waarmee gebruikers deze aangesloten apparaten kunnen bewaken en beheren.

### <a name="data-and-analytics"></a>Gegevens en analyse

IoT-apparaten genereren doorgaans grote hoeveelheden tijdreeksgegevens, zoals temperatuurmetingen van sensoren. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) kan verbinding maken met een IoT-hub, de telemetriegegevensstroom van uw apparaten lezen, die gegevens opslaan en u in staat stellen om hier query's op uit te voeren en het te visualiseren.

[Azure Maps](../azure-maps/index.yml) is een verzameling georuimtelijke services die nieuwe kaartgegevens gebruiken om webtoepassingen en mobiele toepassingen te voorzien van een nauwkeurige geografische context. U kunt een REST API, een JavaScript-besturingselement op het web of een Android-SDK gebruiken om uw toepassingen te bouwen.

## <a name="next-steps"></a>Volgende stappen

Probeer een van de snelstarts voor een praktijkervaring:

- [Een Azure IoT Central-toepassing maken](../iot-central/core/quick-deploy-iot-central.md)
- [Telemetrie verzenden van een apparaat naar een IoT-hub](../iot-hub/quickstart-send-telemetry-cli.md)
