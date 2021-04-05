---
title: Architecturale concepten in azure IoT Central | Microsoft Docs
description: In dit artikel worden de belangrijkste concepten besproken die betrekking hebben op de architectuur van Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c2d5310d1a664aa2e22d4241d8066e41d9c82bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796717"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-architectuur

Dit artikel bevat een overzicht van de Microsoft Azure IoT Central architectuur.

![Architectuur op het hoogste niveau](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Apparaten

Apparaten wisselen gegevens uit met uw Azure IoT Central-toepassing. Een apparaat kan:

- Metingen zoals telemetrie verzenden.
- Synchroniseer de instellingen met uw toepassing.

De gegevens die een apparaat met uw toepassing kan uitwisselen, worden in Azure IoT Central gespecificeerd in een apparaatsjabloon. Zie [meta gegevens beheer](#metadata-management)voor meer informatie over Apparaatinstellingen.

Raadpleeg [connectiviteit](concepts-get-connected.md)van apparaten voor meer informatie over de manier waarop apparaten verbinding maken met uw Azure IOT Central-toepassing.

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-apparaten

En apparaten die zijn gemaakt met behulp van de [Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks), kunt u ook [Azure IOT edge apparaten](../../iot-edge/about-iot-edge.md) verbinden met een IOT Central-toepassing. Met IoT Edge kunt u Cloud Intelligence en aangepaste logica rechtstreeks uitvoeren op IoT-apparaten die worden beheerd door IoT Central. Met de IoT Edge runtime kunt u het volgende doen:

- Workloads op het apparaat installeren en bijwerken.
- IoT Edge beveiligings standaarden op het apparaat onderhouden.
- Ervoor zorgen dat de IoT Edge-modules altijd worden uitgevoerd.
- De status van de module aan de cloud rapporteren voor externe bewaking.
- De communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud beheren.

![Azure IoT Central met Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central kunt de volgende mogelijkheden voor IoT Edge apparaten:

- Device-sjablonen om de mogelijkheden van een IoT Edge apparaat te beschrijven, zoals:
  - Upload functionaliteit voor het implementatie manifest, waarmee u een manifest voor een apparaat wagen kunt beheren.
  - Modules die worden uitgevoerd op het IoT Edge apparaat.
  - De telemetrie van elke module verzendt.
  - De eigenschappen van elke module rapporteren.
  - De opdrachten waarop elke module reageert, worden beantwoord.
  - De relaties tussen een IoT Edge gateway apparaat en het downstream-apparaat.
  - Cloud eigenschappen die niet op het IoT Edge apparaat zijn opgeslagen.
  - Aanpassingen, dashboards en formulieren die deel uitmaken van uw IoT Central-toepassing.

  Zie het artikel [Azure IOT edge apparaten verbinden met een Azure IOT Central-toepassing](./concepts-iot-edge.md) voor meer informatie.

- De mogelijkheid om IoT Edge-apparaten op schaal in te richten met behulp van Azure IoT Device Provisioning Service
- Regels en acties.
- Aangepaste Dash boards en analyses.
- Doorlopende gegevens export van telemetrie vanaf IoT Edge apparaten.

### <a name="iot-edge-device-types"></a>IoT Edge typen apparaten

IoT Central classificeert IoT Edge apparaattypen als volgt:

- Blade apparaten. Een IoT Edge apparaat kan downstream Leaf-apparaten hebben, maar deze apparaten zijn niet ingericht in IoT Central.
- Gateway apparaten met downstream-apparaten. Zowel het gateway apparaat als de downstream-apparaten zijn ingericht in IoT Central

![Overzicht van IoT Central IoT Edge](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge patronen

IoT Central ondersteunt de volgende IoT Edge patronen voor apparaten:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge als blad apparaat

![IoT Edge als blad apparaat](./media/concepts-architecture/edgeasleafdevice.png)

Het IoT Edge apparaat is ingericht in IoT Central en downstream-apparaten en hun telemetrie wordt weer gegeven op het IoT Edge apparaat. Downstream-apparaten die zijn verbonden met het IoT Edge apparaat, zijn niet ingericht in IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge gateway apparaat verbonden met downstream-apparaten met identiteit

![IoT Edge met downstream-apparaat-id](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Het IoT Edge apparaat is ingericht in IoT Central samen met de downstream-apparaten die zijn verbonden met het IoT Edge apparaat. Runtime-ondersteuning voor het inrichten van downstream-apparaten via de gateway wordt momenteel niet ondersteund.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge gateway apparaat verbonden met downstream-apparaten met identiteit die is verschaft door de IoT Edge gateway

![IoT Edge met het downstream-apparaat zonder identiteit](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Het IoT Edge apparaat is ingericht in IoT Central samen met de downstream-apparaten die zijn verbonden met het IoT Edge apparaat. Runtime-ondersteuning van gateway die identiteit levert aan downstream-apparaten en het inrichten van downstream-apparaten wordt momenteel niet ondersteund. Als u uw eigen module voor identiteits vertalingen zet, kan IoT Central dit patroon ondersteunen.

## <a name="cloud-gateway"></a>Cloud gateway

Azure IoT Central maakt gebruik van Azure IoT Hub als een Cloud gateway die connectiviteit van apparaten mogelijk maakt. IoT Hub maakt het volgende mogelijk:

- Gegevens opname op schaal in de Cloud.
- Apparaatbeheer.
- Connectiviteit van beveiligde apparaten.

Zie voor meer informatie over IoT Hub [Azure IOT hub](../../iot-hub/index.yml).

Zie [connectiviteit van apparaten](concepts-get-connected.md)voor meer informatie over de connectiviteit van apparaten in azure IOT Central.

## <a name="data-stores"></a>Gegevensopslag

In azure IoT Central worden toepassings gegevens opgeslagen in de Cloud. De opgeslagen toepassings gegevens zijn onder andere:

- Device-sjablonen.
- Apparaat-id's.
- Meta gegevens van het apparaat.
- Gebruikers gegevens en-rollen.

Azure IoT Central gebruikt een time series Store voor de meet gegevens die vanaf uw apparaten worden verzonden. Time Series-gegevens van apparaten die worden gebruikt door de analyse service.

## <a name="analytics"></a>Analyse

De analyse service is verantwoordelijk voor het genereren van de aangepaste rapportage gegevens die door de toepassing worden weer gegeven. Een operator kan [de analyse aanpassen](howto-create-analytics.md) die in de toepassing wordt weer gegeven. De Analytics-service is boven op [Azure time series Insights](https://azure.microsoft.com/services/time-series-insights/) gebouwd en verwerkt de meet gegevens die vanaf uw apparaten worden verzonden.

## <a name="rules-and-actions"></a>Regels en acties

[Regels en acties](tutorial-create-telemetry-rules.md) kunnen nauw samen worden gebruikt om taken in de toepassing te automatiseren. Een Builder kan regels definiëren op basis van de telemetrie van apparaten, zoals de Tempe ratuur die een gedefinieerde drempel waarde overschrijdt. Azure IoT Central gebruikt een Stream-processor om te bepalen wanneer aan de voor waarden van de regel wordt voldaan. Wanneer aan een regel voorwaarde wordt voldaan, wordt een door de Builder gedefinieerde actie geactiveerd. Een actie kan bijvoorbeeld een e-mail verzenden om een technicus op de hoogte te stellen dat de Tempe ratuur op een apparaat te hoog is.

## <a name="metadata-management"></a>Beheer van meta gegevens

In een Azure IoT Central-toepassing worden met Apparaatinstellingen het gedrag en de mogelijkheden van typen apparaten gedefinieerd. Een sjabloon voor een koel kast bevat bijvoorbeeld de telemetrie die een koel kast naar uw toepassing verzendt.

![Sjabloon architectuur](media/concepts-architecture/template-architecture.png)

In een IoT Central- [apparaatprofiel](concepts-device-templates.md) bevat:

- Een **apparaatprofiel** om de mogelijkheden van een apparaat op te geven, zoals de telemetrie die het verzendt, de eigenschappen die de status van het apparaat definiëren en de opdrachten waarop het apparaat reageert. De mogelijkheden van het apparaat zijn ingedeeld in een of meer interfaces.
- Met **Cloud eigenschappen** geeft u de eigenschappen op IOT Central winkels voor een apparaat. Deze eigenschappen worden alleen opgeslagen in IoT Central en worden nooit naar een apparaat verzonden.
- Met **weer gaven** worden de Dash boards en formulieren opgegeven die de opbouw functie maakt, zodat de operator de apparaten kan controleren en beheren.
- Met **aanpassingen** kan de opbouw functie enkele van de definities in het model van het apparaat overschrijven om ze relevanter te maken voor de IOT Central-toepassing.

Een toepassing kan een of meer gesimuleerde en echte apparaten op basis van elk apparaatprofiel hebben.

## <a name="data-export"></a>Gegevensexport

In een Azure IoT Central-toepassing kunt u [uw gegevens voortdurend exporteren](howto-export-data.md) naar uw eigen azure-Event Hubs en Azure service bus exemplaren. U kunt uw gegevens ook periodiek exporteren naar uw Azure Blob-opslag account. IoT Central kunt metingen, apparaten en apparaatinstellingen exporteren.

## <a name="batch-device-updates"></a>Updates voor batch-apparaten

In een Azure IoT Central-toepassing kunt u [taken maken en uitvoeren](howto-run-a-job.md) om verbonden apparaten te beheren. Met deze taken kunt u bulksgewijs updates uitvoeren op eigenschappen of instellingen van het apparaat, of opdracht geven. U kunt bijvoorbeeld een taak maken om de snelheid van de ventilator voor meerdere gekoelde verkoop machines te verg Roten.

## <a name="role-based-access-control-rbac"></a>Toegangsbeheer op basis van rollen (RBAC)

Elke IoT Central toepassing heeft zijn eigen ingebouwde RBAC-systeem. Een [beheerder kan toegangs regels definiëren](howto-manage-users-roles.md) voor een Azure IOT Central-toepassing met behulp van een van de vooraf gedefinieerde rollen of door een aangepaste rol te maken. Rollen bepalen tot welke gebieden van de toepassing gebruikers toegang hebben en welke acties ze kunnen uitvoeren.

## <a name="security"></a>Beveiliging

Beveiligings functies in azure IoT Central zijn onder andere:

- Gegevens worden in transit en op rest versleuteld.
- Verificatie wordt door Azure Active Directory of een micro soft-account verschaft. Verificatie met twee factoren wordt ondersteund.
- Volledige isolatie van de Tenant.
- Beveiliging op apparaatniveau.

## <a name="ui-shell"></a>UI-shell

De UI-shell is een moderne, op de browser gebaseerde toepassing die in de Cloud is gebaseerd.
Een beheerder kan de gebruikers interface van de toepassing aanpassen door aangepaste Thema's toe te passen en de Help-koppelingen te wijzigen zodat deze naar uw eigen aangepaste Help-bronnen verwijzen. Zie het artikel over [de Azure IOT Central-gebruikers interface aanpassen](howto-customize-ui.md) voor meer informatie over UI-aanpassing.

Een operator kan gepersonaliseerde toepassings dashboards maken. U kunt verschillende Dash boards hebben die verschillende gegevens weer geven en hiertussen scha kelen.

## <a name="next-steps"></a>Volgende stappen

Nu u over de architectuur van Azure IoT Central hebt geleerd, is de voorgestelde volgende stap in azure IoT Central meer informatie over de [connectiviteit van apparaten](concepts-get-connected.md) .