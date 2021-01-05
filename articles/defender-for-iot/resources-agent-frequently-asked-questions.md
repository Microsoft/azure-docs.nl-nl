---
title: Veelgestelde vragen over Azure Defender voor IoT-agent
description: Hier vindt u antwoorden op veelgestelde vragen over Azure Defender voor IoT-agent.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: f584f11603e41b63f3c96749ed6c720eb5ce07a8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835054"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Veelgestelde vragen over Azure Defender voor IoT-agent

In dit artikel vindt u een lijst met veelgestelde vragen en antwoorden over de Defender voor IoT-agent.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Moet ik een Inge sloten beveiligings agent installeren?

Het installeren van de agent op uw IoT-apparaten is niet verplicht om Defender in te scha kelen voor IoT. U kunt kiezen uit de volgende drie opties en zo verschillende niveaus van beveiligings bewaking en beheer mogelijkheden verkrijgen, afhankelijk van uw selectie:

- Passieve, niet-invasieve implementatie met NTA (Network Traffic Analysis) Sens oren om te controleren en diepe inzicht te bieden in IoT/OT-Risico's met een invloed op de prestaties van het netwerk en apparaten
- Installeer de beveiligings agent voor Defender voor IoT embedded met of zonder wijzigingen. Deze optie biedt het hoogste niveau van verbeterde beveiligings inzichten in het gedrag en de toegang tot het apparaat.

- Maak uw eigen agent en implementeer het Defender voor IoT-beveiligings bericht schema. Met deze optie wordt het gebruik van Defender voor IoT-analyse hulpprogramma's boven op de beveiligings agent van het apparaat ingeschakeld.

- Geen beveiligings agent geïnstalleerd op uw IoT-apparaten. Met deze optie wordt IoT Hub communicatie bewaking ingeschakeld, met beperkte beveiligings bewaking en beheer mogelijkheden.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Wat doet de agent voor de Defender voor IoT?

Defender voor IoT-agent biedt een bedreigings dekking op apparaatniveau voor apparaatconfiguratie, gedrag en toegang (door de configuratie te scannen), &-verbinding te verwerken. De Defender voor IoT-beveiligings agent scant geen bedrijfsgerelateerde gegevens of activiteiten.

De beveiligings agent voor Defender voor IoT is open source en beschikbaar op GitHub in 32 bits en 64-bits Windows-en Linux-versies: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Wat zijn de afhankelijkheden en vereisten van de agent?

Defender voor IoT ondersteunt een groot aantal verschillende platformen. Zie [ondersteunde platformen](how-to-deploy-agent.md) voor het controleren van de ondersteuning voor uw specifieke apparaten.

## <a name="which-data-is-collected-by-the-agent"></a>Welke gegevens worden door de agent verzameld?

Connectiviteit, toegang, firewall configuratie, lijst met processen & de basis lijn van het besturings systeem worden verzameld door de agent.

## <a name="how-much-data-will-the-agent-generate"></a>Hoeveel gegevens worden er door de agent gegenereerd?

Het genereren van agent gegevens wordt aangestuurd door apparaat, toepassing, connectiviteits type en configuratie van de klanten agent. Als gevolg van de hoge variabiliteit tussen apparaten en IoT-oplossingen, raden we u aan eerst de agent in een test omgeving of test instelling te implementeren om de specifieke configuratie die aan uw behoeften voldoet, te observeren, te leren en te bepalen, terwijl u de hoeveelheid gegenereerde gegevens meet. Na het starten van de service biedt de agent van de Defender voor IoT operationele aanbevelingen voor het optimaliseren van de door Voer van de agent om u te helpen bij het configureren en aanpassen van het proces.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Gebruiken de agent berichten een quotum van IoT Hub?

Ja. De door agent verzonden gegevens worden meegeteld in uw IoT Hub-quotum.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Wat nu? Ik heb een agent geïnstalleerd en geen activiteiten of logboeken weer geven...

1. Controleer of het [agent type past bij het aangewezen OS-platform van uw apparaat](how-to-deploy-agent.md)

1. Controleer of de [agent wordt uitgevoerd op het apparaat](how-to-agent-configuration.md).

1. Controleer of de [service is ingeschakeld](quickstart-onboard-iot-hub.md) voor de **beveiliging** van uw IOT hub.

1. Controleer of het apparaat is [geconfigureerd in IOT hub met de Defender voor IOT-module](quickstart-create-security-twin.md).

Als de activiteiten of Logboeken nog steeds niet beschikbaar zijn, neemt u contact op met uw Defender voor IoT-partner voor meer hulp.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Wat gebeurt er als de Internet verbinding niet meer werkt?

De Sens oren en agents blijven gegevens uitvoeren en opslaan zolang het apparaat actief is. Gegevens worden opgeslagen in de cache voor beveiligings berichten volgens de grootte configuratie. Wanneer het apparaat de connectiviteit verkrijgt, wordt het verzenden van beveiligings berichten hervat.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Kan de agent invloed hebben op de prestaties van het apparaat of andere geïnstalleerde software?

De agent verbruikt machine resources als elke andere toepassing/elk ander proces en mag geen normale activiteit van het apparaat verstoren. Resource verbruik op het apparaat waarop de agent wordt uitgevoerd, is gekoppeld aan de installatie en configuratie. We raden u aan uw agent configuratie te testen in een opgenomen omgeving, samen met interoperabiliteit met uw andere IoT-toepassingen en-functionaliteit, voordat u probeert te implementeren in een productie omgeving.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Ik maak onderhoud op het apparaat. Kan ik de agent uitschakelen?

De agent kan niet worden uitgeschakeld.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Is er een manier om te testen of de agent correct werkt?

Als de agent niet meer communiceert of geen beveiligings berichten verzendt, wordt een melding op het **apparaat wordt** gegenereerd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over hoe u aan de slag kunt gaan met Defender voor IoT:

- Lees het [overzicht](overview.md) van Defender voor IOT
- De [systeem vereisten](quickstart-system-prerequisites.md) controleren
- Meer informatie over hoe u aan de [slag gaat met Defender voor IOT](getting-started.md)
- Informatie [over Defender voor IOT-beveiligings waarschuwingen](concept-security-alerts.md)
