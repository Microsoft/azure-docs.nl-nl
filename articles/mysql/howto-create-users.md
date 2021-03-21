---
title: Data bases en gebruikers maken-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u nieuwe gebruikers accounts maakt om te communiceren met een Azure Database for MySQL-server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 9a6346a2b62c81dd74cf0ebe9a85df12d3488679
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251247"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Data bases en gebruikers maken in Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In dit artikel wordt beschreven hoe u gebruikers maakt in Azure Database for MySQL.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

Wanneer u uw Azure Database for MySQL server voor het eerst hebt gemaakt, hebt u de gebruikers naam en het wacht woord voor de server beheerder opgegeven. Zie voor meer informatie deze [Snelstartgids](quickstart-create-mysql-server-database-using-azure-portal.md). U kunt de gebruikers naam van de server beheerder bepalen in de Azure Portal.

De gebruiker van de server beheerder heeft de volgende bevoegdheden:

   SELECTEREN, INVOEGEN, BIJWERKEN, VERWIJDEREN, MAKEN, NEERZETTEN, OPNIEUW LADEN, VERWERKEN, VERWIJZINGEN, INDEX, WIJZIGEN, DATA BASES WEER GEVEN, TIJDELIJKE TABELLEN MAKEN, TABELLEN VERGREN DELEN, UITVOEREN, REPLICATIE SLAVE, REPLICATIE CLIENT, WEER GAVE, GEBRUIKER WEER GEVEN, GEBEURTENIS, TRIGGER

Nadat u een Azure Database for MySQL server hebt gemaakt, kunt u het eerste beheerders account voor de server gebruiken om meer gebruikers te maken en beheerders toegang te verlenen. U kunt ook het Server beheerders account gebruiken om minder privilegede gebruikers te maken die toegang hebben tot afzonderlijke database schema's.

> [!NOTE]
> De rol SUPER privilege en DBA worden niet ondersteund. Lees de [bevoegdheden](concepts-limits.md#privileges--data-manipulation-support) in het artikel beperkingen om te begrijpen wat er niet wordt ondersteund in de service.
>
> Wachtwoord modules zoals `validate_password` en `caching_sha2_password` worden niet ondersteund door de service.

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Een Data Base maken met een gebruiker die geen beheerder is in Azure Database for MySQL

1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal.

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals MySQL Workbench, mysql.exe of HeidiSQL.

   Als u niet zeker weet hoe u verbinding kunt maken, raadpleegt u [verbinding maken en query's uitvoeren voor één server](./connect-workbench.md) , of [verbinding maken met gegevens en Query's uitvoeren voor flexibele servers](./flexible-server/connect-workbench.md).

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de waarde van de tijdelijke aanduiding door `db_user` de gewenste nieuwe gebruikers naam. Vervang de waarde van de tijdelijke aanduiding door `testdb` de naam van uw data base.

   Met deze SQL-code maakt u een nieuwe Data Base met de naam testdb. Vervolgens wordt er een nieuwe gebruiker in de MySQL-service gemaakt en worden alle bevoegdheden voor het nieuwe database schema (testdb. \* ) aan die gebruiker verleend.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Controleer de subsidies in de Data Base:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Meld u aan bij de server, waarbij u de aangewezen data base opgeeft en de nieuwe gebruikers naam en wacht woord gebruikt. In dit voor beeld wordt de MySQL-opdracht regel weer gegeven. Wanneer u deze opdracht gebruikt, wordt u gevraagd om het wacht woord van de gebruiker. Gebruik uw eigen server naam, database naam en gebruikers naam.

   ### <a name="single-server"></a>[Single Server](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   ### <a name="flexible-server"></a>[Flexible Server](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>Meer gebruikers met beheerders rechten maken in Azure Database for MySQL

1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal.

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals MySQL Workbench, mysql.exe of HeidiSQL.

   Als u niet zeker weet hoe u verbinding kunt maken, raadpleegt u [MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen](./connect-workbench.md).

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de waarde van de tijdelijke aanduiding door `new_master_user` de nieuwe gebruikers naam. Met deze syntaxis worden de vermelde bevoegdheden verleend voor alle database schema's (*.*) aan de gebruiker ( `new_master_user` in dit voor beeld).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. De subsidies verifiëren:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Alle Azure Database for MySQL-servers worden gemaakt met een gebruiker met de naam ' azure_superuser '. Dit is een systeem account dat door micro soft is gemaakt voor het beheren van de server voor het uitvoeren van bewaking, back-ups en andere regel matig onderhoud. Engineers van de oproep kunnen dit account ook gebruiken om toegang te krijgen tot de server tijdens een incident met certificaat verificatie en moeten toegang aanvragen met Just-in-time (JIT)-processen.

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de computers van de nieuwe gebruikers zodat ze verbinding kunnen maken:

* [Firewall regels maken en beheren op één server](howto-manage-firewall-using-portal.md)
* [Firewall regels maken en beheren op een flexibele server](flexible-server/how-to-connect-tls-ssl.md)

Zie voor meer informatie over beheer van gebruikers accounts de MySQL-product documentatie voor [Gebruikersaccountbeheer](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [syntaxis van Grant](https://dev.mysql.com/doc/refman/5.7/en/grant.html)en [privileges](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
