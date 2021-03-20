---
title: Filters definiëren in Azure Media Services
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stroom te streamen. Media Services maakt dynamische manifesten om deze selectief streaming te verzorgen.
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
ms.openlocfilehash: bb5561ced93c3f5a899c6e48fdab0f14e52914bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89291546"
---
# <a name="filters"></a>Filters

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer u uw inhoud aan klanten levert (live streaming-gebeurtenissen of video op aanvraag), heeft uw client mogelijk meer flexibiliteit nodig dan is beschreven in het manifest bestand van het standaard activum. Azure Media Services biedt [dynamische manifesten](filters-dynamic-manifest-overview.md) op basis van vooraf gedefinieerde filters. 

Filters zijn regels aan de server zijde waarmee uw klanten dingen kunnen doen: 

- Alleen een sectie van een video afspelen (in plaats van de hele video af te spelen). Bijvoorbeeld:
  - Verminder het manifest om een subclip van een live-gebeurtenis weer te geven ("subclip filtering") of
  - Het begin van een video bijsnijden ("een video bijsnijden").
- Lever alleen de opgegeven vertoningen en/of opgegeven taal sporen die worden ondersteund door het apparaat dat wordt gebruikt voor het afspelen van de inhoud (' weergave filters '). 
- Stel Presentation Window (DVR) in om een beperkte lengte van het DVR-venster op te geven in de speler ("presentatie venster aanpassen").

Met Media Services kunt u **account filters** en **activa filters** maken voor uw inhoud. Daarnaast kunt u uw vooraf gemaakte filters koppelen aan een **streaming-Locator**.

## <a name="defining-filters"></a>Filters definiëren

Er zijn twee soorten filters: 

* [Account filters](/rest/api/media/accountfilters) (globaal): kan worden toegepast op alle activa in het Azure Media Services account, een levens duur van het account hebben.
* [Asset filters](/rest/api/media/assetfilters) (lokaal): kan alleen worden toegepast op een Asset waaraan het filter is gekoppeld bij het maken, een levens duur van de Asset heeft. 

De typen **account filters** en **activa filters** hebben precies dezelfde eigenschappen voor het definiëren/beschrijven van het filter. Behalve wanneer u het **activa filter** maakt, moet u de naam van het activum opgeven waaraan u het filter wilt koppelen.

Afhankelijk van uw scenario, bepaalt u welk type filter het meest geschikt is (Asset filter of account filter). Account filters zijn geschikt voor apparaatprofielen (filters weer gave filteren) waarbij Asset filters kunnen worden gebruikt om een specifiek activum te bijsnijden.

U kunt de volgende eigenschappen gebruiken om de filters te beschrijven. 

