---
title: Ontwikkeling van apparaten voor Azure IoT Central | Microsoft Docs
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. Dit artikel bevat een overzicht van het ontwikkelen van apparaten om verbinding te maken met uw IoT Central-toepassing. Apparaten gebruiken telemetrie om streaminggegevens en -eigenschappen te verzenden over de status van het apparaat. IOT Central kan de apparaatstatus instellen met behulp van schrijfbare eigenschappen en opdrachten aanroepen op een apparaat.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: f69bbecfc2acc24cd63b87212197342b28723a9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043096"
---
# <a name="iot-central-device-development-guide"></a>Hand leiding voor het ontwikkelen van IoT Central apparaten

*Dit artikel is bedoeld voor ontwikkelaars van apparaten.*

Met een IoT Central-toepassing kunt u miljoenen apparaten tijdens hun levenscyclus bewaken en beheren. Deze hand leiding is bedoeld voor ontwikkel aars van apparaten die code implementeren om uit te voeren op apparaten die verbinding maken met IoT Central.

Apparaten communiceren met een IoT Central-toepassing met behulp van de volgende primitieven:

- _Telemetrie_ zijn de gegevens die een apparaat naar IoT Central verzendt. Bijvoorbeeld een stroom van de temperatuurwaarden van een ingebouwde sensor.
- _Eigenschappen_ zijn statuswaarden die een apparaat rapporteert aan IoT Central. Bijvoorbeeld de huidige firmwareversie van het apparaat. U kunt ook schrijfbare eigenschappen hebben die door IoT Central op het apparaat kunnen worden bijgewerkt, zoals een doeltemperatuur.
- _Opdrachten_ worden aangeroepen vanuit IoT Central om het gedrag van een apparaat te bepalen. Uw IoT Central-toepassing kan bijvoorbeeld een opdracht aanroepen om een apparaat opnieuw op te starten.

Een oplossingsbouwer is verantwoordelijk voor de configuratie van dashboards en weergaven in de webgebruikersinterface van IoT Central om telemetrie te visualiseren, eigenschappen te beheren en opdrachten aan te roepen.

## <a name="types-of-device"></a>Apparaattypen

In de volgende secties worden de belangrijkste apparaattypen beschreven die u kunt verbinden met een IoT Central-toepassing:

### <a name="standalone-device"></a>Zelfstandig apparaat

Een zelfstandig apparaat maakt rechtstreeks verbinding met IoT Central. Een zelfstandig apparaat verzendt doorgaans telemetriegegevens vanaf de ingebouwde of verbonden sensoren naar uw IoT Central-toepassing. Zelfstandige apparaten kunnen ook eigenschapswaarden rapporteren, schrijfbare eigenschapswaarden ontvangen en reageren op opdrachten.

### <a name="gateway-device"></a>Gatewayapparaat

Een gatewayapparaat beheert een of meer downstreamapparaten die verbinding maken met uw IoT Central-toepassing. U gebruikt IoT Central voor de configuratie van de relaties tussen de downstreamapparaten en het gatewayapparaat. Zie [Define a new IoT gateway device type in your Azure IoT Central application](./tutorial-define-gateway-device-type.md) (Een nieuw IoT-gatewayapparaattype definiëren in uw Azure IoT Central-toepassing) voor meer informatie.

### <a name="edge-device"></a>Edge-apparaat

Een edge-apparaat maakt rechtstreeks verbinding met IoT Central, maar fungeert als intermediair voor andere apparaten die _leaf-apparaten_ worden genoemd. Een edge-apparaat bevindt zich doorgaans dicht bij de leaf-apparaten waarvoor het fungeert als intermediair. Scenario's die gebruikmaken van edge-apparaten zijn onder andere:

- Apparaten inschakelen die niet rechtstreeks verbinding kunnen maken met IoT Central om verbinding te maken via het edge-apparaat. Een leaf-apparaat kan bijvoorbeeld gebruikmaken van Bluetooth om verbinding te maken met het edge-apparaat, dat vervolgens verbinding maakt via internet met IoT Central.
- Verzamel telemetrie voordat deze wordt verzonden naar IoT Central. Met deze aanpak kunt u de kosten van het verzenden van gegevens naar IoT Central verlagen.
- Beheer leaf-apparaten lokaal om de latentie te voorkomen die samenhangt met het maken van verbinding met IoT Central via internet.

