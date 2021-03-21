---
title: VNet-eind punten beheren-Azure CLI-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u Azure Database for MariaDB VNet-service-eind punten en-regels maakt en beheert met behulp van de Azure CLI-opdracht regel.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43d1b7700395bd06960737eae4f318d61aa03717
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665086"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Azure Database for MariaDB VNet-service-eind punten maken en beheren met Azure CLI

Service-eindpunten en -regels voor virtuele netwerken (VNets) breiden de privé-adresruimte van een virtueel netwerk uit naar uw Azure Database for MariaDB-server. Met de handige CLI-opdrachten (opdracht regel Interface) van Azure kunt u VNet-service-eind punten en-regels maken, bijwerken, verwijderen en weer geven om uw server te beheren. Zie [Azure database for MariaDB server VNet-service-eind punten](concepts-data-access-security-vnet.md)voor een overzicht van Azure database for MariaDB VNet-service-eind punten, met inbegrip van beperkingen. VNet-service-eind punten zijn beschikbaar in alle ondersteunde regio's voor Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- U hebt een [Azure database for MariaDB-server en-data base](quickstart-create-mariadb-server-database-using-azure-cli.md)nodig.

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!NOTE]
> Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

## <a name="configure-vnet-service-endpoints"></a>VNet-service-eind punten configureren

De opdracht [AZ Network vnet](/cli/azure/network/vnet) wordt gebruikt voor het configureren van virtuele netwerken.

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account#az-account-set). Gebruik de **id**-eigenschap uit de **az login**-uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnement-id.

- Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

Service-eind punten kunnen afzonderlijk op virtuele netwerken worden geconfigureerd door een gebruiker met schrijf toegang tot het virtuele netwerk.

Als u Azure-service resources wilt beveiligen met een VNet, moet de gebruiker over de machtiging ' micro soft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/' beschikken voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.

Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md).

VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet-en Azure-service resources zich in verschillende abonnementen bevinden, moeten de resources onder dezelfde Active Directory (AD)-Tenant vallen. Zorg ervoor dat de **micro soft. SQL** -resource provider is geregistreerd voor beide abonnementen. Raadpleeg [Resource-Manager-registratie][resource-manager-portal] voor meer informatie

> [!IMPORTANT]
> Het is raadzaam om dit artikel te lezen over service-eindpunt configuraties en overwegingen voordat u service-eind punten configureert. **Service-eind punt Virtual Network:** Een [Virtual Network Service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. VNet-service-eind punten gebruiken de service type naam **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database. Deze servicetag is ook van toepassing op de Azure SQL Database-, Azure Database for MariaDB-, PostgreSQL-en MySQL-Services. Het is belang rijk te weten wanneer u de code van het **micro soft. SQL** -service toepast op een VNet-service-eind punt, waarbij service-eindpunt verkeer wordt geconfigureerd voor alle Azure Data Base-Services, waaronder Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB en Azure database for MySQL servers in het subnet.

### <a name="sample-script"></a>Voorbeeldscript

Dit voorbeeld script wordt gebruikt om een Azure Database for MariaDB server te maken, een VNet-, VNet-service-eind punt te maken en de server te beveiligen met het subnet met een VNet-regel. Wijzig in dit voorbeeld script de gebruikers naam en het wacht woord voor de beheerder. Vervang de SubscriptionID die in de `az account set --subscription` opdracht wordt gebruikt door uw eigen abonnement-id.

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