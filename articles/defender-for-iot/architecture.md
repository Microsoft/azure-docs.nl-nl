---
title: Architectuur van oplossing zonder agent
description: Meer informatie over Azure Defender voor IoT-architectuur zonder agents en informatie stroom.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 1478baa889faf84a53d373863961abc92c1fa34a
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449183"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender voor IoT-architectuur

In dit artikel wordt de functionele systeem architectuur van de Defender voor IoT-oplossing zonder agent beschreven. Azure Defender voor IoT biedt twee mogelijkheden voor het aanpassen van de behoeften van uw omgeving, de oplossing zonder agent voor organisaties en oplossingen op basis van agents voor apparaten.

## <a name="agentless-solution-for-organizations"></a>Oplossing zonder agent voor organisaties
### <a name="defender-for-iot-components"></a>Defender voor IoT-onderdelen

Defender voor IoT verbindt zowel de Azure-Cloud als on-premises onderdelen. De oplossing is ontworpen voor schaal baarheid in grote en geografisch gedistribueerde omgevingen met meerdere externe locaties. Deze oplossing maakt een gedistribueerde architectuur met meerdere lagen mogelijk per land, regio, bedrijfs eenheid of zone. 

Azure Defender voor IoT bevat de volgende onderdelen: 

**Met Cloud verbonden implementaties**

- Azure Defender voor IoT-VM of-apparaat
- Azure Portal voor Cloud beheer en integratie naar Azure Sentinel
- On-premises beheer console voor beheer van lokale sites
- Een Inge sloten beveiligings agent (optioneel)

**Implementaties van Air-gapped (offline)**

- Azure Defender voor IoT-VM of-apparaat
- On-premises beheer console voor lokaal site beheer

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="De architectuur voor Defender voor IoT.":::

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender voor IoT Sens oren

De Defender voor IoT Sens oren detecteren en doorlopend bewaken netwerk apparaten. Sens oren verzamelen ICS-netwerk verkeer met behulp van passieve bewaking (zonder agent) op IoT-en OT-apparaten. 
 
De technologie zonder agent is ontworpen voor IoT-en OT-netwerken en levert diep gaande inzicht in IoT en het risico binnen enkele minuten nadat verbinding is gemaakt met het netwerk. Het heeft geen invloed op de prestaties van het netwerk en de netwerk apparaten vanwege de methode van de niet-invasieve analyse van netwerk verkeer (NTA). 
 
Het Toep assen van gepatenteerde, IoT-en niet-bewuste gedrags analyse en Layer-7 diepe pakket inspectie (DPI), stelt u in staat om te analyseren naast traditionele oplossingen op basis van hand tekeningen om direct geavanceerde IoT-en OT bedreigingen te detecteren (zoals malware zonder bestanden) op basis van afwijkende of niet-geautoriseerde activiteiten. 
  
Defender voor IoT Sens oren maakt verbinding met een SPANNe-poort of netwerk-Tik en begint direct met het uitvoeren van DPI op IoT en het netwerk verkeer. 
 
Het verzamelen, verwerken, analyseren en Signa lering van gegevens vindt direct plaats op de sensor. Dit proces maakt het ideaal voor locaties met een lage band breedte of een connectiviteit met een hoge latentie, omdat alleen meta gegevens worden overgedragen naar de beheer console.

De sensor bevat vijf analyse detectie-engines. De engines activeren waarschuwingen op basis van de analyse van zowel realtime als vooraf opgenomen verkeer. De volgende engines zijn beschikbaar: 

#### <a name="protocol-violation-detection-engine"></a>Detectie-engine voor protocol overtreding
De detectie-engine voor protocol overtredingen bepaalt het gebruik van pakket structuren en veld waarden die in strijd zijn met de specificaties van het ICS-protocol, bijvoorbeeld: Modbus uitzonde ring en initiëren van een verouderde functie code waarschuwingen.

#### <a name="policy-violation-detection-engine"></a>Detectie-engine voor beleids schendingen
Met behulp van machine learning wordt de detectie-engine voor beleids schendingen gebruikers gewaarschuwd van elke afwijking van de basislijn werking, zoals onbevoegd gebruik van specifieke functie codes, toegang tot specifieke objecten of wijzigingen in de apparaatconfiguratie. Bijvoorbeeld: DeltaV software versie gewijzigd en ongeoorloofde PLC-programmeer waarschuwingen. De beleids overtredings engine modelleert met name de ICS-netwerken als deterministische reeksen van statussen en overgangen, met behulp van een gepatenteerde techniek met de naam IFSM (Industrial eindig State Modeler). De detectie-engine voor beleids overtredingen brengt een basis lijn van de ICS-netwerken tot stand, zodat het platform een kortere leer periode nodig heeft om een basis lijn van het netwerk te bouwen dan voor de generieke mathematische benaderingen of analyses, die oorspronkelijk zijn ontwikkeld voor IT in plaats van netwerken.

