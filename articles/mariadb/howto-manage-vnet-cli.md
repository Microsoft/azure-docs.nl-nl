---
title: VNet-eindpunten beheren - Azure CLI - Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u VNet-service Azure Database for MariaDB-eindpunten en -regels maakt en beheert met behulp van de Azure CLI-opdrachtregel.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf87865fb2fc70251e1e417361333cfd750d6e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783661"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>VNet-Azure Database for MariaDB maken en beheren met behulp van Azure CLI

Service-eindpunten en -regels voor virtuele netwerken (VNets) breiden de privé-adresruimte van een virtueel netwerk uit naar uw Azure Database for MariaDB-server. Met behulp van handige CLI-opdrachten (Azure Command Line Interface) kunt u VNet-service-eindpunten en -regels maken, bijwerken, verwijderen, weergeven en weergeven om uw server te beheren. Zie Azure Database for MariaDB Server VNet-service-eindpunten voor een overzicht van [VNet-service-eindpunten Azure Database for MariaDB,](concepts-data-access-security-vnet.md)inclusief beperkingen. VNet-service-eindpunten zijn beschikbaar in alle ondersteunde regio's voor Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- U hebt een [Azure Database for MariaDB server en database nodig.](quickstart-create-mariadb-server-database-using-azure-cli.md)

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!NOTE]
> Ondersteuning voor VNet-service-eindpunten is alleen Algemeen servers die zijn geoptimaliseerd voor geheugen.

## <a name="configure-vnet-service-endpoints"></a>VNet-service-eindpunten configureren

De [opdrachten az network vnet](/cli/azure/network/vnet) worden gebruikt om virtuele netwerken te configureren.

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account#az_account_set). Gebruik de **id**-eigenschap uit de **az login**-uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnement-id.

- Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

Service-eindpunten kunnen onafhankelijk van elkaar in virtuele netwerken worden geconfigureerd door een gebruiker met schrijftoegang tot het virtuele netwerk.

Als u Azure-servicebronnen naar een VNet wilt beveiligen, moet de gebruiker machtigingen hebben voor Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.

Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md).

VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet- en Azure-serviceresources zich in verschillende abonnementen hebben, moeten de resources zich onder dezelfde Active Directory-tenant (AD) vallen. Zorg ervoor dat voor beide abonnementen de **Microsoft.Sql-resourceprovider** is geregistreerd. Raadpleeg [resource-manager-registration voor meer informatie][resource-manager-portal]

> [!IMPORTANT]
> Het wordt ten zeerste aanbevolen dit artikel te lezen over configuraties en overwegingen voor service-eindpunten voordat u service-eindpunten configureert. **Virtual Network service-eindpunt:** Een [Virtual Network service-eindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschapswaarden een of meer formele Namen van Azure-servicetypes bevatten. VNet-service-eindpunten gebruiken de servicetypenaam **Microsoft.Sql,** die verwijst naar de Azure-service met de naam SQL Database. Deze servicetag is ook van toepassing op de Azure SQL Database-, Azure Database for MariaDB-, PostgreSQL- en MySQL-services. Het is belangrijk te weten dat bij het toepassen van de **Microsoft.Sql-servicetag** op een VNet-service-eindpunt het service-eindpuntverkeer configureert voor alle Azure Database-services, waaronder Azure SQL Database-, Azure Database for PostgreSQL-, Azure Database for MariaDB- en Azure Database for MySQL-servers op het subnet.

### <a name="sample-script"></a>Voorbeeldscript

Dit voorbeeldscript wordt gebruikt om een Azure Database for MariaDB-server te maken, een VNet- en VNet-service-eindpunt te maken en de server naar het subnet te beveiligen met een VNet-regel. In dit voorbeeldscript wijzigt u de gebruikersnaam en het wachtwoord van de beheerder. Vervang de SubscriptionID die in de opdracht `az account set --subscription` wordt gebruikt door uw eigen abonnements-id.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md