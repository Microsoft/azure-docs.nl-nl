---
title: Leesreplica's beheren - Azure CLI, REST API - Azure Database for PostgreSQL - Enkele server
description: Meer informatie over het beheren van leesreplica's in Azure Database for PostgreSQL - Single Server vanuit de Azure CLI en REST API
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d13db238674cae62f528c3d730bf892a72b8f6c2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764689"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Leesreplica's maken en beheren vanuit de Azure CLI, REST API

In dit artikel leert u hoe u leesreplica's maakt en beheert in Azure Database for PostgreSQL met behulp van de Azure CLI en REST API. Zie het overzicht voor meer informatie over [leesreplica's.](concepts-read-replicas.md)

## <a name="azure-replication-support"></a>Ondersteuning voor Azure-replicatie
[Leesreplica's](concepts-read-replicas.md) [en logische decoderen](concepts-logical.md) zijn beide afhankelijk van het Postgres Write Ahead Log (WAL) voor informatie. Voor deze twee functies zijn verschillende logboekregistratieniveaus van Postgres nodig. Logische decoderen heeft een hoger logboekregistratieniveau nodig dan leesreplica's.

Gebruik de ondersteuningsparameter voor Azure-replicatie om het juiste logboekregistratieniveau te configureren. Ondersteuning voor Azure-replicatie heeft drie instellingsopties:

* **Uit:** plaatst de minste informatie in de WAL. Deze instelling is niet beschikbaar op de meeste Azure Database for PostgreSQL servers.  
* **Replica:** uitgebreider dan **Uit.** Dit is het minimale niveau van logboekregistratie dat nodig is [om leesreplica's te](concepts-read-replicas.md) laten werken. Deze instelling is de standaardinstelling op de meeste servers.
* **Logisch:** uitgebreider dan **Replica.** Dit is het minimale logboekregistratieniveau voor logische decoderen. Leesreplica's werken ook bij deze instelling.


> [!NOTE]
> Bij het implementeren van leesreplica's voor permanente, zware, schrijfintensieve primaire workloads, kan de replicatievertraging blijven toenemen en kan deze mogelijk nooit een achterstand met de primaire replica's inhalen. Dit kan ook het opslaggebruik op de primaire omdat de WAL-bestanden worden niet verwijderd totdat ze worden ontvangen op de replica.

## <a name="azure-cli"></a>Azure CLI
U kunt leesreplica's maken en beheren met behulp van de Azure CLI.

### <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](/cli/azure/install-azure-cli)
- Een [Azure Database for PostgreSQL server](quickstart-create-server-up-azure-cli.md) als primaire server.


### <a name="prepare-the-primary-server"></a>De primaire server voorbereiden

1. Controleer de waarde van de primaire `azure.replication_support` server. Leesreplica's moeten ten minste REPLICA zijn.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Als `azure.replication_support` niet ten minste REPLICA is, stelt u deze in. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Start de server opnieuw op om de wijziging toe te passen.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Een leesreplica maken

De [opdracht az postgres server replica create](/cli/azure/postgres/server/replica#az_postgres_server_replica_create) vereist de volgende parameters:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group | myResourceGroup |  De resourcegroep waar de replicaserver wordt gemaakt.  |
| naam | mydemoserver-replica | De naam van de nieuwe replicaserver die wordt gemaakt. |
| source-server | mydemoserver | De naam of resource-id van de bestaande primaire server van waardaan moet worden gerepliceerd. Gebruik de resource-id als u wilt dat de resourcegroepen van de replica en de master anders zijn. |

In het onderstaande CLI-voorbeeld wordt de replica gemaakt in dezelfde regio als de hoofdreplica.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Gebruik de parameter om een leesreplica voor verschillende regio's te `--location` maken. In het onderstaande CLI-voorbeeld wordt de replica gemaakt in VS - west.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ga naar het artikel Over concepten van leesreplica's voor meer informatie over de regio's waarin u een [replica kunt maken.](concepts-read-replicas.md) 

Als u de parameter niet hebt ingesteld op REPLICA op een Algemeen of primaire server die is geoptimaliseerd voor geheugen en de server opnieuw hebt opgestart, wordt `azure.replication_support` er een foutbericht weergegeven.  Voltooi deze twee stappen voordat u een replica maakt.

> [!IMPORTANT]
> Bekijk de [sectie overwegingen van het overzicht van Leesreplica.](concepts-read-replicas.md#considerations)
>
> Voordat een primaire serverinstelling wordt bijgewerkt naar een nieuwe waarde, moet u de replica-instelling bijwerken naar een gelijke of hogere waarde. Met deze actie houdt de replica alle wijzigingen in de hoofdreplica bij.

### <a name="list-replicas"></a>Replica's opslijst
U kunt de lijst met replica's van een primaire server weergeven met behulp van [de opdracht az postgres server replica list.](/cli/azure/postgres/server/replica#az_postgres_server_replica_list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replicaserver stoppen
U kunt de replicatie tussen een primaire server en een leesreplica stoppen met behulp van [de opdracht az postgres server replica stop.](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop)

Nadat u de replicatie naar een primaire server en een leesreplica hebt gestopt, kan deze niet ongedaan worden gemaakt. De leesreplica wordt een zelfstandige server die ondersteuning biedt voor zowel lees- als schrijfreplica's. De zelfstandige server kan niet opnieuw worden gemaakt in een replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Een primaire server of replicaserver verwijderen
Als u een primaire server of replicaserver wilt verwijderen, gebruikt u de [opdracht az postgres server delete.](/cli/azure/postgres/server#az_postgres_server_delete)

Wanneer u een primaire server verwijdert, wordt de replicatie naar alle leesreplica's gestopt. De leesreplica's worden zelfstandige servers die nu zowel lees- als schrijfreplica's ondersteunen.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST-API
U kunt leesreplica's maken en beheren met behulp van [de Azure REST API.](/rest/api/azure/)

### <a name="prepare-the-primary-server"></a>De primaire server voorbereiden

1. Controleer de waarde van de primaire `azure.replication_support` server. Het moet ten minste REPLICA zijn om leesreplica's te laten werken.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Als `azure.replication_support` niet ten minste REPLICA is, stelt u deze in.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Start de server opnieuw op](/rest/api/postgresql/servers/restart) om de wijziging toe te passen.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Een leesreplica maken
U kunt een leesreplica maken met behulp van de [API maken:](/rest/api/postgresql/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Ga naar het artikel Over concepten van leesreplica's voor meer informatie over de regio's waarin u een [replica kunt maken.](concepts-read-replicas.md) 

Als u de parameter niet hebt ingesteld op REPLICA op een Algemeen of primaire server die is geoptimaliseerd voor geheugen en de server opnieuw hebt opgestart, wordt `azure.replication_support` er een foutbericht weergegeven.  Voltooi deze twee stappen voordat u een replica maakt.

Een replica wordt gemaakt met behulp van dezelfde reken- en opslaginstellingen als de hoofdreplica. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de primaire server worden gewijzigd: compute-generatie, vCores, opslag en bewaarperiode voor back-up. De prijscategorie kan ook onafhankelijk worden gewijzigd, behalve in of van de Basic-laag.


> [!IMPORTANT]
> Voordat een primaire serverinstelling wordt bijgewerkt naar een nieuwe waarde, moet u de replica-instelling bijwerken naar een gelijke of hogere waarde. Met deze actie houdt de replica alle wijzigingen in de hoofdreplica bij.

### <a name="list-replicas"></a>Replica's op een lijst zetten
U kunt de lijst met replica's van een primaire server weergeven met behulp van de [replicalijst-API](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replicaserver stoppen
U kunt de replicatie tussen een primaire server en een leesreplica stoppen met behulp van de [update-API](/rest/api/postgresql/servers/update).

Nadat u de replicatie naar een primaire server en een leesreplica hebt gestopt, kan deze niet ongedaan worden gemaakt. De leesreplica wordt een zelfstandige server die zowel lees- als schrijfreplica ondersteunt. De zelfstandige server kan niet opnieuw worden gemaakt in een replica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Een primaire server of replicaserver verwijderen
Als u een primaire server of replicaserver wilt verwijderen, gebruikt u [de API verwijderen:](/rest/api/postgresql/servers/delete)

Wanneer u een primaire server verwijdert, wordt de replicatie naar alle leesreplica's gestopt. De leesreplica's worden zelfstandige servers die nu zowel lees- als schrijfreplica's ondersteunen.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [leesreplica's in Azure Database for PostgreSQL](concepts-read-replicas.md).
* Meer informatie over het [maken en beheren van leesreplica's in de Azure Portal.](howto-read-replicas-portal.md)