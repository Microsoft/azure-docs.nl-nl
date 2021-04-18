---
title: Coarse-relokalisatie
description: Meer informatie over hoe en wanneer u coarse-relokalisatie gebruikt. Coarse-relokalisatie helpt u bij het vinden van ankers die bij u in de buurt zijn.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: d2737f58fa95d1aa45d9952e8b501c1b9be4d1b0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600349"
---
# <a name="coarse-relocalization"></a>Coarse-relokalisatie

Coarse-relokalisatie is een functie die grootschalige lokalisatie mogelijk maakt door een geschatte maar snelle antwoord te bieden op deze vragen: 
- *Waar is mijn apparaat nu?* 
- *Welke inhoud moet ik observeren?* 
 
Het antwoord is niet nauwkeurig. Deze heeft deze vorm: *U staat dicht bij deze ankers. Probeer een van deze te vinden.*

Coarse-relokalisatie werkt door ankers te taggen met verschillende sensormetingen op het apparaat die later worden gebruikt voor snelle query's. Voor buitenscenario's zijn de sensorgegevens doorgaans de GPS-positie (Global Positioning System) van het apparaat. Wanneer GPS niet beschikbaar of onbetrouwbaar is, zoals wanneer u binnen bent, bestaan de sensorgegevens uit de Wi-Fi toegangspunten en Bluetooth-bakens binnen bereik. De verzamelde sensorgegevens dragen bij aan het onderhouden van een ruimtelijke index die wordt gebruikt door Azure Spatial Anchors om snel te bepalen welke ankers zich dicht bij uw apparaat.

## <a name="when-to-use-coarse-relocalization"></a>Wanneer gebruikt u coarse-relokalisatie?

Als u van plan bent om meer dan 35 ruimtelijke ankers te verwerken in een ruimte die groter is dan een parkeerplaats, profiteert u waarschijnlijk van coarse-relokalisatie ruimtelijke indexering.

De snelle opzoekactie van ankers die mogelijk worden gemaakt door coarse-relokalisatie is ontworpen om de ontwikkeling te vereenvoudigen van toepassingen die worden gebruikt door grootschalige verzamelingen van bijvoorbeeld miljoenen geografisch gedistribueerde ankers. De complexiteit van ruimtelijke indexering is verborgen, zodat u zich kunt richten op uw toepassingslogica. Alle moeilijke werkzaamheden worden achter de schermen uitgevoerd door Azure Spatial Anchors.

## <a name="using-coarse-relocalization"></a>Coarse-relokalisatie gebruiken

Hier is de gebruikelijke werkstroom voor het maken en opvragen van Azure-Spatial Anchors met coarse-relokalisatie:
1.  Maak en configureer een sensor vingerafdrukprovider voor het verzamelen van de sensorgegevens die u wilt.
2.  Start een Azure Spatial Anchors-sessie en maak de ankers. Omdat sensorvingervingerving is ingeschakeld, worden de ankers ruimtelijk geïndexeerd door coarse-relokalisatie.
3.  Query's uitvoeren rond ankers met behulp van coarse-relokalisatie via de toegewezen zoekcriteria in de Spatial Anchors sessie.

