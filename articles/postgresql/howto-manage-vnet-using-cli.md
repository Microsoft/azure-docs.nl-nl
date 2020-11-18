---
title: Regels voor het virtuele netwerk gebruiken-Azure CLI-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u VNet-service-eind punten en-regels voor Azure Database for PostgreSQL maakt en beheert met behulp van de Azure CLI-opdracht regel.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07e0f7cf2834b3984d9207fa18f3b0e32340e216
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660879"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>VNet-service-eind punten maken en beheren voor Azure Database for PostgreSQL-één server met behulp van Azure CLI
Virtual Network-Services (VNet)-eind punten en-regels breiden de privé-adres ruimte van een Virtual Network naar uw Azure Database for PostgreSQL-server uit. Met de handige CLI-opdrachten (opdracht regel Interface) van Azure kunt u VNet-service-eind punten en-regels maken, bijwerken, verwijderen en weer geven om uw server te beheren. Zie [Azure database for postgresql server VNet-service-eind punten](concepts-data-access-and-security-vnet.md)voor een overzicht van Azure database for PostgreSQL VNet-service-eind punten, met inbegrip van beperkingen. VNet-service-eind punten zijn beschikbaar in alle ondersteunde regio's voor Azure Database for PostgreSQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten
U kunt deze hand leiding door lopen:
- Installeer [de Azure cli](/cli/azure/install-azure-cli) of gebruik de Azure Cloud shell in de browser.
- Een [Azure database for postgresql-server en-data base](quickstart-create-server-database-azure-cli.md)maken.

    > [!NOTE]
    > Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.
    > In het geval van VNet-peering, als verkeer via een gemeen schappelijke VNet-gateway met Service-eind punten stroomt en naar de peer moet stromen, moet u een ACL/VNet-regel maken om Azure Virtual Machines in de gateway-VNet toegang te geven tot de Azure Database for PostgreSQL-server.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Vnet-service-eind punten voor Azure Database for PostgreSQL configureren
De opdracht [AZ Network vnet](/cli/azure/network/vnet) wordt gebruikt voor het configureren van virtuele netwerken.

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account#az-account-set). Gebruik de **id**-eigenschap uit de **az login**-uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnement-id.

- Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

Service-eind punten kunnen afzonderlijk op virtuele netwerken worden geconfigureerd door een gebruiker met schrijf toegang tot het virtuele netwerk.

Als u Azure-service resources wilt beveiligen met een VNet, moet de gebruiker over de machtiging ' micro soft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/' beschikken voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.

Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md).

VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet-en Azure-service resources zich in verschillende abonnementen bevinden, moeten de resources onder dezelfde Active Directory (AD)-Tenant vallen. Zorg ervoor dat de **micro soft. SQL** -resource provider is geregistreerd voor beide abonnementen. Zie [Resource-Manager-Registration][resource-manager-portal](Engelstalig) voor meer informatie.

> [!IMPORTANT]
> Lees dit artikel over service-eindpunt configuraties en overwegingen voordat u het onderstaande voorbeeld script uitvoert of service-eind punten configureert. **Service-eind punt Virtual Network:** Een [Virtual Network Service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. VNet-service-eind punten gebruiken de service type naam **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database. Deze servicetag is ook van toepassing op de Azure SQL Database, Azure Database for PostgreSQL en MySQL-Services. Het is belang rijk te weten wanneer u de code van het **micro soft. SQL** -service toepast op een VNet-service-eind punt Hiermee wordt service-eindpunt verkeer geconfigureerd voor alle Azure Data Base-Services, waaronder Azure SQL Database, Azure Database for PostgreSQL en Azure database for MySQL servers in het subnet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Voorbeeld script voor het maken van een Azure Database for PostgreSQL-data base, het maken van een VNet-, VNet-service-eind punt en het beveiligen van de server met het subnet met een VNet-regel
Wijzig in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder aan te passen. Vervang de SubscriptionID die in de `az account set --subscription` opdracht wordt gebruikt door uw eigen abonnement-id.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
