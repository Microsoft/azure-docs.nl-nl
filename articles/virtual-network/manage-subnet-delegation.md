---
title: Een delegatie van een subnet toevoegen of verwijderen in een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over het toevoegen of verwijderen van een gedelegeerd subnet voor een service in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 401124ed4b2794d891ca224ba3dc1c78edcae8d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783409"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Een delegatie van een subnet toevoegen of verwijderen

Delegering van subnetten geeft de service expliciete machtigingen voor het maken van servicespecifieke resources in het subnet met behulp van een unieke id bij het implementeren van de service. In dit artikel wordt beschreven hoe u een gedelegeerd subnet voor een Azure-service toevoegt of verwijdert.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en het subnet dat u later delegeert aan een Azure-service.

1. Selecteer in de linkerbovenhoek van het scherm **De resource**  >  **Netwerken**  >  **Virtueel netwerk maken.**
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *MyVirtualNetwork in.* |
    | Adresruimte | Voer *10.0.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **EastUS**.|
    | Subnet - Naam | Voer *mySubnet in.* |
    | Subnet - adresbereik | Voer *10.0.0.0/24* in. |
    |||
1. Laat de rest op de standaardwaarde staan en selecteer **vervolgens Maken.**

### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren niet hebt aan een Azure-service, hebt u de volgende machtiging nodig: `Microsoft.Network/virtualNetworks/subnets/write` .

De ingebouwde rol [Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet delegeren aan een Azure-service

In deze sectie delegeert u het subnet dat u in de vorige sectie hebt gemaakt, naar een Azure-service.

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork in* de zoekresultaten.
3. Selecteer **Subnetten** onder **INSTELLINGEN** en selecteer **vervolgens mySubnet.**
4. Selecteer op de pagina *mySubnet* voor de lijst Delegatie van **subnet** een van de services die worden vermeld onder **Subnet** aan een service delegeren (bijvoorbeeld **Microsoft.DBforPostgreSQL/serversv2).**  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnetdelegering verwijderen uit een Azure-service

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork in* de zoekresultaten.
3. Selecteer **Subnetten** onder **INSTELLINGEN** en selecteer **vervolgens mySubnet.**
4. Selecteer *op de pagina mySubnet* voor  de lijst **Subnetdelegering** de optie Geen in de services die worden vermeld onder Subnet aan **een service delegeren.** 

## <a name="azure-cli"></a>Azure CLI

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep maken met [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroup** gemaakt op de locatie **eastus**:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak met [az network vnet create](/cli/azure/network/vnet) in **myResourceGroup** een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt maken, hebt u de volgende machtiging nodig: `Microsoft.Network/virtualNetworks/subnets/write` .

De ingebouwde rol [Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet delegeren aan een Azure-service

In deze sectie delegeert u het subnet dat u in de vorige sectie hebt gemaakt, naar een Azure-service. 

Gebruik [az network vnet subnet update om](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) het subnet met de naam **mySubnet bij** te werken met een overdracht naar een Azure-service.  In dit voorbeeld **wordt Microsoft.DBforPostgreSQL/serversv2** gebruikt voor de voorbeelddelegering:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Gebruik [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show)om te controleren of de overdracht is toegepast. Controleer of de service is gedelegeerd naar het subnet onder de eigenschap **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnetdelegering uit een Azure-service verwijderen

Gebruik [az network vnet subnet update om](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) de delegatie te verwijderen uit het subnet met de naam **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Gebruik [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show)om te controleren of de overdracht is verwijderd. Controleer of de service is verwijderd uit het subnet onder de eigenschap **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
De uitvoer van de opdracht is een null-haakje:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Verbinding maken met Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Virtueel netwerk maken

Maak een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet** met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) in de **myResourceGroup** met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). De IP-adresruimte voor het virtuele netwerk is **10.0.0.0/16.** Het subnet binnen het virtuele netwerk is **10.0.0.0/24.**  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Machtigingen

Als u het subnet dat u wilt delegeren aan een Azure-service niet hebt maken, hebt u de volgende machtiging nodig: `Microsoft.Network/virtualNetworks/subnets/write` .

De ingebouwde rol [Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) bevat ook de benodigde machtigingen.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet delegeren aan een Azure-service

In deze sectie delegeert u het subnet dat u in de vorige sectie hebt gemaakt, naar een Azure-service. 

Gebruik [Add-AzDelegation om](/powershell/module/az.network/add-azdelegation) het subnet met de naam **mySubnet bij** te werken met een delegering met de naam **myDelegation** naar een Azure-service.  In dit voorbeeld **wordt Microsoft.DBforPostgreSQL/serversv2** gebruikt voor de voorbeelddelegering:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Gebruik [Get-AzDelegation om de](/powershell/module/az.network/get-azdelegation) delegering te controleren:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnetdelegering verwijderen uit een Azure-service

Gebruik [Remove-AzDelegation om de delegering](/powershell/module/az.network/remove-azdelegation) te verwijderen uit het subnet met de naam **mySubnet:**

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Gebruik [Get-AzDelegation om](/powershell/module/az.network/get-azdelegation) te controleren of de overdracht is verwijderd:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het beheren van subnetten in Azure](virtual-network-manage-subnet.md).
