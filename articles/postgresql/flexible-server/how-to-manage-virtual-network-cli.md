---
title: Virtuele netwerken beheren - Azure CLI - Azure Database for PostgreSQL - Flexible Server
description: Virtuele netwerken maken en beheren voor Azure Database for PostgreSQL - Flexible Server met behulp van de Azure CLI
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0a4bf648551be723007b0d8856fe0857896aad94
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778387"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Virtuele netwerken maken en beheren voor Azure Database for PostgreSQL - Flexible Server met behulp van de Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL Flexibele server ondersteunt twee soorten wederzijds exclusieve netwerkconnectiviteitsmethoden om verbinding te maken met uw flexibele server. De twee opties zijn:

* Openbare toegang (toegestane IP-adressen)
* Privétoegang (VNet-integratie)

In dit artikel richten we ons op het maken van een PostgreSQL-server met **privétoegang (VNet-integratie)** met behulp van Azure CLI. Met *Privétoegang (VNet-integratie)* kunt u uw flexibele server implementeren in uw eigen [Azure-Virtual Network.](../../virtual-network/virtual-networks-overview.md) Virtuele Azure-netwerken bieden privé- en beveiligde netwerkcommunicatie. Bij Privétoegang zijn de verbindingen met de PostgreSQL-server beperkt tot alleen binnen uw virtuele netwerk. Raadpleeg Privétoegang [(VNet-integratie)](./concepts-networking.md#private-access-vnet-integration)voor meer informatie.

In Azure Database for PostgreSQL Flexibele server kunt u de server alleen implementeren in een virtueel netwerk en subnet tijdens het maken van de server. Nadat de flexibele server is geïmplementeerd in een virtueel netwerk en subnet, kunt u deze niet verplaatsen naar een ander virtueel netwerk, subnet of openbare toegang *(toegestane IP-adressen).*

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

[Azure Cloud Shell](../../cloud-shell/overview.md) is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. Als u naar [https://shell.azure.com/bash](https://shell.azure.com/bash) gaat, kunt u Cloud Shell ook openen in een afzonderlijk browsertabblad. Selecteer **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en selecteer vervolgens **Enter** om de code uit te voeren.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0 of hoger van Azure CLI nodig. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/reference-index#az_login). Noteer **de eigenschap** ID, die verwijst naar **Abonnements-id** voor uw Azure-account.

```azurecli-interactive
az login
```

Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account#az_account_set). Noteer de **id-waarde uit** de **uitvoer az login** om te gebruiken als de waarde voor het argument **subscription** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Een Azure Database for PostgreSQL - Flexible Server maken met cli
U kunt de opdracht `az postgres flexible-server` gebruiken om de flexibele server met privétoegang *(VNet-integratie) te maken.* Deze opdracht maakt gebruik van Privétoegang (VNet-integratie) als de standaardmethode voor connectiviteit. Er worden een virtueel netwerk en subnet voor u gemaakt als er geen is opgegeven. U kunt ook het bestaande virtuele netwerk en subnet met behulp van subnet-id. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Er zijn verschillende opties voor het maken van een flexibele server met CLI, zoals wordt weergegeven in de onderstaande voorbeelden.

>[!Important]
> Met deze opdracht wordt het subnet gedelegeerd **naar Microsoft.DBforPostgreSQL/flexibleServers.** Deze overdracht betekent dat alleen Azure Database for PostgreSQL flexibele servers dat subnet kunnen gebruiken. Er kunnen zich geen andere Azure-resourcetypen in het gedelegeerde subnet bevinden.
>
Raadpleeg de referentiedocumentatie voor Azure CLI <!--FIXME --> voor de volledige lijst met configureerbare CLI-parameters. In de onderstaande opdrachten kunt u bijvoorbeeld eventueel de resourcegroep opgeven.

- Een flexibele server maken met standaard virtueel netwerk, subnet met standaardadresvoorvoegsel
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- Maak een flexibele server met behulp van een bestaand virtueel netwerk en subnet. Als het opgegeven virtuele netwerk en subnet niet bestaat, worden het virtuele netwerk en subnet met het standaardadresvoorvoegsel gemaakt.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- Maak een flexibele server met behulp van een bestaand virtueel netwerk, subnet en de subnet-id. In het opgegeven subnet mag geen andere resource zijn geïmplementeerd en dit subnet wordt gedelegeerd aan **Microsoft.DBforPostgreSQL/flexibleServers,** indien dat nog niet is gedelegeerd.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Het virtuele netwerk en subnet moeten zich in dezelfde regio en hetzelfde abonnement als uw flexibele server.

- Een flexibele server maken met behulp van een nieuw virtueel netwerk, subnet met een niet-standaardadresvoorvoegsel
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Raadpleeg de [referentiedocumentatie](/cli/azure/postgres/flexible-server) voor Azure CLI voor de volledige lijst met configureerbare CLI-parameters.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in Azure Database for PostgreSQL - Flexible Server](./concepts-networking.md).
- [Een virtueel netwerk Azure Database for PostgreSQL flexibele server maken en beheren met behulp van Azure Portal](./how-to-manage-virtual-network-portal.md).
- Meer informatie over [Azure Database for PostgreSQL - Virtueel netwerk met flexibele server.](./concepts-networking.md#private-access-vnet-integration)