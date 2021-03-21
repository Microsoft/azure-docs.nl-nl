---
title: Infra structuur dubbele versleuteling-Azure Portal-Azure Database for PostgreSQL
description: Meer informatie over het instellen en beheren van een infra structuur met dubbele versleuteling voor uw Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: ea486b534ac3e703849ddb3922d7c3a428dd076b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242225"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Infra structuur dubbele versleuteling voor Azure Database for PostgreSQL

Meer informatie over het gebruik van de manier waarop u een infra structuur met dubbele versleuteling instelt en beheert voor uw Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten

* U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Een Azure Database for PostgreSQL-server met dubbele versleuteling met infra structuur maken-Portal

Volg deze stappen voor het maken van een Azure Database for MySQL server met dubbele infra structuur versleuteling van Azure Portal:

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** (+).

2. Selecteer **Databases** > **Azure Database for PostgreSQL**. U kunt ook PostgreSQL in het zoekvak typen om de service te vinden. De implementatie optie voor **één server** is ingeschakeld.

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Azure Database for PostgreSQL in het menu":::

3. Geef de basis gegevens van de server op. Selecteer **extra instellingen** en schakel het selectie vakje **infra structuur dubbele versleuteling** in om de para meter in te stellen.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for PostgreSQL selecties":::

4. Selecteer **Beoordelen en maken** om de server in te richten.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for PostgreSQL overzicht":::

5. Nadat de server is gemaakt, kunt u de infra structuur voor dubbele versleuteling valideren door de status op de Blade **gegevens versleutelings** server te controleren.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL validatie":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Een Azure Database for PostgreSQL-server met dubbele versleuteling van de infra structuur maken-CLI

Volg deze stappen voor het maken van een Azure Database for MySQL-server met dubbele infra structuur versleuteling van CLI:

In dit voor beeld wordt een resource groep `myresourcegroup` met de naam op de `westus` locatie gemaakt.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
In het volgende voor beeld wordt een PostgreSQL 11-server in West US gemaakt met de naam `mydemoserver` in uw resource groep met de aanmelding van de `myresourcegroup` Server beheerder `myadmin` . Dit is een **Gen 4-server voor** **Algemeen gebruik** met **twee vCores**. Er is ook een infra structuur met dubbele versleuteling ingeschakeld voor de server die is gemaakt. Vervang het `<server_admin_password>` door uw eigen waarde.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure database for PostgreSQL gegevens infrastructuur dubbele versleuteling](concepts-Infrastructure-double-encryption.md)voor meer informatie over gegevens versleuteling.

