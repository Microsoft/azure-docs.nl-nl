---
title: Azure CDN optimaliseren voor het type inhouds levering
description: Meer informatie over hoe Azure Content Delivery Network de levering kan optimaliseren op basis van het type inhoud. Aanbevolen procedures voor optimalisatie verbeteren de prestaties en de offload van de oorsprong.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 7c7e4ff05307452da67c37b23bf492db8855e2ef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004497"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Azure CDN optimaliseren voor het type inhouds levering

Wanneer u inhoud levert aan een grote wereld wijde doel groep, is het essentieel om de geoptimaliseerde levering van uw inhoud te garanderen. [Azure Content Delivery Network (CDN)](cdn-overview.md) kan de bezorgings ervaring optimaliseren op basis van het type inhoud dat u hebt. De inhoud kan een website, een live stream, een video of een groot bestand zijn om te downloaden. Wanneer u een CDN-eind punt maakt, geeft u een scenario op in de optie **geoptimaliseerd voor** . Met uw keuze bepaalt u welke optimalisatie wordt toegepast op de inhoud die wordt geleverd vanuit het CDN-eind punt.

Optimalisatie opties zijn ontworpen om het best practice-gedrag te gebruiken voor het verbeteren van de prestaties van de levering van inhoud en de betere offload van de bron. De mogelijkheden van uw scenario zijn van invloed op de prestaties door configuraties voor gedeeltelijke caching, object Chunking en het beleid voor het opnieuw proberen van fouten te wijzigen. 

Dit artikel bevat een overzicht van de verschillende optimalisatie functies en wanneer u deze moet gebruiken. Zie de betreffende artikelen voor elk afzonderlijk optimalisatie type voor meer informatie over de functies en beperkingen.

> [!NOTE]
> Wanneer u een CDN-eind punt maakt, kan de **geoptimaliseerde** opties variëren op basis van het type profiel waarmee het eind punt wordt gemaakt. Azure CDN providers passen een uitbrei ding op verschillende manieren toe, afhankelijk van het scenario. 

## <a name="provider-options"></a>Provider opties

**Azure CDN standaard van micro soft** -profielen ondersteunt de volgende optimalisaties:

