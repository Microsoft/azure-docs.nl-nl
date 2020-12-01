---
title: Aanbevolen procedures voor prestaties-Azure Database for MySQL
description: In dit artikel worden de aanbevolen procedures beschreven voor het bewaken en afstemmen van de prestaties van uw Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: c29c043a3af46086751629b31ce68217e7226442
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354994"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Aanbevolen procedures voor optimale prestaties van uw Azure Database for MySQL-één server

Meer informatie over de aanbevolen procedures voor het verkrijgen van de beste prestaties bij het werken met uw Azure Database for MySQL-één server. Wanneer we nieuwe mogelijkheden toevoegen aan het platform, zullen we verder gaan met de best practices die in deze sectie worden beschreven.

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

- Controleer of het gebruikte geheugen percentage in de [limieten](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) bereikt met behulp [van de metrische gegevens voor de mysql-server](https://docs.microsoft.com/azure/mysql/concepts-monitoring). 
- Stel waarschuwingen in voor dergelijke getallen om er zeker van te zijn dat naarmate de servers limieten bereiken, u prompt acties kunt ondernemen om het probleem op te lossen. Op basis van de grenzen die zijn gedefinieerd, controleert u of u de data base-SKU omhoog kunt schalen, hetzij naar een hogere reken grootte of naar een betere prijs categorie die resulteert in een aanzienlijke toename van de prestaties. 
- U omhoog schalen tot de prestatie cijfers niet langer opvallen na een schaal bewerking. Zie voor meer informatie over het bewaken van de metrische gegevens van een Data Base-exemplaar [MySQL DB Metrics](https://docs.microsoft.com/azure/mysql/concepts-monitoring#metrics).

## <a name="next-steps"></a>Volgende stappen

- [Aanbevolen procedure voor Server bewerkingen met Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Aanbevolen procedure voor het bewaken van uw Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Aan de slag met Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
