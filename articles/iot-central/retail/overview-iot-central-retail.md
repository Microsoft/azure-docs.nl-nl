---
title: Wat zijn de Azure IoT Central Retail oplossingen | Microsoft Docs
description: Krijg meer informatie over het gebruik van Azure IoT Central-toepassingssjablonen om oplossingen te bouwen voor verbonden logistiek, digitale distributiecentra, in-storeanalyse, voorwaardebewaking, betalingen, slim voorraadbeheer en de detailhandel.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 56198cf661001bcfcec45d483d0f691234851894
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832280"
---
# <a name="what-are-the-iot-central-retail-solutions"></a>Wat zijn de IoT Central handels oplossingen?

Azure IoT Central is een platform voor IoT-apps dat de inzet en kosten verlaagt die gepaard gaan met het ontwikkelen, beheren en onderhouden van geavanceerde IoT-oplossingen. Als u ervoor kiest om toepassingen met Azure IoT Central te bouwen, hebt u de mogelijkheid om tijd, geld en energie te besteden aan de transformatie van uw bedrijf met IoT-gegevens, in plaats van alleen maar een complexe en voortdurend veranderende IoT-infrastructuur te onderhouden en bij te werken.

In dit artikel worden specifieke IoT Central-toepassingssjablonen voor de detailhandel beschreven. Als ontwikkelaar van oplossingen kunt u deze sjablonen gebruiken om IoT-oplossingen te bouwen waarmee de toeleveringsketens worden geoptimaliseerd, in-store ervaringen voor klanten worden verbeterd en de voorraad efficiënter kan worden bijgehouden.

:::image type="content" source="media/overview-iot-central-retail/retail-app-templates.png" alt-text="Overzicht van Azure IoT Retail":::

In de volgende secties worden de mogelijkheden van deze toepassingssjablonen beschreven:

## <a name="connected-logistics"></a>Verbonden logistiek

Naar verwachting lopen de wereldwijde uitgaven voor logistiek in 2020 op tot USD 10,6 triljard. Het merendeel van deze uitgaven komt voor rekening van het vervoer van goederen; expediteurs kennen een immense concurrentie en talloze beperkingen.

U kunt IoT-sensoren gebruiken om omgevingsomstandigheden te verzamelen en te bewaken, zoals temperatuur, vochtigheid, kantelhoek, trillingen, licht en de locatie van een zending. Ook kunt u de telemetrie die door IoT-sensoren en apparaten met andere gegevensbronnen, zoals weer- en verkeersinformatie in cloudgebaseerde bedrijfsinformatiesystemen, is verzameld, combineren.

Oplossingen voor verbonden logistiek kennen onder andere de volgende voordelen:

* Bewaking van zendingen met realtime tracering en tracking. 
* Integriteit van de zending met realtime bewaking van de omgevingsomstandigheden.
* Beveiliging tegen diefstal, verlies en beschadiging van de zendingen.
* Geo-fencing, optimalisatie van routes, vlootbeheer en voertuiganalyses.
* Prognoses voor voorspelbare verzending en aankomst van zendingen.

In de volgende schermopnamen ziet u het kant-en-klare dashboard in de toepassingssjabloon. Het dashboard kan volledig worden aangepast aan uw specifieke vereisten voor de oplossing:

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard1.png" alt-text="Schermopname van de bovenste helft van het dashboard voor verbonden logistieke bewerkingen.":::

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard2.png" alt-text="Schermopname van de onderste helft van het dashboard voor verbonden logistieke bewerkingen.":::

Zie de zelfstudie [Een toepassingssjabloon voor verbonden logistiek implementeren en doorlopen](./tutorial-iot-central-connected-logistics.md) voor meer informatie.

## <a name="digital-distribution-center"></a>Digitaal distributiecentrum

Wanneer fabrikanten en detailhandelaren wereldwijd meer voet aan de grond krijgen, vertakken hun toeleveringsketens, die daardoor steeds complexer worden. Klanten verwachten tegenwoordig dat er een grote keuze aan producten beschikbaar is, en dat die goederen bovendien al binnen één of twee dagen na aankoop worden geleverd. Distributiecentra moeten zich aan deze trends aanpassen en tegelijkertijd bestaande inefficiënties zien te overwinnen.

