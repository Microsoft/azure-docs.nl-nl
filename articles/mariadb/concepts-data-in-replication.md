---
title: Replicatie van gegevens-in-Azure Database for MariaDB
description: Meer informatie over het gebruik van replicatie van gegevens in een externe server naar de Azure Database for MariaDB-service.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662624"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Gegevens repliceren naar Azure Database for MariaDB

Met replicatie van inkomende gegevens kunt u gegevens synchroniseren die afkomstig zijn van een MariaDB-server die wordt uitgevoerd on-premises, in virtuele machines (VM's) of in databaseservices gehost door andere cloudproviders in de Azure Database for MariaDB-service. Replicatie van binnenkomende gegevens is gebaseerd op het binaire logbestand (binlog) met replicatie op basis van positie eigen aan MariaDB. Zie het [overzicht van binlog-replicatie](https://mariadb.com/kb/en/library/replication-overview/)voor meer informatie over binlog-replicatie.

## <a name="when-to-use-data-in-replication"></a>Wanneer moet ik Replicatie van inkomende gegevens gebruiken?
De belangrijkste scenario's voor het gebruik van Replicatie van inkomende gegevens zijn:

- **Gegevens synchronisatie hybride:** Met Replicatie van inkomende gegevens kunt u gegevens gesynchroniseerd blijven tussen uw on-premises servers en Azure Database for MariaDB. Deze synchronisatie is handig voor het maken van hybride toepassingen. Deze methode is aantrekkelijker wanneer u een bestaande lokale database server hebt, maar u de gegevens wilt verplaatsen naar een regio dichter bij eind gebruikers.
- **Synchronisatie met meerdere Clouds:** Gebruik Replicatie van inkomende gegevens voor complexe cloud oplossingen om gegevens te synchroniseren tussen Azure Database for MariaDB en verschillende cloud providers, met inbegrip van virtuele machines en database services die worden gehost in die Clouds.

## <a name="limitations-and-considerations"></a>Beperkingen en overwegingen

### <a name="data-not-replicated"></a>Gegevens niet gerepliceerd
De [*MySQL-systeem database*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) op de bron server wordt niet gerepliceerd. Wijzigingen van accounts en machtigingen op de bron server worden niet gerepliceerd. Als u een account op de bron server maakt en dit account moet toegang hebben tot de replica server en vervolgens hand matig hetzelfde account maken op de replica server. Zie de [MariaDB-documentatie](https://mariadb.com/kb/en/library/the-mysql-database-tables/)voor meer informatie over de tabellen die zijn opgenomen in de systeem database.

### <a name="requirements"></a>Vereisten
- De versie van de bron server moet ten minste MariaDB versie 10,2 zijn.
- De bron-en replica server versie moeten gelijk zijn. Beide moeten bijvoorbeeld MariaDB versie 10,2 zijn.
- Elke tabel moet een primaire sleutel hebben.
- De bron server moet de InnoDB-engine gebruiken.
- De gebruiker moet machtigingen hebben voor het configureren van binaire logboek registratie en het maken van nieuwe gebruikers op de bron server.
- Als SSL is ingeschakeld op de bron server, moet u ervoor zorgen dat het SSL-CA-certificaat dat is opgegeven voor het domein, is opgenomen in de `mariadb.az_replication_change_master` opgeslagen procedure. Raadpleeg de volgende [voor beelden](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) en de `master_ssl_ca` para meter.
- Zorg ervoor dat het IP-adres van de bron server is toegevoegd aan de firewall regels van de Azure Database for MariaDB replica-server. Firewallregels bijwerken met de [Azure-portal](howto-manage-firewall-portal.md) of [Azure CLI](howto-manage-firewall-cli.md).
- Zorg ervoor dat de computer die de bron server host, zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat.
- Controleer of de bron server een **openbaar IP-adres** heeft, of de DNS openbaar toegankelijk is of een Fully QUALIFIED domain name (FQDN) heeft.

### <a name="other"></a>Anders
- Replicatie van gegevens wordt alleen ondersteund in de Algemeen en de prijs categorieën die zijn geoptimaliseerd voor geheugen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [instellen van replicatie van gegevens](howto-data-in-replication.md).
