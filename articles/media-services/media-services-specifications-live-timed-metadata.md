---
title: Getimede meta gegevens Azure Media Services signalering in live streamen
description: Deze specificatie bevat een overzicht van methoden voor het Signa leren van getimede meta gegevens bij het opnemen en streamen naar Azure Media Services. Dit omvat ondersteuning voor algemene, getimede meta gegevens signalen (ID3), en SCTE-35-Signa lering voor AD-invoeging en splice-voor waarden.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336391"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Getimede meta gegevens in live streamen signalering 

Laatst bijgewerkt: 2019-08-22

### <a name="conformance-notation"></a>Conformiteit-notatie

De sleutel woorden ' moet ', ' mag niet ', ' vereist ', ' moet ', ' mag niet ', ' moet ', ' mogen ', ' is ', ' mag niet ', ' wordt aanbevolen ', ' mei ' en ' optioneel ' in dit document moeten worden geïnterpreteerd zoals beschreven in RFC 2119

## <a name="1-introduction"></a>1. Inleiding 

Om het invoegen van advertenties of aangepaste meta gegevens gebeurtenissen op een client speler te Signa leren, maken broadcasters vaak gebruik van getimede meta gegevens die zijn Inge sloten in de video. Media Services biedt ondersteuning voor het Trans Port van getimede meta gegevens van het opname punt van het live streaming-kanaal naar de client toepassing om deze scenario's in te scha kelen.
Deze specificatie bevat een overzicht van verschillende modi die worden ondersteund door Media Services voor getimede meta gegevens binnen live streaming-signalen.

1. [SCTE-35] Signa lering die voldoet aan de standaarden die worden beschreven door [SCTE-35], [SCTE-214-1], [SCTE-214-3] en [RFC8216]

2. [SCTE-35] Signa lering die voldoet aan de verouderde specificatie [Adobe-Primetime] voor RTMP AD-Signa lering.
   
3. Een algemene seingevings modus voor meta gegevens, voor berichten die **niet** [SCTE-35] zijn en die [id3v2] of andere aangepaste schema's kunnen bevatten die zijn gedefinieerd door de ontwikkelaar van de toepassing.

## <a name="11-terms-used"></a>1,1 gebruikte termen

