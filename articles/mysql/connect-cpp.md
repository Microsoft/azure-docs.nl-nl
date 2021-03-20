---
title: 'Quickstart: Verbinding maken met C++ - Azure Database for MySQL'
description: Deze snelstartgids bevat een voorbeeld van C++-code dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit een Azure Database voor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: cpp
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: e8b2842300e43a9de88cc0b9b3ae9ce6cd754612
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535840"
---
# <a name="quickstart-use-connectorc-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Connector/C++ gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL

In deze quickstart ziet u hoe u met behulp van een C++-toepassing verbinding maakt met een Azure Database for MySQL. U ziet hier hoe u SQL-instructies gebruikt om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen. In dit artikel wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met C++, maar geen ervaring hebt met het werken met Azure Database for MySQL.

## <a name="prerequisites"></a>Vereisten

In deze quickstart worden de resources die in een van de volgende handleidingen zijn gemaakt, als uitgangspunt gebruikt:
- [Een Azure-database voor een MySQL-server maken met behulp van Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Een Azure-database voor een MySQL-server maken met behulp van Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

U moet ook het volgende doen:
- [.NET Framework](https://www.microsoft.com/net/download) installeren
- [Visual Studio](https://www.visualstudio.com/downloads/) installeren
- [MySQL-connector/C++](https://dev.mysql.com/downloads/connector/cpp/) installeren 
- [Boost](https://www.boost.org/) installeren

> [!IMPORTANT] 
> Controleer of het IP-adres waarmee u verbinding maakt aan de firewallregels van de server is toegevoegd met [Azure Portal](./howto-manage-firewall-using-portal.md) of [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-visual-studio-and-net"></a>Visual Studio en .NET installeren
Voor de stappen in dit gedeelte wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met .NET.

### <a name="windows"></a>**Windows**
- Installeer Visual Studio Community 2019. Visual Studio 2019 Community is een volledig functionele, uitbreidbare en gratis IDE. Met deze IDE kunt u moderne toepassingen maken voor Android-, iOS-, Windows-, web- en databasetoepassingen en cloudservices. U kunt de volledige .NET Framework installeren of slechts .NET Core; in de quickstart wordt met beide codefragmenten gewerkt. Als Visual Studio al is geïnstalleerd op de computer, kunt u de volgende twee stappen overslaan.
   1. Download het [installatieprogramma voor Visual Studio 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Voer het installatieprogramma uit en volg de weergegeven opdrachten om de installatie te voltooien.

### <a name="configure-visual-studio"></a>**Visual Studio configureren**
1. Ga in Visual Studio naar Project > Eigenschappen > Linker > Algemeen > Aanvullende bibliotheekmappen en voeg de map lib\opt toe (bijvoorbeeld: C:\Program Files (x86) \MySQL\MySQL Connector C++ 1.1.9\lib\opt) van de C++-connector toe.
2. Ga in Visual Studio naar Project > Eigenschappen > C/C++ > Algemeen > Aanvullende Include-mappen:
   - Voeg de map \include van de C++-connector toe (bijvoorbeeld: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\include\) toe.
   - Voeg de hoofdmap van de Boost-bibliotheek toe (bijvoorbeeld: C:\boost_1_64_0\).
3. Ga in Visual Studio naar Project > Eigenschappen > Linker > Invoer > Extra afhankelijkheden, en voeg **mysqlcppconn.lib** toe in het tekstveld.
4. Kopieer **mysqlcppconn.dll** uit de bibliotheekmap van de C++-connector in stap 3 naar dezelfde map als het uitvoerbare bestand van de toepassing, of voeg de map toe aan de omgevingsvariabele, zodat de toepassing deze kan vinden.

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen
Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database voor MySQL. U hebt de volledig gekwalificeerde servernaam en aanmeldingsreferenties nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik in het menu aan de linkerkant in Azure Portal op **Alle resources** en zoek naar de server die u hebt gemaakt (bijvoorbeeld **mydemoserver**).
3. Klik op de servernaam.
4. Ga naar het venster **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord in dit venster opnieuw instellen.
 :::image type="content" source="./media/connect-cpp/1_server-overview-name-login.png" alt-text="Naam van Azure Database voor MySQL-server":::

## <a name="connect-create-table-and-insert-data"></a>Verbinden, tabel maken en gegevens invoegen
Gebruik de volgende code om verbinding te maken en de gegevens te laden met behulp van de SQL-instructies **CREATE TABLE** EN **INSERT INTO**. In de code wordt de klasse sql::Driver met de methode connect() gebruikt om een verbinding te maken met MySQL. Vervolgens worden de methoden createStatement() en execute()) gebruikt om de databaseopdrachten uit te voeren. 

Vervang de parameters Host, DBName, User en Password. U kunt de parameters vervangen door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    //please create database "quickstartdb" ahead of time
    con->setSchema("quickstartdb");

    stmt = con->createStatement();
    stmt->execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt->execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con->prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt->setString(1, "banana");
    pstmt->setInt(2, 150);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "orange");
    pstmt->setInt(2, 154);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "apple");
    pstmt->setInt(2, 100);
    pstmt->execute();
    cout << "One row inserted." << endl;

    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="read-data"></a>Gegevens lezen

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **SELECT**. In de code wordt de klasse sql::Driver met de methode connect() gebruikt om een verbinding te maken met MySQL. Vervolgens worden de methoden prepareStatement() en executeQuery() gebruikt om de SELECT-opdrachten uit te voeren. Vervolgens wordt next() gebruikt om naar de records in de resultaten te gaan. Ten slotte worden getInt() en getString() gebruikt om de waarden in de record te parseren.

Vervang de parameters Host, DBName, User en Password. U kunt de parameters vervangen door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    con->setSchema("quickstartdb");

    //select  
    pstmt = con->prepareStatement("SELECT * FROM inventory;");
    result = pstmt->executeQuery();

    while (result->next())
        printf("Reading from table=(%d, %s, %d)\n", result->getInt(1), result->getString(2).c_str(), result->getInt(3));

    delete result;
    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Gegevens bijwerken
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **UPDATE**. In de code wordt de klasse sql::Driver met de methode connect() gebruikt om een verbinding te maken met MySQL. Vervolgens worden de methoden prepareStatement() en executeQuery() gebruikt om de UPDATE-opdrachten uit te voeren. 

Vervang de parameters Host, DBName, User en Password. U kunt de parameters vervangen door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");

    //update
    pstmt = con->prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt->setInt(1, 200);
    pstmt->setString(2, "banana");
    pstmt->executeQuery();
    printf("Row updated\n");

    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Gegevens verwijderen
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **DELETE**. In de code wordt de klasse sql::Driver met de methode connect() gebruikt om een verbinding te maken met MySQL. Vervolgens worden de methoden prepareStatement() en executeQuery() gebruikt om de DELETE-opdrachten uit te voeren.

Vervang de parameters Host, DBName, User en Password. U kunt de parameters vervangen door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");
        
    //delete
    pstmt = con->prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt->setString(1, "orange");
    result = pstmt->executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
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
> [Uw MySQL-database migreren naar Azure Database voor MySQL met behulp van dumpen en terugzetten](concepts-migrate-dump-restore.md)
