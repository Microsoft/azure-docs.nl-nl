---
title: Routeringsvoorkeur voor een VM configureren - Azure CLI
description: Meer informatie over het maken van een VM met een openbaar IP-adres met routeringsvoorkeur met behulp van de Azure-opdrachtregelinterface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9155c3114d5a5a1b8729351dc189bc1e5c22369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764473"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Routeringsvoorkeur voor een VM configureren met behulp van Azure CLI

In dit artikel wordt beschreven hoe u routeringsvoorkeur voor een virtuele machine configureert. Internetverkeer van de VM wordt gerouteerd via het ISP-netwerk wanneer u **Internet** als routeringsvoorkeur kiest. De standaardroutering is via het wereldwijde netwerk van Microsoft.

In dit artikel wordt beschreven hoe u een virtuele machine maakt met een openbaar IP-adres dat is ingesteld om verkeer via het openbare internet te leiden met behulp van Azure CLI.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
1. Als u de Cloud Shell, gaat u verder met stap 2. Open een opdrachtsessie en meld u aan bij Azure met `az login` .
2. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep gemaakt in de Azure-regio VS - oost:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken
Als u vanaf internet toegang wilt krijgen tot uw virtuele machines, moet u een openbaar IP-adres maken. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). In het volgende voorbeeld wordt een openbaar IP-adres van het routeringsvoorkeurstype *Internet* gemaakt in de regio *VS -* oost:

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Voordat u een virtuele machine implementeert, moet u ondersteunende netwerkbronnen maken: netwerkbeveiligingsgroep, virtueel netwerk en virtuele NIC.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep voor de regels voor binnenkomende en uitgaande communicatie in uw VNet met [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). In het volgende voorbeeld wordt een virtueel netwerk met de *naam myVNET gemaakt* met subnetten *mySubNet:*

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Een NIC maken

Maak een virtuele NIC voor de VM met [az network nic create.](/cli/azure/network/nic#az_network_nic_create) In het volgende voorbeeld wordt een virtuele NIC gemaakt, die wordt gekoppeld aan de virtuele machine.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met [az vm create](/cli/azure/vm#az_vm_create). In het volgende voorbeeld wordt een virtuele machine met Windows Server 2019 en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az_group_delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [routeringsvoorkeur in openbare IP-adressen.](routing-preference-overview.md)
- Meer informatie over [openbare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over instellingen [voor openbare IP-adressen.](virtual-network-public-ip-address.md#create-a-public-ip-address)