Vandaag de dag houdt afhankelijkheid van handmatig werk in dat 55 à 65% van de kosten van distributiecentra voor rekening komt van de selectie en verpakking van producten. Handmatig selecteren en verpakken is doorgaans ook langzamer dan een geautomatiseerd systeem, en door snel wisselend personeel is het ook moeilijker om aan de vereiste verzendvolumes te voldoen. Deze seizoensgebonden schommelingen leiden tot een hoog personeelsverloop en een grotere kans op dure fouten.

Oplossingen op basis van IoT-camera's bieden voordelen waarmee het bedrijf kan worden getransformeerd, door middel van een digitale feedbacklus. Gegevens vanuit het hele distributiecentrum genereren nuttige inzichten die op hun beurt weer tot betere gegevens leiden.

Een digitaal distributiecentrum heeft onder meer de volgende voordelen:

* Camera's bewaken de goederen die aankomen en via het lopendebandsysteem worden verplaatst.
* Automatische identificatie van defecte goederen.
* Efficiënte tracering van bestellingen.
* Lagere kosten, betere productiviteit en geoptimaliseerd gebruik.

In de volgende schermopname ziet u het kant-en-klare dashboard in de toepassingssjabloon. Het dashboard kan volledig worden aangepast aan uw specifieke vereisten voor de oplossing: 

:::image type="content" source="media/overview-iot-central-retail/digital-distribution-center-dashboard.png" alt-text="Dashboard van een digitaal distributiecentrum":::

Zie de zelfstudie [Een toepassingssjabloon voor een digitaal distributiecentrum implementeren en doorlopen](./tutorial-iot-central-digital-distribution-center.md) voor meer informatie.

## <a name="in-store-analytics---condition-monitoring"></a>In-Store Analytics: bewaking van condities

Voor veel bedrijven in de detailhandel vormen omgevingsfactoren in hun winkels een belangrijke onderscheidingsfactor ten opzichte van hun concurrenten. Detailhandelaren willen een aangenaam klimaat in hun winkels handhaven, ten bate van de klant.  

Als ontwikkelaar van oplossingen kunt u het toepassingssjabloon voor bewaking van de omstandigheden in de winkel van IoT Central gebruiken om een complete oplossing te bouwen. Met de toepassingssjabloon kunt u digitaal verbinding maken met de omgeving in een winkel en de omstandigheden bewaken met behulp van verschillende sensorapparaten. Deze sensoren genereren telemetrische gegevens die u kunt omzetten in zakelijke inzichten, waarmee detailhandelaren bedrijfskosten kunnen verlagen en hun klanten een geweldige ervaring kunnen bieden.

Gebruik het toepassingssjabloon voor het volgende:

* Verschillende soorten IoT-sensoren verbinden met een IoT Central-toepassingsinstantie.
* De status van het sensornetwerk en alle gatewayapparaten in de omgeving bewaken en beheren.
* Aangepaste regels rondom de omgevingscondities in een winkel maken om waarschuwingen voor winkelmanagers te activeren.
* De omgevingscondities in uw winkel omzetten in inzichten die door het winkelteam kunnen worden gebruikt om de klantervaring te verbeteren.
* De geaggregeerde inzichten exporteren naar bestaande of nieuwe bedrijfstoepassingen om winkelpersoneel tijdig van nuttige informatie te voorzien.

De toepassingssjabloon wordt geleverd met een aantal apparaatsjablonen en er wordt een set gesimuleerde apparaten gebruikt om het dashboard in te vullen. 

In de volgende schermopname ziet u het kant-en-klare dashboard in de toepassingssjabloon. Het dashboard kan volledig worden aangepast aan uw specifieke vereisten voor de oplossing: 

:::image type="content" source="media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png" alt-text="In-Store Analytics: bewaking van condities":::

Raadpleeg de zelfstudie [Een in-store analysetoepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) voor meer informatie.

## <a name="in-store-analytics---checkout"></a>In-Store Analytics: betaling

Voor een aantal detailhandelaren is de manier waarop klanten in hun winkels kunnen betalen een belangrijke onderscheidingsfactor ten opzichte van hun concurrenten. Detailhandelaren willen klanten in hun winkels een vlekkeloze kassa-ervaring bieden om ervoor te zorgen dat ze ook weer in de winkel terugkomen.  

Als ontwikkelaar van oplossingen kunt u de In-Store Analytics-toepassingssjabloon voor betalingen van IoT Central gebruiken om een oplossing te bouwen waarmee winkelpersoneel van inzichten uit het kassagebied wordt voorzien. Sensoren kunnen bijvoorbeeld informatie geven over de lengte van wachtrijen en de gemiddelde wachttijd per kassa.

