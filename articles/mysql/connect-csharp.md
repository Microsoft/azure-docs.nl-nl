---
title: 'Quickstart: Verbinding maken met C# - Azure Database for MySQL'
description: Deze snelstartgids bevat een voorbeeld van C#-code (.NET) dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit een Azure Database voor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 3b90d8819b5d327c3ccd143257198c7ec8538f03
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535823"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: .NET (C#) gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL

In deze quickstart ziet u hoe u met behulp van een C#-toepassing verbinding maakt met Azure Database voor MySQL. U ziet hier hoe u SQL-instructies gebruikt om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen. 

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

[Ondervindt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)

## <a name="create-a-c-project"></a>Een C#-project maken
Voer in een opdrachtprompt het volgende uit:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen
Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database voor MySQL. U hebt de volledig gekwalificeerde servernaam en aanmeldingsreferenties nodig.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik in het menu aan de linkerkant in Azure Portal op **Alle resources** en zoek naar de server die u hebt gemaakt (bijvoorbeeld **mydemoserver**).
3. Klik op de servernaam.
4. Ga naar het venster **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord in dit venster opnieuw instellen.
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="Naam van Azure Database voor MySQL-server":::

## <a name="step-1-connect-and-insert-data"></a>Stap 1: Gegevens verbinden en invoegen
Gebruik de volgende code om verbinding te maken en de gegevens te laden met behulp van de SQL-instructies `CREATE TABLE` en `INSERT INTO`. De code gebruikt de methoden van de klasse `MySqlConnection`:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) om een verbinding tot stand te brengen met MySQL.
- Met [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) wordt de eigenschap CommandText ingesteld
- Met de methode [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) worden de databaseopdrachten uitgevoerd. 

Vervang de parameters `Server`, `Database`, `UserID` en `Password` door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Ondervindt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)


## <a name="step-2-read-data"></a>Stap 2: Gegevens lezen

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie `SELECT`. De code gebruikt de klasse `MySqlConnection` met methoden:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) om een verbinding tot stand te brengen met MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) om de eigenschap CommandText in te stellen.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync) om de databaseopdrachten uit te voeren. 
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) om naar de records in de resultaten te gaan. Vervolgens worden in de code GetInt32() en GetString() gebruikt om de waarden in de record te parseren.


Vervang de parameters `Server`, `Database`, `UserID` en `Password` door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Ondervindt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)

## <a name="step-3-update-data"></a>Stap 3: Gegevens bijwerken
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie `UPDATE`. De code gebruikt de klasse `MySqlConnection` met methode:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) om een verbinding tot stand te brengen met MySQL. 
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) om de eigenschap CommandText in te stellen
- Met de methode [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) worden de databaseopdrachten uitgevoerd. 


Vervang de parameters `Server`, `Database`, `UserID` en `Password` door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[Ondervindt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)

## <a name="step-4-delete-data"></a>Stap 4: Gegevens verwijderen
Gebruik de volgende code om verbinding te maken en de gegevens te verwijderen met behulp van de SQL-instructie `DELETE`. 

De code gebruikt de klasse `MySqlConnection` met methode
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) om een verbinding tot stand te brengen met MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) om de eigenschap CommandText in te stellen.
- Met de methode [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) worden de databaseopdrachten uitgevoerd. 


Vervang de parameters `Server`, `Database`, `UserID` en `Password` door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
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

[Kunt u niet vinden wat u zoekt?Laat het ons weten.](https://aka.ms/mysql-doc-feedback)
