---
title: 'Quickstart: Aan de slag'
description: In deze quickstart leert u hoe u aan de slag gaat met inzicht in de basiswerkstroom voor Defender for IoT-implementatie.
ms.topic: quickstart
ms.date: 04/17/2021
ms.openlocfilehash: b1e7686e1d68d5a3f239320930d69f22c78e13cb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750442"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Quickstart: Aan de slag met Defender for IoT

Dit artikel bevat een overzicht van de stappen die u moet nemen om de Azure Defender for IoT. Voor het proces moet u het volgende doen:

- Registreer uw abonnement en sensoren in de Azure Defender for IoT portal.
- Installeer de sensor- en on-premises beheerconsolesoftware.
- Voer de initiële activering van de sensor en de beheerconsole uit.

## <a name="permission-requirements"></a>Machtigingsvereisten

Voor sommige installatiestappen zijn specifieke gebruikersmachtigingen vereist.

Beheerdersmachtigingen zijn vereist om de sensor en beheerconsole te activeren, SSL/TLS-certificaten te uploaden en nieuwe wachtwoorden te genereren.

In de volgende tabel worden machtigingen voor gebruikerstoegang voor Azure Defender for IoT portal beschreven:

| Machtiging | Beveiligingslezer | Beveiligingsbeheerder | Abonnementsbijdrager | Abonnementseigenaar |
|--|--|--|--|--|
| Details en toegang tot software, activeringsbestanden en bedreigingsinformatiepakketten weergeven  | ✓ | ✓ | ✓ | ✓ |
| Een sensor onboarden  |  |  ✓ | ✓ | ✓ |
| Prijzen bijwerken  |  |  ✓ | ✓ | ✓ |
| Wachtwoord herstellen  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>De infrastructuur van de oplossing identificeren

**Uw netwerkinstallatiebehoeften verduidelijken**

Onderzoek uw netwerkarchitectuur, bewaakte bandbreedte en andere netwerkdetails. Zie About [Azure Defender for IoT network setup (Over Azure Defender for IoT netwerkinstallatie) voor meer informatie.](how-to-set-up-your-network.md)

**Verduidelijken welke sensoren en beheerconsoleapparaten vereist zijn voor het afhandelen van de netwerkbelasting**

Azure Defender for IoT ondersteunt zowel fysieke als virtuele implementaties. Voor de fysieke implementaties kunt u verschillende gecertificeerde apparaten aanschaffen. Zie Vereiste apparaten identificeren [voor meer informatie.](how-to-identify-required-appliances.md)

U wordt aangeraden het geschatte aantal apparaten te berekenen dat wordt bewaakt. Later, wanneer u uw Azure-abonnement registreert bij de portal, wordt u gevraagd dit nummer in te voeren. Getallen kunnen worden toegevoegd met intervallen van 1000 seconden. Het aantal bewaakte apparaten wordt *vastgelegde apparaten genoemd.*

## <a name="register-with-azure-defender-for-iot"></a>Registreren bij Azure Defender for IoT

Registratie omvat:

- Onboarding van uw Azure-abonnementen voor Defender for IoT.
- Vastgelegde apparaten definiëren.
- Download een activeringsbestand voor de on-premises beheerconsole.

Aanmelden:

1. Ga naar Azure Defender for IoT portal.

1. Selecteer **Abonnement onboarden.**

1. Selecteer op **de** pagina Prijzen een abonnement of maak een nieuw abonnement en voeg het aantal vastgelegde apparaten toe.

1. Selecteer het **tabblad De on-premises beheerconsole downloaden** en sla het gedownloade activeringsbestand op. Dit bestand bevat de samengevoegde vastgelegde apparaten die u hebt gedefinieerd. Het bestand wordt geüpload naar de beheerconsole na de eerste aanmelding.