Gebruik het toepassingssjabloon voor het volgende:

* Verschillende soorten IoT-sensoren verbinden met een IoT Central-toepassingsinstantie.
* De status van het sensornetwerk en alle gatewayapparaten in de omgeving bewaken en beheren.
* Aangepaste regels rondom de betaalervaring in een winkel maken om waarschuwingen voor winkelpersoneel te activeren.
* De omstandigheden bij de kassa in de winkel omzetten in inzichten die door het winkelteam kunnen worden gebruikt om de klantervaring te verbeteren.
* De geaggregeerde inzichten exporteren naar bestaande of nieuwe bedrijfstoepassingen om winkelpersoneel tijdig van nuttige informatie te voorzien.

De toepassingssjabloon wordt geleverd met een aantal apparaatsjablonen en er wordt een set gesimuleerde apparaten gebruikt om het dashboard in te vullen met gegevens over de lengte van wachtrijen. 

In de volgende schermopname ziet u het kant-en-klare dashboard in de toepassingssjabloon. Het dashboard kan volledig worden aangepast aan uw specifieke vereisten voor de oplossing: 

:::image type="content" source="media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png" alt-text="In-Store Analytics: betaling":::

Raadpleeg de zelfstudie [Een in-store analysetoepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) voor meer informatie.

## <a name="smart-inventory-management"></a>Slim voorraadbeheer

De voorraad bestaat uit alle goederen die een detailhandelaar in bezit heeft. Voorraadbeheer is essentieel om ervoor te zorgen dat het juiste product zich op het juiste moment op de juiste plek bevindt. Een detailhandelaar moet de kosten van het bewaren van overvoorraad afwegen tegen de kosten die gepaard gaan met situaties waarin er niet voldoende items op voorraad zijn om aan de vraag te voldoen.

IoT-gegevens die afkomstig zijn van RFID-tags (Radio Frequency Identification), bakens en camera's bieden mogelijkheden om voorraadbeheerprocessen te verbeteren. Ook kunt u de telemetrie die door IoT-sensoren en apparaten met andere gegevensbronnen, zoals weer- en verkeersinformatie in cloudgebaseerde bedrijfsinformatiesystemen, is verzameld, combineren.

Slim voorraadbeheer biedt onder meer de volgende voordelen:

* Het risico dat items niet op voorraad zijn, verlagen en het gewenste klantenserviceniveau garanderen. 
* Uitgebreide analyse en inzichten in de nauwkeurigheid van de voorraad, vrijwel in real time.
* Hulpprogramma's om te bepalen hoeveel voorraad er moet zijn om aan de vraag van klanten te voldoen.

Deze toepassingssjabloon is gericht op de connectiviteit van apparaten en de configuratie en het beheer van RFID- en BLE-lezers (Bluetooth Low Energy).

In de volgende schermopname ziet u het kant-en-klare dashboard in de toepassingssjabloon. Het dashboard kan volledig worden aangepast aan uw specifieke vereisten voor de oplossing:

:::image type="content" source="media/overview-iot-central-retail/smart-inventory-management-dashboard.png" alt-text="Dashboard voor slim voorraadbeheer":::

Zie de zelfstudie [Een toepassingssjabloon voor slim voorraadbeheer implementeren en doorlopen](./tutorial-iot-central-smart-inventory-management.md) voor meer informatie.

## <a name="micro-fulfillment-center"></a>Micro-uitvoeringscentrum

Het landschap van de detailhandel kent een steeds sterkere concurrentie. Detailhandelaren staan daardoor continu onder druk om de kloof tussen vraag en aanbod te overbruggen. Er is een nieuwe trend in opkomst om aan de groeiende vraag van klanten te voldoen: de voorraad opslaan op locaties in de buurt van eindgebruikers en de winkels die zij bezoeken.

Met de toepassingssjabloon voor micro-uitvoeringscentra van IoT Central kunnen ontwikkelaars van toepassingen alle aspecten van hun volledig geautomatiseerde uitvoeringscentra bewaken en beheren. De sjabloon omvat een set bewakingssensoren voor gesimuleerde condities en robotische providers om het ontwikkelingssproces van oplossingen te versnellen. Door deze sensoren worden nuttige signalen vastgelegd die in zakelijke inzichten kunnen worden omgezet, zodat detailhandelaren hun operationele kosten kunnen verlagen en prettige ervaringen kunnen maken voor hun klanten.

Met het toepassingssjabloon kunt u onder meer: 

