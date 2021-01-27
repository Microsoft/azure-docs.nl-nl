---
title: Azure Media Services typen LiveEvent
description: In Azure Media Services kan een live-gebeurtenis worden ingesteld op een *Pass-Through-* of *Live-code ring*. In dit artikel wordt een gedetailleerde tabel weer gegeven waarin live gebeurtenis typen worden vergeleken.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3126cbf4f9d87e9baa7267e309210b751a47069d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897795"
---
# <a name="live-event-types-comparison"></a>Vergelijking van live gebeurtenis typen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Azure Media Services kan een  [live gebeurtenis](/rest/api/media/liveevents) worden ingesteld op een *Pass-Through* -(een on-premises Live coderings programma verzendt een multi-bitrate stroom) of *Live encoding* (een on-premises Live Encoder verzendt een stream met één bitsnelheid). 

In deze artikelen worden de functies van de live-gebeurtenis typen vergeleken.

## <a name="types-comparison"></a>Typen vergelijking 

In de volgende tabel worden de functies van de live-gebeurtenis typen vergeleken. De typen worden tijdens het maken ingesteld met behulp van [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. none** : een on-premises Live coderings programma verzendt een stream met meerdere bitrates. De opgenomen streams passeren de live-gebeurtenis zonder verdere verwerking. Dit wordt ook wel een Pass-through live-gebeurtenis genoemd.
* **LiveEventEncodingType. Standard** : een on-premises Live coderings programma verzendt een stream met één bitsnelheid naar de live-gebeurtenis en Media Services maakt meerdere bitrate-streams. Als de bijdrage-feed van 720p of hoger is, wordt met de voor instelling **Default720p** een set van 6 omzetting/bitrate-paren gecodeerd (Details worden verderop in het artikel beschreven).
* **LiveEventEncodingType. Premium1080p** : een on-premises Live coderings programma verzendt een stream met één bitsnelheid naar de live-gebeurtenis en Media Services maakt meerdere bitrate-streams. Met de Default1080p-voor instelling wordt de uitvoerset met omzettingen/bitrate-paren opgegeven (verderop in dit artikel vindt u meer informatie). 

| Functie | Pass-through live-gebeurtenis | Standard-of Premium1080p live-gebeurtenis |
| --- | --- | --- |
| Invoer met één bitsnelheid wordt gecodeerd in meerdere bitrates in de Cloud |Nee |Ja |
| Maximale video resolutie voor invoer van contributie |4.000 (4096x2160 bij 60 frames per seconde) |1080p (1920x1088 bij 30 frames per seconde)|
| Aanbevolen maximum aantal lagen in de invoer van bijdragen|Maxi maal 12|Eén audio|
| Maximum aantal lagen in uitvoer| Hetzelfde als invoer|Maxi maal 6 (zie onderstaande systeem instellingen)|
| Maximale cumulatieve band breedte van invoer voor bijdragen|60 Mbps|N.v.t.|
| Maximale bitrate voor één laag in de bijdrage |20 Mbps|20 Mbps|
| Ondersteuning voor audio tracks met meerdere talen|Ja|Nee|
| Ondersteunde video-codecs voor invoer |H. 264/AVC en H. 265/HEVC|H. 264/AVC|
| Ondersteunde video-codecs voor uitvoer|Hetzelfde als invoer|H. 264/AVC|
| Ondersteunde bitdiepte, invoer en uitvoer van video|Maxi maal 10 bits inclusief HDR 10/HLG|8-bits|
| Ondersteunde codecs voor audio-invoer|AAC-LC, hij-AAC v1, HE-AAC v2|AAC-LC, hij-AAC v1, HE-AAC v2|
| Ondersteunde codecs voor uitvoer audio|Hetzelfde als invoer|AAC-LC|
| Maximale video resolutie van uitvoer video|Hetzelfde als invoer|Standard-720p, Premium1080p-1080p|
| Maximale frame frequentie van invoer video|60 frames per seconde|Standard-of Premium1080p-30-frames per seconde|
| Invoer protocollen|RTMP, gefragmenteerd-MP4 (Smooth Streaming)|RTMP, gefragmenteerd-MP4 (Smooth Streaming)|
| Prijs|Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/) en klik op het tabblad live video|Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/) en klik op het tabblad live video|
| Maximale uitvoerings tijd| 24 uur x 365 dagen, Live lineair | 24 uur x 365 dagen, Live lineair (preview-versie)|
| De mogelijkheid om gegevens van Inge sloten CEA 608/708-bijschriften door te geven|Ja|Ja|
| Mogelijkheid om live-transcriptie in te scha kelen|Ja|Ja|
| Ondersteuning voor het invoegen van pastels|Nee|Nee|
| Ondersteuning voor AD-Signa lering via API| Nee|Nee|
| Ondersteuning voor AD-Signa lering via SCTE-35 in-band-berichten|Ja|Ja|
| Mogelijkheid om te herstellen van korte hokjes in een bijdrager|Ja|Gedeeltelijk|
| Ondersteuning voor niet-uniforme invoer GOPs|Ja|Nee: de duur van de invoer moet een vaste GOP terug hebben|
| Ondersteuning voor variabele frame-rate invoer|Ja|Nee – invoer moet een vaste frame frequentie zijn. Kleine variaties zijn toegestaan, bijvoorbeeld tijdens hoge bewegende scènes. Maar de bijdrage feed kan de frame frequentie niet verwijderen (bijvoorbeeld op 15 frames per seconde).|
| Automatische shutoff van live gebeurtenis wanneer invoer toevoer verloren gaat|Nee|Na 12 uur, als er geen LiveOutput wordt uitgevoerd|

