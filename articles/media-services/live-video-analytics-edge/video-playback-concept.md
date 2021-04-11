---
title: Video afspelen-Azure
description: Tijdelijke aanduiding
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2020d64538b2fcc846ab9a146e2fc95325abd26b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063369"
---
# <a name="video-playback"></a>Video afspelen 

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen 

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md)

## <a name="overview"></a>Overzicht  

U kunt [Media grafieken](media-graph-concept.md) gebruiken om video in een Azure Media Services- [Asset](terminology.md#asset)op te nemen. In dit document vindt u informatie over de stappen die u moet uitvoeren om een Asset af te spelen met bestaande streaming-mogelijkheden van Azure Media Services.

## <a name="streaming-endpoint"></a>Streaming-eindpunt 

U kunt Azure Media Services gebruiken om de Asset naar video spelers te [streamen](terminology.md#streaming) met behulp van industrie standaard, op http gebaseerde protocollen voor mediastreaming zoals http live streaming (HLS) en MPEG-Dash. Deze conversie van media van opgenomen inhoud naar streaming-indelingen wordt verwerkt door een [streaming-eind punt](../latest/streaming-endpoint-concept.md), een bron die u moet inrichten in uw Azure media service-account.

## <a name="streaming-policy"></a>Streaming-beleid 

Azure Media Services biedt u verschillende methoden om uw video-streams te beveiligen, zoals beschreven in [uw inhoud beveiligen met Media Services artikel over dynamische versleuteling](../latest/drm-content-protection-concept.md) . Op hoog niveau zijn opties voor inhouds beveiliging:

* **In-the-Clear streaming** : waar geen versleuteling wordt toegepast tijdens het streamen.
* **Gebruik Advanced Encryption Standard (AES-128)** : en implementeer een methode voor het leveren van de sleutels voor het ontsleutelen van de video alleen naar geauthenticeerde viewers.
* **Gebruik digital rights management (DRM)-systemen** om het gebruik, de wijziging en de levering van video te beheren op apparaten die dit beleid afdwingen.

Als u de beveiliging van inhoud wilt waarborgen, kunt u een [streaming-beleid](../latest/streaming-policy-concept.md) definiëren en maken in uw media service-account en dit gebruiken voor het streamen van alle assets (ervan uitgaande dat alle streams dezelfde vereisten voor beveiliging hebben). U kunt ook een van de vooraf gedefinieerde beleids regels (zoals Predefined_ClearStreamingOnly) gebruiken.

## <a name="streaming-locator"></a>Streaming-locator  

Zodra u een streaming-eind punt hebt gestart in uw media service-account en het streaming-beleid dat u hebt gedefinieerd, kunt u door gaan met het streamen van geregistreerde media vanuit een Asset via HLS of streepje-protocollen. Webspelers en mobiele apps hebben een URL nodig die verwijst naar die HLS of STREEPJES stroom. U kunt deze URL maken met behulp van de [streaming-Locator](../latest/streaming-locators-concept.md). Zoals besproken in dat artikel, en wordt weer gegeven in [een streaming-Locator maken en](../latest/create-streaming-locator-build-url.md) voor beeld van build-url's, wordt de streaming-URL samengesteld uit het streaming-eind punt, het streaming-beleid en de streaming-Locator.

## <a name="content-recorded-using-file-sink"></a>Inhoud die is opgenomen met File Sink  

Zoals beschreven in [Media Graph file Sink](media-graph-concept.md#file-sink), kunt u media grafieken gebruiken om Video's op te nemen in het lokale bestands systeem van het apparaat aan de rand met behulp van een bestands sink in uw media grafiek. De bestands-Sink genereert [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) -bestanden en u kunt het HTML5- [ &lt; video &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) -element gebruiken om dergelijke inhoud af te spelen. 

## <a name="next-steps"></a>Volgende stappen

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
