---
title: Compatibiliteit van Stuur Programma's en hulpprogram ma's-Azure Database for MySQL
description: In dit artikel worden de MySQL-Stuur Programma's en-beheer hulpprogramma's beschreven die compatibel zijn met Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 59f70659f25f925f8f802b0fcaa1f61d4e7f7dc0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106935"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-Stuur Programma's en-beheer hulpprogramma's die compatibel zijn met Azure Database for MySQL

In dit artikel worden de Stuur Programma's en beheer Programma's beschreven die compatibel zijn met Azure Database for MySQL één server.

>[!NOTE]
>Dit artikel is alleen van toepassing op Azure Database for MySQL één server om ervoor te zorgen dat de Stuur Programma's compatibel zijn met de [connectiviteits architectuur](concepts-connectivity-architecture.md) van een service met één server. [Azure database for MySQL flexibele server](/../flexible-server/overview.md) is compatibel met alle Stuur Programma's en hulpprogram ma's die worden ondersteund en compatibel met de MySQL Community Edition. 

## <a name="mysql-drivers"></a>MySQL-Stuur Programma's
Azure Database for MySQL maakt gebruik van de populairste Community-editie van de wereld van de MySQL-data base. Daarom is het compatibel met een groot aantal programmeer talen en-stuur Programma's. Het doel is om de drie meest recente versies MySQL-Stuur Programma's te ondersteunen en inspanningen te doen met auteurs van de open source-community om de functionaliteit en de bruikbaarheid van MySQL-Stuur Programma's voortdurend te verbeteren. In de volgende tabel vindt u een lijst met stuur Programma's die zijn getest en die compatibel zijn met Azure Database for MySQL 5,6 en 5,7.

| **Programmeer taal** | **Stuurprogramma** | **Koppelingen** | **Compatibele versies** | **Incompatibele versies** | **Opmerkingen** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | Voeg MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT toe aan de connection string voor PHP 7,0-verbinding met SSL-MySQLi. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Bob ingesteld: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` optie op ONWAAR.|
| .NET | Asynchrone MySQL-Connector voor .NET | https://github.com/mysql-net/MySqlConnector <br> [Installatie pakket van Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 en na | 0.26.5 en vóór | |
| .NET | MySQL-Connector/NET | https://github.com/mysql/mysql-connector-net | 6.6.3, 7,0, 8,0 |  | Een coderings fout kan ertoe leiden dat verbindingen mislukken op sommige niet-UTF8 Windows-systemen. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Installatie pakket van NPM:<br> Uitvoeren `npm install mysql` vanaf NPM | 2.15 | 2.14.1 en vóór | |
| Node.js | knoop punt-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Ga naar MySQL-stuur programma | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 en vóór | Gebruik `allowNativePasswords=true` in de Connection String voor versie 1,3. Versie 1,4 bevat een oplossing en `allowNativePasswords=true` is niet meer vereist. |
| Python | MySQL-Connector/python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, 8.0.16 + gebruiken met MySQL 8,0  | 1.2.2 en voor | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regressie in web2py) | |
| Java | MariaDB-Connector/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 en vóór | | 
| Java | MySQL-Connector/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +, 8.0.17 + gebruiken met MySQL 8,0 | 5.1.20 en lager | |
| C | MySQL-Connector/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | MySQL-Connector/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | MySQL-Connector/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 en lager | | 
| C++ | MySQL + +| https://github.com/tangentsoft/mysqlpp | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL-Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | Vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Beheerprogramma's
Het compatibiliteits voordeel kan ook worden uitgebreid naar beheer hulpprogramma's voor data bases. De bestaande hulpprogram ma's moeten blijven werken met Azure Database for MySQL, zolang de data base is gemanipuleerd binnen de gebruikers machtigingen. De volgende tabel bevat drie algemene hulpprogram ma's voor database beheer die zijn getest en die compatibel zijn met Azure Database for MySQL 5,6 en 5,7.

|                                     | **MySQL Workbench 6. x en Maxi maal** | **Navicat 12** | **PHPMyAdmin 4. x en Maxi maal** | **dbForge Studio voor MySQL 9,0** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------| :------------------------------- |
| **Maken, bijwerken, lezen, schrijven, verwijderen** | X | X | X | X |
| **SSL-verbinding** | X | X | X | X |
| **SQL-query automatisch aanvullen** | X | X |  | X |
| **Gegevens importeren en exporteren** | X | X | X | X |
| **Exporteren naar meerdere indelingen** | X | X | X | X |
| **Back-up en herstel** |  | X |  | X |
| **Server parameters weer geven** | X | X | X | X |
| **Client verbindingen weer geven** | X | X | X | X |

## <a name="next-steps"></a>Volgende stappen

- [Verbindingsproblemen met Azure Databases for MySQL oplossen](howto-troubleshoot-common-connection-issues.md)
