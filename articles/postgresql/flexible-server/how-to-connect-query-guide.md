---
title: Verbinding maken en query's uitvoeren op flexibele server PostgreSQL
description: Koppelingen naar Quick starts die laten zien hoe u verbinding maakt met uw Azure Database for PostgreSQL flexibele server en query's uitvoert.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97364564"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Verbinding maken en query's uitvoeren op een overzicht van Azure data base for PostgreSQL-flexibele server

Het volgende document bevat koppelingen naar voor beelden waarin wordt getoond hoe u verbinding maakt met Azure Database for PostgreSQL één server. Deze hand leiding bevat ook de TLS-aanbevelingen en uitbrei dingen die u kunt gebruiken om verbinding te maken met de server in ondersteunde talen hieronder.

>[!IMPORTANT]
> Azure Database for PostgreSQL flexibele server is in **Preview**.

## <a name="quickstarts"></a>Snelstartgidsen

| Snelstartgids | Beschrijving |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|U kunt pgAdmin gebruiken om verbinding te maken met de server. het vereenvoudigt het maken, onderhouden en gebruiken van database objecten.|
|[psql in Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|In dit artikel wordt beschreven hoe u [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) uitvoert in [Azure Cloud shell](../../cloud-shell/overview.md) om verbinding te maken met uw server en vervolgens instructies uitvoert om gegevens in de Data Base op te vragen, in te voegen, bij te werken en te verwijderen. U kunt **psql** uitvoeren als dit is geïnstalleerd in uw ontwikkel omgeving|
|[Python](connect-python.md)|In deze Quick Start wordt gedemonstreerd hoe u python gebruikt om verbinding te maken met een Data Base en met behulp van het gebruik van database objecten om gegevens op te vragen. |
|[Django met App Service](tutorial-django-app-service-postgres.md)|In deze zelf studie wordt gedemonstreerd hoe u ruby gebruikt om een programma te maken dat verbinding maakt met een Data Base en met behulp van data base-objecten kunt gebruiken om gegevens op te vragen.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-overwegingen voor de connectiviteit van databases

Transport Layer Security (TLS) wordt gebruikt door alle Stuur Programma's die door micro soft worden geleverd of ondersteund voor het maken van verbinding met data bases in Azure Database for PostgreSQL. Er is geen speciale configuratie nodig, maar afdwingen van TLS 1,2 voor nieuw gemaakte servers. U wordt aangeraden TLS 1,0 en 1,1 te gebruiken en vervolgens de TLS-versie voor uw servers bij te werken. Meer [informatie over het configureren van TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>PostgreSQL-extensies

PostgreSQL biedt de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van extensies. Extensies bundelen meerdere SQL-objecten in één pakket dat met één opdracht kan worden geladen in of verwijderd uit uw database. Nadat de gegevens in de database zijn geladen, functioneren de extensies als ingebouwde functies.

- [Post gres 12-extensies](./concepts-extensions.md#postgres-12-extensions)
- [Post gres 11-extensies](./concepts-extensions.md#postgres-11-extensions)
- [dblink en postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Zie [postgresql-extensies gebruiken op flexibele servers voor](concepts-extensions.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Gegevens migreren met dump en herstel](../howto-migrate-using-dump-and-restore.md)
- [Gegevens migreren met importeren en exporteren](../howto-migrate-using-export-and-import.md)
