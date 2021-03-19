---
title: Wijziging van het Smooth Streaming Protocol (MS-SSTR) voor HEVC-Azure
description: Deze specificatie beschrijft het protocol en de indeling voor gefragmenteerde op MP4 gebaseerde live streamen met HEVC in Azure Media Services. Alleen de wijzigingen die nodig zijn om HEVC te leveren, worden in dit artikel vermeld, met uitzonde ring van ' (geen wijziging) ' geeft aan dat tekst alleen ter verduidelijking wordt gekopieerd.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: 6454bc863cb5fd628d581fff380c5ab61354f762
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87053048"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Wijziging van het Smooth Streaming Protocol (MS-SSTR) voor HEVC 

## <a name="1-introduction"></a>1 Inleiding 

In dit artikel vindt u gedetailleerde wijzigingen die moeten worden toegepast op de Smooth Streaming Protocol specificatie [MS-SSTR] om Smooth Streaming van met HEVC gecodeerde video in te scha kelen. In deze specificatie worden alleen de wijzigingen beschreven die nodig zijn om de HEVC-videocodec te leveren. Het artikel volgt hetzelfde nummer schema als de specificatie [MS-SSTR]. De lege nieuwskoppen die in het hele artikel worden gepresenteerd, worden verstrekt om de lezer naar hun positie te richten in de specificatie [MS-SSTR].  ' (Niet wijzigen) ' geeft aan dat tekst alleen ter informatie wordt gekopieerd.

Het artikel bevat technische implementatie vereisten voor de Signa lering van de HEVC-videocodec (met behulp van ' hev1 ' of ' hvc1-indelings trajecten ') in een Smooth Streaming-manifest en normatieve verwijzingen worden bijgewerkt om te verwijzen naar de huidige MPEG-standaarden die HEVC, Common Encryption van HEVC en Box-names voor de ISO-basis media bestands indeling zijn bijgewerkt zodat deze consistent zijn met de meest recente 

