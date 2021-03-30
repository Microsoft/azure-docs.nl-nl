---
title: Infra structuur dubbele versleuteling-Azure Portal-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van een infra structuur met dubbele versleuteling voor uw Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 3f9c22a690859b459b6bb748c3b1001c4aa7660d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241749"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Infra structuur dubbele versleuteling voor Azure Database for MySQL

Meer informatie over het gebruik van de manier waarop u een infra structuur met dubbele versleuteling instelt en beheert voor uw Azure Database for MySQL.

## <a name="prerequisites"></a>Vereisten

* U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Een Azure Database for MySQL-server met dubbele versleuteling met infra structuur maken-Portal

Volg deze stappen voor het maken van een Azure Database for MySQL server met dubbele infra structuur versleuteling van Azure Portal:

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** (+).

2. Selecteer **Databases** > **Azure Database voor MySQL**. U kunt ook **MySQL** in het zoekvak typen om de service te vinden.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="De optie Azure Database for MySQL":::

3. Geef de basis gegevens van de server op. Selecteer **extra instellingen** en schakel het selectie vakje **infra structuur dubbele versleuteling** in om de para meter in te stellen.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for MySQL selecties":::

4. Selecteer **Beoordelen en maken** om de server in te richten.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for MySQL overzicht":::

5. Nadat de server is gemaakt, kunt u de infra structuur voor dubbele versleuteling valideren door de status op de Blade **gegevens versleutelings** server te controleren.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL validatie":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Een Azure Database for MySQL-server met dubbele versleuteling van de infra structuur maken-CLI

Volg deze stappen voor het maken van een Azure Database for MySQL-server met dubbele infra structuur versleuteling van CLI:

In dit voor beeld wordt een resource groep `myresourcegroup` met de naam op de `westus` locatie gemaakt.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
In het volgende voorbeeld wordt een MySQL 5.7-server in VS - west gemaakt met de naam `mydemoserver` in uw resourcegroep `myresourcegroup` met aanmeldgegevens van de serverbeheerder `myadmin`. Dit is een **Gen 4-server voor** **Algemeen gebruik** met **twee vCores**. Er is ook een infra structuur met dubbele versleuteling ingeschakeld voor de server die is gemaakt. Vervang het `<server_admin_password>` door uw eigen waarde.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Volgende stappen

 Zie [Azure database for MySQL gegevens infrastructuur dubbele versleuteling](concepts-Infrastructure-double-encryption.md)voor meer informatie over gegevens versleuteling.
