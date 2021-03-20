---
title: Verbinding maken en query's uitvoeren-PostgreSQL met één server
description: Koppelingen naar Quick starts die laten zien hoe u verbinding maakt met uw Azure Database for PostgreSQL één server en query's uitvoert.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546548"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Overzicht van verbinding maken met en query's uitvoeren op Azure Database for PostgreSQL - Enkele server

Het volgende document bevat koppelingen naar voor beelden waarin wordt getoond hoe u verbinding maakt met Azure Database for PostgreSQL één server. Deze hand leiding bevat ook de TLS-aanbevelingen en uitbrei dingen die u kunt gebruiken om verbinding te maken met de server in ondersteunde talen hieronder.

## <a name="quickstarts"></a>Snelstartgidsen

| Snelstartgids | Beschrijving |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|U kunt pgAdmin gebruiken om verbinding te maken met de server. het vereenvoudigt het maken, onderhouden en gebruiken van database objecten.|
|[psql in Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|In dit artikel wordt beschreven hoe u [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) uitvoert in [Azure Cloud shell](../cloud-shell/overview.md) om verbinding te maken met uw server en vervolgens instructies uitvoert om gegevens in de Data Base op te vragen, in te voegen, bij te werken en te verwijderen. U kunt **psql** uitvoeren als dit is geïnstalleerd in uw ontwikkel omgeving|
|[PostgreSQL met VS code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|Met de Azure data base-uitbrei ding voor de VS code (preview) kunt u zowel lokaal als in de Cloud een query uitvoeren op uw PostgreSQL-server, met behulp van plak boeken met uitgebreide IntelliSense. |
|[PHP](connect-php.md)|In deze Quick Start wordt gedemonstreerd hoe u PHP gebruikt om een programma te maken dat verbinding maakt met een Data Base en hoe u met Data Base-objecten werkt om gegevens op te vragen.|
|[Java](connect-java.md)|In deze Quick Start wordt gedemonstreerd hoe u Java gebruikt om verbinding te maken met een Data Base en vervolgens met behulp van werk met database objecten query's uitvoert op gegevens.|
|[Node.js](connect-nodejs.md)|In deze Quick start ziet u hoe u Node.js kunt gebruiken om een programma te maken waarmee verbinding kan worden gemaakt met een Data Base en om gegevens op te vragen met behulp van data base-objecten.|
|[.NET (C#)](connect-csharp.md)|In deze Quick start ziet u hoe u met use.NET (C#) een C#-programma maakt om verbinding te maken met een Data Base en met behulp van data base-objecten een werk query uitvoert voor gegevens.|
|[Go](connect-go.md)|In deze quickstart ziet u hoe u Go kunt gebruiken om verbinding te maken met een database. Bovendien worden er Transact-SQL-instructies voor het doorzoeken en wijzigen van gegevens beschreven.|
|[Python](connect-python.md)|In deze Quick Start wordt gedemonstreerd hoe u python gebruikt om verbinding te maken met een Data Base en met behulp van het gebruik van database objecten om gegevens op te vragen. |
|[Ruby](connect-ruby.md)|In deze Quick Start wordt gedemonstreerd hoe u ruby gebruikt om een programma te maken dat verbinding maakt met een Data Base en met behulp van data base-objecten kunt gebruiken om gegevens op te vragen.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-overwegingen voor de connectiviteit van databases

Transport Layer Security (TLS) wordt gebruikt door alle Stuur Programma's die door micro soft worden geleverd of ondersteund voor het maken van verbinding met data bases in Azure Database for PostgreSQL. Er is geen speciale configuratie nodig, maar afdwingen van TLS 1,2 voor nieuw gemaakte servers. U wordt aangeraden TLS 1,0 en 1,1 te gebruiken en vervolgens de TLS-versie voor uw servers bij te werken. Meer [informatie over het configureren van TLS](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>PostgreSQL-extensies

PostgreSQL biedt de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van extensies. Extensies bundelen meerdere SQL-objecten in één pakket dat met één opdracht kan worden geladen in of verwijderd uit uw database. Nadat de gegevens in de database zijn geladen, functioneren de extensies als ingebouwde functies.

- [Post gres 11-extensies](./concepts-extensions.md#postgres-11-extensions)
- [Post gres 10-extensies](./concepts-extensions.md#postgres-10-extensions)
- [Post gres 9,6-extensies](./concepts-extensions.md#postgres-96-extensions)
- [Post gres 9,5-extensies](./concepts-extensions.md#postgres-95-extensions)

Zie [postgresql-extensies gebruiken op één server voor](concepts-extensions.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Gegevens migreren met dump en herstel](howto-migrate-using-dump-and-restore.md)
- [Gegevens migreren met importeren en exporteren](howto-migrate-using-export-and-import.md)
