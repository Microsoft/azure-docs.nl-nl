---
title: Coarse-relokalisatie
description: Meer informatie over hoe en wanneer u ruwe Herlokalisatie gebruikt. Ruwe Herlokalisatie helpt u bij het vinden van ankers die in de buurt zijn.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656172"
---
# <a name="coarse-relocalization"></a>Coarse-relokalisatie

Grof Herlokalisatie is een functie die grootschalige lokalisatie mogelijk maakt door een benadering, maar snel antwoord op deze vragen te bieden: 
- *Waar bevindt mijn apparaat zich nu?* 
- *Welke inhoud moet ik naachten?* 
 
Het antwoord is niet nauw keurig. Dit is in dit formulier: *u bent bijna klaar met deze ankers. Probeer er een te vinden*.

Ruwe Herlokalisatie werkt door ankers te labelen met verschillende Lees bewaarden van de sensor die later worden gebruikt voor snelle query's. Voor scenario's voor buiten komen de sensor gegevens doorgaans de GPS (Global Positioning System) positie van het apparaat. Wanneer GPS niet beschikbaar of onbetrouwbaar is, zoals wanneer u buiten het leven bent, bestaan de sensor gegevens uit de Wi-Fi toegangs punten en Bluetooth beacons in het bereik. De verzamelde sensor gegevens dragen bij aan het onderhouden van een ruimtelijke index die wordt gebruikt door Azure spatiale ankers om snel te bepalen welke ankers dicht bij uw apparaat staan.

## <a name="when-to-use-coarse-relocalization"></a>Wanneer moet u ruwe Herlokalisatie gebruiken?

Als u van plan bent om meer dan 35 ruimtelijke ankers te verwerken in een ruimte die groter is dan een tennis rechtbank, profiteert u waarschijnlijk van ruwe Herlokalisatie ruimtelijke indexering.

Het snel opzoeken van ankers die zijn ingeschakeld door grove Herlokalisatie is ontworpen om de ontwikkeling van toepassingen die worden ondersteund door grootschalige verzamelingen van, zeggen, miljoenen geografisch gedistribueerde ankers, te vereenvoudigen. De complexiteit van ruimtelijke indexering is alle verborgen, zodat u zich kunt concentreren op uw toepassings logica. Alle moeilijke werkzaamheden worden uitgevoerd achter de schermen door Azure spatiale ankers.

## <a name="using-coarse-relocalization"></a>Grove relokalisatie gebruiken

Hier volgt de gebruikelijke werk stroom voor het maken en doorzoeken van ruimtelijke ankers in azure met ruwe Herlokalisatie:
1.  Een sensor vingerafdruk provider maken en configureren om de gewenste sensor gegevens te verzamelen.
2.  Start een Azure spatiale-ankers sessie en maak de ankers. Omdat de sensor vingerafdruking is ingeschakeld, worden de ankers ruimtelijk door ruwe Herlokalisatie.
3.  Zoek reactieve ankers met behulp van ruwe Herlokalisatie via de speciale zoek criteria in de ruimtelijk-ankers sessie.

