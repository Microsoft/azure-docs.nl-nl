---
title: Een Linux-omgeving maken met de Azure CLI
description: Maak opslag, een virtuele Linux-machine, een virtueel netwerk en een subnet, een load balancer, een NIC, een openbaar IP-adres en een netwerk beveiligings groep, helemaal vanaf het begin met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: e8c21df3b783241a3a4bfdbe28b28b2bceb89b4d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509112"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Een volledige virtuele Linux-machine maken met de Azure CLI
Als u snel een virtuele machine (VM) in azure wilt maken, kunt u één Azure CLI-opdracht gebruiken die standaard waarden gebruikt voor het maken van vereiste ondersteunende resources. Resources, zoals een virtueel netwerk, een openbaar IP-adres en een netwerk beveiligings groep, worden automatisch gemaakt. Als u meer controle hebt over uw omgeving in productie gebruik, kunt u deze resources van tevoren maken en vervolgens uw Vm's toevoegen. Dit artikel helpt u bij het maken van een virtuele machine en elk van de ondersteunende bronnen één voor één.

Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account in met [AZ login](/cli/azure/reference-index).

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myVnet* en *myVM*.

## <a name="create-resource-group"></a>Een resourcegroep maken
Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Er moet een resource groep worden gemaakt vóór een virtuele machine en de ondersteunende bronnen voor het virtuele netwerk. Maak de resource groep met [AZ Group Create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

De uitvoer van Azure CLI-opdrachten bevindt zich standaard in JSON (JavaScript Object Notation). Als u de standaard uitvoer wilt wijzigen in een lijst of tabel, gebruikt u bijvoorbeeld [AZ Configure--Output](/cli/azure/reference-index). U kunt ook toevoegen `--output` aan elke opdracht voor eenmalige wijziging in uitvoer indeling. In het volgende voor beeld ziet u de JSON-uitvoer van de `az group create` opdracht:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken
Vervolgens maakt u een virtueel netwerk in Azure en een subnet in waarop u uw virtuele machines kunt maken. Gebruik [AZ Network vnet Create](/cli/azure/network/vnet) om een virtueel netwerk met de naam *myVnet* te maken met het adres voorvoegsel *192.168.0.0/16* . U voegt ook een subnet met de naam *mySubnet* toe met het adres voorvoegsel van *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

In de uitvoer ziet u dat het subnet logisch is gemaakt in het virtuele netwerk:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken
We gaan nu een openbaar IP-adres maken met [AZ Network Public-IP Create](/cli/azure/network/public-ip). Met dit open bare IP-adres kunt u via Internet verbinding maken met uw virtuele machines. Omdat het standaard adres dynamisch is, maakt u een DNS-vermelding met de naam met de `--domain-name-label` para meter. In het volgende voor beeld wordt een openbaar IP-adres met de naam *myPublicIP* gemaakt met de DNS- *mypublicdns*. Omdat de DNS-naam uniek moet zijn, geeft u uw eigen unieke DNS-naam op:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Uitvoer:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken
Als u de stroom van verkeer in en uit uw Vm's wilt beheren, past u een netwerk beveiligings groep toe op een virtuele NIC of subnet. In het volgende voor beeld wordt [AZ Network NSG Create](/cli/azure/network/nsg) gebruikt voor het maken van een netwerk beveiligings groep met de naam *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

U definieert regels die specifiek verkeer toestaan of weigeren. Als u binnenkomende verbindingen op poort 22 wilt toestaan (om SSH-toegang in te scha kelen), maakt u een regel voor binnenkomend verkeer met [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). In het volgende voor beeld wordt een regel gemaakt met de naam *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Als u binnenkomende verbindingen wilt toestaan op poort 80 (voor webverkeer), voegt u nog een regel voor de netwerk beveiligings groep toe. In het volgende voor beeld wordt een regel gemaakt met de naam *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Controleer de netwerk beveiligings groep en-regels met [AZ Network NSG show](/cli/azure/network/nsg):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Uitvoer:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Een virtuele NIC maken
Virtuele netwerk interface kaarten (Nic's) zijn programmatisch beschikbaar omdat u regels kunt Toep assen op hun gebruik. Afhankelijk van de grootte van de [virtuele machine](../sizes.md)kunt u meerdere virtuele nic's koppelen aan een virtuele machine. In de volgende [AZ Network NIC Create](/cli/azure/network/nic) opdracht maakt u een NIC met de naam *myNic* en koppelt u deze aan uw netwerk beveiligings groep. Het open bare IP-adres *myPublicIP* is ook gekoppeld aan de virtuele NIC.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Uitvoer:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Met beschikbaarheids sets kunnen uw Vm's worden verdeeld over fout domeinen en update domeinen. Hoewel u nu slechts één VM maakt, is het best practice om beschikbaarheids sets te gebruiken om het in de toekomst gemakkelijker te maken om het uit te breiden. 

Fout domeinen definiëren een groepering van virtuele machines die een gemeen schappelijke voedings bron en netwerk switch delen. Standaard worden de virtuele machines die in uw beschikbaarheidsset zijn geconfigureerd, gescheiden in Maxi maal drie fout domeinen. Een hardwareprobleem in een van deze fout domeinen heeft geen invloed op elke virtuele machine waarop uw app wordt uitgevoerd.

Met update domeinen worden groepen virtuele machines en onderliggende fysieke hardware aangegeven die op hetzelfde moment opnieuw kunnen worden opgestart. Tijdens gepland onderhoud is de volg orde waarin update domeinen opnieuw worden opgestart mogelijk niet sequentieel, maar er wordt slechts één update domein tegelijk opnieuw opgestart.

Azure distribueert automatisch Vm's over de fout-en update domeinen wanneer ze in een beschikbaarheidsset worden geplaatst. Zie [de beschik baarheid van virtuele machines beheren](../availability.md)voor meer informatie.

Maak een beschikbaarheidsset voor uw virtuele machine met [AZ VM Availability-set Create](/cli/azure/vm/availability-set). In het volgende voorbeeld wordt een beschikbaarheidsset met de naam *myAvailabilitySet* gemaakt:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

De uitvoer notities fout domeinen en update domeinen:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Een virtuele machine maken
U hebt de netwerk bronnen gemaakt ter ondersteuning van virtuele machines die toegankelijk zijn via internet. Maak nu een VM en beveilig deze met een SSH-sleutel. In dit voor beeld maken we een Ubuntu-VM op basis van de meest recente LTS. U kunt aanvullende installatie kopieën vinden met [AZ VM Image List](/cli/azure/vm/image), zoals beschreven in [Azure VM-installatie kopieën zoeken](cli-ps-findimage.md).

Geef een SSH-sleutel op die moet worden gebruikt voor verificatie. Als u geen open bare SSH-sleutel paar hebt, kunt u [deze maken](mac-create-ssh-keys.md) of de `--generate-ssh-keys` para meter gebruiken om ze voor u te maken. Als u al een sleutel paar hebt, gebruikt deze para meter bestaande sleutels in `~/.ssh` .

Maak de virtuele machine door alle resources en informatie samen met de opdracht [AZ VM Create](/cli/azure/vm) op te halen. In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH naar uw virtuele machine met de DNS-vermelding die u hebt opgegeven tijdens het maken van het open bare IP-adres. Dit `fqdn` wordt weer gegeven in de uitvoer bij het maken van de virtuele machine:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Uitvoer:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

U kunt NGINX installeren en de verkeers stroom naar de virtuele machine bekijken. Installeer NGINX als volgt:

```bash
sudo apt-get install -y nginx
```

Als u de standaard NGINX-site in actie wilt zien, opent u uw webbrowser en voert u uw FQDN in:

![Standaard NGINX-site op uw VM](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exporteren als een sjabloon
Wat moet u doen als u nu een extra ontwikkel omgeving met dezelfde para meters of een productie omgeving wilt maken die overeenkomt met deze. Resource Manager maakt gebruik van JSON-sjablonen waarmee alle para meters voor uw omgeving worden gedefinieerd. U bouwt volledige omgevingen door te verwijzen naar deze JSON-sjabloon. U kunt [JSON-sjablonen hand matig maken](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) of een bestaande omgeving exporteren om de JSON-sjabloon voor u te maken. Gebruik [AZ Group export](/cli/azure/group) om de resource groep als volgt te exporteren:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Met deze opdracht maakt `myResourceGroup.json` u het bestand in de huidige werkmap. Wanneer u een omgeving maakt op basis van deze sjabloon, wordt u gevraagd om alle resource namen. U kunt deze namen invullen in het sjabloon bestand door de `--include-parameter-default-value` para meter toe te voegen aan de `az group export` opdracht. Bewerk de JSON-sjabloon om de resource namen op te geven of [Maak een parameters.jsvoor het bestand](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) waarin de resource namen worden opgegeven.

Als u een omgeving wilt maken op basis van uw sjabloon, gebruikt u [AZ Deployment Group Create](/cli/azure/deployment/group) als volgt:

```azurecli
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

[Meer informatie over hoe u kunt implementeren vanuit sjablonen](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json). Meer informatie over het stapsgewijs bijwerken van omgevingen, het gebruiken van het parameter bestand en het openen van sjablonen vanaf één opslag locatie.

## <a name="next-steps"></a>Volgende stappen
Nu bent u klaar om te gaan werken met meerdere netwerk onderdelen en Vm's. U kunt deze voorbeeld omgeving gebruiken om uw toepassing samen te stellen met behulp van de kern onderdelen die hier worden geïntroduceerd.