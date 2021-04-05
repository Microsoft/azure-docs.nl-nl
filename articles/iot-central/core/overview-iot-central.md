---
title: Wat is Azure IoT Central? | Microsoft Docs
description: Azure IoT Central is een platform voor IoT-toepassingen dat het maken van IoT-oplossingen vereenvoudigt en dat helpt om de overhead en kosten van bewerkingen voor IoT-beheer en-ontwikkeling te verminderen. In dit artikel vindt u een overzicht van de functies van Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: e6971fa129c01fc4cdf64e0dc681ac0fab4f707b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833810"
---
# <a name="what-is-azure-iot-central"></a>Wat is Azure IoT Central?

IoT Central is een platform voor IoT-toepassingen dat de inzet en kosten verlaagt die gepaard gaan met het ontwikkelen, beheren en onderhouden van geavanceerde IoT-oplossingen. Als u ervoor kiest om toepassingen met IoT Central te bouwen, hebt u de mogelijkheid om tijd, geld en energie te besteden aan de transformatie van uw bedrijf met IoT-gegevens, in plaats van alleen maar een complexe en voortdurend veranderende IoT-infrastructuur te onderhouden en bij te werken.

Via de webinterface kunt u de status van apparaten bewaken, regels maken en miljoenen apparaten en hun gegevens beheren gedurende de volledige levenscyclus. Daarnaast kunt u met de service reageren op inzichten over apparaten door IoT-intelligentie uit te breiden naar LOB-toepassingen.

In dit artikel worden de volgende onderwerpen besproken voor IoT Central:

- De typische persona's die aan een project zijn gekoppeld.
- Het maken van uw toepassing.
- Het maken van verbinding tussen uw apparaten en uw toepassing
- Het beheren van uw toepassing.
- Mogelijkheden van Azure IoT Edge in IoT Central.
- Hoe u uw Azure IoT Edge runtime-apparaten verbindt met uw toepassing.

## <a name="personas"></a>Persona's

In de documentatie over IoT Central wordt verwezen naar vier persona's die interactie hebben met een IoT Central-toepassing:

- Een _bouwer van oplossingen_ is verantwoordelijk voor [het maken van een toepassing](quick-deploy-iot-central.md), [het configureren van regels en acties](quick-configure-rules.md), [het definiëren van integraties met andere services](howto-export-data.md), en het verder aanpassen van de toepassing voor operators en apparaatontwikkelaars.
- Een _operator_ [beheert de apparaten](howto-manage-devices.md) die met de toepassing zijn verbonden.
- Een _beheerder_ is verantwoordelijk voor beheertaken zoals het beheer van [gebruikersrollen en -machtigingen](howto-administer.md) binnen de toepassing.
- Een _apparaatontwikkelaar_ [maakt de code die wordt uitgevoerd op een apparaat](concepts-telemetry-properties-commands.md) of in een [IoT Edge-module](concepts-iot-edge.md) die met uw toepassing is verbonden.

## <a name="create-your-iot-central-application"></a>Een IoT Central-toepassing maken

Als bouwer van oplossingen gebruikt u IoT Central om een aangepaste, in de cloud gehoste IoT-oplossing voor uw organisatie te maken. Een aangepaste IoT-oplossing bestaat meestal uit:

- Een cloudtoepassing die telemetriegegevens van uw apparaten ontvangt en die u in staat stelt om deze apparaten te beheren.
- Meerdere apparaten waarop aangepaste code wordt uitgevoerd en die met uw cloudtoepassing zijn verbonden.

U kunt een nieuwe IoT Central-toepassing snel implementeren en deze vervolgens in uw browser aanpassen aan uw specifieke vereisten. U kunt beginnen met een algemene _toepassingssjabloon_ of met een van de toepassingssjablonen voor de specifieke branches [Detailhandel](../retail/overview-iot-central-retail.md), [Energie](../energy/overview-iot-central-energy.md), [Overheid](../government/overview-iot-central-government.md)of [Gezondheidszorg](../healthcare/overview-iot-central-healthcare.md).

Als bouwer van oplossingen gebruikt u de webgebaseerde hulpprogramma's om een _apparaatsjabloon_ te maken voor de apparaten die verbinding maken met uw toepassing. Een apparaatsjabloon is de blauwdruk die de eigenschappen en het gedrag van een bepaald type apparaat definieert, zoals:

- De telemetriegegevens die worden verstuurd. Voorbeelden zijn temperatuur en vochtigheid. Telemetrie bestaat uit het streamen van gegevens.
- Bedrijfseigenschappen die kunnen worden gewijzigd door een operator. Voorbeelden zijn een klantadres en een datum van laatste service.
- Apparaateigenschappen die zijn ingesteld door een apparaat en die het kenmerk alleen-lezen hebben in de toepassing. Bijvoorbeeld de status van een klep: open of gesloten.
- Eigenschappen, die door een operator worden ingesteld, waarmee het gedrag van het apparaat wordt bepaald. Bijvoorbeeld een doeltemperatuur voor het apparaat.
- Opdrachten die een operator kan aanroepen en die op een apparaat worden uitgevoerd. Bijvoorbeeld een opdracht om een apparaat op afstand opnieuw op te starten.

