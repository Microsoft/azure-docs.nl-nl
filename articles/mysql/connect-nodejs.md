---
title: 'Quickstart: Verbinding maken met Node.js - Azure Database for MySQL'
description: Deze snelstartgids bevat enkele voorbeelden van Node.js-code die u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit Azure Database voor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/11/2020
ms.openlocfilehash: 6a9134e13e3145daea1eed81c4aa8795a0a49950
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588230"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Node.js gebruiken om verbinding te maken en query's uit te voeren op gegevens in Azure Database for MySQL

In deze quickstart maakt u verbinding met een Azure Database for MySQL met behulp van Node.js. U gebruikt vervolgens SQL-instructies om gegevens op te vragen, in te voegen, bij te werken en te verwijderen in de database vanaf Mac-, Ubuntu Linux- en Windows-platforms. 

In dit onderwerp wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met behulp van Node.js, maar geen ervaring hebt met het werken met Azure Database for MySQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Database for MySQL-server. [Een Azure Database for MySQL-server maken met behulp van de Azure-portal](quickstart-create-mysql-server-database-using-azure-portal.md) of [Een Azure Database for MySQL-server maken met behulp van Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Controleer of het IP-adres waarmee u verbinding maakt aan de firewallregels van de server is toegevoegd met de [Azure-portal](./howto-manage-firewall-using-portal.md) of [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-nodejs-and-the-mysql-connector"></a>Node.js en de MySQL-connector installeren

Afhankelijk van uw platform volgt u de instructies in het bijbehorende gedeelte voor het installeren van [Node.js](https://nodejs.org). Gebruik NPM om het [mysql](https://www.npmjs.com/package/mysql)-pakket en de bijbehorende afhankelijkheden in de projectmap te installeren.

### <a name="windows"></a>Windows

1. Ga naar de [downloadpagina van Node.js](https://nodejs.org/en/download/) en selecteer het gewenste Windows-installatieprogramma.
2. Maak een lokale projectmap, zoals `nodejsmysql`. 
3. Open de opdrachtprompt en wijzig de map in de projectmap, zoals `cd c:\nodejsmysql\`
4. Voer het hulpprogramma NPM uit om de mysql-bibliotheek in de projectmap te installeren.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Controleer de installatie door de `npm list`-uitvoertekst te controleren. Het versienummer kan variëren als nieuwe patches worden vrijgegeven.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Voer de volgende opdrachten uit om **Node.js** en **NPM**, het pakketbeheerprogramma voor Node.js, te installeren.

   ```bash
    # Using Ubuntu
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install -y nodejs

    # Using Debian, as root
    curl -sL https://deb.nodesource.com/setup_14.x | bash -
    apt-get install -y nodejs
   ```

2. Voer de volgende opdrachten uit om een projectmap `mysqlnodejs` te maken en installeer het mysql-pakket in die map.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Controleer de installatie door de uitvoertekst van de npm-lijst te controleren. Het versienummer kan variëren als nieuwe patches worden vrijgegeven.

### <a name="macos"></a>macOS

1. Ga naar de [downloadpagina van Node.js](https://nodejs.org/en/download/) en selecteer vervolgens het MacOS-installatieprogramma.

2. Voer de volgende opdrachten uit om een projectmap `mysqlnodejs` te maken en installeer het mysql-pakket in die map.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Controleer de installatie door de `npm list`-uitvoertekst te controleren. Het versienummer kan variëren als nieuwe patches worden vrijgegeven.

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database voor MySQL. U hebt de volledig gekwalificeerde servernaam en aanmeldingsreferenties nodig.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer in het menu aan de linkerkant in de Azure-portal **Alle resources** en zoek naar de server die u hebt gemaakt (bijvoorbeeld **mydemoserver**).
3. Selecteer de servernaam.
4. Ga naar het venster **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord in dit venster opnieuw instellen.
 :::image type="content" source="./media/connect-nodejs/server-name-azure-database-mysql.png" alt-text="Naam van Azure Database voor MySQL-server":::

## <a name="running-the-javascript-code-in-nodejs"></a>De JavaScript-code in Node.js uitvoeren

1. Plak de JavaScript-code in tekstbestanden en sla deze op in een projectmap met de bestandsextensie .js (zoals C:\nodejsmysql\createtable.js of /home/username/nodejsmysql/createtable.js).
2. Open de opdrachtprompt of Bash-shell en wijzig vervolgens de map in uw projectmap `cd nodejsmysql`.
3. Voer de knooppuntopdracht, gevolgd door de bestandsnaam `node createtable.js` in om de toepassing uit te voeren.
4. In Windows moet u als de knooppunttoepassing zich niet in uw omgevingsvariabelepad bevindt mogelijk het volledige pad gebruiken om de knooppunttoepassing te starten, zoals `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Verbinden, tabel maken en gegevens invoegen

Gebruik de volgende code om verbinding te maken en de gegevens te laden met behulp van de SQL-instructies **CREATE TABLE** en **INSERT INTO**.

De methode [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om een koppeling te maken met de MySQL-server. De functie [connect()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om verbinding met de server te maken. De functie [query()](https://github.com/mysqljs/mysql#performing-queries) wordt gebruikt om de SQL-query uit te voeren op de MySQL-database. 

Vervang de parameters `host`, `user`, `password` en `database` door de waarden die u hebt opgegeven tijdens het maken van de server en database.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }
});

function queryDatabase(){
    conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
        if (err) throw err; 
        console.log('Dropped inventory table if existed.');
    })
        conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Created inventory table.');
    })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
        else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
    function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.end(function (err) { 
    if (err) throw err;
    else  console.log('Done.') 
    });
};
```

## <a name="read-data"></a>Gegevens lezen

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **SELECT**. 

De methode [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om een koppeling te maken met de MySQL-server. De methode [connect()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om verbinding met de server te maken. De methode [query()](https://github.com/mysqljs/mysql#performing-queries) wordt gebruikt om de SQL-query uit te voeren op de MySQL-database. De resultatenmatrix wordt gebruikt om de resultaten van de query op te slaan.

Vervang de parameters `host`, `user`, `password` en `database` door de waarden die u hebt opgegeven tijdens het maken van de server en database.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }
    });

function readData(){
    conn.query('SELECT * FROM inventory', 
        function (err, results, fields) {
            if (err) throw err;
            else console.log('Selected ' + results.length + ' row(s).');
            for (i = 0; i < results.length; i++) {
                console.log('Row: ' + JSON.stringify(results[i]));
            }
            console.log('Done.');
        })
    conn.end(
        function (err) { 
            if (err) throw err;
            else  console.log('Closing connection.') 
    });
};
```

## <a name="update-data"></a>Gegevens bijwerken

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **UPDATE**. 

De methode [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om een koppeling te maken met de MySQL-server. De methode [connect()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om verbinding met de server te maken. De methode [query()](https://github.com/mysqljs/mysql#performing-queries) wordt gebruikt om de SQL-query uit te voeren op de MySQL-database. 

Vervang de parameters `host`, `user`, `password` en `database` door de waarden die u hebt opgegeven tijdens het maken van de server en database.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Gegevens verwijderen

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **DELETE**. 

De methode [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om een koppeling te maken met de MySQL-server. De methode [connect()](https://github.com/mysqljs/mysql#establishing-connections) wordt gebruikt om verbinding met de server te maken. De methode [query()](https://github.com/mysqljs/mysql#performing-queries) wordt gebruikt om de SQL-query uit te voeren op de MySQL-database. 

Vervang de parameters `host`, `user`, `password` en `database` door de waarden die u hebt opgegeven tijdens het maken van de server en database.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
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
> [Een database migreren met behulp van Exporteren en importeren](./concepts-migrate-import-export.md)
