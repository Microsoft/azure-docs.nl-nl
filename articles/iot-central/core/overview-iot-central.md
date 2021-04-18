---
title: Wat is Azure IoT Central? | Microsoft Docs
description: IoT Central is een gehost IoT-app-platform dat veilig is, met u meeschaalt naarmate uw bedrijf groeit en integreert met uw bestaande zakelijke apps. In dit artikel vindt u een overzicht van de functies van Azure IoT Central.
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 1ff8243f9f4d070592ae6dc99e7c33472a6a90de
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600439"
---
# <a name="what-is-azure-iot-central"></a>Wat is Azure IoT Central?

IoT Central is een gehost IoT-app-platform dat veilig is, met u meeschaalt naarmate uw bedrijf groeit en integreert met uw bestaande zakelijke apps. Als u ervoor kiest om toepassingen met IoT Central te bouwen, hebt u de mogelijkheid om tijd, geld en energie te besteden aan de transformatie van uw bedrijf met IoT-gegevens, in plaats van alleen maar een complexe en voortdurend veranderende IoT-infrastructuur te onderhouden en bij te werken.

IoT Central kunt u snel apparaten verbinden, apparaatvoorwaarden bewaken, regels maken en miljoenen apparaten en hun gegevens gedurende hun levenscyclus beheren. Daarnaast kunt u met de service reageren op inzichten over apparaten door IoT-intelligentie uit te breiden naar LOB-toepassingen.

## <a name="create-your-iot-central-application"></a>Een IoT Central-toepassing maken

U kunt snel een nieuwe IoT Central maken en deze vervolgens aanpassen aan uw unieke vereisten. U kunt beginnen met  een algemene toepassingssjabloon of met een van de branchegerichte toepassingssjablonen voor [Retail,](../retail/overview-iot-central-retail.md) [Energy,](../energy/overview-iot-central-energy.md) [Government](../government/overview-iot-central-government.md)of [Healthcare.](../healthcare/overview-iot-central-healthcare.md)

Zie de [quickstart Een nieuwe toepassing](quick-deploy-iot-central.md) maken voor een overzicht van het maken van uw eerste toepassing.

## <a name="connect-your-devices"></a>Uw apparaten verbinden
Nadat u uw toepassing hebt aanmaken, bestaat de eerste stap uit het verbinden van uw apparaten. Zie overzicht [van apparaatontwikkeling voor](./overview-iot-central-developer.md) een inleiding tot het verbinden van apparaten met uw IoT Central toepassing.

### <a name="device-templates"></a>Apparaatsjablonen

Apparaten in IoT Central zijn gekoppeld aan een _apparaatsjabloon_. Een apparaatsjabloon lijkt op een blauwdruk: deze definieert de kenmerken en het gedrag van uw apparaten, zoals:

- Telemetriegegevens, die metingen van sensoren vertegenwoordigen, bijvoorbeeld temperatuur of vochtigheid.
- Eigenschappen, die de duurzame status van een apparaat vertegenwoordigen. Voorbeelden zijn de status van een koelpomp of doeltemperatuur voor een apparaat. U kunt eigenschappen declaren als alleen-lezen of beschrijfbaar. Alleen apparaten kunnen de waarde van een alleen-lezen eigenschap bijwerken. Een operator kan de waarde van een beschrijfbare eigenschap instellen om naar een apparaat te verzenden.
- Opdrachten, bewerkingen die op een apparaat kunnen worden geactiveerd, bijvoorbeeld een opdracht om een apparaat op afstand opnieuw op te starten.
- Cloudeigenschappen, die metagegevens van apparaten zijn die moeten worden opgeslagen in de IoT Central toepassing, bijvoorbeeld het klantadres of de laatste servicedatum.

Zie het [artikel Een apparaatsjabloon](howto-set-up-template.md) maken voor meer informatie.

### <a name="customize-the-ui"></a>De gebruikersinterface aanpassen

U kunt de IoT Central aanpassen voor de operators die verantwoordelijk zijn voor het dagelijkse gebruik van de toepassing, bijvoorbeeld:

- Het configureren van aangepaste dashboards, zodat operators nieuwe inzichten kunnen krijgen en problemen sneller kunnen oplossen.


## <a name="manage-your-devices"></a>Uw apparaten beheren


Voor elke IoT-oplossing die is ontworpen voor gebruik op schaal, is een gestructureerde aanpak van apparaatbeheer belangrijk. Het is niet voldoende om uw apparaten alleen maar te verbinden met de cloud. U moet ervoor zorgen dat uw apparaten verbonden blijven en goed blijven werken.

U kunt [de apparaten beheren met](howto-manage-devices.md) behulp van IoT Central toepassing om taken uit te voeren, zoals:

