---
title: Gebruikers maken-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u nieuwe gebruikers accounts kunt maken om te communiceren met een Azure Database for PostgreSQL-één-server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: c2d0cfc15457d45701f129ae329295064d773a09
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604104"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Gebruikers maken in Azure Database for PostgreSQL-één server

In dit artikel wordt beschreven hoe u gebruikers kunt maken binnen een Azure Database for PostgreSQL-server.

Als u meer wilt weten over het maken en beheren van Azure-abonnements gebruikers en hun bevoegdheden, gaat u naar het [artikel over toegangs beheer op basis van rollen (Azure RBAC) van Azure](../role-based-access-control/built-in-roles.md) of bekijkt u [hoe u rollen kunt aanpassen](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Het beheerdersaccount voor de server

Wanneer u uw Azure Database for PostgreSQL voor het eerst hebt gemaakt, hebt u de gebruikers naam en het wacht woord voor de server beheerder opgegeven. Voor meer informatie kunt u de [Snelstartgids](quickstart-create-server-database-portal.md) volgen om de stapsgewijze benadering te bekijken. Omdat de gebruikers naam van de server beheerder een aangepaste naam is, kunt u de gekozen gebruikers naam voor de server beheerder vinden in de Azure Portal.

De Azure Database for PostgreSQL-server wordt gemaakt met de drie standaard rollen gedefinieerd. U kunt deze rollen zien door de opdracht uit te voeren: `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- de gebruiker van de server beheerder

De gebruiker van de server beheerder is lid van de rol azure_pg_admin. Het account van de server beheerder maakt echter geen deel uit van de azure_superuser rol. Omdat deze service een beheerde PaaS-service is, maakt alleen micro soft deel uit van de rol van super gebruiker.

De PostgreSQL-engine gebruikt bevoegdheden om de toegang tot database objecten te beheren, zoals beschreven in de [product documentatie van postgresql](https://www.postgresql.org/docs/current/static/sql-createrole.html). De gebruiker van de server beheerder heeft in Azure Database for PostgreSQL de volgende bevoegdheden verleend: aanmelding, gebruiker, overnemen, CREATEDB, CREATEROLE, replicatie

De gebruikers account van de server beheerder kan worden gebruikt om extra gebruikers te maken en deze gebruikers toe te kennen aan de azure_pg_admin rol. Het account voor de server beheerder kan ook worden gebruikt voor het maken van minder privilegede gebruikers en rollen die toegang hebben tot afzonderlijke data bases en schema's.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Aanvullende gebruikers met beheerders rechten maken in Azure Database for PostgreSQL

1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal.

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals pgAdmin of psql.
   Als u niet zeker weet hoe u verbinding kunt maken, raadpleegt u [Quick](./quickstart-create-server-database-portal.md) start

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de nieuwe gebruikers naam voor de waarde van de tijdelijke aanduiding <new_user> en vervang het wacht woord voor de tijdelijke aanduiding door uw eigen sterke wacht woord. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Database gebruikers maken in Azure Database for PostgreSQL

1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal.

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals pgAdmin of psql.

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de waarde van de tijdelijke aanduiding door `<db_user>` de gewenste nieuwe gebruikers naam en de tijdelijke aanduiding voor de `<newdb>` naam van uw eigen data base. Vervang het wacht woord voor de tijdelijke aanduiding door uw eigen sterke wacht woord.

   Met deze SQL-code syntaxis maakt u een nieuwe Data Base met de naam testdb, bijvoorbeeld als doel. Vervolgens wordt er een nieuwe gebruiker in de PostgreSQL-service gemaakt en worden er verbindings rechten verleend aan de nieuwe Data Base voor die gebruiker.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Als u een Administrator-account gebruikt, moet u mogelijk extra bevoegdheden verlenen om de objecten in de-data base te beveiligen. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/current/static/ddl-priv.html) voor meer informatie over database rollen en bevoegdheden. Bijvoorbeeld:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Meld u aan bij de server, waarbij u de aangewezen data base opgeeft met de nieuwe gebruikers naam en wacht woord. In dit voor beeld wordt de psql-opdracht regel weer gegeven. Met deze opdracht wordt u gevraagd om het wacht woord voor de gebruikers naam. Vervang uw eigen server naam, database naam en gebruikers naam.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de computers van de nieuwe gebruikers zodat ze verbinding kunnen maken: [Azure database for PostgreSQL firewall regels maken en beheren met behulp van de Azure Portal](howto-manage-firewall-using-portal.md) of [Azure cli](howto-manage-firewall-using-cli.md).

Zie PostgreSQL-product documentatie voor [database rollen en bevoegdheden](https://www.postgresql.org/docs/current/static/user-manag.html), [syntaxis voor granting](https://www.postgresql.org/docs/current/static/sql-grant.html)en [privileges](https://www.postgresql.org/docs/current/static/ddl-priv.html)(Engelstalig) voor meer informatie over het beheer van gebruikers accounts.
