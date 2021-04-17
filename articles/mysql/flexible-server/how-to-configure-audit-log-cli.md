---
title: Auditlogboeken configureren met Azure CLI - Azure Database for MySQL - Flexible Server
description: In dit artikel wordt beschreven hoe u de auditlogboeken in Azure Database for MySQL Flexible Server configureert en gebruikt vanuit de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 757d765f44f09eeb6f7a24644abeb1ce4577f664
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509062"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Auditlogboeken configureren en openen voor Azure Database for MySQL - Flexible Server met behulp van de Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In het artikel wordt beschreven hoe u [auditlogboeken voor](concepts-audit-logs.md) uw flexibele MySQL-server configureert met behulp van Azure CLI.

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

## <a name="configure-audit-logging"></a>Auditlogregistratie configureren

>[!IMPORTANT]
> Het is raadzaam om alleen de gebeurtenistypen en gebruikers te noteren die vereist zijn voor controledoeleinden om ervoor te zorgen dat de prestaties van uw server niet sterk worden beïnvloed.

Auditlogregistratie inschakelen en configureren.

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [auditlogboeken](concepts-audit-logs.md)