* [Algemene Internet levering](#general-web-delivery). Deze optimalisatie wordt ook gebruikt voor mediastreaming en grote bestanden downloaden.

> [!NOTE]
> Dynamische site versnelling van micro soft wordt aangeboden via de [Azure front-deur service](../frontdoor/front-door-overview.md).

**Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** -profielen ondersteunen de volgende optimalisaties:

* [Algemene Internet levering](#general-web-delivery). Deze optimalisatie wordt ook gebruikt voor mediastreaming en grote bestanden downloaden.

* [Dynamische siteversnelling](#dynamic-site-acceleration) 


**Azure CDN standaard van Akamai** profielen ondersteunen de volgende optimalisaties:

* [Algemene Internet levering](#general-web-delivery) 

* [Algemene mediastreaming](#general-media-streaming)

* [Video-on-demand mediastreaming](#video-on-demand-media-streaming)

* [Grote bestanden downloaden](#large-file-download)

* [Dynamische siteversnelling](#dynamic-site-acceleration) 

Micro soft raadt u aan prestatie verschillen tussen verschillende providers te testen om de optimale provider voor uw levering te selecteren.

## <a name="select-and-configure-optimization-types"></a>Optimalisatie typen selecteren en configureren

Wanneer u een CDN-eind punt maakt, selecteert u een optimalisatie type dat het beste overeenkomt met het scenario en type inhoud dat door het eind punt moet worden geleverd. **Algemene Internet levering** is de standaard selectie. Voor bestaande **Azure CDN standaard van Akamai** -eind punten kunt u de optimalisatie optie op elk gewenst moment bijwerken. Door deze wijziging wordt de levering van Azure CDN niet onderbroken. 

1. Selecteer een eind punt in een **Azure CDN standaard van Akamai** profiel.

    ![Eindpunt selectie](./media/cdn-optimization-overview/01_Akamai.png)

2. Selecteer **optimalisatie** onder instellingen. Selecteer vervolgens een type in de vervolg keuzelijst **geoptimaliseerd voor** .

    ![Optimalisatie en type selectie](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalisatie voor specifieke scenario's

U kunt het CDN-eind punt optimaliseren voor een van deze scenario's. 

### <a name="general-web-delivery"></a>Algemene Internet levering

Algemene Internet levering is de meest voorkomende optimalisatie optie. Het is ontworpen voor algemeen optimalisatie van webinhoud, zoals webpagina's en webtoepassingen. Deze optimalisatie kan ook worden gebruikt voor het downloaden van bestanden en Video's.

Een typische website bevat statische en dynamische inhoud. Statische inhoud bevat afbeeldingen, java script-bibliotheken en opmaak modellen die kunnen worden opgeslagen in de cache en worden geleverd aan verschillende gebruikers. Dynamische inhoud wordt aangepast voor afzonderlijke gebruikers, zoals nieuws items die zijn afgestemd op een gebruikers profiel. Dynamische inhoud, zoals inhoud van een winkel wagen, is niet in de cache opgeslagen omdat deze uniek is voor elke gebruiker. De volledige website kan worden geoptimaliseerd door de algemene weblevering. 

> [!NOTE]
> Als u een Azure CDN- **Standaard profiel van Akamai** gebruikt, selecteert u dit optimalisatie type als uw gemiddelde bestands grootte kleiner is dan 10 MB. Als uw gemiddelde bestands grootte groter is dan 10 MB, selecteert u **grote bestanden downloaden** in de vervolg keuzelijst **geoptimaliseerd voor** .

### <a name="general-media-streaming"></a>Algemene mediastreaming

Als u het eind punt voor live streamen en video-on-demand streaming wilt gebruiken, selecteert u het optimalisatie type voor de algemene mediastreaming.

Mediastreaming is tijd gevoelig, omdat pakketten die te laat op de client arriveren, zoals veelvuldige buffering van video-inhoud, een gedegradeerde weergave ervaring kunnen veroorzaken. De optimalisatie van media-streaming vermindert de latentie van media-inhoud en biedt een smooth streaming ervaring voor gebruikers. 

Dit scenario is gebruikelijk voor klanten van Azure media service. Wanneer u Azure Media Services gebruikt, krijgt u één streaming-eind punt dat kan worden gebruikt voor zowel live als streaming op aanvraag. In dit scenario hoeven klanten niet over te scha kelen naar een ander eind punt wanneer ze van Live worden gewijzigd in streaming op aanvraag. Algemene optimalisatie van mediastreaming ondersteunt dit type scenario.

Voor **Azure CDN standaard van micro soft**, **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon**, gebruikt u het optimalisatie type algemene weblevering voor het leveren van algemene streaming-media-inhoud.

Zie [optimalisatie](cdn-media-streaming-optimization.md)van mediastreaming voor meer informatie over de optimalisatie van media-streaming.

### <a name="video-on-demand-media-streaming"></a>Video-on-demand mediastreaming

Video-on-demand optimalisatie van mediastreaming verbetert video-on-demand streaming-inhoud. Als u een eind punt gebruikt voor video-on-demand streaming, gebruikt u deze optie.

Voor **Azure CDN standaard van micro soft**, **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** -profielen, gebruikt u het optimalisatie type algemene weblevering voor het leveren van video-on-demand streaming media-inhoud.

Zie [optimalisatie](cdn-media-streaming-optimization.md)van mediastreaming voor meer informatie over de optimalisatie van media-streaming.

> [!NOTE]
> Als het CDN-eind punt voornamelijk video-on-demand inhoud heeft, gebruikt u dit optimalisatie type. Het belangrijkste verschil tussen dit optimalisatie type en het optimalisatie type General media streaming is de time-out voor het opnieuw proberen van de verbinding. De time-out is veel korter voor het werken met live streaming-scenario's.
>

### <a name="large-file-download"></a>Grote bestanden downloaden

Voor **Azure CDN standaard van Akamai** -profielen zijn grote bestands downloads geoptimaliseerd voor inhoud die groter is dan 10 MB. Als uw gemiddelde bestands grootte kleiner is dan 10 MB, gebruikt u algemene Internet levering. Als uw gemiddelde bestands grootte consistent groter is dan 10 MB, kan het efficiënter zijn om een afzonderlijk eind punt voor grote bestanden te maken. Bijvoorbeeld: firmware of software-updates zijn grote bestanden. Als u bestanden wilt leveren die groter zijn dan 1,8 GB, is de optimalisatie van grote bestanden vereist.

Voor **Azure CDN standaard van micro soft**, **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** -profielen, gebruikt u het optimalisatie type algemene weblevering voor het leveren van inhoud voor grote bestanden downloaden. Er is geen beperking voor de grootte van het downloaden van bestanden.

Zie voor meer informatie over de optimalisatie van grote bestanden [grote bestanden optimaliseren](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Dynamische siteversnelling

 Dynamic site Acceleration (DSA) is beschikbaar voor **Azure CDN Standard van Akamai**, **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** -profielen. Deze optimalisatie omvat extra kosten voor het gebruik van. Zie [Content Delivery Network prijzen](https://azure.microsoft.com/pricing/details/cdn/)voor meer informatie.

> [!NOTE]
> Dynamische site versnelling van micro soft wordt aangeboden via de [Azure front-deur service](../frontdoor/front-door-overview.md) , een wereld wijde [anycast](https://en.wikipedia.org/wiki/Anycast) -service die gebruikmaakt van het privé wereld wijde netwerk van micro soft om uw app-workloads te leveren.

DSA bevat verschillende technieken voor het voor deel van de latentie en prestaties van dynamische inhoud. Technieken zijn route-en netwerk optimalisatie, TCP-optimalisatie en meer. 

U kunt deze optimalisatie gebruiken om een web-app te versnellen die talloze reacties bevat die niet in cache kunnen worden opgeslagen. Voor beelden zijn Zoek resultaten, afhandelings transacties of realtime-gegevens. U kunt de basis mogelijkheden voor Azure CDN caching blijven gebruiken voor statische gegevens. 

Zie [Dynamic site Acceleration](cdn-dynamic-site-acceleration.md)voor meer informatie over dynamische site versnelling.