---
title: Compatibiliteit van Stuur Programma's en hulpprogram ma's-Azure Database for MariaDB
description: In dit artikel worden de MariaDB-Stuur Programma's en-beheer Programma's beschreven die compatibel zijn met Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 49fd2c8da3ba0a44f4901bc6c7a2241e9e20df4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659866"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB-Stuur Programma's en-beheer hulpprogramma's die compatibel zijn met Azure Database for MariaDB

In dit artikel worden de Stuur Programma's en beheer Programma's beschreven die compatibel zijn met Azure Database for MariaDB.

## <a name="mariadb-drivers"></a>MariaDB-Stuur Programma's

Azure Database for MariaDB maakt gebruik van de Community Edition van MariaDB server. Daarom is het compatibel met een groot aantal programmeer talen en-stuur Programma's. De API en het Protocol van MariaDB zijn compatibel met de api's en protocollen die worden gebruikt door MySQL. Dit betekent dat connectors die werken met MySQL ook moeten werken met MariaDB.

Het doel is het ondersteunen van de drie meest recente versies van MariaDB-Stuur Programma's en inspanningen met schrijvers van de open source-community om de functionaliteit en de bruikbaarheid van MariaDB-Stuur Programma's voortdurend te verbeteren. In de volgende tabel vindt u een lijst met stuur Programma's die zijn getest en die compatibel zijn met Azure Database for MariaDB 10,2:

**Stuurprogramma** | **Koppelingen** | **Compatibele versies** | **Incompatibele versies** | **Opmerkingen**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | Voeg MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT toe aan de connection string voor PHP 7,0-verbinding met SSL-MySQLi. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Bob ingesteld: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` optie op ONWAAR.
.NET | [MySqlConnector op GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installatie pakket van Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 en na | 0.26.5 en vóór |
MySQL-Connector/NET | [MySQL-Connector/NET](https://github.com/mysql/mysql-connector-net) | 8,0, 7,0, 6,10 |  | Een coderings fout kan ertoe leiden dat verbindingen mislukken op sommige niet-UTF8 Windows-systemen.
Node.js |  [MySQLjs op GitHub](https://github.com/mysqljs/mysql/) <br> Installatie pakket van NPM:<br> Uitvoeren `npm install mysql` vanaf NPM | 2.15 | 2.14.1 en vóór
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 en vóór | Gebruik `allowNativePasswords=true` in de Connection String voor versie 1,3. Versie 1,4 bevat een oplossing en `allowNativePasswords=true` is niet meer vereist.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2 | 1.2.2 en voor |
Java | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 en vóór |

## <a name="management-tools"></a>Beheerprogramma's

Het compatibiliteits voordeel kan ook worden uitgebreid naar beheer hulpprogramma's voor data bases. De bestaande hulpprogram ma's moeten blijven werken met Azure Database for MariaDB, zolang de data base is gemanipuleerd binnen de gebruikers machtigingen. De volgende tabel bevat drie algemene hulpprogram ma's voor database beheer die zijn getest en compatibel zijn met Azure Database for MariaDB 10,2:

| Bewerking | **MySQL Workbench 6. x en Maxi maal** | **Navicat 12** | **PHPMyAdmin 4. x en Maxi maal**
---|---|---|---
Maken, bijwerken, lezen, schrijven, verwijderen | X | X | X
SSL-verbinding | X | X | X
SQL-query automatisch aanvullen | X | X |
Gegevens importeren en exporteren | X | X | X
Exporteren naar meerdere indelingen | X | X | X
Back-up en herstel |  | X |
Server parameters weer geven | X | X | X
Client verbindingen weer geven | X | X | X

## <a name="next-steps"></a>Volgende stappen

- [Verbindingsproblemen met Azure Database for MariaDB oplossen](howto-troubleshoot-common-connection-issues.md)