Deze [apparaatsjabloon](howto-set-up-template.md) omvat:

- Een _apparaatmodel_ dat de mogelijkheden beschrijft waarmee een apparaat moet worden geïmplementeerd. De apparaatmogelijkheden zijn:

  - De telemetrie die naar IoT Central wordt gestreamd.
  - De alleen-lezeneigenschappen die worden gebruikt om de status aan IoT Central te rapporteren.
  - De beschrijfbare eigenschappen die van IoT Central worden ontvangen om de apparaatstatus in te stellen.
  - De opdrachten die vanuit IoT Central worden aangeroepen.

- Cloudeigenschappen die niet op het apparaat zijn opgeslagen.
- Aanpassingen, dashboards en formulieren die deel uitmaken van uw IoT Central-toepassing.

### <a name="create-device-templates"></a>Apparaatsjablonen maken

Als bouwer van oplossingen hebt u verschillende opties voor het maken van apparaatsjablonen:

- Ontwerp de apparaatsjabloon in IoT Central en implementeer vervolgens het apparaatmodel in de code van uw apparaat.
- Maak een apparaatmodel met behulp van Visual Studio-code en publiceer het model naar een opslagplaats. Implementeer uw apparaatcode vanuit het model en verbind uw apparaat met uw IoT Central-toepassing. IoT Central vindt het apparaatmodel in de opslagplaats en maakt een eenvoudige apparaatsjabloon voor u.
- Een apparaatmodel maken met Visual Studio Code. Implementeer uw apparaatcode vanuit het model. Importeer het apparaatmodel handmatig in uw IoT Central-toepassing en voeg vervolgens alle cloudeigenschappen, aanpassingen en dashboards toe die uw IoT Central-toepassing nodig heeft.

Als bouwer van oplossingen kunt u IoT Central gebruiken om code te genereren voor testapparaten om uw apparaatsjablonen te valideren.

Als u een ontwikkelaar van apparaten bent, raadpleegt u [Overzicht van het ontwikkelen van IoT Central-apparaten](./overview-iot-central-developer.md) voor een inleiding tot de implementatie van apparaten die gebruikmaken van deze apparaatsjablonen.

### <a name="customize-the-ui"></a>De gebruikersinterface aanpassen

Als bouwer van oplossingen kunt u de gebruikersinterface van de IoT Central-toepassing ook aanpassen voor de operators die verantwoordelijk zijn voor het dagelijks gebruik van de toepassing. Dit zijn enkele voorbeelden van aanpassingen die een bouwer van oplossingen kan doen:

- Het definiëren van de indeling van eigenschappen en instellingen in een apparaatsjabloon.
- Het configureren van aangepaste dashboards, zodat operators nieuwe inzichten kunnen krijgen en problemen sneller kunnen oplossen.
- Het configureren van aangepaste analyses om tijdseriegegevens van uw verbonden apparaten te verkennen.

## <a name="manage-your-devices"></a>Uw apparaten beheren

Als operator gebruikt u de IoT Central-toepassing om [de apparaten te beheren](howto-manage-devices.md) in uw IoT Central-oplossing. Operators voeren taken uit zoals:

- Het controleren van de apparaten die met de toepassing zijn verbonden.
- Het oplossen en verhelpen van problemen met apparaten.
- Het inrichten van nieuwe apparaten.

Als bouwer van oplossingen kunt u [aangepaste regels en acties definiëren](howto-configure-rules.md) die worden toegepast voor het streamen van gegevens vanaf verbonden apparaten. Een operator kan deze regels op apparaatniveau in- of uitschakelen om taken binnen de toepassing te beheren en automatiseren.

Voor elke IoT-oplossing die is ontworpen voor gebruik op schaal, is een gestructureerde aanpak van apparaatbeheer belangrijk. Het is niet voldoende om uw apparaten alleen maar te verbinden met de cloud. U moet ervoor zorgen dat uw apparaten verbonden blijven en goed blijven werken. Een operator kan de volgende mogelijkheden van IoT Central gebruiken om uw apparaten te beheren gedurende de levenscyclus van de toepassing:

### <a name="dashboards"></a>Dashboards

