---
title: Een IPv6-toepassing met dubbele stack implementeren-Standard Load Balancer-CLI
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u een IPv6 Dual stack-toepassing implementeert in een virtueel Azure-netwerk met behulp van Azure CLI.
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
ms.openlocfilehash: 81635a5f81548f0b4edeeb62fae19c567c6942bb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932738"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli"></a>Een IPv6-toepassing met dubbele stack implementeren in het virtuele netwerk van Azure-CLI

In dit artikel wordt beschreven hoe u een dual stack (IPv4 + IPv6)-toepassing implementeert met behulp van Standard Load Balancer in azure, met een virtueel netwerk van twee stacks met een dual stack-subnet, een Standard Load Balancer met dubbele (IPv4 + IPv6) front-end configuraties, Vm's met Nic's met een dubbele IP-configuratie, dubbele netwerk beveiligings groeps regels en dubbele open bare Ip's.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een virtueel netwerk met twee stacks kunt maken, moet u een resource groep maken met [AZ Group Create](/cli/azure/group). In het volgende voor beeld wordt een resource groep met de naam *DsResourceGroup01* gemaakt op de locatie *eastus* :

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Open bare IPv4-en IPv6-adressen maken voor load balancer
Als u toegang wilt krijgen tot uw IPv4-en IPv6-eind punten op internet, hebt u open bare IPv4-en IPv6-adressen voor de load balancer nodig. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). In het volgende voor beeld worden het IPv4-en IPv6-open bare IP-adres gemaakt met de naam *dsPublicIP_v4* en *dsPublicIP_v6* in de resource groep *DsResourceGroup01* :

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Open bare IP-adressen voor Vm's maken

Als u op afstand toegang wilt krijgen tot uw virtuele machines op internet, hebt u open bare IPv4-IP-adressen voor de virtuele machines nodig. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>Een Standard Load Balancer maken

In deze sectie configureert u dual front-end-IP (IPv4 en IPv6) en de back-end-adres groep voor de load balancer en maakt u vervolgens een Standard Load Balancer.

### <a name="create-load-balancer"></a>Load balancer maken

Maak de Standard Load Balancer met [AZ Network lb Create](/cli/azure/network/lb) met de naam **dsLB** die een front-end-groep met de naam **dsLbFrontEnd_v4** bevat, een back-end-groep met de naam **dsLbBackEndPool_v4** die is gekoppeld aan het open bare IPv4-IP-adres **dsPublicIP_v4** dat u in de vorige stap hebt gemaakt. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6-front-end maken

Maak een IPV6-frontend-IP met [AZ Network lb frontend-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create). In het volgende voor beeld wordt een front-end-IP-configuratie met de naam *dsLbFrontEnd_v6* gemaakt en wordt het *dsPublicIP_v6* adres gekoppeld:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Adres groep voor IPv6-back-end configureren

Maak een IPv6-back-end-adres groep met [AZ Network lb address-pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create). In het volgende voor beeld wordt een back-end-adres groep met de naam *dsLbBackEndPool_v6*  gemaakt, met inbegrip van Vm's met IPv6-NIC-configuraties:

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

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) om een load balancer-regel te maken. In het volgende voor beeld worden load balancer-regels met de naam *dsLBrule_v4* en *dsLBrule_v6* en wordt het verkeer op *TCP* -poort *80* gebalanceerd naar de IPv4-en IPv6-front-end IP-configuraties:

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
Voordat u enkele Vm's implementeert, moet u ondersteunende netwerk bronnen maken-beschikbaarheidsset, netwerk beveiligings groep, virtueel netwerk en virtuele Nic's. 
### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Als u de beschik baarheid van uw app wilt verbeteren, plaatst u uw virtuele machines in een beschikbaarheidsset.

Maak een beschikbaarheidsset met behulp van [az vm availability-set create](/cli/azure/vm/availability-set). In het volgende voor beeld wordt een beschikbaarheidsset gemaakt met de naam *dsAVset*:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken

Maak een netwerk beveiligings groep voor de regels die van toepassing zijn op binnenkomende en uitgaande communicatie in uw VNet.

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Een netwerk beveiligings groep maken met [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Een regel voor de netwerk beveiligings groep maken voor binnenkomende en uitgaande verbindingen

Maak een regel voor de netwerk beveiligings groep om RDP-verbindingen toe te staan via poort 3389, Internet verbinding via poort 80 en voor uitgaande verbindingen met [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

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

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *dsVNET* met subnetten *dsSubNET_v4* en *dsSubNET_v6*:

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

Maak virtuele Nic's voor elke VM met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). In het volgende voor beeld wordt een virtuele NIC gemaakt voor elke VM. Elke NIC heeft twee IP-configuraties (1 IPv4-configuratie, 1 IPv6-configuratie). U maakt de IPV6-configuratie met [AZ Network NIC IP-config Create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create).
 
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

Maak de virtuele machines met [AZ VM Create](/cli/azure/vm#az-vm-create). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan. 

Maak de *dsVM0* van de virtuele machine als volgt:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Maak de *dsVM1* van de virtuele machine als volgt:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtueel IPv6-netwerk met dubbele stack in Azure Portal weer geven
U kunt het virtuele IPv6-netwerk met dubbele stack als volgt weer geven in Azure Portal:
1. Voer in de zoek balk van de portal *dsVnet* in.
2. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit. Hiermee opent u de **overzichts** pagina van het virtuele netwerk met dubbele stack met de naam *dsVnet*. Het virtuele netwerk met dubbele stack toont de twee Nic's met zowel IPv4-als IPv6-configuraties die zich bevinden in het dubbele stack-subnet met de naam *dsSubnet*.

  ![Virtueel IPv6-netwerk met dubbele stack in azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Standard Load Balancer met een dubbele frontend-IP-configuratie (IPv4 en IPv6) gemaakt. U hebt ook een twee virtuele machines gemaakt die Nic's bevatten met dubbele IP-configuraties (IPV4 + IPv6) die zijn toegevoegd aan de back-end-pool van de load balancer. Zie [Wat is IPv6 voor azure Virtual Network?](ipv6-overview.md) voor meer informatie over IPv6-ondersteuning in azure Virtual Networks.
