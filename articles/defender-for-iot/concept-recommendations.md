---
title: Beveiligings aanbevelingen voor IoT Hub
description: Meer informatie over het concept van beveiligings aanbevelingen en hoe ze worden gebruikt in de Defender voor IoT Hub.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779338"
---
# <a name="security-recommendations-for-iot-hub"></a>Beveiligings aanbevelingen voor IoT Hub

Met Defender voor IoT worden uw Azure-resources en IoT-apparaten gescand en worden beveiligings aanbevelingen gegeven om uw kwets baarheid te beperken.
Beveiligings aanbevelingen zijn maat regelen en zijn bedoeld om klanten te helpen bij het naleven van de best practices voor beveiliging.

In dit artikel vindt u een lijst met aanbevelingen die kunnen worden geactiveerd op uw IoT Hub.

## <a name="built-in-recommendations-in-iot-hub"></a>Ingebouwde aanbevelingen in IoT Hub

Aanbevelings waarschuwingen bieden inzicht en suggesties voor acties om de beveiliging postuur van uw omgeving te verbeteren.

| Severity | Name | Gegevensbron | Beschrijving |
|--|--|--|--|
| Hoog | Identieke verificatie referenties die worden gebruikt door meerdere apparaten | IoT Hub | IoT Hub authenticatie referenties worden gebruikt door meerdere apparaten. Dit proces duidt mogelijk op een illegitimate-apparaat dat een legitiem apparaat imiteert. Dubbel referentie gebruik verhoogt het risico van imitatie van apparaten door een schadelijke actor. |
| Normaal | Het standaard IP-filter beleid moet worden geweigerd | IoT Hub | Voor de configuratie van de IP-filter moeten regels worden gedefinieerd voor het toegestane verkeer, en standaard moeten alle andere verkeer standaard worden geweigerd. |
| Normaal | De IP-filter regel bevat een groot IP-bereik | IoT Hub | Een IP-adres bereik van de bron filter regel toestaan is te groot. Over de regel matig strikte regels kunnen uw IoT-hub beschikbaar maken voor kwaad aardige actors. |
| Beperkt | Diagnostische logboeken inschakelen in IoT Hub | IoT Hub | Logboeken inschakelen en ze tot een jaar bewaren. Door Logboeken te behouden, kunt u de activiteiten sporen voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |

## <a name="next-steps"></a>Volgende stappen

- [Overzicht](overview.md) van Defender voor IOT-service
- Meer informatie over [het verkrijgen van toegang tot uw beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)
