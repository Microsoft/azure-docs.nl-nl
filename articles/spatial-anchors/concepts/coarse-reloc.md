---
title: Ruwe Herlokalisatie
description: Meer informatie over het gebruik van grove Herlokalisatie om ankers bij u in de buurt te vinden.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071126"
---
# <a name="coarse-relocalization"></a>Coarse-relokalisatie

Ruwe Herlokalisatie is een functie die grootschalige lokalisatie mogelijk maakt door een benadering te bieden, maar snel antwoord te geven op de vraag: *waar is mijn apparaat nu/welke inhoud moet ik naachten?* Het antwoord is niet nauw keurig, maar in plaats daarvan bevindt zich in de vorm: *u sluit deze ankers aan. Probeer een van de twee te vinden*.

Ruwe Herlokalisatie werkt door ankers te labelen met verschillende Lees bewaarden van de sensor die later worden gebruikt voor snelle query's. Voor scenario's voor buiten komen de sensor gegevens doorgaans de GPS (Global Positioning System) positie van het apparaat. Wanneer GPS niet beschikbaar of onbetrouwbaar is (zoals in de lucht), bestaan de sensor gegevens uit de Wi-Fi-toegangs punten en Bluetooth beacons in het bereik. De verzamelde sensor gegevens dragen bij aan het onderhouden van een ruimtelijke index die wordt gebruikt door Azure spatiale ankers om snel te bepalen welke ankers zich in de buurt van uw apparaat bevinden.

## <a name="when-to-use-coarse-relocalization"></a>Wanneer moet u ruwe Herlokalisatie gebruiken?

Als u van plan bent om meer dan 35 ruimtelijke ankers te verwerken in een ruimte die groter is dan een tennis rechtbank, profiteert u waarschijnlijk van ruwe Herlokalisatie ruimtelijke indexering.

Het snel opsporen van ankers die zijn ingeschakeld door ruwe Herlokalisatie is ontworpen om de ontwikkeling van toepassingen die worden ondersteund door grootschalige verzamelingen (bijvoorbeeld miljoenen geo-gedistribueerde), te vereenvoudigen. De complexiteit van ruimtelijke indexering is verborgen, zodat u zich kunt concentreren op uw toepassings logica. Alle verankeringen worden vóór de schermen zwaar gemaakt door Azure spatiale ankers.

## <a name="using-coarse-relocalization"></a>Grove relokalisatie gebruiken

De gebruikelijke werk stroom voor het maken en doorzoeken van ruimtelijke ankers in azure met ruwe Herlokalisatie is:
1.  Een sensor vingerafdruk provider maken en configureren om sensor gegevens van uw keuze te verzamelen.
2.  Start een Azure spatiale-anker sessie en maak ankers. Omdat de sensor vingerafdruking is ingeschakeld, worden de ankers ruimtelijk door ruwe Herlokalisatie.
3.  Een query uitvoeren op omringende ankers met behulp van de speciale zoek criteria in de Azure spatiale-anker sessie.

