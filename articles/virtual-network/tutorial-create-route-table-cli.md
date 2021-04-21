---
title: Netwerkverkeer routeer - Azure CLI-| Microsoft Docs
description: In dit artikel leert u hoe u netwerkverkeer routeert met een routetabel met behulp van de Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ff643c39820fa529c8678c7a36881dd25da354c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762493"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Netwerkverkeer routeer met een routetabel met behulp van de Azure CLI

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

Voordat u een routetabel kunt maken, maakt u een resourcegroep [met az group create voor](/cli/azure/group) alle resources die in dit artikel zijn gemaakt. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Maak een routetabel [met az network route-table create](/cli/azure/network/route-table#az_network_route_table_create). In het volgende voorbeeld wordt een routetabel met de *naam myRouteTablePublic gemaakt.* 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Een route maken

Maak een route in de routetabel met [az network route-table route create.](/cli/azure/network/route-table/route#az_network_route_table_route_create) 

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

Voordat u een routetabel aan een subnet kunt koppelen, moet u een virtueel netwerk en subnet maken. Maak een virtueel netwerk met één subnet met [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Maak twee extra subnetten met [az network vnet subnet create.](/cli/azure/network/vnet/subnet)

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

Koppel de *routetabel myRouteTablePublic* aan het *openbare* subnet met [az network vnet subnet update](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Een NVA maken

Een NVA is een VM die een netwerkfunctie uitvoert, zoals routering, firewall of WAN-optimalisatie.

Maak een NVA in het *DMZ-subnet* [met az vm create](/cli/azure/vm). Wanneer u een VM maakt, maakt en wijst Azure standaard een openbaar IP-adres toe aan de VM. De parameter geeft Azure de instructie om geen openbaar IP-adres te maken en toe te wijzen aan de VM, omdat de VM niet via `--public-ip-address ""` internet verbonden hoeft te zijn. Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

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

Het maken van de virtuele machine duurt een paar minuten. Ga pas verder met de volgende stap als Azure klaar is met het maken van de VM en uitvoer retourneert over de VM. 

Een netwerkinterface kan alleen netwerkverkeer doorsturen dat niet voor zijn eigen IP-adres bestemd is, als doorsturen via IP voor de netwerkinterface is ingeschakeld. Doorsturen via IP inschakelen voor de netwerkinterface [met az network nic update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

In de VM moet het besturingssysteem of een toepassing die wordt uitgevoerd op de virtuele machine, ook netwerkverkeer kunnen doorsturen. Schakel doorsturen via IP in het besturingssysteem van de VM in [met az vm extension set](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Het uitvoeren van de opdracht kan tot een minuut duren.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee virtuele machines in het virtuele netwerk, zodat u kunt valideren dat verkeer van het openbare *subnet* in een latere stap via de NVA wordt doorgeleid naar het privésubnet.  

Maak een virtuele machine in het *openbare* subnet [met az vm create](/cli/azure/vm). Met `--no-wait` de parameter kan Azure de opdracht op de achtergrond uitvoeren, zodat u verder kunt gaan met de volgende opdracht. Voor het stroomlijnen van dit artikel wordt een wachtwoord gebruikt. Sleutels worden doorgaans gebruikt in productie-implementaties. Als u sleutels gebruikt, moet u ook het doorsturen van de SSH-agent configureren. Zie de documentatie voor uw SSH-client voor meer informatie. Vervang `<replace-with-your-password>` in de volgende opdracht door een wachtwoord van uw keuze.

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

Maak een virtuele  machine in het privésubnet.

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

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, toont de Azure CLI soortgelijke informatie als in het volgende voorbeeld: 

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

Let op het **openbare IP-adres**. Dit adres wordt in een latere stap gebruikt voor toegang tot de VM via internet.

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

Gebruik de volgende opdracht om een SSH-sessie te maken met de *VM myVmPrivate.* Vervang *\<publicIpAddress>* door het openbare IP-adres van uw VM. In het bovenstaande voorbeeld is het IP-adres *13.90.242.231.*

```bash
ssh azureuser@<publicIpAddress>
```

Wanneer u wordt gevraagd om een wachtwoord, voert u het wachtwoord in dat u hebt geselecteerd in [Virtuele machines maken.](#create-virtual-machines)

Gebruik de volgende opdracht om traceerroute te installeren op *de VM myVmPrivate:*

```bash
sudo apt-get install traceroute
```

Gebruik de volgende opdracht om de routering te testen voor netwerkverkeer naar de *VM myVmPublic* vanaf *de VM myVmPrivate.*

```bash
traceroute myVmPublic
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

U ziet dat verkeer rechtstreeks vanuit *myVmPrivate* naar *myVmPublic* wordt geleid. Standaardroutes van Azure worden verkeer rechtstreeks tussen subnetten gerouteerd. 

Gebruik de volgende opdracht om via SSH vanaf de *VM myVmPrivate* naar de VM *myVmPublic* te gaan:

```bash
ssh azureuser@myVmPublic
```

Gebruik de volgende opdracht om traceerroute te installeren op *de VM myVmPublic:*

```bash
sudo apt-get install traceroute
```

Gebruik de volgende opdracht om de routering voor netwerkverkeer naar de *VM myVmPrivate* vanaf *de VM myVmPublic* te testen.

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

Sluit de SSH-sessies voor zowel de *VM's myVmPublic* als *myVmPrivate.*

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik az group [delete](/cli/azure/group) om de resourcegroep en alle resources die deze bevat te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een routetabel gemaakt en deze gekoppeld aan een subnet. U hebt een eenvoudig NVA gemaakt dat verkeer van een openbaar subnet naar een privé-subnet heeft geleid. U kunt allerlei vooraf geconfigureerde NVA's die netwerkfuncties uitvoeren zoals firewalls en WAN-optimalisatie, implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen resources voor sommige Azure PaaS-diensten niet in een virtueel netwerk worden geïmplementeerd. U kunt de toegang tot de resources van sommige Azure PaaS-diensten echter nog steeds beperken tot alleen verkeer vanaf een subnet van een virtueel netwerk. Zie Netwerktoegang tot [PaaS-resources beperken voor meer informatie.](tutorial-restrict-network-access-to-resources-cli.md)
