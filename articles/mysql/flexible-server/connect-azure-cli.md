---
title: 'Quickstart: Verbinding maken met behulp van Azure CLI - Azure Database for MySQL - Flexible Server'
description: Deze quickstart biedt verschillende manieren om verbinding te maken met Azure CLI met Azure Database for MySQL - Flexible Server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: e0fd5969a3c4f84b6e8f98e99335bf120179e7af
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481086"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Quickstart: Verbinding maken en query's uitvoeren met Azure CLI met Azure Database for MySQL - Flexible Server

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In deze quickstart wordt gedemonstreerd hoe u verbinding maakt met een Azure Database for MySQL Flexible Server met behulp van Azure CLI met ```az mysql flexible-server connect``` de opdracht . Met deze opdracht kunt u de connectiviteit met uw databaseserver testen en query's rechtstreeks op uw server uitvoeren.  U kunt ook de opdracht uitvoeren in een interactieve modus gebruiken voor het uitvoeren van meerdere query's.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, kunt u [een gratis proefversie krijgen](https://azure.microsoft.com/free/).
- Installeer de nieuwste versie van [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 of hoger)
- Meld u aan met behulp van Azure CLI met ```az login``` de opdracht 
- Schakel parameterpersistence in met ```az config param-persist on``` . Parameterpersistence helpt u bij het gebruik van lokale context zonder veel argumenten te herhalen, zoals resourcegroep of locatie, enzovoort.

## <a name="create-an-mysql-flexible-server"></a>Een MySQL Flexible Server maken

We maken eerst een beheerde MySQL-server. Voer [Azure Cloud Shell](https://shell.azure.com/)volgende script uit en noteer de **servernaam,** gebruikersnaam en het **wachtwoord** die zijn gegenereerd met deze opdracht. 

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

U kunt aanvullende argumenten voor deze opdracht geven om deze aan te passen. Zie alle argumenten voor [az mysql flexible-server create.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)

## <a name="create-a-database"></a>Een database maken
Voer de volgende opdracht uit om een database, **newdatabase,** te maken als u er nog geen hebt gemaakt.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Alles weergeven argumenten op te geven
U kunt alle argumenten voor deze opdracht weergeven met ```--help``` argument. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Databaseserververbinding testen
Voer het volgende script uit om de verbinding met de database vanuit uw ontwikkelomgeving te testen en te valideren.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Voorbeeld:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Als het goed is, ziet u de volgende uitvoer voor een geslaagde verbinding:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Probeer de volgende oplossingen als de verbinding is mislukt:
- Controleer of poort 3306 is geopend op uw clientmachine.
- als de gebruikersnaam en het wachtwoord van de serverbeheerder juist zijn
- als u een firewallregel hebt geconfigureerd voor uw clientmachine
- Als u uw server hebt geconfigureerd met privétoegang in virtuele netwerken, moet u ervoor zorgen dat uw clientmachine zich in hetzelfde virtuele netwerk.

## <a name="run-single-query"></a>Eén query uitvoeren
Voer de volgende opdracht uit om één query uit te voeren met ```--querytext``` behulp van argument , ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Voorbeeld:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

U ziet een uitvoer zoals hieronder wordt weergegeven:

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

## <a name="run-multiple-queries-using-interactive-mode"></a>Meerdere query's uitvoeren met behulp van de interactieve modus
U kunt meerdere query's uitvoeren met behulp van **de interactieve** modus. Voer de volgende opdracht uit om de interactieve modus in te schakelen

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Voorbeeld:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

U ziet de **MySQL-shell-ervaring** zoals hieronder wordt weergegeven:

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
* [Verbinding maken met Azure Database for MySQL - Flexible Server met versleutelde verbindingen](how-to-connect-tls-ssl.md)
* [De server beheren](./how-to-manage-server-cli.md)