Een edge-apparaat kan ook zijn eigen telemetrie verzenden, de eigenschappen ervan rapporteren en reageren op beschrijfbare updates van eigenschappen en opdrachten.

IoT Central ziet alleen het edge-apparaat, niet de leaf-apparaten die zijn verbonden met het edge-apparaat.

Zie [Add an Azure IoT Edge device to your Azure IoT Central application](./tutorial-add-edge-as-leaf-device.md) (Een Azure IoT Edge-apparaat toevoegen aan uw Azure IoT Central-toepassing) voor meer informatie.

## <a name="connect-a-device"></a>Een apparaat verbinden

Azure IoT Central maakt gebruik van de [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) om alle apparaatregistratie en -verbindingen te beheren.

Met DPS kunt u het volgende doen:

- IoT Central voor het ondersteunen van onboarding en op schaal verbinding van apparaten.
- U kunt de referenties van het apparaat genereren en de apparaten offline configureren zonder de apparaten te registreren via IoT Central-gebruikersinterface.
- U kunt uw eigen apparaat-id's gebruiken om apparaten te registreren in IoT Central. Het gebruik van uw eigen apparaat-id's vereenvoudigt de integratie met bestaande back-officesystemen.
- Een enkele, consistente manier om apparaten te verbinden met IoT Central.

Zie [Get connected to Azure IOT Central](./concepts-get-connected.md) en [Best practices](concepts-best-practices.md)(Engelstalig) voor meer informatie.

### <a name="security"></a>Beveiliging

