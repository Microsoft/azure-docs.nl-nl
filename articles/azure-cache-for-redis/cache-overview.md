---
title: Wat is Azure Cache voor Redis?
description: Kom erachter hoe u met Azure Cache voor Redis cache-aside, cacheopslag van inhoud, gebruikerssessie opslaan in cache, wachtrij met taken en berichten, en gedistribueerde transacties kunt inschakelen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 225df0dc53a0386bb53576970a1b6330351f4545
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184124"
---
# <a name="azure-cache-for-redis"></a>Azure Cache voor Redis
Azure Cache voor Redis biedt gegevensopslag in het geheugen op basis van de [Redis](https://redis.io/)-software. Redis verbetert de prestaties en schaalbaarheid van toepassingen die gebruik maken van back-end-gegevensarchieven. Het kan grote hoeveelheden toepassingsaanvragen verwerken door vaak gebruikte gegevens in het servergeheugen te bewaren zodat deze snel kunnen worden geschreven en gelezen. Redis is een essentiële oplossing voor gegevensopslag met lage latentie en hoge doorvoer voor moderne toepassingen.

Azure Cache voor Redis biedt zowel het opensource-product als het commerciële product van Redis Labs als een beheerde service. Het biedt veilige en toegewezen Redis-serverinstanties en volledige Redis API-compatibiliteit. De service wordt beheerd door Microsoft, gehost op Azure en is toegankelijk voor alle toepassingen van Azure en daarbuiten.

Azure Cache voor Redis kan worden gebruikt als gedistribueerde gegevens- of inhoudscache, sessieopslag, berichtenbroker en meer. Het kan zelfstandig worden geïmplementeerd of naast andere databaseservice van Azure, zoals Azure SQL of Cosmos DB.

## <a name="key-scenarios"></a>Belangrijke scenario's
Azure Cache voor Redis verbetert de prestaties van toepassingen door veelgebruikte architectuurpatronen van toepassingen te ondersteunen. Sommige van de meest voorkomende zijn de volgende:

| Patroon      | Beschrijving                                        |
| ------------ | -------------------------------------------------- |
| [Gegevenscache](cache-web-app-cache-aside-leaderboard.md) | Databases zijn vaak te groot om rechtstreeks in een cache te laden. Het is gebruikelijk om het [cache-aside](/azure/architecture/patterns/cache-aside)-patroon te gebruiken om alleen de benodigde gegevens in de cache te laden. Als het systeem wijzigingen aanbrengt in de gegevens, kan het ook de cache bijwerken, die vervolgens wordt gedistribueerd naar andere clients. Het systeem kan bovendien een vervaldatum instellen voor gegevens of een verwijderingsbeleid gebruiken om gegevensupdates in de cache te activeren.|
| [Inhoudscache](cache-aspnet-output-cache-provider.md) | Veel webpagina's worden gegenereerd op basis van sjablonen die gebruikmaken van statische inhoud, zoals kopteksten, voetteksten en banners. Deze statische items worden meestal niet vaak bijgewerkt. Cache in het geheugen biedt snelle toegang tot statische inhoud vergeleken met back-endgegevensarchieven. Dit patroon vermindert de verwerkingstijd en serverbelasting, waardoor webservers sneller kunnen reageren. Zo hebt u minder servers nodig om belasting te verwerken. Azure Cache voor Redis biedt de Redis Output Cache Provider om dit patroon te ondersteunen met ASP.NET.|
| [Sessieopslag](cache-aspnet-session-state-provider.md) | Dit patroon wordt vaak gebruikt met winkelwagens en andere gegevens van de gebruikersgeschiedenis die een webtoepassing mogelijk wil koppelen aan gebruikerscookies. Te veel informatie opslaan in een cookie kan negatieve gevolgen hebben voor de prestaties als de cookie groter wordt en bij elke aanvraag wordt doorgegeven en gevalideerd. Een gangbare oplossing is om de cookie als sleutel te gebruiken voor het opvragen van gegevens in een database. Het gebruik van een cache in het geheugen zoals Azure Cache voor Redis om gegevens te koppelen aan een gebruiker, is veel sneller dan interactie met een volledige relationele database. |
| Wachtrij met taken en berichten | Toepassingen voegen taken vaak toe aan een wachtrij als er tijd nodig is om de bewerkingen van een bepaalde aanvraag uit te voeren. Langdurige bewerkingen worden in de wachtrij gezet en op volgorde verwerkt, vaak door een andere server.  Deze methode van werk uitstellen heet taken in de wachtrij plaatsen. Azure Cache voor Redis biedt een gedistribueerde wachtrij om dit patroon in te schakelen in uw toepassing.|
| Gedistribueerde transacties | Toepassingen vereisen soms een reeks opdrachten voor een back-endgegevensopslag die moeten worden uitgevoerd als één atomische bewerking. Alle opdrachten moeten slagen of alle moet worden teruggezet naar de beginstatus. Azure Cache voor Redis ondersteunt het uitvoeren van een batch met opdrachten als één [transactie](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Redis-versies

Azure Cache voor Redis ondersteunt OSS Redis versie 4.x en, als preview, 6.0. We hebben besloten Redis 5.0 over te slaan om u te voorzien van de nieuwste versie. Voorheen werd in Azure Cache voor Redis slechts één Redis-versie bijhouden. Het biedt een nieuwere, grote release-upgrade en ten minste één oudere, stabiele versie die langer wordt ondersteund. U kunt [kiezen welke versie](cache-how-to-version.md) het beste werkt voor uw toepassing.


## <a name="service-tiers"></a>Servicelagen
Azure Cache voor Redis is beschikbaar in de volgende lagen:

| Laag | Beschrijving |
|---|---|
| Basic | Een OSS Redis-cache die op één VM wordt uitgevoerd. Deze laag heeft geen service level agreement (SLA) en is ideaal voor ontwikkeling/test en niet-essentiële werkbelastingen. |
| Standard | Een OSS Redis-cache die wordt uitgevoerd op twee VM’s in een gerepliceerde configuratie. |
| Premium | Krachtige OSS Redis-caches. Deze laag biedt hogere doorvoer, lagere latentie, betere beschikbaarheid en meer functies. Premium-caches worden geïmplementeerd op krachtigere VM's vergeleken met die voor Basic-of Standard-caches. |
| Enterprise | Krachtige caches worden mogelijk gemaakt door de Redis Enterprise-software van Redis Labs. Deze laag ondersteunt Redis-modules, waaronder RediSearch, RedisBloom en RedisTimeSeries. Daarnaast biedt het een nog hogere beschikbaarheid dan de Premium-laag. |
| Enterprise Flash | Rendabele grote caches mogelijk gemaakt door Redis Enterprise-software van Redis Labs. Deze laag breidt Redis-gegevensopslag uit naar niet-vluchtig geheugen, wat goedkoper is dan DRAM, op een VM. Dit vermindert de totale kosten per GB geheugen. |

### <a name="feature-comparison"></a>Vergelijking van functies
[Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) bevat een gedetailleerde vergelijking van elke laag. In de volgende tabel worden de functies beschreven die door elke laag worden ondersteund:

| Omschrijving | Basic | Standard | Premium | Enterprise | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Gegevensversleuteling |✔|✔|✔|✔|✔|
| [Netwerkisolatie](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Schalen](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [Zoneredundantie](cache-how-to-zone-redundancy.md) |-|-|✔|✔|✔|
| [Geo-replicatie](cache-how-to-geo-replication.md) |-|-|✔|-|-|
| [Gegevenspersistentie](cache-how-to-premium-persistence.md) |-|-|✔|-|-|
| [OSS-cluster](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Modules](https://redis.io/modules) |-|-|-|✔|-|
| [Import/export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Geplande updates](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>De juiste laag kiezen
Houd rekening met het volgende bij het kiezen van een laag voor Azure Cache voor Redis:

* **Geheugen**: De lagen Basic en Standard bieden 250 MB – 53 GB; de laag Premium 6 GB - 1,2 TB; de Enterprise-lagen 12 GB - 14 TB.  Als u een cache van een Premium-laag wilt maken die groter is dan 120 GB, kunt u Redis OSS-clustering gebruiken. Zie [Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) voor meer informatie. Zie [Clustering voor een Premium Azure Cache voor Redis configureren](cache-how-to-premium-clustering.md) voor meer informatie.
* **Netwerkprestaties**: Als u een werkbelasting hebt waarvoor hoge doorvoer is vereist, bieden de Premium- of Enterprise-lagen meer bandbreedte vergeleken met Standard of Basic. Daarnaast hebben grotere caches in elke laag meer bandbreedte vanwege de onderliggende virtuele machine die als host fungeert voor de cache. Zie [Prestaties van Azure Cache voor Redis](cache-planning-faq.md#azure-cache-for-redis-performance) voor meer informatie.
* **Doorvoer**: De laag Premium biedt de maximaal beschikbare doorvoer. Als de cacheserver of client de bandbreedtelimiet bereikt, krijgt u mogelijk time-outs aan de clientzijde. Zie de volgende tabel voor meer informatie.
* **Hoge beschikbaarheid**: Azure Cache voor Redis biedt meerdere opties voor [hoge beschikbaarheid](cache-high-availability.md). Het garandeert dat een Standard-, Premium-, of Enterprise-cache beschikbaar is volgens onze [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). De SLA heeft alleen betrekking op connectiviteit met de cache-eindpunten. De SLA heeft geen betrekking op beveiliging tegen gegevensverlies. We raden u aan de Redis-functie voor gegevenspersistentie in de laag Premium te gebruiken om de tolerantie voor gegevensverlies te vergroten.
* **Gegevenspersistentie**: Met Premium kunt u de cachegegevens in een Azure Storage-account bewaren. In andere lagen worden gegevens alleen in het geheugen opgeslagen. Onderliggende problemen met de infrastructuur kunnen leiden tot mogelijk gegevensverlies. We raden u aan de Redis-functie voor gegevenspersistentie in de laag Premium te gebruiken om de tolerantie voor gegevensverlies te vergroten. Azure Cache voor Redis biedt RDB- en AOF-opties (preview-versie) voor Redis-persistentie. Zie [Persistentie configureren voor een Premium Azure Cache voor Redis](cache-how-to-premium-persistence.md) voor meer informatie.
* **Netwerkisolatie**: Azure Private Link en Virtual Network (VNET)-implementaties bieden verbeterde beveiliging en verkeersisolatie voor uw Azure Cache voor Redis. Met VNET kunt u de toegang verder beperken via beleid voor netwerktoegangsbeheer. Zie voor meer informatie [Azure Cache voor Redis met Azure Private Link](cache-private-link.md) en [Hoe u Virtual Network-ondersteuning kunt configureren voor een Premium Azure Cache voor Redis](cache-how-to-premium-vnet.md).
* **Maximumaantal clientverbindingen**: De laag Premium biedt het maximumaantal clients dat verbinding kan maken met Redis, met een groter aantal verbindingen voor caches met een grotere omvang. Bij clustering wordt het aantal beschikbare verbindingen voor een geclusterde cache niet verhoogd. Zie [Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) voor meer informatie.
* **Toegewezen kerngeheugen voor Redis-server**: Alle caches behalve C0 voeren toegewezen VM-kernen uit.
* **Verwerking met één thread**: Redis maakt standaard gebruik van slechts één thread voor de verwerking van opdrachten. Azure Cache voor Redis gebruikt ook extra kerngeheugens voor I/O-verwerking. Het gebruik van verschillende kerngeheugens verbetert de doorvoerprestaties, ook al maakt het geen lineaire schaling mogelijk. Daarnaast worden grotere VM-grootten meestal met hogere bandbreedtelimieten geleverd dan kleinere. Zo kunt u oververzadiging van het netwerk voorkomen, waardoor er time-outs optreden in uw toepassing.
* **Prestatieverbeteringen**: Caches in de Premium- en Enterprise-lagen worden geïmplementeerd op hardware met snellere processors en bieden daarom betere prestaties in vergelijking met Basic of Standard. Premium caches hebben een hogere doorvoer en een lagere latentie. Zie [Prestaties van Azure Cache voor Redis](cache-planning-faq.md#azure-cache-for-redis-performance) voor meer informatie.

U kunt uw cache schalen van de Basic-laag tot aan Premium, nadat deze is gemaakt. Omlaag schalen naar een lagere laag wordt niet ondersteund. Zie voor stapsgewijze instructies [De schaal aanpassen van Azure Cache voor Redis](cache-how-to-scale.md) en [Een schaalbewerking automatiseren](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="enterprise-tier-requirements"></a>Vereisten voor de Enterprise-laag

De Enterprise-lagen maken gebruik van Redis Enterprise, een commerciële versie van Redis van Redis Labs. Klanten ontvangen en betalen voor een licentie voor deze software via een Azure Marketplace-aanbieding. Azure Cache voor Redis faciliteert de aanschaf van de licentie, zodat u dit niet afzonderlijk hoeft te doen. Als u wilt kopen op Azure Marketplace, moet u over de volgende vereisten beschikken:
* Uw Azure-abonnement heeft een geldig betaal middel. Azure-tegoeden of gratis MSDN-abonnementen worden niet ondersteund.
* U bent Eigenaar of Inzender van het abonnement.
* Uw organisatie staat [Azure Marketplace-aankopen](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases) toe.
* Als u een privé Marketplace gebruikt, moet deze de Redis Labs Enterprise-aanbieding bevatten.

## <a name="next-steps"></a>Volgende stappen
* [Een instantie van Azure Cache voor Redis maken](quickstart-create-redis.md)
* [Een cache voor een Enterprise-laag maken](quickstart-create-redis-enterprise.md)
* [Azure Cache voor Redis in een ASP.NET-web-app gebruiken](cache-web-app-howto.md)
* [Azure Cache voor Redis in .NET Core gebruiken](cache-dotnet-core-quickstart.md)
* [Azure Cache voor Redis in .NET Framework gebruiken](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Azure Cache voor Redis in Node.js gebruiken](cache-nodejs-get-started.md)
* [Azure Cache voor Redis in Java gebruiken](cache-java-get-started.md)
* [Azure Cache voor Redis in Python gebruiken](cache-python-get-started.md)