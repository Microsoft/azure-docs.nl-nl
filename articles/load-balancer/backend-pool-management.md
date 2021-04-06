---
title: Beheer van back-endpools
titleSuffix: Azure Load Balancer
description: Ga aan de slag met het configureren en beheren van de back-endpool van een Azure Load Balancer
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: c49a721a4db758965c9cf8d71f5d73b5754b6088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654472"
---
# <a name="backend-pool-management"></a>Beheer van back-endpools
De back-endpool is een essentieel onderdeel van de load balancer. Met de back-endpool wordt de groep resources gedefinieerd die het verkeer verwerken voor een bepaalde taakverdelingsregel.

Er zijn twee manieren om een back-endpool te configureren:
* NIC (netwerkinterfacekaart)
* Combinatie van IP-adres en resource-id van VNET (virtueel netwerk)

Configureer uw back-endpool met NIC wanneer u gebruikmaakt van bestaande virtuele machines en virtuele-machineschaalsets. Met deze methode bouwt u de meest directe koppeling tussen uw resource en de back-endpool. 

Wanneer u uw back-endpool vooraf wilt toewijzen met een IP-adresbereik waarvoor u later virtuele machines en virtuele-machineschaalsets wilt maken, moet u uw back-endpool configureren met een combinatie van IP-adres en VNET-id.

U kunt op IP gebaseerde en op NIC gebaseerde back-endservers configureren voor dezelfde load balancer u kunt echter geen enkele back-end-groep maken die is gericht op NIC-en IP-adressen in dezelfde groep.

De configuratiesecties in dit artikel zijn gericht op:

* Azure PowerShell
* Azure CLI
* REST-API
* Azure Resource Manager-sjablonen 

Deze secties bieden inzicht in hoe de back-endpools voor elke configuratieoptie zijn gestructureerd.

## <a name="configuring-backend-pool-by-nic"></a>Back-endpool configureren met NIC
De back-endpool wordt gemaakt als onderdeel van de load balancer-bewerking. De eigenschap IP-configuratie van de NIC wordt gebruikt om leden van de back-endpool toe te voegen.

De volgende voorbeelden zijn gericht op de bewerkingen voor het maken en vullen van de back-endpool om de bijbehorende werkstroom en relatie te markeren.

  >[!NOTE] 
  >Het is belangrijk te weten dat back-endpools die zijn geconfigureerd via de netwerkinterface, niet kunnen worden bijgewerkt als onderdeel van een bewerking in de back-endpool. Het eventueel toevoegen of verwijderen van back-endresources moet gebeuren via de netwerkinterface van de resource.

### <a name="powershell"></a>PowerShell
Maak een nieuwe back-endpool:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Maak een nieuwe netwerkinterface en voeg deze toe aan de back-endpool:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

Haal de gegevens van de back-endpool op voor de load balancer om te bevestigen dat deze netwerkinterface is toegevoegd aan de back-endpool:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Maak een nieuwe virtuele machine en koppel de netwerkinterface om deze in de back-endpool te plaatsen:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Maak de back-endpool:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Maak een nieuwe netwerkinterface en voeg deze toe aan de back-endpool:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

Haal de back-endpool op om te bevestigen dat het IP-adres juist is toegevoegd:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Maak een nieuwe virtuele machine en koppel de netwerkinterface om deze in de back-endpool te plaatsen:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="resource-manager-template"></a>Resource Manager-sjabloon

Volg deze [quickstart voor Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) om een load balancer en virtuele machines te implementeren, en de virtuele machines toe te voegen aan de back-endpool via de netwerkinterface.

Volg deze [Snelstartgids Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-ip-configured-backend-pool) om een Load Balancer en virtuele machines te implementeren en voeg de virtuele machines toe aan de back-end-groep via IP-adres.


## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>De back-endpool configureren op basis van IP-adres en virtueel netwerk
Gebruik het IP-adres en virtueel netwerk in scenario's met vooraf gevulde back-endpools.

Alle beheer van de back-endpool wordt rechtstreeks uitgevoerd in het back-endpoolobject, zoals gemarkeerd in de onderstaande voorbeelden.

### <a name="powershell"></a>PowerShell
Maak een nieuwe back-endpool:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Werk de back-endpool bij met een nieuw IP-adres uit het bestaand virtueel netwerk:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

Haal de gegevens van de back-endpool op voor de load balancer om te bevestigen dat de back-endadressen zijn toegevoegd aan de back-endpool:

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Maak een netwerkinterface en voeg deze toe aan de back-endpool. Stel het IP-adres in op een van de back-endadressen:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Maak een VM en koppel de NIC met een IP-adres in de back-endpool:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Met CLI kunt u de back-endpool vullen via opdrachtregelparameters of via een JSON-configuratiebestand. 

De back-endpool maken en vullen via de opdrachtregelparameters:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

De back-endpool maken en vullen via het JSON-configuratiebestand:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

JSON-configuratiebestand:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Haal de gegevens van de back-endpool op voor de load balancer om te bevestigen dat de back-endadressen zijn toegevoegd aan de back-endpool:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Maak een netwerkinterface en voeg deze toe aan de back-endpool. Stel het IP-adres in op een van de back-endadressen:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Maak een VM en koppel de NIC met een IP-adres in de back-endpool:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
 
### <a name="limitations"></a>Beperkingen
Een back-end-pool die is geconfigureerd door IP-adressen heeft de volgende beperkingen:
  * Kan alleen worden gebruikt voor standaard load balancers
  * Limiet van 100 IP-adressen in de back-endpool
  * De back-endresources moeten zich in hetzelfde virtueel netwerk bevinden als de load balancer
  * Een Load Balancer met een op IP gebaseerde back-end-pool kunnen niet functioneren als Private Link-service
  * Deze functie wordt momenteel niet ondersteund in de Azure-portal
  * ACI-containers worden momenteel niet ondersteund door deze functie
  * Load balancers of services zoals Application Gateway kunnen niet worden geplaatst in de back-end-groep van de load balancer
  * Inkomende NAT-regels kunnen niet per IP-adres worden opgegeven

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u informatie gekregen over het beheer van back-endpools in Azure Load Balancer en over hoe u een back-endpool kunt configureren op basis van het IP-adres en virtuele netwerk.

Meer informatie over [Azure Load Balancer](load-balancer-overview.md).

Bekijk de [rest API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerbackendaddresspools/createorupdate) voor hosts-beheer op basis van IP.
