---
title: Para meters configureren-Azure Database for PostgreSQL-flexibele server
description: In dit artikel wordt beschreven hoe u post gres-para meters configureert in Azure Database for PostgreSQL-flexibele server met behulp van de Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 315e4439ca66a3fbadac228c013797f516bc2940
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605761"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Server parameters voor Azure Database for PostgreSQL-flexibele server aanpassen met behulp van Azure CLI

U kunt configuratie parameters voor een Azure PostgreSQL-server weer geven, tonen en bijwerken met behulp van de opdracht regel interface (Azure CLI). Een subset van engine parameters wordt weer gegeven op server niveau en kan worden gewijzigd. 

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Maak een Azure Database for PostgreSQL-server en-data base door [de volgende Azure database for PostgreSQL te maken](quickstart-create-server-cli.md)
- Installeer de [Azure cli](/cli/azure/install-azure-cli) -opdracht regel interface op uw computer of gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md) in het Azure Portal met behulp van uw browser.

## <a name="list-server-parameters-for-a-flexible-server"></a>Server parameters weer geven voor een flexibele server

Als u alle para meters die kunnen worden gewijzigd op een server en hun waarden wilt weer geven, voert u de opdracht [AZ post gres Flexible-server para meter List](/cli/azure/postgres/flexible-server/parameter) uit.

U kunt de server parameters weer geven voor de server **mydemoserver.postgres.database.Azure.com** onder resource groep **myresourcegroup**.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Details van server parameters weer geven

Als u details over een bepaalde para meter voor een server wilt weer geven, voert u de opdracht [AZ post gres Flexible-server para meter show](/cli/azure/postgres/flexible-server/parameter)  uit.

In dit voor beeld worden details weer gegeven van de server parameter **logboek registratie \_ min \_ berichten** voor server **mydemoserver.postgres.database.Azure.com** onder resource groep **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Waarde voor Server parameter wijzigen

U kunt ook de waarde van een bepaalde server parameter wijzigen, waarmee de onderliggende configuratie waarde wordt bijgewerkt voor de PostgreSQL-server engine. Als u de para meter wilt bijwerken, gebruikt u de opdracht [AZ post gres Flexible-server para meter set](/cli/azure/postgres/flexible-server/parameter) . 

De server parameter **Log \_ min \_ Messages** van server **mydemoserver.postgres.database.Azure.com** onder resource groep **myresourcegroup bijwerken.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Als u de waarde van een para meter opnieuw wilt instellen, kunt u gewoon de optionele `--value` para meter laten verlaten en de service de standaard waarde Toep assen. In het bovenstaande voor beeld ziet het er als volgt uit:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

Met deze opdracht wordt de para meter voor het **logboek \_ min- \_ berichten** teruggezet naar de standaard waarde **waarschuwing**. Zie PostgreSQL-documentatie over het [instellen van para meters](https://www.postgresql.org/docs/12/config-setting.html)voor meer informatie over server parameters en toegestane waarden.

## <a name="next-steps"></a>Volgende stappen

- Zie [Server Logboeken in azure database for PostgreSQL](concepts-logging.md) voor informatie over het configureren en openen van server logboeken
