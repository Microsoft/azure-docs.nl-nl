---
title: 'Quickstart: Verbinding maken met PHP - Azure Database for MySQL - Flexible Server'
description: Deze snelstartgids bevat enkele voorbeelden van PHP-code die u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit een Azure Database for MySQL - Flexible Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: baba4d373d4a79ab0c339aac00bb9ab48de9262b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109604"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Quickstart: PHP gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL - Flexible Server

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In deze snelstartgids ziet u hoe u met behulp van een [PHP](https://secure.php.net/manual/intro-whatis.php)-toepassing verbinding maakt met een Azure Database for MySQL Flexible Server. U ziet hier hoe u SQL-instructies gebruikt om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen. In dit artikel wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met behulp van PHP, maar geen ervaring hebt met het werken met Azure Database for MySQL - Flexible Server.

## <a name="prerequisites"></a>Vereisten
In deze snelstartgids worden de resources die in een van deze handleidingen zijn gemaakt, als uitgangspunt gebruikt:

- [Een Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./quickstart-create-server-portal.md)
- [Een Azure Database for MySQL Flexible Server maken met behulp van Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Uw clientwerkstation voorbereiden
1. Als u een flexibele server hebt gemaakt met *Private Access (VNet-integratie)* , moet u verbinding maken met uw server vanuit een resource binnen hetzelfde VNet als uw server. U kunt een virtuele machine maken en toevoegen aan het VNet dat is gemaakt met de flexibele server. Zie [Virtueel netwerk met Azure Database for MySQL Flexible Server maken met behulp van Azure CLI](./how-to-manage-virtual-network-cli.md).

2. Als u uw flexibele server met *Openbare toegang (toegestane IP-adressen)* hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server. Zie [Firewallregels voor Azure Database for MySQL Flexible Server maken met behulp van de Azure CLI](./how-to-manage-firewall-cli.md).

### <a name="install-php"></a>PHP installeren

Installeer PHP op uw eigen server of maak een Azure-[web-app](../../app-service/overview.md) die PHP omvat.  Raadpleeg [firewallregels maken en beheren](./how-to-manage-firewall-portal.md) voor meer informatie over het maken van firewallregels.

#### <a name="macos"></a>macOS

1. Download [PHP 7.1.4](https://secure.php.net/downloads.php).
2. Installeer PHP en raadpleeg de [PHP-handleiding](https://secure.php.net/manual/install.macosx.php) voor verdere configuratie.

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Download [PHP 7.1.4 niet-thread-veilig (x64)](https://secure.php.net/downloads.php).
2. Installeer PHP en raadpleeg de [PHP-handleiding](https://secure.php.net/manual/install.unix.php) voor verdere configuratie.

#### <a name="windows"></a>Windows

1. Download [PHP 7.1.4 niet-thread-veilig (x64)](https://windows.php.net/download#php-7.1).
2. Installeer PHP en raadpleeg de [PHP-handleiding](https://secure.php.net/manual/install.windows.php) voor verdere configuratie.

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database for MySQL Flexible Server. U hebt de volledig gekwalificeerde servernaam en aanmeldingsreferenties nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer in het menu aan de linkerkant in de Azure-portal **Alle resources** en zoek naar de server die u hebt gemaakt (bijvoorbeeld **mydemoserver**).
3. Selecteer de servernaam.
4. Ga naar het venster **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord in dit venster opnieuw instellen.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>Verbinding maken met flexibele server met behulp van TLS/SSL in PHP

Zie de volgende codevoorbeelden als u vanuit uw toepassing een versleutelde verbinding via TLS/SSL tot stand wilt brengen. U kunt het voor communicatie via TLS/SSL benodigde certificaat downloaden van [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Verbinding maken en een tabel maken

Gebruik de volgende code om verbinding te maken en een tabel te maken met behulp van de SQL-instructie **CREATE TABLE**.

Voor de code wordt gebruikgemaakt van de klasse **MySQL Improved extension** (mysqli) die deel uitmaakt van PHP. Met de code worden de methoden [mysqli_init](https://secure.php.net/manual/mysqli.init.php) en [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) aangeroepen om verbinding te maken met MySQL. Daarna wordt de methode [mysqli_query](https://secure.php.net/manual/mysqli.query.php) aangeroepen op de query uit te voeren. Vervolgens wordt methode [mysqli_close](https://secure.php.net/manual/mysqli.close.php) aangeroepen om de verbinding te sluiten.

Vervang de parameters Host, Gebruikersnaam, Wachtwoord en db_name door uw eigen waarden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Gegevens invoegen

Gebruik de volgende code om verbinding te maken en de gegevens in te voegen met behulp van de SQL-instructie **INSERT**.

Voor de code wordt gebruikgemaakt van de klasse **MySQL Improved extension** (mysqli) die deel uitmaakt van PHP. Met de code wordt gebruikgemaakt van de methode [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) om een invoeginstructie te maken. Daarna worden de parameters van elke ingevoegde kolomwaarde verbonden met de methode [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Met de code wordt de instructie uitgevoerd via de methode [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php). Daarna wordt de instructie gesloten via de methode [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Vervang de parameters Host, Gebruikersnaam, Wachtwoord en db_name door uw eigen waarden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Gegevens lezen

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **SELECT**.  Voor de code wordt gebruikgemaakt van de klasse **MySQL Improved extension** (mysqli) die deel uitmaakt van PHP. De code gebruikt de methode [mysqli_query](https://secure.php.net/manual/mysqli.query.php) om de SQL-query uit te voeren en de methode [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) om de resulterende rijen op te halen.

Vervang de parameters Host, Gebruikersnaam, Wachtwoord en db_name door uw eigen waarden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Gegevens bijwerken

Gebruik de volgende code om verbinding te maken en de gegevens bij te werken met behulp van de SQL-instructie **UPDATE**.

Voor de code wordt gebruikgemaakt van de klasse **MySQL Improved extension** (mysqli) die deel uitmaakt van PHP. Met de code wordt gebruikgemaakt van de methode [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) om een bijwerkinstructie te maken. Daarna worden de parameters van elke bijgewerkte kolomwaarde verbonden met de methode [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Met de code wordt de instructie uitgevoerd via de methode [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php). Daarna wordt de instructie gesloten via de methode [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Vervang de parameters Host, Gebruikersnaam, Wachtwoord en db_name door uw eigen waarden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Gegevens verwijderen
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **DELETE**.

Voor de code wordt gebruikgemaakt van de klasse **MySQL Improved extension** (mysqli) die deel uitmaakt van PHP. Met de code wordt gebruikgemaakt van de methode [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) om een verwijderinstructie te maken. Daarna worden de parameters verbonden voor het Where-component in de instructie. Hiervoor wordt de methode [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) gebruikt. Met de code wordt de instructie uitgevoerd via de methode [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php). Daarna wordt de instructie gesloten via de methode [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Vervang de parameters Host, Gebruikersnaam, Wachtwoord en db_name door uw eigen waarden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Volgende stappen
- [Versleutelde verbinding met behulp van Transport Layer Security (TLS 1.2) in Azure Database for MySQL - Flexible Server](./how-to-connect-tls-ssl.md).
- Meer informatie over [Netwerken in Azure Database for MySQL - Flexible Server](./concepts-networking.md).
- [Firewallregels voor Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-firewall-portal.md).
- [Virtueel netwerk met Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-virtual-network-portal.md).