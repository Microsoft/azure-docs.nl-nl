---
title: Replicatie van inkomende gegevens-Azure Database for MySQL
description: Meer informatie over het gebruik van Replicatie van inkomende gegevens om te synchroniseren vanaf een externe server in de Azure Database for MySQL-service.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 83ad2d4f392afb6bb33c99a5449b9bc8ceeaa058
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312804"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Gegevens repliceren naar Azure Database for MySQL

Met Replicatie van inkomende gegevens kunt u gegevens van een externe MySQL-server synchroniseren met de Azure Database for MySQL-service. De externe server kan on-premises, in virtuele machines of een database service worden gehost door andere cloud providers. Replicatie van inkomende gegevens is gebaseerd op de locatie van het binaire logboek bestand (binlog) of op GTID gebaseerde replicatie van de native modus naar MySQL. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

## <a name="when-to-use-data-in-replication"></a>Wanneer moet ik Replicatie van inkomende gegevens gebruiken?

De belangrijkste scenario's waarmee u rekening moet houden over het gebruik van Replicatie van inkomende gegevens zijn:

- **Gegevens synchronisatie hybride:** Met Replicatie van inkomende gegevens kunt u gegevens gesynchroniseerd blijven tussen uw on-premises servers en Azure Database for MySQL. Deze synchronisatie is handig voor het maken van hybride toepassingen. Deze methode is handig wanneer u een bestaande lokale database server hebt, maar de gegevens naar een regio dichter bij eind gebruikers wilt verplaatsen.
- **Synchronisatie met meerdere Clouds:** Gebruik Replicatie van inkomende gegevens voor complexe cloud oplossingen om gegevens te synchroniseren tussen Azure Database for MySQL en verschillende cloud providers, met inbegrip van virtuele machines en database services die worden gehost in die Clouds.

Gebruik de [Azure database Migration service](https://azure.microsoft.com/services/database-migration/)(DMS) voor migratie scenario's.

## <a name="limitations-and-considerations"></a>Beperkingen en overwegingen

### <a name="data-not-replicated"></a>Gegevens niet gerepliceerd

De [*MySQL-systeem database*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) op de bron server wordt niet gerepliceerd. Daarnaast worden wijzigingen aan accounts en machtigingen op de bron server niet gerepliceerd. Als u een account op de bron server maakt en dit account toegang heeft tot de replica-server, moet u het account hand matig op de replica server maken. Zie de [MySQL-hand leiding](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)voor meer informatie over de tabellen die zijn opgenomen in de systeem database.

### <a name="filtering"></a>Filteren

Voor het overs laan van replicerende tabellen van de bron server (lokaal gehost, op virtuele machines of een database service die wordt gehost door andere cloud providers), `replicate_wild_ignore_table` wordt de para meter ondersteund. U kunt deze para meter eventueel bijwerken op de replica server die wordt gehost in azure met behulp van de [Azure Portal](howto-server-parameters.md) of [Azure cli](howto-configure-server-parameters-using-cli.md).

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table)voor meer informatie over deze para meter.

## <a name="supported-in-general-purpose-or-memory-optimized-tier-only"></a>Alleen ondersteund in de Algemeen of de laag geoptimaliseerd voor geheugen

Replicatie van inkomende gegevens wordt alleen ondersteund in de Algemeen en de prijs categorie geoptimaliseerd voor geheugen.

### <a name="requirements"></a>Vereisten

- De versie van de bron server moet ten minste MySQL-versie 5,6 zijn.
- De bron-en replica server versie moeten gelijk zijn. Beide moeten bijvoorbeeld MySQL-versie 5,6 zijn of beide moeten MySQL-versie 5,7 zijn.
- Elke tabel moet een primaire sleutel hebben.
- De bron server moet de MySQL InnoDB-engine gebruiken.
- De gebruiker moet machtigingen hebben voor het configureren van binaire logboek registratie en het maken van nieuwe gebruikers op de bron server.
- Als SSL is ingeschakeld op de bron server, moet u ervoor zorgen dat het SSL-CA-certificaat dat is opgegeven voor het domein, is opgenomen in de `mysql.az_replication_change_master` of `mysql.az_replication_change_master_with_gtid` opgeslagen procedure. Raadpleeg de volgende [voor beelden](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) en de `master_ssl_ca` para meter.
- Zorg ervoor dat het IP-adres van de bron server is toegevoegd aan de firewall regels van de Azure Database for MySQL replica-server. Firewallregels bijwerken met de [Azure-portal](./howto-manage-firewall-using-portal.md) of [Azure CLI](./howto-manage-firewall-using-cli.md).
- Zorg ervoor dat de computer die de bron server host, zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat.
- Controleer of de bron server een **openbaar IP-adres** heeft, of de DNS openbaar toegankelijk is, of dat de bron server een Fully QUALIFIED domain name (FQDN) heeft.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [instellen van gegevens replicatie](howto-data-in-replication.md)
- Meer informatie over [repliceren in azure met replica's lezen](concepts-read-replicas.md)
- Meer informatie over het [migreren van gegevens met minimale downtime met behulp van DMS](howto-migrate-online.md)
