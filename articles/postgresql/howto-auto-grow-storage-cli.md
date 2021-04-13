---
title: Automatisch opslag laten groeien - Azure CLI - Azure Database for PostgreSQL - Individuele server
description: In dit artikel wordt beschreven hoe u opslag automatisch kunt laten groeien met behulp van de Azure CLI in Azure Database for PostgreSQL - enkele server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d16fe5ef6654ee29c3e345ff0532ed91206d86d3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366161"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Automatisch Azure Database for PostgreSQL opslag : één server met behulp van de Azure CLI
In dit artikel wordt beschreven hoe u een serveropslag Azure Database for PostgreSQL om te groeien zonder dat dit van invloed is op de werkbelasting.

De server [die de opslaglimiet bereikt,](./concepts-pricing-tiers.md#reaching-the-storage-limit)is ingesteld op alleen-lezen. Als automatisch verhogen van opslag is ingeschakeld, wordt de inrichtende opslaggrootte voor servers met minder dan 100 GB inrichten met 5 GB verhoogd zodra de gratis opslag kleiner is dan 1 GB of 10% van de inrichtende opslag. Voor servers met meer dan 100 GB inrichtende opslag wordt de inrichtende opslaggrootte met 5% verhoogd wanneer de vrije opslagruimte kleiner is dan 10 GB van de inrichtende opslaggrootte. De maximale opslaglimieten die hier worden [opgegeven, zijn](./concepts-pricing-tiers.md#storage) van toepassing.

## <a name="prerequisites"></a>Vereisten

- U hebt een [Azure Database for PostgreSQL-server nodig.](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="enable-postgresql-server-storage-auto-grow"></a>Automatische groei van PostgreSQL-serveropslag inschakelen

Schakel automatisch opslag op een bestaande server in met de volgende opdracht:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Schakel automatisch opslag voor servergroei in tijdens het maken van een nieuwe server met de volgende opdracht:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen voor metrische gegevens.](howto-alert-on-metric.md)
