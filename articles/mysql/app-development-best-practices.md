---
title: Aanbevolen procedures voor app-ontwikkeling-Azure Database for MySQL
description: Meer informatie over aanbevolen procedures voor het bouwen van een app met behulp van Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 7f98e14c2eae133941f3fc87247a427198ebcdc2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611960"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Aanbevolen procedures voor het bouwen van een toepassing met Azure Database for MySQL 

Hier volgen enkele aanbevolen procedures om u te helpen bij het bouwen van een toepassing die klaar is voor de Cloud met behulp van Azure Database for MySQL. Deze aanbevolen procedures kunnen de ontwikkelings tijd voor uw app beperken. 

## <a name="configuration-of-application-and-database-resources"></a>Configuratie van toepassings-en database bronnen

### <a name="keep-the-application-and-database-in-the-same-region"></a>De toepassing en data base in dezelfde regio blijven gebruiken
Zorg ervoor dat alle afhankelijkheden zich in dezelfde regio bevinden wanneer u uw toepassing in azure implementeert. Door instanties in verschillende regio's of beschikbaarheids zones te verspreiden, wordt netwerk latentie gemaakt. Dit kan van invloed zijn op de algehele prestaties van uw toepassing. 

### <a name="keep-your-mysql-server-secure"></a>De MySQL-server beveiligen
Configureer uw MySQL-server zodanig dat deze [veilig](./concepts-security.md) is en niet openbaar toegankelijk is. Gebruik een van de volgende opties om uw server te beveiligen: 
- [Firewall-regels](./concepts-firewall-rules.md)
- [Virtuele netwerken](./concepts-data-access-and-security-vnet.md) 
- [Azure Private Link](./concepts-data-access-security-private-link.md)

Voor beveiliging moet u altijd verbinding maken met uw MySQL-server via SSL en uw MySQL-server en uw toepassing configureren voor het gebruik van TLS 1,2. Zie [SSL/TLS configureren voor meer informatie](./concepts-ssl-connection-security.md). 

### <a name="tune-your-server-parameters"></a>Uw server parameters afstemmen
Voor het afstemmen van de server parameters voor Read-zware werk belastingen `tmp_table_size` en `max_heap_table_size` kan helpen optimaliseren voor betere prestaties. Als u de vereiste waarden voor deze variabelen wilt berekenen, bekijkt u de totale geheugen waarden per verbinding en het basis geheugen. De som van geheugen parameters per verbinding, met uitzonde ring van `tmp_table_size` , gecombineerd met de basis geheugen accounts voor het totale geheugen van de server.

Als u de grootst mogelijke grootte van en wilt berekenen `tmp_table_size` `max_heap_table_size` , gebruikt u de volgende formule:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Totaal geheugen geeft de totale hoeveelheid geheugen aan die de server op de ingerichte vCores heeft.  Zo is het totale geheugen in een Algemeen-Azure Database for MySQL server met twee vCore 5 GB * 2. Meer informatie over het geheugen voor elke laag vindt u in de documentatie over de [prijs categorie](./concepts-pricing-tiers.md) .
>
> Basis geheugen duidt op de geheugen variabelen, zoals `query_cache_size` en `innodb_buffer_pool_size` , dat MySQL wordt geïnitialiseerd en toegewezen bij het starten van de server. Geheugen per verbinding, zoals `sort_buffer_size` en `join_buffer_size` , is geheugen dat alleen wordt toegewezen wanneer een query dat nodig heeft.

### <a name="create-non-admin-users"></a>Gebruikers zonder beheerders rechten maken 
[Gebruikers zonder beheerders rechten maken](./howto-create-users.md) voor elke Data Base. De gebruikers namen worden meestal aangeduid als de database namen.

