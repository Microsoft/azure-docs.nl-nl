---
title: 'Quickstart: Verbinding maken met Python - Azure Database for PostgreSQL - individuele server'
description: Deze quickstart bevat een voorbeeld van Python-code dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit een Azure Database for PostgreSQL - individuele server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998937"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: Python gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for PostgreSQL - individuele server

In deze quickstart leert u hoe u verbinding kunt maken met de database in een enkele Azure Database for PostgreSQL-server, en SQL-instructies kunt gebruiken om query's uit te voeren met behulp van Python in macOS, Ubuntu Linux of Windows.

> [!TIP]
> Als u een Django-toepassing met PostgreSQL wilt maken, raadpleegt u de zelfstudie [Tutorial: Deploy a Django web app with PostgreSQL](../app-service/tutorial-python-postgresql-app.md) (Engelstalig).


## <a name="prerequisites"></a>Vereisten
Voor deze quickstart hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
- Maak een enkele Azure Database for PostgreSQL-server met behulp van [Azure Portal](./quickstart-create-server-database-portal.md) <br/> of [Azure CLI](./quickstart-create-server-database-azure-cli.md), als u er nog geen hebt.
- Voltooi **EEN** van de onderstaande acties om connectiviteit in te schakelen, afhankelijk van of u openbare toegang of privétoegang hebt.

  |Bewerking| Verbindingsmethode|Instructiegids|
  |:--------- |:--------- |:--------- |
  | **Firewallregels configureren** | Openbaar | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Service-eindpunt configureren** | Openbaar | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Privékoppeling configureren** | Privé | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9+ of 3.4+.

- Nieuwste [PIP](https://pip.pypa.io/en/stable/installing/)-pakketinstallatieprogramma.
- Installeer [psycopg2](https://pypi.python.org/pypi/psycopg2/) met behulp van `pip install psycopg2` in een terminal of opdrachtpromptvenster. Zie [`psycopg2` installeren](http://initd.org/psycopg/docs/install.html) voor meer informatie.

## <a name="get-database-connection-information"></a>De verbindingsgegevens voor de database ophalen
Voor het maken van verbinding met een Azure Database for PostgreSQL-database zijn de volledig gekwalificeerde servernaam en aanmeldingsreferenties vereist. U kunt deze informatie ophalen uit Azure Portal.

1. Zoek en selecteer in [Azure Portal](https://portal.azure.com/) uw Azure Database for PostgreSQL-servernaam.
1. Kopieer op de pagina **Overzicht** van de server de volledig gekwalificeerde **servernaam** en de **gebruikersnaam met beheerdersrechten**. De volledig gekwalificeerde **servernaam** heeft altijd de vorm *\<my-server-name>.postgres.database.azure.com* en de **gebruikersnaam met beheerdersrechten** heeft altijd van de vorm *\<my-admin-username>@\<my-server-name>* .

   U hebt ook uw beheerderswachtwoord nodig. Als u deze vergeten bent, kunt u deze opnieuw instellen op deze pagina.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Servernaam Azure Database for PostgreSQL":::

> [!IMPORTANT]
>  Vervang de volgende waarden:
>   - `<server-name>` en `<admin-username>` door de waarden die u hebt gekopieerd uit Azure Portal.
>   - `<admin-password>` door uw serverwachtwoord.
>   - `<database-name>` een standaarddatabase met de naam *postgres* is automatisch gemaakt tijdens het maken de server. U kunt de naam van de database wijzigen of [een nieuwe database maken](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) met behulp van SQL-opdrachten.

## <a name="step-1-connect-and-insert-data"></a>Stap 1: Gegevens verbinden en invoegen
Met het volgende codevoorbeeld wordt uw Azure Database for PostgreSQL-database verbonden met behulp van
-  de functie [psycopg2.connect](http://initd.org/psycopg/docs/connection.html), en worden gegevens geladen met een SQL **INSERT**-instructie.
- Met de functie [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) wordt de SQL-query uitgevoerd op de database.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Wanneer de code wordt uitgevoerd, wordt de volgende uitvoer gegenereerd:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Opdrachtregeluitvoer":::


[Ondervindt u problemen? Laat het ons weten](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Stap 2: Gegevens lezen
Met het volgende codevoorbeeld wordt uw Azure Database for PostgreSQL-database verbonden en maakt deze gebruik van
- [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) met de SQL **SELECT**-instructie om gegevens te lezen.
- Met [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) wordt een query geaccepteerd en een resultatenset geretourneerd voor herhaling, met behulp van

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Ondervindt u problemen? Laat het ons weten](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Stap 3: Gegevens bijwerken
In het volgende codevoorbeeld wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) gebruikt met de SQL **UPDATE**-instructie om gegevens bij te werken.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Ondervindt u problemen? Laat het ons weten](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Stap 5: Gegevens verwijderen
Met het volgende codevoorbeeld wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) uitgevoerd met de SQL **DELETE**-instructie om een voorraadartikel te verwijderen dat u eerder hebt ingevoegd.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Ondervindt u problemen? Laat het ons weten](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt opschonen die tijdens deze quickstart zijn gebruikt, verwijdert u de resourcegroep. Dit kan met de volgende opdracht:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een Azure Database for MySQL-server beheren met de portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Een Azure Database for MySQL-server beheren met CLI](./how-to-manage-server-cli.md)<br/>

[Kunt u niet vinden wat u zoekt? Laat het ons weten.](https://aka.ms/postgres-doc-feedback)
