---
title: Aangepaste waarschuwingen op basis van agent beveiliging
description: Meer informatie over aanpas bare beveiligings waarschuwingen en aanbevolen herstel met Defender voor de functies en service van het IoT-apparaat.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 2fb1385c12cbd9d0479d8528f54aad8816393ad1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784914"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Aangepaste beveiligings waarschuwingen voor Defender voor IoT-apparaten

Defender voor IoT analyseert uw IoT-oplossing voortdurend met geavanceerde analyses en bedreigings informatie om u te waarschuwen voor schadelijke activiteiten.

We raden u aan om aangepaste waarschuwingen te maken op basis van uw kennis van het verwachte gedrag van het apparaat om te zorgen dat waarschuwingen fungeren als de meest efficiënte indica toren van mogelijke inbreuk in uw unieke organisatie-implementatie en landschap.

De volgende lijsten van de Defender voor IoT-waarschuwingen kunt u bepalen op basis van uw verwachte IoT-apparaat gedrag. Zie [aangepaste waarschuwingen maken](quickstart-create-custom-alerts.md)voor meer informatie over het aanpassen van elke waarschuwing.

## <a name="agent-based-security-custom-alerts"></a>Aangepaste waarschuwingen op basis van agent beveiliging

| Severity | Naam waarschuwing | Gegevensbron | Description | Voorgestelde herstel |
|--|--|--|--|--|
| Beperkt | Aangepaste waarschuwing: het aantal actieve verbindingen valt buiten het toegestane bereik | Klassiek Defender-IoT-micro-agent, Azure RTO'S | Het aantal actieve verbindingen binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. | De logboeken van het apparaat onderzoeken. Ontdek waar de verbinding vandaan komt en bepaal of het goed is of kwaad aardig is. Als dat schadelijk is, verwijdert u mogelijke malware en begrijpt u de bron. Voeg, indien goed aardig, de bron toe aan de lijst met toegestane verbindingen. |
| Beperkt | Aangepaste waarschuwing: de uitgaande verbinding die is gemaakt met een IP-adres dat niet is toegestaan | Klassiek Defender-IoT-micro-agent, Azure RTO'S | Er is een uitgaande verbinding gemaakt met een IP-adres buiten de lijst met toegestane IP-adressen. | De logboeken van het apparaat onderzoeken. Ontdek waar de verbinding vandaan komt en bepaal of het goed is of kwaad aardig is. Als dat schadelijk is, verwijdert u mogelijke malware en begrijpt u de bron. Voeg, indien goed aardig, de bron toe aan de lijst met toegestane IP-adressen. |
| Beperkt | Aangepaste waarschuwing: het aantal mislukte lokale aanmeldingen ligt buiten het toegestane bereik | Klassiek Defender-IoT-micro-agent, Azure RTO'S | Het aantal mislukte lokale aanmeldingen binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |  |
| Beperkt | Aangepaste waarschuwing: de aanmelding van een gebruiker die niet voor komt in de lijst met toegestane gebruikers | Klassiek Defender-IoT-micro-agent, Azure RTO'S | Een lokale gebruiker buiten de lijst met toegestane gebruikers die is aangemeld bij het apparaat. | Als u onbewerkte gegevens opslaat, navigeert u naar uw log Analytics-account en gebruikt u de gegevens om het apparaat te onderzoeken, identificeert u de bron en verhelpt u de lijst met toegestane/geblokkeerde websites voor deze instellingen. Als u momenteel geen onbewerkte gegevens opslaat, gaat u naar het apparaat en herstelt u de lijst met toegestane en geblokkeerde websites. |
| Beperkt | Aangepaste waarschuwing: er is een proces uitgevoerd dat niet is toegestaan | Klassiek Defender-IoT-micro-agent, Azure RTO'S | Een proces dat niet is toegestaan, is uitgevoerd op het apparaat. | Als u onbewerkte gegevens opslaat, navigeert u naar uw log Analytics-account en gebruikt u de gegevens om het apparaat te onderzoeken, identificeert u de bron en verhelpt u de lijst met toegestane/geblokkeerde websites voor deze instellingen. Als u momenteel geen onbewerkte gegevens opslaat, gaat u naar het apparaat en herstelt u de lijst met toegestane en geblokkeerde websites. |
|

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [aanpassen van een waarschuwing](quickstart-create-custom-alerts.md)
- [Overzicht](overview.md) van Defender voor IOT-service
- Meer informatie over [het verkrijgen van toegang tot uw beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)
