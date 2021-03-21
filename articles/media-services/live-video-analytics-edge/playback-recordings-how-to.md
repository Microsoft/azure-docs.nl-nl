---
title: Opnamen afspelen-Azure
description: U kunt live video Analytics op IoT Edge gebruiken voor continue video-opname, waarbij u gedurende weken of maanden video in de cloud kunt opnemen. U kunt de opname ook beperken tot clips die van belang zijn, via het vastleggen op basis van gebeurtenissen. In dit artikel wordt uitgelegd hoe u opnamen kunt afspelen.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0351f10d9fac3ad7e3b4fdd5fd549eb7c0023694
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99490891"
---
# <a name="playback-of-recordings"></a>Opnamen afspelen 

## <a name="pre-read"></a>Vooraf lezen  

* [Video afspelen](video-playback-concept.md)
* [Continue video-opname](continuous-video-recording-concept.md)
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)

## <a name="background"></a>Achtergrond  

U kunt live video Analytics gebruiken op IoT Edge voor [continue video-opname](continuous-video-recording-concept.md) (CVR), waarbij u gedurende weken of maanden video in de cloud kunt opnemen. U kunt de opname ook beperken tot clips die van belang zijn, via een [op gebeurtenissen gebaseerde video-opname](event-based-video-recording-concept.md) (EVR). 

