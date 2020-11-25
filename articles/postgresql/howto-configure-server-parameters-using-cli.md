---
title: Para meters configureren-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u post gres-para meters configureert in Azure Database for PostgreSQL-één server met behulp van de Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4231f348f99073406fcb6a5bef9bf0f84cacf2eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005568"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Server configuratie parameters aanpassen voor Azure Database for PostgreSQL-één server met behulp van Azure CLI
U kunt configuratie parameters voor een Azure PostgreSQL-server weer geven, tonen en bijwerken met behulp van de opdracht regel interface (Azure CLI). Een subset van de engine configuraties wordt weer gegeven op server niveau en kan worden gewijzigd. 

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Maak een Azure Database for PostgreSQL-server en-data base door [de volgende Azure database for PostgreSQL te maken](quickstart-create-server-database-azure-cli.md)
- Installeer de [Azure cli](/cli/azure/install-azure-cli) -opdracht regel interface op uw computer of gebruik de [Azure Cloud Shell](../cloud-shell/overview.md) in het Azure Portal met behulp van uw browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Server configuratie parameters voor Azure Database for PostgreSQL server weer geven
Voer de opdracht [AZ post gres Server Configuration List](/cli/azure/postgres/server/configuration) uit om alle para meters die kunnen worden gewijzigd op een server en hun waarden weer te geven.

U kunt de server configuratie parameters weer geven voor de server **mydemoserver.postgres.database.Azure.com** onder resource groep **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Details van server configuratie parameters weer geven
Voer de opdracht [AZ post gres Server Configuration show](/cli/azure/postgres/server/configuration)  uit om details over een bepaalde configuratie parameter voor een server weer te geven.

In dit voor beeld worden details weer gegeven van de server configuratie parameter **\_ min \_ Berichten logboek** voor server **mydemoserver.postgres.database.Azure.com** onder resource groep **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Parameter waarde voor de server configuratie wijzigen
U kunt ook de waarde van een bepaalde server configuratie parameter wijzigen, waarmee de onderliggende configuratie waarde wordt bijgewerkt voor de PostgreSQL-server engine. Als u de configuratie wilt bijwerken, gebruikt u de opdracht [AZ post gres server configuration set](/cli/azure/postgres/server/configuration) . 

De server configuratie parameter van het **logboek \_ min- \_ berichten** van server **mydemoserver.postgres.database.Azure.com** onder resource groep **myresourcegroup bijwerken.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Als u de waarde van een configuratie parameter opnieuw wilt instellen, kunt u gewoon de optionele `--value` para meter laten verlaten en de service de standaard waarde Toep assen. In het bovenstaande voor beeld ziet het er als volgt uit:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Met deze opdracht wordt de configuratie **min-logboek \_ \_ berichten** opnieuw ingesteld op de standaard waarde **waarschuwing**. Zie PostgreSQL-documentatie over [Server configuratie](https://www.postgresql.org/docs/9.6/static/runtime-config.html)voor meer informatie over de configuratie van de server en de toegestane waarden.

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het opnieuw opstarten van een server](howto-restart-server-cli.md)
- Zie [Server Logboeken in azure database for PostgreSQL](concepts-server-logs.md) voor informatie over het configureren en openen van server logboeken
