---
title: Server beheren - Azure CLI - Azure Database for MySQL
description: Meer informatie over het beheren van Azure Database for MySQL server vanuit de Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 03227f121f58e52d2e9d34613917fda864666ce1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769963"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Een Azure Database for MySQL Single-server beheren met behulp van de Azure CLI

In dit artikel wordt beschreven hoe u uw individuele servers beheert die in Azure zijn geïmplementeerd. Beheertaken omvatten het schalen van berekeningen en opslag, het opnieuw instellen van het beheerderswachtwoord en het weergeven van servergegevens.

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

Als u nog geen sever hebt gemaakt, raadpleegt u deze [quickstart om](quickstart-create-mysql-server-database-using-azure-cli.md) er een te maken.

## <a name="scale-compute-and-storage"></a>Rekenkracht en opslag schalen
U kunt uw prijscategorie, rekencapaciteit en opslag eenvoudig omhoog schalen met behulp van de volgende opdracht. U ziet alle serverbewerkingen die u kunt uitvoeren [az mysql server overview](/cli/azure/mysql/server)

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Hier volgen de detailgegevens voor bovenstaande argumenten:

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam voor uw Azure Database for MySQL-server in. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name|GP_Gen5_2|Voer de naam van de prijscategorie en de berekeningsconfiguratie in. Volgt de verkorte notatie voor conventie {prijscategorie} _{compute-generatie}_ {vCores}. Raadpleeg de [prijscategorieën](./concepts-pricing-tiers.md) voor meer informatie.
storage-size | 6144 | De opslagcapaciteit van de server (eenheid is MB). Minimaal 5120 en toenames in stappen van 1024.

> [!Important]
> - Opslag kan omhoog worden geschaald (u kunt opslag echter niet omlaag schalen)
> - Omhoog schalen van basic naar algemeen gebruik of prijscategorie geoptimaliseerd voor geheugen wordt niet ondersteund. U kunt handmatig omhoog schalen met behulp van een  [bash-script](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) of [met behulp van MySQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL-databases op een server beheren
U kunt een van deze opdrachten gebruiken om database-eigenschappen van een database op uw server te maken, te verwijderen, weer te geven en weer te geven

| Cmdlet | Gebruik| Description |
| --- | ---| --- |
|[az mysql db create](/cli/azure/sql/db#az_mysql_db_create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Hiermee maakt u een database|
|[az mysql db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Verwijder uw database van uw server. Met deze opdracht wordt uw server niet verwijderd. |
|[az mysql db list](/cli/azure/sql/db#az_mysql_db_list)|```az mysql db list -g myresourcegroup -s mydemoserver```|geeft een lijst weer van alle databases op de server|
|[az mysql db show](/cli/azure/sql/db#az_mysql_db_show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Geeft meer details van de database weer|

## <a name="update-admin-password"></a>Beheerderswachtwoord bijwerken
U kunt het wachtwoord van de beheerdersrol wijzigen met deze opdracht
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Zorg ervoor dat het wachtwoord uit minimaal 8 en maximaal 128 tekens bestaat.
> Het wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.

## <a name="delete-a-server"></a>Een server verwijderen
Als u alleen de MySQL Single-server wilt verwijderen, kunt u de [opdracht az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) uitvoeren.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- [Een server opnieuw opstarten](howto-restart-server-cli.md)
- [Een server in een slechte staat herstellen](howto-restore-server-cli.md)
- [De service controleren en afstemmen](concepts-monitoring.md)