Uw media service-account is gekoppeld aan een Azure Storage-account en wanneer u video opneemt in de Cloud, wordt de inhoud naar een [Media Service-Asset](terminology.md#asset)geschreven. Met Media Services kunt u [die inhoud streamen](terminology.md#streaming), hetzij nadat de opname is voltooid, of wanneer de opname actief is. Media Services biedt u de benodigde mogelijkheden voor het leveren van stromen via HLS of MPEG-DASH-protocollen voor het afspelen van apparaten (clients). Zie het artikel over het [afspelen van Video's](video-playback-concept.md) voor meer informatie. U gebruikt de Api's van media service om de vereiste streaming-Url's te genereren, die door clients worden gebruikt om de video & audio af te spelen. Zie [een streaming-Locator maken en url's bouwen](../latest/create-streaming-locator-build-url.md)voor meer informatie over het samen stellen van de streaming-URL voor een Asset. Zodra de streaming-URL voor een Asset is gemaakt, kunt u de koppeling van de URL met de video bron (camera) in uw content management-systeem opslaan.

Als streaming via HLS, ziet de streaming-URL er bijvoorbeeld uit als `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Voor MPEG-DASH zou het er als volgt uitzien `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Hiermee wordt een manifest bestand geretourneerd, dat onder andere de totale duur van de stroom die wordt geleverd, wordt beschreven en of er vooraf geregistreerde inhoud of de live-feed wordt weer gegeven.

### <a name="live-vs-vod"></a>Live versus VoD  

Streaming-protocollen zoals HLS en MPEG-DASH zijn ontworpen voor het afhandelen van scenario's zoals het streamen van Live Video's, evenals het streamen van on-demand/vooraf geregistreerde inhoud zoals TV-Program ma's en films. Voor Live Video's zijn HLS-en MPEG-DASH-clients ontworpen om te beginnen met het afspelen vanaf de meest recente tijd. Met films kunnen bezoekers echter verwachten dat ze vanaf het begin beginnen en om te gaan als ze ervoor kiezen. De HLS-en MPEG-DASH-manifesten hebben vlaggen die aangeven of de video een live stream vertegenwoordigt of vooraf geregistreerde inhoud is.
Dit concept is ook van toepassing op HLS-en MPEG-DASH-streams van assets die video bevatten die is opgenomen met live video Analytics op IoT Edge.

## <a name="browsing-and-selective-playback-of-recordings"></a>Bladeren en selectief afspelen van opnamen  

Denk na over het scenario waarin u live video Analytics op IoT Edge hebt gebruikt voor het opnemen van video alleen van 8 A.M. tot 10AM op dagen dat een school is geopend, voor het hele academische jaar. Of misschien wilt u video alleen opnemen van 7AM naar 19.00 uur op nationale feest dagen. Bij een van deze twee scenario's moet u, wanneer u uw video-opname wilt doorzoeken en weer geven, het volgende doen:

* Een manier om te bepalen welke datums/uren video u hebt tijdens een opname.
* Een manier om een gedeelte te selecteren (bijvoorbeeld 9:00 naar 11:00 op nieuwe dag van de jaren) van die opname om af te spelen.

Media Services biedt u een query-API (availableMedia) om het eerste probleem op te lossen en filters voor tijd bereik (startTime, endTime) om de seconde op te lossen.

## <a name="query-api"></a>Query-API 

Wanneer u CVR gebruikt, kunnen afspeel apparaten (clients) geen manifest aanvragen voor het afspelen van de volledige opname.  Een registratie met meerdere jaren levert een manifest bestand op dat te groot was om te worden afgespeeld. het is dus onhandiger om te parseren in bruikbare delen aan de client zijde.  De client moet weten welke datetime-bereiken gegevens bevatten in de opname, zodat het mogelijk is om geldige aanvragen te doen zonder veel raden. Dit is de plaats waar de nieuwe query-API wordt geleverd. clients kunnen nu gebruikmaken van deze API voor server zijde om inhoud te detecteren.

Waarbij de precisie waarde een van de volgende waarden kan zijn: jaar, maand, dag of volledig (zoals hieronder weer gegeven). 

|Precisie|jaar|maand|day|waard|
|---|---|---|---|---|
|Query|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Antwoord|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Antwoord van de volledige precisie. Als er helemaal geen hiaten zijn, is de start tijd startTime en eindigt de eind datum.|
|Beperkingen|&#x2022;startTime <= endTime<br/>&#x2022;moet beide de notatie JJJJ hebben. anders wordt er een fout geretourneerd.<br/>&#x2022;waarden kunnen elk wille keurig aantal jaren van elkaar zijn.<br/>&#x2022;waarden zijn inclusief.|&#x2022;startTime <= endTime<br/>&#x2022;beide moeten de notatie JJJJ-MM hebben, anders retourneert fout.<br/>&#x2022;waarden kunnen Maxi maal 12 maanden uit elkaar liggen.<br/>&#x2022;waarden zijn inclusief.|&#x2022;startTime <= endTime<br/>&#x2022;beide moeten zijn in de indeling JJJJ-MM-DD, anders wordt er een fout geretourneerd.<br/>&#x2022;waarden kunnen Maxi maal 31 dagen uit elkaar liggen.<br/>Waarden zijn inclusief.|&#x2022;startTime < endTime<br/>&#x2022;waarden kunnen Maxi maal 25 uur uit elkaar liggen.<br/>&#x2022;waarden zijn inclusief.|

#### <a name="additional-request-format-considerations"></a>Aandachtspunten voor aanvullende aanvraag indeling

* Alle tijden zijn in UTC
* De para meter voor de precisie query reeks is vereist.  
* De query teken reeks parameters startTime en endTime zijn vereist voor de waarden voor maand, dag en volledige precisie.  
* De query teken reeks parameters startTime en endTime zijn optioneel voor de waarde van het jaar precisie (geen, ofwel of beide worden ondersteund).  

    * Als de startTime wordt wegge laten, wordt ervan uitgegaan dat het eerste jaar in de registratie is.
    * Als de endTime wordt wegge laten, wordt ervan uitgegaan dat het laatste jaar in de registratie is.
    * Bijvoorbeeld, als uw opname is gestart in 2011, en tot 2020 is voortgezet, geldt het volgende:

        * /availableMedia? Precision = Year is hetzelfde als/availableMedia? Precision = Year&startTime = 2011&endTime = 2020
        * /availableMedia? Precision = Year&startTime = 2015 is hetzelfde als/availableMedia? Precision = Year&startTime = 2015&endTime = 2020
        * /availableMedia? Precision = Year&endTime = 2018 is hetzelfde als/availableMedia? Precision = Year&startTime = 2011&endTime = 2018

Als er geen media gegevens beschikbaar zijn voor de peri Oden, wordt een lege lijst geretourneerd.

Als de Asset geen opnamen van een media grafiek bevat, wordt een HTTP 400-antwoord geretourneerd met een fout bericht waarin wordt uitgelegd dat deze functie alleen beschikbaar is voor inhoud die is opgenomen via een media grafiek.

Als de tijds duur die door de para meters is opgegeven, groter is dan wordt toegestaan door het maximale tijds bereik voor een opgegeven query type, wordt een HTTP-400 geretourneerd met een fout bericht dat het Maxi maal toegestane tijds bereik voor het aangevraagde query type beschrijft.

Ervan uitgaande dat het tijds bereik geldig is voor de opgegeven para meters, worden er geen fouten geretourneerd voor query's die zich buiten het tijd venster van de gegevens in de opname bevinden.  Als de opname is gestart 7 uur geleden, maar de klant vraagt om beschik bare media van {UtcNow – 24 uur} tot UtcNow, retour neren we alleen de {UtcNow – 7} uur aan gegevens.

### <a name="response-format"></a>Antwoord indeling  

De availableMedia-aanroep retourneert een set tijd waarden.

Het antwoord is een JSON-hoofd tekst waarbij timeRanges waarden een matrix van ISO 8601-UTC-datums voor het jaar (in de jjjj-indeling), de maand (in de notatie JJJJ-MM) of dag (JJJJ-MM-DD) zijn, afhankelijk van de aangevraagde precisie.  De volledige timeRanges bevat een begin-en eind waarde die is opgemaakt als een ISO 8601 UTC-datum tijd (in JJJJ-MM-DDTuu: mm: SS. sssZ).

Voor de availableMedia-query is de API de sleutel van de video tijdlijn. Eventuele onderbrekingen in de tijd lijn worden weer gegeven als hiaten in het antwoord.

#### <a name="response-example-for-availablemedia"></a>Antwoord voorbeeld voor availableMedia

##### <a name="example-1-live-recording-with-no-gaps"></a>Voor beeld 1: Live-opname zonder hiaten

Hier volgt een antwoord waarin alle beschik bare media voor 21 december 2019 worden weer gegeven, waarbij de opname 100% is voltooid. Het antwoord heeft slechts één begin-en eind paar.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Voor beeld 2: Live-opname met een ruimte tussen twee seconden

De camera kan om een of andere reden geen geldige video verzenden voor een interval van 2 seconden op een dag. Hier volgt een antwoord waarin alle beschik bare media voor 21 december 2019 worden weer gegeven:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Voor beeld 3: Live-opname met een hiaat van 8 uur

Stel dat de camera en/of de on-premises faciliteit gedurende de dag een periode van 8 uur kwijt zijn, van 4 tot 12 uur UTC en dat er geen back-upvoedingser is. Hier volgt een antwoord waarin alle beschik bare media voor een dergelijke dag worden weer gegeven

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Voor beeld 4: Live-opname waarbij geen video wordt geregistreerd over zomer-en winter dagen

Stel dat u video alleen hebt opgenomen als de school zich in de sessie bevond en de opname werd gestopt vanaf 17 juni tot en met 6 september. Een query voor beschik bare maanden ziet er als volgt uit:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Als u vervolgens in juni de beschik bare dagen hebt gevraagd, ziet u het volgende:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Voor beeld 5: Live-opname waarbij geen video wordt opgenomen in het weekend of op feest dagen

Stel dat u video alleen tijdens kantoor uren hebt opgenomen. Een query voor beschik bare dagen ziet er als volgt uit

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Filters voor tijds bereik

Zoals hierboven vermeld, kunt u met deze filters delen van uw opname selecteren (bijvoorbeeld van 9:00 tot 11:00 op nieuwe jaren) voor afspelen. Wanneer streaming via HLS, ziet de streaming-URL eruit `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Als u een deel van de opname wilt selecteren, voegt u een para meter startTime en endTime toe, zoals: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . De tijds bereik filters zijn dus URL-wijzigingen die worden gebruikt om het deel van de tijd lijn van de opname te beschrijven die is opgenomen in het streaming-manifest:

* `starttime` is een ISO 8601 DateTime-stempel waarmee de gewenste begin tijd van de video tijdlijn in het geretourneerde manifest wordt beschreven.
* `endtime` is een ISO 8601 DateTime-stempel waarmee de gewenste eind tijd van de video tijdlijn wordt beschreven die in het manifest wordt geretourneerd.

De maximale lengte (in tijd) van een dergelijk manifest mag niet langer zijn dan 24 uur.

Hier volgen de beperkingen voor de filters.

|startTime| endTime |Resultaat|
|---|---|---|
|Aanwezig |Aanwezig |Retourneert het meest recente gedeelte van de video in het activum, tot een maximum lengte van vier uur.<br/><br/>Als de Asset niet is geschreven naar (er zijn geen nieuwe video gegevens beschikbaar in), wordt een VoD-manifest (on-demand) geretourneerd. Anders wordt een live-manifest geretourneerd.|
|Ontbreekt|Aanwezig|    Een manifest retour neren met een wille keurige video die nieuwer is dan startTime, als een dergelijk manifest korter dan 24 uur zou zijn.<br/>Als de lengte van het manifest langer is dan 24 uur, wordt een fout geretourneerd.<br/>Als de Asset niet is geschreven naar (er zijn geen nieuwe video gegevens beschikbaar in), wordt een VoD-manifest (on-demand) geretourneerd. Anders wordt een live-manifest geretourneerd.
|Aanwezig|Ontbreekt |Fout: als startTime aanwezig is, is endTime verplicht.|
|Ontbreekt|Ontbreekt|Een VoD-manifest retour neren met een wille keurige video die beschikbaar is tussen startTime en endTime.<br/>De reeks (startTime, endTime) mag niet langer zijn dan 24 uur.|

### <a name="response-examples"></a>Antwoord voorbeelden  

#### <a name="example-1-live-recording-with-no-gaps"></a>Voor beeld 1: Live-opname zonder hiaten

Hier volgt een antwoord waarin alle beschik bare media voor 21 december 2019 worden weer gegeven, waarbij de opname 100% is voltooid. Het antwoord heeft slechts één begin-en eind paar.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Wanneer de opname doorlopend is, kunt u HLS of streep manifesten aanvragen voor een wille keurig deel van de tijd binnen die begin-en eind paar, op voor waarde dat de duur 24 uur of minder is. Een voor beeld van een HLS-manifest aanvraag van vier uur voor de bovenstaande zou zijn:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Voor beeld 2: Live-opname met een ruimte tussen twee seconden

De camera kan om een of andere reden geen geldige video verzenden voor een interval van 2 seconden op een dag. Hier volgt een antwoord met de beschik bare media voor 21 december 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Met een record zoals hierboven, kunt u de HLS-en DASH-manifesten ook aanvragen met een startTime/endTime-paar, zolang de reeks minder dan 24 uur is. Als deze waarden straddled de lege ruimte op 04:01:08AM UTC, zou het geretourneerde manifest de onderbreking in de media tijdlijn aangeven, volgens de relevante specificaties voor deze protocollen.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Voor beeld 3: Live-opname met een ruimte van 8 uur

Stel dat de camera en/of de on-premises faciliteit gedurende de dag een periode van 8 uur kwijt zijn, van 4 tot 12 uur UTC en dat er geen back-upvoedingser is. Hier volgt een antwoord waarin alle beschik bare media voor een dergelijke dag worden weer gegeven.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Met een dergelijke registratie:

* Als u een manifest aanvraagt waarbij de filters voor startTime/endTime Range zich in de ' beschik bare ' onderdelen van de tijd lijn bevinden, te weten van middernacht tot 4 A.M. of 12:00 tot middernacht, retourneert de service een manifest dat de tijd van startTime tot endTime bestrijkt, zoals:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Als u een manifest aanvraagt waarbij startTime en endTime zich in het midden bevonden, zegt u van 8 A.M. tot 10AM UTC, werkt de service op dezelfde manier alsof een activum filter zou resulteren in een leeg resultaat.

    [Dit is een aanvraag waarbij een leeg antwoord wordt ontvangen] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Als u een manifest aanvraagt waarbij slechts één van de startTime of endTime binnen het gat ligt, zou het geretourneerde manifest alleen een gedeelte van die time span bevatten. De waarde startTime of endTime wordt op de dichtstbijzijnde geldige grens uitgelijnd. Als u bijvoorbeeld hebt gevraagd om een 3-HR-stroom van 10AM naar 1PM, zou het antwoord 1-uur media bevatten gedurende 12 12:00 uur tot 1PM

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    Het geretourneerde manifest begint bij 2019-12-21T12:00:00.000 Z, ook al is de aanvraag voor het starten van 10AM gevraagd. Wanneer u een video beheersysteem met een invoeg toepassing voor plug-in bouwt, moet u ervoor zorgen dat dit wordt gesignaleerd aan de viewer. Een niet-compatibele Viewer zou worden verward met de reden waarom de Speel tijdlijn begint om 12:00 uur en niet 10AM zoals aangevraagd

## <a name="recording-and-playback-latencies"></a>Latentie van vastleggen en afspelen

Wanneer u live video Analytics gebruikt op IoT Edge om te registreren bij een Asset, geeft u een segmentLength-eigenschap op waarmee de module de minimale duur van de video (in seconden) kan samen voegen voordat deze in de Cloud wordt vastgelegd. Als segmentLength bijvoorbeeld is ingesteld op 300, wordt in de module 5 minuten aan video verzameld vóór het uploaden van 1 5 minuten ' chunk ', waarna u de volgende vijf minuten kunt teruggaan naar de accumulatie modus. Het verhogen van de segmentLength heeft het voor deel van het verlagen van uw Azure Storage transactie kosten, omdat het aantal lees-en schrijf bewerkingen niet vaker dan eenmaal per segmentLength seconden zal duren.

Als gevolg hiervan wordt het streamen van de video van Media Services minstens zoveel tijd vertraagd. 

Een andere factor die de afspeel latentie bepaalt (de vertraging tussen de tijd dat een gebeurtenis vóór de camera plaatsvindt, naar de tijd dat deze op een afspeel apparaat kan worden weer gegeven) is de [GOP terug](https://en.wikipedia.org/wiki/Group_of_pictures) duur van de groep van foto's. Als [u de vertraging van live streams vermindert door drie eenvoudige technieken te gebruiken](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) , wordt de duur van de GOP terug langer. Het is gebruikelijk dat IP-camera's worden gebruikt in bewakings-en beveiligings scenario's die zijn geconfigureerd om GOPs langer dan 30 seconden te gebruiken. Dit heeft een grote invloed op de totale latentie.

## <a name="next-steps"></a>Volgende stappen

[Zelf studie voor doorlopende video-opname](continuous-video-recording-tutorial.md)
