---
title: Server beheren - Azure CLI - Azure Database for MySQL Flexible Server
description: Meer informatie over het beheren van Azure Database for MySQL Flexibele server via de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4ef1408d5f7afc3b78ab021cdd25eedd75110849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776928"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Een Azure Database for MySQL - Flexible Server (preview) beheren met de Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel wordt beschreven hoe u uw flexibele server (preview) beheert die in Azure is geïmplementeerd. Beheertaken omvatten het schalen van berekeningen en opslag, het opnieuw instellen van het beheerderswachtwoord en het weergeven van servergegevens.

## <a name="prerequisites"></a>Vereisten
Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. In dit artikel moet u Azure CLI-versie 2.0 of later lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/reference-index#az_login). Let op de eigenschap **id**, die verwijst naar **abonnements-id** voor uw Azure-account.

```azurecli-interactive
az login
```

Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account). Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Als u nog geen flexibele server hebt gemaakt, maakt u er een om aan de slag te gaan met deze handleiding.

## <a name="scale-compute-and-storage"></a>Rekenkracht en opslag schalen

U kunt uw rekenlaag, vCores en opslag eenvoudig omhoog schalen met behulp van de volgende opdracht. U ziet alle serverbewerkingen die u kunt uitvoeren [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Hier volgen de detailgegevens voor bovenstaande argumenten:

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam voor uw Azure Database for MySQL-server in. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name|Standard_D4ds_v4|Voer de naam van de rekenlaag en grootte in. Volgt de verkorte Standard_{VM-grootte}. Raadpleeg de [prijscategorieën](../concepts-pricing-tiers.md) voor meer informatie.
storage-size | 6144 | De opslagcapaciteit van de server (eenheid is MB). Minimaal 5120 en toenames in stappen van 1024.

> [!Important]
> - Opslag kan omhoog worden geschaald (u kunt opslag echter niet omlaag schalen)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL-databases op een server beheren.
U kunt een van deze opdrachten gebruiken om database-eigenschappen van een database op uw server te maken, te verwijderen, weer te geven en weer te geven

| Cmdlet | Gebruik| Description |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Hiermee maakt u een database|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Verwijder uw database van uw server. Met deze opdracht wordt uw server niet verwijderd. |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|geeft een lijst weer van alle databases op de server|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Geeft meer details van de database weer|

## <a name="update-admin-password"></a>Beheerderswachtwoord bijwerken
U kunt het wachtwoord van de beheerdersrol wijzigen met deze opdracht
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Zorg ervoor dat het wachtwoord uit minimaal 8 en maximaal 128 tekens bestaat.
> Het wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.

## <a name="delete-a-server"></a>Een server verwijderen
Als u alleen de MySQL Flexible Server wilt verwijderen, kunt u de opdracht [az mysql flexible-server server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) uitvoeren.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het starten of stoppen van een server](how-to-stop-start-server-portal.md)
- [Meer informatie over het beheren van een virtueel netwerk](how-to-manage-virtual-network-cli.md)
- [Verbindingsproblemen oplossen](how-to-troubleshoot-common-connection-issues.md)
- [Firewall maken en beheren](how-to-manage-firewall-cli.md)