Ingebouwde [dashboards](./howto-set-up-template.md#generate-default-views) bieden een aanpasbare gebruikersinterface voor het bewaken van de status en telemetrie van apparaten. Begin met een vooraf gebouwd dashboard in een [toepassingssjabloon](howto-use-app-templates.md) of maak uw eigen dashboards die speciaal zijn afgestemd op de behoeften van uw operators. U kunt dashboards delen met alle gebruikers in uw toepassing of ze privé houden.

### <a name="rules-and-actions"></a>Regels en acties

Definieer [aangepaste regels](tutorial-create-telemetry-rules.md) op basis van apparaatstatus en telemetrie om vast te stellen welke apparaten aandacht nodig hebben. Configureer acties om de juiste personen te waarschuwen en ervoor te zorgen dat op het juiste moment de juiste maatregelen worden genomen.

### <a name="jobs"></a>Taken

Met [taken](howto-run-a-job.md) kunt u losse of bulksgewijze updates toepassen op apparaten door eigenschappen in te stellen of opdrachten aan te roepen.

## <a name="integrate-with-other-services"></a>Integreren met andere services

Als een toepassingsplatform kunt u IoT Central gebruiken om uw IoT-gegevens te transformeren in de zakelijke inzichten die toepasbare resultaten opleveren. [Regels](./tutorial-create-telemetry-rules.md), [gegevensexport](./howto-export-data.md) en de [openbare REST-API](/learn/modules/manage-iot-central-apps-with-rest-api/) zijn voorbeelden van manieren om IoT Central te integreren met LOB-toepassingen:

![Hoe IoT Central uw IoT-gegevens kan transformeren](media/overview-iot-central/transform.png)

U kunt zakelijke inzichten genereren, zoals het vaststellen van efficiencytrends voor machines of het voorspellen van toekomstig energieverbruik in een fabriek, door het samenstellen van aangepaste analysepijplijnen voor de verwerking van telemetrie van uw apparaten en het opslaan van de resultaten. Configureer gegevensexports in uw IoT Central-toepassing voor het exporteren van telemetrie en wijzigingen van apparaateigenschappen en apparaatsjablonen naar andere services waar u de gegevens kunt analyseren, opslaan en visualiseren met de tools die u het liefst gebruikt.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Aangepaste IoT-oplossingen en -integraties bouwen met de REST-API's

U kunt IoT-oplossingen bouwen zoals:

- Mobiele versies van apps om apparaten op afstand in te stellen en beheren.
- Aangepaste integraties die bestaande LOB-toepassingen in staat stellen om interactie te hebben met uw IoT-apparaten en -gegevens.
- Toepassingen voor apparaatbeheer voor apparaatmodellering, onboarding, beheer en gegevenstoegang.

## <a name="administer-your-application"></a>Uw toepassing beheren

IoT Central-toepassingen worden volledig gehost door Microsoft, waardoor de overheadkosten voor het beheer van uw toepassingen worden verlaagd. Beheerders beheren de toegang tot uw toepassing met [gebruikersrollen en -machtigingen](howto-administer.md).

## <a name="pricing"></a>Prijzen

U kunt IoT Central-toepassingen maken met een gratis proefversie van zeven dagen of u kunt een betaald abonnement gebruiken.

- Toepassingen die u maakt met het *gratis* abonnement zijn gratis gedurende zeven dagen en ondersteunen maximaal vijf apparaten. U kunt ze op enig moment voor het aflopen van het abonnement overzetten naar een betaald abonnement.
- Toepassingen die u maakt met een *Standard*-abonnement worden gefactureerd per apparaat. U kunt kiezen tussen **Standard 0**, **Standard 1** en **Standard 2**, waarbij de eerste twee apparaten gratis zijn. Ga voor meer informatie naar [Prijzen voor IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="quotas"></a>Quota

Voor elk Azure-abonnement gelden standaardquota die invloed kunnen hebben op het bereik van uw IoT-oplossing. Op dit moment is het aantal toepassingen dat u kunt implementeren met een abonnement van IoT Central beperkt tot tien. Als u deze limiet wilt verhogen, kunt u contact opnemen met [Microsoft Ondersteuning](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Bekende problemen

- Continue gegevensexport is niet mogelijk voor de Avro-indeling (incompatibiliteit).
- GeoJSON wordt momenteel niet ondersteund.
- Kaarttegels worden momenteel niet ondersteund.
- Het schematype Matrix wordt niet ondersteund.
- Alleen de C-apparaat-SDK en de Node.js-apparaat- en service-SDK's worden ondersteund.
- IoT Central is momenteel beschikbaar in de Verenigde Staten, Europa, Azië en Stille Oceaan, Australië, Verenigd Koninkrijk en Japan.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Central hebt, zijn dit mogelijke volgende stappen:

- Als u apparaatontwikkelaar bent en u meer wilt weten over coderen, kunt u het beste de stap [Create and connect a client application to your Azure IoT Central application](./tutorial-connect-device.md) (Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing) volgen.
- Raak vertrouwd met de [gebruikersinterface van Azure IoT Central](overview-iot-central-tour.md).
- Ga aan de slag door [een Azure IoT Central-toepassing te maken](quick-deploy-iot-central.md).