Raadpleeg een van deze zelfstudies voor het instellen van coarse-relokalisatie in uw toepassing:
* [Coarse-relokalisatie in Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Coarse-relokalisatie in Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Coarse-relokalisatie in Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Coarse-relokalisatie in Java](../how-tos/set-up-coarse-reloc-java.md)
* [Coarse-relokalisatie in C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Coarse-relokalisatie in C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensoren en platforms

### <a name="platform-availability"></a>Platformbeschikbaarheid

U kunt deze typen sensorgegevens naar de ankerservice verzenden:

* GPS-positie: breedtegraad, lengtegraad, hoogte
* Signaalsterkte van Wi-Fi toegangspunten binnen bereik
* Signaalsterkte van Bluetooth-bakens binnen bereik

Deze tabel bevat een overzicht van de beschikbaarheid van de sensorgegevens op ondersteunde platforms en bevat informatie die u moet kennen:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **Gps**         | Nr.<sup>1</sup>  | Ja<sup>2</sup> | Ja<sup>3</sup> |
| **Wi-Fi**        | Ja<sup>4</sup> | Ja<sup>5</sup> | No  |
| **BLE-bakens** | Ja<sup>6</sup> | Ja<sup>6</sup> | Ja<sup>6</sup>|


<sup>1</sup> Er kan een extern GPS-apparaat worden gekoppeld aan HoloLens. Neem [contact op met](../spatial-anchor-support.md) onze ondersteuning als u HoloLens wilt gebruiken met een GPS-tracker.<br/>
<sup>2</sup> Ondersteund via [LocationManager-API's][3] (zowel GPS als NETWORK).<br/>
<sup>3 Ondersteund</sup> via [CLLocationManager-API's.][4]<br/>
<sup>4</sup> Ondersteund met een snelheid van ongeveer één scan per 3 seconden. <br/>
<sup>5</sup> Vanaf API-niveau 28 worden Wi-Fi scans beperkt tot vier aanroepen om de 2 minuten. Vanaf Android 10 kunt u deze beperking uitschakelen via het menu **Instellingen voor** ontwikkelaars. Zie de [Android-documentatie voor meer informatie.][5]<br/>
<sup>6</sup> Beperkt tot [Moetstone][1] en [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Welke sensor moet worden ingeschakeld

De keuze van de sensor is afhankelijk van de toepassing die u ontwikkelt en het platform.
Dit diagram biedt een beginpunt om te bepalen welke combinatie van sensoren u kunt inschakelen, afhankelijk van het lokalisatiescenario:

![Diagram met ingeschakelde sensoren voor verschillende scenario's.](media/coarse-relocalization-enabling-sensors.png)

De volgende secties bieden meer inzicht in de voordelen en beperkingen van elk sensortype.

### <a name="gps"></a>Gps

GPS is de optie voor buitenscenario's.
Wanneer u GPS in uw toepassing gebruikt, moet u er rekening mee houden dat de metingen die door de hardware worden geleverd doorgaans zijn:

* Asynchrone en lage frequentie (minder dan 1 Hz).
* Onbetrouwbaar/ruis (gemiddeld 7 m standaarddeviatie).

In het algemeen zullen zowel het besturingssysteem van het apparaat als Spatial Anchors het onbewerkte GPS-signaal filteren en extrapoleren in een poging om deze problemen te verhelpen. Deze extra verwerking vereist tijd voor convergentie, dus voor de beste resultaten moet u het volgende proberen:

* Maak zo vroeg mogelijk één sensor-vingerafdrukprovider in uw toepassing.
* Houd de vingerafdrukprovider van de sensor tussen meerdere sessies in leven.
* Deel de vingerafdrukprovider van de sensor tussen meerdere sessies.

GPS-apparaten op consumentenkwaliteit zijn doorgaans onnauwkeurig. Uit een onderzoek door [Kunnenteen en Barbava (2011)][6] blijkt dat de mediaannauwkeurigheid van mobiele telefoons met ondersteuning voor GPS (A-GPS) ongeveer 7 meter is. Dat is nogal wat om te negeren. Om rekening te houden met deze meetfouten, behandelt de service ankers als waarschijnlijkheidsdistributies in GPS-ruimte. Een anker is dus de regio van de ruimte die waarschijnlijk (met meer dan 95% betrouwbaarheid) de werkelijke, onbekende GPS-positie bevat.

Dezelfde redenering is van toepassing wanneer u een query uitvoert met BEHULP van GPS. Het apparaat wordt weergegeven als een andere regio voor ruimtelijk vertrouwen rond de werkelijke, onbekende GPS-positie. Het detecteren van nabijgelegen ankers vertaalt zich  in het vinden van de ankers met betrouwbaarheidsregio's die dicht genoeg bij de betrouwbaarheidsregio van het apparaat liggen, zoals hier wordt geïllustreerd:

![Diagram dat het vinden van ankerkandidaten met behulp van GPS illustreert.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

Op HoloLens en Android kan Wi-Fi signaalsterkte een goede manier zijn om binnen coarse-relokalisatie mogelijk te maken.
Het voordeel is de potentiële onmiddellijke beschikbaarheid van Wi-Fi-toegangspunten (bijvoorbeeld in kantoorruimten en winkelruimten) zonder dat er extra instellingen nodig zijn.

> [!NOTE]
> iOS biedt geen API voor het lezen van Wi-Fi signaalsterkte, zodat deze niet kan worden gebruikt voor coarse-relokalisatie ingeschakeld via Wi-Fi.

Wanneer u een Wi-Fi in uw toepassing gebruikt, moet u er rekening mee houden dat de metingen die door de hardware worden geleverd doorgaans zijn:

* Asynchroon en lage frequentie (minder dan 0,1 Hz).
* Mogelijk beperkt op het niveau van het besturingssysteem.
* Onbetrouwbaar/ruis (gemiddeld 3 dBm standaardafwijking).

Spatial Anchors probeert tijdens een sessie een gefilterde kaart van Wi-Fi signaalsterkte te bouwen om deze problemen te verhelpen. Probeer het volgende voor de beste resultaten:

* Maak de sessie goed voordat u het eerste anker gaat plaatsen.
* Houd de sessie zo lang mogelijk in leven. (Dat wil zeggen dat alle ankers en query's in één sessie worden uitgevoerd.)

### <a name="bluetooth-beacons"></a>Bluetooth-bakens
<a name="beaconsDetails"></a>

Een zorgvuldige implementatie van Bluetooth-bakens is een goede oplossing voor grootschalige scenario's met grootschalige coarse-relokalisatie, waarbij GPS niet of onnauwkeurig is. Het is ook de enige indoormethode die op alle drie de platforms wordt ondersteund.

Bakens zijn doorgaans veelzijdige apparaten waarop alles kan worden geconfigureerd, waaronder UUID's en MAC-adressen. Azure Spatial Anchors verwacht dat bakens uniek worden geïdentificeerd door hun UUID's. Als u deze uniekheid niet zeker weet, krijgt u waarschijnlijk onjuiste resultaten. Voor de beste resultaten:

* Wijs unieke UUID's toe aan uw bakens.
* Implementeer bakens op een manier die uw ruimte gelijkmatig bedekt en zodat ten minste drie bakens bereikbaar zijn vanaf elk punt in de ruimte.
* Geef de lijst met unieke baken-UUID's door aan de vingerafdrukprovider van de sensor.

Radiosignalen zoals die van Bluetooth worden beïnvloed door obstakels en kunnen andere radiosignalen verstoren. Het kan dus lastig zijn om te raden of uw ruimte gelijkmatig wordt gedekt. Om een betere klantervaring te garanderen, raden we u aan de dekking van uw bakens handmatig te testen. U kunt een test uitvoeren door rond uw ruimte te lopen met kandidaatapparaten en een toepassing met Bluetooth binnen bereik. Zorg er tijdens het testen van de dekking voor dat u ten minste drie bakens kunt bereiken vanaf elke strategische positie in uw ruimte. Te veel bakens kunnen leiden tot meer interferentie tussen de bakens en verbeteren niet noodzakelijkerwijs de nauwkeurigheid van coarse-relokalisatie.

Bluetooth-bakens omvatten doorgaans 80 meter als er geen obstakels in de ruimte aanwezig zijn.
Voor een ruimte zonder grote obstakels kunt u dus elke 40 meter bakens in een rasterpatroon implementeren.

Een baken dat bijna leeg is, is van invloed op de resultaten. Zorg er daarom voor dat u uw implementatie regelmatig controleert op lage of niet-geladen accu's.

Azure Spatial Anchors houdt alleen Bluetooth-bakens bij die zich in de lijst met bekende nabijheids-UUID's bevinden. Maar schadelijke bakens die zijn geprogrammeerd om toegestane UUID's te hebben, kunnen een negatieve invloed hebben op de kwaliteit van de service. U krijgt dus de beste resultaten in gecureerde ruimten waar u de implementatie van bakens kunt bepalen.

### <a name="sensor-accuracy"></a>Nauwkeurigheid van de sensor

De nauwkeurigheid van het GPS-signaal, zowel tijdens het maken van ankers als tijdens query's, heeft een aanzienlijke invloed op de set geretourneerde ankers. Bij query's op basis van Wi-Fi/bakens wordt daarentegen rekening houden met alle ankers met ten minste één toegangspunt/baken die gemeenschappelijk zijn voor de query. In die zin wordt het resultaat van een query op basis van Wi-Fi/bakens voornamelijk bepaald door het fysieke bereik van de toegangspunten/bakens en omgevingsfactoren.
In deze tabel wordt de verwachte zoekruimte voor elk sensortype schatten:

| Sensor      | Radius voor zoekruimte (bij benadering) | Details |
|-------------|:-------:|---------|
| **Gps**         | 20 m tot 30 m | Bepaald door de GPS-onzekerheid, onder andere factoren. De gerapporteerde nummers worden geschat voor de mediaan GPS nauwkeurigheid van mobiele telefoons met A-GPS: 7 meters. |
| **Wi-Fi**        | 50 m tot 100 m | Bepaald door het bereik van de draadloze toegangspunten. Is afhankelijk van de frequentie, de sterkte van de sterkte van de sterkte, de fysieke verstoring, interferentie, en meer. |
| **BLE-bakens** |  70 m | Bepaald door het bereik van het baken. Is afhankelijk van de frequentie, de overdrachtssterkte, fysieke verstoringen, interferentie, en meer. |

<!-- Reference links in article -->
[1]: https://developer.estimote.com/eddystone/
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