- Naadloos verschillende IoT-sensoren, zoals een robot of bewakingssensor, verbinden met een IoT Central-toepassingsinstantie.
- De status van het sensornetwerk en alle gatewayapparaten in de omgeving bewaken en beheren.
- Aangepaste regels rondom de omgevingscondities in een uitvoeringscentrum maken om waarschuwingen te activeren.
- De omgevingscondities in uw uitvoeringscentrum omzetten in inzichten die door het team van het winkelmagazijn kunnen worden gebruikt.
- De geaggregeerde inzichten exporteren naar bestaande of nieuwe bedrijfstoepassingen om het winkelpersoneel te helpen.

In de volgende schermopname ziet u het kant-en-klare dashboard in de toepassingssjabloon. Het dashboard kan volledig worden aangepast aan uw specifieke vereisten voor de oplossing:

:::image type="content" source="media/overview-iot-central-retail/MFC-Dashboard.png" alt-text="Micro-uitvoeringscentrum":::

Zie de zelfstudie [Een toepassingssjabloon voor een micro-uitvoeringscentrum implementeren en doorlopen](./tutorial-micro-fulfillment-center.md) voor meer informatie.

## <a name="video-analytics---object-and-motion-detection"></a>Videoanalyse: object- en bewegingsdetectie

Met de toepassingssjabloon *IoT Central-videoanalyse: object- en bewegingsdetectie* kunt u snel ontdekken hoe u een oplossing implementeert, beheert en bewaakt waarvoor intelligente Edge-camera's worden gebruikt voor het detecteren van objecten en beweging.

Voor de videoanalysetoepassing wordt een [Live Video Analytics](#live-video-analytics)-module (LVA) gebruikt die in IoT Edge wordt uitgevoerd. De LVA-module biedt een platform voor het bouwen van intelligente videotoepassingen die de edge en de cloud overbruggen. U kunt het platform gebruiken om IoT-oplossingen, zoals de videoanalysetoepassing, uit te breiden met object- en bewegingsdetectie.

De toepassingssjabloon omvat vier toepassingsdashboards:

* **Aan de slag** bevat koppelingen naar bronnen om u op weg te helpen met de toepassingssjabloon.
* **Voorbeelddashboard** bevat een illustratie van de soorten informatie die u kunt weergeven met de verbonden camera's.
* **(Voorbeeld) Real Camera Management** gebruikt gesimuleerde camera's om te laten zien hoe u uw camera's vanuit de toepassing kunt beheren.
* **(Voorbeeld) Real Camera Monitor** gebruikt gesimuleerde camera's om te laten zien hoe u uw camera's vanuit de toepassing kunt bewaken.

:::image type="content" source="media/overview-iot-central-retail/live-video-analytics.png" alt-text="Videoanalyse: object- en bewegingsdetectie":::

Zie [De architectuur van de videoanalysetoepassing](architecture-video-analytics.md) voor meer informatie over de architectuur van een oplossing.

Zie de zelfstudie [Een videoanalysetoepassing maken in Azure IoT Central](tutorial-video-analytics-deploy.md) voor meer informatie over het implementeren van de oplossing.

### <a name="live-video-analytics"></a>Livevideoanalyse

[Livevideoanalyse](https://github.com/Azure/live-video-analytics) biedt een platform voor het bouwen van intelligente videotoepassingen die de edge en de cloud overbruggen. Het platform biedt de mogelijkheid om live video vast te leggen, op te nemen en te analyseren. Bovendien kunnen de resultaten, zoals video en/of videoanalyses, worden gepubliceerd naar Azure-services. De Azure-services kunnen in de cloud of aan de rand worden uitgevoerd. U kunt het platform gebruiken om IoT-oplossingen te verbeteren met videoanalyse.

## <a name="next-steps"></a>Volgende stappen

U begint als volgt met het maken van een oplossing voor de detailhandel:

* Ga aan de slag met de zelfstudie [Een In-Store Analytics-toepassing in Azure IoT Central maken](./tutorial-in-store-analytics-create-app.md). Hierin leert u stap voor stap hoe u een oplossing bouwt met een van de In-Store Analytics-toepassingssjablonen.
* [Een toepassingssjabloon voor de videoanalysetoepassing implementeren en doorlopen](./tutorial-video-analytics-deploy.md).
* [Een toepassingssjabloon voor verbonden logistiek implementeren en doorlopen](./tutorial-iot-central-connected-logistics.md).
