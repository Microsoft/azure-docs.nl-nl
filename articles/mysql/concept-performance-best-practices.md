---
title: Aanbevolen procedures voor prestaties-Azure Database for MySQL
description: In dit artikel worden enkele aanbevelingen beschreven voor het bewaken en afstemmen van de prestaties van uw Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7b5223bc08c470a0e8722b76b80473aaa235b51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727155"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Aanbevolen procedures voor optimale prestaties van uw Azure Database for MySQL-één server

Meer informatie over het verkrijgen van de beste prestaties bij het werken met uw Azure Database for MySQL-één-server. Wanneer we nieuwe mogelijkheden toevoegen aan het platform, zullen we de aanbevelingen in deze sectie blijven verfijnen.

## <a name="physical-proximity"></a>Fysieke nabijheid

 Zorg ervoor dat u een toepassing en de data base in dezelfde regio implementeert. Een snelle controle voordat u begint met het uitvoeren van prestatie benchmarking is het bepalen van de netwerk latentie tussen de client en de data base met behulp van een eenvoudige SELECT 1-query. 

## <a name="accelerated-networking"></a>Versneld netwerken

Gebruik versneld netwerken voor de toepassings server als u gebruikmaakt van Azure virtual machine, Azure Kubernetes of App Services. Versneld netwerken maken gebruik van I/O-virtualisatie met één hoofdmap (SR-IOV) naar een virtuele machine, waardoor de netwerk prestaties aanzienlijk worden verbeterd. Dit pad met hoge prestaties omzeilt de host van de DataPath, vermindert latentie, jitter en CPU-gebruik, voor gebruik met de meest veeleisende netwerk workloads op ondersteunde VM-typen.

## <a name="connection-efficiency"></a>Efficiëntie van verbinding

Het tot stand brengen van een nieuwe verbinding is altijd een kost bare en tijdrovende taak. Wanneer een toepassing een database verbinding aanvraagt, wordt er prioriteit gegeven aan de toewijzing van bestaande niet-actieve database verbindingen, in plaats van dat er een nieuwe wordt gemaakt.  Hier volgen enkele opties voor goede verbindings procedures:

