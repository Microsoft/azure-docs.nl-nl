---
title: Lees replica's beheren in Azure Database for MySQL flexibele server met behulp van Azure CLI.
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for MySQL flexibele server met behulp van de Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bc95cd3ab471826538a551687c38d1422e4b7163
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108652"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Lees replica's maken en beheren in Azure Database for MySQL flexibele server met behulp van de Azure CLI

> [!IMPORTANT]
> Het lezen van replica's in Azure Database for MySQL-flexibele server is in preview.

In dit artikel leert u hoe u met de Azure CLI Lees replica's maakt en beheert op de flexibele Azure Database for MySQL server. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.

> [!Note]
> Replica wordt niet ondersteund op een server met hoge Beschik baarheid. 

## <a name="azure-cli"></a>Azure CLI
U kunt met behulp van de Azure CLI Lees replica's maken en beheren.

### <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](/cli/azure/install-azure-cli)
- Een [Azure database for MySQL flexibele server](quickstart-create-server-cli.md) die wordt gebruikt als de bron server.

### <a name="create-a-read-replica"></a>Een leesreplica maken

> [!IMPORTANT]
> Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

Een lees replica-server kan worden gemaakt met behulp van de volgende opdracht:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Lees replica's worden gemaakt met dezelfde server configuratie als de bron. De configuratie van de replica server kan worden gewijzigd nadat deze is gemaakt. De replica server wordt altijd gemaakt in dezelfde resource groep, dezelfde locatie en hetzelfde abonnement als de bron server. Als u een replica server wilt maken naar een andere resource groep of een ander abonnement, kunt u [de replica-server verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md) nadat u deze hebt gemaakt. Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de bron om ervoor te zorgen dat de replica de bron kan blijven gebruiken.


### <a name="list-replicas-for-a-source-server"></a>Replica's weer geven voor een bron server

Als u alle replica's voor een bepaalde bron server wilt weer geven, voert u de volgende opdracht uit: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen

> [!IMPORTANT]
> Het stoppen van de replicatie naar een server is onomkeerbaar. Zodra de replicatie tussen een bron en replica is gestopt, kan deze niet meer ongedaan worden gemaakt. De replica-server wordt vervolgens een zelfstandige server en ondersteunt nu lezen en schrijven. Deze server kan niet opnieuw in een replica worden gemaakt.

Replicatie naar een lees replica-server kan worden gestopt met de volgende opdracht:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Een replica server verwijderen

Het verwijderen van een lees replica-server kan worden uitgevoerd door de opdracht **[AZ mysql server delete](/cli/azure/mysql/server)** uit te voeren.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Een bron server verwijderen

> [!IMPORTANT]
> Als u een bronserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de bronserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een bron server wilt verwijderen, kunt u de opdracht **[AZ mysql Flexible-server delete](/cli/azure/mysql/flexible-server)** uitvoeren.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [lezen van replica's](concepts-read-replicas.md)