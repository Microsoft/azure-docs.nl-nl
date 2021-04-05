---
title: Virtuele machine met meerdere IP-adressen met behulp van Azure CLI
titlesuffix: Azure Virtual Network
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine met behulp van de Azure-opdracht regel interface (CLI).
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: allensu
ms.openlocfilehash: 0b5e8f28be9960a2bb4e1406a293ab5e03962681
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680566"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Meerdere IP-adressen toewijzen aan virtuele machines met behulp van de Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) maakt via het Azure Resource Manager-implementatie model met behulp van de Azure CLI. Er kunnen geen meerdere IP-adressen worden toegewezen aan resources die zijn gemaakt via het klassieke implementatie model. Lees het artikel over [implementatie modellen begrijpen](../azure-resource-manager/management/deployment-models.md) voor meer informatie over Azure-implementatie modellen.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Een virtuele machine met meerdere IP-adressen maken

In de volgende stappen wordt uitgelegd hoe u een voor beeld van een virtuele machine met meerdere IP-adressen maakt, zoals beschreven in het scenario. Wijzig de variabelen waarden in ' ' en de IP-adres typen, indien nodig, voor uw implementatie. 

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) als u deze nog niet hebt geïnstalleerd.
2. Maak een openbaar en persoonlijk SSH-sleutel paar voor virtuele Linux-machines door de stappen in het [maken van een open bare SSH-en privé sleutel paar voor virtuele Linux-machines](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)uit te voeren.
3. Meld u vanuit een opdracht shell aan met de opdracht `az login` en selecteer het abonnement dat u gebruikt.
4. Maak de virtuele machine door het script uit te voeren dat volgt op een Linux-of Mac-computer. Met het script maakt u een resource groep, een virtueel netwerk (VNet), één NIC met drie IP-configuraties en een virtuele machine waaraan de twee Nic's zijn gekoppeld. De NIC, het open bare IP-adres, het virtuele netwerk en de VM-resources moeten allemaal aanwezig zijn op dezelfde locatie en hetzelfde abonnement. De resources hoeven echter niet in dezelfde resource groep te bestaan, in het volgende script.

```azurecli
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Naast het maken van een virtuele machine met een NIC met 3 IP-configuraties, maakt het script het volgende:

- Een enkele Premium beheerde schijf standaard, maar u hebt andere opties voor het schijf type dat u kunt maken. Lees de [een Linux-VM maken met behulp van het Azure cli](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -artikel voor meer informatie.
- Een virtueel netwerk met één subnet en twee open bare IP-adressen. U kunt ook *bestaande* virtuele netwerken, subnetten, NIC of open bare IP-adres bronnen gebruiken. Als u wilt weten hoe u bestaande netwerk bronnen kunt gebruiken in plaats van extra resources te maken, voert u in `az vm create -h` .

Open bare IP-adressen hebben een nominale vergoeding. Lees de pagina met [prijzen voor IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) voor meer informatie over de prijzen van IP-adressen. Er is een limiet voor het aantal open bare IP-adressen dat kan worden gebruikt in een abonnement. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Nadat de VM is gemaakt, voert `az network nic show --name MyNic1 --resource-group myResourceGroup` u de opdracht in om de NIC-configuratie weer te geven. Voer de `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` in om een lijst weer te geven met de IP-configuraties die zijn gekoppeld aan de NIC.

Voeg de privé-IP-adressen toe aan het VM-besturings systeem door de stappen voor uw besturings systeem in de sectie [IP-adressen toevoegen aan een VM-besturings systeem](#os-config) van dit artikel uit te voeren.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-adressen toevoegen aan een VM

U kunt extra persoonlijke en open bare IP-adressen toevoegen aan een bestaande Azure-netwerk interface door de volgende stappen uit te voeren. De voor beelden zijn gebaseerd op het [scenario](#scenario) dat in dit artikel wordt beschreven.

1. Open een opdracht shell en voltooi de resterende stappen in deze sectie binnen één sessie. Als u Azure CLI nog niet hebt geïnstalleerd en geconfigureerd, voltooit u de stappen in het [Azure cli-installatie](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel en meldt u zich aan bij uw Azure-account met de `az-login` opdracht.

2. Voer de stappen in een van de volgende secties uit, afhankelijk van uw vereisten:

    **Een privé-IP-adres toevoegen**
    
    Als u een privé-IP-adres aan een NIC wilt toevoegen, moet u een IP-configuratie maken met behulp van de volgende opdracht. Het statische IP-adres moet een ongebruikt adres voor het subnet zijn.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Maak zoveel configuraties als u nodig hebt, met behulp van unieke configuratie namen en privé-IP-adressen (voor configuraties met vaste IP-adressen).

    **Een openbaar IP-adres toevoegen**
    
    Een openbaar IP-adres wordt toegevoegd door het te koppelen aan een nieuwe IP-configuratie of een bestaande IP-configuratie. Voer de stappen in een van de volgende secties uit, zoals u dat nodig hebt.

    Open bare IP-adressen hebben een nominale vergoeding. Lees de pagina met [prijzen voor IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) voor meer informatie over de prijzen van IP-adressen. Er is een limiet voor het aantal open bare IP-adressen dat kan worden gebruikt in een abonnement. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

    - **De resource aan een nieuwe IP-configuratie koppelen**
    
        Wanneer u een openbaar IP-adres toevoegt aan een nieuwe IP-configuratie, moet u ook een persoonlijk IP-adres toevoegen, omdat alle IP-configuraties een privé-IP-adres moeten hebben. U kunt een bestaande resource met een openbaar IP-adres toevoegen of een nieuwe maken. Als u een nieuw account wilt maken, voert u de volgende opdracht in:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Voer de volgende opdracht in om een nieuwe IP-configuratie met een statisch privé-IP-adres en de bijbehorende open bare IP-adres bron *myPublicIP3* te maken:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **De resource koppelen aan een bestaande IP-configuratie** Een resource met een openbaar IP-adres kan alleen worden gekoppeld aan een IP-configuratie waaraan nog geen is gekoppeld. U kunt bepalen of een IP-configuratie een bijbehorend openbaar IP-adres heeft door de volgende opdracht in te voeren:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Geretourneerde uitvoer:
    
        ```output
        Name        PublicIpAddressId
        
        ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3
        ```

        Omdat de kolom **PublicIpAddressId** voor *IpConfig-3* leeg is in de uitvoer, is er momenteel geen open bare IP-adres resource aan gekoppeld. U kunt een bestaand openbaar IP-adres toevoegen aan IpConfig-3 of de volgende opdracht invoeren om een resource te maken:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Voer de volgende opdracht in om de open bare IP-adres resource aan de bestaande IP-configuratie met de naam *ipconfig-3* te koppelen:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Bekijk de privé-IP-adressen en de resource-Id's van het open bare IP-adres die zijn toegewezen aan de NIC door de volgende opdracht in te voeren:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Geretourneerde uitvoer: <br>

    ```output
    Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
    
    ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```
    
4. Voeg de privé-IP-adressen die u aan de NIC hebt toegevoegd toe aan het VM-besturings systeem door de instructies in de sectie [IP-adressen toevoegen aan een VM-besturings systeem](#os-config) van dit artikel te volgen. Voeg de open bare IP-adressen niet toe aan het besturings systeem.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]