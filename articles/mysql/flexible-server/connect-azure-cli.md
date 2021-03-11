---
title: 'Snelstartgids: verbinding maken met behulp van Azure CLI-Azure Database for MySQL-flexibele server'
description: Deze Quick Start biedt verschillende manieren om verbinding te maken met Azure CLI met Azure Database for MySQL-flexibele server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: b28c4457129985a1d5c47d251873eaa52a253f72
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607965"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Quick Start: verbinding maken en query's uitvoeren met Azure CLI met Azure Database for MySQL-flexibele server

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In deze Quick start ziet u hoe u verbinding kunt maken met een Azure Database for MySQL flexibele server met behulp van Azure CLI met de ```az mysql flexible-server connect``` opdracht. Met deze opdracht kunt u de verbinding met uw database server testen en query's rechtstreeks uitvoeren op uw server.  U kunt ook de opdracht uitvoeren in een interactieve modus gebruiken om meerdere query's uit te voeren.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, kunt u [een gratis proefversie krijgen](https://azure.microsoft.com/free/).
- De nieuwste versie van [Azure cli](/cli/azure/install-azure-cli) installeren (2.20.0 of hoger)
- Aanmelden met behulp van Azure CLI met ```az login``` opdracht 
- Schakel parameter persistentie in met ```az config param-persist on``` . Met parameter persistentie kunt u lokale context gebruiken zonder dat u veel argumenten hoeft te herhalen, zoals een resource groep of locatie, enzovoort.

## <a name="create-an-mysql-flexible-server"></a>Een flexibele MySQL-server maken

We maken eerst een beheerde MySQL-server. Voer in [Azure Cloud shell](https://shell.azure.com/)het volgende script uit en noteer de **Server naam**, de **gebruikers naam** en het  **wacht woord** die zijn gegenereerd met deze opdracht.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

U kunt aanvullende argumenten opgeven voor deze opdracht om deze aan te passen. Bekijk alle argumenten voor [AZ mysql Flexible-server Create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Een database maken
Voer de volgende opdracht uit om een Data Base te maken, **newdatabase** als u deze nog niet hebt gemaakt.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Alle argumenten weer geven
U kunt alle argumenten voor deze opdracht weer geven met een ```--help``` argument. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Database server verbinding testen
Voer het volgende script uit om de verbinding met de data base te testen en te valideren vanuit uw ontwikkel omgeving.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Voorbeeld:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

De volgende uitvoer wordt weer geven voor een geslaagde verbinding:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Als de verbinding is mislukt, probeert u de volgende oplossingen:
- Controleer of poort 3306 is geopend op de client computer.
- Als de gebruikers naam en het wacht woord van de server beheerder juist zijn
- Als u de firewall regel voor uw client computer hebt geconfigureerd
- Als u uw server hebt geconfigureerd met persoonlijke toegang in virtuele netwerken, zorgt u ervoor dat de client computer zich in hetzelfde virtuele netwerk bevindt.

## <a name="run-single-query"></a>Eén query uitvoeren
Voer de volgende opdracht uit om één query uit te voeren met behulp van het ```--querytext``` argument ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Voorbeeld:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

U ziet een uitvoer zoals hieronder wordt weer gegeven:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Meerdere query's uitvoeren met de interactieve modus
U kunt meerdere query's uitvoeren met de **interactieve** modus. Voer de volgende opdracht uit om de interactieve modus in te scha kelen:

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Voorbeeld:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

U ziet de **MySQL** shell-ervaring, zoals hieronder wordt weer gegeven:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De server beheren](./how-to-manage-server-cli.md)

