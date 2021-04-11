---
title: 'Quickstart: Verbinding maken met Python - Azure Database for MySQL'
description: Deze snelstartgids bevat enkele voorbeelden van Python-code die u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit Azure Database voor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889022"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Python gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL

In deze quickstart maakt u verbinding met een Azure Database for MySQL met behulp van Python. U gebruikt vervolgens SQL-instructies om gegevens op te vragen, in te voegen, bij te werken en te verwijderen in de database vanaf Mac-, Ubuntu Linux- en Windows-platforms. 

## <a name="prerequisites"></a>Vereisten
Voor deze quickstart hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
- Eén Azure Database for MySQL-server maken met behulp van [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> of [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md), als u er nog geen hebt.
- Voltooi **EEN** van de onderstaande acties om connectiviteit in te schakelen, afhankelijk van of u openbare toegang of privétoegang hebt.

   |Bewerking| Verbindingsmethode|Instructiegids|
   |:--------- |:--------- |:--------- |
   | **Firewallregels configureren** | Openbaar | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **Service-eindpunt configureren** | Openbaar | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **Privékoppeling configureren** | Privé | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Een database en een gebruiker die geen beheerder is maken](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Python en de MySQL-connector installeren

Installeer Python en de MySQL-connector voor Python op uw computer met behulp van de volgende stappen: 

> [!NOTE]
> In deze quickstart wordt [MySQL Connector/Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/) gebruikt.

1. Download en installeer [Python 3.7 of hoger](https://www.python.org/downloads/) voor uw besturingssysteem. Zorg ervoor dat u Python toevoegt aan uw `PATH`, omdat de MySQL-connector dat vereist.
   
2. Open een opdrachtprompt of `bash` shell en controleer uw Python-versie door `python -V` uit te voeren met de schakeloptie voor hoofdletter V.
   
3. Het installatieprogramma voor het `pip`-pakket is opgenomen in de meest recente versies van Python. Werk `pip` bij naar de nieuwste versie door `pip install -U pip` uit te voeren. 
   
   Als `pip` niet is geïnstalleerd, kunt u het downloaden en installeren met `get-pip.py`. Zie [Installatie](https://pip.pypa.io/en/stable/installing/) voor meer informatie. 
   
4. Gebruik `pip` om de MySQL-connector voor Python en de bijbehorende afhankelijkheden te installeren:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Ondervindt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database for MySQL van de Azure-portal. U hebt de servernaam, databasenaam en aanmeldingsreferenties nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
   
1. Zoek en selecteer in de portalzoekbalk de Azure Database for MySQL server die u hebt gemaakt, zoals **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Naam van Azure Database voor MySQL-server":::
   
1. Ga naar de pagina **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord op deze pagina opnieuw instellen.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Naam 2 van Azure Database for MySQL-server":::

## <a name="step-1-create-a-table-and-insert-data"></a>Stap 1: Een tabel maken en gegevens invoegen

Gebruik de volgende code om verbinding te maken met de server en database, een tabel te maken en gegevens te laden met behulp van de SQL-instructie **INSERT**. Met de code importeert u de bibliotheek mysql.connector en wordt de volgende methode gebruikt:
- functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database for MySQL, met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. 
- met de methode [cursor.execute](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wordt de SQL-query uitgevoerd op de MySQL-database. 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) wanneer u klaar bent met een cursor.
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) om de verbinding met de verbinding te verbreken.

> [!IMPORTANT]
> - SSL is standaard ingeschakeld. Mogelijk moet u het [SSL-certificaat DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) downloaden om verbinding te maken vanuit uw lokale omgeving.
> - Vervang de tijdelijke aanduidingen `<mydemoserver>`, `<myadmin>`, `<mypassword>` en `<mydatabase>` door de waarden voor uw MySQL-server en -database.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[Ondervindt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Stap 2: Gegevens lezen

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **SELECT**. Met de code importeert u de bibliotheek mysql.connector en met de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wordt de SQL-query uitgevoerd op de MySQL-database. 

De code leest de gegevensrijen met behulp van de methode [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html), bewaart de resultatenset in een verzamelingrij en gebruikt een `for`-iterator om de rijen te doorlopen.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Stap 3: Gegevens bijwerken

Gebruik de volgende code om verbinding te maken en de gegevens bij te werken met behulp van de SQL-instructie **UPDATE**. Met de code importeert u de bibliotheek mysql.connector en met de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wordt de SQL-query uitgevoerd op de MySQL-database. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Stap 4: Gegevens verwijderen

Gebruik de volgende code om verbinding te maken en de gegevens te verwijderen met behulp van de SQL-instructie **DELETE**. Met de code importeert u de bibliotheek mysql.connector en met de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wordt de SQL-query uitgevoerd op de MySQL-database. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

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
> [Een Azure Database for MySQL-server beheren met CLI](./how-to-manage-single-server-cli.md)

[Kunt u niet vinden wat u zoekt? Laat het ons weten.](https://aka.ms/mysql-doc-feedback)
