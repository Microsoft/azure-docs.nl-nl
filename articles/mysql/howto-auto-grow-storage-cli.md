---
title: Automatisch opslag laten groeien - Azure CLI - Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u automatisch groeien van opslag kunt inschakelen met behulp van de Azure CLI in Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ae22a80829ecaaff84c308ec9059d398b1ccbfb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365056"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Automatisch de opslag Azure Database for MySQL met behulp van de Azure CLI
In dit artikel wordt beschreven hoe u een serveropslag Azure Database for MySQL om te groeien zonder dat dit van invloed is op de werkbelasting.

De server [die de opslaglimiet bereikt,](./concepts-pricing-tiers.md#reaching-the-storage-limit)is ingesteld op alleen-lezen. Als automatisch groeien van opslag is ingeschakeld voor servers met minder dan 100 GB inrichtende opslag, wordt de inrichtende opslag met 5 GB verhoogd zodra de vrije opslag onder de grotere van 1 GB of 10% van de inrichtende opslag is. Voor servers met meer dan 100 GB inrichtende opslag wordt de inrichtende opslag met 5% verhoogd wanneer de vrije opslagruimte kleiner is dan 10 GB van de inrichtende opslaggrootte. De maximale opslaglimieten die hier worden [opgegeven, zijn](./concepts-pricing-tiers.md#storage) van toepassing.

## <a name="prerequisites"></a>Vereisten

U kunt deze handleiding als volgende voltooien:

- U hebt een [Azure Database for MySQL nodig.](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="enable-mysql-server-storage-auto-grow"></a>Automatisch groeien van MySQL-serveropslag inschakelen

Schakel opslag op een bestaande server automatisch uit met de volgende opdracht:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Schakel automatisch opslag voor servergroei in tijdens het maken van een nieuwe server met de volgende opdracht:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen voor metrische gegevens.](howto-alert-on-metric.md)
