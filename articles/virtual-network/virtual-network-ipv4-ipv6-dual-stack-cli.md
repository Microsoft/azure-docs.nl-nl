---
title: Een IPv6-toepassing met dubbele stack implementeren - Basic Load Balancer - CLI
titlesuffix: Azure Virtual Network
description: Leer hoe u een toepassing met dubbele stack (IPv4 + IPv6) implementeert met Basic Load Balancer azure CLI.
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
ms.openlocfilehash: e6205b4c5428f03459bc75c6fbb2a40458f0f898
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773203"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Een IPv6-toepassing met dubbele stack implementeren met basic Load Balancer - CLI

In dit artikel wordt beschreven hoe u een toepassing met twee stacks (IPv4 + IPv6) implementeert met Basic Load Balancer met behulp van Azure CLI. Dit omvat een virtueel netwerk met twee stacks met een dual stack-subnet, een Basic Load Balancer met dubbele front-endconfiguraties (IPv4 + IPv6), VM's met NIC's met een dubbele IP-configuratie, dubbele netwerkbeveiligingsgroepsregels en dubbele openbare IP-adressen.

Zie Deploy [an IPv6 dual stack](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)application with Standard Load Balancer using Azure CLI (Een IPv6-toepassing met dubbele stack implementeren met Standard Load Balancer met behulp van Azure CLI) voor het implementeren van een toepassing met dubbele stack (IPV4 + IPv6) met behulp van Standard Load Balancer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw virtuele netwerk met dubbele stack kunt maken, moet u een resourcegroep maken [met az group create.](/cli/azure/group) In het volgende voorbeeld wordt een resourcegroep met de *naam DsResourceGroup01* gemaakt op de *locatie eastus:*

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Openbare IPv4- en IPv6-IP-adressen maken voor load balancer
Voor toegang tot uw IPv4- en IPv6-eindpunten op internet hebt u openbare IPv4- en IPv6-IP-adressen nodig voor de load balancer. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). In het volgende voorbeeld worden het openbare IPv4- en IPv6-IP-adres *dsPublicIP_v4* en *dsPublicIP_v6* gemaakt in de resourcegroep *DsResourceGroup01:*

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Openbare IP-adressen voor VM's maken

Voor externe toegang tot uw VM's op internet hebt u openbare IPv4-IP-adressen voor de VM's nodig. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Load balancer van het type Basic maken

In deze sectie configureert u dubbele front-end-IP (IPv4 en IPv6) en de back-endadresgroep voor de load balancer en maakt u vervolgens een Basic-Load Balancer.

### <a name="create-load-balancer"></a>Load balancer maken

Maak de Basic Load Balancer met [az network lb create](/cli/azure/network/lb) met de naam **dsLB** die een front-endpool met de naam **dsLbFrontEnd_v4** bevat, een back-eindpuntgroep met de naam **dsLbBackEndPool_v4** die is gekoppeld aan het openbare IP-adres van IPv4 **dsPublicIP_v4** dat u in de vorige stap hebt gemaakt. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6-front-end maken

Maak een IPV6-front-end-IP [met az network lb frontend-ip create.](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) In het volgende voorbeeld wordt een front-end-IP-configuratie met *de dsLbFrontEnd_v6* gemaakt en wordt het *dsPublicIP_v6* gekoppeld:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6-back-endadresgroep configureren

Maak een IPv6-back-endadresgroepen [met az network lb address-pool create.](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) In het volgende voorbeeld wordt een back-endadresgroep met de *naam dsLbBackEndPool_v6*  om VM's met IPv6 NIC-configuraties op te nemen:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Een statustest maken
Maak met [az network lb probe create](/cli/azure/network/lb/probe) een statustest om de status van de virtuele machines te bewaken. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) om een load balancer-regel te maken. In het volgende voorbeeld worden load balancer regels met de naam *dsLBrule_v4* en *dsLBrule_v6* gemaakt en wordt verkeer op *TCP-poort* *80* naar de IPv4- en IPv6-front-end-IP-configuraties ge balanceerd:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Netwerkbronnen maken
Voordat u een aantal VM's implementeert, moet u ondersteunende netwerkbronnen maken: beschikbaarheidsset, netwerkbeveiligingsgroep, virtueel netwerk en virtuele NIC's. 
### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Plaats uw VM's in een beschikbaarheidsset om de beschikbaarheid van uw app te verbeteren.

Maak een beschikbaarheidsset met behulp van [az vm availability-set create](/cli/azure/vm/availability-set). In het volgende voorbeeld wordt een beschikbaarheidsset met de *naam dsAVset gemaakt:*

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep voor de regels voor binnenkomende en uitgaande communicatie in uw VNET.

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Een netwerkbeveiligingsgroep maken met [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Een netwerkbeveiligingsgroepsregel maken voor binnenkomende en uitgaande verbindingen

Maak een netwerkbeveiligingsgroepsregel om RDP-verbindingen toe te staan via poort 3389, internetverbinding via poort 80 en voor uitgaande verbindingen met [az network nsg rule create.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). In het volgende voorbeeld wordt een virtueel netwerk met de *naam dsVNET gemaakt* met subnetten *dsSubNET_v4* en *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>NIC's maken

Maak virtuele NIC's voor elke VM [met az network nic create.](/cli/azure/network/nic#az_network_nic_create) In het volgende voorbeeld wordt een virtuele NIC voor elke virtuele machine gemaakt. Elke NIC heeft twee IP-configuraties (1 IPv4-configuratie, 1 IPv6-configuratie). U maakt de IPV6-configuratie [met az network nic ip-config create.](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create)

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak de VM's [met az vm create](/cli/azure/vm#az_vm_create). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan. 

Maak als *volgt virtuele machine dsVM0:*

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Maak als *volgt virtuele machine dsVM1:*

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtueel IPv6-netwerk met dubbele stack weergeven in Azure Portal
U kunt het virtuele IPv6-netwerk met dubbele stack als Azure Portal weergeven:
1. Voer in de zoekbalk van de portal *dsVnet in.*
2. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit. Hiermee start u de **pagina Overzicht** van het virtuele netwerk met dubbele stack met de *naam dsVnet.* Het virtuele netwerk met dubbele stack toont de twee NIC's met zowel IPv4- als IPv6-configuraties in het dual stack-subnet met de *naam dsSubnet.*

  ![Virtueel IPv6-netwerk met dubbele stack in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az_group_delete) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Basic-Load Balancer met een dubbele front-end-IP-configuratie (IPv4 en IPv6). U hebt ook twee virtuele machines gemaakt met NIC's met dubbele IP-configuraties (IPV4 + IPv6) die zijn toegevoegd aan de back-endpool van de load balancer. Zie Wat is IPv6 voor Azure Virtual Network? voor meer informatie over [IPv6-ondersteuning](ipv6-overview.md) in virtuele Netwerken van Azure.
