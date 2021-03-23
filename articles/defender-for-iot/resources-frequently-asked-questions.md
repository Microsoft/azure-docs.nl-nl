---
title: Veelgestelde vragen over Defender voor IoT
description: Vind antwoorden op veelgestelde vragen over Azure Defender voor IoT-functies en-services.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778590"
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

Azure Defender voor IoT biedt uitgebreide beveiliging in al uw IoT/OT-apparaten. Voor **organisaties van eind gebruikers** biedt Azure Defender voor IOT zonder agent, netwerk-laag beveiliging die snel wordt geïmplementeerd, werkt met diverse bedrijfs eigen en verouderde Windows-systemen en samenwerkt met Azure Sentinel en andere Soc-hulpprogram ma's. Het kan on-premises of in met Azure verbonden omgevingen worden geïmplementeerd. Voor **IOT-apparaats bouwers** biedt Azure Defender voor IOT licht gewicht agenten om beveiliging op apparaatniveau in te sluiten in nieuwe IOT/OT-initiatieven.

## <a name="do-i-have-to-be-an-azure-customer"></a>Moet ik een Azure-klant zijn?

Nee, u hoeft geen Azure-klant te zijn voor de agentloze versie van Azure Defender voor IoT. Als u echter waarschuwingen wilt verzenden naar Azure Sentinel; richt netwerk Sens oren in en bewaak hun status vanuit de Cloud; en profiteer van automatische updates van software-en bedreigings informatie, moet u de sensor verbinden met Azure via Azure IoT Hub.

Voor de op de agent gebaseerde versie van Azure Defender voor IoT moet u een Azure-klant zijn.

## <a name="can-i-create-my-own-alerts"></a>Kan ik mijn eigen waarschuwingen maken?

Ja, u kunt aangepaste waarschuwingen maken op basis van meerdere para meters, zoals IP/MAC-adres, protocol type, klasse, service, functie, opdracht, enzovoort, en waarden van aangepaste labels in de payloads.  Zie [aangepaste waarschuwingen maken](quickstart-create-custom-alerts.md) voor meer informatie over aangepaste waarschuwingen en hoe u deze kunt maken.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Wat gebeurt er als de Internet verbinding niet meer werkt?

De Sens oren en agents blijven gegevens uitvoeren en opslaan zolang het apparaat actief is. Gegevens worden opgeslagen in de cache voor beveiligings berichten volgens de grootte configuratie. Wanneer het apparaat de connectiviteit verkrijgt, wordt het verzenden van beveiligings berichten hervat.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over hoe u aan de slag kunt gaan met Defender voor IoT:

- Lees het [overzicht](overview.md) van Defender voor IOT
- De [systeem vereisten](quickstart-system-prerequisites.md) controleren
- Meer informatie over hoe u aan de [slag gaat met Defender voor IOT](getting-started.md)
- Informatie [over Defender voor IOT-beveiligings waarschuwingen](concept-security-alerts.md)