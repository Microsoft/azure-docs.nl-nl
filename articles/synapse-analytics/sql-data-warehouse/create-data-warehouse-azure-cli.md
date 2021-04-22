---
title: 'Quickstart: Een Synapse SQL-pool maken met Azure CLI'
description: Met een firewallregel op serverniveau en de Azure CLI kunt u snel een Synapse SQL-pool maken.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 3903aa0be5ffa63bc4292371c59002846ec9363c
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892090"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Quickstart: Een Synapse SQL-pool maken met Azure CLI

Maak een Synapse SQL-pool (datawarehouse) in Azure Synapse Analytics met behulp van de Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Aan de slag

Gebruik deze opdrachten om u aan te melden bij Azure en een resourcegroep in te stellen.

1. Als u een lokale installatie gebruikt, voert u de opdracht [az login](/cli/azure/reference-index#az_login) uit om u aan te melden bij Azure:

   ```azurecli
   az login
   ```

1. Gebruik, indien nodig, de [az account set](/cli/azure/account#az_account_set) opdracht om uw abonnement te selecteren:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Maak een [logische SQL-server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) met de opdracht [az sql server create](/cli/azure/sql/server#az_sql_server_create):

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Een server bevat een groep met databases die worden beheerd als groep.

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren

Een [serverfirewallregel](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) maken. Een firewallregel op serverniveau kan een externe toepassing, zoals SQL Server Management Studio of het hulpprogramma SQLCMD, via de firewall van de SQL-poolservice verbinding laten maken met een SQL-pool.

Maak een firewallregel door de opdracht [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) uit te voeren:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

In dit voorbeeld wordt de firewall alleen geopend voor andere Azure-resources. Voor externe connectiviteit wijzigt u het IP-adres in een correct adres voor uw omgeving. Als u alle IP-adressen wilt openen, gebruikt u 0.0.0.0 als beginadres en 255.255.255.255 als eindadres.

> [!NOTE]
> SQL-eindpunten communiceren via poort 1433. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 1433 openstelt.
>

## <a name="create-and-manage-your-sql-pool"></a>Uw SQL-pool maken en beheren

Maak de SQL-pool. Dit voorbeeld gebruikt DW100c als de servicedoelstelling. Dit is een goedkoper startpunt voor uw SQL-pool.

> [!NOTE]
> U hebt een eerder gemaakte werkruimte nodig. Zie voor meer informatie [Snelstart: Een Azure Synapse-werkruimte maken met Azure CLI](../quickstart-create-workspace-cli.md).

Gebruik de opdracht [az synapse sql pool create](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_create) voor het maken van de SQL-pool:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Zie [az synapse sql pool](/cli/azure/synapse/sql/pool) voor meer informatie over de parameteropties.

U kunt uw SQL-pools weergeven met behulp van de opdracht [az synapse sql pool list](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_list):

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Gebruik de opdracht [az synapse sql pool update](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_update) voor het bijwerken van een bestaande pool:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Gebruik de opdracht [az synapse sql pool pause](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_pause) om uw pool te onderbreken:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Gebruik de opdracht [az synapse sql pool resume](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_resume) om een onderbroken pool te starten:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Als u een bestaande SQLpool wilt verwijderen, gebruikt u de opdracht [az synapse sql pool delete](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_delete):

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Resources opschonen

Andere zelfstudies in deze verzameling zijn gebaseerd op deze snelstart.

> [!TIP]
> Verwijder de resources die u in deze quickstart hebt gemaakt niet als u verder wilt gaan met volgende quickstart-zelfstudies. Als u niet wilt doorgaan, gebruikt u de opdracht [az group delete](/cli/azure/group#az_group_delete) om alle resources te verwijderen die door deze quickstart zijn gemaakt.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

U hebt nu een SQL-pool gemaakt, een firewallregel gemaakt en deze verbonden met uw SQL-pool. Ga voor meer informatie naar het artikel [Gegevens in een SQL-pool laden](./load-data-from-azure-blob-storage-using-copy.md).
