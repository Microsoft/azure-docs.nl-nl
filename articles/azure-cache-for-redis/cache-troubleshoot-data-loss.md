---
title: Problemen met gegevensverlies in Azure Cache voor Redis oplossen
description: Meer informatie over het oplossen van problemen met gegevens verlies met Azure cache voor redis, zoals gedeeltelijk verlies van sleutels, verval datum van sleutel of volledig verlies van sleutels.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6db036752bab7b84b72a37b148eaec7aa5765ef3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92538592"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Problemen met gegevensverlies in Azure Cache voor Redis oplossen

In dit artikel wordt beschreven hoe u de werkelijke of waargenomen gegevens verliezen die zich kunnen voordoen in azure cache voor redis kunt vaststellen.

> [!NOTE]
> Enkele van de stappen voor probleem oplossing in deze hand leiding bevatten instructies voor het uitvoeren van redis-opdrachten en het controleren van verschillende prestatie gegevens. Zie de artikelen in de sectie met [aanvullende informatie](#additional-information) voor meer informatie en instructies.
>

## <a name="partial-loss-of-keys"></a>Gedeeltelijk verlies van sleutels

Azure cache voor redis verwijdert geen wille keurige sleutels nadat ze zijn opgeslagen in het geheugen. Er worden echter wel sleutels verwijderd als reactie op verloop-of verwijderings beleid en op expliciete opdrachten voor het verwijderen van sleutels. Sleutels die zijn geschreven naar het primaire knoop punt in een Premium-of Standard Azure-cache voor redis-exemplaar, zijn ook mogelijk niet direct beschikbaar voor een replica. Gegevens worden op asynchrone en niet-blokkerende wijze gerepliceerd van de primaire naar de replica.

Als u ontdekt dat sleutels zijn verdwenen uit uw cache, controleert u de volgende mogelijke oorzaken:

| Oorzaak | Description |
|---|---|
| [Verval datum van de sleutel](#key-expiration) | Sleutels worden verwijderd omdat er time-outs zijn ingesteld. |
| [Sleutel verwijdering](#key-eviction) | Sleutels worden verwijderd onder geheugen belasting. |
| [Sleutel verwijderen](#key-deletion) | Sleutels worden verwijderd door expliciete Delete-opdrachten. |
| [Asynchrone replicatie](#async-replication) | Sleutels zijn niet beschikbaar op een replica vanwege vertragingen in de gegevens replicatie. |

### <a name="key-expiration"></a>Verval datum van de sleutel

Azure cache voor redis verwijdert automatisch een sleutel als aan de sleutel een time-out wordt toegewezen en die periode is verstreken. Zie de documentatie van de [verlopende](https://redis.io/commands/expire) opdracht voor meer informatie over de verval datum van de redis-sleutel. Time-outwaarden kunnen ook worden ingesteld met behulp van de [set](https://redis.io/commands/set)-, [SETEX](https://redis.io/commands/setex)-, [GETSET](https://redis.io/commands/getset)-en andere **\* Store** -opdrachten.

Gebruik de opdracht [info](https://redis.io/commands/info) om statistieken weer te geven over hoeveel sleutels zijn verlopen. In de `Stats` sectie wordt het totale aantal verlopen sleutels weer gegeven. De `Keyspace` sectie bevat meer informatie over het aantal sleutels met time-outs en de gemiddelde time-outwaarde.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

U kunt ook diagnostische gegevens voor uw cache bekijken om te zien of er een correlatie is tussen wanneer de sleutel ontbreekt en een piek in verlopen sleutels. Zie de bijlage over het [opsporen van fouten in redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) voor meer informatie over het gebruik van de opdracht regel meldingen of **monitor** om dit soort problemen op te lossen.

### <a name="key-eviction"></a>Sleutel verwijdering

Azure cache voor redis vereist geheugen ruimte voor het opslaan van gegevens. De sleutels worden verwijderd om zo nodig het geheugen beschikbaar te maken. Wanneer de **used_memory** -of **used_memory_rss** -waarden in de [info](https://redis.io/commands/info) opdracht de geconfigureerde **Maxmemory** -instelling benaderen, begint Azure cache voor redis het verwijderen van sleutels uit het geheugen op basis van het [cache beleid](https://redis.io/topics/lru-cache).

U kunt het aantal verwijderde sleutels bewaken met behulp van de opdracht [info](https://redis.io/commands/info) :

```
# Stats

evicted_keys:13224
```

U kunt ook diagnostische gegevens voor uw cache bekijken om te zien of er een correlatie is tussen wanneer de sleutel ontbreekt en een piek in Verwijderde sleutels. Zie de bijlage over het [opsporen van fouten in redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) voor meer informatie over het gebruik van de opdracht regel meldingen of **monitor** om dit soort problemen op te lossen.

### <a name="key-deletion"></a>Sleutel verwijderen

Redis-clients kunnen de opdracht [del](https://redis.io/commands/del) of [HDEL](https://redis.io/commands/hdel) uitgeven om sleutels expliciet te verwijderen uit Azure cache voor redis. U kunt het aantal verwijderings bewerkingen bijhouden met behulp van de opdracht [info](https://redis.io/commands/info) . Als **del** -of **HDEL** -opdrachten zijn aangeroepen, worden deze weer gegeven in de `Commandstats` sectie.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynchrone replicatie

Elk Azure-cache-exemplaar voor redis in de laag Standard of Premium is geconfigureerd met een primair knoop punt en ten minste één replica. Gegevens worden asynchroon van de primaire naar een replica gekopieerd met behulp van een achtergrond proces. De [redis.io](https://redis.io/topics/replication) -website beschrijft hoe redis-gegevens replicatie in het algemeen werkt. Voor scenario's waarin clients regel matig schrijven naar redis, kan gedeeltelijk gegevens verlies optreden omdat deze replicatie niet gegarandeerd onmiddellijk is. Bijvoorbeeld, als de primaire gaat omlaag *nadat* een client een sleutel naar de replica schrijft, maar *voordat* het achtergrond proces die sleutel kan verzenden naar de replica's, wordt de sleutel verwijderd wanneer de replica de nieuwe primaire gaat.

## <a name="major-or-complete-loss-of-keys"></a>Groot of volledig verlies van sleutels

Als de meeste of alle sleutels uit uw cache zijn verdwenen, controleert u de volgende mogelijke oorzaken:

| Oorzaak | Description |
|---|---|
| [Leegmaken van sleutel](#key-flushing) | Sleutels zijn hand matig verwijderd. |
| [Onjuiste database selectie](#incorrect-database-selection) | Azure cache voor redis is ingesteld op het gebruik van een niet-standaard database. |
| [Redis-instantie fout](#redis-instance-failure) | De redis-server is niet beschikbaar. |

### <a name="key-flushing"></a>Leegmaken van sleutel

Clients kunnen de opdracht [FLUSHDB](https://redis.io/commands/flushdb) aanroepen om alle sleutels in *één* data base of [FLUSHALL](https://redis.io/commands/flushall) te verwijderen om alle sleutels te verwijderen uit *alle* data bases in een redis-cache. Als u wilt weten of sleutels zijn leeg gemaakt, gebruikt u de opdracht [info](https://redis.io/commands/info) . De `Commandstats` sectie laat zien of de opdracht **Flush** is aangeroepen:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Onjuiste database selectie

Azure cache voor redis maakt standaard gebruik van de **db0** -data base. Als u overschakelt naar een andere data base (bijvoorbeeld **db1**) en probeert sleutels te lezen, worden deze daar niet gevonden door Azure cache voor redis. Elke Data Base is een logische afzonderlijke eenheid en bevat een andere gegevensset. Gebruik de [Select](https://redis.io/commands/select) -opdracht om andere beschik bare data bases te gebruiken en zoek naar sleutels in elk van deze.

### <a name="redis-instance-failure"></a>Redis-instantie fout

Redis is een gegevens archief in het geheugen. Gegevens worden bewaard op de fysieke of virtuele machines die de redis-cache hosten. Een Azure-cache voor het redis-exemplaar in de laag Basic kan slechts op één virtuele machine (VM) worden uitgevoerd. Als deze virtuele machine niet beschikbaar is, gaan alle gegevens die u in de cache hebt opgeslagen verloren. 

Caches in de Standard-en Premium-lagen bieden veel meer tolerantie tegen gegevens verlies met behulp van twee virtuele machines in een gerepliceerde configuratie. Wanneer het primaire knoop punt in een dergelijke cache mislukt, neemt het replica knooppunt de gegevens automatisch door. Deze Vm's bevinden zich in verschillende domeinen voor fouten en updates, om de kans te verkleinen dat ze niet tegelijkertijd niet meer beschikbaar zijn. Als er sprake is van een ernstige storing in het Data Center, kunnen de Vm's echter nog steeds samen gaan. De gegevens gaan in deze zeldzame gevallen verloren.

Overweeg het gebruik van [redis-gegevens persistentie](https://redis.io/topics/persistence) en [geo-replicatie](./cache-how-to-geo-replication.md) om de beveiliging van uw gegevens tegen deze infrastructuur fouten te verbeteren.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan serverzijde oplossen](cache-troubleshoot-server.md)
- [De juiste laag kiezen](cache-overview.md#choosing-the-right-tier)
- [Azure Cache voor Redis bewaken](cache-how-to-monitor.md)
- [Hoe kan ik redis-opdrachten uitvoeren?](cache-development-faq.md#how-can-i-run-redis-commands)