De verbinding tussen een apparaat en uw IoT Central-toepassing wordt beveiligd met behulp van [handtekeningen voor gedeelde toegang](./concepts-get-connected.md#sas-group-enrollment) of de industriestandaard [X. 509-certificaten](./concepts-get-connected.md#x509-group-enrollment).

### <a name="communication-protocols"></a>Communicatieprotocollen

Communicatieprotocollen die een apparaat kan gebruiken om verbinding te maken met IoT Central zijn MQTT, AMQP en HTTPS. Intern gebruikt IoT Central een IoT-hub om connectiviteit van apparaten in te schakelen. Zie [Een communicatieprotocol kiezen](../../iot-hub/iot-hub-devguide-protocols.md) voor meer informatie over de communicatieprotocollen die door IoT Hub worden ondersteund voor connectiviteit van apparaten.

## <a name="implement-the-device"></a>Het apparaat implementeren

Een IoT Central-apparaatsjabloon bevat een _model_ dat het gedrag specificeert dat een apparaat van dat type moet implementeren. Gedragingen zijn onder andere telemetrie, eigenschappen en opdrachten.

> [!TIP]
> U kunt het model exporteren vanuit IoT Central als een JSON-bestand van [Digital Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Elk model heeft een unieke _model-id van apparaatdubbel_ (DTMI), zoals `dtmi:com:example:Thermostat;1`. Wanneer een apparaat verbinding maakt met IoT Central, verzendt het een DTMI van het model dat het implementeert. IoT Central kan vervolgens de juiste apparaatsjabloon aan het apparaat koppelen.

[IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) definieert een set conventies die een apparaat moet volgen bij het implementeren van een DTDL-model.

De [Azure IoT Device SDK’s](#languages-and-sdks) bevat ook ondersteuning voor de IoT-Plug and Play-conventies.

### <a name="device-model"></a>Apparaatmodel

Een apparaatmodel wordt gedefinieerd met behulp van de [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Met deze taal kunt u het volgende definiëren:

- De telemetrie die het apparaat verzendt. De definitie bevat de naam en het gegevenstype van de telemetrie. Zo verstuurt een apparaat bijvoorbeeld de temperatuurtelemetrie als een dubbel.
- De eigenschappen die het apparaat rapporteert aan IoT Central. Een eigenschapsdefinitie bevat de naam en het gegevenstype. Zo rapporteert een apparaat bijvoorbeeld de status van een klep als een Booleaanse waarde.
- De eigenschappen van het apparaat kunnen ontvangen van IoT Central. U kunt desgewenst een eigenschap markeren als beschrijfbaar. IoT Central verzendt bijvoorbeeld een doeltemperatuur als een dubbel naar een apparaat.
- De opdrachten waarop een apparaat reageert. De definitie bevat de naam van de opdracht en de namen en gegevenstypen van parameters. Een apparaat reageert bijvoorbeeld op een opdracht voor opnieuw opstarten, waarmee wordt opgegeven hoeveel seconden moet worden gewacht voordat opnieuw wordt opgestart.

Een DTDL-model kan een model met _geen-onderdeel_ of _multi-onderdeel_ zijn:

- Geen-onderdeelmodel: Een eenvoudig model maakt geen gebruik van ingesloten of trapsgewijze onderdelen. Alle telemetrie, eigenschappen en opdrachten zijn gedefinieerd als één _standaard onderdeel_. Zie het [Thermostaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)-model voor een voorbeeld.
- Multi-onderdeelmodel. Een complexer model met twee of meer onderdelen. Deze onderdelen bevatten één standaard onderdeel en een of meer extra geneste onderdelen. Zie het [Temperatuur-controller](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)-model voor een voorbeeld.

Zie voor meer informatie [IoT Plug and Play-componenten in modellen](../../iot-pnp/concepts-components.md)

### <a name="conventions"></a>Conventies

Een apparaat moet de IoT Plug and Play-conventies volgen wanneer het gegevens uitwisselt met IoT Central. De conventies zijn onder andere:

- De DTMI verzenden wanneer deze verbinding maakt met IoT Central.
- De juist geformatteerde JSON-payloads en -metagegevens verzenden naar IoT Central.
- Op de juiste manier reageren op schrijfbare eigenschappen en opdrachten van IoT Central.
- De naamconventies voor onderdeelopdrachten volgen.

> [!NOTE]
> Momenteel biedt IoT Central geen volledige ondersteuning voor de **Matrix**- en **Georuimtelijke** gegevenstypen van DTDL.

Zie [Telemetrie-, eigenschap- en opdracht-payloads](concepts-telemetry-properties-commands.md) voor meer informatie over de indeling van de JSON-berichten die door een apparaat worden uitgewisseld met IoT Central.

Zie voor meer informatie over de IoT Plug and Play-conventies [IoT Plug and Play-conventies](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>Apparaat-SDK's

Gebruik een van de [SDK's voor Azure IoT-apparaten](#languages-and-sdks) om het gedrag van uw apparaat te implementeren. De code moet:

- Het apparaat registreren met DPS en de informatie van DPS gebruiken om verbinding te maken met de interne IoT-hub in uw IoT Central-toepassing.
- De DTMI aankondigen van het model dat het apparaat implementeert.
- Telemetrie verzenden in de indeling die door het apparaatmodel wordt opgegeven. IoT Central gebruikt het model in de apparaatsjabloon om te bepalen hoe u de telemetrie gebruikt voor visualisaties en analyses.
- De eigenschapswaarden synchroniseren tussen het apparaat en IoT Central. In het model worden de eigenschapsnamen en gegevenstypen opgegeven, zodat IoT Central de informatie kan weergeven.
- Opdrachthandlers implementeren voor de opdrachten die worden opgegeven in het model. In het model worden de opdrachtnamen en parameters opgegeven die het apparaat moet gebruiken.

Zie [Wat zijn apparaatsjablonen?](./concepts-device-templates.md) voor meer informatie over de rol van apparaatsjablonen.

Zie [Een clienttoepassing maken en er verbinding mee maken](./tutorial-connect-device.md)voor voorbeeldcode.

### <a name="languages-and-sdks"></a>Talen en SDK's

Zie [Understand and use Azure IoT Hub device SDKs](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) (SDK's voor Azure IoT Hub-apparaten begrijpen en gebruiken) voor meer informatie over de ondersteunde talen en SDK's.

## <a name="next-steps"></a>Volgende stappen

Als u apparaatontwikkelaar bent en u meer wilt weten over coderen, kunt u het beste de stap [Create and connect a client application to your Azure IoT Central application](./tutorial-connect-device.md) (Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing) volgen.

Als u meer wilt weten over het gebruik van IoT Central, kunt u de volgende stappen uitvoeren om de quickstarts te proberen, te beginnen met [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md).
