---
title: Aan de slag
description: Aan de slag met uitleg over de basis werk stroom voor de implementatie van Defender voor IoT.
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
ms.date: 2/18/2021
ms.author: shhazam
ms.openlocfilehash: c224dc3f919471e769717f308aff9ae58630d421
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712756"
---
# <a name="get-started-with-defender-for-iot"></a>Aan de slag met Defender voor IoT

Dit artikel bevat een overzicht van de stappen die u moet nemen om Azure Defender voor IoT in te stellen. Voor het proces moet u het volgende doen:

- Registreer uw abonnement en Sens oren in de Azure Defender voor IoT-Portal.
- Installeer de sensor-en on-premises beheer console-software.
- Voer de eerste activering van de sensor en de beheer console uit.

## <a name="permission-requirements"></a>Machtigings vereisten

Voor sommige installatie stappen zijn specifieke gebruikers machtigingen vereist.

Gebruikers machtigingen voor beheerders zijn vereist voor het activeren van de sensor-en beheer console, het uploaden van SSL/TLS-certificaten en het genereren van nieuwe wacht woorden.

In de volgende tabel worden de machtigingen voor gebruikers toegang voor Azure Defender voor IoT-Portal tools beschreven:

| Machtiging | Beveiligingslezer | Beveiligingsbeheerder | Mede werker van abonnement | Abonnements eigenaar |
|--|--|--|--|--|
| Alle Defender voor IoT-schermen en-gegevens weer geven | ✓ | ✓ | ✓ | ✓ |
| Een sensor onboarden  |  |  ✓ | ✓ | ✓ |
| Update prijzen  |  |  ✓ | ✓ | ✓ |
| Wacht woord herstellen  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. de oplossings infrastructuur identificeren

**Verhelder uw netwerk installatie vereisten**

Onderzoek uw netwerk architectuur, bewaakte band breedte en andere netwerk gegevens. Zie [about Azure Defender voor IOT Network Setup](how-to-set-up-your-network.md)(Engelstalig) voor meer informatie.

**Verduidelijk welke Sens oren en beheer console apparaten vereist zijn voor het verwerken van de netwerk belasting**

Azure Defender voor IoT ondersteunt zowel fysieke als virtuele implementaties. Voor de fysieke implementaties kunt u verschillende gecertificeerde apparaten kopen. Zie voor meer informatie [vereiste apparaten identificeren](how-to-identify-required-appliances.md).

U wordt aangeraden het geschatte aantal apparaten te berekenen dat wordt bewaakt. Later, wanneer u uw Azure-abonnement registreert bij de portal, wordt u gevraagd dit nummer in te voeren. Getallen kunnen worden toegevoegd met intervallen van 1.000 seconden. De aantallen bewaakte apparaten worden *toegewezen apparaten* genoemd.

## <a name="2-register-with-azure-defender-for-iot"></a>2. Registreer u bij Azure Defender voor IoT

Registratie omvat:

- Het voorbereiden van uw Azure-abonnementen op Defender voor IoT.
- Vastgelegde apparaten definiëren.
- Een activerings bestand downloaden voor de on-premises beheer console.

Aanmelden:

1. Ga naar de portal van Azure Defender voor IoT.
1. Selecteer **abonnement voor onboarding**.
1. Selecteer op de pagina met **prijzen** een abonnement of maak een nieuwe, en voeg het aantal toegewezen apparaten toe.
1. Selecteer het tabblad **de on-premises beheer console downloaden** en sla het gedownloade activerings bestand op. Dit bestand bevat de geaggregeerde vastgelegde apparaten die u hebt gedefinieerd. Het bestand wordt na de eerste aanmelding geüpload naar de beheer console.

Zie [niet meer vrijgeven a subscription](how-to-manage-sensors-on-the-cloud.md#offboard-a-subscription)(Engelstalig) voor meer informatie over het niet meer vrijgeven van een abonnement.

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. de on-premises beheer console installeren en instellen

Na het verkrijgen van uw on-premises beheer console-apparaat:

- Down load het ISO-pakket vanuit de Azure Defender voor IoT-Portal.
- De software installeren.
- De installatie van de oorspronkelijke beheer console activeren en uitvoeren.

Installeren en instellen van:

1. Selecteer **aan de slag** vanuit de Defender voor IOT-Portal.
1. Selecteer het tabblad **on-premises beheer console** .
1. Kies een versie en selecteer **downloaden**.
1. De on-premises beheer console-software installeren. Zie voor meer informatie [Defender voor IOT-installatie](how-to-install-software.md).
1. De beheer console activeren en instellen. Zie [uw on-premises beheer console activeren en instellen](how-to-activate-and-set-up-your-on-premises-management-console.md)voor meer informatie.

## <a name="4-onboard-a-sensor"></a>4. een sensor onboarden

Een sensor onboarden door deze te registreren bij Azure Defender voor IoT en een sensor activerings bestand te downloaden:

1. Definieer de naam van een sensor en koppel deze aan een abonnement.
1. Kies een sensor beheer modus:

   - **Met de Cloud verbonden Sens oren**: informatie die Sens oren detecteert wordt weer gegeven in de sensor console. Daarnaast worden waarschuwings gegevens geleverd via een IoT-hub en kunnen ze worden gedeeld met andere Azure-Services, zoals Azure Sentinel.

   - **Lokaal beheerde Sens oren**: informatie die Sens oren detecteert wordt weer gegeven in de sensor console. Als u werkt met een Air-gapped-netwerk en u een uniforme weer gave wilt van alle informatie die wordt gedetecteerd door meerdere lokaal beheerde Sens oren, werkt u met de on-premises beheer console. 

1. Down load een sensor activerings bestand.

Zie voor meer informatie [Sens oren voor onboarding en beheren in de Defender voor IOT-Portal](how-to-manage-sensors-on-the-cloud.md).

## <a name="5-install-and-set-up-the-sensor"></a>5. de sensor installeren en instellen

Down load het ISO-pakket vanuit de Azure Defender voor IoT-Portal, installeer de software en stel de sensor in.

1. Selecteer **aan de slag** vanuit de Defender voor IOT-Portal.
1. Selecteer **sensor instellen**.
1. Kies een versie en selecteer **downloaden**.
1. Installeer de sensor software. Zie voor meer informatie [Defender voor IOT-installatie](how-to-install-software.md).
1. Uw sensor activeren en instellen. Zie [Aanmelden en een sensor activeren](how-to-activate-and-set-up-your-sensor.md)voor meer informatie.

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. Sens oren verbinden met een on-premises beheer console

Verbind Sens oren met de beheer console om ervoor te zorgen dat:

- Sens oren sturen waarschuwings-en apparaat-inventaris gegevens naar de on-premises beheer console.

- De on-premises beheer console kan sensor back-ups uitvoeren, waarschuwingen beheren die Sens oren detecteren, het verbreken van de sensor onderzoeken en andere activiteiten uitvoeren op verbonden Sens oren.

Het is raadzaam om meerdere Sens oren te groeperen die dezelfde netwerken in één zone bewaken. Dit leidt tot Coalesce-gegevens die worden verzameld door meerdere Sens oren.

Zie [Sens oren verbinden met de on-premises beheer console](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)voor meer informatie.

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. Azure Sentinel vullen met waarschuwings gegevens (optioneel)

Waarschuwings gegevens verzenden naar Azure Sentinel door Azure Sentinel te configureren. Zie [uw gegevens verbinden vanuit Defender voor IOT naar Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="see-also"></a>Zie ook

- [Welkom bij Azure Defender voor IoT](overview.md)

- [Azure Defender voor IoT-architectuur](architecture.md)
