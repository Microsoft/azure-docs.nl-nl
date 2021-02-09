---
title: Referentiearchitectuur voor een oplossing voor het bewaken van het waterverbruik die is gebouwd met Azure IoT Central | Microsoft Docs
description: Leer concepten voor een oplossing voor het bewaken van het waterverbruik die is gebouwd met Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831651"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Referentiearchitectuur voor de bewaking van waterverbruik 

Een oplossing voor het bewaken van het waterverbruik kan worden ontwikkeld met behulp van de **Azure IoT Central app-sjabloon** als uitgangspunt voor een IoT-toepassing. In dit artikel vindt u uitgebreide richtlijnen voor referentiearchitectuur voor het bouwen van een end-to-end-oplossing. 

![Architectuur voor bewaking van waterverbruik maken](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Concepten:

1. Apparaten en connectiviteit  
1. IoT Central 
1. Uitbreidbaarheid en integraties
1. Zakelijke toepassingen

Laten we eens kijken naar de belangrijkste onderdelen die in het algemeen een rol spelen in een oplossing voor het bewaken van waterverbruik.

## <a name="devices-and-connectivity"></a>Apparaten en connectiviteit 
In dit gedeelte wordt verwezen naar apparaten die worden gebruikt voor slimme wateroplossingen, zoals bewaking van de waterkwaliteit of het waterverbruik, in het algemeen slimme waterapparaten genoemd. Slimme waterapparaten kunnen stromingsmeters, monitors voor waterkwaliteit, slimme kleppen, lekkagedetectoren, enzovoort zijn.

Apparaten die worden gebruikt in slimme-waterapparatuur zijn over het algemeen via een externe netwerkprovider verbonden via LPWAN (Low Power Wide Area Networks). Voor deze typen apparaten kunt u [Azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) gebruiken om uw apparaatgegevens naar uw IoT-toepassing in Azure IoT Central te verzenden. U kunt ook gebruikmaken van apparaatgateways die IP-capabel zijn en rechtstreeks verbinding kunnen maken met IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central is een IoT-app-platform, waarmee u snel aan de slag kunt met uw IoT-oplossing. U kunt uw oplossing voorzien van een merk, aanpassen en integreren met services van derden.
Nadat u uw slimme waterapparaten hebt aangesloten op IoT Central, kunt u het apparaat instructies geven en bedienen, beschikt u over een bewakings- en waarschuwingssysteem, een gebruikersinterface met ingebouwde RBAC, configureerbare dashboards om inzichten te verkrijgen en mogelijkheden om uit te breiden. 


## <a name="extensibility-and-integrations"></a>Uitbreidbaarheid en integraties
U kunt uw IoT-toepassing uitbreiden in IoT Central, en optioneel:
* uw IoT-gegevens voor geavanceerde analyses transformeren en integreren, bijvoorbeeld voor het trainen van machine-learningmodellen, door middel van continue gegevensexport vanuit de IoT Central-toepassing
* werkstromen in andere systemen automatiseren door acties te activeren met behulp van Power Automate of webhooks van de IoT Central-toepassing
* programmatisch toegang krijgen tot uw IoT-toepassing in IoT Central via IoT Central-API's

## <a name="business-applications"></a>Zakelijke toepassingen 
De IoT-gegevens kunnen worden gebruikt om verschillende soorten zakelijke toepassingen binnen een afvalverwerkingsbedrijf aan te sturen. Als u wilt weten hoe u de IoT Central-toepassing voor het bewaken van het waterverbruik met services in het veld kunt verbinden, volgt u de zelfstudie over [integreren met Dynamics 365-veldservices](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Volgende stappen
* Informatie over hoe u [een IoT Central-toepassing voor waterverbruik maakt](./tutorial-water-consumption-monitoring.md)
* Meer informatie over [IoT Central-sjablonen voor de overheid](./overview-iot-central-government.md)
* Zie [Overzicht van IoT Central](../core/overview-iot-central.md) voor meer informatie over IoT Central