De Smooth Streaming Protocol specificatie [MS-SSTR] waarnaar wordt verwezen, beschrijft de telegram indeling die wordt gebruikt voor het leveren van Live en on-demand digitale media, zoals audio en video, in de volgende omstandigheden: van een encoder naar een webserver, van een server naar een andere server en van een server naar een HTTP-client.
Door het gebruik van een op MPEG-4 ([[MPEG4-Ra])](https://go.microsoft.com/fwlink/?LinkId=327787)gebaseerde levering van gegevens structuren via http kan naadloze switching bijna in realtime worden uitgevoerd tussen verschillende kwaliteits niveaus van gecomprimeerde media-inhoud. Het resultaat is een constante afspeel ervaring voor de eind gebruiker van de HTTP-client, zelfs als de weer gave van netwerk-en video weergave voor de client computer of het apparaat is gewijzigd.

## <a name="11-glossary"></a>1,1 woorden lijst 

De volgende termen worden gedefinieerd in *[MS-Glos]*:

>   **Globally Unique Identifier (GUID) Universally Unique Identifier (UUID)**

De volgende termen zijn specifiek voor dit document:

>  **samenstellings tijd:** Het tijdstip waarop een steek proef wordt weer gegeven op de client, zoals gedefinieerd in   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: common Encryption, zoals gedefinieerd in [ISO/IEC 23001-7] Tweede editie.
> 
>   **decoderings tijd:** Het tijdstip waarop een steek proef moet worden gedecodeerd op de client, zoals gedefinieerd in   [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** Een afzonderlijk Download bare **Media** -eenheid met een of meer voor **beelden**.

>   **HEVC:** Video codering met hoge efficiëntie, zoals gedefinieerd in [ISO/IEC 23008-2]
> 
>   **manifest:** Meta gegevens over de **presentatie** waarmee een client aanvragen voor **Media** kan uitvoeren. **Media:** Gecomprimeerde audio-, video-en tekst gegevens die door de client worden gebruikt om een **presentatie** af te spelen. **Media-indeling:** Een goed gedefinieerde indeling voor het weer geven van audio of video als een gecomprimeerd voor **beeld**.
> 
>   **presentatie:** De set van alle **streams** en gerelateerde meta gegevens die nodig zijn om één film af te spelen. **aanvraag:** Een HTTP-bericht dat van de client naar de server wordt verzonden, zoals gedefinieerd in [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **Response:** een HTTP-bericht dat van de server naar de client is verzonden, zoals gedefinieerd in [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   voor **Beeld:** De kleinste basis eenheid (zoals een frame) waarin **Media** worden opgeslagen en verwerkt.
> 
>   **mag,** moet dit niet, moet: Deze voor waarden (in hoofd letters) worden gebruikt zoals beschreven in   [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) alle instructies voor het gebruik van een optioneel gedrag, of dat kan.

## <a name="12-references"></a>1,2 verwijzingen

>   Verwijzingen naar documentatie over micro soft open-specificaties bevatten geen publicatie jaar omdat koppelingen naar de meest recente versie van de documenten zijn die regel matig worden bijgewerkt. Verwijzingen naar andere documenten bevatten een publicatie jaar, wanneer er een beschikbaar is.

### <a name="121-normative-references"></a>1.2.1 normatieve verwijzingen 

>  [MS-SSTR] Smooth Streaming Protocol *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)
> 
>   [ISO/IEC 14496-12] Internationale Organisatie voor Standaardisatie, ' informatie technologie--code ring van audiovisuele objecten--deel 12: ISO-basis media bestands indeling ', ISO/IEC 14496-12:2014, Edition 4, plus corrigendum 1, wijzigingen 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Internationale Organisatie voor Standaardisatie, ' informatie technologie: code ring van audiovisuele objecten--deel 15: Trans Port van de NAL-video in de ISO-basis media bestands indeling ', ISO 14496-15:2015, editie 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Informatie technologie: hoge efficiëntie code ring en media levering in heterogene omgevingen--deel 2: video codering met hoge efficiëntie: 2013 of nieuwste versie   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Informatie technologie — MPEG Systems technologieën — deel 7: algemene versleuteling in ISO base Media File Format-bestanden, CENC Edition 2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, ' codecs ' en ' Profiles ' para meters voor ' Bucket-media typen ' <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] De MP4-registratie-instantie, ' MP4REG ', [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "sleutel woorden voor gebruik in Rfc's voor het aanduiden van vereisten niveaus", BCP 14, RFC 2119, maart 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informatieve verwijzingen 

>   [MS-GLOS] Micro soft Corporation, '*verklarende woorden lijst voor Windows-protocols*'.
> 
>   [RFC3548] Josefsson, S., ed., "de Base16-, Base32-en base64-gegevens codering", RFC 3548, juli 2003, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., ed. en Overell, P., "uitgebreid BNF voor de syntaxis specificaties: ABNF", STD 68, RFC 5234, januari 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>overzicht van 1,3 

>   Hieronder worden alleen wijzigingen in de Smooth Streaming opgegeven die vereist zijn voor het leveren van HEVC. Ongewijzigd sectie headers worden weer gegeven om de locatie in de Smooth Streaming specificatie [MS-SSTR] waarnaar wordt verwezen, te onderhouden.

## <a name="14-relationship-to-other-protocols"></a>1,4-relatie met andere protocollen 

## <a name="15-prerequisitespreconditions"></a>1,5 vereisten/voor waarden 

## <a name="16-applicability-statement"></a>1,6 toepas baarheid, instructie 

## <a name="17-versioning-and-capability-negotiation"></a>1,7-versie beheer en mogelijkheden onderhandeling 

## <a name="18-vendor-extensible-fields"></a>1,8 leverancier-uitbreid bare velden 

>   De volgende methode wordt gebruikt voor het identificeren van streams met behulp van de HEVC-video-indeling:
> 
>   * **Aangepaste beschrijvende codes voor media-indelingen:** Deze functie wordt bepaald door het **FourCC** -veld, zoals beschreven in sectie *2.2.2.5*.
>   Implementeerers kunnen ervoor zorgen dat extensies niet conflicteren door extensie codes te registreren bij de MPEG4-RA, zoals opgegeven in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1,9-standaard toewijzingen 

## <a name="2-messages"></a>2 berichten 

## <a name="21-transport"></a>2,1-Trans Port

## <a name="22-message-syntax"></a>Syntaxis van 2,2-berichten 

### <a name="221-manifest-request"></a>2.2.1 manifest-aanvraag 

### <a name="222-manifest-response"></a>2.2.2 manifest reactie 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variabele):** De secundaire versie van het antwoord bericht van het manifest. MOET worden ingesteld op 2. (Geen wijziging)
> 
>   **Tijd schaal (variabele):** De tijd schaal van het kenmerk duration, opgegeven als het aantal stappen in één seconde. De standaardwaarde is 
> 1. (Geen wijziging)
> 
>    De aanbevolen waarde is 90000 voor het weer geven van de exacte duur van video frames en fragmenten die de video met breuken per frame bevatten (bijvoorbeeld 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

Het ProtectionElement moet aanwezig zijn als Common Encryption (CENC) is toegepast op video-of audio-streams. HEVC versleutelde streams moeten overeenkomen met Common Encryption 2e editie [ISO/IEC 23001-7]. Alleen segment gegevens in VCL NAL-eenheden moeten worden versleuteld.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variabele):** De tijd schaal voor de duur-en tijd waarden in deze stroom, opgegeven als het aantal stappen in één seconde. Een waarde van 90000 wordt aanbevolen voor HEVC-streams. Een waarde die overeenkomt met de voor beeld-Golf frequentie (bijvoorbeeld 48000 of 44100) wordt aanbevolen voor audio stromen.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variabele):** Een code van vier tekens die aangeeft welke media-indeling voor elk voor beeld wordt gebruikt. Het volgende waarden bereik is gereserveerd met de volgende semantische betekenissen:
> 
> * "hev1": video-voor beelden voor dit traject maken gebruik van HEVC-video, met behulp van de voorbeeld indeling ' hev1 ' die is opgegeven in [ISO/IEC-14496-15].
>
> * "hvc1": video-voor beelden voor dit traject maken gebruik van HEVC-video, met behulp van de voorbeeld indeling ' hvc1 ' die is opgegeven in [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variabele):** Gegevens die para meters opgeven die specifiek zijn voor de media-indeling en die gemeen schappelijk zijn voor alle voor beelden in de track, weer gegeven als een teken reeks met hexadecimale code bytes. De indeling en semantische betekenis van de byte reeks varieert als volgt met de waarde van het veld **FourCC** :
> 
>   * Wanneer een TrackElement de HEVC-video beschrijft, is het veld **FourCC** gelijk aan **"hev1"** of **"hvc1"**
> 
>   Het veld **CodecPrivateData** bevat een hexadecimale teken reeks representatie van de volgende byte reeks, opgegeven in ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (geen wijziging van MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField bevat de Sequence para meter set (SPS).
> 
>   * PPSField bevat de para meter set van het segment (PPS).
> 
>   Opmerking: de para meter voor de video parameters (VPS) bevindt zich niet in CodecPrivateData, maar moet in de bestands header van opgeslagen bestanden in het vak ' hvcC ' staan. Systemen die gebruikmaken van Smooth Streaming Protocol moeten aanvullende para meters voor het decoderen (bijvoorbeeld HEVC-laag) Signa leren met het aangepaste kenmerk ' codecs '.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Het veld **MajorVersion van de SMOOTHSTREAMINGMEDIA** moet zijn ingesteld op 2 en het veld **MinorVersion** moet zijn ingesteld op 2. (Geen wijziging)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 fragment aanvraag 

>   **Opmerking**: de standaard media-indeling die is aangevraagd voor **MinorVersion** 2 en ' hev1 ' of ' hvc1 ' is ' Iso8 ' brand ISO base-basis media bestands indeling die is opgegeven in [ISO/IEC 14496-12] ISO base Media File Format vierde editie en [ISO/IEC 23001-7] common Encryption tweede editie.

### <a name="224-fragment-response"></a>2.2.4 fragment antwoord 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   De **TfxdBox** is afgeschaft en de functie wordt vervangen door het vak gedecodeerd fragment (' tfdt ') dat is opgegeven in de sectie [ISO/IEC 14496-12] 8.8.12.
> 
>   **Opmerking**: een client kan de duur van een fragment berekenen door de tijds duur van de steek proef die wordt vermeld in het vak uitvoering bijhouden (' Trun ') te totaliseren of door het aantal steek proeven te vermenigvuldigen met de standaard duur van de steek proef. De baseMediaDecodeTime in ' tfdt ' plus fragment duur is gelijk aan de URL-tijd parameter voor het volgende fragment.
> 
>   Een referentie tijd voor een producent (' prft ') moet worden ingevoegd vóór een film fragment (' moof '), indien nodig, om de UTC-tijd aan te geven die overeenkomt met de decodeer tijd van het tracerings fragment van de eerste steek proef waarnaar wordt verwezen door het vak film fragment, zoals opgegeven in de sectie [ISO/IEC 14496-12] 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   De **TfrfBox** is afgeschaft en de functie wordt vervangen door het vak gedecodeerd fragment (' tfdt ') dat is opgegeven in de sectie [ISO/IEC 14496-12] 8.8.12.
> 
>   **Opmerking**: een client kan de duur van een fragment berekenen door de tijds duur van de steek proef die wordt vermeld in het vak uitvoering bijhouden (' Trun ') te totaliseren of door het aantal steek proeven te vermenigvuldigen met de standaard duur van de steek proef. De baseMediaDecodeTime in ' tfdt ' plus fragment duur is gelijk aan de URL-tijd parameter voor het volgende fragment. Doorgifte adressen zijn afgeschaft omdat ze live streamen vertragen.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   In de velden **TfhdBox** en gerelateerde worden standaard waarden voor de meta gegevens van de voor beelden in het fragment ingekapseld. De syntaxis van het veld **TfhdBox** is een strikte subset van de syntaxis van het vak fragment koptekst bijhouden dat is gedefinieerd in de sectie [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7.
> 
>   **BaseDataOffset (8 bytes):** De offset, in bytes, vanaf het begin van het veld **MdatBox** naar het voorbeeld veld in het veld **MdatBox** . Als u deze beperking wilt aangeven, moet de standaard-Base-moof-vlag (0x020000) worden ingesteld.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   De **TrunBox** en gerelateerde velden bevatten een voor beeld van meta gegevens voor het aangevraagde fragment. De syntaxis van **TrunBox** is een strikte subset van het selectie vakje fragment uitvoering van versie 1 bijhouden dat is gedefinieerd in de sectie [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bytes):** De verschuiving van het voor beeld van de compositie tijd van elk voor beeld is aangepast, zodat de presentatie tijd van de eerste gepresenteerde steek proef in het fragment gelijk is aan de decoderings tijd van het eerste gedecodeerde voor beeld. Er worden negatieve verschuivingen van de samen stelling van de video monsters gebruikt,
> 
>   zoals gedefinieerd in   [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Opmerking: hiermee voor komt u dat er een video synchronisatie fout wordt veroorzaakt door video die geluid vertoont die gelijk is aan de grootste gedecodeerde vertraging bij het verwijderen van de afbeeldings buffer en wordt er een presentatie tijd in rekening gebracht tussen alternatieve fragmenten die verschillende verwijderings vertragingen kunnen hebben.
> 
>   De syntaxis van de velden die in deze sectie zijn gedefinieerd, opgegeven in ABNF   [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) blijft hetzelfde, met uitzonde ring van de volgende opties:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>Algemene velden van 2.2.4.9-fragment reactie 

### <a name="225-sparse-stream-pointer"></a>2.2.5 sparse stream-aanwijzer 

### <a name="226-fragment-not-yet-available"></a>het 2.2.6-fragment is nog niet beschikbaar 

### <a name="227-live-ingest"></a>Live-opname 2.2.7 

#### <a name="2271-filetype"></a>2.2.7.1 bestands type 

>   **Bestands type (variabele):** Hiermee geeft u het subtype en beoogd gebruik op van het MPEG-4 ([[MPEG4-Ra])-](https://go.microsoft.com/fwlink/?LinkId=327787) bestand en de kenmerken op hoog niveau.
> 
>   **MajorBrand (variabele):** Het belangrijkste merk van het Media bestand. MOET worden ingesteld op ' isml '.
> 
>   **MinorVersion (variabele):** De secundaire versie van het Media bestand. MOET worden ingesteld op 1.
> 
>   **CompatibleBrands (variabele):** Hiermee geeft u de ondersteunde merken van MPEG-4 op.
>   MOET ' ccff ' en ' iso8 ' bevatten.
> 
>   De syntaxis van de velden die in deze sectie zijn gedefinieerd, opgegeven in ABNF   [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) is als volgt:

```properties
FileType = MajorBrand MinorVersion CompatibleBrands
MajorBrand = STRING_UINT32
MinorVersion = STRING_UINT32
CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)
```

**Opmerking**: de compatibiliteits Brands ' ccff ' en ' iso8 ' geven aan dat fragmenten overeenkomen met ' common container File Format ' en common Encryption [ISO/IEC 23001-7] en ISO base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5-fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 track fragment uitgebreide koptekst 

### <a name="228-server-to-server-ingest"></a>server-naar-server-opname 2.2.8 

## <a name="3-protocol-details"></a>3-protocol Details 


## <a name="31-client-details"></a>3,1-client Details 

### <a name="311-abstract-data-model"></a>3.1.1 abstract gegevens model 

#### <a name="3111-presentation-description"></a>Beschrijving van 3.1.1.1-presentatie 

>   Het gegevens element van de presentatie beschrijving omvat alle meta gegevens voor de presentatie.
> 
>   Meta gegevens van Presentation: een set meta gegevens die gemeen schappelijk is voor alle streams in de presentatie. Meta gegevens van de presentatie omvatten de volgende velden, zoals beschreven in sectie *2.2.2.1*:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **Lijnen**
> * **Duur**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Presentaties met HEVC-stromen moeten worden ingesteld op:

```properties
MajorVersion = 2
MinorVersion = 2
```

>   LookaheadCount = 0 (Opmerking: vakken afgeschaft)
> 
>   Presentaties moeten ook worden ingesteld:

```properties
TimeScale = 90000
```

>   Stream-verzameling: een verzameling gegevens elementen van stream description, zoals beschreven in sectie *3.1.1.1.2*.
> 
>   Beschrijving van beveiliging: een verzameling gegevens elementen van meta gegevens van een beveiligings systeem, zoals beschreven in sectie *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>Beschrijving van meta gegevens van het 3.1.1.1.1 Protection-systeem 

>   Het gegevens element van de meta gegevens beschrijving van het beveiligings systeem encapsulateert meta gegevens die specifiek zijn voor een enkel Content Protection systeem. (Geen wijziging)
> 
>   Beschrijving van de beveiligings header: meta gegevens van inhouds beveiliging die betrekking hebben op een enkel Content Protection systeem. De beschrijving van de beveiligings header bestaat uit de volgende velden, zoals beschreven in sectie *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>Beschrijving van de 3.1.1.1.2-stroom 

###### <a name="311121-track-description"></a>Beschrijving van 3.1.1.1.2.1-track 

###### <a name="3111211-custom-attribute-description"></a>Beschrijving van aangepast 3.1.1.1.2.1.1-kenmerk 

##### <a name="3113-fragment-reference-description"></a>Beschrijving van 3.1.1.3-fragment verwijzing 

###### <a name="31131-track-specific-fragment-reference-description"></a>Beschrijving van de 3.1.1.3.1-Track-Specific fragment 

#### <a name="3112-fragment-description"></a>Beschrijving van 3.1.1.2-fragment 

##### <a name="31121-sample-description"></a>Beschrijving van 3.1.1.2.1-voor beeld 

### <a name="312-timers"></a>3.1.2 timers 

### <a name="313-initialization"></a>3.1.3-initialisatie 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Higher-Layer geactiveerde gebeurtenissen 

#### <a name="3141-open-presentation"></a>3.1.4.1 open presentatie 

#### <a name="3142-get-fragment"></a>Fragment ophalen 3.1.4.2 

#### <a name="3143-close-presentation"></a>Presentatie 3.1.4.3 sluiten 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 verwerkings gebeurtenissen en Sequentiëren 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1-manifest aanvraag en reactie op Manifest 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 fragment aanvraag en fragment reactie

## <a name="32-server-details"></a>3,2-Server Details

## <a name="33-live-encoder-details"></a>Details van de Live Encoder van 3,3 

## <a name="4-protocol-examples"></a>4 protocol-voor beelden 

## <a name="5-security"></a>5 beveiliging 

## <a name="51-security-considerations-for-implementers"></a>5,1 beveiligings overwegingen voor implementaties

>   Als de inhoud die wordt getransporteerd met dit protocol een hoge commerciële waarde heeft, moet een Content Protection systeem worden gebruikt om ongeoorloofd gebruik van de inhoud te voor komen. De **ProtectionElement** kan worden gebruikt voor het gebruiken van meta gegevens met betrekking tot het gebruik van een Content Protection-systeem. Beveiligde audio-en video-inhoud moeten worden versleuteld zoals is opgegeven door MPEG Common Encryption tweede editie: 2015 [ISO/IEC 23001-7].
> 
>   **Opmerking**: voor HEVC video worden alleen segment gegevens in VCL NALs versleuteld. Segment koppen en andere NALs zijn toegankelijk voor presentatie toepassingen voordat ze worden ontsleuteld. in een beveiligd video pad zijn versleutelde gegevens niet beschikbaar voor presentatie toepassingen.

## <a name="52-index-of-security-parameters"></a>5,2 index van beveiligings parameters 


| **Beveiligings parameter**  | **Sectie**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryption vakken | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5,3 Common Encryption vakken

De volgende vakken kunnen aanwezig zijn in fragment reacties wanneer Common Encryption wordt toegepast en zijn opgegeven in [ISO/IEC 23001-7] of [ISO/IEC 14496-12]:

1.  Vak beveiligings systeem specifiek koptekst (' pssh ')

2.  Voor beeld van een versleutelings doos (' senc ')

3.  Voor beeld van een offset-vak met hulp gegevens (' saio ')

4.  Voor beeld van het vak voor de grootte van de hulp gegevens (' Saiz ')

5.  Vak Beschrijving van de voorbeeld groep (' sgpd ')

6.  Voor beeld van groeps vak (' sbgp ')

---

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
