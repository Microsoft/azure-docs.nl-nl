---
title: 'Quickstart: Verbinding maken met Ruby - Azure Database for PostgreSQL - één server'
description: Deze quickstart bevat een voorbeeld van Ruby-code die u kunt gebruiken om verbinding te maken en gegevens op te vragen uit Azure Database for PostgreSQL - één server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 20e45454284af230da74896d5b3f5e9da676dbb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831700"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: Ruby gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for PostgreSQL - één server

In deze snelstartgids ziet u hoe u met behulp van een [Ruby](https://www.ruby-lang.org)-toepassing verbinding maakt met een Azure Database voor PostgreSQL. U ziet hier hoe u SQL-instructies gebruikt om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen. In de stappen van dit artikel wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met behulp van Ruby en geen ervaring hebt met het werken met Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten
In deze snelstartgids worden de resources die in een van deze handleidingen zijn gemaakt, als uitgangspunt gebruikt:
- [Database maken - Portal](quickstart-create-server-database-portal.md)
- [Database maken - Azure CLI](quickstart-create-server-database-azure-cli.md)

Daarnaast moet ook het volgende zijn geïnstalleerd:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby pg](https://rubygems.org/gems/pg/), de PostgreSQL-module voor Ruby

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen
Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database voor PostgreSQL. U hebt de volledig gekwalificeerde servernaam en aanmeldingsreferenties nodig.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik in het menu aan de linkerkant in Azure Portal op **Alle resources** en zoek naar de server die u hebt gemaakt (bijvoorbeeld **mydemoserver**).
3. Klik op de servernaam.
4. Ga naar het venster **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord in dit venster opnieuw instellen.
 :::image type="content" source="./media/connect-ruby/1-connection-string.png" alt-text="Servernaam Azure Database for PostgreSQL":::

> [!NOTE]
> Het `@`-symbool in de Azure Postgres-gebruikersnaam is als URL gecodeerd als `%40` in alle verbindingstekenreeksen.

## <a name="connect-and-create-a-table"></a>Verbinding maken en een tabel maken
Gebruik de volgende code om een tabel te verbinden en te maken met de SQL-instructie **CREATE TABLE**, gevolgd door **INSERT INTO**-instructies om rijen in de tabel toe te voegen.

In de code wordt een ```PG::Connection```-object met de ```new```-constructor gebruikt om verbinding te maken met Azure Database for PostgreSQL. Vervolgens wordt de methode ```exec()``` aangeroepen op de opdrachten DROP, CREATE TABLE en INSERT INTO uit te voeren. Met de code wordt met de klasse ```PG::Error``` gecontroleerd op fouten. Vervolgens wordt methode ```close()``` aangeroepen om de verbinding vóór het sluiten te verbreken. Zie de referentiedocumentatie voor Ruby Pg (Engelstalig) voor meer informatie over deze klassen en methoden.

Vervang de tekenreeksen `host`, `database`, `user` en `password` door uw eigen waarden.


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Gegevens lezen
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met de SQL-instructie **SELECT**.

In de code wordt het object ```PG::Connection``` gebruikt met de constructor ```new``` om verbinding te maken met Azure Database voor PostgreSQL. De methode ```exec()``` wordt dan aangeroepen om de opdracht SELECT uit te voeren. De resultaten worden in een resultatenset opgeslagen. De resultatensetverzameling wordt herhaald met de lus `resultSet.each do`. De huidige rijwaarden worden opgeslagen in de variabele `row`. Met de code wordt met de klasse ```PG::Error``` gecontroleerd op fouten. Vervolgens wordt methode ```close()``` aangeroepen om de verbinding vóór het sluiten te verbreken. Zie de referentiedocumentatie voor Ruby Pg (Engelstalig) voor meer informatie over deze klassen en methoden.

Vervang de tekenreeksen `host`, `database`, `user` en `password` door uw eigen waarden.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Gegevens bijwerken
Gebruik de volgende code om verbinding te maken en de gegevens bij te werken met de SQL-instructie **UPDATE**.

In de code wordt het object ```PG::Connection``` gebruikt met de constructor ```new``` om verbinding te maken met Azure Database voor PostgreSQL. Vervolgens wordt de methode ```exec()``` aangeroepen om de opdracht UPDATE uit te voeren. Met de code wordt met de klasse ```PG::Error``` gecontroleerd op fouten. Vervolgens wordt methode ```close()``` aangeroepen om de verbinding vóór het sluiten te verbreken. Zie [de referentiedocumentatie voor Ruby Pg](https://rubygems.org/gems/pg) (Engelstalig) voor meer informatie over deze klassen en methoden.

Vervang de tekenreeksen `host`, `database`, `user` en `password` door uw eigen waarden.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Gegevens verwijderen
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met de SQL-instructie **DELETE**.

In de code wordt het object ```PG::Connection``` gebruikt met de constructor ```new``` om verbinding te maken met Azure Database voor PostgreSQL. Vervolgens wordt de methode ```exec()``` aangeroepen om de opdracht UPDATE uit te voeren. Met de code wordt met de klasse ```PG::Error``` gecontroleerd op fouten. Vervolgens wordt methode ```close()``` aangeroepen om de verbinding vóór het sluiten te verbreken.

Vervang de tekenreeksen `host`, `database`, `user` en `password` door uw eigen waarden.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
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
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md) <br/>
> [!div class="nextstepaction"]
> [Ruby Pg-referentiedocumentatie](https://rubygems.org/gems/pg)
