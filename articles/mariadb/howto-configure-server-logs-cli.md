---
title: Toegang tot langzame query logboeken-Azure CLI-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u de trage Logboeken in Azure Database for MariaDB kunt openen met behulp van het opdracht regel hulpprogramma van Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cea1571a48afe00350dde247c3c10e222a446247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662386"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Een langzame query logboeken van Azure data base for Maria DB configureren en gebruiken met behulp van Azure CLI

U kunt de Azure Database for MariaDB langzame query logboeken downloaden met behulp van Azure CLI, het opdracht regel programma van Azure.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- De [Azure cli](/cli/azure/install-azure-cli) of Azure Cloud shell in de browser

## <a name="configure-logging"></a>Logboek registratie configureren
U kunt de server configureren voor toegang tot het langzame Slow-query logboek door de volgende stappen uit te voeren:
1. Schakel logboek registratie voor langzame query's in door de langzame para meter voor **\_ query \_ Logboeken** in te stellen op on.
2. Selecteer waar de logboeken moeten worden uitgevoerd om de **logboek \_ uitvoer** te gebruiken. Selecteer **bestand** om logboeken naar zowel lokale opslag als Azure monitor Diagnostische logboeken te verzenden. Als u Logboeken alleen naar Azure Monitor logboeken wilt verzenden, selecteert u **geen**
3. Pas andere para meters aan, zoals **lange \_ query \_ tijd** en **logboek \_ langzame \_ beheer \_ instructies**.

Zie [server parameters configureren](howto-configure-server-parameters-cli.md)voor meer informatie over het instellen van de waarde van deze para meters via Azure cli.

Met de volgende CLI-opdracht wordt bijvoorbeeld het langzame query logboek ingeschakeld, wordt de lange query tijd ingesteld op 10 seconden, waarna de logboek registratie van de langzame beheer instructie wordt uitgeschakeld. Ten slotte worden de configuratie opties voor uw beoordeling weer gegeven.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Logboeken voor Azure Database for MariaDB server weer geven
Als **log_output** is geconfigureerd voor "bestand", hebt u rechtstreeks toegang tot logboeken vanuit de lokale opslag van de server. Als u de beschik bare langzame query logboek bestanden voor uw server wilt weer geven, voert u de opdracht [AZ mariadb server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) uit.

U kunt de logboek bestanden voor server **mydemoserver.mariadb.database.Azure.com** weer geven onder de resource groep **myresourcegroup**. Ga vervolgens naar de lijst met logboek bestanden naar een tekst bestand met de naam **logboek \_ bestanden \_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Logboeken van de server downloaden
Als **log_output** is geconfigureerd voor ' bestand ', kunt u afzonderlijke logboek bestanden downloaden van de server met de opdracht [AZ mariadb server-logs down load](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) .

Gebruik het volgende voor beeld om het specifieke logboek bestand voor de server **mydemoserver.mariadb.database.Azure.com** onder de resource groep **myresourcegroup** te downloaden naar uw lokale omgeving.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [trage query Logboeken in azure database for MariaDB](concepts-server-logs.md).
