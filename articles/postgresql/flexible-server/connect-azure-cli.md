---
title: 'Quickstart: Verbinding maken met behulp van Azure CLI - Azure Database for PostgreSQL - Flexible Server'
description: Deze quickstart biedt verschillende manieren om verbinding te maken met Azure CLI met Azure Database for PostgreSQL - Flexible Server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 3017d10abc910233e0627037349c0dfd966fd88e
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883792"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Quickstart: Verbinding maken en query's uitvoeren met Azure CLI met Azure Database for PostgreSQL - Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is momenteel beschikbaar als openbare preview.

In deze quickstart wordt gedemonstreerd hoe u verbinding maakt met een Azure Database for PostgreSQL Flexible Server met behulp van Azure CLI met ```az postgres flexible-server connect``` de opdracht . Met deze opdracht kunt u de connectiviteit met uw databaseserver testen en query's uitvoeren. U kunt ook meerdere query's uitvoeren met behulp van de interactieve modus. 

## <a name="prerequisites"></a>Vereisten
- Een Azure-account. Als u geen account hebt, kunt u [een gratis proefversie krijgen](https://azure.microsoft.com/free/).
- Installeer de nieuwste versie van [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 of hoger)
- Meld u aan met behulp van Azure CLI met ```az login``` de opdracht 
- Schakel parameter persistentie in met ```az config param-persist on``` . Parameterpersistence helpt u bij het gebruik van lokale context zonder dat u veel argumenten zoals resourcegroep of locatie moet herhalen.

## <a name="create-an-postgresql-flexible-server"></a>Een Flexibele PostgreSQL-server maken

We maken eerst een beheerde PostgreSQL-server. Voer [Azure Cloud Shell](https://shell.azure.com/)volgende script uit en noteer de **servernaam,** gebruikersnaam en het **wachtwoord** die zijn gegenereerd met deze opdracht. 

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
U kunt aanvullende argumenten voor deze opdracht geven om deze aan te passen. Zie alle argumenten voor [az postgres flexible-server create.](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)

## <a name="view-all-the-arguments"></a>Alles weergeven argumenten op te geven
U kunt alle argumenten voor deze opdracht weergeven met ```--help``` argument. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Databaseserververbinding testen
U kunt de verbinding met de database testen en valideren vanuit uw ontwikkelomgeving met behulp van de opdracht .

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Voorbeeld:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
U ziet de uitvoer als de verbinding is geslaagd.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Probeer de volgende oplossingen als de verbinding is mislukt:
- Controleer of poort 5432 is geopend op uw clientmachine.
- als de gebruikersnaam en het wachtwoord van de serverbeheerder juist zijn
- als u een firewallregel hebt geconfigureerd voor uw clientmachine
- Als u uw server hebt geconfigureerd met privétoegang in virtuele netwerken, moet u ervoor zorgen dat uw clientmachine zich in hetzelfde virtuele netwerk.

## <a name="run-single-query"></a>Eén query uitvoeren
U kunt één query uitvoeren met de opdracht met behulp van ```--querytext``` argument , ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Voorbeeld:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

U ziet een uitvoer zoals hieronder wordt weergegeven:

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

## <a name="run-multiple-queries-using-interactive-mode"></a>Meerdere query's uitvoeren met behulp van de interactieve modus
U kunt meerdere query's uitvoeren met behulp van **de interactieve** modus . Voer de volgende opdracht uit om de interactieve modus in te schakelen

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Voorbeeld:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

U ziet de **psql** shell-ervaring zoals hieronder wordt weergegeven:

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
