---
title: Architectuurconcepten in Azure IoT Central | Microsoft Docs
description: In dit artikel worden de belangrijkste concepten besproken die betrekking hebben op de architectuur van Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bcda4ca252101ed1505f71a1b5f9fe9a0d8d16b9
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728388"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-architectuur

In dit artikel vindt u een overzicht van de belangrijkste concepten in de Azure IoT Central architectuur.

## <a name="devices"></a>Apparaten

Apparaten wisselen gegevens uit met uw Azure IoT Central toepassing. Een apparaat kan:

- Metingen zoals telemetrie verzenden.
- Synchroniseer instellingen met uw toepassing.

De gegevens die een apparaat met uw toepassing kan uitwisselen, worden in Azure IoT Central gespecificeerd in een apparaatsjabloon. Zie Apparaatsjablonen voor meer informatie [over apparaatsjablonen.](concepts-device-templates.md)

Zie Apparaatconnectiviteit voor meer informatie over hoe apparaten verbinding maken Azure IoT Central [uw toepassing.](concepts-get-connected.md)

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-apparaten

Naast apparaten die zijn gemaakt met behulp van de [Azure IoT SDK's,](https://github.com/Azure/azure-iot-sdks)kunt u ook Azure IoT Edge [verbinden](../../iot-edge/about-iot-edge.md) met een IoT Central toepassing. IoT Edge kunt u cloudintelligentie en aangepaste logica rechtstreeks uitvoeren op IoT-apparaten die worden beheerd door IoT Central. Met IoT Edge runtime kunt u het volgende doen:

- Workloads op het apparaat installeren en bijwerken.
- Houd IoT Edge beveiligingsstandaarden op het apparaat aan.
- Ervoor zorgen dat de IoT Edge-modules altijd worden uitgevoerd.
- De status van de module aan de cloud rapporteren voor externe bewaking.
- De communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud beheren.

![Azure IoT Central met Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central schakelt u de volgende mogelijkheden in voor IoT Edge apparaten:

- Apparaatsjablonen om de mogelijkheden van een IoT Edge te beschrijven, zoals:
  - Uploadmogelijkheid voor distributiemanifest, waarmee u een manifest voor een hele groep apparaten kunt beheren.
  - Modules die worden uitgevoerd op IoT Edge apparaat.
  - De telemetrie die elke module verzendt.
  - De eigenschappen die elke module rapporteert.
  - De opdrachten waar elke module op reageert.
  - De relaties tussen een IoT Edge gatewayapparaat en downstreamapparaat.
  - Cloudeigenschappen die niet zijn opgeslagen op het IoT Edge apparaat.
  - Aanpassingen, dashboards en formulieren die deel uitmaken van uw IoT Central-toepassing.

  Zie het artikel Connect Azure IoT Edge devices to an Azure IoT Central application (Verbinding maken [Azure IoT Edge met een Azure IoT Central toepassing).](./concepts-iot-edge.md)

- De mogelijkheid om apparaten op schaal IoT Edge inrichten met behulp van Azure IoT Device Provisioning Service
- Regels en acties.
- Aangepaste dashboards en analyses.
- Continue gegevensexport van telemetrie van IoT Edge apparaten.

### <a name="iot-edge-device-types"></a>IoT Edge typen apparaten

IoT Central classificeert IoT Edge apparaattypen als volgt:

- Leaf-apparaten. Een IoT Edge kan downstream leaf-apparaten hebben, maar deze apparaten worden niet ingericht in IoT Central.
- Gatewayapparaten met downstreamapparaten. Zowel gatewayapparaten als downstreamapparaten worden ingericht in IoT Central

![IoT Central met IoT Edge Overview](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge patronen

IoT Central ondersteunt de volgende IoT Edge-apparaatpatronen:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge als leaf-apparaat

![IoT Edge als leaf-apparaat](./media/concepts-architecture/edgeasleafdevice.png)

Het IoT Edge apparaat wordt ingericht in IoT Central en alle downstreamapparaten en de telemetrie wordt weergegeven als afkomstig van het IoT Edge apparaat. Downstreamapparaten die zijn verbonden met IoT Edge apparaat, worden niet ingericht in IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge-gatewayapparaat met identiteit verbonden met downstreamapparaten

![IoT Edge downstreamapparaat-id](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Het IoT Edge apparaat wordt ingericht in IoT Central downstreamapparaten die zijn verbonden met IoT Edge apparaat. Runtime-ondersteuning voor het inrichten van downstreamapparaten via de gateway wordt momenteel niet ondersteund.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge-gatewayapparaat dat is verbonden met downstreamapparaten met een identiteit die wordt geleverd door de IoT Edge-gateway

![IoT Edge downstreamapparaat zonder identiteit](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Het IoT Edge apparaat wordt ingericht in IoT Central downstreamapparaten die zijn verbonden met IoT Edge apparaat. Runtime-ondersteuning voor gateway die identiteit biedt aan downstreamapparaten en inrichting van downstreamapparaten wordt momenteel niet ondersteund. Als u uw eigen module voor identiteitsvertaling gebruikt, IoT Central dit patroon ondersteunen.

## <a name="cloud-gateway"></a>Cloudgateway

Azure IoT Central gebruikt Azure IoT Hub als een cloudgateway die apparaatconnectiviteit mogelijk maakt. IoT Hub schakelt het volgende in:

- Gegevens opnemen op schaal in de cloud.
- Apparaatbeheer.
- Beveiligde apparaatconnectiviteit.

Zie voor meer informatie IoT Hub meer informatie [Azure IoT Hub.](../../iot-hub/index.yml)

Zie Apparaatconnectiviteit voor meer Azure IoT Central [over apparaatconnectiviteit in de Azure IoT Central.](concepts-get-connected.md)

## <a name="data-stores"></a>Gegevensopslag

Azure IoT Central worden toepassingsgegevens opgeslagen in de cloud. Opgeslagen toepassingsgegevens zijn onder andere:

- Apparaatsjablonen.
- Apparaat-id's.
- Metagegevens van het apparaat.
- Gebruikers- en rolgegevens.

Azure IoT Central gebruikt een tijdreeksopslag voor de meetgegevens die vanaf uw apparaten worden verzonden. Tijdreeksgegevens van apparaten die worden gebruikt door de analyseservice.

## <a name="data-export"></a>Gegevensexport

In een Azure IoT Central kunt u [uw](howto-export-data.md) gegevens continu exporteren naar uw eigen Azure Event Hubs en Azure Service Bus instanties. U kunt uw gegevens ook periodiek exporteren naar uw Azure Blob Storage-account. IoT Central kunt metingen, apparaten en apparaatsjablonen exporteren.

## <a name="batch-device-updates"></a>Batch-apparaatupdates

In een Azure IoT Central kunt u taken maken en [uitvoeren om](howto-run-a-job.md) verbonden apparaten te beheren. Met deze taken kunt u bulksgewijs updates uitvoeren voor apparaateigenschappen of -instellingen, of opdrachten uitvoeren. U kunt bijvoorbeeld een taak maken om de ventilatorsnelheid voor meerdere gekoelde verkoopautomaat te verhogen.

## <a name="role-based-access-control-rbac"></a>Toegangsbeheer op basis van rollen (RBAC)

Elke IoT Central toepassing heeft een eigen ingebouwd RBAC-systeem. Een [beheerder kan toegangsregels voor een](howto-manage-users-roles.md) Azure IoT Central definiëren met behulp van een van de vooraf gedefinieerde rollen of door een aangepaste rol te maken. Rollen bepalen tot welke gebieden van de toepassing gebruikers toegang hebben en welke acties ze kunnen uitvoeren.

## <a name="security"></a>Beveiliging

Beveiligingsfuncties binnen Azure IoT Central zijn onder andere:

- Gegevens worden tijdens de overdracht en at-rest versleuteld.
- Verificatie wordt geleverd door een Azure Active Directory of een Microsoft-account. Verificatie met twee factoren wordt ondersteund.
- Volledige tenantisolatie.
- Beveiliging op apparaatniveau.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over de architectuur van Azure IoT Central, is de voorgestelde volgende stap om meer te weten te komen over apparaatconnectiviteit [in](concepts-get-connected.md) Azure IoT Central.