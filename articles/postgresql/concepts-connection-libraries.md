---
title: Verbindingsbibliotheken - Azure Database for PostgreSQL - Enkele server
description: In dit artikel worden verschillende bibliotheken en stuurprogramma's beschreven die u kunt gebruiken bij het coderen van toepassingen om verbinding te maken en query's uit te voeren Azure Database for PostgreSQL - enkele server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b8a1526605195b5eb24d8044f42b70ca5336bf7c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878304"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Verbindingsbibliotheken Azure Database for PostgreSQL - Enkele server
In dit artikel vindt u bibliotheken en stuurprogramma's die ontwikkelaars kunnen gebruiken om toepassingen te ontwikkelen om verbinding te maken met en query's uit te Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Clientinterfaces
De meeste taalclientbibliotheken die worden gebruikt om verbinding te maken met de PostgreSQL-server, zijn externe projecten en worden onafhankelijk gedistribueerd. De vermelde bibliotheken worden ondersteund op het Windows-, Linux- en Mac-platform om verbinding te maken met Azure Database for PostgreSQL. Er worden verschillende quickstartvoorbeelden weergegeven in de sectie Volgende stappen.

| **Taal** | **Clientinterface** | **Aanvullende informatie** | **Downloaden** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [Psycopg](http://initd.org/psycopg/) | DB API 2.0-compatibel | [Downloaden](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Database-extensie | [Installeren](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm-pakket](https://www.npmjs.com/package/pg) | Pure JavaScript-niet-blokkerende client | [Installeren](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Type 4 JDBC-stuurprogramma | [Downloaden](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby-interface | [Downloaden](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Pakket pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres-stuurprogramma | [Installeren](https://github.com/lib/pq/blob/master/README.md) |
| \#C/.NET | [Npgsql](https://www.npgsql.org/) | ADO.NET-gegevensprovider | [Downloaden](https://dotnet.microsoft.com/download) |
| ODBC | [Psqlodbc](https://odbc.postgresql.org/) | ODBC-stuurprogramma | [Downloaden](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [Libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primaire C-taalinterface | Inbegrepen |
| C++ | [Libpqxx](http://pqxx.org/) | Nieuwe C++-interface | [Downloaden](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Volgende stappen
Lees deze quickstarts over het maken van verbinding met en het uitvoeren van query'Azure Database for PostgreSQL met behulp van de taal van uw keuze:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Go](./connect-go.md)
