---
title: Veelgestelde vragen over Defender voor IoT
description: Vind antwoorden op veelgestelde vragen over Azure Defender voor IoT-functies en-services.
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
ms.openlocfilehash: 678b50ffb2df2cd16312548109b93f6f1a7f08ff
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247462"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Veelgestelde vragen over Azure Defender voor IoT

In dit artikel vindt u een lijst met veelgestelde vragen en antwoorden over Defender voor IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Wat is de unieke toegevoegde waarde van Azure voor IoT-beveiliging?

Met Defender voor IoT kunnen bedrijven hun bestaande Cyber-beveiligings weergave uitbreiden naar hun volledige IoT-oplossing. Azure biedt een end-to-end weer gave van uw bedrijfs oplossing, zodat u zakelijke acties en beslissingen kunt nemen op basis van uw bedrijfs beveiligings postuur en verzamelde gegevens. Bij gecombineerde beveiliging met Azure IoT, Azure IoT Edge en Azure Security Center kunt u de gewenste oplossing maken met de beveiliging die u nodig hebt.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Onze organisatie maakt gebruik van eigen niet-standaard industriële protocollen. Worden deze scripts ondersteund? 

Azure Defender voor IoT biedt uitgebreide ondersteuning voor het protocol. Naast ondersteuning voor Inge sloten protocol kunt u IoT-en OT-apparaten beveiligen die gebruikmaken van eigen en aangepaste protocollen, of protocollen die afwijken van de standaard. Ontwikkel aars kunnen met behulp van de node-SDK (Open Development Environment) Dissector-invoeg toepassingen maken waarmee netwerk verkeer wordt gedecodeerd op basis van gedefinieerde protocollen. Verkeer wordt door services geanalyseerd om volledige bewaking, waarschuwingen en rapportage mogelijk te maken. Gebruik de Horizony tot:
- Breid zicht baarheid en beheer uit zonder dat u de nieuwe versies hoeft te upgraden.
- Beveilig eigendoms gegevens door on-site te ontwikkelen als externe invoeg toepassing. 
- Lokalisatie van tekst voor waarschuwingen, gebeurtenissen en protocol parameters.

Deze unieke oplossing voor het ontwikkelen van protocollen als invoeg toepassingen vereist geen gespecialiseerde teams of versie releases van ontwikkel aars om een nieuw protocol te kunnen ondersteunen. Ontwikkel aars, partners en klanten kunnen veilig protocollen ontwikkelen en inzichten en kennis delen met behulp van Horizon. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Moet ik hardware-apparaten aanschaffen bij micro soft-partners?
Azure Defender voor IoT-sensor wordt uitgevoerd op specifieke hardwarespecificaties zoals beschreven in de [hand leiding voor hardwarespecificaties](./how-to-identify-required-appliances.md), klanten kunnen gecertificeerde hardware aanschaffen bij micro soft-partners of gebruikmaken van de meegeleverde stuk lijst en deze zelf aanschaffen. 

Gecertificeerde hardware is getest in onze Labs voor stabiliteit van Stuur Programma's, pakket drupt en netwerk formaat.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>Met deze verordening kunnen we ons systeem niet verbinden met internet. Kunnen we Defender voor IoT nog gebruiken?

Ja, dat kan! De on-premises oplossing van het Azure Defender voor IoT-platform wordt geïmplementeerd als een fysiek of virtueel sensorapparaat dat bewaakt passief netwerk verkeer (via SPAN, RSPAN of TAP) opneemt om IT-, OT-en IoT-netwerken te analyseren, detecteren en continu te controleren. Voor grotere ondernemingen kunnen meerdere Sens oren hun gegevens samen voegen in een on-premises beheer console.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Waar in het netwerk moet ik controle poorten koppelen?

De Azure Defender voor IoT-sensor maakt verbinding met een SPANNe-poort of-netwerk en begint onmiddellijk met het verzamelen van ICS-netwerk verkeer via passieve bewaking (zonder agent). Dit heeft geen invloed op de netwerken, omdat deze niet in het gegevenspad is geplaatst en niet actief is.

Bijvoorbeeld:
- Eén apparaat (virtueel fysiek) kan zich in de DMZ-laag van de werk vloer bevinden, zodat alle werk vloer-cellen verkeer naar deze laag worden doorgestuurd.
- U kunt ook kleine mini Sens oren vinden in elke werk vloer-cel met ofwel Cloud-of lokaal beheer dat zich in de DMZ-laag van de Shop Floor bevindt. Een ander apparaat (virtueel of fysiek) kan het verkeer bewaken in de DMZ-laag (voor SCADA, historian of MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Hoe kan Defender voor IoT worden vergeleken met de competitie?

Hoewel andere oplossingen een reeks mogelijkheden bieden waarmee klanten hun eigen oplossingen kunnen maken, biedt Defender voor IoT een unieke end-to-end IoT-beveiligings oplossing die een brede weer gave biedt over de beveiliging van al uw verwante Azure-resources. Azure maakt snelle implementatie en volledige integratie mogelijk met IoT Hub module apparaatdubbels voor eenvoudige integratie met bestaande hulpprogram ma's voor Apparaatbeheer.


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Moet ik een Azure IoT-klant zijn?

Ja. Voor implementaties in de Cloud is Azure Defender voor IoT afhankelijk van Azure IoT-connectiviteit en-infra structuur.
## <a name="can-i-create-my-own-alerts"></a>Kan ik mijn eigen waarschuwingen maken?

Ja. U kunt een aangepaste waarschuwing instellen voor een vooraf vastgestelde set gedrag, zoals IP-adres en open poorten. Zie [aangepaste waarschuwingen maken](quickstart-create-custom-alerts.md) voor meer informatie over aangepaste waarschuwingen en hoe u deze kunt maken.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Waar kan ik logboeken zien? Kan ik logboeken aanpassen?

- Waarschuwingen en aanbevelingen weer geven met behulp van uw verbonden Log Analytics-werk ruimte. Opslag grootte en duur in de werk ruimte configureren.

- Onbewerkte gegevens van uw beveiligings agent kunnen ook worden opgeslagen in uw Log Analytics-account. Overweeg grootte, duur, opslag vereisten en gekoppelde kosten voordat u de configuratie van deze optie wijzigt.



## <a name="what-happens-when-the-internet-connection-stops-working"></a>Wat gebeurt er als de Internet verbinding niet meer werkt?

De Sens oren en agents blijven gegevens uitvoeren en opslaan zolang het apparaat actief is. Gegevens worden opgeslagen in de cache voor beveiligings berichten volgens de grootte configuratie. Wanneer het apparaat de connectiviteit verkrijgt, wordt het verzenden van beveiligings berichten hervat.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over hoe u aan de slag kunt gaan met Defender voor IoT:

- Lees het [overzicht](overview.md) van Defender voor IOT
- De [systeem vereisten](quickstart-system-prerequisites.md) controleren
- Meer informatie over hoe u aan de [slag gaat met Defender voor IOT](getting-started.md)
- Informatie [over Defender voor IOT-beveiligings waarschuwingen](concept-security-alerts.md)