- **ProxySQL**: gebruik [ProxySQL](https://proxysql.com/) die ingebouwde groepsgewijze verbindingen biedt en taak verdeling van [uw werk belasting](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) naar meerdere Lees-replica's, indien nodig op aanvraag, met eventuele wijzigingen in de toepassings code.

- **Heimdall data proxy**: u kunt ook gebruikmaken van Heimdall data proxy, een leverancier-onafhankelijke proxy oplossing. Het ondersteunt query caching en lezen/schrijven splitsen met detectie van replicatie vertraging. U kunt ook verwijzen naar [het versnellen van MySQL-prestaties met de Heimdall-proxy](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Permanente of Long-Lived verbinding**: als uw toepassing korte trans acties of query's met de uitvoerings tijd bevat < 5-10 MS, vervangt u vervolgens de korte verbindingen door permanente verbindingen. Voor het vervangen van korte verbindingen met permanente verbindingen zijn alleen kleine wijzigingen in de code vereist, maar dit heeft een grote invloed op de prestaties in veel typische toepassings scenario's. Zorg ervoor dat u de time-out of verbinding sluiten instelt wanneer de trans actie is voltooid.

- **Replica**: als u replica gebruikt, gebruikt u [ProxySQL](https://proxysql.com/) om de belasting tussen de primaire server en de Lees bare secundaire replica server af te sluiten. Meer informatie [over het instellen van ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Configuraties voor gegevens import

- U kunt uw exemplaar tijdelijk schalen naar een hogere SKU-grootte voordat u een bewerking voor het importeren van gegevens start en deze vervolgens schalen wanneer het importeren is voltooid.
- U kunt uw gegevens met minimale downtime importeren met behulp van [Azure database Migration service (DMS)](https://datamigration.microsoft.com/) voor online-of offline migraties. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Aanbevelingen voor Azure Database for MySQL geheugen

Een Azure Database for MySQL prestatie best practice om voldoende RAM-geheugen toe te wijzen, zodat u werkset net volledig in het geheugen bevindt. 

- Controleer of het gebruikte geheugen percentage in de [limieten](./concepts-pricing-tiers.md) bereikt met behulp [van de metrische gegevens voor de mysql-server](./concepts-monitoring.md). 
- Stel waarschuwingen in voor dergelijke getallen om er zeker van te zijn dat naarmate de servers limieten bereiken, u prompt acties kunt ondernemen om het probleem op te lossen. Op basis van de grenzen die zijn gedefinieerd, controleert u of u de data base-SKU omhoog kunt schalen, hetzij naar een hogere reken grootte of naar een betere prijs categorie, wat leidt tot een aanzienlijke toename van de prestaties. 
- U omhoog schalen tot de prestatie cijfers niet langer opvallen na een schaal bewerking. Zie voor meer informatie over het bewaken van de metrische gegevens van een Data Base-exemplaar [MySQL DB Metrics](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>Opwarm voor InnoDB-buffer groep gebruiken

Nadat Azure Database for MySQL server opnieuw is opgestart, worden de gegevens pagina's die zich in de opslag bevinden, geladen als de tabellen worden opgevraagd, waardoor de latentie en tragere prestaties voor de eerste uitvoering van de query's worden verbeterd. Dit is mogelijk niet acceptabel voor latentie gevoelige werk belastingen. 

Het gebruik van de InnoDB-buffer groep opwarm verkort de opwarm periode door schijf pagina's opnieuw te laden die zich in de buffer groep bevonden vóór het opnieuw opstarten, in plaats van te wachten op DML of SELECT-bewerkingen om toegang te krijgen tot overeenkomende rijen.

U kunt de opwarm-periode verminderen nadat u uw Azure Database for MySQL-server opnieuw hebt opgestart, wat een prestatie voordeel vertegenwoordigt door [InnoDB-buffer pool server parameters](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)te configureren. InnoDB slaat een percentage van de meest recent gebruikte pagina's op voor elke buffer groep bij het afsluiten van de server en herstelt deze pagina's bij het opstarten van de server.

Het is ook belang rijk om te weten dat verbeterde prestaties ten koste gaan van een langere opstart tijd voor de-server. Als deze para meter is ingeschakeld, wordt het opstarten van de server en de start tijd naar verwachting verhoogd, afhankelijk van de IOPS die op de server is ingericht. 

Het is raadzaam om de herstarttijd te testen en te controleren om ervoor te zorgen dat de prestaties voor opstarten en opnieuw opstarten acceptabel zijn omdat de server gedurende die tijd niet beschikbaar is. Het wordt niet aangeraden deze para meter te gebruiken met minder dan 1000 ingerichte IOPS (of met andere woorden, wanneer de inrichting van de opslag minder is dan 335 GB).

Als u de status van de buffer groep wilt opslaan bij het afsluiten van de server, stelt u de server parameter `innodb_buffer_pool_dump_at_shutdown` in op `ON` . Stel op dezelfde manier de server parameter in om `innodb_buffer_pool_load_at_startup` `ON` de status van de buffer groep te herstellen bij het opstarten van de server. U kunt de invloed op het opstarten en opnieuw opstarten bepalen door de waarde van de server parameter te verlagen en af te stemmen `innodb_buffer_pool_dump_pct` . Deze para meter is standaard ingesteld op `25` .

> [!Note]
> De opwarm-para meters van de InnoDB-buffer groep worden alleen ondersteund in de opslag servers voor algemeen gebruik met Maxi maal 16 TB opslag. Meer informatie over [Azure database for MySQL opslag opties vindt u hier](./concepts-pricing-tiers.md#storage).

## <a name="next-steps"></a>Volgende stappen

- [Aanbevolen procedure voor Server bewerkingen met Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Aanbevolen procedure voor het bewaken van uw Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Aan de slag met Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>