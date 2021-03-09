---
title: 'Snelstartgids: verbinding maken met behulp van Azure CLI-Azure Database for PostgreSQL-flexibele server'
description: Deze Quick Start biedt verschillende manieren om verbinding te maken met Azure CLI met Azure Database for PostgreSQL-flexibele server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: f10978107f80e7dea4e6d5ad40c078c55f225c2d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102494773"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Quick Start: verbinding maken en query's uitvoeren met Azure CLI met Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibele server is momenteel beschikbaar als open bare preview.

In deze Quick start ziet u hoe u verbinding kunt maken met een Azure Database for PostgreSQL flexibele server met behulp van Azure CLI met de ```az postgres flexible-server connect``` opdracht. Met deze opdracht kunt u de verbinding met uw database server testen en query's uitvoeren. U kunt ook meerdere query's uitvoeren met de interactieve modus. 

## <a name="prerequisites"></a>Vereisten
- Een Azure-account. Als u geen account hebt, kunt u [een gratis proefversie krijgen](https://azure.microsoft.com/free/).
- De nieuwste versie van [Azure cli](/cli/azure/install-azure-cli) installeren (2.20.0 of hoger)
- Aanmelden met behulp van Azure CLI met ```az login``` opdracht 
- Schakel parameter persistentie in met ```az config param-persist on``` . Met parameter persistentie kunt u lokale context gebruiken zonder veel argumenten te herhalen, zoals een resource groep of locatie.

## <a name="create-an-postgresql-flexible-server"></a>Een flexibele PostgreSQL-server maken

We maken eerst een beheerde PostgreSQL-server. Voer in [Azure Cloud shell](https://shell.azure.com/)het volgende script uit en noteer de **Server naam**, de **gebruikers naam** en het  **wacht woord** die zijn gegenereerd met deze opdracht.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
U kunt aanvullende argumenten opgeven voor deze opdracht om deze aan te passen. Zie alle argumenten voor [AZ post gres Flexible-server Create](/cli/azure/postgres/flexible-server?view=azure-cli-latest#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Alle argumenten weer geven
U kunt alle argumenten voor deze opdracht weer geven met een ```--help``` argument. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Database server verbinding testen
Met behulp van de opdracht kunt u de verbinding met de data base testen en valideren via de-omgeving.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Voorbeeld:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Als de verbinding is geslaagd, ziet u de uitvoer.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Als de verbinding is mislukt, probeert u de volgende oplossingen:
- Controleer of poort 5432 is geopend op de client computer.
- Als de gebruikers naam en het wacht woord van de server beheerder juist zijn
- Als u de firewall regel voor uw client computer hebt geconfigureerd
- Als u uw server hebt geconfigureerd met persoonlijke toegang in virtuele netwerken, zorgt u ervoor dat de client computer zich in hetzelfde virtuele netwerk bevindt.

## <a name="run-single-query"></a>Eén query uitvoeren
U kunt één query uitvoeren met de opdracht met behulp van het ```--querytext``` argument ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Voorbeeld:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

U ziet een uitvoer zoals hieronder wordt weer gegeven:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Voorbeeld:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

U ziet de **psql** shell-ervaring, zoals hieronder wordt weer gegeven:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De server beheren](./how-to-manage-server-cli.md)