| Termijn                | Definitie                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AD-afbreek punt            | Een locatie of tijdstip waarop een of meer advertenties mogen worden gepland voor levering; hetzelfde als beschik bare verkoop kansen.                                                                                                                     |
| AD-besluit service | externe service die bepaalt welke AD ('s) en de duur van de gebruiker worden weer gegeven. De services worden doorgaans door een partner verschaft en zijn buiten het bereik van dit document.                                                                    |
| WMS                 | Indicatie van de tijd en para meters van de aanstaande AD-afbreek periode. Houd er rekening mee dat hints kunnen wijzen op een overschakeling van een AD-eind punt, in afwachting van de overstap naar de volgende AD binnen een AD-out en wachtende switch van een AD-afbreek bewerking naar de hoofd inhoud.           |
| Packager            | De Azure Media Services ' streaming-eind punt ' biedt dynamische verpakkings mogelijkheden voor DASH en HLS en wordt ' packager ' genoemd in de media branche.                                                                              |
| Presentatie tijd   | De tijd dat een gebeurtenis wordt weer gegeven aan een viewer. De tijd vertegenwoordigt het tijdstip op de media tijdlijn dat een viewer de gebeurtenis zou zien. Zo is de presentatie tijd van een SCTE-35 splice_info ()-opdracht bericht de splice_time (). |
| Aankomst tijd        | Het tijdstip waarop een gebeurtenis bericht binnenkomt. De tijd is doorgaans verschillend van de presentatie tijd van de gebeurtenis, omdat gebeurtenis berichten worden verzonden vóór de presentatie tijd van de gebeurtenis.                                                    |
| Sparse track        | Media spoor die niet doorlopend is en tijd is gesynchroniseerd met een bovenliggend of controle spoor.                                                                                                                                                  |
| Oorsprong              | De Azure media streaming-service                                                                                                                                                                                                             |
| Kanaal filter        | De Azure Media Live streaming-service                                                                                                                                                                                                        |
| HLS                 | Apple HTTP Live Streaming-Protocol                                                                                                                                                                                                            |
| LIGGENDE                | Dynamisch adaptief streamen via HTTP                                                                                                                                                                                                          |
| Werken              | Smooth Streaming-Protocol                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG 2-transport stromen                                                                                                                                                                                                                      |
| RTMP                | Multi Media-Protocol Real-Time                                                                                                                                                                                                                 |
| uimsbf              | Niet-ondertekend geheel getal, meest significante bit eerst.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1,2 normatieve verwijzingen

De volgende documenten bevatten voorzieningen, die door de verwijzing in deze tekst de bepalingen van dit document vormen. Alle documenten zijn onderworpen aan de herziening door de standaard instellingen en lezers worden aangemoedigd om de mogelijkheid van de meest recente versies van de hieronder vermelde documenten te onderzoeken. Er wordt ook aandacht besteed aan het feit dat nieuwere versies van de documenten waarnaar wordt verwezen, mogelijk niet compatibel zijn met deze versie van de getimede meta gegevens specificatie voor Azure Media Services.


| Standard          | Definitie                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital-invoeg signalerings specificatie 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Naslag informatie voor FLASH action script-taal](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Actie bericht indeling AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [STREEPJE-IF-ONTDUBBELINGS IOP]     | Richt 4,2 lijnen voor samen werking met DASH Industry-Forum [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Getimede meta gegevens voor HTTP Live Streaming- [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Getimede meta gegevens in de common media-toepassings indeling (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3-code versie 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: deel 12 ISO-basis media bestands indeling, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Informatie technologie--dynamisch adaptief streamen via HTTP (DASH)--deel 1: beschrijving van media presentatie en segment indelingen. Mei 2014. Gemaakt. URL https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Informatie technologie: multi media-toepassings indeling (MPEG-A)--deel 19: common media toepassings indeling (CMAF) voor gesegmenteerde media. Januari 2018. Gemaakt. URL https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Informatie technologie--MPEG-systeem technologieën--deel 7: algemene versleuteling in ISO-basis bestanden voor media bestands indeling. Februari 2016. Gemaakt. URL https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Micro soft Smooth Streaming-Protocol", 15 mei 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-opname]  | [Azure Media Services gefragmenteerde MP4 Live opname-specificatie](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | N. Pantos, ed.; W. mei. HTTP Live Streaming. 2017 augustus. Informatief. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | De Base16-, Base32-en base64-gegevens codering- [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Het Real-Time Messa ging-Protocol van Adobe", december 21, 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019-cueing-bericht van Digital-invoeg toepassing voor kabel- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH voor IP-Based-kabel Services deel 1: MPD-beperkingen en-extensies                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG-DASH voor IP-Based-kabel Services deel 3: streepje/FF-profiel                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – gebeurtenis planning en meldings interface                                                                                                                                                  |
| [SCTE-250]        | API voor gebeurtenis-en signalerings beheer (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. time-opname van meta gegevens

Azure Media Services ondersteunt realtime in-band meta gegevens voor zowel [RTMP] als Smooth Streaming [MS-SSTR-opname]-protocollen. Realtime meta gegevens kunnen worden gebruikt voor het definiëren van aangepaste gebeurtenissen, met uw eigen unieke aangepaste schema's (JSON, binary en XML), evenals door de bedrijfstak gedefinieerde indelingen als ID3, of SCTE-35 voor AD-Signa lering in een broadcast-stroom. 

In dit artikel vindt u informatie over het verzenden van aangepaste, getimede meta gegevens signalen met de ondersteunde opname protocollen van Azure Media Services. In dit artikel wordt ook uitgelegd hoe de manifesten voor HLS, DASH en Smooth Streaming worden gedecoreerd met de getimede meta gegevens signalen, en hoe deze in-band worden geplaatst wanneer de inhoud wordt geleverd met CMAF (MP4-fragmenten) of transport stroom (TS)-segmenten voor HLS. 

Veelvoorkomende use-case scenario's voor tijdgebonden meta gegevens zijn:

 - SCTE-35 AD-signalen voor het activeren van AD-onderbrekingen in een live event of lineaire uitzending
 - Aangepaste ID3-meta gegevens die gebeurtenissen kunnen activeren in een client toepassing (browser, iOS of Android)
 - Aangepaste gedefinieerde JSON-, binaire of XML-meta gegevens voor het activeren van gebeurtenissen in een client toepassing
 - Telemetrie van een live coderings programma, IP-camera of drone
 - Gebeurtenissen van een IP-camera, zoals beweging, gezichts detectie, enzovoort.
 - Informatie over geografische posities van een actie camera, Drone of het verplaatsen van een apparaat
 - Song teksten
 - Begrenzingen van Program ma's op een lineaire live-feed
 - Afbeeldingen of uitgebreide meta gegevens die moeten worden weer gegeven in een live-feed
 - Sport scores of game Clock-informatie
 - Interactieve reclame pakketten die naast de video worden weer gegeven in de browser
 - Quizzen of polls
  
Azure Media Services Live-gebeurtenissen en packager kunnen deze getimede meta gegevens signalen ontvangen en converteren naar een stroom met meta gegevens die client toepassingen kunnen bereiken met behulp van op standaarden gebaseerde protocollen zoals HLS en DASH.


## <a name="21-rtmp-timed-metadata"></a>2,1 RTMP getimede meta gegevens

Met het protocol [RTMP] kunnen getimede meta gegevens signalen worden verzonden voor verschillende scenario's, waaronder aangepaste meta gegevens en SCTE-35-AD-signalen. 

Reclame signalen (Hint berichten) worden verzonden als [AMF0]-Hint berichten die zijn Inge sloten in de [RTMP]-stream. De hint berichten kunnen ergens worden verzonden voordat de werkelijke gebeurtenis of het AD-Splice-signaal van [SCTE35] moet worden uitgevoerd. Ter ondersteuning van dit scenario wordt de werkelijke tijds tempel van de presentatie van de gebeurtenis verzonden binnen het bericht hint. Zie [AMF0] voor meer informatie.

De volgende [AMF0]-opdrachten worden ondersteund door Azure Media Services voor RTMP-opname:

- **onUserDataEvent** : wordt gebruikt voor aangepaste meta gegevens of [id3v2] getimede meta gegevens
- **onAdCue** : wordt hoofd zakelijk gebruikt voor het Signa leren van een opportuniteit voor het plaatsen van advertenties in de live stream. Twee vormen van de hint worden ondersteund, een eenvoudige modus en de modus ' SCTE-35 '. 
- **onCuePoint** : wordt ondersteund door bepaalde on-premises hardware encoders, zoals het elementaire Live coderings programma, om berichten van [SCTE35] te Signa leren. 
  

In de volgende tabel wordt de indeling van de AMF-bericht lading beschreven die Media Services neemt voor zowel de bericht modus eenvoudig als [SCTE35].

De naam van het bericht [AMF0] kan worden gebruikt om meerdere gebeurtenis stromen van hetzelfde type te onderscheiden.  Voor zowel berichten van [SCTE-35] als de modus ' Simple ' moet de naam van het AMF-bericht ' onAdCue ' zijn, zoals vereist in de [Adobe-Primetime]-specificatie.  Alle velden die hieronder niet worden vermeld, worden genegeerd door Azure Media Services op opname.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP met aangepaste meta gegevens met behulp van ' onUserDataEvent '

Als u aangepaste meta gegevensfeeds van uw upstream-encoder, een IP-camera, Drone of apparaat met het RTMP-protocol wilt opgeven, gebruikt u het opdracht type ' onUserDataEvent ' [AMF0]-gegevens bericht.

De gegevens bericht opdracht **' onUserDataEvent '** moet een bericht lading met de volgende definitie bevatten om te kunnen worden vastgelegd door Media Services en zijn verpakt in de in-band bestands indeling, evenals de manifesten voor HLS, DASH en Smooth streaming.
Het is raadzaam om berichten met een time-out van de meta gegevens niet vaker dan één keer per 0,5 seconden (500ms) of stabiliteits problemen met de live stream te verzenden. Elk bericht kan meta gegevens van meerdere frames samen voegen als u meta gegevens op frame niveau moet bieden. Als u multi-bitrate streams verzendt, kunt u het beste ook de meta gegevens op één bitrate geven om de band breedte te verminderen en storingen met de verwerking van video/audio te voor komen. 

De payload voor **' onUserDataEvent '** moet een bericht van het type [MPEGDASH] EventStream XML-indeling zijn. Dit maakt het eenvoudig om aangepaste gedefinieerde schema's door te geven die kunnen worden uitgevoerd in ' emsg-nettoladingen in-band voor CMAF [MPEGCMAF] die worden geleverd via HLS-of streepje-protocollen. Elk streepje gebeurtenis stroom bericht bevat een schemeIdUri die fungeert als een URN-bericht schema-ID en definieert de payload van het bericht. Sommige schema's, zoals " https://aomedia.org/emsg/ID3 " voor [id3v2] of **urn: scte: scte35:2013: bin** voor [scte-35], worden gestandaardiseerd door industriële consortia voor interoperabiliteit. Elke toepassings provider kan hun eigen aangepaste schema definiëren met behulp van een URL die ze beheren (domein in eigendom) en kunnen een specificatie op die URL geven als ze kiezen. Als een speler een handler heeft voor het gedefinieerde schema, is dat het enige onderdeel dat de payload en het protocol moet begrijpen.

Het schema voor de EventStream XML-nettolading van [MPEG-DASH] is gedefinieerd als (fragment van het streepje ISO-IEC-23009-1-3e editie). Houd er rekening mee dat er op dit moment slechts één "Event type" per "EventStream" wordt ondersteund. Alleen het eerste **gebeurtenis** element wordt verwerkt als er meerdere gebeurtenissen worden gegeven in de **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Voor beeld van XML-gebeurtenis stroom met een ID3-schema-ID en nettolading met base64-gecodeerde gegevens.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Voorbeeld gebeurtenis stroom met aangepaste schema-ID en met base64 gecodeerde binaire gegevens  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Voor beeld van een gebeurtenis stroom met aangepaste schema-ID en aangepaste JSON  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Ingebouwde ondersteunde combi natie van schema-id's
| URI van schema-ID                 | Beschrijving                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https://aomedia.org/emsg/ID3 | Hierin wordt beschreven hoe [ID3v2] meta gegevens kunnen worden uitgevoerd als getimede meta gegevens in een CMAF [MPEGCMAF] gefragmenteerde MP4. Zie de [getimede meta gegevens in de indeling common Media Application (CMAF)](https://github.com/AOMediaCodec/id3-emsg) voor meer informatie. |

### <a name="event-processing-and-manifest-signaling"></a>Gebeurtenis verwerking en manifest signalen

Bij de ontvangst van een geldige **' onUserDataEvent** -gebeurtenis zoekt Azure Media Services naar een geldige XML-nettolading die overeenkomt met de EventStreamType (gedefinieerd in [MPEGDASH]), parseert u de XML-nettolading en converteert u deze naar een [MPEGCMAF] MP4 fragment ' emsg ' versie 1 box voor opslag in het archief van Live en verzen ding naar de Media Services packager.   De packager detecteert het vak ' emsg ' in de Live Stream en:

- (a) ' het pakket wordt dynamisch in TS-segmenten verpakt voor levering aan HLS-clients in overeenstemming met de HLS met de getimede meta gegevens specificatie [HLS-TMD], of
- (b) door sturen voor levering in CMAF-fragmenten via HLS of streepje, of 
- (c) Converteer deze naar een verspreid spoor signaal voor levering via Smooth Streaming [MS-SSTR].

Naast de in-band emsg Format-CMAF of TS PES-pakketten voor HLS bevat de manifests voor het streepje (MPD) en Smooth Streaming een verwijzing naar de in-band-gebeurtenis stromen (ook wel bekend als sparse stream track in Smooth Streaming).

Afzonderlijke gebeurtenissen of gegevens ladingen worden niet rechtstreeks uitgevoerd in de HLS-, DASH-of Smooth-manifesten. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Aanvullende informatieve beperkingen en standaard waarden voor onUserDataEvent-gebeurtenissen

- Als de tijd schaal niet is ingesteld in het EventStream-element, wordt de RTMP 1 kHz-tijd schaal standaard gebruikt
- De levering van een onUserDataEvent-bericht is beperkt tot één keer per 500ms. Als u gebeurtenissen vaker verzendt, kan dit van invloed zijn op de band breedte en de stabiliteit van de live-feed

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP AD Cue signalering met ' onAdCue '

Azure Media Services kunt verschillende bericht typen van [AMF0] Luis teren en erop reageren die kunnen worden gebruikt om verschillende realtime gesynchroniseerde meta gegevens in de live stream te Signa leren.  De specificatie [Adobe-Primetime] definieert twee Cue-typen met de naam ' eenvoudige ' en ' SCTE-35-modus. Voor de eenvoudige modus ondersteunt Media Services één AMF Cue-bericht met de naam ' onAdCue ' met een Payload die overeenkomt met de onderstaande tabel die is gedefinieerd voor het signaal ' eenvoudige modus '.  

De volgende sectie bevat de RTMP-' eenvoudige ' modus ' payload, die kan worden gebruikt om een Basic ' spliceOut '-ad-signaal te Signa leren dat wordt uitgevoerd naar het client manifest voor HLS, DASH en micro soft Smooth Streaming. Dit is zeer nuttig voor scenario's waarbij de klant geen complexe AD signalerings-of invoegings systeem op basis van SCTE-35 heeft en gebruikmaakt van een Basic on-premises encoder om via een API te verzenden in het bericht hint. De on-premises encoder ondersteunt meestal een op REST gebaseerde API om dit signaal te activeren. Dit geeft ook de video stroom ' splice-condition ' door een IDR-frame in de video in te voegen en een nieuwe GOP terug te starten.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP AD Cue signalering met ' onAdCue '-eenvoudige modus

| Veldnaam | Veldtype | Vereist? | Beschreven                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | Tekenreeks     | Vereist  | Het gebeurtenis bericht.  Is ' SpliceOut ' om een eenvoudige modus-Splice aan te wijzen.                                                                                                                                                                                                         |
| id         | Tekenreeks     | Vereist  | Een unieke id waarmee de Splice of het segment wordt beschreven. Identificeert dit exemplaar van het bericht                                                                                                                                                                                       |
| duur   | Getal     | Vereist  | De duur van de splice. Eenheden zijn fractie seconden.                                                                                                                                                                                                                           |
| elapsed    | Getal     | Optioneel  | Wanneer het signaal wordt herhaald om afstemming in te ondersteunen, is dit veld de hoeveelheid presentatie tijd die is verstreken sinds de gestarte splice. Eenheden zijn fractie seconden. Wanneer u de eenvoudige modus gebruikt, mag deze waarde de oorspronkelijke duur van de Splice niet overschrijden. |
| tijd       | Getal     | Vereist  | Is de tijd van de splices, in de presentatie tijd. Eenheden zijn fractie seconden.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Voor beeld van een MPEG DASH-manifest uitvoer bij het gebruik van de eenvoudige modus van Adobe RTMP

Zie voor beeld [3.3.2.1 MPEG Dash. mpd EventStream met behulp van de eenvoudige modus van Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Bekijk een voor beeld [van een 3.3.3.1-streepje-manifest met één periode en Adobe-eenvoudige modus ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Voor beeld van HLS-manifest uitvoer bij gebruik van Adobe RTMP eenvoudige modus

Zie voor beeld [3.2.2 HLS-manifest met de Adobe-eenvoudige modus en Ext-X-Cue-tag](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP AD Cue signalering met ' onAdCue '-SCTE-35-modus

Wanneer u werkt met een meer geavanceerde productie werk stroom voor broadcasten waarvoor het volledige SCTE-Payload 35-bericht moet worden uitgevoerd naar het HLS of het streep-manifest, is het het beste om de ' SCTE-35-modus ' van de specificatie [Adobe-Primetime] te gebruiken.  Deze modus ondersteunt in-band SCTE-35-signalen die rechtstreeks naar een on-premises Live Encoder worden verzonden, waarna de signalen worden gecodeerd in de RTMP-stroom met behulp van de ' SCTE-35-modus ' die is opgegeven in de [Adobe-Primetime]-specificatie. 

Normaal gesp roken SCTE-35-berichten kunnen alleen voor komen in MPEG-2-transport stroom (TS) op een on-premises encoder. Neem contact op met de fabrikant van uw coderings programma voor meer informatie over het configureren van een Trans Port stream-opname die SCTE-35 bevat en schakel deze in voor Pass-Through naar RTMP in de Adobe SCTE-35-modus.

In dit scenario moet de volgende Payload worden verzonden vanuit het on-premises coderings programma met behulp van het bericht type **' onAdCue '** [AMF0].

| Veldnaam | Veldtype | Vereist? | Beschreven                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WMS        | Tekenreeks     | Vereist  | Het gebeurtenis bericht.  Voor [SCTE-35] berichten moet dit de met base64 gecodeerde [RFC4648] binaire splice_info_section () zijn om berichten te verzenden naar HLS-, Smooth-en dash-clients.                                                                                                                                                                                                                               |
| type       | Tekenreeks     | Vereist  | Een URN of URL die het bericht schema aangeeft. Voor [SCTE-35] berichten **moet** dit **' scte35 '** zijn om berichten te verzenden naar HLS-, Smooth-en dash-clients, in overeenstemming met [Adobe-Primetime]. De URN: scte: scte35:2013: bin kan eventueel ook worden gebruikt om een bericht [SCTE-35] te Signa leren.                                                                                                        |
| id         | Tekenreeks     | Vereist  | Een unieke id waarmee de Splice of het segment wordt beschreven. Identificeert dit exemplaar van het bericht.  Berichten met een gelijkwaardige semantiek moeten dezelfde waarde hebben.                                                                                                                                                                                                                                                       |
| duur   | Getal     | Vereist  | De duur van de gebeurtenis of het AD-segment, indien bekend. Als dit onbekend is, **moet** de waarde 0 zijn.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Getal     | Optioneel  | Wanneer het AD-signaal [SCTE-35] wordt herhaald om in te stellen, is dit veld de hoeveelheid presentatie tijd die is verstreken sinds de gestarte splice. Eenheden zijn fractie seconden. In de modus [SCTE-35] kan deze waarde groter zijn dan de oorspronkelijke opgegeven duur van de Splice of het segment.                                                                                                                   |
| tijd       | Getal     | Vereist  | De presentatie tijd van de gebeurtenis of AD-splice.  De tijd en duur van de presentatie **moeten** worden uitgelijnd met Stream Access points (SAP) van het type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I. Voor uitgaand HLS **moeten** tijd en duur worden uitgelijnd met de grenzen van het segment. De presentatie tijd en duur van verschillende gebeurtenis berichten binnen dezelfde gebeurtenis stroom mogen elkaar niet overlappen. Eenheden zijn fractie seconden. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Voor beeld van HLS manifest. M3U8 met het SCTE-35-modus signaal
Zie de [sectie 3.2.1.1 voor beeld HLS-manifest met SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP AD signalering met ' onCuePoint ' voor elementaire Live

De elementaire Live on-premises encoder ondersteunt advertentie markeringen in het RTMP-signaal. Azure Media Services ondersteunt momenteel alleen het AD-markerings type ' onCuePoint ' voor RTMP.  Deze optie kan worden ingeschakeld in de instellingen van de Adobe RTMP-groep in de instellingen of API van de Live-coderings programma voor media, door de '**ad_markers**' in te stellen op ' onCuePoint '.  Raadpleeg de documentatie over elementaire Live voor meer informatie. Als u deze functie inschakelt in de RTMP-groep, worden SCTE-35-signalen door gegeven aan de Adobe RTMP-uitvoer die moeten worden verwerkt door Azure Media Services.

Het bericht type ' onCuePoint ' is gedefinieerd in [Adobe-Flash-AS] en heeft de volgende Payload-structuur wanneer deze wordt verzonden vanuit de elementaire Live RTMP-uitvoer.


| Eigenschap   | Beschrijving                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| naam       | De naam moet '**scte35**' zijn door elementair Live.                                                                                                                                                                              |
| tijd       | De tijd in seconden waarop het actie punt tijdens de tijd lijn is opgetreden in het video bestand                                                                                                                                           |
| type       | Het type actie punt moet worden ingesteld op**gebeurtenis**.                                                                                                                                                                             |
| parameters | Een associatieve matrix met combi natie van naam/waarde-paren die de informatie uit het SCTE-35-bericht bevat, inclusief id en duur. Deze waarden worden geparseerd door Azure Media Services en opgenomen in de label manifest decoratie. |


Wanneer deze modus van AD-markering wordt gebruikt, is de HLS-manifest uitvoer vergelijkbaar met de eenvoudige modus van Adobe.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Voor beeld van MPEG-streep-MPD, enkelvoudige periode, Adobe-eenvoudige modus signalen

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Voor beeld van een HLS-afspeel lijst, Adobe simple mode-signalen met een EXT-X-CUE-tag (afgekapt...) voor beknoptheid)

In het volgende voor beeld ziet u de uitvoer van de Media Services Dynamic packager voor een RTMP-opname stroom met behulp van Adobe ' eenvoudige ' modus signalen en de verouderde [Adobe-Primetime] EXT-X-CUE-tag.  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>2.1.6 annuleren en updates

Berichten kunnen worden geannuleerd of bijgewerkt door meerdere berichten te verzenden met dezelfde presentatie tijd en ID. De presentatie tijd en-ID identificeren de gebeurtenis uniek en het laatste bericht dat is ontvangen voor een specifieke presentatie tijd die voldoet aan de vooraf ingestelde beperkingen is het bericht dat wordt verwerkt. De bijgewerkte gebeurtenis vervangt alle eerder ontvangen berichten. De vooraf-rollen beperking is vier seconden. Berichten die ten minste vier seconden vóór de presentatie tijd zijn ontvangen, worden op de gewenste actie uitgevoerd.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 gefragmenteerde MP4-opname (Smooth Streaming)

Raadpleeg [MS-SSTR-opname] voor vereisten voor Live Stream-opname. De volgende secties bevatten informatie over de opname van meta gegevens van de time-out voor presentaties.  De time-outmetagegevens van de presentatie worden opgenomen als een sparse track, die is gedefinieerd in het manifest van de live-server (Zie MS-SSTR) en het vak Movie (' Moov ').  

Elk sparse fragment bestaat uit een film fragment box (' moof ') en media Data Box (' mdat '), waarbij het vak ' mdat ' het binaire bericht is.

Het coderings programma moet het fragment splitsen op het moment van de presentatie waar de hint moet worden ingevoegd om een frame nauw keurige invoeging van advertenties te krijgen.  Er moet een nieuw fragment worden gemaakt dat begint met een nieuw gemaakt IDR frame of met SAP (stream Access Points) van het type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I.
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>Dialoog venster 2.2.1 Live server

De sparse track **moet** worden gedeclareerd in het manifest van de live-server met een **\<textstream\>** vermelding en **moet** de volgende kenmerken hebben ingesteld:

| **Kenmerknaam** | **Veld type** | **Vereist?** | **Beschrijving**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Getal         | Vereist      | **Moet** ' 0 ' zijn, wat een track met een onbekende variabele bitrate aangeeft.                                                                                                                                                          |
| parentTrackName    | Tekenreeks         | Vereist      | **Moet** de naam zijn van het bovenliggende spoor, waarbij de tijd codes van de sparse track worden uitgelijnd. Het bovenliggende spoor kan geen sparse track zijn.                                                                             |
| manifestOutput     | Booleaans        | Vereist      | **Moet** ' True ' zijn, om aan te geven dat de sparse track wordt Inge sloten in het Smooth client-manifest.                                                                                                                        |
| Subtype            | Tekenreeks         | Vereist      | **Moet** de vier teken code ' data ' zijn.                                                                                                                                                                                  |
| Schema             | Tekenreeks         | Vereist      | **Moet** een urn of URL zijn die het bericht schema aangeeft. Voor [SCTE-35] berichten **moet** dit ' urn: SCTE: scte35:2013: bin ' zijn om berichten te verzenden naar HLS-, Smooth-en dash-clients in overeenstemming met [SCTE-35]. |
| nummer bijhouden          | Tekenreeks         | Vereist      | **Moet** de naam van de sparse track zijn. De tracknaam kan worden gebruikt om meerdere gebeurtenis stromen te onderscheiden met hetzelfde schema. Elke unieke gebeurtenis stroom **moet** een unieke Track naam hebben.                                |
| lijnen          | Getal         | Optioneel      | **Moet** de tijd schaal van het bovenliggende spoor zijn.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 movie box

Het vak film (' Moov ') volgt het manifest van Live server als onderdeel van de stream-header voor een sparse track.

Het vak ' Moov ' **moet** een **TrackHeaderBox (' tkhd ')** bevatten zoals gedefinieerd in [ISO-14496-12] met de volgende beperkingen:

| **Veld naam** | **Veld type**          | **Vereist?** | **Beschrijving**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duur       | 64-bits geheel getal zonder teken | Vereist      | **Moet** 0 zijn, omdat het vakje track nul voor beelden heeft en de totale duur van de voor beelden in het vak bijhouden 0 is. |

---

Het vak ' Moov ' **moet** een **HandlerBox (' hdlr ')** bevatten, zoals gedefinieerd in [ISO-14496-12] met de volgende beperkingen:

| **Veld naam** | **Veld type**          | **Vereist?** | **Beschrijving**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-bits geheel getal zonder teken | Vereist      | **Moet** ' meta ' zijn. |

---

Het vak ' stsd ' **moet** een MetaDataSampleEntry bevatten met een coderings naam zoals gedefinieerd in [ISO-14496-12].  Bijvoorbeeld: voor SCTE-35 berichten **moet** de naam van de code ' SCTE ' zijn.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 film fragment en media Data Box

Sparse track fragmenten bestaan uit een film fragment box (' moof ') en een media Data Box (' mdat ').

> [!NOTE]
> Het coderings programma moet het fragment splitsen op het moment van de presentatie waar de hint moet worden ingevoegd om een frame nauw keurige invoeging van advertenties te krijgen.  Er moet een nieuw fragment worden gemaakt dat begint met een nieuw gemaakt IDR frame of met SAP (stream Access Points) van het type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I
> 

Het vak MovieFragmentBox (' moof ') **moet** een **TrackFragmentExtendedHeaderBox (' uuid ')** bevatten, zoals gedefinieerd in [MS-SSTR] met de volgende velden:

| **Veld naam**         | **Veld type**          | **Vereist?** | **Beschrijving**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-bits geheel getal zonder teken | Vereist      | **Moet** de aankomst tijd van de gebeurtenis zijn. Deze waarde Lijnt het bericht uit met het bovenliggende nummer.           |
| fragment_duration      | 64-bits geheel getal zonder teken | Vereist      | **Moet** de duur van de gebeurtenis zijn. De duur kan nul zijn om aan te geven dat de duur onbekend is. |

---


Het MediaDataBox-vak (' mdat ') **moet** de volgende indeling hebben:

| **Veld naam**          | **Veld type**                   | **Vereist?** | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versie                 | 32-bits geheel getal zonder teken (uimsbf) | Vereist      | Bepaalt de indeling van de inhoud van het vak ' mdat '. Niet-herkende versies worden genegeerd. Momenteel is de enige ondersteunde versie 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bits geheel getal zonder teken (uimsbf) | Vereist      | Identificeert dit exemplaar van het bericht. Berichten met een gelijkwaardige semantiek moeten dezelfde waarde hebben; dat wil zeggen dat het verwerken van elk gebeurtenis bericht met dezelfde id voldoende is.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bits geheel getal zonder teken (uimsbf) | Vereist      | De som van de fragment_absolute_time, opgegeven in de TrackFragmentExtendedHeaderBox, en de presentation_time_delta **moet** de presentatie tijd van de gebeurtenis zijn. De tijd en duur van de presentatie **moeten** worden uitgelijnd met Stream Access points (SAP) van het type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I. Voor uitgaand HLS **moeten** tijd en duur worden uitgelijnd met de grenzen van het segment. De presentatie tijd en duur van verschillende gebeurtenis berichten binnen dezelfde gebeurtenis stroom **mogen** elkaar niet overlappen. |
| message                 | bytematrix                       | Vereist      | Het gebeurtenis bericht. Voor [SCTE-35] berichten is het bericht het binaire splice_info_section (). Voor [SCTE-35] berichten **moet** dit de splice_info_section () zijn om berichten te verzenden naar HLS-, Smooth-en dash-clients in overeenstemming met [SCTE-35]. Voor [SCTE-35] berichten is de binaire splice_info_section () de payload van het vak ' mdat ' en is het **niet** base64-gecodeerd.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 annuleringen en updates

Berichten kunnen worden geannuleerd of bijgewerkt door meerdere berichten te verzenden met dezelfde presentatie tijd en ID.  De presentatie tijd en-ID identificeren de gebeurtenis uniek. Het laatste bericht dat is ontvangen voor een specifieke presentatie tijd die voldoet aan de voor waarden voor de roll, is het bericht dat wordt verwerkt. Het bijgewerkte bericht vervangt alle eerder ontvangen berichten.  De vooraf-rollen beperking is vier seconden. Berichten die ten minste vier seconden vóór de presentatie tijd zijn ontvangen, worden op de gewenste actie uitgevoerd. 


## <a name="3-timed-metadata-delivery"></a>3 getimede levering van meta gegevens

De gegevens van de gebeurtenis stroom zijn dekkend om te Media Services. Media Services geeft slechts drie stukjes informatie door tussen het opname-en eind punt van de client. De volgende eigenschappen worden aan de client geleverd, in overeenstemming met [SCTE-35] en/of het streaming-protocol van de client:

1.  Schema: een URN of URL die het schema van het bericht aangeeft.
2.  Presentatie tijd: de presentatie tijd van de gebeurtenis op de media tijdlijn.
3.  Duur: de duur van de gebeurtenis.
4.  ID: een optionele unieke id voor de gebeurtenis.
5.  Bericht: de gegevens van de gebeurtenis.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 micro soft Smooth Streaming-manifest  

Raadpleeg de verwerking van sparse track [MS-SSTR] voor meer informatie over het format teren van een sparse-berichten spoor. Voor [SCTE35]-berichten voert Smooth Streaming de met base64 gecodeerde splice_info_section () uit naar een sparse fragment.
De StreamIndex **moet** een subtype van ' data ' hebben en de CustomAttributes **moet** een kenmerk met name = "schema" en value = "urn: scte: scte35:2013: bin" bevatten.

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Glad client manifest voorbeeld met base64-gecodeerde [SCTE35] splice_info_section ()
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>3,2 Apple HLS-manifest decoratie

Azure Media Services ondersteunt de volgende HLS-manifest Tags voor de Signa lering van AD beschik bare gegevens tijdens een live-of on-demand-gebeurtenis. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- EXT-X-CUE zoals gedefinieerd in [Adobe-Primetime]
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

De gegevens uitvoer naar elke tag kan variëren op basis van de gebruikte opname signaal modus. Bijvoorbeeld, RTMP-opname met de eenvoudige modus van Adobe bevat niet de volledige SCTE-35-nettolading met base64-code ring.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1-voor beeld HLS-manifest. M3U8 met EXT-X-CUE-Signa lering van SCTE-35

In het volgende voor beeld wordt HLS van de manifest uitvoer van de Media Services Dynamic packager bevat een EXT-X-CUE-tag voor [Adobe-Primetime] in de modus SCTE35. 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS met Adobe Primetime EXT-X-CUE

Media Services (versie 2 en 3 API) ondersteunt de uitvoer van de EXT-X-CUE-tag zoals gedefinieerd in [Adobe-Primetime] "SCTE-35-modus". In deze modus wordt Azure Media Services met base64 gecodeerde [SCTE-35] splice_info_section () Inge sloten in de tag EXT-X-CUE.  

De "verouderde" EXT-X-CUE-tag wordt hieronder gedefinieerd en er kan ook normatieve worden verwezen in de [Adobe-Primetime]-specificatie. Dit mag alleen worden gebruikt voor verouderde SCTE35-Signa lering wanneer dat nodig is, anders wordt de aanbevolen tag gedefinieerd in [RFC8216] als EXT-X-DATERANGE. 

| **Kenmerknaam** | **Type**                      | **Vereist?**                             | **Beschrijving**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WMS                | teken reeks tussen aanhalings tekens                 | Vereist                                  | Het bericht dat is gecodeerd als een base64-gecodeerde teken reeks, zoals beschreven in [RFC4648]. Voor [SCTE-35] berichten is dit de met base64 gecodeerde splice_info_section ().                                                                                                                                      |
| TYPE               | teken reeks tussen aanhalings tekens                 | Vereist                                  | Een URN of URL die het bericht schema aangeeft. Voor [SCTE-35]-berichten neemt het type de speciale waarde ' scte35 '.                                                                                                                                                                          |
| Id                 | teken reeks tussen aanhalings tekens                 | Vereist                                  | Een unieke id voor de gebeurtenis. Als de ID niet is opgegeven wanneer het bericht wordt opgenomen, wordt door Azure Media Services een unieke id gegenereerd.                                                                                                                                              |
| DURATION           | decimaal getal met drijvende komma | Vereist                                  | De duur van de gebeurtenis. Als dit onbekend is, **moet** de waarde 0 zijn. Eenheden zijn factional seconden.                                                                                                                                                                                           |
| VERSTREKEN            | decimaal getal met drijvende komma | Optioneel, maar vereist voor sliding window | Wanneer het signaal wordt herhaald ter ondersteuning van een schuif presentatie venster, **moet** dit veld de hoeveelheid presentatie tijd zijn die is verstreken sinds de gebeurtenis is gestart. Eenheden zijn fractie seconden. Deze waarde kan groter zijn dan de oorspronkelijke opgegeven duur van de Splice of het segment. |
| TIME               | decimaal getal met drijvende komma | Vereist                                  | De presentatie tijd van de gebeurtenis. Eenheden zijn fractie seconden.                                                                                                                                                                                                                        |

In de toepassingslaag van de HLS-speler wordt het TYPE gebruikt om de indeling van het bericht te identificeren, het bericht te decoderen, de benodigde tijds conversies toe te passen en de gebeurtenis te verwerken.  De gebeurtenissen worden gesynchroniseerd in de segment-afspeel lijst van het bovenliggende spoor, op basis van de tijds tempel van de gebeurtenis.  Ze worden vóór het dichtstbijzijnde segment (#EXTINF tag) ingevoegd.


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>voor beeld van een HLS. M3U8-manifest in 3.2.3 met Adobe Primetime EXT-X-CUE

In het volgende voor beeld ziet u de HLS-manifest decoratie met behulp van de tag Adobe Primetime EXT-X-CUE.  De para meter ' CUE ' bevat alleen de eigenschappen TYPE en duration, wat betekent dat dit een RTMP-bron is met behulp van het ' eenvoudige ' modus signaal van Adobe.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>3.2.4 HLS-bericht verwerking voor Adobe Primetime EXT-X-CUE

Gebeurtenissen worden gesignaleerd in de segment-afspeel lijst van elk video-en audio spoor. De positie van de EXT-X-CUE-tag **moet** altijd direct vóór het eerste HLS-segment (voor het starten of het sluiten van het segment) of direct na het laatste HLS-segment (voor Splice in of segment einde) zijn, zoals is vereist door [Adobe-Primetime].

Wanneer een verschuivende presentatie venster is ingeschakeld, **moet** de tag Ext-X-Cue vaak voldoende worden herhaald, waardoor de Splice of het segment altijd volledig wordt beschreven in de segment-afspeel lijst en het verstreken kenmerk **moet** worden gebruikt om aan te geven hoe lang de Splice of het segment actief is, zoals is vereist door [Adobe-Primetime].

Wanneer een verschuivende presentatie venster is ingeschakeld, worden de Tags EXT-X-CUE verwijderd uit de segment-afspeel lijst wanneer de media tijd waarnaar ze verwijzen, is uitgerold uit het venster voor de schuif weergave.

## <a name="33-dash-manifest-decoration-mpd"></a>3,3-streep manifest decoratie (MPD)

[MPEGDASH] biedt drie manieren om gebeurtenissen te Signa leren:

1.  Gebeurtenissen die in het MPD-EventStream worden gesignaleerd
2.  In-band gesignaleerde gebeurtenissen met behulp van het gebeurtenis bericht venster (' emsg ')
3.  Een combi natie van zowel 1 als 2

Gebeurtenissen die zijn gesignaleerd in de MPD EventStream zijn handig voor VOD streaming, omdat clients toegang hebben tot alle gebeurtenissen, onmiddellijk wanneer de MPD wordt gedownload. Het is ook handig voor SSAI-Signa lering, waarbij de downstream SSAI leverancier de signalen moet parseren vanuit het MPD-manifest en om AD-inhoud dynamisch in te voegen.  De oplossing ' in-band ' (' emsg ') is handig voor live streaming waarbij clients de MPD niet opnieuw hoeven te downloaden, of als er geen SSAI-manifest bewerking plaatsvindt tussen de client en de oorsprong. 

Azure Media Services standaard gedrag voor een streepje is om in het EventStream en in de band een signaal te geven met behulp van het gebeurtenis bericht box (' emsg ').

Cue-berichten die zijn opgenomen in [RTMP] of [MS-SSTR-opname], worden toegewezen aan streep gebeurtenissen, met behulp van de vakken in-band emsg en/of in-MPD EventStreams. 

In-band SCTE-35-Signa lering voor streepje volgt de definitie en vereisten die zijn gedefinieerd in [SCTE-214-3] en in [streepje-IF-ONTDUBBELINGS IOP] sectie 13.12.2 (' SCTE35 Events '). 

Voor het Trans Port in-band [SCTE-35] gebruikt het gebeurtenis bericht venster (' emsg ') de schemeId = "urn: SCTE: scte35:2013: bin". Voor de MPD-manifest decoratie maakt de EventStream schemeId gebruik van ' urn: scte: scte35:2014: XML + bin '.  Deze indeling is een XML-weer gave van de gebeurtenis, inclusief een binaire, met base64 gecodeerde uitvoer van het volledige SCTE-35-bericht dat bij opname is aangekomen. 

Normatieve referentie definities van het vervoer van [SCTE-35]-Hint berichten in het DASH zijn beschikbaar in [SCTE-214-1] SEC 6.7.4 (MPD) en [SCTE-214-3] SEC 7.3.2 (Trans Port van SCTE 35-Hint berichten).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG-streepje (MPD) EventStream-Signa lering

Manifest (MPD) van gebeurtenissen wordt in de MPD gesignaleerd met behulp van het EventStream-element, dat binnen het period-element wordt weer gegeven. De schemeId die wordt gebruikt is "urn: scte: scte35:2014: XML + bin".


> [!NOTE]
> Voor de boog doelen [SCTE-35] staat het gebruik toe van de sectie met base64-code ring in het element Signal. binary (in plaats van het element Signal. SpliceInfoSection) als alternatief voor het vervoer van een volledig geparseerd bericht van een hint.
> Azure Media Services maakt gebruik van deze ' XML + bin '-benadering voor Signa lering in het MPD-manifest.
> Dit is ook de aanbevolen methode die wordt gebruikt in de sectie [DASH-IF-ONTDUBBELINGS IOP]-Zie het gedeelte [' ad-invoeg gebeurtenis stromen ' van het streepje weer geven als ontdubbelings IOP richtlijn](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 


Het EventStream-element heeft de volgende kenmerken:

| **Kenmerknaam** | **Type**                | **Vereist?** | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | tekenreeks                  | Vereist      | Hiermee wordt het schema van het bericht aangegeven. Het schema wordt ingesteld op de waarde van het kenmerk schema in het vak Live server-manifest. De waarde **moet** een urn of URL zijn die het bericht schema aangeeft. De ondersteunde uitvoer schemeId moet "urn: scte: scte35:2014: XML + bin" per [SCTE-214-1] SEC 6.7.4 (MPD) zijn, aangezien de service op dit moment alleen de XML + bin ondersteunt voor het boog gebruik in de MPD. |
| waarde              | tekenreeks                  | Optioneel      | Een extra teken reeks waarde die door de eigen aren van het schema wordt gebruikt om de semantiek van het bericht aan te passen. Als u meerdere gebeurtenis stromen wilt onderscheiden met hetzelfde schema, **moet** de waarde worden ingesteld op de naam van de gebeurtenis stroom (Tracknaam voor [MS-SSTR-ingestie] of AMF-bericht naam voor een [RTMP]-opname).                                                                         |
| Tijdschaal          | 32-bits geheel getal zonder teken | Vereist      | De tijd schaal, in ticks per seconde.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2-voor beeld van gebeurtenis stromen voor MPEG-DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1-voorbeeld MPEG DASH. mpd-manifestie van RTMP-streaming met de eenvoudige modus van Adobe

In het volgende voor beeld ziet u een fragment EventStream van de Media Services Dynamic packager voor een RTMP-stroom met behulp van de eenvoudige modus-Signa lering van Adobe.

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>voor beeld van een 3.3.2.2 MPEG DASH. mpd-manifest van een RTMP-stroom met Adobe SCTE-35-modus

In het volgende voor beeld ziet u een fragment EventStream van de Media Services Dynamic packager voor een RTMP-stroom met behulp van Adobe SCTE-35-modus signalering.

Voor beeld van een EventStream-element met behulp van XML + bin-notatie signalering per [SCTE-214-1]

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> Houd er rekening mee dat presentationTime de presentatie tijd van de gebeurtenis [SCTE-35] is die relatief is ten opzichte van de begin tijd van de periode, niet de aankomst tijd van het bericht.
> [MPEGDASH] definieert de Event@presentationTime as "bepaalt de tijd van de presentatie van de gebeurtenis ten opzichte van het begin van de periode.
> De waarde van de presentatie tijd in seconden is de deling van de waarde van dit kenmerk en de waarde van het EventStream@timescale kenmerk.
> Als deze niet aanwezig is, is de waarde van de tijd van de presentatie 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1-voorbeeld MPEG DASH-manifest (MPD) met één punt, EventStream, met behulp van eenvoudige Adobe-modus signalen

In het volgende voor beeld ziet u de uitvoer van de Media Services Dynamic packager voor een bron-RTMP-stroom met behulp van de Adobe ' Simple ' modus AD-signaal methode. De uitvoer is een enkelvoudig periode manifest met een EventStream met behulp van de schemeId-URI ingesteld op "urn: com: Adobe: dpi: simple: 2015" en de eigenschap value ingesteld op "simplesignal".
Elk eenvoudig signaal wordt gegeven in een gebeurtenis element met de @presentationTime Eigenschappen, en die zijn @duration @id gevuld op basis van de binnenkomende eenvoudige signalen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG-DASH in-band gebeurtenis bericht Box signalering

Voor een in-band-gebeurtenis stroom moet de mpd een InbandEventStream-element hebben op het niveau van de aanpassings.  Dit element heeft een verplicht schemeIdUri-kenmerk en een optioneel tijdschaal kenmerk dat ook wordt weer gegeven in het gebeurtenis bericht venster (' emsg ').  Gebeurtenis bericht vakken met schema-id's die niet in de MPD zijn gedefinieerd, **mogen** niet aanwezig zijn.

Voor het Trans Port in-band [SCTE-35] **moeten** signalen de schemeId = "urn: SCTE: scte35:2013: bin" gebruiken.
Normatieve definities van het vervoer van [SCTE-35] in-band berichten worden gedefinieerd in [SCTE-214-3] SEC 7.3.2 (Trans Port van SCTE 35 Cue-berichten).

In de volgende details worden de specifieke waarden beschreven die de client in overeenstemming met [SCTE-214-3] verwacht in het ' emsg ':

| **Veld naam**          | **Veld type**          | **Vereist?** | **Beschrijving**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | tekenreeks                  | Vereist      | Hiermee wordt het schema van het bericht aangegeven. Het schema wordt ingesteld op de waarde van het kenmerk schema in het vak Live server-manifest. De waarde **moet** een urn zijn waarmee het bericht schema wordt aangeduid. Voor [SCTE-35] berichten **moet** dit ' urn: SCTE: scte35:2013: bin ' zijn in overeenstemming met [SCTE-214-3]          |
| Waarde                   | tekenreeks                  | Vereist      | Een extra teken reeks waarde die door de eigen aren van het schema wordt gebruikt om de semantiek van het bericht aan te passen. Als u meerdere gebeurtenis stromen wilt onderscheiden met hetzelfde schema, wordt de waarde ingesteld op de naam van de gebeurtenis stroom (Tracknaam voor Smooth-opname of AMF-bericht naam voor RTMP-opname). |
| Tijdschaal               | 32-bits geheel getal zonder teken | Vereist      | De tijd schaal, in maten per seconde, van de velden tijden en duur in het vak ' emsg '.                                                                                                                                                                                                            |
| Presentation_time_delta | 32-bits geheel getal zonder teken | Vereist      | De tijd van de media presentatie verschilt van de presentatie tijd van de gebeurtenis en de eerste presentatie tijd in dit segment. De tijd en duur van de presentatie **moeten** worden uitgelijnd met Stream Access points (SAP) van het type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I.                                  |
| event_duration          | 32-bits geheel getal zonder teken | Vereist      | De duur van de gebeurtenis, of 0xFFFFFFFF, om een onbekende duur aan te geven.                                                                                                                                                                                                                              |
| Id                      | 32-bits geheel getal zonder teken | Vereist      | Identificeert dit exemplaar van het bericht. Berichten met een gelijkwaardige semantiek moeten dezelfde waarde hebben. Als de ID niet is opgegeven wanneer het bericht wordt opgenomen, wordt door Azure Media Services een unieke id gegenereerd.                                                                                        |
| Message_data            | bytematrix              | Vereist      | Het gebeurtenis bericht. Voor [SCTE-35] berichten is de bericht gegevens de binaire splice_info_section () in overeenstemming met [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Voor beeld van een InBandEvenStream-entiteit voor de eenvoudige modus van Adobe
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>Verwerking van 3.3.5-streepje-berichten

Gebeurtenissen worden in het vak ' emsg ' in een gesignaleerd bereik weer gegeven voor video-en audio-tracks.  Het signaal wordt weer gegeven voor alle segment aanvragen waarvoor de presentation_time_delta 15 seconden of minder is. 

Wanneer een verschuivende presentatie venster is ingeschakeld, worden gebeurtenis berichten uit de MPD verwijderd wanneer de som van de tijd en duur van het gebeurtenis bericht kleiner is dan de tijd van de media gegevens in het manifest.  Met andere woorden, de gebeurtenis berichten worden uit het manifest verwijderd wanneer de media tijd waarnaar ze verwijzen, uit het schuif venster wordt Gerolt.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 opname richtlijnen voor encoder leveranciers

De volgende richt lijnen zijn veelvoorkomende problemen die van invloed kunnen zijn op de implementatie van de leverancier van het coderings programma van deze specificatie.  De onderstaande richt lijnen zijn verzameld op basis van de feedback van de echte wereld partner, zodat het eenvoudiger is om deze specificatie voor anderen te implementeren. 

[SCTE-35] berichten worden opgenomen in binaire indeling met het schema **"urn: SCTE: scte35:2013: bin"** voor [MS-SSTR-opname] en het type **"scte35"** voor [RTMP] opnemen. Voor het vergemakkelijken van de conversie van de timing [SCTE-35], die is gebaseerd op de presentatie tijds tempels (PTS) voor MPEG-2-transport stromen, wordt een toewijzing tussen PTS (pts_time + pts_adjustment van de splice_time ()) en de media tijdlijn verschaft door de gebeurtenis presentatie tijd (het fragment_absolute_time veld voor het vloeiend opnemen en het tijd veld voor RTMP-opname). De toewijzing is nood zakelijk omdat de 33-bits waarde PTS ongeveer elke 26,5 uur rolt.

Smooth Streaming opname [MS-SSTR-ingestie] vereist dat de media Data Box (' mdat ') de **splice_info_section ()** **moet** bevatten die is gedefinieerd in [SCTE-35]. 

Voor RTMP-opname wordt het kenmerk Cue van het AMF-bericht ingesteld op de met base64 gecodeerde **splice_info_section ()** gedefinieerd in [SCTE-35].  


Wanneer de berichten de indeling hebben die hierboven is beschreven, worden ze verzonden naar HLS-, Smooth-en DASH-clients zoals hierboven is gedefinieerd.  

Wanneer u uw implementatie met het Azure Media Services-platform test, moet u eerst testen met een ' Pass-through ' LiveEvent voordat u doorgaat met het testen van een coderings LiveEvent.

---

## <a name="change-history"></a>Wijzigingsoverzicht

| Date     | Wijzigingen                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Herziene ondersteuning voor RTMP-opname, toegevoegd RTMP "onCuePoint" voor elementaire Live                                            |
| 08/22/19 | Bijgewerkt om OnUserDataEvent toe te voegen aan RTMP voor aangepaste meta gegevens                                                          |
| 1/08/20  | Er is een fout opgetreden in de RTMP-eenvoudige en RTMP-SCTE35 modus. Gewijzigd van ' onCuePoint ' in ' onAdCue '. De tabel eenvoudige modus is bijgewerkt. |
| 08/4/20  | Ondersteuning voor DATERANGE-label is verwijderd om overeen te komen met de implementatie in de productie service.    |

## <a name="next-steps"></a>Volgende stappen
Media Services leer trajecten weer geven.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