#### <a name="industrial-malware-detection-engine"></a>Detectie-engine voor industriële malware
De industriële malware-detectie-engine identificeert gedrag dat aangeeft dat er sprake is van bekende schadelijke software, zoals Conficker, Black Energy, WannaCry, NotPetya en Triton. 

#### <a name="anomaly-detection-engine"></a>Anomalie detectie-engine
De anomalie detectie-engine detecteert ongebruikelijke M2M-communicatie (machine-to-machine) en gedrag. Door het model leren van ICS-netwerken als deterministische reeksen van statussen en overgangen, moet het platform een kortere leer periode hebben dan de generieke mathematische benaderingen of analyses die oorspronkelijk voor IT zijn ontwikkeld in plaats van in het geval Er worden ook afwijkingen sneller gedetecteerd, met minimale valse positieven. Waarschuwingen voor de anomalie detectie-engine omvatten buitensporige SMB-aanmeldingen in pogingen en de PLC-scan heeft waarschuwingen gedetecteerd.

#### <a name="operational-incident-detection"></a>Detectie van operationele incidenten
De detectie van operationele incidenten detecteert operationele problemen zoals een onregelmatige verbinding die kan wijzen op vroegtijdige tekenen van een storing in de apparatuur. Zo wordt bijvoorbeeld aangenomen dat het apparaat wordt losgekoppeld (niet meer reageert) en Siemens S7 stop PLC-opdracht is verzonden.

### <a name="management-consoles"></a>Beheer consoles
Het beheren van Azure Defender voor IoT in hybride omgevingen geschiedt via twee beheer portals: 
- Sensor console
- De on-premises beheer console
- Azure Portal

### <a name="sensor-console"></a>Sensor console
Sensor detecties worden weer gegeven in de sensor console, waar ze kunnen worden weer gegeven, onderzocht en geanalyseerd in een netwerk kaart, een inventarisatie van apparaten en in een uitgebreide reeks rapporten, bijvoorbeeld rapporten voor risico analyse, gegevens analyse query's en aanvals vectoren. U kunt ook de-console gebruiken om bedreigingen weer te geven en te verwerken die zijn gedetecteerd door sensor motoren, gegevens door te sturen naar partner systemen, gebruikers te beheren en meer.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender voor IoT-sensor console":::

### <a name="on-premises-management-console"></a>On-premises beheer console
De on-premises beheer console maakt SOC-Opera tors (Security Operations Center) mogelijk om waarschuwingen te beheren en analyseren die zijn geaggregeerd van meerdere Sens oren in één dash board en biedt een algemeen overzicht van de status van de netwerken.

Deze architectuur biedt een uitgebreide, uniforme weer gave van het netwerk op het niveau van SOC, geoptimaliseerde waarschuwingen en het beheer van operationele netwerk beveiliging, om ervoor te zorgen dat het besluit vorming en risico beheer probleemloos blijft.

Naast de afstands bediening voor multitenancy, bewaking, gegevens analyse en gecentraliseerde sensors biedt de beheer console extra hulpprogram ma's voor systeem onderhoud (zoals waarschuwings uitsluiting) en volledig aangepaste rapportage functies voor elk van de externe apparaten. Deze architectuur ondersteunt zowel lokaal beheer op site niveau, zone niveau als globaal beheer binnen het SOC.

De-beheer console kan worden geïmplementeerd voor een configuratie met een hoge Beschik baarheid, die een back-upconsole biedt waarmee periodiek back-ups worden ontvangen van alle configuratie bestanden die nodig zijn voor herstel. Als er een storing optreedt in de primaire console, worden de lokale site beheer apparaten automatisch gefailovert om te synchroniseren met de back-upconsole om zonder onderbreking de beschik baarheid te behouden.

Nauw geïntegreerd met uw SOC-werk stromen en rapporten uitvoeren, maakt het eenvoudig om de prioriteit van oplossings activiteiten en de correlatie van bedreigingen op meerdere locaties te verbeteren.

- Holistische-Verminder complexiteit met één uniform platform voor Apparaatbeheer, Risico's en beveiligings problemen en bewaking van bedreigingen met incident reacties.

- Aggregatie en correlatie: gegevens en waarschuwingen die zijn verzameld van alle sites weer geven, verzamelen en analyseren.

- Alle Sens oren beheren: Configureer en bewaak alle Sens oren vanaf één locatie.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Beheer al uw waarschuwingen en informatie.":::

### <a name="azure-portal"></a>Azure Portal

De Defender voor IoT-Portal in azure wordt gebruikt om u te helpen:

- Oplossings apparaten kopen

- Software installeren en bijwerken

- Trein opstaande Sens oren

- Update van Threat Intelligence-pakketten

## <a name="see-also"></a>Zie ook

[Veelgestelde vragen over Defender voor IoT](resources-frequently-asked-questions.md)

[Systeemvereisten](quickstart-system-prerequisites.md)
