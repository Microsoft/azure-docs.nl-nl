---
title: Verbindings reeksen-Azure Database for MariaDB
description: In dit document worden de momenteel ondersteunde verbindings reeksen vermeld voor toepassingen om verbinding te maken met Azure Database for MariaDB, waaronder ADO.NET (C#), JDBC, Node.js, ODBC, PHP, python en Ruby.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662174"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Toepassingen verbinden met Azure Database for MariaDB
In dit onderwerp vindt u een overzicht van de connection string typen die door Azure Database for MariaDB worden ondersteund, samen met sjablonen en voor beelden. Er zijn mogelijk verschillende para meters en instellingen in uw connection string.

- Zie [SSL configureren voor meer informatie over](./howto-configure-ssl.md)het verkrijgen van het certificaat.
- {your_host} = [servername]. mariadb. data base. Azure. com
- {your_user} @ {servername} = indeling van gebruikers-id voor de juiste verificatie.  Als u alleen de gebruikers-id gebruikt, mislukt de verificatie.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

In dit voor beeld is de naam van de server `mydemoserver` , de naam van de data base, de `wpdb` gebruikers naam `WPAdmin` en het wacht woord `mypassword!2` . Als gevolg hiervan moet de connection string:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>De connection string Details ophalen uit de Azure Portal
Ga in het [Azure Portal](https://portal.azure.com)naar de Azure database for MariaDB-server en klik vervolgens op **verbindings reeksen** om de teken reeks lijst voor uw instantie op te halen: ![ het deel venster verbindings reeksen in de Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

De teken reeks bevat details zoals het stuur programma, de server en andere para meters voor database verbindingen. Wijzig deze voor beelden om uw eigen para meters te gebruiken, zoals de database naam, het wacht woord, enzovoort. U kunt deze teken reeks vervolgens gebruiken om vanuit uw code en toepassingen verbinding te maken met de server.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
