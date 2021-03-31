---
title: Opgeslagen procedures voor beheer-Azure Database for MariaDB
description: Informatie over welke opgeslagen procedures in Azure Database for MariaDB nuttig zijn om u te helpen bij het configureren van replicatie van gegevens in, het instellen van de tijd zone en het beëindigen van query's.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 103bba37f5574185f10f5c4e28e66268da0c7f39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664628"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Opgeslagen procedures voor Azure Database for MariaDB beheer

Opgeslagen procedures zijn beschikbaar op Azure Database for MariaDB servers om uw MariaDB-server te beheren. Dit omvat het beheren van de verbindingen van uw server, het uitvoeren van query's en het instellen van Replicatie van inkomende gegevens.  

## <a name="data-in-replication-stored-procedures"></a>Opgeslagen procedures Replicatie van inkomende gegevens

Met replicatie van inkomende gegevens kunt u gegevens synchroniseren die afkomstig zijn van een MariaDB-server die wordt uitgevoerd on-premises, in virtuele machines (VM's) of in databaseservices gehost door andere cloudproviders in de Azure Database for MariaDB-service.

De volgende opgeslagen procedures worden gebruikt om Replicatie van inkomende gegevens tussen een bron en replica in te stellen of te verwijderen.

|**Naam van opgeslagen procedure**|**Invoerparameters**|**Uitvoer parameters**|**Opmerking over gebruik**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N.v.t.|Als u gegevens wilt overdragen met de SSL-modus, geeft u de context van het CA-certificaat door aan de para meter master_ssl_ca. </br><br>Als u gegevens zonder SSL wilt overdragen, geeft u een lege teken reeks door in de para meter master_ssl_ca.|
|*mysql.az_replication _start*|N.v.t.|N.v.t.|Replicatie wordt gestart.|
|*mysql.az_replication _stop*|N.v.t.|N.v.t.|De replicatie wordt gestopt.|
|*mysql.az_replication _remove_master*|N.v.t.|N.v.t.|Hiermee verwijdert u de replicatie relatie tussen de bron en de replica.|
|*mysql.az_replication_skip_counter*|N.v.t.|N.v.t.|Hiermee wordt één replicatie fout overs Laan.|

Zie [replicatie van inkomende gegevens configureren voor het](howto-data-in-replication.md)instellen van replicatie van inkomende gegevens tussen een bron en een replica in azure database for MariaDB.

## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

De volgende opgeslagen procedures zijn beschikbaar in Azure Database for MariaDB voor het beheren van uw server.

|**Naam van opgeslagen procedure**|**Invoerparameters**|**Uitvoer parameters**|**Opmerking over gebruik**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N.v.t.|Gelijk aan- [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) opdracht. De verbinding die is gekoppeld aan de geleverde processlist_id na het beëindigen van een instructie die wordt uitgevoerd, wordt beëindigd.|
|*mysql.az_kill_query*|processlist_id|N.v.t.|Gelijk aan- [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) opdracht. Hiermee wordt de instructie beëindigd die momenteel wordt uitgevoerd door de verbinding. De verbinding zelf blijft actief.|
|*mysql.az_load_timezone*|N.v.t.|N.v.t.|Laadt tijd zone tabellen zodat de `time_zone` para meter kan worden ingesteld op benoemde waarden (bijvoorbeeld "VS/Pacific").|

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het instellen van [replicatie van inkomende gegevens](howto-data-in-replication.md)
- Meer informatie over het gebruik van de [tabellen met tijd zones](howto-server-parameters.md#working-with-the-time-zone-parameter)