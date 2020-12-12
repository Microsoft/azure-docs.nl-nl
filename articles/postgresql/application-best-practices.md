---
title: Aanbevolen procedures voor app-ontwikkeling-Azure Database for PostgreSQL
description: Meer informatie over aanbevolen procedures voor het bouwen van een app met behulp van Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364575"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Aanbevolen procedures voor het bouwen van een toepassing met Azure Database for PostgreSQL

Hier volgen enkele aanbevolen procedures om u te helpen bij het bouwen van een toepassing die klaar is voor de Cloud met behulp van Azure Database for PostgreSQL. Deze aanbevolen procedures kunnen de ontwikkelings tijd voor uw app beperken.

## <a name="configuration-of-application-and-database-resources"></a>Configuratie van toepassings-en database bronnen

### <a name="keep-the-application-and-database-in-the-same-region"></a>De toepassing en data base in dezelfde regio blijven gebruiken
Zorg ervoor dat alle afhankelijkheden zich in dezelfde regio bevinden wanneer u uw toepassing in azure implementeert. Door instanties in verschillende regio's of beschikbaarheids zones te verspreiden, wordt netwerk latentie gemaakt. Dit kan van invloed zijn op de algehele prestaties van uw toepassing.

### <a name="keep-your-postgresql-server-secure"></a>Uw PostgreSQL-server beveiligen
Configureer uw PostgreSQL-server zodanig dat deze [veilig](./concepts-security.md) is en niet openbaar toegankelijk is. Gebruik een van de volgende opties om uw server te beveiligen:
- [Firewall-regels](./concepts-firewall-rules.md)
- [Virtuele netwerken](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

Voor beveiliging moet u altijd verbinding maken met uw PostgreSQL-server via SSL en uw PostgreSQL-server en uw toepassing configureren voor het gebruik van TLS 1,2. Zie [SSL/TLS configureren voor meer informatie](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Uw server parameters afstemmen
Voor het afstemmen van de server parameters voor Read-zware werk belastingen `tmp_table_size` en `max_heap_table_size` kan helpen optimaliseren voor betere prestaties. Als u de vereiste waarden voor deze variabelen wilt berekenen, bekijkt u de totale geheugen waarden per verbinding en het basis geheugen. De som van geheugen parameters per verbinding, met uitzonde ring van `tmp_table_size` , gecombineerd met de basis geheugen accounts voor het totale geheugen van de server.

### <a name="use-environment-variables-for-connection-information"></a>Omgevings variabelen gebruiken voor verbindings gegevens
Sla uw database referenties niet op in de code van uw toepassing. Afhankelijk van de front-end-toepassing volgt u de richt lijnen voor het instellen van omgevings variabelen. Zie[app-instellingen](../app-service/configure-common.md#configure-app-settings) en voor Azure Kuberentes-service configureren voor meer informatie over het gebruik van app service. Zie [How to use Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)(Engelstalig).

## <a name="performance-and-resiliency"></a>Prestaties en tolerantie
Hier volgen enkele hulp middelen en procedures die u kunt gebruiken om problemen met de prestaties van uw toepassing op te sporen.

### <a name="use-connection-pooling"></a>Groepsgewijze verbindingen gebruiken
Met Groepsgewijze verbinding wordt een vaste set verbindingen tot stand gebracht op het moment van opstarten en onderhouden. Dit vermindert ook de geheugen fragmentatie op de server die wordt veroorzaakt door de dynamische nieuwe verbindingen die tot stand zijn gebracht op de database server. De Groepsgewijze verbinding kan worden geconfigureerd aan de kant van de toepassing als het app-Framework of het database stuur programma dit ondersteunt. Als dat niet wordt ondersteund, is de andere aanbevolen optie om gebruik te maken van een proxy connection pooler-service zoals [PgBouncer](https://pgbouncer.github.io/) of [pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) die buiten de toepassing wordt uitgevoerd en verbinding maakt met de database server. Zowel PgBouncer als pgpool zijn op community's gebaseerd hulp middelen die samen werken met Azure Database for PostgreSQL.

### <a name="retry-logic-to-handle-transient-errors"></a>Poging tot logica voor het afhandelen van tijdelijke fouten
Uw toepassing kan tijdelijke fouten ondervinden waarbij verbindingen met de data base regel matig worden verwijderd of verloren gaan. In dergelijke situaties is de server actief en wordt deze uitgevoerd na een tot twee nieuwe pogingen van 5 tot 10 seconden. Het is een goed idee om vijf seconden te wachten voordat u voor het eerst opnieuw probeert. Volg daarna elke nieuwe poging door de wacht tijd geleidelijk te verhogen tot 60 seconden. Beperk het maximum aantal nieuwe pogingen waarbij de bewerking door uw toepassing wordt beschouwd als mislukt, zodat u vervolgens verder kunt onderzoeken. Zie [verbindings fouten oplossen](./concepts-connectivity.md) voor meer informatie.

### <a name="enable-read-replication-to-mitigate-failovers"></a>Lees replicatie inschakelen om failovers te beperken
U kunt [replicatie van inkomende gegevens](./concepts-read-replicas.md) gebruiken voor failover-scenario's. Wanneer u Replicas lezen gebruikt, vindt er geen automatische failover tussen bron-en replica servers plaats. U ziet een vertraging tussen de bron en de replica, omdat de replicatie asynchroon is. Netwerk vertraging kan worden beïnvloed door veel factoren, zoals de grootte van de werk belasting die wordt uitgevoerd op de bron server en de latentie tussen data centers. In de meeste gevallen ligt de replica vertraging van een paar seconden naar een paar minuten.


## <a name="database-deployment"></a>Data base-implementatie

### <a name="configure-cicd-deployment-pipeline"></a>Pijp lijn voor CI/CD-implementatie configureren
Af en toe moet u wijzigingen in uw data base implementeren. In dergelijke gevallen kunt u doorlopende integratie (CI) via [github-acties](https://github.com/Azure/postgresql/blob/master/README.md) voor uw postgresql-server gebruiken om de data base bij te werken door er een aangepast script op uit te voeren.

### <a name="define-manual-database-deployment-process"></a>Hand matig implementatie proces voor data bases definiëren
Voer tijdens hand matige implementatie van de data base de volgende stappen uit om de downtime te minimaliseren of het risico op mislukte implementatie te verminderen:

- Een kopie maken van een productie database in een nieuwe Data Base met behulp van pg_dump.
- Werk de nieuwe Data Base bij met de nieuwe schema wijzigingen of updates die nodig zijn voor uw data base.
- Plaats de productie database in een alleen-lezen status. U moet geen schrijf bewerkingen op de productie database hebben totdat de implementatie is voltooid.
- Test uw toepassing met de zojuist bijgewerkte data base uit stap 1.
- Implementeer de wijzigingen van uw toepassing en zorg ervoor dat de toepassing nu gebruikmaakt van de nieuwe Data Base met de meest recente updates.
- Behoud de oude productie database zodat u de wijzigingen kunt terugdraaien. U kunt vervolgens evalueren of u de oude productie database wilt verwijderen of op Azure Storage wilt exporteren, indien nodig.

>  [!NOTE]
> Als de toepassing een e-commerce-app is en u deze niet in de modus alleen-lezen kunt plaatsen, implementeert u de wijzigingen rechtstreeks in de productie database na het maken van een back-up. Deze wijziging moet zich voordoen tijdens rustige uren met weinig verkeer naar de app om de impact te minimaliseren, omdat sommige gebruikers mislukte aanvragen kunnen ondervinden. Zorg ervoor dat de toepassings code ook alle mislukte aanvragen verwerkt.

## <a name="database-schema-and-queries"></a>Database schema en query's
Hier volgen enkele tips die u moet overwegen wanneer u uw database schema en uw query's bouwt.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>BIGINT of UUID gebruiken voor primaire sleutels
Bij het bouwen van een aangepaste toepassing of sommige Frameworks kunnen ze worden gebruikt `INT` in plaats van `BIGINT` voor primaire sleutels. Wanneer u gebruikt ```INT``` , loopt u het risico dat de waarde in uw data base de opslag capaciteit van het ```INT``` gegevens type overschrijdt. Als u deze wijziging aanbrengt in een bestaande productie toepassing, kan dit veel tijd in beslag nemen met meer ontwikkelings tijd. Een andere optie is het gebruik van [uuid](https://www.postgresql.org/docs/current/datatype-uuid.html) voor primaire sleutels. Deze id maakt gebruik van een automatisch gegenereerde 128-bits teken reeks, bijvoorbeeld ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . Meer informatie over [postgresql-gegevens typen](https://www.postgresql.org/docs/8.1/datatype.html).

### <a name="use-indexes"></a>Indexen gebruiken

Er zijn veel soorten [indexen](https://www.postgresql.org/docs/9.1/indexes.html) in post gres die op verschillende manieren kunnen worden gebruikt. Het gebruik van een index helpt de server bij het zoeken en ophalen van specifieke rijen veel sneller dan zonder een index. Maar indexen voegen ook overhead toe aan de database server, dus vermijd te veel indexen.

### <a name="use-autovacuum"></a>Autovacuüm gebruiken
U kunt uw server met autovacuüm op een Azure Database for PostgreSQL server optimaliseren. PostgreSQL kunnen meer data base gelijktijdig worden gecurrencyd, maar bij elke update worden de resultaten invoegen en verwijderen. Voor verwijdering worden de records zacht gemarkeerd, die later worden verwijderd. PostgreSQL voert een vacuüm taak uit om deze taken uit te voeren. Als u niet van tijd tot tijd vacuüm, kunnen de niet-actieve Tuples die worden samengevoegd resulteren in:

- Gegevens grootten, zoals grotere data bases en tabellen.
- Grotere suboptimale indexen.
- Meer I/O-bewerkingen.

Meer informatie over [hoe u kunt optimaliseren met autovacuüm](howto-optimize-autovacuum.md).

### <a name="use-pg_stats_statements"></a>Pg_stats_statements gebruiken
Pg_stat_statements is een PostgreSQL-extensie die standaard is ingeschakeld in Azure Database for PostgreSQL. De uitbrei ding biedt een manier om uitvoerings statistieken bij te houden voor alle SQL-instructies die worden uitgevoerd door een server. Zie [pg_statement gebruiken](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>Het query archief gebruiken
De functie [query Store](./concepts-query-store.md) in azure database for PostgreSQL biedt een effectievere methode om query statistieken bij te houden. Deze functie wordt aangeraden als alternatief voor het gebruik van pg_stats_statements.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Bulk toevoegingen optimaliseren en tijdelijke gegevens gebruiken
Als u werk belasting bewerkingen hebt waarbij tijdelijke gegevens worden betrokken of als u grote data sets tegelijk invoegt, kunt u gebruikmaken van niet-geregistreerde tabellen. Het biedt standaard atomische en duurzaamheid. Atomiciteit, consistentie, isolatie en duurzaamheid vormen de ACID-eigenschappen. Zie [bulk toevoegingen optimaliseren](howto-optimize-bulk-inserts.md).

## <a name="next-steps"></a>Volgende stappen
[Post gres-hand leiding](http://postgresguide.com/)
