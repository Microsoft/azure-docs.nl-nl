---
title: Live video-analyses op IoT Edge quota's en beperkingen-Azure
description: In dit artikel wordt de analyse van live video op IoT Edge quota's en beperkingen beschreven.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400521"
---
# <a name="quotas-and-limitations"></a>Quota en beperkingen

In dit artikel worden de quota's en beperkingen van de live video Analytics op IoT Edge module opgesomd.

## <a name="maximum-period-of-disconnected-use"></a>De maximale periode van het niet-verbonden gebruik

De Edge-module kan tijdelijk geen verbinding met internet hebben. Als de module meer dan 36 uur niet meer is verbonden, worden alle grafiek exemplaren die werden uitgevoerd, gedeactiveerd. Alle verdere directe methode aanroepen worden geblokkeerd.

Als u de module Edge wilt hervatten naar een operationele status, moet u de Internet verbinding herstellen zodat de module kan communiceren met het Azure media service-account.

## <a name="maximum-number-of-graph-instances"></a>Maximum aantal grafiek exemplaren

Er worden Maxi maal 1000 Graph-exemplaren per module (gemaakt via GraphInstanceSet) ondersteund.

## <a name="maximum-number-of-graph-topologies"></a>Maximum aantal grafiek topologieën

De meeste 50 Graph-topologieën per module (gemaakt via GraphTopologySet) worden ondersteund.

## <a name="limitations-on-graph-topologies-at-preview"></a>Beperkingen voor grafiek topologieën tijdens de preview-versie

Met de preview-versie zijn er beperkingen op verschillende knoop punten die kunnen worden verbonden met een media grafiek topologie.

* RTSP-bron
   * Er is slechts één RTSP-bron per grafiek topologie toegestaan.
* Bewegings detectie processor
   * Moet direct worden downstream van de RTSP-bron.
   * Kan niet worden gebruikt als downstream van een HTTP-of gRPC-extensie processor.
* Signal Gate-processor
   * Moet direct worden downstream van de RTSP-bron.
* Asset Sink 
   * Moet direct worden downstream van de RTSP-bron-of signaal Gate-processor.
* Bestands Sink
   * Moet direct worden downstream van de Signal Gate-processor.
   * Kan niet direct worden downstream van een HTTP-of gRPC-extensie processor of bewegings detectie processor
* IoT Hub Sink
   * Kan niet direct worden downstream van een IoT Hub bron.

## <a name="limitations-on-media-service-operations-at-preview"></a>Beperkingen van media service-bewerkingen op de preview-versie

Op het moment van de preview-versie biedt de live video Analytics op IoT Edge geen ondersteuning voor het volgende:

* De mogelijkheid om het media service account te migreren van het ene naar het andere abonnement zonder onderbreking.
* De mogelijkheid om meer dan één opslag account met het media service account te gebruiken.
* De mogelijkheid om de gegevens van de Service-Principal in de gewenste eigenschappen van de module dynamisch te wijzigen zonder opnieuw op te starten.

U kunt alleen IP-camera's gebruiken die het RTSP-protocol ondersteunen. U vindt IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.

Verder moet u deze camera's configureren voor het gebruik van H. 264 video en AAC-audio. Andere codecs worden momenteel niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen

[Overzicht](overview.md)
