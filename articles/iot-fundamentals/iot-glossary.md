---
title: Woorden lijst voor Azure IoT met termen | Microsoft Docs
description: 'Ontwikkelaars handleiding: een verklarende woorden lijst met een aantal algemene termen die worden gebruikt in de Azure IoT-artikelen.'
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: d7ae1e72dee28509c1338a1b56cf42a5293af9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104670253"
---
# <a name="glossary-of-iot-terms"></a>Woorden lijst met IoT-termen

In dit artikel vindt u een aantal algemene termen die worden gebruikt in de IoT-artikelen.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Protocol voor geavanceerde berichten wachtrijen

[Advanced Message queueing Protocol (AMQP)](https://www.amqp.org/) is een van de berichten protocollen die [IOT hub](#iot-hub) ondersteunt voor het communiceren met apparaten. Zie [berichten verzenden en ontvangen met IOT hub](../iot-hub/iot-hub-devguide-messaging.md)voor meer informatie over de berichten protocollen die door IOT hub worden ondersteund.

### <a name="allocation-policy"></a>Toewijzings beleid

In de [Device Provisioning Service](#device-provisioning-service)bepaalt het toewijzings beleid hoe de service apparaten toewijst aan [gekoppelde IOT-hubs](#linked-iot-hub).

### <a name="attestation-mechanism"></a>Attestation-mechanisme

In de [Device Provisioning Service](#device-provisioning-service)is het Attestation-mechanisme de methode die wordt gebruikt om de identiteit van een apparaat te bevestigen. Het Attestation-mechanisme wordt geconfigureerd voor een [registratie](#enrollment).

Attestation-mechanismen zijn onder andere X. 509-certificaten, Trusted platform modules en symmetrische sleutels.

### <a name="automatic-deployment"></a>Automatische implementatie

In IoT Edge configureert een automatische implementatie een doelset van IoT Edge apparaten om een set IoT Edge modules uit te voeren. Elke implementatie zorgt er continu voor dat alle apparaten die overeenkomen met de doel voorwaarde, de opgegeven set modules uitvoeren, zelfs wanneer er nieuwe apparaten worden gemaakt of gewijzigd in overeenstemming met de doel voorwaarde. Elk IoT Edge apparaat krijgt alleen de implementatie met de hoogste prioriteit waarvan de doel voorwaarde voldoet. Meer informatie over [IOT Edge automatische implementatie](../iot-edge/module-deployment-monitoring.md).

### <a name="automatic-device-configuration"></a>Automatische apparaatconfiguratie

De back-end van de oplossing kan [automatische hardwareconfiguraties](../iot-hub/iot-hub-automatic-device-management.md) gebruiken om de gewenste eigenschappen toe te wijzen aan een set [apparaatdubbels](#device-twin) en de status van het rapport met behulp van systeem metrieken en aangepaste metrische gegevens.

### <a name="automatic-device-management"></a>Automatisch Apparaatbeheer

Automatische Apparaatbeheer in azure IoT Hub automatiseert veel van de herhaalde en complexe taken van het beheer van grote apparaat vloots in de hele levens cyclus. Met automatisch Apparaatbeheer kunt u een set apparaten op basis van hun eigenschappen richten, een gewenste configuratie definiëren en apparaten IoT Hub bijwerken wanneer deze binnen het bereik vallen.  Bestaat uit [automatische configuraties van apparaten](../iot-hub/iot-hub-automatic-device-management.md) en [IOT Edge automatische implementaties](../iot-edge/how-to-deploy-at-scale.md).

### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Apparaatdubbels is een PaaS-aanbieding (platform as a Service) voor het maken van digitale weer gaven van concrete dingen, plaatsen, bedrijfs processen en personen. Bouw kennis grafieken die hele omgevingen vertegenwoordigen en gebruik ze om inzicht te krijgen in het verkrijgen van betere producten, het optimaliseren van bewerkingen en kosten en het maken van baanbrekende klant ervaringen. Zie [Azure Digital apparaatdubbels](../digital-twins/index.yml)voor meer informatie.

### <a name="azure-digital-twins-instance"></a>Azure Digital Apparaatdubbels-instantie

Eén exemplaar van de Azure Digital Apparaatdubbels-service in het abonnement van een klant. [Azure Digital apparaatdubbels](#azure-digital-twins) verwijst naar de Azure-service als geheel, uw Azure Digital apparaatdubbels- **instantie** is uw persoonlijke Azure Digital apparaatdubbels-bron.

### <a name="azure-iot-device-sdks"></a>Sdk's van Azure IoT-apparaat

Er zijn _apparaat-sdk's_ beschikbaar voor meerdere talen waarmee u [apps](#device-app) kunt maken die communiceren met een IOT-hub. De IoT Hub zelf studies laten zien hoe u deze apparaat-Sdk's kunt gebruiken. U vindt de bron code en meer informatie over de Sdk's van het apparaat in deze GitHub- [opslag plaats](https://github.com/Azure/azure-iot-sdks).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

[Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer) wordt gebruikt voor het weer geven van de telemetrie die het apparaat verzendt, het werken met apparaateigenschappen en het aanroepen van opdrachten. U kunt ook de Explorer gebruiken om te communiceren met en uw apparaten te testen en [IoT Plug en Play-apparaten](#iot-plug-and-play-device)te beheren.

### <a name="azure-iot-service-sdks"></a>Sdk's van Azure IoT-service

Er zijn _service-sdk's_ beschikbaar voor meerdere talen waarmee u [back-end-apps](#back-end-app) kunt maken die communiceren met een IOT-hub. De IoT Hub zelf studies laten zien hoe u deze service-Sdk's kunt gebruiken. U vindt de bron code en meer informatie over de Sdk's van de service in deze GitHub- [opslag plaats](https://github.com/Azure/azure-iot-sdks).

### <a name="azure-iot-tools"></a>Azure IoT Tools

De [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) is een platformoverschrijdende, open-source Visual Studio code-extensie waarmee u Azure IOT hub en-apparaten kunt beheren in VS code. Met Azure IoT-Hulpprogram Ma's kunnen IoT-ontwikkel aars IoT-project met gemak ontwikkelen in VS code.

## <a name="b"></a>B

### <a name="back-end-app"></a>Back-end-app

In de context van [IOT hub](#iot-hub)is een back-end-app een app die verbinding maakt met een van de service gerichte eind punten op een IOT-hub. Een back-end-app kan bijvoorbeeld [apparaat-naar-Cloud-](#device-to-cloud) berichten ophalen of het [identiteits register](#identity-registry)beheren. Normaal gesp roken wordt een back-end-app uitgevoerd in de Cloud, maar in veel van de zelf studies zijn de back-end-apps console-apps die worden uitgevoerd op uw lokale ontwikkel computer.

### <a name="built-in-endpoints"></a>Ingebouwde eind punten

Een type [eind punt](#endpoint) dat is ingebouwd in IOT hub. Elke IoT-hub bevat een ingebouwd [eind punt](../iot-hub/iot-hub-devguide-endpoints.md) dat compatibel is met Event hub. U kunt elk mechanisme gebruiken dat werkt met Event Hubs om apparaat-naar-Cloud-berichten van dit eind punt te lezen.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Cloud gateway

Een Cloud gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub). Een Cloud gateway wordt in de Cloud gehost in tegens telling tot een [veld Gateway](#field-gateway) die lokaal op uw apparaten wordt uitgevoerd. Een typische use-case voor een Cloud gateway is het implementeren van protocol vertalingen voor uw apparaten.

### <a name="cloud-to-device"></a>Cloud-naar-apparaat

Verwijst naar berichten die vanuit een IoT-hub naar een verbonden apparaat worden verzonden. Deze berichten zijn vaak opdrachten waarmee het apparaat een actie kan uitvoeren. Zie [berichten verzenden en ontvangen met IOT hub](../iot-hub/iot-hub-devguide-messaging.md)voor meer informatie.

### <a name="commands"></a>Opdracht

In IoT-Plug en Play vertegenwoordigen opdrachten die zijn gedefinieerd in een [Interface](#interface) , de methoden die kunnen worden uitgevoerd op het [digitale dubbele](#digital-twin). Bijvoorbeeld een opdracht om een apparaat opnieuw op te starten.

### <a name="component"></a>Onderdeel

Met onderdelen kunt u in IoT Plug en Play en Azure Digital Apparaatdubbels een model [Interface](#interface) bouwen als een assembly van andere interfaces. Een [apparaatprofiel](#device-model) kan meerdere interfaces als onderdelen combi neren. Zo kan een model een switch-onderdeel en een thermo staat bevatten. Meerdere onderdelen in een model kunnen ook hetzelfde interface type gebruiken. Zo kan een model bijvoorbeeld twee Thermo staat-onderdelen bevatten.

### <a name="configuration"></a>Configuratie

In de context van [automatische apparaatconfiguratie](../iot-hub/iot-hub-automatic-device-management.md), definieert een configuratie binnen IOT hub de gewenste configuratie voor een set apparaten apparaatdubbels en biedt een set metrische gegevens om de status en de voortgang te rapporteren.

### <a name="connection-string"></a>Verbindingsreeks

U gebruikt verbindings reeksen in uw app-code om de vereiste gegevens voor het maken van verbinding met een eind punt te integreren. Een connection string bevat normaal gesp roken het adres van het eind punt en de beveiligings gegevens, maar connection string indelingen variëren in verschillende services. Er zijn twee soorten connection string gekoppeld aan de IoT Hub-service:

- Met *verbindings reeksen* voor apparaten kunnen apparaten verbinding maken met de op het apparaat gerichte eind punten op een IOT-hub.

- Met *IOT hub verbindings reeksen* kunnen back-end-apps verbinding maken met de service gerichte eind punten op een IOT-hub.

### <a name="custom-endpoints"></a>Aangepaste eindpunten

U kunt aangepaste [eind punten](../iot-hub/iot-hub-devguide-endpoints.md) maken op een IOT-hub om berichten te leveren die worden verzonden door een [routerings regel](#routing-rules). Aangepaste eind punten verbinden rechtstreeks met een event hub, een Service Bus wachtrij of een Service Bus onderwerp.

### <a name="custom-gateway"></a>Aangepaste gateway

Een gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub). U kunt Azure IoT Edge gebruiken om aangepaste gateways te maken die aangepaste logica implementeren voor het afhandelen van berichten, aangepaste protocol conversies en andere verwerking aan de rand.

## <a name="d"></a>D

### <a name="data-point-message"></a>Bericht van gegevens punt

Een bericht van het gegevens punt is een [apparaat-naar-Cloud](#device-to-cloud) -bericht dat [telemetriegegevens](#telemetry) bevat, zoals wind snelheid of Tempe ratuur.

### <a name="default-component"></a>Standaard onderdeel

In IoT Plug en Play hebben alle [modellen van apparaten](#device-model) een standaard onderdeel. Een eenvoudig apparaatprofiel heeft alleen een standaard onderdeel. dit model wordt ook wel een onderdeel apparaat genoemd. Een complexere model heeft meerdere onderdelen genest onder het standaard onderdeel.

### <a name="deployment-manifest"></a>Distributiemanifest

In [IOT Edge](#iot-edge)is het implementatie manifest een JSON-document met de informatie die moet worden gekopieerd in een of meer IOT edge apparaten module dubbele (s) voor het implementeren van een set modules, routes en de gewenste eigenschappen van de module.

### <a name="desired-configuration"></a>Gewenste configuratie

In de context van een [apparaat dubbele](../iot-hub/iot-hub-devguide-device-twins.md), gewenste configuratie verwijst naar de volledige set eigenschappen en meta gegevens in het apparaat-dubbele dat moet worden gesynchroniseerd met het apparaat.

### <a name="desired-properties"></a>Gewenste eigenschappen

In de context van een [apparaat met dubbele](../iot-hub/iot-hub-devguide-device-twins.md), gewenste eigenschappen is een subsectie van het apparaat dat wordt gebruikt met [gerapporteerde eigenschappen](#reported-properties) om de apparaatconfiguratie of voor waarde te synchroniseren. Gewenste eigenschappen kunnen alleen worden ingesteld door een [back-end-app](#back-end-app) en worden waargenomen door de [apparaat-app](#device-app).

### <a name="device"></a>Apparaat

In de context van IoT is een apparaat doorgaans een klein, zelfstandig computer apparaat dat gegevens kan verzamelen of andere apparaten kan beheren. Een apparaat kan bijvoorbeeld een milieubewakings apparaat zijn of een controller voor de water-en ventilatie systemen in een broeikas. De [catalogus](https://catalog.azureiotsolutions.com/) met apparaten bevat een lijst met apparaten die zijn gecertificeerd voor gebruik met [IOT hub](#iot-hub).

### <a name="device-app"></a>Apparaat-app

Een apparaat-app wordt op uw [apparaat](#device) uitgevoerd en verwerkt de communicatie met uw [IOT-hub](#iot-hub). Normaal gesp roken gebruikt u een van de [Azure IOT-apparaat-sdk's](#azure-iot-device-sdks) wanneer u een apparaat-app implementeert. In veel van de IoT-zelf studies gebruikt u een [gesimuleerd apparaat](#simulated-device) voor het gemak.

### <a name="device-builder"></a>Opbouw functie voor apparaten

Een apparaat Builder gebruikt een [model](#device-model) en [interfaces](#interface) voor het uitvoeren van code voor uitvoering op een [IOT Plug en Play-apparaat](#iot-plug-and-play-device). Apparaat bouwers gebruiken meestal een van de [Azure IOT-apparaat-sdk's](#azure-iot-device-sdks) voor het implementeren van de apparaatclient.

### <a name="device-certification"></a>Apparaatcertificering

Het programma voor IoT Plug and Play-apparaatcertificering verifieert dat een apparaat voldoet aan de IoT Plug and Play-certificeringsvereisten. U kunt een gecertificeerd apparaat toevoegen aan de openbare [catalogus Gecertificeerd voor Azure IoT-apparaten](https://aka.ms/devicecatalog).

### <a name="device-condition"></a>Voorwaarde voor apparaat

Verwijst naar informatie over de status van het apparaat, zoals de verbindings methode die momenteel in gebruik is, zoals gerapporteerd door een [apparaat-app](#device-app). [Apparaat-apps](#device-app) kunnen ook hun mogelijkheden rapporteren. U kunt een query uitvoeren op informatie over de voor waarde en de capaciteit met behulp van Device apparaatdubbels.

### <a name="device-data"></a>Apparaatgegevens

Apparaatgegevens verwijzen naar de gegevens per apparaat die zijn opgeslagen in het IoT Hub [identiteits register](#identity-registry). Het is mogelijk om deze gegevens te importeren en exporteren.

### <a name="device-identity"></a>Apparaat-id

De identiteit van het apparaat (of apparaat-ID) is de unieke id die is toegewezen aan elk apparaat dat is geregistreerd in het IoT Hub- [identiteits register](#identity-registry).

### <a name="device-management"></a>Apparaatbeheer

Apparaatbeheer omvat de volledige levens cyclus voor het beheer van de apparaten in uw IoT-oplossing, waaronder het plannen, inrichten, configureren, bewaken en buiten gebruik stellen.

### <a name="device-management-patterns"></a>Patronen voor apparaatbeheer

Met [IOT hub](#iot-hub) kunt u veelgebruikte patronen voor Apparaatbeheer maken, waaronder het opnieuw opstarten, het opnieuw instellen van de fabriek en het uitvoeren van firmware-updates op uw apparaten.

### <a name="device-model"></a>Apparaatmodel

Een apparaatprofiel is een type [model](#model) dat gebruikmaakt van de [Digital Apparaatdubbels definition-taal](#digital-twins-definition-language-dtdl) om de mogelijkheden van een IOT Plug en Play-apparaat te beschrijven. Een model voor eenvoudige apparaten maakt gebruik van één interface om de mogelijkheden van het apparaat te beschrijven. Een complexere apparaatprofiel bevat meerdere onderdelen, die allemaal een reeks mogelijkheden beschrijven. Zie [IoT Plug en Play-onderdelen in modellen](../iot-pnp/concepts-components.md)voor meer informatie.

### <a name="device-modeling"></a>Apparaats modellen

Een [opbouw](#device-builder) functie voor apparaten of [module](#module-builder)maakt gebruik van de [taal Digital apparaatdubbels definition](#digital-twins-definition-language-dtdl) om de mogelijkheden van een [IOT Plug en Play-apparaat](#iot-plug-and-play-device)te model leren. Een [oplossings functie voor oplossingen](#solution-builder) kan een IOT-oplossing van het model configureren.

### <a name="device-provisioning"></a>Apparaat inrichten

Het inrichten van apparaten is het proces van het toevoegen van de initiële gegevens van het [apparaat](#device-data) aan de winkels in uw oplossing. Als u een nieuw apparaat wilt inschakelen om verbinding te maken met uw hub, moet u een apparaat-ID en sleutels toevoegen aan het IoT Hub- [identiteits register](#identity-registry). Als onderdeel van het inrichtings proces moet u mogelijk apparaatspecifieke gegevens initialiseren in andere oplossingen Stores.

### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) is een Helper-service voor [IOT hub](#iot-hub) die u gebruikt om het inrichten van Zero-Touch-apparaten te configureren voor een opgegeven IOT-hub. Met de DPS kunt u miljoenen apparaten op een veilige en schaal bare manier inrichten.

### <a name="device-rest-api"></a>Apparaat REST API

U kunt het [apparaat rest API](/rest/api/iothub/device) van een apparaat gebruiken om apparaat-naar-Cloud-berichten te verzenden naar een IOT-hub en [Cloud-naar-apparaat-](#cloud-to-device) berichten van een IOT-hub te ontvangen. Normaal gesp roken moet u een van de [apparaat-sdk's](#azure-iot-device-sdks) van het hogere niveau gebruiken, zoals wordt weer gegeven in de IOT hub zelf studies.

### <a name="device-twin"></a>Dubbel apparaat

Een dubbel apparaat is een JSON-document waarin informatie over de status van het apparaat, zoals meta gegevens, configuraties en voor waarden, wordt opgeslagen. IoT Hub persistent voor elk apparaat dat u inricht in IoT hub. Met Device apparaatdubbels kunt u de voor waarden en configuraties van apparaten synchroniseren tussen het apparaat en de back-end van de oplossing. U kunt een query uitvoeren op apparaat apparaatdubbels om specifieke apparaten te vinden en voor de status van langlopende bewerkingen.

### <a name="device-to-cloud"></a>Apparaat-naar-Cloud

Verwijst naar berichten die vanuit een verbonden apparaat naar [IOT hub](#iot-hub)worden verzonden. Deze berichten kunnen [gegevens](#data-point-message) of [interactieve](#interactive-message) berichten zijn. Zie [berichten verzenden en ontvangen met IOT hub](../iot-hub/iot-hub-devguide-messaging.md)voor meer informatie.

### <a name="digital-twin"></a>Digitale dubbele

Een digitale dubbele waarde is een verzameling digitale gegevens die een fysiek object vertegenwoordigt. Wijzigingen in het fysieke object worden weer gegeven in de digitale twee. In sommige scenario's kunt u de digitale twee gebruiken om het fysieke object te manipuleren. De [Azure Digital apparaatdubbels-service](../digital-twins/index.yml) maakt gebruik van [modellen](#model) die worden weer gegeven in de [Digital apparaatdubbels Definition Language (DTDL)](#digital-twins-definition-language-dtdl) om digitale apparaatdubbels aan te duiden van fysieke apparaten of abstracte zakelijke concepten op een hoger niveau, waardoor u een breed scala aan hoogwaardige, op de cloud gebaseerde digitale twee oplossingen kunt maken. Een [IOT-Plug en Play](../iot-pnp/index.yml) apparaat heeft een digitale dubbele, beschreven door een [DTDL.](#device-model)

### <a name="digital-twin-change-events"></a>Gebeurtenissen met wijziging van dubbel

Wanneer een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) is verbonden met een IOT-hub, kan de hub de route ring gebruiken om meldingen van digitale dubbele wijzigingen te verzenden. Wanneer bijvoorbeeld een [eigenschaps](#properties) waarde op een apparaat wordt gewijzigd, kan IOT hub een melding verzenden naar een eind punt zoals een event hub.

### <a name="digital-twin-route"></a>Digitale dubbele route

Een route die is ingesteld in een IoT-hub voor het leveren van [digitale dubbele wijzigings gebeurtenissen](#digital-twin-change-events) op een eind punt, zoals een event hub.

### <a name="digital-twins-definition-language-dtdl"></a>DTDL (Digital Twins Definition Language)

Een JSON-LD-taal voor het beschrijven van [modellen](#model) en [interfaces](#interface) voor [IOT Plug en Play-apparaten](#iot-plug-and-play-device) en [Azure Digital apparaatdubbels](../digital-twins/index.yml) -entiteiten. Gebruik de [Digital Apparaatdubbels definition language versie 2](https://github.com/Azure/opendigitaltwins-dtdl) om een [digitale twee](#digital-twin) mogelijkheden te beschrijven en het IoT-platform en IOT-oplossingen in te scha kelen om de semantiek van de entiteit te gebruiken. De taal van de Digital Apparaatdubbels-definitie is vaak afgekort tot DTDL.

### <a name="direct-method"></a>Directe methode

Een [directe methode](../iot-hub/iot-hub-devguide-direct-methods.md) is een manier waarop u een methode voor het uitvoeren van een apparaat kunt activeren door een API op uw IOT-hub aan te roepen.

### <a name="downstream-services"></a>Downstream-Services

Een relatieve term voor het beschrijven van services die gegevens ontvangen van de huidige context. Als u bijvoorbeeld in de context van Azure Digital Apparaatdubbels denkt, zou [Time Series Insights](../time-series-insights/index.yml) als een downstream-service worden beschouwd als u uw gegevens zo instelt dat deze vanuit Azure Digital apparaatdubbels stromen naar Time Series Insights.

## <a name="e"></a>E

### <a name="endpoint"></a>Eindpunt

Een benoemde weer gave van een Data Routing-service die gegevens van andere services kan ontvangen.

Een IoT-hub biedt meerdere [eind punten](../iot-hub/iot-hub-devguide-endpoints.md) die uw apps in staat stellen om verbinding te maken met de IOT-hub. Er zijn apparaat gerichte eind punten die apparaten in staat stellen om bewerkingen uit te voeren, zoals het verzenden van [apparaat-naar-Cloud](#device-to-cloud) -berichten en het ontvangen van [Cloud-naar-apparaat-](#cloud-to-device) berichten. Er zijn service-Facing Management-eind punten waarmee [back-end-apps](#back-end-app) bewerkingen kunnen uitvoeren zoals het beheer van [apparaat-id's](#device-identity) en het dubbele beheer van apparaten. Er zijn op de service gerichte [ingebouwde eind punten](#built-in-endpoints) voor het lezen van apparaat-naar-Cloud-berichten. U kunt [aangepaste eind punten](#custom-endpoints) maken om apparaat-naar-Cloud-berichten te ontvangen die worden verzonden door een [routerings regel](#routing-rules).

### <a name="enrollment"></a>Inschrijving

In de [Device Provisioning Service](#device-provisioning-service)is een inschrijving het record van afzonderlijke apparaten of groepen apparaten die met een [gekoppelde IOT-hub](#linked-iot-hub) kunnen worden geregistreerd via de module voor autoinrichting.

### <a name="enrollment-group"></a>Registratie groep

In de [Device Provisioning Service](#device-provisioning-service)identificeert een registratie groep een groep apparaten die een X. 509 of een symmetrisch sleutel [Attestation-mechanisme](#attestation-mechanism)delen.

### <a name="event-handlers"></a>Event Handlers

Dit kan verwijzen naar elk proces dat wordt geactiveerd door de ontvangst van een gebeurtenis en een bepaalde verwerkings actie uitvoert. Een manier om gebeurtenis-handlers te maken, is door gebeurtenis verwerkings code toe te voegen aan een Azure-functie en gegevens te verzenden met behulp van [eind punten](#endpoint) en [gebeurtenis routering](#event-routing).

### <a name="event-hub-compatible-endpoint"></a>Event hub-compatibel eind punt

Voor het lezen van [apparaat-naar-Cloud](#device-to-cloud) -berichten die naar uw IOT hub worden verzonden, kunt u verbinding maken met een eind punt op uw hub en een event hub-compatibele methode gebruiken om die berichten te lezen. Event hub-compatibele methoden zijn het gebruik van de [Event hubs sdk's](../event-hubs/event-hubs-programming-guide.md) en [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md).

### <a name="event-routing"></a>Gebeurtenisroutering

Het proces van het verzenden van gebeurtenissen en hun gegevens van een apparaat of service naar het [eind punt](#endpoint) van een andere. 

In IOT hub kunt u [routerings regels](#routing-rules) definiëren om te beschrijven hoe berichten moeten worden verzonden. In azure Digital Apparaatdubbels zijn gebeurtenis routes entiteiten die voor dit doel worden gemaakt. Azure Digital Apparaatdubbels-gebeurtenis routes kunnen filters bevatten om te beperken welke typen gebeurtenissen naar elk eind punt worden verzonden.

## <a name="f"></a>F

### <a name="field-gateway"></a>Veld Gateway

Een veld Gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub) en die doorgaans lokaal op uw apparaten wordt geïmplementeerd. Zie [Wat is Azure IOT hub?](../iot-hub/about-iot-hub.md)voor meer informatie.

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

Een gateway maakt connectiviteit mogelijk voor apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub). Zie ook [veld Gateway](#field-gateway), [Cloud gateway](#cloud-gateway)en [aangepaste gateway](#custom-gateway).

### <a name="gateway-device"></a>Gatewayapparaat

Een apparaat is een voor beeld van een [veld Gateway](#field-gateway). Een gateway apparaat kan standaard IoT- [apparaat](#device) zijn of een [IOT edge apparaat](#iot-edge-device).

Een gateway apparaat maakt connectiviteit mogelijk voor downstream-apparaten die niet rechtstreeks verbinding kunnen maken met [IOT hub](#iot-hub).

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Hardware Security module

Er wordt een HSM (Hardware Security module) gebruikt voor beveiligde, op hardware gebaseerde opslag van geheimen van apparaten. Het is de veiligste vorm van een geheim opslag voor een apparaat. Zowel X. 509-certificaten als symmetrische sleutels kunnen worden opgeslagen in een HSM. In de [Device Provisioning Service](#device-provisioning-service)kan een [Attestation-mechanisme](#attestation-mechanism) gebruikmaken van een HSM.

## <a name="i"></a>I

### <a name="id-scope"></a>ID-bereik

Het ID-bereik is een unieke waarde die is toegewezen aan een [DPS-exemplaar (Device Provisioning Service)](#device-provisioning-service) wanneer deze wordt gemaakt.

IoT Central-toepassingen maken gebruik van DPS-instanties en maken het ID-bereik beschikbaar via de IoT Central-gebruikers interface.

### <a name="identity-registry"></a>Id-REGI ster

Het [id-REGI ster](../iot-hub/iot-hub-devguide-identity-registry.md) is het ingebouwde onderdeel van een IOT-hub dat informatie bevat over de afzonderlijke apparaten die verbinding mogen maken met een IOT-hub.

### <a name="individual-enrollment"></a>Individuele inschrijving

In de [Device Provisioning Service](#device-provisioning-service)identificeert een afzonderlijke registratie één apparaat dat gebruikmaakt van een X. 509-Leaf-certificaat of een symmetrische sleutel als [Attestation-mechanisme](#attestation-mechanism).

### <a name="interactive-message"></a>Interactief bericht

Een interactief bericht is een [Cloud-naar-apparaat-](#cloud-to-device) bericht dat een onmiddellijke actie activeert in de back-end van de oplossing. Een apparaat kan bijvoorbeeld een waarschuwing sturen over een fout die automatisch moet worden aangemeld bij een CRM-systeem.

### <a name="interface"></a>Interface

In IoT Plug en Play worden in een interface gerelateerde mogelijkheden beschreven die worden geïmplementeerd door een [IOT-Plug en Play apparaat](#iot-plug-and-play-device) of [digitale dubbele](#digital-twin). U kunt interfaces opnieuw gebruiken in verschillende [modellen](#device-model). Wanneer een interface wordt gebruikt in een apparaatprofiel, definieert deze een [onderdeel](#component) van het apparaat. Een eenvoudig apparaat bevat alleen een standaard interface.

In azure Digital Apparaatdubbels kan *Interface* worden gebruikt om te verwijzen naar het code item op het hoogste niveau in een model definitie [DTDL](#digital-twins-definition-language-dtdl) .

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge maakt cloud-gebaseerde implementatie van Azure-Services en oplossingen-specifieke code op on-premises apparaten mogelijk. [IOT edge-apparaten](#iot-edge-device) kunnen gegevens van andere apparaten verzamelen voor het uitvoeren van computing en analyse voordat de gegevens naar de cloud worden verzonden. Zie [Azure IOT Edge](../iot-edge/index.yml)voor meer informatie.

### <a name="iot-edge-agent"></a>IoT Edge-agent

Het deel van de IoT Edge runtime dat verantwoordelijk is voor het implementeren en bewaken van modules.

### <a name="iot-edge-device"></a>IoT Edge-apparaat

Een IoT Edge apparaat gebruikt container IoT Edge [modules](#modules) om Azure-Services, services van derden of uw eigen code uit te voeren. Op het IoT Edge-apparaat beheert de [IOT Edge runtime](#iot-edge-runtime) de modules. U kunt een IoT Edge apparaat op afstand bewaken en beheren vanuit de Cloud.

### <a name="iot-edge-hub"></a>IoT Edge hub

Het deel van de IoT Edge runtime dat verantwoordelijk is voor de module communicatie, upstream (naar IoT Hub) en downstream (weg van IoT Hub)-communicatie.

### <a name="iot-edge-runtime"></a>IoT Edge-runtime

IoT Edge runtime bevat alles wat micro soft distribueert om te worden geïnstalleerd op een IoT Edge apparaat. Het bevat Edge-agent, Edge hub en de IoT Edge Security daemon.

### <a name="iot-extension-for-azure-cli"></a>IoT-extensie voor Azure CLI

[De IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) is een platform, opdracht regel programma voor meerdere platforms. Met het hulp programma kunt u uw apparaten beheren in het [identiteits register](#identity-registry), berichten en bestanden van uw apparaten verzenden en ontvangen en uw IOT hub-bewerkingen bewaken.

### <a name="iot-hub"></a>IoT Hub

IoT Hub is een volledig beheerde Azure-service die betrouw bare en veilige bidirectionele communicatie mogelijk maakt tussen miljoenen apparaten en een back-end van een oplossing. Zie [Wat is Azure IOT hub?](../iot-hub/about-iot-hub.md)voor meer informatie. Met uw Azure-abonnement kunt u IoT hubs maken om uw IoT Messa ging-werk belastingen te verwerken.

### <a name="iot-hub-metrics"></a>IoT Hub metrische gegevens

IoT Hub metrische gegevens geven u informatie over de status van de IoT-hubs in uw Azure-abonnement. Met IoT Hub metrische gegevens kunt u de algemene status van de service en de aangesloten apparaten beoordelen. Met IoT Hub metrische gegevens kunt u zien wat er gebeurt met uw IoT hub en problemen met de hoofd oorzaak onderzoeken zonder dat u contact hoeft op te nemen met de ondersteuning van Azure. Zie [Azure IoT Hub controleren](../iot-hub/monitor-iot-hub.md) voor meer informatie.

### <a name="iot-hub-query-language"></a>Query taal IoT Hub

De [IOT hub query taal](../iot-hub/iot-hub-devguide-query-language.md) is een SQL-achtige taal waarmee u een query kunt uitvoeren op uw [taak](#job), digitale apparaatdubbels en apparaat apparaatdubbels.

### <a name="iot-hub-resource-rest-api"></a>IoT Hub resource REST API

U kunt de [IOT hub Resource rest API](/rest/api/iothub/iothubresource) gebruiken voor het beheren van de IOT-hubs in uw Azure-abonnement voor het uitvoeren van bewerkingen, zoals het maken, bijwerken en verwijderen van hubs.

### <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-brug

IoT Plug en Play Bridge is een open-source toepassing waarmee bestaande Sens oren en rand apparatuur die zijn gekoppeld aan Windows-of Linux-gateways, verbinding kunnen maken als [IoT Plug en Play-apparaten](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-conventions"></a>Conventies voor IoT Plug en Play

IoT-Plug en Play [apparaten](#iot-plug-and-play-device) worden naar verwachting een set conventies volgen wanneer ze gegevens uitwisselen met een oplossing.

### <a name="iot-plug-and-play-device"></a>IoT Plug en Play-apparaat

Een IoT-Plug en Play apparaat is doorgaans een klein, zelfstandig computer apparaat waarmee gegevens worden verzameld of andere apparaten worden beheerd, en waarmee software of firmware wordt uitgevoerd waarmee een [model](#device-model)wordt geïmplementeerd.  Een IoT-Plug en Play apparaat kan bijvoorbeeld een milieubewakings apparaat zijn of een controller voor een irrigatie systeem met een slimme land bouw. Een IoT-Plug en Play apparaat kan rechtstreeks of als IoT Edge module worden geïmplementeerd. U kunt een in de Cloud gehoste IoT-oplossing schrijven naar de opdracht, het besturings element en de gegevens van IoT Plug en Play-apparaten ontvangen.

### <a name="iot-solution-accelerators"></a>IoT-oplossingsversnellers

Met accelerators van Azure IoT-oplossing worden meerdere Azure-Services in oplossingen gecombineerd. Met deze oplossingen kunt u snel aan de slag met end-to-end-implementaties van algemene IoT-scenario's. Zie [Wat zijn Azure IOT-oplossings Accelerators?](../iot-accelerators/about-iot-accelerators.md) voor meer informatie.

## <a name="j"></a>J

### <a name="job"></a>Taak

De back-end van uw oplossing kan [taken](../iot-hub/iot-hub-devguide-jobs.md) gebruiken om activiteiten te plannen en bij te houden voor een set apparaten die zijn geregistreerd bij uw IOT-hub. Activiteiten omvatten het bijwerken van de dubbele [gewenste eigenschappen](#desired-properties)van het apparaat, het bijwerken van de dubbele [Tags](#tags)van een apparaat en het aanroepen van [directe methoden](#direct-method). [IOT hub](#iot-hub) wordt ook gebruikt voor het [importeren van en exporteren](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) uit het [identiteits register](#identity-registry).

## <a name="l"></a>L

### <a name="leaf-device"></a>Blad apparaat

In [IOT Edge](#iot-edge)is een blad apparaat een apparaat zonder downstream-apparaat.

### <a name="lifecycle-event"></a>Levenscyclus gebeurtenis

In azure Digital Apparaatdubbels wordt dit type gebeurtenis geactiveerd wanneer een gegevens item, zoals een digitaal element, een relatie of een gebeurtenis-handler, wordt gemaakt of verwijderd uit uw Azure Digital Apparaatdubbels-exemplaar.

### <a name="linked-iot-hub"></a>Gekoppelde IoT-hub

Met de [Device Provisioning Service (DPS)](#device-provisioning-service)kunnen apparaten worden ingericht naar IOT hubs die eraan zijn gekoppeld. Als u een IoT-hub koppelt aan een DPS-exemplaar, kan de service een apparaat-ID registreren en de eerste configuratie in het apparaat configureren.

## <a name="m"></a>M

### <a name="model"></a>Model

Een model definieert een type entiteit in uw fysieke omgeving, waaronder de eigenschappen, teleelementen, onderdelen en soms andere informatie. Modellen worden gebruikt voor het maken van [digitale apparaatdubbels](#digital-twin) die specifieke fysieke objecten van dit type vertegenwoordigen. Modellen worden geschreven in de [taal van de Digital apparaatdubbels-definitie](#digital-twins-definition-language-dtdl).

In de [Azure Digital apparaatdubbels-service](../digital-twins/index.yml)kunnen modellen apparaten of op een hoger niveau abstracte zakelijke concepten definiëren. In [IoT Plug en Play](../iot-pnp/index.yml)worden [apparaatklassen](#device-model) gebruikt om apparaten specifiek te beschrijven.

### <a name="model-id"></a>Model-id

Wanneer een IoT Plug en Play-apparaat verbinding maakt met een IoT Hub, verzendt het een **model-id** van het [DTDL](#digital-twins-definition-language-dtdl) -model dat wordt geïmplementeerd. Met deze ID kan de oplossing het model van het apparaat vinden.

### <a name="model-repository"></a>Modelopslagplaats

Met een model opslagplaats worden modellen en [interfaces](#interface)van [apparaten](#device-model) opgeslagen.

### <a name="model-repository-rest-api"></a>REST API van model opslagplaats

Een API voor het beheer en interactie met de model opslagplaats. U kunt bijvoorbeeld de API gebruiken om [apparaatprofielen](#device-model)toe te voegen en te zoeken.

### <a name="module-builder"></a>Module builder

Een module Builder maakt gebruik van een [model](#device-model) en [interfaces](#interface) voor het uitvoeren van code voor uitvoering op een [IOT Plug en Play-apparaat](#iot-plug-and-play-device). Module Builders implementeren de code als een module of een IoT Edge module om te implementeren in de IoT Edge runtime op een apparaat.

### <a name="module-identity"></a>Module-identiteit

De module-id is de unieke id die is toegewezen aan elke module die deel uitmaakt van een apparaat. Module-identiteit wordt ook geregistreerd in het [identiteits register](#identity-registry).

De module identificeert Details van de beveiligings referenties die de module gebruikt voor verificatie met de [IOT hub](#iot-hub) of, in het geval van een IOT Edge-module aan de [IOT Edge hub](#iot-edge-hub).

### <a name="module-image"></a>Installatie kopie van module

De docker-installatie kopie die door de [IOT Edge runtime](#iot-edge-runtime) wordt gebruikt om module-exemplaren te instantiëren.

### <a name="module-twin"></a>Module dubbele

Net als bij een ander apparaat dan is een module gelijk aan het JSON-document waarin module status gegevens worden opgeslagen, zoals meta gegevens, configuraties en voor waarden. IoT Hub persistent een module tussen een module-identiteit die u onder een apparaat-id in uw IoT-hub inricht. Met module apparaatdubbels kunt u module omstandigheden en configuraties synchroniseren tussen de module en de back-end van de oplossing. U kunt de module apparaatdubbels opvragen om specifieke modules te vinden en de status van langlopende bewerkingen te controleren.

### <a name="modules"></a>Modules

Aan de kant van het apparaat kunt u met de Sdk's van het IoT Hub apparaat [modules](../iot-hub/iot-hub-devguide-module-twins.md) maken waarbij elke ene een onafhankelijke verbinding met IOT hub opent. Met deze functie kunt u afzonderlijke naam ruimten voor verschillende onderdelen op het apparaat gebruiken.

Module-identiteit en module twee bieden dezelfde mogelijkheden als de [apparaat-id](#device-identity) en het [apparaat](#device-twin) , met een nauw keurigere granulariteit. Met deze nauw keurigheid kunnen apparaten, zoals apparaten op basis van een besturings systeem of firmware-apparaten meerdere onderdelen beheren, de configuratie en voor waarden voor elk van deze onderdelen worden geïsoleerd.

In [IOT Edge](#iot-edge)is een module een docker-container die u op IOT edge apparaten kunt implementeren. Hiermee wordt een specifieke taak uitgevoerd, zoals het opnemen van een bericht van een apparaat, het transformeren van een bericht of het verzenden van een bericht naar een IoT-hub. De service communiceert met andere modules en verzendt gegevens naar de [IOT Edge runtime](#iot-edge-runtime).

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) is een van de berichten protocollen die [IOT hub](#iot-hub) ondersteunt voor het communiceren met apparaten. Zie [berichten verzenden en ontvangen met IOT hub](../iot-hub/iot-hub-devguide-messaging.md)voor meer informatie over de berichten protocollen die door IOT hub worden ondersteund.

## <a name="o"></a>O

### <a name="operations-monitoring"></a>Controle van bewerkingen

Met IoT Hub [bewerkingen](../iot-hub/iot-hub-operations-monitoring.md) bewaken kunt u de status van de bewerkingen op uw IOT-hub in realtime bewaken. [IOT hub](#iot-hub) houdt gebeurtenissen in verschillende categorieën bewerkingen bij. U kunt ervoor kiezen gebeurtenissen te verzenden van een of meer categorieën naar een IoT Hub eind punt voor verwerking. U kunt de gegevens controleren op fouten of complexere verwerking instellen op basis van gegevens patronen.

## <a name="p"></a>P

### <a name="physical-device"></a>Fysiek apparaat

Een fysiek apparaat is een echt apparaat, zoals een Raspberry Pi, waarmee verbinding wordt gemaakt met een IoT-hub. Voor het gemak gebruiken veel van de IoT Hub zelf studies [gesimuleerde apparaten](#simulated-device) waarmee u voor beelden kunt uitvoeren op uw lokale computer.

### <a name="primary-and-secondary-keys"></a>Primaire en secundaire sleutels

Wanneer u verbinding maakt met een apparaat-Facing of een service gericht eind punt op een IoT-hub, bevat uw [Connection String](#connection-string) sleutel om u toegang te verlenen. Wanneer u een apparaat toevoegt aan het [identiteits register](#identity-registry) of een [gedeeld toegangs beleid](#shared-access-policy) toevoegt aan uw hub, wordt door de service een primaire en secundaire sleutel gegenereerd. Met twee sleutels kunt u overgaan van de ene sleutel naar de andere wanneer u een sleutel bijwerkt zonder de toegang tot de IoT-hub te verliezen.

### <a name="properties"></a>Eigenschappen

Eigenschappen zijn gegevens velden die zijn gedefinieerd in een [Interface](#interface) die een permanente status van een [digitaal, twee](#digital-twin)Voorst Ellen. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar. Alleen-lezen eigenschappen, zoals serie nummer, worden ingesteld door code die wordt uitgevoerd op de [IOT-Plug en Play apparaat](#iot-plug-and-play-device) zelf. Beschrijf bare eigenschappen, zoals een alarm drempel, worden doorgaans ingesteld vanuit de in de cloud gebaseerde IoT-oplossing.

### <a name="property-change-event"></a>Gebeurtenis eigenschap wijzigen

Een gebeurtenis die het gevolg is van een wijziging in een eigenschap in een [digitale dubbele](#digital-twin).

### <a name="protocol-gateway"></a>Protocolgateway

Een protocol gateway wordt doorgaans geïmplementeerd in de Cloud en biedt protocol Vertaal Services voor apparaten die verbinding maken met [IOT hub](#iot-hub). Zie [Wat is Azure IOT hub?](../iot-hub/about-iot-hub.md)voor meer informatie.

## <a name="r"></a>R

### <a name="registration"></a>Registratie

Een registratie is de record van een apparaat in het IoT Hub [identiteits register](#identity-registry). U kunt het apparaat rechtstreeks registreren of de Device [Provisioning Service](#device-provisioning-service) gebruiken om apparaatregistratie te automatiseren.

### <a name="registration-id"></a>Registratie-ID

De registratie-ID wordt gebruikt voor het [identificeren van een apparaatregistratie met](#registration) de [Device Provisioning Service](#device-provisioning-service). De registratie-ID kan dezelfde waarde hebben als de [identiteit](#device-identity)van het apparaat.

### <a name="relationship"></a>Relatie

In de [Azure Digital apparaatdubbels](../digital-twins/index.yml) -service worden relaties gebruikt voor het verbinden van [digitale apparaatdubbels](#digital-twin) in kennis grafieken die uw hele fysieke omgeving digitaal vertegenwoordigen. De typen relaties die uw apparaatdubbels kan hebben, zijn gedefinieerd als onderdeel van de [model](#model) definities van de apparaatdubbels, het [DTDL](#digital-twins-definition-language-dtdl) -model voor een bepaald type dubbele gegevens bevat informatie over wat de relaties met andere apparaatdubbels kunnen hebben.

### <a name="reported-configuration"></a>Gerapporteerde configuratie

In de context van een [apparaat dubbele](../iot-hub/iot-hub-devguide-device-twins.md), gerapporteerde configuratie verwijst naar de volledige set eigenschappen en meta gegevens in het apparaat dat moet worden gerapporteerd aan de back-end van de oplossing.

### <a name="reported-properties"></a>Gerapporteerde eigenschappen

In de context van een [apparaat](../iot-hub/iot-hub-devguide-device-twins.md), gerapporteerde eigenschappen is een subsectie van het apparaat dat wordt gebruikt met de [gewenste eigenschappen](#desired-properties) voor het synchroniseren van de apparaatconfiguratie of voor waarde. Gerapporteerde eigenschappen kunnen alleen worden ingesteld door de [apparaat-app](#device-app) en kunnen worden gelezen en opgevraagd door een [back-end-app](#back-end-app).

### <a name="retry-policy"></a>Beleid voor opnieuw proberen

U gebruikt een beleid voor opnieuw proberen om [tijdelijke fouten](/azure/architecture/best-practices/transient-faults) af te handelen wanneer u verbinding maakt met een Cloud service.

### <a name="routing-rules"></a>Regels voor doorsturen

U configureert [routerings regels](../iot-hub/iot-hub-devguide-messages-read-custom.md) in uw IOT-hub om apparaat-naar-Cloud-berichten te routeren naar een [ingebouwd eind punt](#built-in-endpoints) of naar [aangepaste eind punten](#custom-endpoints) voor verwerking door de back-end van uw oplossing.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN is een protocol dat het AMQP-protocol gebruikt voor het overdragen van beveiligings tokens.

### <a name="service-operations-endpoint"></a>Service-eind punt

Een [eind punt](#endpoint) voor het beheren van service-instellingen die worden gebruikt door een service beheerder. In de [Device Provisioning Service](#device-provisioning-service) gebruikt u bijvoorbeeld het service-eind punt voor het beheren van inschrijvingen.

### <a name="service-rest-api"></a>Service REST API

U kunt de [Service rest API](/rest/api/iothub/service/configuration) van de back-end van de oplossing gebruiken om uw apparaten te beheren. Met de API kunt u dubbele eigenschappen van het [apparaat](#device-twin) ophalen en bijwerken, [direct methoden](#direct-method)aanroepen en [taken](#job)plannen. Normaal gesp roken moet u een van de high-level [service-sdk's](#azure-iot-service-sdks) gebruiken, zoals weer gegeven in de IOT hub zelf studies.

### <a name="shared-access-policy"></a>Beleid voor gedeelde toegang

In een gedeeld toegangs beleid worden de machtigingen gedefinieerd die aan iedereen zijn verleend en die een geldige [primaire of secundaire sleutel](#primary-and-secondary-keys) heeft die aan dat beleid is gekoppeld. U kunt het gedeelde toegangs beleid en de sleutels voor uw hub in de portal beheren.

### <a name="shared-access-signature"></a>Shared Access Signature

Shared Access signatures (SAS) zijn een verificatie methode op basis van SHA-256 Secure hashes of Uri's. SAS-verificatie heeft twee onderdelen: een _gedeeld toegangs beleid_ en een _Shared Access Signature_ (vaak een token genoemd). Een apparaat gebruikt SAS om te verifiëren bij een IoT-hub. [Back-end-apps](#back-end-app) maken ook gebruik van SAS om te verifiëren bij de service gerichte eind punten op een IOT-hub. Normaal gesp roken neemt u het SAS-token op in het [Connection String](#connection-string) dat een app gebruikt om een verbinding met een IOT-hub tot stand te brengen.

### <a name="simulated-device"></a>Gesimuleerd apparaat

Voor het gemak gebruiken veel van de IoT Hub zelf studies gesimuleerde apparaten waarmee u voor beelden kunt uitvoeren op uw lokale computer. Een [fysiek apparaat](#physical-device) daarentegen is een echt apparaat, zoals een Raspberry Pi, waarmee verbinding wordt gemaakt met een IOT-hub.

### <a name="solution"></a>Oplossing

Een _oplossing_ kan verwijzen naar een Visual Studio-oplossing die een of meer projecten bevat. Een _oplossing_ kan ook verwijzen naar een IOT-oplossing die elementen, zoals apparaten, [apps](#device-app), een IOT-hub, andere Azure-Services en [back-end-apps](#back-end-app)bevat.

### <a name="solution-builder"></a>Opbouw functie voor oplossingen

Een oplossings functie voor oplossingen maakt de back-end van de oplossing. Een oplossings opbouw werkt meestal met Azure-resources, zoals IoT Hub en [model opslagplaatsen](#model-repository).

### <a name="system-properties"></a>Systeemeigenschappen

In de context van een [apparaat](../iot-hub/iot-hub-devguide-device-twins.md)zijn de systeem eigenschappen alleen-lezen en bevatten informatie over het gebruik van het apparaat, zoals de tijd van de laatste activiteit en de status van de verbinding.

## <a name="t"></a>T

### <a name="tags"></a>Tags

In de context van een [apparaat dubbele](../iot-hub/iot-hub-devguide-device-twins.md)worden de meta gegevens van het apparaat opgeslagen en opgehaald door de back-end van de oplossing in de vorm van een JSON-document. Tags zijn niet zichtbaar voor apps op een apparaat.

### <a name="target-condition"></a>Doel voorwaarde

In een IoT Edge-implementatie selecteert de doel voorwaarde de doel apparaten van de implementatie, bijvoorbeeld **tag. Environment = Prod**. De doel voorwaarde wordt continu geëvalueerd om nieuwe apparaten op te halen die voldoen aan de vereisten of om apparaten te verwijderen die niet meer beschikbaar zijn.

### <a name="telemetry"></a>Telemetrie

Apparaten verzamelen telemetriegegevens, zoals wind snelheid of Tempe ratuur, en gebruiken gegevens punt berichten om de telemetrie te verzenden naar een IoT-hub.

In IoT Plug en Play en Azure Digital Apparaatdubbels zijn de velden voor telemetrie die zijn gedefinieerd in een [Interface](#interface) metingen vertegenwoordigd. Deze metingen zijn doorgaans waarden zoals sensor aflezingen die worden verzonden door apparaten, zoals [IoT Plug en Play-apparaten](#iot-plug-and-play-device), als een gegevens stroom.

In tegens telling tot [Eigenschappen](#properties)wordt telemetrie niet opgeslagen op een [digitale dubbele](#digital-twin); het is een stroom van tijdgebonden gegevens gebeurtenissen die moeten worden verwerkt wanneer deze zich voordoen.

### <a name="telemetry-event"></a>Telemetrie-gebeurtenis

Een gebeurtenis die de ontvangst van telemetriegegevens aanduidt.

### <a name="token-service"></a>Token Service

U kunt een token service gebruiken om een verificatie mechanisme voor uw apparaten te implementeren. Er wordt gebruikgemaakt van een IoT Hub [beleid voor gedeelde toegang](#shared-access-policy) met **DeviceConnect** -machtigingen voor het maken van tokens met een *bereik* . Met deze tokens kunnen apparaten verbinding maken met uw IoT-hub. Een apparaat gebruikt een aangepast verificatie mechanisme om te verifiëren met de token service. Als het apparaat is geverifieerd, geeft de token service een SAS-token uit voor het apparaat dat moet worden gebruikt voor toegang tot uw IoT-hub.

### <a name="twin-graph-or-digital-twin-graph"></a>Dubbele grafiek (of digitale dubbele grafiek)

In de [Azure Digital apparaatdubbels](../digital-twins/index.yml) -service kunt u [digitale apparaatdubbels](#digital-twin) verbinden met [relaties](#relationship) om kennis grafieken te maken die uw hele fysieke omgeving digitaal vertegenwoordigen. Eén [Azure Digital apparaatdubbels-exemplaar](#azure-digital-twins-instance) kan veel niet-verbonden grafieken of één onderling verbonden grafiek hosten.

### <a name="twin-queries"></a>Dubbele query's

Bij [dubbele query's voor apparaten en modules](../iot-hub/iot-hub-devguide-query-language.md) wordt de SQL-achtige IOT hub query taal gebruikt om informatie op te halen van uw apparaat apparaatdubbels of module apparaatdubbels. U kunt dezelfde IoT Hub query taal gebruiken om informatie op te halen over een [taak](#job) die wordt uitgevoerd in uw IOT-hub.

### <a name="twin-synchronization"></a>Dubbele synchronisatie

Dubbele synchronisatie maakt gebruik van de [gewenste eigenschappen](#desired-properties) in uw apparaat apparaatdubbels of module apparaatdubbels om uw apparaten of modules te configureren en [gerapporteerde eigenschappen](#reported-properties) van ze te verkrijgen voor opslag in de dubbele.

## <a name="u"></a>U

### <a name="upstream-services"></a>Upstream-Services

Een relatieve term voor het beschrijven van services die gegevens in de huidige context invoeren. Als u bijvoorbeeld in de context van Azure Digital Apparaatdubbels denkt, wordt IoT Hub beschouwd als upstream-service omdat gegevens stromen van IoT Hub naar Azure Digital Apparaatdubbels.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X. 509-client certificaat

Een apparaat kan een X. 509-certificaat gebruiken om te verifiëren met [IOT hub](#iot-hub). Het gebruik van een X. 509-certificaat is een alternatief voor het gebruik van een [SAS-token](#shared-access-signature).