## <a name="system-presets"></a>Systeem instellingen

De resoluties en bitrates die zijn opgenomen in de uitvoer van het Live coderings programma, worden bepaald door de [standaard instelling](/rest/api/media/liveevents/create#liveeventencoding). Als u gebruikmaakt van een **standaard** Live coderings programma (LiveEventEncodingType. Standard), geeft de *Default720p* -voor instelling een set van 6 omzetting/bitrate-paren die hieronder worden beschreven. Als u een **Premium1080p** Live-coderings programma (LiveEventEncodingType. Premium1080p) gebruikt, geeft de *Default1080p* -voor instelling de uitvoerset van de combi natie van Resolution/bitrate op.

> [!NOTE]
> U kunt de voor instelling Default1080p niet Toep assen op een live gebeurtenis als deze is ingesteld voor standaard Live encoding. er wordt een fout bericht weer geven. Er wordt ook een fout bericht weer geven als u probeert de Default720p-voor instelling toe te passen op een Premium1080p-Live coderings programma.

### <a name="output-video-streams-for-default720p"></a>Video stromen uitvoeren voor Default720p

Als de bijdrager is van 720p of hoger, wordt de feed door de voor instelling **Default720p** in de volgende 6 lagen gecodeerd. In de onderstaande tabel is de bitsnelheid in kbps, MaxFPS staat voor de Maxi maal toegestane frame frequentie (in frames/seconde), het profiel vertegenwoordigt het H. 264-profiel dat wordt gebruikt.

| Bitsnelheid | Breedte | Hoogte | MaxFPS | Profiel |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hoog |
| 2200 |960 |540 |30 |Hoog |
| 1350 |704 |396 |30 |Hoog |
| 850 |512 |288 |30 |Hoog |
| 550 |384 |216 |30 |Hoog |
| 200 |340 |192 |30 |Hoog |

> [!NOTE]
> Als u de voor instelling voor Live encoding wilt aanpassen, opent u een ondersteunings ticket via Azure Portal. U moet de gewenste tabel met video resolutie en bitrates opgeven. Aanpassing van de bitsnelheid voor audio versleuteling wordt niet ondersteund. Controleer of er slechts één laag is op 720 p, en maximaal zes lagen. Geef ook op dat u een voor instelling aanvraagt.

### <a name="output-video-streams-for-default1080p"></a>Video stromen uitvoeren voor Default1080p

Als de bijdrage-feed van de 1080p-resolutie is, wordt de feed door de voor instelling **Default1080p** in de volgende 6 lagen gecodeerd.

| Bitsnelheid | Breedte | Hoogte | MaxFPS | Profiel |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Hoog |
| 3000 |1280 |720 |30 |Hoog |
| 1600 |960 |540 |30 |Hoog |
| 800 |640 |360 |30 |Hoog |
| 400 |480 |270 |30 |Hoog |
| 200 |320 |180 |30 |Hoog |

> [!NOTE]
> Als u de voor instelling voor Live encoding wilt aanpassen, opent u een ondersteunings ticket via Azure Portal. Geef de gewenste tabel met resoluties en bitrates op. Controleer of er slechts één laag is op 1080p en Maxi maal 6 lagen. Geef ook op dat u een voor instelling aanvraagt voor een Premium1080p Live-coderings programma. De specifieke waarden van de bitsnelheden en resoluties kunnen na verloop van tijd worden aangepast.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Audio stroom voor uitvoer voor Default720p en Default1080p

Voor zowel *Default720p* -als *Default1080p* -voor instellingen wordt audio gecodeerd naar stereo AAC-LC om 128 kbps. De sampling frequentie volgt die van het audio spoor in de bijdrager.

## <a name="implicit-properties-of-the-live-encoder"></a>Impliciete eigenschappen van het Live coderings programma

In de vorige sectie worden de eigenschappen beschreven van het Live coderings programma dat expliciet kan worden beheerd via de vooraf ingestelde waarde, zoals het aantal lagen, resoluties en bitrates. In deze sectie worden de impliciete eigenschappen verduidelijkt.

### <a name="group-of-pictures-gop-duration"></a>De duur van de groep afbeeldingen (GOP terug)

In het Live coderings programma wordt de [GOP terug](https://en.wikipedia.org/wiki/Group_of_pictures) -structuur van de contributie feed gevolgd, wat betekent dat de uitvoer lagen dezelfde GOP terug-duur hebben. Daarom is het raadzaam om de on-premises encoder te configureren voor het produceren van een contributie met vaste duur van de GOP terug (meestal 2 seconden). Dit zorgt ervoor dat de uitgaande HLS-en MPEG-streep stromen van de service ook een vaste GOP terug-duur hebben. Kleine variaties in GOP terug-duur worden waarschijnlijk door de meeste apparaten toegestaan.

### <a name="frame-rate"></a>Frame frequentie

De Live coderings Programma's volgen ook de duur van de afzonderlijke video frames in de bijdrage-invoer, wat betekent dat de uitvoer lagen frames met dezelfde duur hebben. Daarom is het raadzaam om de on-premises encoder te configureren voor het maken van een contributie met een vaste frame snelheid (Maxi maal 30 frames per seconde). Dit zorgt ervoor dat de uitgaande HLS-en MPEG-streep stromen van de service ook de duur van een vaste frame snelheid hebben. Kleine variaties in de frame snelheid kunnen worden toegestaan door de meeste apparaten, maar er is geen garantie dat de Live Encoder een uitvoer produceert die correct wordt afgespeeld. Uw on-premises Live Encoder mag geen frames verwijderen (bijvoorbeeld onder lage accu omstandigheden) of de frame frequentie op een wille keurige manier te variëren.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Oplossing voor invoer van bijdragen en uitvoer lagen

Het Live coderings programma is geconfigureerd om te voor komen dat de contributie wordt geconverteerd. Als gevolg hiervan is de maximale resolutie van de uitvoer lagen niet groter dan die van de contributie feed.

Als u bijvoorbeeld een contributie verzendt op 720p naar een live-gebeurtenis die is geconfigureerd voor Default1080p Live encoding, heeft de uitvoer slechts vijf lagen, beginnend met 720p op 3Mbps, tot 1080p om 200 kbps. Of als u een contributie verzendt naar 360p in een live-gebeurtenis die is geconfigureerd voor standaard Live-code ring, bevat de uitvoer 3 lagen (bij resoluties van 288p, 216p en 192p). Als u in de aanvraag voor het decoderen een bijdrage verzendt naar een 160x90 van een standaard-Live Encoder, bevat de uitvoer één laag bij 160x90 resolutie op dezelfde bitrate als die van de bijdrage toevoer.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bitrate voor invoer van bijdragen en uitvoer lagen

Het Live coderings programma is zo geconfigureerd dat de instellingen voor de bitrate in de voor instelling worden nageleefd, ongeacht de bitsnelheid van de contributie. Als gevolg hiervan kan de bitrate van de uitvoer lagen groter zijn dan die van de contributie feed. Als u bijvoorbeeld een contributie verzendt met een oplossing van 720p met 1 Mbps, blijft de uitvoer lagen hetzelfde als in de bovenstaande [tabel](live-event-types-comparison.md#output-video-streams-for-default720p) .

## <a name="next-steps"></a>Volgende stappen

[Overzicht van live streamen](live-streaming-overview.md)