- De apparaten bewaken.
- Het oplossen en verhelpen van problemen met apparaten.
- Voer bulksgewijs updates uit op apparaten.

### <a name="dashboards"></a>Dashboards

Ingebouwde [dashboards](./howto-set-up-template.md#generate-default-views) bieden een aanpasbare gebruikersinterface voor het bewaken van de status en telemetrie van apparaten. Begin met een vooraf gebouwd dashboard in een [toepassingssjabloon](howto-use-app-templates.md) of maak uw eigen dashboards die speciaal zijn afgestemd op de behoeften van uw operators. U kunt dashboards delen met alle gebruikers in uw toepassing of ze privé houden.

### <a name="rules-and-actions"></a>Regels en acties

Definieer [aangepaste regels](tutorial-create-telemetry-rules.md) op basis van apparaatstatus en telemetrie om vast te stellen welke apparaten aandacht nodig hebben. Configureer acties om de juiste personen te waarschuwen en ervoor te zorgen dat op het juiste moment de juiste maatregelen worden genomen.

### <a name="jobs"></a>Taken

Met [taken](howto-run-a-job.md) kunt u losse of bulksgewijze updates toepassen op apparaten door eigenschappen in te stellen of opdrachten aan te roepen.

### <a name="analytics"></a>Analyse
[Analyse](howto-create-analytics.md) biedt uitgebreide mogelijkheden voor het analyseren van historische trends en het correleren van verschillende telemetriegegevens van uw apparaten.

## <a name="integrate-with-other-services"></a>Integreren met andere services

Als een toepassingsplatform kunt u IoT Central gebruiken om uw IoT-gegevens te transformeren in de zakelijke inzichten die toepasbare resultaten opleveren. [Regels](./tutorial-create-telemetry-rules.md), [gegevensexport](./howto-export-data.md) en de [openbare REST-API](/learn/modules/manage-iot-central-apps-with-rest-api/) zijn voorbeelden van manieren om IoT Central te integreren met LOB-toepassingen:

![Hoe IoT Central uw IoT-gegevens kan transformeren](media/overview-iot-central/transform.png)

U kunt zakelijke inzichten genereren, zoals het vaststellen van efficiencytrends voor machines of het voorspellen van toekomstig energieverbruik in een fabriek, door het samenstellen van aangepaste analysepijplijnen voor de verwerking van telemetrie van uw apparaten en het opslaan van de resultaten. Configureer gegevensexports in uw IoT Central om uw gegevens te exporteren naar andere services waar u deze kunt analyseren, opslaan en visualiseren met de hulpprogramma's van uw voorkeur.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Aangepaste IoT-oplossingen en -integraties bouwen met de REST-API's

U kunt IoT-oplossingen bouwen zoals:

- Mobiele versies van apps om apparaten op afstand in te stellen en beheren.
- Aangepaste integraties die bestaande LOB-toepassingen in staat stellen om interactie te hebben met uw IoT-apparaten en -gegevens.
- Toepassingen voor apparaatbeheer voor apparaatmodellering, onboarding, beheer en gegevenstoegang.

## <a name="administer-your-application"></a>Uw toepassing beheren

IoT Central-toepassingen worden volledig gehost door Microsoft, waardoor de overheadkosten voor het beheer van uw toepassingen worden verlaagd. Beheerders beheren de toegang tot uw toepassing met [gebruikersrollen en -machtigingen](howto-administer.md).

## <a name="pricing"></a>Prijzen

U kunt een IoT Central maken met een gratis proefversie van 7 dagen of een standaardprijsplan gebruiken.

- Toepassingen die u maakt met het *gratis* abonnement zijn gratis gedurende zeven dagen en ondersteunen maximaal vijf apparaten. U kunt ze op enig moment voor het aflopen van het abonnement overzetten naar een betaald abonnement.
- Toepassingen die u maakt met behulp van *het standaardplan,* worden per apparaat gefactureerd. U kunt het prijsplan **Standard 0,** **Standard 1** of **Standard 2** kiezen, omdat de eerste twee apparaten gratis zijn. Ga voor meer informatie naar [Prijzen voor IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Central hebt, zijn dit mogelijke volgende stappen:

- Ga aan de slag door [een Azure IoT Central-toepassing te maken](quick-deploy-iot-central.md).
- Raak vertrouwd met de [gebruikersinterface van Azure IoT Central](overview-iot-central-tour.md).
- Als u een apparaatontwikkelaar bent en wat code wilt gaan gebruiken, maakt en verbindt u een clienttoepassing met [uw Azure IoT Central toepassing](./tutorial-connect-device.md).
