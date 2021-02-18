---
title: Aangepaste beveiligings waarschuwingen voor IoT Hub
description: Meer informatie over aanpas bare beveiligings waarschuwingen en aanbevolen herstel met Defender voor de functies en service van IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 04198432f2b600a3c703d5e4f253656f116000db
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636523"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender voor aangepaste beveiligings waarschuwingen voor IoT Hub

Defender voor IoT analyseert uw IoT-oplossing voortdurend met geavanceerde analyses en bedreigings informatie om u te waarschuwen voor schadelijke activiteiten.

We raden u aan om aangepaste waarschuwingen te maken op basis van uw kennis van het verwachte gedrag van het apparaat om te zorgen dat waarschuwingen fungeren als de meest efficiënte indica toren van mogelijke inbreuk in uw unieke organisatie-implementatie en landschap.

De volgende lijsten van de Defender voor IoT-waarschuwingen kunt u bepalen op basis van het verwachte IoT Hub gedrag. Zie [aangepaste waarschuwingen maken](quickstart-create-custom-alerts.md)voor meer informatie over het aanpassen van elke waarschuwing.

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Ingebouwde aangepaste waarschuwingen in de IoT Hub

| Severity | Naam waarschuwing | Gegevensbron | Description | Voorgestelde herstel |
|--|--|--|--|--|
| Beperkt | Aangepaste waarschuwing: het aantal Cloud-naar-apparaat-berichten in het AMQP-protocol valt buiten het toegestane bereik | IoT Hub | Het aantal Cloud-naar-apparaat-berichten (AMQP-Protocol) binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: het aantal geweigerde Cloud-naar-apparaat-berichten in het AMQP-protocol valt buiten het toegestane bereik | IoT Hub | Het aantal Cloud-naar-apparaat-berichten (AMQP-Protocol) dat door het apparaat is afgewezen, binnen een bepaald tijd venster zich buiten het huidige geconfigureerde en toegestane bereik bevinden. |  |
| Beperkt | Aangepaste waarschuwing: het aantal van de Cloud berichten in het AMQP-protocol valt buiten het toegestane bereik | IoT Hub | De hoeveelheid apparaat naar Cloud berichten (AMQP-Protocol) binnen een bepaald tijd venster valt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: het aantal aanroepen van de methode direct valt buiten het toegestane bereik | IoT Hub | De hoeveelheid directe methode aanroepen binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: het aantal uploads van bestanden valt buiten het toegestane bereik | IoT Hub | De hoeveelheid uploads van bestanden binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: het aantal Cloud-naar-apparaat-berichten in het HTTP-protocol ligt buiten het toegestane bereik | IoT Hub | De hoeveelheid Cloud-naar-apparaat-berichten (HTTP-protocol) in een tijd venster bevindt zich niet in het geconfigureerde toegestane bereik |
| Beperkt | Aangepaste waarschuwing: het aantal geweigerde Cloud-naar-apparaat-berichten in het HTTP-protocol bevindt zich niet in het toegestane bereik | IoT Hub | De hoeveelheid Cloud-naar-apparaat-berichten (HTTP-protocol) binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |
| Beperkt | Aangepaste waarschuwing: het aantal van het apparaat naar de Cloud berichten in het HTTP-protocol ligt buiten het toegestane bereik | IoT Hub | De hoeveelheid apparaat naar Cloud berichten (HTTP-protocol) binnen een bepaald tijd venster valt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: het aantal Cloud-naar-apparaat-berichten in het MQTT-protocol valt buiten het toegestane bereik | IoT Hub | De hoeveelheid Cloud-naar-apparaat-berichten (MQTT-Protocol) binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: het aantal geweigerde Cloud-naar-apparaat-berichten in het MQTT-protocol valt buiten het toegestane bereik | IoT Hub | De hoeveelheid Cloud-naar-apparaat-berichten (MQTT-Protocol) die binnen een bepaald tijd venster van het apparaat worden afgewezen, ligt buiten het huidige geconfigureerde en toegestane bereik. |
| Beperkt | Aangepaste waarschuwing: het aantal van de Cloud berichten in het MQTT-protocol valt buiten het toegestane bereik | IoT Hub | De hoeveelheid apparaat naar Cloud berichten (MQTT-Protocol) binnen een bepaald tijd venster valt buiten het huidige geconfigureerde en toegestane bereik. |
| Beperkt | Aangepaste waarschuwing: het aantal opschoningen van de opdracht wachtrij die buiten het toegestane bereik vallen | IoT Hub | De hoeveelheid leegmaak opdrachten van de opdracht wachtrij binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: het aantal module dubbele updates valt buiten het toegestane bereik | IoT Hub | De hoeveelheid module dubbele updates binnen een specifiek tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |
| Beperkt | Aangepaste waarschuwing: het aantal niet-geautoriseerde bewerkingen ligt buiten het toegestane bereik | IoT Hub | De hoeveelheid niet-geautoriseerde bewerkingen binnen een bepaald tijd venster bevindt zich buiten het huidige geconfigureerde en toegestane bereik. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [aanpassen van een waarschuwing](quickstart-create-custom-alerts.md)
- [Overzicht](overview.md) van Defender voor IOT-service
- Meer informatie over [het verkrijgen van toegang tot uw beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)
