---
title: Meta gegevens schema voor invoer Azure Media Services | Microsoft Docs
description: Dit artikel bevat een overzicht van Azure Media Services schema voor de invoer van meta gegevens.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017406"
---
# <a name="input-metadata"></a>Invoer van meta gegevens

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Een coderings taak is gekoppeld aan een invoer element (of activa) waarvoor u bepaalde coderings taken wilt uitvoeren.  Wanneer een taak is voltooid, wordt een uitvoer activum geproduceerd.  Het uitvoer element bevat video, audio, miniaturen, manifest, enzovoort. Het uitvoer activum bevat ook een bestand met meta gegevens over de invoer Asset. De naam van het XML-bestand met meta gegevens heeft de volgende indeling: &lt; asset_id &gt;_metadata.xml (bijvoorbeeld 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), waarbij &lt; asset_id &gt; de AssetId-waarde van de invoer Asset is.  

Media Services voert geen bekend in voor het genereren van meta gegevens. Invoer meta gegevens worden alleen gegenereerd als een artefact wanneer een invoer element in een taak wordt verwerkt. Dit artefact wordt daarom geschreven naar de uitvoer Asset. Er worden verschillende hulpprogram ma's gebruikt voor het genereren van meta gegevens voor invoer assets en uitvoer assets. Daarom heeft de invoer meta gegevens een iets ander schema dan de uitvoer meta gegevens.

Als u het meta gegevensbestand wilt controleren, kunt u een **SAS** -Locator maken en het bestand downloaden naar uw lokale computer. Hier vindt u een voor beeld van hoe u een SAS-Locator maakt en een bestand downloadt  [met behulp van de Media Services .NET SDK-extensies](media-services-dotnet-get-started.md).  

In dit artikel worden de elementen en typen beschreven van het XML-schema waarop de invoer-metada ( &lt; asset_id &gt;_metadata.xml) is gebaseerd.  Zie [uitvoer van meta gegevens](media-services-output-metadata-schema.md)voor informatie over het bestand dat meta gegevens bevat over het uitvoer element.  

