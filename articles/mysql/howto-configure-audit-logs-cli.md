---
title: Access-controle logboeken-Azure CLI-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u de audit Logboeken in Azure Database for MySQL kunt configureren en openen vanuit de Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541994"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Controle logboeken configureren en openen in de Azure CLI

U kunt de [Azure database for MySQL audit logboeken](concepts-audit-logs.md) configureren vanuit de Azure cli.

## <a name="prerequisites"></a>Vereisten

U kunt deze hand leiding door lopen:

- U hebt een [Azure database for mysql-server](quickstart-create-mysql-server-database-using-azure-portal.md)nodig.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

>[!IMPORTANT]
> Het is raadzaam om alleen de gebeurtenis typen en gebruikers die vereist zijn voor controle doeleinden te registreren om ervoor te zorgen dat de prestaties van uw server niet sterk worden beïnvloed.

Schakel logboek registratie voor de controle in en configureer deze met de volgende stappen:

1. Schakel audit Logboeken in door de para meter **audit_logs_enabled** in te stellen op on. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecteer de [gebeurtenis typen](concepts-audit-logs.md#configure-audit-logging) die moeten worden geregistreerd door de **audit_log_events** -para meter bij te werken.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Voeg MySQL-gebruikers toe die moeten worden uitgesloten van logboek registratie door de para meter **audit_log_exclude_users** bij te werken. Geef gebruikers op door hun MySQL-gebruikers naam op te geven.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Voeg specifieke MySQL-gebruikers toe die moeten worden opgenomen voor logboek registratie door de para meter **audit_log_include_users** bij te werken. Geef gebruikers op door hun MySQL-gebruikers naam op te geven.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [audit logboeken](concepts-audit-logs.md) in azure database for MySQL
- Meer informatie over het configureren van audit Logboeken in de [Azure Portal](howto-configure-audit-logs-portal.md)
