---
title: Overzicht van Azure Media Services streaming-eind punten | Microsoft Docs
description: Dit artikel bevat een overzicht van Azure Media Services streaming-eind punten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0961b52ebc7271fabf4cc05ed99eea23d911a2d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009085"
---
# <a name="streaming-endpoints-overview"></a>Overzicht van streaming-eind punten  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In Microsoft Azure Media Services (AMS) vertegenwoordigt een **streaming-eind punt** een streaming-service die inhoud rechtstreeks kan leveren aan een client speler of een Content Delivery Network (CDN) voor verdere distributie. Media Services biedt ook naadloze Azure CDN integratie. De uitgaande stroom van een StreamingEndpoint-service kan een live stream, een video op aanvraag of een progressief downloaden van uw asset in uw Media Services-account zijn. Elk Azure Media Services account bevat een standaard-StreamingEndpoint. Aanvullende StreamingEndpoints kunnen worden gemaakt onder het account. Er zijn twee versies van StreamingEndpoints, 1,0 en 2,0. Vanaf 10 januari 2017 zullen nieuw gemaakte AMS-accounts versie 2,0 **standaard** StreamingEndpoint bevatten. Extra streaming-eind punten die u aan dit account toevoegt, zijn ook versie 2,0. Deze wijziging heeft geen invloed op de bestaande accounts. de bestaande StreamingEndpoints wordt versie 1,0 en kan worden bijgewerkt naar versie 2,0. Deze wijziging is van invloed op wijzigingen in de facturerings-en onderdelen (Zie de sectie **streaming-typen en-versies** die hieronder worden beschreven) voor meer informatie.

Azure Media Services de volgende eigenschappen toegevoegd aan de streaming-eindpunt entiteit: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Zie voor gedetailleerde informatie [over deze eigenschappen](/rest/api/media/operations/streamingendpoint). 

Wanneer u een Azure Media Services account maakt, wordt er een standaard-streaming-eind punt voor u gemaakt in de status **gestopt** . U kunt het standaard streaming-eind punt niet verwijderen. Afhankelijk van de beschik baarheid van Azure CDN in de doel regio, bevat standaard nieuw gemaakt StandardVerizon-eind punt ook de integratie van de CDN-provider. 
                
> [!NOTE]
> Azure CDN-integratie kan worden uitgeschakeld voordat het streaming-eind punt wordt gestart. De `hostname` en de streaming-URL blijven hetzelfde, ongeacht of u CDN inschakelt.

In dit onderwerp vindt u een overzicht van de belangrijkste functies die worden geboden door streaming-eind punten.

## <a name="naming-conventions"></a>Naamconventies

Voor het standaard eindpunt: `{AccountName}.streaming.mediaservices.windows.net`

Voor alle extra eind punten: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Typen streaming en versies

### <a name="standardpremium-types-version-20"></a>Standard/Premium-typen (versie 2,0)

Vanaf de versie van Media Services januari 2017 hebt u twee streaming-typen: **Standard** (preview) en **Premium**. Deze typen maken deel uit van de streaming-eindpunt versie ' 2,0 '.


|Type|Description|
|--------|--------|  
|**Standard**|Het standaard streaming-eind punt is een **standaard** type, dat kan worden gewijzigd in het Premium-type door streaming-eenheden aan te passen.|
|**Premium** |Deze optie is geschikt voor professionele scenario's die een hogere schaal of beheer vereisen. U gaat naar een **Premium** -type door streaming-eenheden aan te passen.<br/>Toegewezen streaming-eind punten zijn Live in geïsoleerde omgevingen en concurreren niet voor resources.|

Voor klanten die inhoud willen leveren aan grote Internet doelgroepen, raden we u aan CDN op het streaming-eind punt in te scha kelen.

