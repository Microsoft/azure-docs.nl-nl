---
title: Lees replica's beheren-Azure CLI, REST API-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for MySQL met behulp van de Azure CLI of REST API.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a5bc9638e2e6eeff8f2176247f579b64beede90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540209"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Lees replica's maken en beheren in Azure Database for MySQL met behulp van de Azure CLI en REST API

In dit artikel wordt beschreven hoe u in de Azure Database for MySQL-service Lees replica's maakt en beheert met behulp van de Azure CLI en REST API. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.

## <a name="azure-cli"></a>Azure CLI
U kunt met behulp van de Azure CLI Lees replica's maken en beheren.

### <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](/cli/azure/install-azure-cli)
- Een [Azure database for mysql-server](quickstart-create-mysql-server-database-using-azure-portal.md) die wordt gebruikt als de bron server. 

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de bron server zich in een van deze prijs categorieën bevindt.

### <a name="create-a-read-replica"></a>Een leesreplica maken

> [!IMPORTANT]
> Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

Een lees replica-server kan worden gemaakt met behulp van de volgende opdracht:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Voor de `az mysql server replica create` opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resource groep waar de replica-server wordt gemaakt.  |
| naam | mydemoreplicaserver | De naam van de nieuwe replica server die wordt gemaakt. |
| source-server | mydemoserver | De naam of ID van de bestaande bron server waaruit moet worden gerepliceerd. |

Gebruik de para meter om een lees replica te maken `--location` . In het CLI-voor beeld hieronder wordt de replica in VS West gemaakt.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

> [!NOTE]
> Lees replica's worden gemaakt met dezelfde server configuratie als de Master. De configuratie van de replica server kan worden gewijzigd nadat deze is gemaakt. Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de bron om ervoor te zorgen dat de replica kan blijven werken met de Master.


### <a name="list-replicas-for-a-source-server"></a>Replica's weer geven voor een bron server

Als u alle replica's voor een bepaalde bron server wilt weer geven, voert u de volgende opdracht uit: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Voor de `az mysql server replica list` opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resource groep waar de replica-server wordt gemaakt.  |
| servernaam | mydemoserver | De naam of ID van de bron server. |

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen

> [!IMPORTANT]
> Het stoppen van de replicatie naar een server is onomkeerbaar. Zodra de replicatie tussen een bron en replica is gestopt, kan deze niet meer ongedaan worden gemaakt. De replica-server wordt vervolgens een zelfstandige server en ondersteunt nu lezen en schrijven. Deze server kan niet opnieuw in een replica worden gemaakt.

Replicatie naar een lees replica-server kan worden gestopt met de volgende opdracht:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Voor de `az mysql server replica stop` opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resource groep waar de replica-server bestaat.  |
| naam | mydemoreplicaserver | De naam van de replica server waarvoor replicatie moet worden gestopt. |

### <a name="delete-a-replica-server"></a>Een replica server verwijderen

Het verwijderen van een lees replica-server kan worden uitgevoerd door de opdracht **[AZ mysql server delete](/cli/azure/mysql/server)** uit te voeren.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Een bron server verwijderen

> [!IMPORTANT]
> Als u een bronserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de bronserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een bron server wilt verwijderen, kunt u de opdracht **[AZ mysql server delete](/cli/azure/mysql/server)** uitvoeren.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST-API
U kunt met behulp van de [Azure rest API](/rest/api/azure/)Lees replica's maken en beheren.

### <a name="create-a-read-replica"></a>Een leesreplica maken
U kunt een lees replica maken met behulp van de [API Create](/rest/api/mysql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

Als u de `azure.replication_support` para meter niet op **replica** hebt ingesteld op een bron server met algemeen of geoptimaliseerd voor geheugen en de server opnieuw hebt opgestart, wordt een fout bericht weer gegeven. Voer de volgende twee stappen uit voordat u een replica maakt.

Een replica wordt gemaakt met behulp van dezelfde berekenings-en opslag instellingen als de hoofd server. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de bron server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.


> [!IMPORTANT]
> Voordat de instelling van een bron server wordt bijgewerkt naar een nieuwe waarde, werkt u de replica-instelling bij naar een gelijke of hogere waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

### <a name="list-replicas"></a>Replica's weer geven
U kunt de lijst met replica's van een bron server weer geven met behulp van de [replica lijst-API](/rest/api/mysql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen
U kunt de replicatie tussen een bron server en een lees replica stoppen door de [Update-API](/rest/api/mysql/servers/update)te gebruiken.

Nadat u de replicatie naar een bron server en een lees replica hebt gestopt, kunt u deze niet meer ongedaan maken. De Lees replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen ondersteunt. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Een bron-of replica server verwijderen
Als u een bron-of replica server wilt verwijderen, gebruikt u de [API verwijderen](/rest/api/mysql/servers/delete):

Wanneer u een bron server verwijdert, wordt replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige servers die nu zowel lees-als schrijf bewerkingen ondersteunen.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [lezen van replica's](concepts-read-replicas.md)