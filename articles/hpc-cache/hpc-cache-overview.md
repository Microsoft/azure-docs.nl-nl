---
title: Overzicht van Azure HPC Cache
description: Hierin wordt Azure HPC Cache beschreven, een versnellingsoplossing voor bestandstoegang voor High Performance Computing
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2085efc5f38b2252e40f4aeb1ebfa16bf38147b4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256137"
---
# <a name="what-is-azure-hpc-cache"></a>Wat is Azure HPC Cache?

Met Azure HPC Cache wordt de toegang tot uw gegevens versneld voor HPC-taken (High Performance Computing). Door bestanden in cache op te slaan in Azure, brengt Azure HPC Cache de schaalbaarheid van cloud-computing naar uw bestaande werkstroom. Deze service kan ook worden gebruikt voor werkstromen waarbij uw gegevens in WAN-verbindingen worden opgeslagen, zoals in de NAS-omgeving (network-attached storage) van uw lokale datacentrum.

Azure HPC Cache is eenvoudig te starten en te bewaken vanuit Azure Portal. Bestaande NFS-opslag of nieuwe blobcontainers kunnen deel uitmaken van de geaggregeerde naamruimte, waardoor clienttoegang eenvoudig wordt, zelfs als u het back-endopslagdoel wijzigt.

## <a name="overview-video"></a>Video-overzicht

[![videominiatuur: Overzicht van Azure HPC Cache: klik om de videopagina te bezoeken](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Klik op de bovenstaande afbeelding om een [kort overzicht van Azure HPC Cache te bekijken](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

## <a name="use-cases"></a>Gebruiksvoorbeelden

Met Azure HPC Cache wordt de productiviteit het beste verbeterd voor werkstromen zoals deze:

* Werkstromen met leesintensieve bestandstoegang
* Gegevens die zijn opgeslagen in NFS-toegankelijke opslag, Azure Blob of beide
* Computefarms met maximaal 75.000 CPU-kernen

Azure HPC Cache kan worden toegevoegd aan een groot aantal verschillende werkstromen in allerlei sectoren. Elk systeem waarin een groot aantal machines toegang nodig heeft tot een set bestanden op schaal en met lage latentie heeft baat bij deze service. De onderstaande secties bevatten specifieke voorbeelden.

### <a name="visual-effects-vfx-rendering"></a>Rendering van visuele effecten (VFX)

In media en entertainment kan Azure HPC Cache de gegevenstoegang voor urgente renderingprojecten versnellen. VFX-renderingwerkstromen vereisen vaak last-minute verwerking door grote aantallen berekeningsknooppunten. Gegevens voor deze werkstromen bevinden zich doorgaans in een on-premises NAS-omgeving. Met Azure HPC Cache kunnen de bestandsgegevens in cache in de cloud worden opgeslagen om de latentie te beperken en de flexibiliteit te verbeteren voor rendering op aanvraag.

### <a name="life-sciences"></a>Biowetenschappen

Veel werkstromen in biowetenschappen kunnen profiteren van uitschaling van bestandsopslag in cache.

Een onderzoeksinstituut dat werkstromen voor genomische analyse wil overbrengen naar Azure, kan deze eenvoudig verplaatsen met behulp van Azure HPC Cache. Omdat de cache toegang tot POSIX-bestanden biedt, zijn er geen wijzigingen op de client nodig om de bestaande clientwerkstroom in de cloud uit te voeren.

De Azure HPC Cache kan ook worden gebruikt om de efficiëntie te verbeteren van taken zoals secundaire analyse, farmacologische simulatie of AI-gestuurde afbeeldingsanalyse.

### <a name="financial-services-analytics"></a>Analyse voor financiële dienstverlening

Een Azure HPC Cache-implementatie kan bijdragen aan het versnellen van kwantitatieve analyseberekeningen, risicoanalyseworkloads en Monte Carlo-simulaties om financiële dienstverleners beter inzicht te bieden voor het nemen van strategische beslissingen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Bezoek de pagina [Azure Global Infrastructure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) om te ontdekken waar Azure HPC Cache beschikbaar is.

De Azure HPC Cache bevindt zich in een enkele regio. Het kan toegang krijgen tot gegevens die zijn opgeslagen in andere regio's als u deze verbindt met blobcontainers die daar zich bevinden. Met de cache worden klantgegevens niet permanent opgeslagen.

## <a name="next-steps"></a>Volgende stappen

* Lees de [productpagina van Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) voor meer informatie over de mogelijkheden ervan
* Meer informatie over de [productvereisten](hpc-cache-prerequisites.md)
* [Een Azure HPC Cache maken](hpc-cache-create.md) vanuit Azure Portal
