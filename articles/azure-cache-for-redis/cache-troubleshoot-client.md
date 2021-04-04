---
title: Problemen met Azure Cache voor Redis aan clientzijde oplossen
description: Informatie over het oplossen van veelvoorkomende problemen aan de client zijde met Azure cache voor redis, zoals redis-client geheugen belasting, verkeers burst, hoge CPU, beperkte band breedte, grote aanvragen of grote reactie grootte.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008964"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Problemen met Azure Cache voor Redis aan clientzijde oplossen

In deze sectie vindt u informatie over het oplossen van problemen die zich voordoen als gevolg van een voor waarde op de redis-client die door uw toepassing wordt gebruikt.

- [Geheugen druk op redis-client](#memory-pressure-on-redis-client)
- [Verkeers burst](#traffic-burst)
- [Intensief CPU-gebruik van client](#high-client-cpu-usage)
- [Bandbreedte beperking aan client zijde](#client-side-bandwidth-limitation)
- [Grote aanvraag-of antwoord grootte](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Geheugen druk op redis-client

Geheugen druk op de client computer leidt naar alle soorten prestatie problemen die de verwerking van antwoorden van de cache kunnen vertragen. Wanneer geheugen druk treffers zijn, kunnen er gegevens naar de schijf worden gewisseld. Als gevolg van deze _pagina fout_ wordt het systeem aanzienlijk trager.

Geheugen druk op de client detecteren:

- Controleer het geheugen gebruik op de computer om er zeker van te zijn dat deze niet groter is dan het beschik bare geheugen.
- Het prestatie meter item van de client bewaken `Page Faults/Sec` . Tijdens normale werking hebben de meeste systemen enkele pagina fouten. Pieken in wisselbestandsfouten die overeenkomen met time-outs van aanvragen kunnen geheugenbelasting aangeven.

Hoge geheugen druk op de client kan op verschillende manieren worden verminderd:

- Bestudeer de gebruiks patronen van het geheugen om het geheugen gebruik op de client te verminderen.
- Upgrade uw client-VM naar een grotere grootte met meer geheugen.

## <a name="traffic-burst"></a>Verkeers burst

Bursts van verkeer in combi natie met slechte `ThreadPool` instellingen kunnen leiden tot vertragingen bij het verwerken van gegevens die al zijn verzonden door de redis-server, maar die nog niet zijn verbruikt aan de client zijde.

Bewaak hoe uw `ThreadPool` statistieken in de loop van de tijd worden gewijzigd met [een voor `ThreadPoolLogger` beeld ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). U kunt  `TimeoutException` berichten van stack Exchange. redis zoals hieronder gebruiken om verder te onderzoeken:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

In de voor gaande uitzonde ring zijn er verschillende problemen die interessant zijn:

- U ziet dat in de `IOCP` sectie en de `WORKER` sectie een `Busy` waarde is die groter is dan de `Min` waarde. Dit verschil betekent dat uw `ThreadPool` instellingen moeten worden aangepast.
- U kunt ook zien `in: 64221` . Deze waarde geeft aan dat 64.211 bytes zijn ontvangen op de kernel-laag van de client, maar niet zijn gelezen door de toepassing. Dit verschil betekent meestal dat uw toepassing (bijvoorbeeld stack Exchange. redis) geen gegevens van het netwerk leest zodra de server deze naar u verzendt.

U kunt [uw `ThreadPool` instellingen configureren](cache-management-faq.md#important-details-about-threadpool-growth) om ervoor te zorgen dat uw thread pool snel omhoog wordt geschaald onder burst-scenario's.

## <a name="high-client-cpu-usage"></a>Intensief CPU-gebruik van client

Intensief CPU-gebruik van de client geeft aan dat het systeem het werk dat is gevraagd, niet kan bijhouden. Hoewel de cache het antwoord snel heeft verzonden, kan de client de reactie niet tijdig verwerken.

Bewaak het CPU-gebruik voor het hele systeem op basis van metrische gegevens die beschikbaar zijn in de Azure Portal of via prestatie meter items op de computer. Zorg ervoor dat u de *proces* -CPU niet bewaakt omdat een enkel proces een laag CPU-gebruik kan hebben, maar dat de systeem-CPU hoog kan zijn. Bekijk pieken in het CPU-gebruik die overeenkomen met time-outs. Hoge CPU kan ook grote `in: XXX` waarden in `TimeoutException` fout berichten veroorzaken, zoals wordt beschreven in de sectie [verkeers burst](#traffic-burst) .

> [!NOTE]
> Stack Exchange. redis 1.1.603 en hoger bevat de `local-cpu` metrische gegevens in `TimeoutException` fout berichten. Zorg ervoor dat u de nieuwste versie van het [stack Exchange. redis NuGet-pakket](https://www.nuget.org/packages/StackExchange.Redis/)gebruikt. Er zijn fouten die voortdurend worden opgelost in de code, zodat deze robuuster is voor time-outs zodat de meest recente versie van belang is.
>

Het hoge CPU-gebruik van een client beperken:

- Onderzoeken wat CPU-pieken veroorzaken.
- Upgrade uw client naar een grotere VM-grootte met meer CPU-capaciteit.

## <a name="client-side-bandwidth-limitation"></a>Bandbreedte beperking aan client zijde

Afhankelijk van de architectuur van client computers, kunnen ze beperkingen hebben ten opzichte van de hoeveelheid netwerk bandbreedte die ze beschikbaar hebben. Als de client de beschik bare band breedte overschrijdt door de netwerk capaciteit te overbelasten, worden de gegevens niet op de client verwerkt, net zoals de server deze verzendt. Deze situatie kan leiden tot time-outs.

Bewaak hoe uw bandbreedte gebruik in de loop van de tijd verandert met [een voor beeld `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Deze code wordt mogelijk niet goed uitgevoerd in sommige omgevingen met beperkte machtigingen (zoals Azure web sites).

Als u het risico wilt beperken, vermindert u de netwerk bandbreedte of verhoogt u de VM-grootte van de client naar een met meer netwerk capaciteit.

## <a name="large-request-or-response-size"></a>Grote aanvraag-of antwoord grootte

Een grote aanvraag/antwoord kan time-outs veroorzaken. Stel dat uw time-outwaarde voor uw client 1 seconde is. Uw toepassing vraagt twee sleutels (bijvoorbeeld ' A ' en ' B ') tegelijk aan (met behulp van dezelfde fysieke netwerk verbinding). De meeste clients ondersteunen de aanvraag ' pipeline ', waarbij beide aanvragen ' A ' en ' B ' na elkaar worden verzonden zonder dat ze op hun antwoord wachten. De server verzendt de antwoorden in dezelfde volg orde. Als het antwoord ' A ' groot is, kan dit het grootste deel van de time-out voor latere aanvragen beeten.

In het volgende voor beeld worden de aanvragen ' A ' en ' B ' snel naar de server verzonden. De server begint met het verzenden van antwoorden ' A ' en ' B ' snel. Als gevolg van gegevens overdrachts tijden moet het antwoord ' B ' na een time-out van het antwoord wachten, zelfs als de server snel heeft gereageerd.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

Deze aanvraag/reactie is moeilijk te meten. U kunt uw client code instrumenteren om grote aanvragen en antwoorden bij te houden.

Resoluties voor grote antwoord grootten zijn variërend, maar zijn onder andere:

1. Optimaliseer uw toepassing voor een groot aantal kleine waarden, in plaats van een paar grote waarden.
    - De aanbevolen oplossing is het opsplitsen van uw gegevens in gerelateerde kleinere waarden.
    - Zie het bericht [Wat is de ideale waarde voor redis? Is 100 KB te groot?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) voor meer informatie over hoe kleinere waarden worden aanbevolen.
1. Verhoog de grootte van uw virtuele machine om betere bandbreedte mogelijkheden te verkrijgen
    - Meer band breedte op de client of Server-VM kan de gegevens overdrachts tijden voor grotere reacties verminderen.
    - Vergelijk uw huidige netwerk gebruik op beide machines met de limieten van uw huidige VM-grootte. Meer band breedte op alleen de server of alleen op de client is niet voldoende.
1. Verhoog het aantal verbindings objecten dat door uw toepassing wordt gebruikt.
    - Gebruik een Round Robin-benadering om aanvragen via verschillende verbindings objecten te maken.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan serverzijde oplossen](cache-troubleshoot-server.md)
- [Hoe kan ik Bench Mark en test de prestaties van mijn cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