|Naam|Beschrijving|
|---|---|
|firstQuality|De bitsnelheid van de eerste kwaliteit van het filter.|
|presentationTimeRange|Het tijds bereik voor de presentatie. Deze eigenschap wordt gebruikt voor het filteren van begin-en eind punten van het manifest, de lengte van het presentatie venster en de Live start positie. <br/>Zie [PresentationTimeRange](#presentationtimerange)voor meer informatie.|
|Registreer|Hiermee worden de selectie voorwaarden bijgehouden. Zie voor meer informatie [sporen](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Gebruik deze eigenschap met **activa filters**. Het is niet raadzaam om de eigenschap in te stellen met **account filters**.

|Naam|Beschrijving|
|---|---|
|**endTimestamp**|Is van toepassing op video on demand (VoD).<br/>Voor de Live Stream-presentatie wordt deze op de achtergrond genegeerd en toegepast wanneer de presentatie wordt beëindigd en de stroom wordt VoD.<br/>Dit is een lange waarde die een absoluut eind punt van de presentatie vertegenwoordigt, afgerond op het dichtstbijzijnde volgende GOP terug start. De eenheid is de tijd schaal, dus een endTimestamp van 1800000000 zou drie minuten zijn.<br/>Gebruik startTimestamp en endTimestamp om de fragmenten die in de afspeel lijst (manifest) staan, te verkleinen.<br/>Bijvoorbeeld: startTimestamp = 40000000 en endTimestamp = 100000000 met behulp van de standaard tijd schaal genereren een afspeel lijst met fragmenten tussen 4 seconden en 10 seconden van de VoD-presentatie. Als een fragment de grens straddles, wordt het volledige fragment opgenomen in het manifest.|
|**forceEndTimestamp**|Is alleen van toepassing op live streamen.<br/>Hiermee wordt aangegeven of de eigenschap endTimestamp moet aanwezig zijn. Indien waar, moet endTimestamp worden opgegeven of er wordt een ongeldige aanvraag code geretourneerd.<br/>Toegestane waarden: False, True.|
|**liveBackoffDuration**|Is alleen van toepassing op live streamen.<br/> Met deze waarde wordt de laatste live positie gedefinieerd waarnaar een client kan zoeken.<br/>Met deze eigenschap kunt u de positie van live afspelen vertragen en een buffer aan de server zijde maken voor spelers.<br/>De eenheid voor deze eigenschap is tijdschaal (zie hieronder).<br/>De maximale duur van Live back-ups is 300 seconden (3000000000).<br/>Een waarde van 2000000000 betekent bijvoorbeeld dat de meest recente beschik bare inhoud 20 seconden vertraagd is ten opzichte van de werkelijke Live-rand.|
|**presentationWindowDuration**|Is alleen van toepassing op live streamen.<br/>Gebruik presentationWindowDuration om een sliding window van fragmenten toe te passen die moeten worden toegevoegd aan een afspeel lijst.<br/>De eenheid voor deze eigenschap is tijdschaal (zie hieronder).<br/>Stel bijvoorbeeld presentationWindowDuration = 1200000000 in om een sliding window van twee minuten toe te passen. Media binnen 2 minuten van de Live Edge worden opgenomen in de afspeel lijst. Als een fragment de grens straddles, wordt het volledige fragment opgenomen in de afspeel lijst. De minimale duur van het presentatie venster is 60 seconden.|
|**startTimestamp**|Is van toepassing op video on demand (VoD) of live streamen.<br/>Dit is een lange waarde die een absoluut begin punt van de stroom aangeeft. De waarde wordt afgerond naar de dichtstbijzijnde volgende GOP terug start. De eenheid is de tijd schaal, dus een startTimestamp van 150000000 zou 15 seconden zijn.<br/>Gebruik startTimestamp en endTimestampp om de fragmenten die in de afspeel lijst (manifest) staan, te verkleinen.<br/>Bijvoorbeeld: startTimestamp = 40000000 en endTimestamp = 100000000 met behulp van de standaard tijd schaal genereren een afspeel lijst met fragmenten tussen 4 seconden en 10 seconden van de VoD-presentatie. Als een fragment de grens straddles, wordt het volledige fragment opgenomen in het manifest.|
|**lijnen**|Is van toepassing op alle tijds tempels en duursen in een tijds bereik voor de presentatie, opgegeven als het aantal stappen in één seconde.<br/>De standaard waarde is 10000000-10.000.000 in één seconde, waarbij elke verhoging van 100 nano seconden lang is.<br/>Als u bijvoorbeeld een startTimestamp op 30 seconden wilt instellen, gebruikt u de waarde 300000000 wanneer u de standaard tijdschaal gebruikt.|

### <a name="tracks"></a>Registreer

U geeft een lijst met filter eigenschappen (FilterTrackPropertyConditions) op op basis van de trajecten van uw stroom (live streamen of Video's op aanvraag) moeten worden opgenomen in het dynamisch gemaakte manifest. De filters worden gecombineerd met behulp van een logische **en** - **of** -bewerking.

Bij het filteren van eigenschaps voorwaarden worden spoor typen, waarden (beschreven in de volgende tabel) en bewerkingen (gelijk aan NotEqual) beschreven. 

|Naam|Beschrijving|
|---|---|
|**Bitsnelheid**|Gebruik de bitsnelheid van het nummer voor het filteren.<br/><br/>De aanbevolen waarde is een aantal bitrates, in bits per seconde. Bijvoorbeeld ' 0-2427000 '.<br/><br/>Opmerking: Hoewel u een specifieke bitsnelheid kunt gebruiken, zoals 250000 (bits per seconde), wordt deze methode niet aanbevolen, omdat de exacte bitsnelheid van het ene naar het andere activum kunnen variëren.|
|**FourCC**|Gebruik de FourCC-waarde van het nummer voor filteren.<br/><br/>De waarde is het eerste element van de indeling codecs, zoals opgegeven in [RFC 6381](https://tools.ietf.org/html/rfc6381). Momenteel worden de volgende codecs ondersteund: <br/>For video: "avc1", "hev1", "hvc1"<br/>Voor audio: "mp4a", "EC-3"<br/><br/>Als u de FourCC-waarden voor sporen in een Asset wilt bepalen, kunt u het manifest bestand ophalen en onderzoeken.|
|**Taal**|De taal van het nummer gebruiken voor het filteren.<br/><br/>De waarde is het label van een taal die u wilt toevoegen, zoals opgegeven in RFC 5646. Bijvoorbeeld ' en '.|
|**Naam**|Gebruik de naam van het nummer voor filteren.|
|**Type**|Het type van het nummer gebruiken voor filteren.<br/><br/>De volgende waarden zijn toegestaan: "video", "audio" of "text".|

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een filter voor live streams gedefinieerd: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Filters koppelen aan de streaming-Locator

U kunt een lijst met [Asset-of account filters](filters-concept.md) opgeven voor uw [streaming-Locator](/rest/api/media/streaminglocators/create#request-body). Met de [dynamische packager](dynamic-packaging-overview.md) wordt deze lijst met filters toegepast, samen met de records die door uw client zijn opgegeven in de URL. Deze combi natie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md)dat is gebaseerd op filters in de URL + filters die u opgeeft op de streaming-Locator. 

Zie de volgende voorbeelden:

* [Filters koppelen aan streaming-Locator-.NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Filters koppelen aan streaming-Locator-CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Filters bijwerken
 
**Streaming-Locators** worden niet bijgewerkt terwijl filters kunnen worden bijgewerkt. 

Het wordt niet aanbevolen om de definitie van filters bij te werken die zijn gekoppeld aan een actief gepubliceerde **streaming-Locator**, met name wanneer CDN is ingeschakeld. Streaming-servers en Cdn's kunnen interne caches hebben die ertoe kunnen leiden dat verouderde gegevens in de cache worden geretourneerd. 

Als de filter definitie moet worden gewijzigd, kunt u een nieuw filter maken en toevoegen aan de URL van de **streaming-Locator** of een nieuwe **streaming-Locator** publiceren die rechtstreeks verwijst naar het filter.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u via een programma filters maakt.  

- [Filters maken met REST-Api's](filters-dynamic-manifest-rest-howto.md)
- [Filters maken met .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Filters maken met CLI](filters-dynamic-manifest-cli-howto.md)