Aan het einde van dit artikel vindt u de [schema code](media-services-input-metadata-schema.md#code) een [XML-voor beeld](media-services-input-metadata-schema.md#xml) .  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> AssetFiles-element (hoofd element)
Bevat een verzameling [AssetFile-elementen](media-services-input-metadata-schema.md#AssetFile)voor de coderings taak.  

Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

| Naam | Beschrijving |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = "1" maxOccurs = "ontgrensd" |Eén onderliggend element. Zie het [element AssetFile](media-services-input-metadata-schema.md#AssetFile)voor meer informatie. |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile-element
 Bevat kenmerken en elementen waarmee een Asset-bestand wordt beschreven.  

 Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Naam**<br /><br /> Vereist |**XS: teken reeks** |Bestands naam van het activum. |
| **Grootte**<br /><br /> Vereist |**XS: Long** |Grootte van het activa bestand in bytes. |
| **Duur**<br /><br /> Vereist |**XS: duration** |Duur van het afspelen van inhoud. Voor beeld: duration = "PT25M 37.757 S". |
| **NumberOfStreams**<br /><br /> Vereist |**XS: int** |Aantal streams in het activa bestand. |
| **FormatNames**<br /><br /> Vereist |**XS: teken reeks** |Indelings namen. |
| **FormatVerboseNames**<br /><br /> Vereist |**XS: teken reeks** |Uitgebreide namen opmaken. |
| **StartTime** |**XS: duration** |Begin tijd van inhoud. Voor beeld: StartTime = "PT 2.669 S". |
| **OverallBitRate** |**XS: int** |De gemiddelde bitrate van het activa bestand in kbps. |

> [!NOTE]
> De volgende vier onderliggende elementen moeten in een reeks worden weer gegeven.  
> 
> 

### <a name="child-elements"></a>Onderliggende elementen
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Programma's**<br /><br /> minOccurs = "0" | |Verzameling van alle [Program ma's-elementen](media-services-input-metadata-schema.md#Programs) wanneer het Asset-bestand zich in MPEG-TS-indeling bevindt. |
| **VideoTracks**<br /><br /> minOccurs = "0" | |Elk fysiek-activa bestand kan nul of meer video tracks bevatten die Interleaved zijn in een geschikte container indeling. Dit element bevat een verzameling van alle [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) die deel uitmaken van het activa bestand. |
| **AudioTracks**<br /><br /> minOccurs = "0" | |Elk fysiek-activa bestand kan nul of meer audio tracks Interleaved in een geschikte container indeling bevatten. Dit element bevat een verzameling van alle [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) die deel uitmaken van het activa bestand. |
| **Metagegevens**<br /><br /> minOccurs = "0" maxOccurs = "onbegrensd" |[Gegevens type](media-services-input-metadata-schema.md#MetadataType) |Meta gegevens van Asset file worden weer gegeven als key\value-teken reeksen. Bijvoorbeeld:<br /><br /> **&lt;Meta Data-sleutel = "taal" value = "eng"/&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> TrackType
Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Id**<br /><br /> Vereist |**XS: int** |Op nul gebaseerde index van dit audio-of video spoor.<br /><br /> Dit is niet noodzakelijkerwijs het TrackID dat wordt gebruikt in een MP4-bestand. |
| **Videocodec** |**XS: teken reeks** |Video track-codec teken reeks. |
| **CodecLongName** |**XS: teken reeks** |De lange naam van de audio-of video track-codec. |
| **Negatieve**<br /><br /> Vereist |**XS: teken reeks** |Tijd basis. Voor beeld: tijds basis = "1/48000" |
| **NumberOfFrames** |**XS: int** |Aantal frames (aanwezig voor video tracks). |
| **StartTime** |**XS: duration** |Start tijd bijhouden. Voor beeld: StartTime = "PT 2.669 S" |
| **Duur** |**XS: duration** |Houd de duur bij. Voor beeld: duration = "PTSampleFormat M 37.757 S". |

> [!NOTE]
> De volgende twee onderliggende elementen moeten in een reeks worden weer gegeven.  
> 
> 

### <a name="child-elements"></a>Onderliggende elementen
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Toestand**<br /><br /> minOccurs = "0" maxOccurs = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Bevat presentatie-informatie (bijvoorbeeld of een bepaald audio spoor is voor visueel gehandicapten). |
| **Metagegevens**<br /><br /> minOccurs = "0" maxOccurs = "onbegrensd" |[Gegevens type](media-services-input-metadata-schema.md#MetadataType) |Algemene sleutel/waarde-teken reeksen die kunnen worden gebruikt voor het opslaan van verschillende gegevens. Bijvoorbeeld: Key = "taal" en waarde = "eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (overneemt van TrackType)
 **AudioTrackType** is een globaal complex type dat overneemt van [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Het type vertegenwoordigt een specifiek audio spoor in het activa bestand.  

 Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **SampleFormat** |**XS: teken reeks** |Voorbeeld indeling. |
| **ChannelLayout** |**XS: teken reeks** |Kanaal indeling. |
| **Kanalen**<br /><br /> Vereist |**XS: int** |Aantal (0 of meer) audio kanalen. |
| **SamplingRate**<br /><br /> Vereist |**XS: int** |Audio sampling frequentie in samples/sec of Hz. |
| **Bitsnelheid** |**XS: int** |De gemiddelde bitsnelheid van audio in bits per seconde, zoals berekend op basis van het Asset-bestand. Alleen de nettolading van de elementaire stream wordt geteld en de pakket belasting is niet inbegrepen in dit aantal. |
| **BitsPerSample** |**XS: int** |Bits per voor beeld voor het wFormatTag-indelings type. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (overneemt van TrackType)
**VideoTrackType** is een globaal complex type dat overneemt van [TrackType](media-services-input-metadata-schema.md#TrackType).  

Het type vertegenwoordigt een specifiek video spoor in het activa bestand.  

Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **FourCC**<br /><br /> Vereist |**XS: teken reeks** |Code van de video-codec FourCC. |
| **Profiel** |**XS: teken reeks** |Profiel van video spoor. |
| **Afvlakking** |**XS: teken reeks** |Niveau van de video track. |
| **PixelFormat** |**XS: teken reeks** |Pixel indeling van video track. |
| **Breedte**<br /><br /> Vereist |**XS: int** |Breedte van gecodeerde video in pixels. |
| **Hoogte**<br /><br /> Vereist |**XS: int** |Versleutelde video hoogte in pixels. |
| **DisplayAspectRatioNumerator**<br /><br /> Vereist |**XS: Double** |Teller voor hoogte-breedte verhouding video weergave. |
| **DisplayAspectRatioDenominator**<br /><br /> Vereist |**XS: Double** |Noemer van hoogte-breedte verhouding video weergave. |
| **DisplayAspectRatioDenominator**<br /><br /> Vereist |**XS: Double** |Teller voor sample aspect verhouding van video. |
| **SampleAspectRatioNumerator** |**XS: Double** |Teller voor sample aspect verhouding van video. |
| **SampleAspectRatioNumerator** |**XS: Double** |Noemer voor beeld van hoogte verhouding voor video. |
| **Snelheid**<br /><br /> Vereist |**XS: decimaal** |Gemeten video frame frequentie in. 3F-indeling. |
| **Bitsnelheid** |**XS: int** |De gemiddelde bitsnelheid voor video in kilobits per seconde, zoals berekend op basis van het activa bestand. Alleen de nettolading van de elementaire stream wordt geteld en de verpakkings overhead is niet opgenomen. |
| **MaxGOPBitrate** |**XS: int** |Maximale GOP terug gemiddelde bitrate voor dit video spoor, in kilobits per seconde. |
| **HasBFrames** |**XS: int** |Video track van het aantal B-frames. |

## <a name="metadatatype"></a><a name="MetadataType"></a> Gegevens type
Meta **Data type** is een globaal complex type dat de gegevens van een activa bestand als sleutel/waarde teken reeksen beschrijft. Bijvoorbeeld: Key = "taal" en waarde = "eng".  

Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **prestatie**<br /><br /> Vereist |**XS: teken reeks** |De sleutel in het sleutel/waarde-paar. |
| **value**<br /><br /> Vereist |**XS: teken reeks** |De waarde in het sleutel/waarde-paar. |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
**ProgramType** is een globaal complex type dat een programma beschrijft.  

### <a name="attributes"></a>Kenmerken
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **ProgramId**<br /><br /> Vereist |**XS: int** |Programma-id |
| **NumberOfPrograms**<br /><br /> Vereist |**XS: int** |Aantal Program ma's. |
| **PmtPid**<br /><br /> Vereist |**XS: int** |Programma tabellen (PMTs) bevatten informatie over Program ma's.  Zie voor meer informatie [Bet](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Vereist |**XS: int** |Gebruikt door de decoder. Zie [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) voor meer informatie. |
| **StartPTS** |**XS: Long** |De tijds tempel van de presentatie wordt gestart. |
| **EndPTS** |**XS: Long** |De tijds tempel van de presentatie wordt beëindigd. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** is een globaal complex type waarmee de stroom wordt beschreven.  

Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Prijs**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit de standaard presentatie is. |
| **Dub**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit de nagesynchroniseerde presentatie is. |
| **Oorspronkelijk**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit de oorspronkelijke presentatie is. |
| **Opmerking**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit nummer commentaar bevat. |
| **Waaraan**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit nummer Song teksten bevat. |
| **Karaoke**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit staat voor het karaoke-spoor (achtergrond muziek, geen vocals). |
| **Geforceerd**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit de geforceerde presentatie is. |
| **HearingImpaired**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit nummer voor mensen is die slechthorend zijn. |
| **VisualImpaired**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit nummer voor visueel gehandicapten is. |
| **CleanEffects**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit spoor duidelijke effecten heeft. |
| **AttachedPic**<br /><br /> Vereist |**XS: int** |Stel dit kenmerk in op 1 om aan te geven dat dit spoor afbeeldingen heeft. |

## <a name="programs-element"></a><a name="Programs"></a> Element Program ma's
Wrapper-element dat meerdere **programma** -elementen vasthoudt.  

### <a name="child-elements"></a>Onderliggende elementen
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Programma**<br /><br /> minOccurs = "0" maxOccurs = "onbegrensd" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Voor Asset-bestanden die zich in MPEG-TS-indeling bevinden, bevat informatie over Program ma's in het Asset-bestand. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks-element
 Wrapper-element dat meerdere **VideoTrack** -elementen vasthoudt.  

 Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Onderliggende elementen
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = "0" maxOccurs = "onbegrensd" |[VideoTrackType (overneemt van TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Bevat informatie over video tracks in het Asset-bestand. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks-element
 Wrapper-element dat meerdere **AudioTrack** -elementen vasthoudt.  

 Bekijk een XML-voor beeld aan het einde van dit artikel: [XML-voor beeld](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>opties
| Naam | Type | Beschrijving |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = "0" maxOccurs = "onbegrensd" |[AudioTrackType (overneemt van TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Bevat informatie over audio sporen in het Asset-bestand. |

## <a name="schema-code"></a><a name="code"></a> Schema code
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            elementFormDefault="qualified">  

  <xs:complexType name="MetadataType">  
    <xs:attribute name="key"   type="xs:string" use="required"/>  
    <xs:attribute name="value" type="xs:string" use="required"/>  
  </xs:complexType>  

  <xs:complexType name="ProgramType">  
    <xs:attribute name="ProgramId" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Program Id</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Number of programs</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PmtPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pmt pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PcrPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pcr pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="StartPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>start pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="EndPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>end pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="StreamDispositionType">  
    <xs:attribute name="Default"          type="xs:int" use="required" />  
    <xs:attribute name="Dub"              type="xs:int" use="required" />  
    <xs:attribute name="Original"         type="xs:int" use="required" />  
    <xs:attribute name="Comment"          type="xs:int" use="required" />  
    <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
    <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
    <xs:attribute name="Forced"           type="xs:int" use="required" />  
    <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
    <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
    <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
    <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
  </xs:complexType>  

  <xs:complexType name="TrackType" abstract="true">  
    <xs:sequence>  
      <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
      <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
    </xs:sequence>  
    <xs:attribute name="Id" use="required">  
      <xs:annotation>  
        <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="Codec" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec string</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="CodecLongName" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec long name</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="TimeBase"  type="xs:string" use="required">  
      <xs:annotation>  
        <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfFrames">  
      <xs:annotation>  
        <xs:documentation>number of frames</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="StartTime" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="Duration" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="VideoTrackType">  
    <xs:annotation>  
      <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="FourCC" type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>video codec FourCC code</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Profile" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>profile</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Level" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>level</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PixelFormat" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>Video track's pixel format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Width" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video width in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Height" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video height in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioNumerator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioDenominator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="FrameRate" use="required">  
          <xs:annotation>  
            <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:decimal">  
              <xs:minInclusive value="0"/>  
              <xs:fractionDigits value="3"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="MaxGOPBitrate">  
          <xs:annotation>  
            <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="HasBFrames" type="xs:int">  
          <xs:annotation>  
            <xs:documentation>video track number of B frames</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:complexType name="AudioTrackType">  
    <xs:annotation>  
      <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="SampleFormat"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>sample format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="ChannelLayout"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>channel layout</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Channels" use="required">  
          <xs:annotation>  
            <xs:documentation>number of audio channels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SamplingRate" use="required">  
          <xs:annotation>  
            <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="BitsPerSample">  
          <xs:annotation>  
            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:element name="AssetFiles">  
    <xs:annotation>  
      <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
    </xs:annotation>  
    <xs:complexType>  
      <xs:sequence>  
        <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
          <xs:annotation>  
            <xs:documentation>asset file</xs:documentation>  
          </xs:annotation>  
          <xs:complexType>  
            <xs:sequence>  
              <xs:element name="Programs" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
            <xs:attribute name="Name" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>the media asset file name</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Size" use="required">  
              <xs:annotation>  
                <xs:documentation>size of file in bytes</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:long">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Duration" type="xs:duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
              <xs:annotation>  
                <xs:documentation>number of streams in asset file</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatNames" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format verbose names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="StartTime" type="xs:duration">  
              <xs:annotation>  
                <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="OverallBitRate">  
              <xs:annotation>  
                <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:complexType>  
        </xs:element>  
      </xs:sequence>  
    </xs:complexType>  
  </xs:element>  
</xs:schema>  
```


## <a name="xml-example"></a><a name="xml"></a> XML-voor beeld
Hier volgt een voor beeld van het meta gegevensbestand voor invoer.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
  <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
    <VideoTracks>  
      <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
      </VideoTrack>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
      </AudioTrack>  
    </AudioTracks>  
    <Metadata key="major_brand" value="mp42" />  
    <Metadata key="minor_version" value="0" />  
    <Metadata key="compatible_brands" value="mp42mp41" />  
    <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
    <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