Zie de sectie [streaming-typen vergelijken](#comparing-streaming-types) volgende voor meer informatie.

### <a name="classic-type-version-10"></a>Klassiek type (versie 1,0)

Voor gebruikers die AMS-accounts hebben gemaakt vóór de release van januari 10 2017, hebt u een **klassiek** type van een streaming-eind punt. Dit type maakt deel uit van de streaming-eindpunt versie ' 1,0 '.

Als het streaming-eind punt van uw **versie 1,0** >= 1 Premium streaming-eenheden (su) heeft, is het Premium streaming-eind punt en worden alle AMS-functies (net als het type **standaard/Premium** ) weer geven zonder dat er aanvullende configuratie stappen worden uitgevoerd.

>[!NOTE]
>**Klassieke** streaming-eind punten (versie "1,0" en 0 su) biedt beperkte functies en bevatten geen sla. Het is raadzaam om te migreren naar het **standaard** type voor betere ervaring en om functies zoals dynamische verpakking of versleuteling en andere functies te gebruiken die worden geleverd met het **standaard** type. Als u wilt migreren naar het **standaard** type, gaat u naar de [Azure Portal](https://portal.azure.com/) en selecteert u de optie **opt-in op Standard**. Zie de sectie [migratie](#migration-between-types) voor meer informatie over migratie.
>
>Houd er rekening mee dat deze bewerking niet kan worden teruggedraaid en dat de prijs gevolgen heeft.
>
 
## <a name="comparing-streaming-types"></a>Streaming-typen vergelijken

### <a name="versions"></a>Versies

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Billing|
|--------------|----------|-----------------|-----------------|-----------------|
|Klassiek|1.0|0|NA|Gratis|
|Standard streaming-eind punt (preview-versie)|2,0|0|Yes|Betaald|
|Premium-streaming-eenheden|1.0|>0|Yes|Betaald|
|Premium-streaming-eenheden|2,0|>0|Yes|Betaald|

### <a name="features"></a>Functies

Functie|Standard|Premium
---|---|---
Doorvoer |Maxi maal 600 Mbps en kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.|200 Mbps per streaming-eenheid (SU). Kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.
CDN|Azure CDN, CDN van derden of geen CDN.|Azure CDN, CDN van derden of geen CDN.
Facturering is naar verhouding| Dagelijks|Dagelijks
Dynamische versleuteling|Ja|Ja
Dynamische verpakking|Ja|Ja
Schalen|Automatisch geschaald naar de beoogde door voer.|Extra streaming-eenheden.
IP-filtering/G20/aangepaste host <sup>1</sup>|Ja|Ja
Progressieve down load|Ja|Ja
Aanbevolen gebruik |Aanbevolen voor de meeste streaming-scenario's.|Professioneel gebruik. 

<sup>1</sup> alleen rechtstreeks op het streaming-eind punt gebruikt wanneer CDN niet is ingeschakeld op het eind punt.<br/>

Zie [prijzen en sla](https://azure.microsoft.com/pricing/details/media-services/)voor informatie over sla.

## <a name="migration-between-types"></a>Migratie tussen typen

Van | Tot | Bewerking
---|---|---
Klassiek|Standard|Moet u zich aanmelden
Klassiek|Premium| Schalen (extra streaming-eenheden)
Standard/Premium|Klassiek|Niet beschikbaar (als de versie van het streaming-eind punt 1,0 is. Het is toegestaan te wijzigen in klassiek met instelling scaleunits in ' 0 ')
Standard (met/zonder CDN)|Premium met dezelfde configuraties|Toegestaan in de status **gestart** . (via Azure Portal)
Premium (met/zonder CDN)|Standaard met dezelfde configuraties|Toegestaan in de status **gestart** (via Azure Portal)
Standard (met/zonder CDN)|Premium met andere configuratie|Toegestaan in de status **gestopt** (via Azure Portal). Niet toegestaan bij de status wordt uitgevoerd.
Premium (met/zonder CDN)|Standaard met andere configuratie|Toegestaan in de status **gestopt** (via Azure Portal). Niet toegestaan bij de status wordt uitgevoerd.
Versie 1,0 met SU >= 1 met CDN|Standard/Premium zonder CDN|Toegestaan in de status **gestopt** . Niet toegestaan in de status **gestart** .
Versie 1,0 met SU >= 1 met CDN|Standaard met/zonder CDN|Toegestaan in de status **gestopt** . Niet toegestaan in de status **gestart** . Versie 1,0 CDN wordt verwijderd en er wordt een nieuwe gemaakt en gestart.
Versie 1,0 met SU >= 1 met CDN|Premium met/zonder CDN|Toegestaan in de status **gestopt** . Niet toegestaan in de status **gestart** . Klassiek CDN wordt verwijderd en er wordt een nieuwe gemaakt en gestart.

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
