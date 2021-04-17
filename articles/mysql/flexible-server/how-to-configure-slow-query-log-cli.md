---
title: Auditlogboeken en logboeken voor langzame query's configureren met Azure CLI - Azure Database for MySQL - Flexible Server
description: In dit artikel wordt beschreven hoe u logboeken voor langzame query's kunt configureren en openen in Azure Database for MySQL Flexible Server via de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509086"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Logboeken voor langzame query'Azure Database for MySQL - Flexible Server configureren met behulp van de Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In het artikel wordt beschreven hoe u logboeken voor [langzame query's](concepts-slow-query-logs.md) configureert voor uw Flexibele MySQL-server met behulp van Azure CLI. 

## <a name="prerequisites"></a>Vereisten
- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Installeer of upgrade Azure CLI naar de nieuwste versie. Zie [Azure CLI installeren](/cli/azure/install-azure-cli).
-  Meld u aan bij het Azure-account [met de opdracht az login.](/cli/azure/reference-index#az-login) Let op de eigenschap **id**, die verwijst naar **abonnements-id** voor uw Azure-account.

    ```azurecli-interactive
    az login
    ````

- Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin u de server wilt maken met behulp van de ```az account set``` opdracht .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Maak een MySQL Flexible Server als u er nog geen hebt gemaakt met de ```az mysql flexible-server create``` opdracht .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-slow-query-logs"></a>Logboeken voor langzame query's configureren

>[!IMPORTANT]
> Het is raadzaam om alleen de gebeurtenistypen en gebruikers te noteren die vereist zijn voor controledoeleinden om ervoor te zorgen dat de prestaties van uw server niet sterk worden beïnvloed.

Logboeken voor langzame query's voor uw server inschakelen en configureren.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboeken voor langzame query's](concepts-slow-query-logs.md)
