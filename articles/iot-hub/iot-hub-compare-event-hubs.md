---
title: Azure-IoT Hub vergelijken met Azure Event Hubs | Microsoft Docs
description: Een vergelijking van de IoT Hub en Event Hubs Azure-Services die functionele verschillen en use cases markeren. De vergelijking bevat ondersteunde protocollen, Apparaatbeheer, bewaking en het uploaden van bestanden.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 56bb179c50862f09f1b789c359db97976017bbb5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142760"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT-apparaten verbinden met Azure: IoT Hub en Event Hubs

Azure biedt services die specifiek zijn ontwikkeld voor diverse soorten connectiviteit en communicatie om u te helpen uw gegevens te verbinden met de kracht van de Cloud. Zowel Azure IoT Hub als Azure Event Hubs zijn Cloud Services die grote hoeveel heden gegevens kunnen opnemen en verwerken of die gegevens voor zakelijke inzichten opslaan. De twee services zijn vergelijkbaar met de ondersteuning voor opname van gegevens met lage latentie en hoge betrouw baarheid, maar ze zijn ontworpen voor verschillende doel einden. IoT Hub is ontwikkeld om de unieke vereisten van het verbinden van IoT-apparaten met de Azure-Cloud te verhelpen, terwijl Event Hubs is ontworpen voor big data streaming. Micro soft raadt aan Azure IoT Hub te gebruiken om IoT-apparaten te verbinden met Azure

Azure IoT Hub is de Cloud gateway die IoT-apparaten verbindt met het verzamelen van gegevens en het maken van zakelijke inzichten en automatisering. Daarnaast bevat IoT Hub functies die de relatie tussen uw apparaten en uw back-end-systemen verrijkt. Bidirectionele communicatie mogelijkheden betekenen dat wanneer u gegevens van apparaten ontvangt, ook opdrachten en beleids regels terug naar apparaten kunnen worden verzonden. Gebruik bijvoorbeeld Cloud-naar-apparaat-berichten om eigenschappen bij te werken of acties voor Apparaatbeheer aan te roepen. Met Cloud-naar-apparaat-communicatie kunt u ook Cloud Intelligence naar uw edge-apparaten verzenden met Azure IoT Edge. De unieke identiteit op apparaatniveau die wordt verschaft door IoT Hub helpt uw IoT-oplossing beter te beveiligen tegen mogelijke aanvallen. 

[Azure Event hubs](../event-hubs/event-hubs-about.md) is de Big data streaming service van Azure. Het is ontworpen voor scenario's voor gegevensstromen met een hoge doorvoer waarbij klanten miljarden aanvragen per dag kunnen verzenden. Event Hubs maakt gebruik van een gepartitioneerd verbruikersmodel om uw stroom uit te breiden, en is geïntegreerd in de big data- en analyseservices van Azure, met inbegrip van Databricks, Stream Analytics, ADLS en HDInsight. Met functies als Event Hubs vastleggen en automatisch verg Roten, is deze service ontworpen ter ondersteuning van uw big data apps en oplossingen. Daarnaast gebruikt IoT Hub Event Hubs voor het pad van de telemetrie stroom, zodat uw IoT-oplossing ook profiteert van de enorme kracht van Event Hubs.

Om samen te vatten zijn beide oplossingen ontworpen voor gegevens opname op grote schaal. Alleen IoT Hub biedt de uitgebreide IoT-specifieke mogelijkheden die zijn ontworpen voor u om de bedrijfs waarde van uw IoT-apparaten met de Azure-Cloud te maximaliseren.  Als uw IoT-traject gewoon begint, kunt u vanaf IoT Hub ter ondersteuning van uw scenario's voor gegevens opname zorgen dat u direct toegang hebt tot de volledige IoT-mogelijkheden, zodra uw zakelijke en technische behoeften hiervoor zijn vereist.

De volgende tabel bevat informatie over de manier waarop de twee lagen van IoT Hub worden vergeleken met Event Hubs wanneer u deze voor IoT-mogelijkheden evalueert. Zie [How to choose the right IOT hub tier](iot-hub-scaling.md)voor meer informatie over de standaard-en basis lagen van IOT hub.

| IoT-mogelijkheid | IoT Hub Standard-laag | IoT Hub Basic-laag | Event Hubs |
| --- | --- | --- | --- |
| Apparaat-naar-Cloud-berichten | ![Selecteren][checkmark] | ![Selecteren][checkmark] | ![Selecteren][checkmark] |
| Protocollen: HTTPS, AMQP, AMQP over websockets | ![Selecteren][checkmark] | ![Selecteren][checkmark] | ![Selecteren][checkmark] |
| Protocollen: MQTT, MQTT over websockets | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Identiteit per apparaat | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Bestand uploaden van apparaten | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Device Provisioning Service | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Cloud-naar-apparaat-berichten | ![Selecteren][checkmark] |  |  |
| Dubbel apparaat-en Apparaatbeheer | ![Selecteren][checkmark] |  |  |
| Apparaatversleuteling (preview-versie) | ![Selecteren][checkmark] |  |  |
| IoT Edge | ![Selecteren][checkmark] |  |  |

Zelfs als de enige use-case apparaat-naar-Cloud gegevens opname is, raden wij u ten zeerste aan IoT Hub uit te voeren, omdat het een service biedt die is ontworpen voor IoT-connectiviteit van apparaten. 

### <a name="next-steps"></a>Volgende stappen

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)als u meer wilt weten over de mogelijkheden van IOT hub.

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png