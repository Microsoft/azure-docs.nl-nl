---
title: Logboeken voor langzame query's openen - Azure CLI - Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u toegang krijgt tot de langzame logboeken in Azure Database for MariaDB met behulp van het Azure CLI-opdrachtregelprogramma.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42382076b6c14989eb153725e991c8ef047ad15b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774787"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Logboeken voor langzame query's van Azure Database for Maria DB configureren en openen met behulp van Azure CLI

U kunt de logboeken Azure Database for MariaDB query's downloaden met behulp van Azure CLI, het Azure-opdrachtregelprogramma.

## <a name="prerequisites"></a>Vereisten
Als u deze handleiding wilt door nemen, hebt u het volgende nodig:
- [Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- De [Azure CLI](/cli/azure/install-azure-cli) of Azure Cloud Shell in de browser

## <a name="configure-logging"></a>Logboekregistratie configureren
U kunt de server configureren voor toegang tot het logboek voor langzame MySQL-query's door de volgende stappen uit te voeren:
1. Schakel logboekregistratie voor langzame query's in door de parameter logboek voor langzame **\_ \_ query's** in te stellen op AAN.
2. Selecteer waar de logboeken moeten worden uitgevoerd met behulp van **\_ logboekuitvoer.** Als u logboeken wilt verzenden naar zowel lokale opslag als diagnostische Azure Monitor, selecteert u **Bestand**. Als u alleen logboeken wilt verzenden Azure Monitor logboeken, selecteert u **Geen**
3. Pas andere parameters aan, zoals lange **\_ querytijd \_ en** trage **\_ \_ beheerdersverklaringen voor \_ logboeken.**

Zie Serverparameters configureren voor meer informatie over het instellen van de waarde van deze parameters via Azure [CLI.](howto-configure-server-parameters-cli.md)

Met de volgende CLI-opdracht schakelt u bijvoorbeeld het logboek voor langzame query's in, stelt u de lange querytijd in op 10 seconden en schakelt u vervolgens de logboekregistratie van de instructie voor langzame beheerders uit. Ten slotte worden de configuratieopties voor uw beoordeling vermeld.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Logboeken voor Azure Database for MariaDB server
Als **log_output** is geconfigureerd voor 'Bestand', hebt u rechtstreeks vanuit de lokale opslag van de server toegang tot logboeken. Voer de opdracht [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list) uit om de beschikbare logboekbestanden voor langzame query's voor uw server weer te geven.

U kunt de logboekbestanden voor serverbestanden **mydemoserver.mariadb.database.azure.com** onder de resourcegroep **myresourcegroup**. Vervolgens stuurt u de lijst met logboekbestanden door naar een tekstbestand met de naam **\_ \_ logboekbestandenlist.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Logboeken downloaden van de server
Als **log_output** is geconfigureerd voor 'File', kunt u afzonderlijke logboekbestanden van uw server downloaden met de [opdracht az mariadb server-logs download.](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download)

Gebruik het volgende voorbeeld om het specifieke logboekbestand voor de server te **mydemoserver.mariadb.database.azure.com** onder de resourcegroep **myresourcegroup naar** uw lokale omgeving.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboeken voor langzame query's in Azure Database for MariaDB](concepts-server-logs.md).
