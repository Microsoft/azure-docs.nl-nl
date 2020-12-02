---
title: 'Quickstart: Verbinding maken met PHP - Azure Database for MySQL'
description: Deze snelstartgids bevat enkele voorbeelden van PHP-code die u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit een Azure Database voor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: cb3b711c532ccf44bebf08d42b5284db458cf5b7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492655"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: PHP gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL
In deze snelstartgids ziet u hoe u met behulp van een [PHP](https://secure.php.net/manual/intro-whatis.php)-toepassing verbinding maakt met een Azure Database voor MySQL. U ziet hier hoe u SQL-instructies gebruikt om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen.

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

- [Een database en een gebruiker die geen beheerder is maken](./howto-create-users.md?tabs=single-server)
- Nieuwste PHP-versie installeren voor uw besturingssysteem
    - [PHP op macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP op Linux](https://secure.php.net/manual/install.unix.php)
    - [PHP op Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> In deze quickstart gebruiken we de bibliotheek [MySQLi](https://www.php.net/manual/en/book.mysqli.php) om de verbinding te beheren en een query uit te voeren op de server.

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen
U kunt de verbindingsgegevens van de databaseserver ophalen van Azure Portal door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Ga naar de pagina Azure Databases for MySQL. Zoek en selecteer **Azure Database for MySQL.**
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL opzoeken":::

2. Selecteer de MySQL-server (bijvoorbeeld **mydemoserver**).
3. Kopieer op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** en de gebruikersnaam van de beheerder naast **Aanmeldingsnaam van serverbeheerder**. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

> [!IMPORTANT]
> - Als u uw wachtwoord bent vergeten, kunt u [het wachtwoord opnieuw instellen](./howto-create-manage-server-portal.md#update-admin-password).
> - Vervang de parameters **host, gebruikersnaam, wachtwoord** en **db_naam** door uw eigen waarden**

## <a name="step-1-connect-to-the-server"></a>Stap 1: Verbinding maken met de server
SSL is standaard ingeschakeld. Mogelijk moet u het [SSL-certificaat DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) downloaden om verbinding te maken vanuit uw lokale omgeving. Met deze code wordt het volgende aangeroepen:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) om MySQLi te initialiseren.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) om naar het pad van het SSL-certificaat te verwijzen. Dit is vereist voor uw lokale omgeving, maar is niet vereist voor App Service-web-app of Azure Virtual Machines.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) om verbinding te maken met MySQL.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) om de verbinding te sluiten.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

// If using  Azure Virtual machines or Azure Web App, 'mysqli-ssl_set()' is not required as the certificate is already installed on the machines.
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Ondervindt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Stap 2: Een tabel maken
Gebruik de volgende code om verbinding te maken. Met deze code wordt het volgende aangeroepen:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) om de query uit te voeren.
```php
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
```

## <a name="step-3-insert-data"></a>Stap 3: Gegevens invoegen
Gebruik de volgende code om gegevens in te voegen met behulp van de SQL-instructie **INSERT**. Voor deze code worden de volgende methoden gebruikt:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) om een voorbereide INSERT-instructie te maken
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) om de parameters voor elke ingevoegde kolomwaarde te koppelen.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) om de instructie te sluiten met behulp van methode


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Stap 4: Gegevens lezen
Gebruik de volgende code om de gegevens te lezen door de SQL-instructie **SELECT** te gebruiken.  Voor de code wordt de volgende methode gebruikt:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) om de **SELECT**-query uit te voeren
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) om de resulterende rijen op te halen.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Stap 5: Gegevens verwijderen
Gebruik de volgende code om rijen te verwijderen met de SQL-instructie **DELETE**. Voor de code worden de volgende methoden gebruikt:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) om een voorbereide DELETE-instructie te maken
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) om de parameters te binden
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) om de voorbereide DELETE-instructie uit te voeren
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) om de instructie te sluiten

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
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

[Kunt u niet vinden wat u zoekt? Laat het ons weten.](https://aka.ms/mysql-doc-feedback)