U kunt naar een van deze zelf studies verwijzen om ruwe herconfiguratie in uw toepassing in te stellen:
* [Coarse-relokalisatie in Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Coarse-relokalisatie in Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Coarse-relokalisatie in Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Coarse-relokalisatie in Java](../how-tos/set-up-coarse-reloc-java.md)
* [Coarse-relokalisatie in C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Coarse-relokalisatie in C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sens oren en platformen

### <a name="platform-availability"></a>Platform beschikbaarheid

U kunt deze typen sensor gegevens verzenden naar de anker service:

* GPS-positie: breedte graad, lengte graad, hoogte
* Signaal sterkte van Wi-Fi toegangs punten in bereik
* Signaal sterkte van Bluetooth-beacons in bereik

Deze tabel bevat een overzicht van de beschik baarheid van de sensor gegevens op ondersteunde platforms en biedt informatie waarvan u rekening moet houden:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Geen<sup>1</sup>  | Ja<sup>2</sup> | Ja<sup>3</sup> |
| **Wi-Fi**        | Ja<sup>4</sup> | Ja<sup>5</sup> | Nee  |
| **Conbakeners** | Ja<sup>6</sup> | Ja<sup>6</sup> | Ja<sup>6</sup>|


<sup>1</sup> een extern GPS-apparaat kan worden gekoppeld aan HoloLens. Neem contact op met de [ondersteuning](../spatial-anchor-support.md) als u gebruik wilt maken van HoloLens met een GPS-tracering.<br/>
<sup>2</sup> ondersteund via [LOCATIONMANAGER][3] API'S (GPS en netwerk).<br/>
<sup>3</sup> wordt ondersteund via [CLLocationManager][4] -api's.<br/>
<sup>4</sup> wordt ondersteund met een snelheid van ongeveer één scan om de 3 seconden. <br/>
<sup>5</sup> als u begint met API Level 28, worden Wi-Fi scans elke 2 minuten beperkt tot vier aanroepen. Vanaf Android 10 kunt u deze beperking uitschakelen vanuit het menu instellingen voor **ontwikkel aars** . Raadpleeg de [Android-documentatie][5]voor meer informatie.<br/>
<sup>6</sup> beperkt tot [Eddystone][1] en [iBeacon][2].

### <a name="which-sensor-to-enable"></a>De sensor die moet worden ingeschakeld

De keuze van de sensor is afhankelijk van de toepassing die u ontwikkelt en het platform.
Dit diagram bevat een start punt voor het bepalen van de combi natie van Sens oren die u kunt inschakelen, afhankelijk van het lokalisatie scenario:

![Diagram waarin ingeschakelde Sens oren voor verschillende scenario's worden weer gegeven.](media/coarse-relocalization-enabling-sensors.png)

De volgende secties bieden meer inzicht in de voor delen en beperkingen van elk type sensor.

### <a name="gps"></a>GPS

GPS is de go-to-optie voor scenario's van buiten.
Wanneer u GPS gebruikt in uw toepassing, moet u er ook voor zorgen dat de beschik bare leesingen door de hardware doorgaans:

* Asynchrone en lage frequentie (minder dan 1 Hz).
* Onbetrouwbaar/ruis (gemiddeld 7-m standaard afwijking).

In het algemeen worden zowel het besturings systeem als het ruimtelijke ankers een aantal filters en extrapolatie van het onbewerkte GPS-signaal in een poging gedaan om deze problemen te verhelpen. Deze extra verwerking vereist tijd voor convergentie, dus voor de beste resultaten moet u het volgende proberen:

* Maak zo snel mogelijk één sensor vingerafdruk provider in uw toepassing.
* Zorg ervoor dat de sensor vingerafdruk provider actief blijft tussen meerdere sessies.
* De sensor vingerafdruk provider delen tussen meerdere sessies.

GPS-apparaten van consumenten kwaliteit zijn meestal niet nauw keurig. Een studie van [Zandenbergen en Barbeau (2011)][6] rapporteert dat de gemiddelde nauw keurigheid van mobiele telefoons met ondersteuning voor GPS (A-GPS) ongeveer 7 meters is. Dat is een zeer grote waarde om te negeren. Als u deze meet fouten wilt verwerken, behandelt de service ankers als waarschijnlijkheids distributies in GPS-ruimte. Een anker is dus de ruimte die het meest waarschijnlijk is (met meer dan 95% betrouw baarheid) met de waarde waar, onbekende GPS-positie.

Dezelfde reden hiervoor geldt wanneer u een query uitvoert met behulp van GPS. Het apparaat wordt weer gegeven als een andere ruimtelijk betrouw bare regio rondom de True, onbekende GPS-positie. Het detecteren van naburige ankers vertaalt de ankers met betrouw bare regio's *dicht genoeg* voor de vertrouwens regio van het apparaat, zoals hier wordt weer gegeven:

![Diagram dat illustreert het vinden van anker kandidaten door gebruik te maken van GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

Op HoloLens en Android kan Wi-Fi signaal sterkte een goede manier zijn om grove Herlokalisatie mogelijk te maken.
Het voor deel is de potentiële onmiddellijke Beschik baarheid van Wi-Fi toegangs punten (bijvoorbeeld gemeen schappelijk in kantoor ruimten en winkel malls) zonder dat er extra instellingen nodig zijn.

> [!NOTE]
> iOS biedt geen API voor het lezen van Wi-Fi signaal sterkte en kan daarom niet worden gebruikt voor grove Herlokalisatie die via Wi-Fi is ingeschakeld.

Wanneer u Wi-Fi gebruikt in uw toepassing, moet u er ook voor zorgen dat de beschik bare waarden van de hardware doorgaans:

* Asynchrone en lage frequentie (minder dan 0,1 Hz).
* Mogelijk beperkt op het niveau van het besturings systeem.
* Onbetrouwbaar/ruis (gemiddeld 3-dBm standaard deviatie).

Ruimtelijke ankers proberen tijdens een sessie een gefilterde kaart met Wi-Fi signaal sterkte te maken in een poging om deze problemen te verhelpen. Voor de beste resultaten kunt u het volgende proberen:

* Maak de sessie goed voordat u het eerste anker plaatst.
* Zorg ervoor dat de sessie zo lang mogelijk actief blijft. (Dat wil zeggen, alle ankers en query's in één sessie maken.)

### <a name="bluetooth-beacons"></a>Bluetooth-beacons
<a name="beaconsDetails"></a>

Een zorgvuldige implementatie van Bluetooth-beacons is een goede oplossing voor grootschalige grove en onnauwkeurige grootschalige Herlokalisatie scenario's. Het is ook de enige methode binnenste die wordt ondersteund op alle drie de platforms.

Beacons zijn doorgaans veelzijdige apparaten waarop alles kan worden geconfigureerd, met inbegrip van UUID-en MAC-adressen. In azure ruimtelijke ankers verwacht beacons dat ze uniek worden geïdentificeerd door hun UUID. Als u deze uniekheid niet weet, krijgt u waarschijnlijk onjuiste resultaten. Voor de beste resultaten:

* Wijs unieke UUID toe aan uw beacons.
* Implementeer beacons op een manier die zich op een gelijkmatige afstand van uw ruimte bevindt, zodat er ten minste drie beacons bereikbaar zijn vanaf elk punt in de ruimte.
* Geef de lijst met unieke Beacon-UUID door aan de sensor vingerafdruk provider.

Radio signalen, zoals die van Bluetooth, worden beïnvloed door obstakels en kunnen interfereren met andere radio signalen. Het kan lastig zijn om te raden of uw ruimte op uniforme wijze wordt gedekt. Om een betere klant ervaring te garanderen, raden wij u aan de dekking van uw beacons hand matig te testen. U kunt een test uitvoeren door uw Space door te lopen met de apparaten van de kandidaat en een toepassing die Bluetooth in bereik weergeeft. Terwijl u de dekking test, moet u ervoor zorgen dat u ten minste drie beacons kunt bereiken vanaf een strategische positie in uw ruimte. Als er te veel beacons zijn, kan dit leiden tot meer interferentie en kan het niet nood zakelijk zijn om de nauw keurigheid van ruwe Herlokalisatie te verbeteren.

Bluetooth-beacons best rijken doorgaans 80 meters als er geen obstakels aanwezig zijn in de ruimte.
Voor een ruimte zonder grote obstakels kunt u dus beacons implementeren in een raster patroon elke 40 meter.

Een Beacon-out-batterij heeft invloed op de resultaten. Zorg er dus voor dat u uw implementatie regel matig bewaakt voor weinig of niet-gefactureerde accu's.

In azure spatiale ankers worden alleen Bluetooth beacons gevolgd die zich in de lijst met bekende beacons-UUID bevinden. Maar kwaadwillende beacons die zijn geprogrammeerd met allowlisted-UUID, kunnen een negatieve invloed hebben op de kwaliteit van de service. Daarom krijgt u de beste resultaten in de weer te geven ruimtes waarbij u de Beacon-implementatie kunt beheren.

### <a name="sensor-accuracy"></a>Nauw keurigheid van sensor

De nauw keurigheid van het GPS-signaal, zowel tijdens het maken van het anker als tijdens query's, heeft een grote invloed op de set geretourneerde ankers. Query's op basis van Wi-Fi/beacons worden daarentegen beschouwd als alle ankers die ten minste één toegangs punt of Beacon bevatten die gemeen schappelijk zijn voor de query. In dat opzicht wordt het resultaat van een query die is gebaseerd op Wi-Fi/beacons, voornamelijk bepaald door het fysieke bereik van de toegangs punten/beacons en omgevings obstakels.
In deze tabel wordt de verwachte Zoek ruimte voor elk type sensor geschat:

| Sensoren      | RADIUS van zoek ruimte (bij benadering) | Details |
|-------------|:-------:|---------|
| **GPS**         | 20 m tot 30 m | Bepaald door de GPS-onzekerheid, onder andere factoren. De gerapporteerde getallen worden geschat voor de mediaan GPS nauw keurigheid van mobiele telefoons met een-GPS: 7 meters. |
| **Wi-Fi**        | 50 m tot 100 m | Bepaald door het bereik van de draadloze toegangs punten. Is afhankelijk van de frequentie, de verzender sterkte, de fysieke obstakels, de interferentie, enzovoort. |
| **Conbakeners** |  70 m | Bepaald door het bereik van het Beacon. Is afhankelijk van de frequentie, overdrachts sterkte, fysieke obstakels, interferentie, enzovoort. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
