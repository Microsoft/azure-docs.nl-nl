---
title: Veelgestelde vragen over Azure cache voor redis planning
description: Meer informatie over de antwoorden op veelgestelde vragen die u helpen bij het plannen van Azure cache voor redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537402"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Veelgestelde vragen over Azure cache voor redis planning

In dit artikel vindt u antwoorden op veelgestelde vragen over het plannen van Azure-cache voor redis.

## <a name="common-questions-and-answers"></a>Veelgestelde vragen en antwoorden
In deze sectie worden de volgende veelgestelde vragen behandeld:

* [Azure cache voor redis-prestaties](#azure-cache-for-redis-performance)
* [In welke regio moet ik mijn cache vinden?](#in-what-region-should-i-locate-my-cache)
* [Waar bevinden de gegevens zich in de cache?](#where-do-my-cached-data-reside)
* [Hoe word ik gefactureerd voor Azure-cache voor redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Kan ik Azure cache gebruiken voor redis met Azure Government Cloud, Azure China 21Vianet Cloud of Microsoft Azure Duitsland?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure cache voor redis-prestaties
De volgende tabel bevat de maximum waarden voor de band breedte tijdens het testen van verschillende groottes van de standaard-en Premium-caches met behulp `redis-benchmark.exe` van een IaaS-VM op basis van de Azure-cache voor redis-eind punt. Voor TLS-door Voer wordt redis-Bench Mark gebruikt met stunnel om verbinding te maken met het Azure-cache geheugen voor redis-eind punten.

>[!NOTE] 
>Deze waarden zijn niet gegarandeerd en er is geen SLA voor deze getallen, maar het moet gebruikelijk zijn. U moet de test uw eigen toepassing laden om de juiste cache grootte voor uw toepassing te bepalen.
>Deze nummers kunnen veranderen wanneer er periodiek nieuwere resultaten worden geplaatst.
>

In deze tabel kunnen we de volgende conclusies tekenen:

* De door Voer voor de caches die dezelfde grootte hebben, is hoger in de laag Premium, vergeleken met de laag standaard. Met een cache van 6 GB is de door Voer van P1 bijvoorbeeld 180.000 aanvragen per seconde (RPS) in vergelijking met 100.000 RPS voor C3.
* Met redis clustering neemt de door Voer lineair toe als u het aantal Shards (knoop punten) in het cluster verhoogt. Als u bijvoorbeeld een P4-cluster van 10 Shards maakt, is de beschik bare door Voer 400.000 * 10 = 4.000.000 RPS.
* De door Voer voor grotere sleutel grootten is hoger in de Premium-laag, vergeleken met de Standard-laag.

| Prijscategorie | Grootte | CPU-kernen | Beschik bare band breedte | 1-grootte van KB-waarde | 1-grootte van KB-waarde |
| --- | --- | --- | --- | --- | --- |
| **Standaard cache grootten** | | |**Megabits per seconde (MB/s)/mega bytes per seconde (MB/s)** |**Aantal aanvragen per seconde (RPS) niet-SSL** |**SSL-aanvragen per seconde (RPS)** |
| C0 | 250 MB | Gedeeld | 100/12,5  |  15.000 |   7\.500 |
| C1 |   1 GB | 1      | 500/62,5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41.000 |  37.000 |
| Stand |   6 GB | 4      | 1000/125  | 100.000 |  90,000 |
| C4 |  13 GB | 2      | 500/62,5  |  60.000 |  55.000 |
| C5 |  26 GB | 4      | 1.000/125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2.000/250 | 126.000 | 120.000 |
| **Premium-cache grootten** | |**CPU-kernen per Shard** | **Megabits per seconde (MB/s)/mega bytes per seconde (MB/s)** |**Aanvragen per seconde (RPS) niet-SSL, per Shard** |**SSL-aanvragen per seconde (RPS) per Shard** |
| P1 |   6 GB |  2 | 1.500/187,5 | 180.000 | 172.000 |
| P2 |  13 GB |  4 | 3.000/375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3.000/375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6.000/750   | 400,000 | 373.000 |
| P5 | 120 GB | 20 | 6.000/750   | 400,000 | 373.000 |

Voor instructies over het instellen van stunnel of het downloaden van de redis-hulpprogram ma's zoals `redis-benchmark.exe` , Zie [Hoe kan ik redis-opdrachten uitvoeren?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>In welke regio moet ik mijn cache vinden?
Voor de beste prestaties en de laagste latentie zoekt u uw Azure-cache voor redis in dezelfde regio als uw cache-client toepassing.

### <a name="where-do-my-cached-data-reside"></a>Waar bevinden de gegevens zich in de cache?
Met Azure cache voor redis worden uw toepassings gegevens opgeslagen in het RAM-geheugen van de virtuele machine of Vm's, afhankelijk van de laag die uw cache host. Uw gegevens bevinden zich in de Azure-regio die u standaard hebt geselecteerd. Er zijn twee gevallen waarin uw gegevens een regio kunnen verlaten:
* Wanneer u persistentie op de cache inschakelt, maakt Azure cache voor redis een back-up van uw gegevens naar een Azure Storage account dat u bezit. Als het opslag account dat u opgeeft, zich in een andere regio bevindt, wordt er een kopie van uw gegevens weer gegeven.
* Als u geo-replicatie instelt en de secundaire cache zich in een andere regio bevindt, wat normaal het geval is, worden uw gegevens gerepliceerd naar deze regio.

U moet Azure-cache expliciet configureren voor redis om deze functies te kunnen gebruiken. Daarnaast hebt u volledige controle over de regio waarin het opslag account of de secundaire cache zich bevindt.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Hoe word ik gefactureerd voor Azure-cache voor redis?
Azure cache voor redis-prijzen is [hier](https://azure.microsoft.com/pricing/details/cache/). De prijzen pagina vermeldt prijs per uur en maandelijks tarief. Caches worden per minuut in rekening gebracht vanaf het moment dat de cache wordt gemaakt tot het moment dat een cache wordt verwijderd. Er is geen optie voor het stoppen of onderbreken van de facturering van een cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Kan ik Azure cache gebruiken voor redis met Azure Government Cloud, Azure China 21Vianet Cloud of Microsoft Azure Duitsland?
Ja, Azure cache voor redis is beschikbaar in Azure Government Cloud, Azure China 21Vianet-Cloud en Microsoft Azure Duitsland. De Url's voor toegang tot en beheer van Azure cache voor redis verschillen in de clouds in vergelijking met de open bare Azure-Cloud.

| Cloud   | DNS-achtervoegsel voor redis            |
|---------|---------------------------------|
| Openbaar  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Duitsland | *. redis.cache.cloudapi.de       |
| China   | *. redis.cache.chinacloudapi.cn  |

Zie de volgende koppelingen voor meer informatie over overwegingen bij het gebruik van Azure cache voor redis met andere Clouds.

- [Azure Government-data bases-Azure cache voor redis](../azure-government/compare-azure-government-global-azure.md)
- [Azure China 21Vianet Cloud-Azure cache voor redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)

Zie [verbinding maken met andere Clouds-Azure cache voor redis Power shell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)voor meer informatie over het gebruik van Azure cache voor redis met Power shell in azure Government Cloud, Azure China 21Vianet-cloud en Microsoft Azure Duitsland.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Veelgestelde vragen over andere Azure-caches voor redis](cache-faq.md).