---
title: Lijst met ingebouwde & aangepaste waarschuwingen
description: Meer informatie over beveiligings waarschuwingen en aanbevolen herstel met Defender voor de functies en service van IoT Hub.
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
ms.openlocfilehash: ef33851600c576494e4e0903c6ab8ffefc9a1a59
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636489"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Beveiligings waarschuwingen voor Defender voor IoT Hub

Defender voor IoT analyseert uw IoT-oplossing voortdurend met geavanceerde analyses en bedreigings informatie om u te waarschuwen voor schadelijke activiteiten.
Daarnaast kunt u aangepaste waarschuwingen maken op basis van uw kennis van het verwachte gedrag van het apparaat.
Een waarschuwing fungeert als een indicatie van mogelijke inbreuk en moet worden onderzocht en opgelost.

In dit artikel vindt u een lijst met ingebouwde waarschuwingen die kunnen worden geactiveerd op uw IoT Hub.
Naast ingebouwde waarschuwingen kunt u met Defender voor IoT aangepaste waarschuwingen definiëren op basis van het verwachte gedrag van IoT Hub en/of apparaat.
Zie [aanpas bare waarschuwingen](concept-customizable-security-alerts.md)voor meer informatie.

## <a name="built-in-alerts-for-iot-hub"></a>Ingebouwde waarschuwingen voor IoT Hub

| Severity | Naam | Beschrijving | Voorgestelde herstel |
|--|--|--|--|
| **Gemiddelde** Ernst |  |  |  |
| Er is een nieuw certificaat toegevoegd aan een IoT Hub | Normaal | Er is een certificaat met de naam \' % {DescCertificateName} \' toegevoegd aan IOT hub \' % {DescIoTHubName} \' . Als deze actie is uitgevoerd door een niet-geautoriseerde partij, kan dit wijzen op schadelijke activiteiten. | 1. Controleer of het certificaat is toegevoegd door een geautoriseerde partij. <br> 2. als deze niet is toegevoegd door een geautoriseerde partij, verwijdert u het certificaat en verdeelt u de waarschuwing naar het beveiligings team van de organisatie. |
| Het certificaat is verwijderd uit een IoT Hub | Normaal | Een certificaat met de naam \' % {DescCertificateName} \' is verwijderd uit IOT hub \' % {DescIoTHubName} \' . Als deze actie is uitgevoerd door een niet-geautoriseerde partij, kan dit wijzen op een schadelijke activiteit. | 1. Controleer of het certificaat is verwijderd door een bevoegde partij. <br> 2. als het certificaat niet is verwijderd door een bevoegde partij, voegt u het certificaat dan toe en doorstuurt u de waarschuwing naar het beveiligings team van de organisatie. |
| Er is een niet-geslaagde poging gedetecteerd om een certificaat toe te voegen aan een IoT Hub | Normaal | Er is een mislukte poging gedaan om het certificaat \' % {DescCertificateName} toe \' te voegen aan IOT hub \' % {DescIoTHubName} \' . Als deze actie is uitgevoerd door een niet-geautoriseerde partij, kan dit wijzen op schadelijke activiteiten. | Zorg ervoor dat machtigingen voor het wijzigen van certificaten alleen worden verleend aan geautoriseerde partijen. |
| Er is een niet-geslaagde poging gedetecteerd om een certificaat uit een IoT Hub te verwijderen | Normaal | Er is een mislukte poging gedaan om het certificaat \' % {DescCertificateName} te verwijderen \' uit IOT hub \' % {DescIoTHubName} \' . Als deze actie is uitgevoerd door een niet-geautoriseerde partij, kan dit wijzen op schadelijke activiteiten. | Zorg ervoor dat machtigingen voor het wijzigen van certificaten alleen worden verleend aan een gemachtigde partij. |
| x. 509 apparaat certificaat vingerafdruk komt niet overeen | Normaal | de vinger afdruk van het certificaat van x. 509 komt niet overeen met de configuratie. | Controleer waarschuwingen op de apparaten. Er is geen verdere actie vereist. |
| x. 509-certificaat is verlopen | Normaal | Het certificaat van X. 509-apparaat is verlopen. | Dit kan een legitiem apparaat zijn met een verlopen certificaat of een poging om een legitiem apparaat te imiteren. Als het legitieme apparaat momenteel op de juiste wijze communiceert, is dit waarschijnlijk een imitatie poging. |
| **Lage** urgentie |  |  |  |
| Er is geprobeerd een diagnostische instelling van een gedetecteerde IoT Hub toe te voegen of te bewerken | Beperkt | Poging om de diagnostische instellingen van een IoT Hub toe te voegen of te bewerken is gedetecteerd. Met Diagnostische instellingen kunt u de activiteiten sporen voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. Als deze actie niet is uitgevoerd door een bevoegde partij, kan dit wijzen op schadelijke activiteiten. | 1. Controleer of het certificaat is verwijderd door een bevoegde partij.<br> 2. als het certificaat niet is verwijderd door een bevoegde partij, voegt u het certificaat opnieuw toe en geeft u de waarschuwing door aan uw gegevens beveiligings team. |
| Poging tot het verwijderen van een diagnostische instelling van een gedetecteerde IoT Hub | Beperkt | Er is% {DescAttemptStatusMessage} \' geprobeerd de diagnostische instelling \' % {DescDiagnosticSettingName} \' van IOT hub \' % {DescIoTHubName} toe te voegen of te bewerken \' . Met de diagnostische instelling kunt u de activiteiten sporen voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. Als deze actie niet is uitgevoerd door een bevoegde partij, kan dit wijzen op een schadelijke activiteit. | Zorg ervoor dat machtigingen voor het wijzigen van diagnostische instellingen alleen worden toegekend aan een geautoriseerde partij. |
| Verlopen SAS-token | Beperkt | Verlopen SAS-token dat wordt gebruikt door een apparaat | Dit is mogelijk een legitiem apparaat met een verlopen token of een poging om een legitiem apparaat te imiteren. Als het legitieme apparaat momenteel op de juiste wijze communiceert, is dit waarschijnlijk een imitatie poging. |
| Ongeldige SAS-token handtekening | Beperkt | Een SAS-token dat door een apparaat wordt gebruikt, heeft een ongeldige hand tekening. De hand tekening komt niet overeen met de primaire of secundaire sleutel. | Bekijk de waarschuwingen op de apparaten. Er is geen verdere actie vereist. |

## <a name="next-steps"></a>Volgende stappen

- [Overzicht](overview.md) van Defender voor IOT-service
- Meer informatie over [het verkrijgen van toegang tot uw beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)
