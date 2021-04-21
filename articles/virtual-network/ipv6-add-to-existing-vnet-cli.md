---
title: IPv6 toevoegen aan een IPv4-toepassing in een virtueel Azure-netwerk - Azure CLI
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u IPv6-adressen implementeert in een bestaande toepassing in een virtueel Azure-netwerk met behulp van Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 5835ea4d80f9c4111b76672facc4a0250ae0079a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769855"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>IPv6 toevoegen aan een IPv4-toepassing in een virtueel Azure-netwerk - Azure CLI

In dit artikel wordt beschreven hoe u IPv6-adressen toevoegt aan een toepassing die gebruik maakt van een openbaar IPv4-IP-adres in een virtueel Azure-netwerk voor een Standard Load Balancer met behulp van Azure CLI. De in-place upgrade bevat een virtueel netwerk en subnet, een Standard Load Balancer met IPv4 + IPv6-front-en-eindpuntconfiguraties, VM's met NIC's met een IPv4 + IPv6-configuratie, netwerkbeveiligingsgroep en openbare IP's.

## <a name="prerequisites"></a>Vereisten

- In dit artikel wordt ervan uitgenomen dat u een Standard Load Balancer geïmplementeerd, zoals beschreven in [Quickstart: Een](../load-balancer/quickstart-load-balancer-standard-public-cli.md)Standard Load Balancer maken - Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-ipv6-addresses"></a>IPv6-adressen maken

Maak een openbaar IPv6-adres met [az network public-ip create](/cli/azure/network/public-ip) voor uw Standard Load Balancer. In het volgende voorbeeld wordt een openbaar IPv6-IP-adres *met de PublicIP_v6* gemaakt in de resourcegroep *myResourceGroupSLB:*

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>IPv6-front-load balancer configureren

Configureer de load balancer met het nieuwe IPv6 [IP-adres met az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) als volgt:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6-back load balancer pool configureren

Maak als volgt de back-endpool voor NIC's met [IPv6-adressen met az network lb address-pool create:](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create)

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6-load balancer configureren

Maak IPv6-load balancer met [az network lb rule create.](/cli/azure/network/lb/rule#az_network_lb_rule_create)

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6-adresbereiken toevoegen

Voeg als volgt IPv6-adresbereiken toe aan het virtuele netwerk en subnet dat als host load balancer het virtuele netwerk:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>IPv6-configuratie toevoegen aan NIC's

Configureer de VM-NIC's met een [IPv6-adres met az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) als volgt:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtueel IPv6-netwerk met dubbele stack weergeven in Azure Portal

U kunt het virtuele IPv6-netwerk met dubbele stack als Azure Portal weergeven:
1. Voer in de zoekbalk van de portal *myVnet in.*
2. Wanneer **myVnet** wordt weergegeven in de zoekresultaten, selecteert u dit. Hiermee wordt de pagina **Overzicht van** het virtuele netwerk met dubbele stack met de *naam myVNet gelanceerd.* Het virtuele netwerk met dubbele stack toont de drie NIC's met zowel IPv4- als IPv6-configuraties in het dual stack-subnet met de *naam mySubnet.*

  ![Virtueel IPv6-netwerk met dubbele stack in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een bestaande Standard Load Balancer bijgewerkt met een IPv4-front-end-IP-configuratie naar een configuratie met dubbele stack (IPv4 en IPv6). U hebt ook IPv6-configuraties toegevoegd aan de NIC's van de VM's in de back-endpool. Zie Wat is IPv6 voor Azure Virtual Network? voor meer informatie over [IPv6-ondersteuning](ipv6-overview.md) in virtuele Netwerken van Azure.
