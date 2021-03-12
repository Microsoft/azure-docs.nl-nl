---
title: Continue patiëntbewakingsarchitectuur in Azure IoT Central | Microsoft Docs
description: Zelfstudie - Meer informatie over de architectuur van een oplossing voor doorlopende patiëntbewaking.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6f7359b2b2fb0a1ea6ce92ec52bba15fc74fc75a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017151"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architectuur van continue patiëntbewaking

In dit artikel wordt de architectuur beschreven van een oplossing die is gebouwd op basis van de toepassingsjabloon van **doorlopende patiëntbewaking**:

Continue patiëntbewakingsoplossingen kunnen gebouwd worden met behulp van de aangeboden app-sjabloon en met behulp van de architectuur die hieronder wordt beschreven.

:::image type="content" source="media/cpm-architecture.png" alt-text="Architectuur van continue patiëntbewaking":::

## <a name="details"></a>Details

In deze sectie wordt elk deel van het architectuurdiagram uitvoeriger beschreven:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Medische apparaten die gebruikmaken van Bluetooth Low Energy (BLE)

Veel medische wearables die worden gebruikt in IoT-oplossingen voor de gezondheidszorg zijn BLE-apparaten. Deze apparaten kunnen niet rechtstreeks communiceren met de cloud en moeten een gateway gebruiken om gegevens uit te wisselen met uw cloudoplossing. Deze architectuur gebruikt een mobiele app als de gateway.

### <a name="mobile-phone-gateway"></a>Gateway voor mobiele telefoon

De primaire functie van de mobiele app is het verzamelen van BLE-gegevens van medische apparaten en deze aan IoT Central doorgeven. De app begeleidt patiënten ook bij de installatie van apparaten en stelt hen in staat hun persoonlijke gezondheidsgegevens in te zien. Andere oplossingen kunnen een tabletgateway of een statische gateway in een ziekenhuiskamer gebruiken. Er is een opensourcevoorbeeld van een mobiele app beschikbaar voor Android en iOS. U kunt dit voorbeeld gebruiken als uitgangspunt om uw app te ontwikkelen. Meer informatie vindt u in de [mobiele app IoT Central Continuous Patient Monitoring](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exporteren naar Azure API for FHIR&reg;

Azure IoT Central voldoet aan HIPAA en is gecertificeerd door HITRUST&reg;. U kunt ook gezondheidsgegevens van patiënten naar andere services verzenden met behulp van de [Azure API for FHIR](../../healthcare-apis/fhir/overview.md). Azure API for FHIR is een op standaarden gebaseerde API voor klinische gezondheidsgegevens. Met de [Azure IoT Connector for FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) kunt u de Azure API for FHIR gebruiken als een doorlopend gegevensexportdoel van IoT Central.

### <a name="machine-learning"></a>Machine learning

Gebruik machine-learningmodellen met uw FHIR-gegevens om inzichten te genereren en de besluitvorming van uw zorgteam te ondersteunen. Bekijk de [documentatie voor Azure Machine Learning](../../machine-learning/index.yml) voor meer informatie.

### <a name="provider-dashboard"></a>Provider-dashboard

Gebruik de gegevens van Azure API for FHIR om een dashboard te bouwen met patiëntinzichten of om de gegevens rechtstreeks te integreren in een elektronische medische record die wordt gebruikt door zorgteams. Zorgteams kunnen het dashboard gebruiken om patiënten te assisteren en vroegtijdige waarschuwingssignalen van verslechtering te identificeren. Zie voor meer informatie de zelfstudie [Een Power BI-providerdashboard maken](tutorial-health-data-triage.md).

## <a name="next-steps"></a>Volgende stappen

De aanbevolen volgende stap is: [Leer hoe u een toepassingssjabloon voor doorlopende patiëntbewaking kunt implementeren](tutorial-continuous-patient-monitoring.md).