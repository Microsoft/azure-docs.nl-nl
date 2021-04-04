---
title: Server beheren-Azure CLI-Azure Database for MySQL flexibele server
description: Meer informatie over het beheren van een Azure Database for MySQL flexibele server vanuit de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545052"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Een Azure Database for MySQL-flexibele server (preview) beheren met de Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Dit artikel laat u zien hoe u uw flexibele server (preview) kunt beheren die is geïmplementeerd in Azure. Beheer taken zijn onder andere berekening en opslag schalen, beheerders wachtwoord opnieuw instellen en server details weer geven.

## <a name="prerequisites"></a>Vereisten
Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. In dit artikel moet u Azure CLI-versie 2.0 of later lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/reference-index#az-login). Let op de eigenschap **id**, die verwijst naar **abonnements-id** voor uw Azure-account.

```azurecli-interactive
az login
```

Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account). Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Als u nog geen flexibele server hebt gemaakt, kunt u er een maken om aan de slag te gaan met deze hand leiding.

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

U kunt uw Compute-laag, vCores en opslag eenvoudig omhoog schalen met de volgende opdracht. U kunt alle server bewerkingen zien die u kunt uitvoeren [AZ mysql Flexible-Server Update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Hier volgen de detailgegevens voor bovenstaande argumenten:

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam voor uw Azure Database for MySQL-server in. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name|Standard_D4ds_v4|Voer de naam van de compute-laag en de grootte in. Volgt de Conventie Standard_ {VM size} in steno. Raadpleeg de [prijscategorieën](../concepts-pricing-tiers.md) voor meer informatie.
storage-size | 6144 | De opslagcapaciteit van de server (eenheid is MB). Mini maal 5120 en verhogingen in 1024 stappen.

> [!Important]
> - Opslag kan omhoog worden geschaald (u kunt de opslag echter niet omlaag schalen)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL-data bases op een server beheren.
U kunt elk van deze opdrachten gebruiken om data base-eigenschappen van een Data Base op uw server te maken, te verwijderen, weer te geven.

| Cmdlet | Gebruik| Description |
| --- | ---| --- |
|[AZ mysql Flexible-server DB Create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Hiermee maakt u een Data Base|
|[AZ mysql flexibel-server DB-verwijderen](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Verwijder uw data base van uw server. Met deze opdracht wordt de server niet verwijderd. |
|[AZ mysql Flexible-server DB List](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|een lijst met alle data bases op de server|
|[AZ mysql Flexible-server DB show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Meer details van de Data Base weer geven|

## <a name="update-admin-password"></a>Beheerders wachtwoord bijwerken
U kunt het wacht woord van de beheerdersrol wijzigen met deze opdracht
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Zorg ervoor dat het wacht woord mini maal acht tekens en Maxi maal 128 tekens bevat.
> Het wacht woord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofd letters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.

## <a name="delete-a-server"></a>Een server verwijderen
Als u de flexibele MySQL-server wilt verwijderen, kunt u [AZ mysql flexibele-server opdracht verwijderen](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) uitvoeren.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het starten of stoppen van een server](how-to-stop-start-server-portal.md)
- [Meer informatie over het beheren van een virtueel netwerk](how-to-manage-virtual-network-cli.md)
- [Verbindingsproblemen oplossen](how-to-troubleshoot-common-connection-issues.md)
- [Firewall maken en beheren](how-to-manage-firewall-cli.md)