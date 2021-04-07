---
title: Server beheren-Azure CLI-Azure Database for PostgreSQL
description: Meer informatie over het beheren van een Azure Database for PostgreSQL-server vanuit de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 2ea07e2bc12e6fc0d62abd462b8537c6a93689f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935798"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Een Azure Database for PostgreSQL één server beheren met de Azure CLI

Dit artikel laat u zien hoe u uw enkele servers kunt beheren die zijn geïmplementeerd in Azure. Beheer taken zijn onder andere berekening en opslag schalen, beheerders wachtwoord opnieuw instellen en server details weer geven.

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

Als u nog geen server hebt gemaakt, raadpleegt u deze [Snelstartgids](quickstart-create-server-database-azure-cli.md) om er een te maken.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

U kunt de prijs categorie, de reken capaciteit en de opslag eenvoudig omhoog schalen met de volgende opdracht. U kunt alle server bewerkingen zien die u kunt uitvoeren [AZ post gres server Overview](/cli/azure/mysql/server) (Engelstalig)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Hier volgen de detailgegevens voor bovenstaande argumenten:

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam in voor de Azure Database for PostgreSQL-server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name|GP_Gen5_2|Voer de naam van de prijscategorie en de berekeningsconfiguratie in. Volgt de verkorte notatie voor conventie {prijscategorie} _{compute-generatie}_ {vCores}. Raadpleeg de [prijscategorieën](./concepts-pricing-tiers.md) voor meer informatie.
storage-size | 6144 | De opslagcapaciteit van de server (eenheid is MB). Mini maal 5120 en verhogingen in 1024 stappen.

> [!Important]
> - Opslag kan omhoog worden geschaald (u kunt de opslag echter niet omlaag schalen)
> - Het omhoog schalen van de basis naar het algemene doel of de prijs categorie geoptimaliseerd voor geheugen wordt niet ondersteund. U kunt hand matig schalen met  [behulp van een bash-script](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) of [met behulp van postgresql Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-data bases op een server beheren.
U kunt elk van deze opdrachten gebruiken om data base-eigenschappen van een Data Base op uw server te maken, te verwijderen, weer te geven en te bekijken

| Cmdlet | Gebruik| Description |
| --- | ---| --- |
|[AZ post gres DB Create](/cli/azure/sql/db#az-mysql-db-create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Hiermee maakt u een Data Base|
|[AZ post gres DB Delete](/cli/azure/sql/db#az-mysql-db-delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Verwijder uw data base van uw server. Met deze opdracht wordt de server niet verwijderd. |
|[AZ post gres DB List](/cli/azure/sql/db#az-mysql-db-list)|```az postgres db list -g myresourcegroup -s mydemoserver```|een lijst met alle data bases op de server|
|[AZ post gres DB show](/cli/azure/sql/db#az-mysql-db-show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Meer details van de Data Base weer geven|

## <a name="update-admin-password"></a>Beheerders wachtwoord bijwerken
U kunt het wacht woord van de beheerdersrol wijzigen met deze opdracht
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Zorg ervoor dat het wacht woord mini maal acht tekens en Maxi maal 128 tekens bevat.
> Het wacht woord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofd letters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.

## <a name="delete-a-server"></a>Een server verwijderen
Als u de PostgreSQL één server wilt verwijderen, kunt u de opdracht [AZ post gres server delete](/cli/azure/mysql/server#az-mysql-server-delete) uitvoeren.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- [Een server opnieuw opstarten](howto-restart-server-cli.md)
- [Een server met een onjuiste status herstellen](howto-restore-server-cli.md)
- [De service controleren en afstemmen](concepts-monitoring.md)
