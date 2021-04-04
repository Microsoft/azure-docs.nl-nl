---
title: Verbinding maken met omleiding-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u een toepassing kunt configureren om verbinding te maken met Azure Database for MariaDB met behulp van omleiding.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 3f26de72839fcaa39bff4d827aba757721736934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664899"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>Verbinding maken met Azure Database for MariaDB met behulp van omleiding

In dit onderwerp wordt uitgelegd hoe u met de omleidings modus verbinding maakt met een toepassing op uw Azure Database for MariaDB-server. Omleiding is gericht op het verminderen van de netwerk latentie tussen client toepassingen en MariaDB-servers door toepassingen toe te staan rechtstreeks verbinding te maken met back-end-server knooppunten.

## <a name="before-you-begin"></a>Voordat u begint
Meld u aan bij [Azure Portal](https://portal.azure.com). Maak een Azure Database for MariaDB-server met Engine versie 10,2 of 10,3. 

Zie een Azure Database for MariaDB-server maken met behulp van de [Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md) of [Azure cli](quickstart-create-mariadb-server-database-using-azure-cli.md)voor meer informatie.

## <a name="enable-redirection"></a>Omleiding inschakelen

Configureer op uw Azure Database for MariaDB-server de `redirect_enabled` para meter om `ON` verbindingen met de omleidings modus toe te staan. Gebruik de [Azure Portal](howto-server-parameters.md) of [Azure cli](howto-configure-server-parameters-cli.md)om deze server parameter bij te werken.

## <a name="php"></a>PHP

Ondersteuning voor omleiding in PHP-toepassingen is beschikbaar via de extensie [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , ontwikkeld door micro soft. 

De uitbrei ding mysqlnd_azure is beschikbaar om toe te voegen aan PHP-toepassingen via PECL en het wordt ten zeerste aanbevolen om de uitbrei ding te installeren en te configureren via het officieel gepubliceerde [PECL-pakket](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Ondersteuning voor omleiding in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -uitbrei ding is momenteel als preview-versie beschikbaar.

### <a name="redirection-logic"></a>Logica voor omleiding

>[!IMPORTANT]
> De logica/het gedrag van het omleidings scenario 1.1.0 is bijgewerkt en **wordt aanbevolen om versie 1.1.0 + te gebruiken**.

Het omleidings gedrag wordt bepaald door de waarde van `mysqlnd_azure.enableRedirect` . De onderstaande tabel geeft een overzicht van het gedrag van omleiding op basis van de waarde van deze para meter vanaf **versie 1.1.0 +**.

Als u een oudere versie van de mysqlnd_azure extensie (versie 1.0.0-1.0.3) gebruikt, wordt het omleidings gedrag bepaald door de waarde van `mysqlnd_azure.enabled` . De geldige waarden zijn `off` (reageren op dezelfde manier als het gedrag dat wordt beschreven in de onderstaande tabel) en `on` ( `preferred` in de onderstaande tabel).  

|**waarde van mysqlnd_azure. enableRedirect**| Gedrag|
|----------------------------------------|-------------|
|`off` of `0`|Omleiding wordt niet gebruikt. |
|`on` of `1`|-Als de verbinding geen SSL op het stuur programma gebruikt, wordt er geen verbinding gemaakt. De volgende fout wordt geretourneerd: *"mysqlnd_azure. enableRedirect is ingeschakeld, maar de SSL-optie is niet ingesteld in Connection String. Omleiding is alleen mogelijk met SSL. "*<br>-Als SSL wordt gebruikt op het stuur programma, maar de omleiding wordt niet ondersteund op de server, wordt de eerste verbinding afgebroken en wordt de volgende fout geretourneerd: *' de verbinding is afgebroken omdat omleiding niet is ingeschakeld op de MariaDB-server of het netwerk pakket niet voldoet aan het omleidings Protocol '. '*<br>-Als de MariaDB-server omleiding ondersteunt, maar de omgeleide verbinding om een of andere reden mislukt, wordt ook de eerste proxy verbinding afgebroken. Retourneert de fout van de omgeleide verbinding.|
|`preferred` of `2`<br> (standaard waarde)|-mysqlnd_azure wordt indien mogelijk omleiding gebruikt.<br>-Als de verbinding geen gebruikmaakt van SSL op het stuur programma, biedt de server geen ondersteuning voor omleidingen of de omgeleide verbinding kan geen verbinding maken voor een niet-fatale reden, terwijl de proxy verbinding nog steeds een geldig is, wordt de verbinding met de eerste proxy verbroken.|

In de volgende secties van het document wordt uitgelegd hoe u de `mysqlnd_azure` uitbrei ding installeert met behulp van PECL en de waarde van deze para meter instelt.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Vereisten 
- PHP-versies 7.2.15 + en 7.3.2 +
- PHP PEER 
- php-mysql
- Azure Database for MariaDB server

1. Installeer [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) met [PECL](https://pecl.php.net/package/mysqlnd_azure). Het is raadzaam versie 1.1.0 + te gebruiken.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Ga naar de extensie Directory ( `extension_dir` ) door de onderstaande stappen uit te voeren:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Wijzig mappen in de map die wordt geretourneerd en zorg ervoor dat `mysqlnd_azure.so` deze zich in deze map bevindt. 

4. Ga naar de map voor. ini-bestanden door de onderstaande stappen uit te voeren: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Wijzig de mappen in deze geretourneerde map. 

6. Maak een nieuw. ini-bestand voor `mysqlnd_azure` . Zorg ervoor dat de alfabetische volg orde van de naam na mysqnld is, omdat de modules zijn geladen op basis van de naam volgorde van de ini-bestanden. Bijvoorbeeld, als `mysqlnd` . ini een naam heeft `10-mysqlnd.ini` , de mysqlnd ini als `20-mysqlnd-azure.ini` .

7. Voeg in het nieuwe ini-bestand de volgende regels toe om omleiding in te scha kelen.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Vereisten 
- PHP-versies 7.2.15 + en 7.3.2 +
- php-mysql
- Azure Database for MariaDB server

1. Bepaal of u een x64-of x86-versie van PHP gebruikt door de volgende opdracht uit te voeren:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Down load de bijbehorende x64-of x86-versie van de [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll van [PECL](https://pecl.php.net/package/mysqlnd_azure) die overeenkomt met uw versie van PHP. Het is raadzaam versie 1.1.0 + te gebruiken.

3. Pak het zip-bestand uit en zoek de naam van de DLL `php_mysqlnd_azure.dll` .

4. Zoek de extensie Directory ( `extension_dir` ) door de onderstaande opdracht uit te voeren:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Kopieer het `php_mysqlnd_azure.dll` bestand naar de map die is geretourneerd in stap 4. 

6. Zoek de PHP-map met het `php.ini` bestand met behulp van de volgende opdracht:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Wijzig het `php.ini` bestand en voeg de volgende extra regels toe om omleiding in te scha kelen. 

    Onder de sectie dynamische extensies: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Klik onder de sectie module-instellingen op:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Omleiding bevestigen

U kunt ook bevestigen dat de omleiding is geconfigureerd met behulp van de onderstaande PHP-voorbeeld code. Maak een PHP-bestand `mysqlConnect.php` met de naam en plak de onderstaande code. Werk de server naam, gebruikers naam en het wacht woord bij met uw eigen account. 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Volgende stappen
Zie [verbindings reeksen](howto-connection-string.md)voor meer informatie over verbindings reeksen.
