---
title: Connect and query-single server MySQL
description: Koppelingen naar Quick starts die laten zien hoe u verbinding maakt met uw Azure mijn SQL Database één server en query's uitvoert.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546429"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Verbinding maken en een query uitvoeren op een overzicht van Azure data base for MySQL-één server

Het volgende document bevat koppelingen naar voor beelden waarin wordt getoond hoe u verbinding maakt met Azure Database for MySQL één server. Deze hand leiding bevat ook de TLS-aanbevelingen en-bibliotheken die u kunt gebruiken om verbinding te maken met de server in ondersteunde talen hieronder.

## <a name="quickstarts"></a>Snelstartgidsen

| Snelstartgids | Beschrijving |
|---|---|
|[MySQL Workbench](connect-workbench.md)|In deze Quick start ziet u hoe u de MySQL Workbench-client kunt gebruiken om verbinding te maken met een Data Base. U kunt vervolgens MySQL-instructies gebruiken om gegevens in de-data base te zoeken, in te voegen, bij te werken en te verwijderen.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|In dit artikel wordt beschreven hoe u **mysql.exe** uitvoert in [Azure Cloud shell](../cloud-shell/overview.md) om verbinding te maken met uw server en vervolgens instructies uitvoert om gegevens in de Data Base op te vragen, in te voegen, bij te werken en te verwijderen.|
|[MySQL met Visual Studio](https://www.mysql.com/why-mysql/windows/visualstudio)|U kunt MySQL voor Visual Studio gebruiken om verbinding te maken met uw MySQL-server. MySQL voor Visual Studio wordt rechtstreeks geïntegreerd in Server Explorer zodat u eenvoudig nieuwe verbindingen kunt instellen en met database objecten kunt werken.|
|[PHP](connect-php.md)|In deze Quick Start wordt gedemonstreerd hoe u PHP gebruikt om een programma te maken dat verbinding maakt met een Data Base en MySQL-instructies gebruikt om gegevens op te vragen.|
|[Java](connect-java.md)|In deze Quick start ziet u hoe u Java gebruikt om verbinding te maken met een Data Base en vervolgens MySQL-instructies gebruikt om gegevens op te vragen.|
|[Node.js](connect-nodejs.md)|In deze Quick start ziet u hoe u Node.js gebruikt om een programma te maken dat verbinding maakt met een Data Base en MySQL-instructies gebruikt om gegevens op te vragen.|
|[.NET (C#)](connect-csharp.md)|In deze Quick start ziet u hoe u met use.NET (C#) een C#-programma maakt om verbinding te maken met een Data Base en MySQL-instructies gebruikt om gegevens op te vragen.|
|[Go](connect-go.md)|In deze quickstart ziet u hoe u Go kunt gebruiken om verbinding te maken met een database. Bovendien worden er Transact-SQL-instructies voor het doorzoeken en wijzigen van gegevens beschreven.|
|[Python](connect-python.md)|In deze Quick start ziet u hoe u python gebruikt om verbinding te maken met een Data Base en MySQL-instructies gebruikt om gegevens op te vragen. |
|[Ruby](connect-ruby.md)|In deze Quick Start wordt gedemonstreerd hoe u ruby gebruikt om een programma te maken dat verbinding maakt met een Data Base en MySQL-instructies gebruikt om gegevens op te vragen.|
|[C++](connect-cpp.md)|In deze Quick start ziet u hoe u met C++ + een programma maakt om verbinding te maken met een Data Base en query gegevens kunt gebruiken.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-overwegingen voor de connectiviteit van databases

Transport Layer Security (TLS) wordt gebruikt door alle Stuur Programma's die door micro soft worden geleverd of ondersteund voor het maken van verbinding met data bases in Azure Database for MySQL. Er is geen speciale configuratie nodig, maar afdwingen van TLS 1,2 voor nieuw gemaakte servers. U wordt aangeraden TLS 1,0 en 1,1 te gebruiken en vervolgens de TLS-versie voor uw servers bij te werken. Meer [informatie over het configureren van TLS](howto-tls-configurations.md)

## <a name="libraries"></a>Bibliotheken

Azure Database for MySQL maakt gebruik van de populairste Community-editie van de wereld van de MySQL-data base. Daarom is het compatibel met een groot aantal programmeer talen en-stuur Programma's. Het doel is om de drie meest recente versies MySQL-Stuur Programma's te ondersteunen en inspanningen te doen met auteurs van de open source-community om de functionaliteit en de bruikbaarheid van MySQL-Stuur Programma's voortdurend te verbeteren.

Bekijk welke [Stuur Programma's](concepts-compatibility.md) compatibel zijn met Azure database for MySQL één server.

## <a name="next-steps"></a>Volgende stappen

- [Gegevens migreren met dump en herstel](concepts-migrate-dump-restore.md)
- [Gegevens migreren met importeren en exporteren](concepts-migrate-import-export.md)