---
title: VNet-eindpunten beheren - Azure CLI - Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u VNet Azure Database for MySQL-service-eindpunten en -regels maakt en beheert met behulp van de Azure CLI-opdrachtregel.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bc686efbd07cf39d7932b175b6f9800b1ff185f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774661"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>VNet-service Azure Database for MySQL-eindpunten maken en beheren met behulp van Azure CLI
Service-eindpunten en -regels voor virtuele netwerken (VNets) breiden de privé-adresruimte van een virtueel netwerk uit naar uw Azure Database for MySQL-server. Met behulp van handige CLI-opdrachten (Azure Command Line Interface) kunt u VNet-service-eindpunten en -regels voor het beheren van uw server maken, bijwerken, verwijderen, weergeven en weergeven. Zie voor een overzicht Azure Database for MySQL VNet-service-eindpunten, inclusief [beperkingen, Azure Database for MySQL Server VNet-service-eindpunten.](concepts-data-access-and-security-vnet.md) VNet-service-eindpunten zijn beschikbaar in alle ondersteunde regio's voor Azure Database for MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- U hebt een [Azure Database for MySQL-server en -database nodig.](quickstart-create-mysql-server-database-using-azure-cli.md)
 
- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!NOTE]
> Ondersteuning voor VNet-service-eindpunten is alleen van Algemeen servers die zijn geoptimaliseerd voor geheugen.
> Als in het geval van VNet-peering verkeer via een gemeenschappelijke VNet Gateway met service-eindpunten stroomt en naar de peer moet stromen, maakt u een ACL/VNet-regel om Azure Virtual Machines in het gateway-VNet toegang te geven tot de Azure Database for MySQL-server.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Vnet-service-eindpunten configureren voor Azure Database for MySQL
De [opdrachten az network vnet](/cli/azure/network/vnet) worden gebruikt om virtuele netwerken te configureren.

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account#az_account_set). Vervang de **eigenschap id** uit de **az login** output voor uw abonnement door de tijdelijke aanduiding voor de abonnements-id.

- Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

Service-eindpunten kunnen onafhankelijk van elkaar in virtuele netwerken worden geconfigureerd door een gebruiker met schrijftoegang tot het virtuele netwerk.

Als u Azure-servicebronnen naar een VNet wilt beveiligen, moet de gebruiker machtigingen hebben voor Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.

Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md).

VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet- en Azure-serviceresources zich in verschillende abonnementen hebben, moeten de resources zich onder dezelfde Active Directory-tenant (AD) vallen. Zorg ervoor dat voor beide abonnementen de **Microsoft.Sql-resourceprovider** is geregistreerd. Raadpleeg [resource-manager-registration voor meer informatie][resource-manager-portal]

> [!IMPORTANT]
> Het wordt ten zeerste aanbevolen dit artikel te lezen over configuraties en overwegingen voor service-eindpunten voordat u het onderstaande voorbeeldscript gaat uitvoeren of service-eindpunten configureert. **Virtual Network service-eindpunt:** Een [Virtual Network service-eindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschapswaarden een of meer formele Namen van Azure-servicetypes bevatten. VNet-service-eindpunten gebruiken de servicetypenaam **Microsoft.Sql,** die verwijst naar de Azure-service met de naam SQL Database. Deze servicetag is ook van toepassing op Azure SQL Database-, Azure Database for PostgreSQL- en MySQL-services. Het is belangrijk om te weten dat bij het toepassen van de **Microsoft.Sql-servicetag** op een VNet-service-eindpunt het service-eindpuntverkeer wordt geconfigureerd voor alle Azure Database-services, waaronder Azure SQL Database-, Azure Database for PostgreSQL- en Azure Database for MySQL-servers op het subnet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Voorbeeldscript voor het maken van een Azure Database for MySQL-database, het maken van een VNet, een VNet-service-eindpunt en het beveiligen van de server naar het subnet met een VNet-regel
Wijzig in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder aan te passen. Vervang de SubscriptionID die in de opdracht `az account set --subscription` wordt gebruikt door uw eigen abonnements-id.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