Zie Offboard a subscription (Een abonnement offboarden) voor meer informatie over het [offboarden van een abonnement.](how-to-manage-subscriptions.md#offboard-a-subscription)

## <a name="install-and-set-up-the-on-premises-management-console"></a>De on-premises beheerconsole installeren en instellen

Nadat u uw on-premises beheerconsoleapparaat hebt verkregen:

- Download het ISO-pakket via Azure Defender for IoT portal.
- Installeer de software.
- Activeer en voer de eerste installatie van de beheerconsole uit.

Installeren en instellen:

1. Selecteer **Aan de slag** in de Defender for IoT-portal.
1. Selecteer het **tabblad On-premises beheerconsole.**
1. Kies een versie en selecteer **Downloaden.**
1. Installeer de on-premises beheerconsolesoftware. Zie Defender [for IoT-installatie voor meer informatie.](how-to-install-software.md)
1. Activeer en stel de beheerconsole in. Zie Activate [and set up your on-premises management console](how-to-activate-and-set-up-your-on-premises-management-console.md)(Uw on-premises beheerconsole activeren en instellen) voor meer informatie.

## <a name="onboard-a-sensor"></a>Een sensor onboarden ##

Onboard een sensor door deze te registreren bij Azure Defender for IoT en een activeringsbestand voor de sensor te downloaden:

1. Definieer een sensornaam en koppel deze aan een abonnement.
1. Kies een verbindingsmodus voor de sensor:

   - **Verbonden sensoren in de** cloud: informatie die door sensoren wordt gedetecteerd, wordt weergegeven in de sensorconsole. Bovendien wordt waarschuwingsinformatie geleverd via een IoT-hub en kan deze worden gedeeld met andere Azure-services, zoals Azure Sentinel.  U kunt er ook voor kiezen om automatisch bedreigingsinformatiepakketten van de Azure Defender for IoT naar uw sensoren te pushen. Zie Onderzoek naar bedreigingsinformatie [en pakketten voor meer informatie.](how-to-work-with-threat-intelligence-packages.md)

   - **Lokaal beheerde sensoren:** informatie die door sensoren wordt gedetecteerd, wordt weergegeven in de sensorconsole. Als u werkt in een netwerk met ruimte in de lucht en een uniforme weergave wilt van alle informatie die door meerdere lokaal beheerde sensoren wordt gedetecteerd, werkt u met de on-premises beheerconsole.

1. Download een activeringsbestand voor de sensor.

Zie Sensoren onboarden en beheren in de [Defender for IoT-portal](how-to-manage-sensors-on-the-cloud.md)voor meer informatie over onboarding.

## <a name="install-and-set-up-the-sensor"></a>De sensor installeren en instellen

Download het ISO-pakket via Azure Defender for IoT portal, installeer de software en stel de sensor in.

1. Selecteer **Aan de slag** in de Defender for IoT-portal.

1. Selecteer **Sensor instellen.**

1. Kies een versie en selecteer **Downloaden.**

1. Installeer de sensorsoftware. Zie Defender [for IoT-installatie voor meer informatie.](how-to-install-software.md)

1. Activeer en stel de sensor in. Zie Aanmelden en een sensor activeren [voor meer informatie.](how-to-activate-and-set-up-your-sensor.md)

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Sensoren verbinden met een on-premises beheerconsole

Sluit sensoren aan op de beheerconsole om ervoor te zorgen dat:

- Sensoren verzenden informatie over waarschuwingen en apparaatinventarisatie naar de on-premises beheerconsole.

- De on-premises beheerconsole kan sensorback-ups uitvoeren, waarschuwingen beheren die door sensoren worden gedetecteerd, sensorverbrekingen onderzoeken en andere activiteiten uitvoeren op verbonden sensoren.

U wordt aangeraden meerdere sensoren te groeperen die dezelfde netwerken in één zone bewaken. Als u dit doet, worden gegevens verzameld door meerdere sensoren.

Zie Connect [sensors to the on-premises management console (Sensoren verbinden met de on-premises beheerconsole) voor meer informatie.](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Gegevens in Azure Sentinel met waarschuwingsgegevens (optioneel)

Verzend waarschuwingsgegevens naar Azure Sentinel door het configureren van Azure Sentinel. Zie [Connect your data from Defender for IoT to Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="next-steps"></a>Volgende stappen ##

[Welkom bij Azure Defender for IoT](overview.md)

[Azure Defender for IoT architectuur](architecture.md)
