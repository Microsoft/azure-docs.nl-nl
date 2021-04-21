---
title: Logboeken voor langzame query's openen - Azure CLI - Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u toegang krijgt tot de logboeken voor langzame query's in Azure Database for MySQL met behulp van de Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d5fc2b14a655251e59a9209e078b0534f08baf9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763231"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Logboeken voor langzame query's configureren en openen met behulp van Azure CLI
U kunt de logboeken Azure Database for MySQL langzame query's downloaden met behulp van Azure CLI, het Azure-opdrachtregelprogramma.

## <a name="prerequisites"></a>Vereisten
Als u deze handleiding wilt door nemen, hebt u het volgende nodig:
- [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-cli.md)
- De [Azure CLI](/cli/azure/install-azure-cli) of Azure Cloud Shell in de browser

## <a name="configure-logging"></a>Logboekregistratie configureren
U kunt de server configureren voor toegang tot het logboek voor langzame MySQL-query's door de volgende stappen uit te voeren:
1. Schakel logboekregistratie voor langzame query's in door de parameter **voor logboeken voor langzame \_ \_ query's** in te stellen op AAN.
2. Selecteer waar de logboeken moeten worden uitgevoerd met behulp van **\_ logboekuitvoer.** Als u logboeken wilt verzenden naar zowel lokale opslag als diagnostische Azure Monitor, selecteert u **Bestand**. Als u logboeken alleen wilt verzenden Azure Monitor logboeken, selecteert u **Geen**
3. Pas andere parameters aan, zoals lange **\_ querytijd \_ en** trage **beheerders instructies voor \_ \_ \_ logboeken.**

Zie Serverparameters configureren voor meer informatie over het instellen van de waarde van deze [parameters](howto-configure-server-parameters-using-cli.md)via Azure CLI.

Met de volgende CLI-opdracht schakelt u bijvoorbeeld het logboek voor langzame query's in, stelt u de lange querytijd in op 10 seconden en schakelt u vervolgens de logboekregistratie van de instructie voor langzame beheerders uit. Ten slotte worden de configuratieopties voor uw beoordeling vermeld.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Logboeken voor een Azure Database for MySQL server
Als **log_output** is geconfigureerd op 'Bestand', hebt u rechtstreeks toegang tot logboeken vanuit de lokale opslag van de server. Voer de opdracht [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) uit om de beschikbare logboekbestanden voor langzame query's voor uw server weer te geven.

U kunt de logboekbestanden voor serverbestanden **mydemoserver.mysql.database.azure.com** onder de resourcegroep **myresourcegroup**. Vervolgens stuurt u de lijst met logboekbestanden door naar een tekstbestand met de naam **\_ \_ logboekbestandenlist.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Logboeken downloaden van de server
Als **log_output** is geconfigureerd voor 'File', kunt u afzonderlijke logboekbestanden van uw server downloaden met [de opdracht az mysql server-logs download.](/cli/azure/mysql/server-logs#az_mysql_server_logs_download)

Gebruik het volgende voorbeeld om het specifieke logboekbestand voor de server te **downloaden** mydemoserver.mysql.database.azure.com onder de resourcegroep **myresourcegroup naar** uw lokale omgeving.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboeken voor langzame query's in Azure Database for MySQL](concepts-server-logs.md).