U kunt de bijbehorende volgende zelf studie raadplegen voor het instellen van grove Herlokalisatie in uw toepassing:
* [Ruwe Herlokalisatie in eenheid](../how-tos/set-up-coarse-reloc-unity.md)
* [Ruwe Herlokalisatie in doel stelling-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Grove Herlokalisatie in SWIFT](../how-tos/set-up-coarse-reloc-swift.md)
* [Grove Herlokalisatie in Java](../how-tos/set-up-coarse-reloc-java.md)
* [Ruwe Herlokalisatie in C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Ruwe Herlokalisatie in C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sens oren en platformen

### <a name="platform-availability"></a>Platform beschikbaarheid

De typen sensor gegevens die u naar de anker service kunt verzenden zijn:

* GPS-positie: breedte graad, lengte graad, hoogte.
* Signaal sterkte van WiFi-toegangs punten in bereik.
* De signaal sterkte van de Bluetooth-beacons in het bereik.

De volgende tabel bevat een overzicht van de beschik baarheid van de sensor gegevens op ondersteunde platforms, samen met alle platformspecifieke voor behoud:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | GEEN<sup>1</sup>  | Ja<sup>2</sup> | Ja<sup>3</sup> |
| **WiFi**        | Ja<sup>4</sup> | Ja<sup>5</sup> | NO  |
| **Conbakeners** | Ja<sup>6</sup> | Ja<sup>6</sup> | Ja<sup>6</sup>|


<sup>1</sup> een extern GPS-apparaat kan worden gekoppeld aan HoloLens. Neem contact op met de [ondersteuning](../spatial-anchor-support.md) als u gebruik wilt maken van HoloLens met een GPS-tracering.<br/>
<sup>2</sup> ondersteund via [LOCATIONMANAGER][3] API'S (GPS en netwerk)<br/>
<sup>3</sup> ondersteund via [CLLocationManager][4] -api's<br/>
<sup>4</sup> wordt ondersteund met een snelheid van ongeveer één scan om de 3 seconden <br/>
<sup>5</sup> als u begint met API Level 28, worden de WiFi-scans elke twee minuten beperkt tot 4 aanroepen. In Android 10 kan de beperking worden uitgeschakeld vanuit het menu instellingen voor ontwikkel aars. Raadpleeg de [Android-documentatie][5]voor meer informatie.<br/>
<sup>6</sup> beperkt tot [Eddystone][1] en [iBeacon][2]

### <a name="which-sensor-to-enable"></a>De sensor die moet worden ingeschakeld

De keuze van de sensor is specifiek voor de toepassing die u ontwikkelt en het platform.
Het volgende diagram biedt een begin punt waarop de combi natie van Sens oren kan worden ingeschakeld, afhankelijk van het lokalisatie scenario:

![Diagram van ingeschakelde Sens oren selecteren](media/coarse-relocalization-enabling-sensors.png)

In de volgende secties vindt u meer informatie over de voor delen en beperkingen voor elk type sensor.

### <a name="gps"></a>GPS

GPS is de go-to-optie voor scenario's van buiten.
Wanneer u GPS gebruikt in uw toepassing, moet u er ook voor zorgen dat de beschik bare leesingen door de hardware doorgaans:

* asynchrone en lage frequentie (minder dan 1 Hz).
* onbetrouwbaar/ruis (gemiddeld 7-m standaard afwijking).

In het algemeen worden zowel het besturings systeem als het ruimtelijke anker van Azure een aantal filtering en extrapolatie op het onbewerkte GPS-signaal in een poging om deze problemen te verhelpen. Deze extra verwerking vereist tijd voor convergentie, zodat u de beste resultaten kunt proberen:

* zo snel mogelijk een provider voor sensor vingerafdruk maken in uw toepassing
* de sensor vingerafdruk provider in stand houden tussen meerdere sessies
* de sensor vingerafdruk provider delen tussen meerdere sessies

GPS-apparaten van consumenten kwaliteit zijn meestal niet nauw keurig. Een studie van [Zandenbergen en Barbeau (2011)][6] rapporteert de mediaan nauw keurigheid van mobiele telefoons met ondersteuning van GPS (A-GPS) om ongeveer 7 meters te zijn. een grote waarde wordt genegeerd. Als u deze meet fouten wilt verwerken, behandelt de service de ankers als waarschijnlijkheids distributies in GPS-ruimte. Als zodanig is een anker nu de ruimte die het meest waarschijnlijk is (dat wil zeggen, met meer dan 95% betrouw baarheid). Dit is waar, onbekende GPS-positie.

Dezelfde reden hiervoor wordt toegepast bij het uitvoeren van query's met GPS. Het apparaat wordt weer gegeven als een andere ruimtelijk betrouw bare regio rondom de True, onbekende GPS-positie. Het detecteren van naburige ankers vertaalt gewoon het vinden van de ankers met betrouw bare regio's die *voldoende dicht* bij de betrouw bare regio van het apparaat staan, zoals in de onderstaande afbeelding wordt weer gegeven:

![Selectie van anker kandidaten met GPS](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>WiFi

Op HoloLens en Android kan Wi-Fi-signaal sterkte een goede optie zijn om grove Herlokalisatie mogelijk te maken.
Het voor deel is de potentiële onmiddellijke Beschik baarheid van Wi-Fi-toegangs punten (gemeen schappelijk in, bijvoorbeeld kantoor ruimten of winkel malls) zonder extra configuratie nodig.

> [!NOTE]
> iOS biedt geen API om Wi-Fi-signaal sterkte te lezen en kan daarom niet worden gebruikt voor ruwe Herlokalisatie met WiFi.

Wanneer u Wi-Fi gebruikt in uw toepassing, moet u erop letten dat de leesingen die worden meegeleverd door de hardware doorgaans:

* asynchrone en lage frequentie (minder dan 0,1 Hz).
* mogelijk beperkt op het niveau van het besturings systeem.
* onbetrouwbaar/ruis (gemiddeld 3-dBm-standaard deviatie).

Bij een poging om deze problemen te verhelpen, wordt geprobeerd een gefilterde WiFi-signaal sterkte toewijzing te bouwen tijdens een sessie. Voor de beste resultaten moet u het volgende proberen:

* Maak de sessie goed voordat u het eerste anker plaatst.
* Bewaar de sessie zo lang mogelijk (dat wil zeggen, alle ankers en query's in één sessie maken).

### <a name="bluetooth-beacons"></a>Bluetooth-beacons
<a name="beaconsDetails"></a>

Het zorgvuldig implementeren van Bluetooth-beacons is een goede oplossing voor grootschalige grove en onnauwkeurige grootschalige Herlokalisatie scenario's. Het is ook de enige methode binnenste die wordt ondersteund op alle drie de platforms.

Beacons zijn doorgaans veelzijdige apparaten, waarbij alles, inclusief UUIDs en MAC-adressen, kan worden geconfigureerd. In azure ruimtelijke ankers verwacht beacons dat ze uniek worden geïdentificeerd door hun UUID. Het is niet mogelijk om ervoor te zorgen dat deze uniekheid waarschijnlijk onjuiste resultaten veroorzaakt. Voor de beste resultaten moet u het volgende doen:

* Wijs unieke UUID toe aan uw beacons.
* Implementeer ze op een manier waarbij uw ruimte op een uniforme plaats wordt bedekt, zodat ten minste drie beacons bereikbaar zijn vanaf elk punt in de ruimte.
* de lijst met unieke Beacon-UUIDs door geven aan de sensor fingerprint provider

Radio signalen zoals Bluetooth worden beïnvloed door obstakels en kunnen interfereren met andere radio signalen. Daarom kan het lastig zijn om te raden of uw ruimte op uniforme wijze wordt gedekt. Om een betere klant ervaring te garanderen, raden wij u aan de dekking van uw beacons hand matig te testen. Dit kan worden gedaan door uw Space door te lopen met de apparaten van de kandidaat en een toepassing met Bluetooth in het bereik. Zorg er tijdens het testen van de dekking voor dat u ten minste drie beacons kunt bereiken vanuit een strategisch standpunt van uw Space. Als u te veel bakens instelt, kan dit leiden tot meer interferentie tussen de beacons en niet noodzakelijkerwijs de nauw keurigheid van ruwe Herlokalisatie verbeteren.

Bluetooth-beacons hebben doorgaans een dekking van 80 meters als er zich geen obstakels in de ruimte bevinden.
Dit betekent dat voor een ruimte die geen grote obstakels heeft, één op een raster patroon elke 40 meter beacons kan implementeren.

Een Beacon-out-batterij heeft een negatieve invloed op de resultaten. Zorg er dus voor dat u uw implementatie regel matig bewaakt voor lage of dode accu's.

In azure spatiale ankers worden alleen Bluetooth-beacons bijgehouden die zich in de lijst met bekende Beacon-UUID bevinden. Kwaadwillende beacons die zijn geprogrammeerd met Allow-List-UUIDen kunnen een negatieve invloed hebben op de kwaliteit van de service. Daarom krijgt u de beste resultaten als u de implementatie van de hoofd code kunt controleren.

### <a name="sensors-accuracy"></a>Nauw keurigheid Sens oren

De nauw keurigheid van het GPS-signaal, zowel bij het maken van ankers als tijdens query's, heeft een grote invloed op de set geretourneerde ankers. Query's op basis van WiFi/beacons worden daarentegen beschouwd als alle ankers die ten minste één toegangs punt of Beacon bevatten die gemeen schappelijk zijn met de query. In dat opzicht wordt het resultaat van een query op basis van WiFi/beacons meestal bepaald door het fysieke bereik van de toegangs punten/beacons en omgevings obstakels.
In de onderstaande tabel wordt de verwachte Zoek ruimte voor elk type sensor geschat:

| Sensoren      | RADIUS van zoek ruimte (ong.) | Details |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Bepaald door de GPS-onzekerheid onder andere factoren. De gerapporteerde getallen worden geschat voor de mediaan GPS nauw keurigheid van mobiele telefoons met een-GPS, dat wil zeggen 7 meters. |
| WiFi        | 50 m-100 m | Bepaald door het bereik van de draadloze toegangs punten. Is afhankelijk van de frequentie, de verzender sterkte, de fysieke obstakels, de interferentie, enzovoort. |
| Conbakeners |  70 m | Bepaald door het bereik van het Beacon. Is afhankelijk van de frequentie, overdrachts sterkte, fysieke obstakels, interferentie, enzovoort. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
