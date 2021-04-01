---
title: Problemen met Azure Cache voor Redis aan serverzijde oplossen
description: Informatie over het oplossen van veelvoorkomende problemen aan de server zijde met Azure cache voor redis, zoals geheugen belasting, hoge CPU, langdurende opdrachten of bandbreedte beperkingen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008913"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Problemen met Azure Cache voor Redis aan serverzijde oplossen

In deze sectie vindt u informatie over het oplossen van problemen die zich voordoen als gevolg van een voor waarde in een Azure-cache voor redis of de virtuele machine (s) die de host heeft.

- [Geheugendruk op Redis-server](#memory-pressure-on-redis-server)
- [Hoog CPU-gebruik of server belasting](#high-cpu-usage-or-server-load)
- [Langlopende opdrachten](#long-running-commands)
- [Bandbreedtebeperking aan serverzijde](#server-side-bandwidth-limitation)

> [!NOTE]
> Enkele van de stappen voor probleem oplossing in deze hand leiding bevatten instructies voor het uitvoeren van redis-opdrachten en het controleren van verschillende prestatie gegevens. Zie de artikelen in de sectie met [aanvullende informatie](#additional-information) voor meer informatie en instructies.
>

## <a name="memory-pressure-on-redis-server"></a>Geheugendruk op Redis-server

Geheugen druk aan de server zijde leidt tot allerlei prestatie problemen die het verwerken van aanvragen kunnen vertragen. Wanneer geheugen druk treffers zijn, kunnen er gegevens naar de schijf worden gewisseld. Als gevolg van deze _pagina fout_ wordt het systeem aanzienlijk trager. Er zijn verschillende mogelijke oorzaken van deze geheugen belasting:

- De cache wordt gevuld met gegevens in de buurt van de maximale capaciteit.
- Redis ziet u hoog geheugen fragmentatie. Deze fragmentatie wordt meestal veroorzaakt door het opslaan van grote objecten omdat redis is geoptimaliseerd voor kleine objecten.

Redis maakt twee statistieken beschikbaar via de opdracht [info](https://redis.io/commands/info) , waarmee u dit probleem kunt identificeren: ' used_memory ' en ' used_memory_rss '. U kunt [Deze metrische gegevens weer geven](cache-how-to-monitor.md#view-metrics-with-azure-monitor) met behulp van de portal.

Er zijn verschillende mogelijke wijzigingen die u kunt aanbrengen om het geheugengebruik in balans te houden:

- [Configureer een geheugen beleid](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) en stel verval tijden in voor uw sleutels. Dit beleid is mogelijk niet voldoende als u te maken hebt met fragmentatie.
- [Configureer een maxmemory-gereserveerde waarde](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) die groot genoeg is voor het compenseren van geheugen fragmentatie.
- Splits uw grote objecten in de cache op in kleinere gerelateerde objecten.
- [Maak waarschuwingen](cache-how-to-monitor.md#alerts) voor metrische gegevens, zoals het gebruikte geheugen, zodat u tijdig een melding krijgt over mogelijke gevolgen.
- [Schaal](cache-how-to-scale.md) naar een grotere cache grootte met meer geheugen capaciteit.

## <a name="high-cpu-usage-or-server-load"></a>Hoog CPU-gebruik of server belasting

Een hoge belasting van de server of het CPU-gebruik betekent dat de server niet tijdig aanvragen kan verwerken. Het kan lang duren voordat de server reageert en de aanvraag frequentie niet kan worden bijgehouden.

[Meet waarden bewaken](cache-how-to-monitor.md#view-metrics-with-azure-monitor) , zoals CPU of server belasting. Bekijk pieken in het CPU-gebruik die overeenkomen met time-outs.

Er zijn verschillende wijzigingen die u kunt aanbrengen om de belasting van hoge servers te beperken:

- Onderzoek wat er kan leiden tot CPU-pieken, zoals [langlopende opdrachten](#long-running-commands) die hieronder worden vermeld of waarvoor de pagina fouten zijn veroorzaakt door een hoge geheugen belasting.
- [Maak waarschuwingen](cache-how-to-monitor.md#alerts) voor metrische gegevens, zoals CPU of server belasting, om te worden geïnformeerd over mogelijke gevolgen.
- [Schaal](cache-how-to-scale.md) naar een grotere cache grootte met meer CPU-capaciteit.

## <a name="long-running-commands"></a>Langlopende opdrachten

Sommige redis-opdrachten zijn duurder om uit te voeren dan andere. In de [documentatie voor redis-opdrachten](https://redis.io/commands) wordt de tijd complexiteit van elke opdracht weer gegeven. Omdat de verwerking van redis-opdrachten uit één thread bestaat, worden alle andere opdrachten die na het uitvoeren van de opdracht worden uitgevoerd, geblokkeerd. Bekijk de opdrachten die u aan uw redis-server uitgeeft om inzicht te krijgen in de gevolgen van de prestaties. De opdracht [sleutels](https://redis.io/commands/keys) wordt bijvoorbeeld vaak gebruikt zonder te weten dat het een O (N)-bewerking is. U kunt sleutels voor komen door [scans](https://redis.io/commands/scan) te gebruiken om CPU-pieken te verminderen.

Met de [SLOWLOG](https://redis.io/commands/slowlog) -opdracht kunt u dure opdrachten meten die worden uitgevoerd op de server.

## <a name="server-side-bandwidth-limitation"></a>Bandbreedtebeperking aan serverzijde

Verschillende cache grootten hebben verschillende capaciteit voor netwerk bandbreedte. Als de server de beschik bare band breedte overschrijdt, worden de gegevens niet snel naar de client verzonden. Clients kunnen een time-out hebben omdat de server geen gegevens snel genoeg kan pushen naar de client.

De metrische gegevens ' cache read ' en ' cache write ' kunnen worden gebruikt om te zien hoeveel band breedte aan de server zijde wordt gebruikt. U kunt [Deze metrische gegevens weer geven](cache-how-to-monitor.md#view-metrics-with-azure-monitor) in de portal.

Voor het beperken van situaties waarbij het netwerk bandbreedte gebruik bijna de maximale capaciteit heeft:

- Wijzig het gedrag van de client aanroep om de netwerk vraag te verminderen.
- [Maak waarschuwingen](cache-how-to-monitor.md#alerts) voor metrische gegevens, zoals het lezen van de cache of schrijven naar de cache, zodat u tijdig een melding krijgt over mogelijke gevolgen.
- [Schaal](cache-how-to-scale.md) naar een grotere cache grootte met meer capaciteit voor netwerk bandbreedte.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan clientzijde oplossen](cache-troubleshoot-client.md)
- [De juiste laag kiezen](cache-overview.md#choosing-the-right-tier)
- [Hoe kan ik Bench Mark en test de prestaties van mijn cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure Cache voor Redis bewaken](cache-how-to-monitor.md)
- [Hoe kan ik redis-opdrachten uitvoeren?](cache-development-faq.md#how-can-i-run-redis-commands)