### <a name="reset-your-password"></a>Uw wachtwoord opnieuw instellen
U kunt [uw wacht woord](./howto-create-manage-server-portal.md#update-admin-password) voor de mysql-server opnieuw instellen met behulp van de Azure Portal. 

Het opnieuw instellen van uw server wachtwoord voor een productie database kan uw toepassing innemen. Het is een goed idee om het wacht woord opnieuw in te stellen voor productie werkbelastingen met een buiten piek uren om de gevolgen voor de gebruikers van uw toepassing te minimaliseren.

## <a name="performance-and-resiliency"></a>Prestaties en tolerantie 
Hier volgen enkele hulp middelen en procedures die u kunt gebruiken om problemen met de prestaties van uw toepassing op te sporen.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Langzame query logboeken inschakelen voor het identificeren van prestatie problemen
U kunt [langzame query logboeken](./concepts-server-logs.md) en [controle logboeken](./concepts-audit-logs.md) op de server inschakelen. Het analyseren van langzame query logboeken kan helpen bij het identificeren van prestatie knelpunten bij het oplossen van problemen. 

Audit logboeken zijn ook beschikbaar via Azure Diagnostics-Logboeken in Azure Monitor-logboeken, Azure Event Hubs en opslag accounts. Zie problemen [met prestaties van query's oplossen](./howto-troubleshoot-query-performance.md).

### <a name="use-connection-pooling"></a>Groepsgewijze verbindingen gebruiken
Het beheren van database verbindingen kan een grote invloed hebben op de prestaties van de toepassing als geheel. Om de prestaties te optimaliseren, moet u het aantal keren dat verbindingen tot stand zijn gebracht en de tijd voor het tot stand brengen van verbindingen in sleutel code paden verminderen. Gebruik [groepsgewijze verbindingen](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended) om verbinding te maken met Azure database for MySQL om de tolerantie en prestaties te verbeteren. 

U kunt de [ProxySQL](https://proxysql.com/) -verbindings groep gebruiken om verbindingen efficiënt te beheren. Het gebruik van een verbindings groep kan inactieve verbindingen verminderen en bestaande verbindingen hergebruiken. Dit helpt u bij het voor komen van problemen. Zie [ProxySQL instellen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) voor meer informatie. 

### <a name="retry-logic-to-handle-transient-errors"></a>Poging tot logica voor het afhandelen van tijdelijke fouten
Uw toepassing kan [tijdelijke fouten](./concepts-connectivity.md#handling-transient-errors) ondervinden waarbij verbindingen met de data base regel matig worden verwijderd of verloren gaan. In dergelijke situaties is de server actief en wordt deze uitgevoerd na een tot twee nieuwe pogingen van 5 tot 10 seconden. 

Het is een goed idee om vijf seconden te wachten voordat u voor het eerst opnieuw probeert. Volg daarna elke nieuwe poging door de wacht tijd geleidelijk te verhogen tot 60 seconden. Beperk het maximum aantal nieuwe pogingen waarbij de bewerking door uw toepassing wordt beschouwd als mislukt, zodat u vervolgens verder kunt onderzoeken. Zie [verbindings fouten oplossen](./howto-troubleshoot-common-connection-issues.md) voor meer informatie. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Lees replicatie inschakelen om failovers te beperken
U kunt [replicatie van inkomende gegevens](./howto-data-in-replication.md) gebruiken voor failover-scenario's. Wanneer u Replicas lezen gebruikt, vindt er geen automatische failover tussen bron-en replica servers plaats. 

U ziet een vertraging tussen de bron en de replica, omdat de replicatie asynchroon is. Netwerk vertraging kan worden beïnvloed door veel factoren, zoals de grootte van de werk belasting die wordt uitgevoerd op de bron server en de latentie tussen data centers. In de meeste gevallen ligt de replica vertraging van een paar seconden naar een paar minuten.

## <a name="database-deployment"></a>Database-implementatie 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Een Azure data base for MySQL-taak configureren in uw CI/CD-implementatie pijplijn
Af en toe moet u wijzigingen in uw data base implementeren. In dergelijke gevallen kunt u doorlopende integratie (CI) en continue levering (CD) via [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) gebruiken en een taak voor [uw MySQL-server](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment) gebruiken om de data base bij te werken door er een aangepast script op uit te voeren.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Een effectief proces gebruiken voor hand matige implementatie van de data base 
Voer tijdens hand matige implementatie van de data base de volgende stappen uit om de downtime te minimaliseren of het risico op mislukte implementatie te verminderen: 

1. Een kopie maken van een productie database in een nieuwe Data Base met behulp van [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) of [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html). 
2. Werk de nieuwe Data Base bij met de nieuwe schema wijzigingen of updates die nodig zijn voor uw data base. 
3. Plaats de productie database in een alleen-lezen status. U moet geen schrijf bewerkingen op de productie database hebben totdat de implementatie is voltooid. 
4. Test uw toepassing met de zojuist bijgewerkte data base uit stap 1.
5. Implementeer de wijzigingen van uw toepassing en zorg ervoor dat de toepassing nu gebruikmaakt van de nieuwe Data Base met de meest recente updates. 
6. Behoud de oude productie database zodat u de wijzigingen kunt terugdraaien. U kunt vervolgens evalueren of u de oude productie database wilt verwijderen of op Azure Storage wilt exporteren, indien nodig. 

>[!NOTE]
>Als de toepassing een e-commerce-app is en u deze niet in de modus alleen-lezen kunt plaatsen, implementeert u de wijzigingen rechtstreeks in de productie database na het maken van een back-up. Deze wijziging moet zich voordoen tijdens rustige uren met weinig verkeer naar de app om de impact te minimaliseren, omdat sommige gebruikers mislukte aanvragen kunnen ondervinden. 
>
>Zorg ervoor dat de toepassings code ook alle mislukte aanvragen verwerkt.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Gebruik de native MySQL-metrische gegevens om te controleren of de werk belasting de tijdelijke tabel grootte in het geheugen overschrijdt
Met een lees-zware werk belasting kunnen query's die worden uitgevoerd op de MySQL-server de tijdelijke tabel grootte in het geheugen overschrijden. Een lees-zware werk belasting kan ervoor zorgen dat uw server overschakelt naar het schrijven van tijdelijke tabellen naar de schijf, wat van invloed is op de prestaties van uw toepassing. Als u wilt bepalen of uw server naar de schijf schrijft als gevolg van het overschrijden van de tijdelijke tabel grootte, bekijkt u de volgende metrische gegevens:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
De `created_tmp_disk_tables` metriek geeft aan hoeveel tabellen op schijf zijn gemaakt. De `created_tmp_table` metriek vertelt u hoeveel tijdelijke tabellen in het geheugen moeten worden gevormd, op basis van uw werk belasting. Als u wilt bepalen of het uitvoeren van een specifieke query gebruikmaakt van tijdelijke tabellen, voert u de instructie [uitleg](https://dev.mysql.com/doc/refman/8.0/en/explain.html) uit voor de query. Het detail in de `extra` kolom geeft aan `Using temporary` of de query wordt uitgevoerd met behulp van tijdelijke tabellen.

Gebruik uw metrische waarden in de volgende formule om het percentage van uw workload te berekenen met query's die overlopen op schijven:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

In het ideale geval moet dit percentage minder 25% zijn. Als u ziet dat het percentage 25% of groter is, raden we u aan om twee server parameters, tmp_table_size en max_heap_table_size te wijzigen.

## <a name="database-schema-and-queries"></a>Database schema en query's

Hier volgen enkele tips die u moet overwegen wanneer u uw database schema en uw query's bouwt.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Het juiste gegevens type voor uw tabel kolommen gebruiken
Als u het juiste gegevens type gebruikt op basis van het type gegevens dat u wilt opslaan, kunt u de opslag optimaliseren en fouten verminderen die kunnen optreden vanwege onjuiste gegevens typen.

### <a name="use-indexes"></a>Indexen gebruiken
U kunt indexen gebruiken om trage query's te voor komen. Indexen kunnen snel rijen met specifieke kolommen vinden. Zie [indexen gebruiken in MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>UITLEG gebruiken voor uw SELECT-query's
Gebruik de- `EXPLAIN` instructie om inzicht te krijgen in wat MySQL doet om uw query uit te voeren. Het helpt u bij het detecteren van knel punten of problemen met uw query. Zie [uitleg gebruiken om de prestaties van query's te](./howto-troubleshoot-query-performance.md)bepalen.