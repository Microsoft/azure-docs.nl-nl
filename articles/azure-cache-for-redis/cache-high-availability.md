---
title: Hoge beschikbaarheid voor Azure Cache voor Redis
description: Meer informatie Azure Cache voor Redis functies en opties voor hoge beschikbaarheid
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 6c44c87221442797f063877385ac5eb7f8585850
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719093"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Hoge beschikbaarheid voor Azure Cache voor Redis

Azure Cache voor Redis heeft ingebouwde hoge beschikbaarheid. Het doel van de architectuur voor hoge beschikbaarheid is ervoor te zorgen dat uw beheerde Redis-exemplaar werkt, zelfs wanneer de onderliggende virtuele machines (VM's) worden beïnvloed door geplande of ongeplande uitval. Het biedt veel hogere percentages dan wat u kunt bereiken door Redis op één VM te hosten.

Azure Cache voor Redis implementeert hoge beschikbaarheid met behulp van meerdere VM's, *knooppunten* genoemd, voor een cache. Deze knooppunten worden zodanig geconfigureerd dat gegevensreplicatie en failover op gecoördineerde manieren plaatsvinden. Het orkestreert ook onderhoudsbewerkingen zoals Redis-softwarepatching. Er zijn verschillende opties voor hoge beschikbaarheid beschikbaar in de lagen Standard, Premium en Enterprise:

| Optie | Beschrijving | Beschikbaarheid | Standard | Premium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standaardreplicatie](#standard-replication)| Configuratie met twee knooppunt gerepliceerd in één datacenter met automatische failover | 99,9% (zie [details](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |✔|✔|-|
| [Zoneredundantie](#zone-redundancy) | Configuratie met meerdere knooppuntrepliceerde knooppunt in meerdere AZ's, met automatische failover | Maximaal 99,99% (zie [details](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |-|Preview|✔|
| [Geo-replicatie](#geo-replication) | Gekoppelde cache-exemplaren in twee regio's, met door de gebruiker beheerde failover | Maximaal 99,999% (zie [details](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)) |-|✔|Preview|

## <a name="standard-replication"></a>Standaardreplicatie

Een Azure Cache voor Redis in de Standard- of Premium-laag wordt standaard uitgevoerd op een paar Redis-servers. De twee servers worden gehost op toegewezen VM's. Met Opensource Redis kan slechts één server gegevens schrijven aanvragen verwerken. Deze server is het *primaire knooppunt,* terwijl de andere *replica is.* Nadat de serverknooppunten zijn Azure Cache voor Redis er primaire en replicarollen aan toegewezen. Het primaire knooppunt is meestal verantwoordelijk voor het onderhouden van schrijf- en leesaanvragen van Redis-clients. Bij een schrijfbewerking worden een nieuwe sleutel en een sleutelupdate aan het interne geheugen toegevoegd en wordt er onmiddellijk op de client gereageerd. De bewerking wordt asynchroon naar de replica doorgestuurd.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Gegevensreplicatie instellen":::
   
>[!NOTE]
>Normaal gesproken communiceert een Redis-client met het primaire knooppunt in een Redis-cache voor alle lees- en schrijfaanvragen. Bepaalde Redis-clients kunnen worden geconfigureerd voor het lezen van het replica-knooppunt.
>
>

Als het primaire knooppunt in een Redis-cache niet beschikbaar is, wordt de replica automatisch het nieuwe primaire knooppunt. Dit proces wordt een *failover genoemd.* De replica wacht voldoende lang voordat het overgenomen wordt, voor het geval het primaire knooppunt snel wordt hersteld. Wanneer er een failover wordt Azure Cache voor Redis een nieuwe VM in en wordt deze als het replica-knooppunt aan de cache geplaatst. De replica voert een volledige gegevenssynchronisatie uit met de primaire replica, zodat deze een andere kopie van de cachegegevens heeft.

Een primair knooppunt kan buiten gebruik worden genomen als onderdeel van een geplande onderhoudsactiviteit, zoals Redis-software of een update van het besturingssysteem. Het werkt mogelijk ook niet meer vanwege niet-geplande gebeurtenissen, zoals fouten in onderliggende hardware, software of netwerk. [Failover en patching voor Azure Cache voor Redis](cache-failover.md) biedt een gedetailleerde uitleg over de typen Redis-failovers. Een Azure Cache voor Redis een groot aantal failovers tijdens de levensduur. De architectuur voor hoge beschikbaarheid is ontworpen om deze wijzigingen in een cache zo transparant mogelijk te maken voor de clients.

>[!NOTE]
>Het volgende is beschikbaar als preview-versie.
>
>

Bovendien kunt Azure Cache voor Redis extra replicaknooppunten in de Premium-laag. Een [cache met meerdere replica's](cache-how-to-multi-replicas.md) kan worden geconfigureerd met maximaal drie replicaknooppunten. Het gebruik van meer replica's verbetert over het algemeen de tolerantie vanwege de extra knooppunten die een back-up maken van de primaire knooppunten. Zelfs met meer replica's kan een Azure Cache voor Redis exemplaar nog steeds ernstig worden beïnvloed door een storing op datacenter- of AZ-niveau. U kunt de beschikbaarheid van de cache vergroten met behulp van meerdere replica's in combinatie met [zone-redundantie.](#zone-redundancy)

## <a name="zone-redundancy"></a>Zoneredundantie

Azure Cache voor Redis ondersteunt zone-redundante configuraties in de Premium- en Enterprise-lagen. Een [zone-redundante cache](cache-how-to-zone-redundancy.md) kan de knooppunten op verschillende [Azure-beschikbaarheidszones](../availability-zones/az-overview.md) in dezelfde regio plaatsen. Het elimineert datacenter- of AZ-uitval als single point of failure en verhoogt de algehele beschikbaarheid van uw cache.

### <a name="premium-tier"></a>Premium-laag

>[!NOTE]
>Dit is beschikbaar als preview-versie.
>
>

In het volgende diagram ziet u de zone-redundante configuratie voor de Premium-laag:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Zone-redundantie instellen":::
   
Azure Cache voor Redis verdeelt knooppunten in een zone-redundante cache op een round robin-manier over de AZ's die u hebt geselecteerd. Ook wordt bepaald welk knooppunt in eerste instantie als primair moet fungeren.

Een zone-redundante cache biedt automatische failover. Wanneer het huidige primaire knooppunt niet beschikbaar is, neemt een van de replica's het over. Uw toepassing heeft mogelijk een hogere reactietijd voor de cache als het nieuwe primaire knooppunt zich in een andere AZ bevindt. AZ's zijn geografisch gescheiden. Als u overschakelt van de ene AZ naar de andere, verandert de fysieke afstand tussen de locatie waar uw toepassing en de cache worden gehost. Deze wijziging is van invloed op retournetwerklatentie van uw toepassing naar de cache. De extra latentie valt naar verwachting binnen een acceptabel bereik voor de meeste toepassingen. U wordt aangeraden uw toepassing te testen om er zeker van te zijn dat deze goed kan presteren met een zone-redundante cache.

### <a name="enterprise-tiers"></a>Enterprise-lagen

Een cache in beide Enterprise-lagen wordt uitgevoerd op een Redis Enterprise-cluster. Er is altijd een oneven aantal serverknooppunten nodig om een quorum te vormen. Deze bestaat standaard uit drie knooppunten, die elk worden gehost op een toegewezen VM. Een Enterprise-cache heeft twee gegevensknooppunten van *dezelfde grootte* en één kleiner *quorumknooppunt.* Een Enterprise Flash cache heeft drie gegevensknooppunten van dezelfde grootte. Het Enterprise-cluster verdeelt Redis-gegevens intern in partities. Elke partitie heeft *een primaire* en ten minste één *replica.* Elk gegevens knooppunt bevat een of meer partities. Het Enterprise-cluster zorgt ervoor dat de primaire en replica(s) van een partitie nooit op hetzelfde gegevensknooppunt worden geplaatst. Partities repliceren gegevens asynchroon van de primaireries naar de bijbehorende replica's.

Wanneer een gegevens knooppunt niet meer beschikbaar is of er een netwerksplitsing plaatsvindt, vindt er een failover plaats die vergelijkbaar is met de failover die wordt beschreven in [Standaardreplicatie.](#standard-replication) Het Enterprise-cluster maakt gebruik van een quorummodel om te bepalen welke overlevende knooppunten deelnemen aan een nieuw quorum. Ook worden replicapartities binnen deze knooppunten naar behoefte gepromoot tot voorveren.

## <a name="geo-replication"></a>Geo-replicatie

[Geo-replicatie](cache-how-to-geo-replication.md) is een mechanisme voor het koppelen van twee of meer Azure Cache voor Redis exemplaren, meestal verspreid over twee Azure-regio's. 

### <a name="premium-tier"></a>Premium-laag

>[!NOTE]
>Geo-replicatie in de Premium-laag is hoofdzakelijk ontworpen voor herstel na noodherstel.
>
>

Twee cache-exemplaren van de Premium-laag kunnen worden verbonden via [geo-replicatie,](cache-how-to-geo-replication.md) zodat u een back-up kunt maken van uw cachegegevens naar een andere regio. Zodra het ene exemplaar aan elkaar is gekoppeld, wordt het ene exemplaar aangewezen als de primaire gekoppelde cache en de andere als de secundaire gekoppelde cache. Alleen de primaire cache accepteert lees- en schrijfaanvragen. Gegevens die naar de primaire cache worden geschreven, worden gerepliceerd naar de secundaire cache. Een toepassing heeft toegang tot de cache via afzonderlijke eindpunten voor de primaire en secundaire caches. De toepassing moet alle schrijfaanvragen verzenden naar de primaire cache wanneer deze is geïmplementeerd in meerdere Azure-regio's. Het kan lezen uit de primaire of secundaire cache. Over het algemeen wilt u de reken-exemplaren van uw toepassing lezen uit de dichtstbijzijnde caches om de latentie te verminderen. Gegevensoverdracht tussen de twee cache-exemplaren wordt beveiligd door TLS.

Geo-replicatie biedt geen automatische failover vanwege problemen met de retourtijd van het toegevoegde netwerk tussen regio's als de rest van uw toepassing in de primaire regio blijft. U moet de failover beheren en initiëren door de secundaire cache los te koppelen. Hierdoor wordt het nieuwe primaire exemplaar.

### <a name="enterprise-tiers"></a>Enterprise-lagen

>[!NOTE]
>Deze is beschikbaar als preview-versie.
>
>

De Enterprise-lagen ondersteunen een geavanceerdere vorm van geo-replicatie, actieve [geo-replicatie genoemd.](cache-how-to-active-geo-replication.md) De Redis Enterprise-software maakt gebruik van conflictvrije gerepliceerde gegevenstypen en ondersteunt schrijf-naar-meerdere cache-exemplaren en zorgt voor het samenvoegen van wijzigingen en het oplossen van conflicten, indien nodig. Twee of meer cache-exemplaren van de Enterprise-laag in verschillende Azure-regio's kunnen worden samengevoegd om een actieve geo-gerepliceerde cache te vormen. Een toepassing die een dergelijke cache gebruikt, kan lezen en schrijven naar de geografisch gedistribueerde cache-exemplaren via bijbehorende eindpunten. Er moet worden gebruikt wat het dichtst bij elk reken-exemplaar ligt, waardoor de laagste latentie wordt bereikt. De toepassing moet ook de cache-exemplaren bewaken en overschakelen naar een andere regio als een van de exemplaren niet meer beschikbaar is. Zie [Active-Active Geo-Distriubtion (CRDTs-Based) voor](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/)meer informatie over hoe actieve geo-replicatie werkt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van Azure Cache voor Redis opties voor hoge beschikbaarheid.

* [Azure Cache voor Redis Premium-servicelagen](cache-overview.md#service-tiers)
* [Replica's toevoegen aan Azure Cache voor Redis](cache-how-to-multi-replicas.md)
* [Zone-redundantie inschakelen voor Azure Cache voor Redis](cache-how-to-zone-redundancy.md)
* [Geo-replicatie instellen voor Azure Cache voor Redis](cache-how-to-geo-replication.md)
