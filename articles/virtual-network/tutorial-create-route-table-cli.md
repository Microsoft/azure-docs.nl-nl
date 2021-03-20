---
title: Netwerk verkeer routeren-Azure CLI | Microsoft Docs
description: In dit artikel wordt beschreven hoe u netwerk verkeer via de Azure CLI kunt routeren met een route tabel.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 043d450a1b968174ad263579d39de06a296a98e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94741481"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Netwerk verkeer routeren met een route tabel met behulp van de Azure CLI

Azure routeert standaard automatisch verkeer tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. De mogelijkheid voor het maken van aangepaste routes is handig als u bijvoorbeeld verkeer tussen subnetten wilt routeren via een NVA (virtueel netwerkapparaat). In dit artikel leert u het volgende:

* Een routetabel maken
* Een route maken
* Een virtueel netwerk met meerdere subnetten maken
* Een routetabel aan een subnet koppelen
* Een NVA maken voor het routeren van verkeer
* Virtuele machines (VM's) implementeren in verschillende subnetten
* Verkeer van het ene subnet naar het andere leiden via een NVA

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-route-table"></a>Een routetabel maken

Voordat u een route tabel kunt maken, maakt u een resource groep met [AZ Group Create](/cli/azure/group) voor alle resources die in dit artikel zijn gemaakt. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Maak een route tabel met [AZ Network Route-Table Create](/cli/azure/network/route-table#az-network-route-table-create). In het volgende voor beeld wordt een route tabel gemaakt met de naam *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Een route maken

Maak een route in de route tabel met [AZ Network Route-Table Route Create](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

Voordat u een routetabel aan een subnet kunt koppelen, moet u een virtueel netwerk en subnet maken. Maak een virtueel netwerk met één subnet met [AZ Network vnet Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Maak twee extra subnetten met [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Koppel de *myRouteTablePublic* -route tabel aan het *open bare* subnet met [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Een NVA maken

Een NVA is een VM die een netwerkfunctie uitvoert, zoals routering, firewall of WAN-optimalisatie.

Maak een NVA in het *DMZ* -subnet met [AZ VM Create](/cli/azure/vm). Wanneer u een virtuele machine maakt, maakt Azure standaard een openbaar IP-adres en wijst deze toe aan de virtuele machine. De `--public-ip-address ""` para meter zorgt ervoor dat Azure geen openbaar IP-adres maakt en toewijst aan de virtuele machine omdat de virtuele machine niet via internet verbonden hoeft te zijn. Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet verder met de volgende stap totdat Azure klaar is met het maken van de VM en het retour neren van uitvoer over de virtuele machine. 

Een netwerkinterface kan alleen netwerkverkeer doorsturen dat niet voor zijn eigen IP-adres bestemd is, als doorsturen via IP voor de netwerkinterface is ingeschakeld. Schakel door sturen via IP in voor de netwerk interface met [AZ Network NIC update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

In de VM moet het besturingssysteem of een toepassing die wordt uitgevoerd op de virtuele machine, ook netwerkverkeer kunnen doorsturen. Door sturen via IP inschakelen in het besturings systeem van de virtuele machine met [AZ VM extension set](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Het kan een minuut duren voordat de opdracht is uitgevoerd.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee Vm's in het virtuele netwerk, zodat u kunt valideren dat verkeer van het *open bare* subnet via de NVA in een latere stap wordt doorgestuurd naar het *privé* -subnet. 

Maak een virtuele machine in het *open bare* subnet met [AZ VM Create](/cli/azure/vm). Met de `--no-wait` para meter kan Azure de opdracht op de achtergrond uitvoeren, zodat u kunt door gaan met de volgende opdracht. Er wordt een wacht woord gebruikt om dit artikel te stroom lijnen. Sleutels worden doorgaans gebruikt in productie-implementaties. Als u sleutels gebruikt, moet u ook het door sturen van SSH-agent configureren. Zie de documentatie voor uw SSH-client voor meer informatie. Vervang `<replace-with-your-password>` in de volgende opdracht door een wacht woord van uw keuze.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Maak een virtuele machine in het *privé* -subnet.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, toont de Azure CLI informatie die lijkt op het volgende voor beeld: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Let op het **openbare IP-adres**. Dit adres wordt gebruikt voor toegang tot de virtuele machine via internet in een latere stap.

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

Gebruik de volgende opdracht om een SSH-sessie te maken met de *VM myvmprivate* -VM. Vervang door *\<publicIpAddress>* het open bare IP-adres van uw virtuele machine. In het bovenstaande voor beeld is het IP-adres *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Wanneer u wordt gevraagd om een wacht woord, voert u het wacht woord in dat u hebt geselecteerd in [virtuele machines maken](#create-virtual-machines).

Gebruik de volgende opdracht om tracerings route te installeren op de *VM myvmprivate* -VM:

```bash
sudo apt-get install traceroute
```

Gebruik de volgende opdracht om de route ring van het netwerk verkeer naar de *VM myvmpublic* -VM van de *VM myvmprivate* -VM te testen.

```bash
traceroute myVmPublic
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

U ziet dat verkeer rechtstreeks vanuit *myVmPrivate* naar *myVmPublic* wordt geleid. De standaard routes van Azure routeren het verkeer rechtstreeks tussen subnetten. 

Gebruik de volgende opdracht voor het SSHen van de *VM myvmpublic* -VM vanaf de *VM myvmprivate* VM:

```bash
ssh azureuser@myVmPublic
```

Gebruik de volgende opdracht om tracerings route te installeren op de *VM myvmpublic* -VM:

```bash
sudo apt-get install traceroute
```

Gebruik de volgende opdracht om de route ring van het netwerk verkeer naar de *VM myvmprivate* -VM van de *VM myvmpublic* -VM te testen.

```bash
traceroute myVmPrivate
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

U ziet dat de eerste hop 10.0.2.4 is, het privé IP-adres van het NVA. De tweede hop is 10.0.1.4, het privé- IP-adres van de VM *myVmPrivate*. Door de route die is toegevoegd aan de routetabel *myRouteTablePublic* en gekoppeld aan het *Openbare* subnet leidt Azure het verkeer via het NVA in plaats van rechtstreeks naar het *Privé*-subnet.

Sluit de SSH-sessies op de *VM myvmpublic* -en *VM myvmprivate* -vm's.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik [AZ Group delete](/cli/azure/group) om de resource groep en alle resources die deze bevat te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een route tabel gemaakt en gekoppeld aan een subnet. U hebt een eenvoudig NVA gemaakt dat verkeer van een openbaar subnet naar een privé-subnet heeft geleid. U kunt allerlei vooraf geconfigureerde NVA's die netwerkfuncties uitvoeren zoals firewalls en WAN-optimalisatie, implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen resources voor sommige Azure PaaS-diensten niet in een virtueel netwerk worden geïmplementeerd. U kunt de toegang tot de resources van sommige Azure PaaS-diensten echter nog steeds beperken tot alleen verkeer vanaf een subnet van een virtueel netwerk. Zie [netwerk toegang tot PaaS-resources beperken](tutorial-restrict-network-access-to-resources-cli.md)voor meer informatie.
