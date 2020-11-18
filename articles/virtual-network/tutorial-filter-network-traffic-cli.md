---
title: Netwerk verkeer filteren-Azure CLI | Microsoft Docs
description: In dit artikel leert u hoe u netwerk verkeer kunt filteren op een subnet, met een netwerk beveiligings groep, met behulp van de Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7589b13dc517a23e8d9d65907fb3342e4e2490a9
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734339"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Netwerk verkeer filteren met een netwerk beveiligings groep met behulp van de Azure CLI

U kunt het netwerkverkeer inkomend in en uitgaand naar een subnet van een virtueel netwerk filteren met een netwerkbeveiligingsgroep. Netwerkbeveiligingsgroepen bevatten beveiligingsregels die netwerkverkeer filteren op IP-adres, poort en protocol. Beveiligingsregels worden toegepast op resources die zijn geïmplementeerd in een subnet. In dit artikel leert u het volgende:

* Een netwerkbeveiligingsgroep en beveiligingsregels maken
* Een virtueel netwerk maken en een netwerkbeveiligingsgroep koppelen aan een subnet
* Virtuele machines (VM) implementeren in een subnet
* Verkeersfilters testen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Een netwerkbeveiligingsgroep bevat beveiligingsregels. Beveiligingsregels geven een bron en doel op. Bronnen en doelen kunnen toepassingsbeveiligingsgroepen zijn.

### <a name="create-application-security-groups"></a>Toepassingsbeveiligingsgroepen maken

Maak eerst een resource groep voor alle resources die in dit artikel zijn gemaakt met [AZ Group Create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam gemaakt op de locatie *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Maak een toepassings beveiligings groep met [AZ Network ASG Create](/cli/azure/network/asg). Met een toepassingsbeveiligingsgroep kunt u servers met vergelijkbare poortfiltervereisten groeperen. In het volgende voorbeeld worden twee toepassingsbeveiligingsgroepen gemaakt.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerk beveiligings groep met [AZ Network NSG Create](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNsg* gemaakt: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Beveiligingsregels maken

Maak een beveiligings regel met [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). In het volgende voorbeeld wordt een regel gemaakt die inkomend verkeer van internet naar de toepassingsbeveiligingsgroep *myWebServers* toestaat via de poorten 80 en 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

In het volgende voor beeld wordt een regel gemaakt waarmee binnenkomend verkeer van Internet naar de *myMgmtServers* -toepassings beveiligings groep via poort 22 wordt toegestaan:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

In dit artikel wordt SSH (poort 22) blootgesteld aan Internet voor de *myAsgMgmtServers* -VM. Voor productie omgevingen, in plaats van poort 22 op internet weer te geven, is het raadzaam om verbinding te maken met Azure-resources die u wilt beheren met een [VPN-](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [particuliere](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) netwerk verbinding.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork* gemaakt:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Voeg een subnet toe aan een virtueel netwerk met [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet). In het volgende voorbeeld wordt een subnet met de naam *mySubnet* toegevoegd aan het virtuele netwerk en wordt de netwerkbeveiligingsgroep *myNsg* hieraan gekoppeld:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee virtuele machines in het virtuele netwerk, zodat u het filteren van verkeer in een latere stap kunt controleren. 

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een virtuele machine gemaakt die als een webserver fungeert. De `--asgs myAsgWebServers` optie zorgt ervoor dat Azure de netwerk interface maakt die wordt gemaakt voor de virtuele machine lid is van de *myAsgWebServers* -toepassings beveiligings groep.

De `--nsg ""` optie is opgegeven om te voor komen dat Azure een standaard netwerk beveiligings groep maakt voor de netwerk interface die Azure maakt wanneer de virtuele machine wordt gemaakt. Er wordt een wacht woord gebruikt om dit artikel te stroom lijnen. Sleutels worden doorgaans gebruikt in productie-implementaties. Als u sleutels gebruikt, moet u ook het door sturen van SSH-agents configureren voor de resterende stappen. Zie de documentatie voor uw SSH-client voor meer informatie. Vervang `<replace-with-your-password>` in de volgende opdracht door een wacht woord van uw keuze.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, wordt de uitvoer vergelijkbaar met het volgende voor beeld geretourneerd: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Let op het **openbare IP-adres**. Dit adres wordt gebruikt voor toegang tot de virtuele machine via internet in een latere stap.  Maak een VM die fungeert als een beheerserver:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, noteert u de **publicIpAddress** in de geretourneerde uitvoer. Dit adres wordt gebruikt voor toegang tot de virtuele machine in de volgende stap. Ga pas verder met de volgende stap als Azure klaar is met het maken van de virtuele machine.

## <a name="test-traffic-filters"></a>Verkeersfilters testen

Gebruik de volgende opdracht om een SSH-sessie te maken met de *VM myvmmgmt* -VM. Vervang door *\<publicIpAddress>* het open bare IP-adres van uw virtuele machine. In het bovenstaande voor beeld is het IP-adres *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Wanneer u wordt gevraagd om een wacht woord, voert u het wacht woord in dat u hebt opgegeven in [Vm's maken](#create-virtual-machines).

De verbinding is geslaagd, omdat poort 22 wordt toegestaan van het internet naar de *myAsgMgmtServers* -toepassings beveiligings groep waaraan de netwerk interface in de *VM myvmmgmt* -VM is gekoppeld.

Gebruik de volgende opdracht voor het SSHen van de *VM myvmweb* -VM vanaf de *VM myvmmgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

De verbinding slaagt omdat een standaardbeveiligingsregel binnen elke netwerkbeveiligingsgroep verkeer via alle poorten tussen alle IP-adressen binnen een virtueel netwerk toestaat. U kunt geen SSH-verbinding maken met de *VM myvmweb* -VM via internet omdat de beveiligings regel voor de *myAsgWebServers* geen poort 22 van het Internet toestaat.

Gebruik de volgende opdrachten om de nginx-webserver te installeren op de *VM myvmweb* -VM:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

De *VM myvmweb* -VM is uitgaand naar Internet om nginx op te halen omdat al het uitgaande verkeer naar Internet wordt toegestaan door een standaard beveiligings regel. Sluit de *VM myvmweb* SSH-sessie, waarmee u op de `username@myVmMgmt:~$` vraag van de *VM myvmmgmt* -VM kunt blijven. Voer de volgende opdracht in om het welkomst scherm van nginx op te halen van de *VM myvmweb* -VM:

```bash
curl myVmWeb
```

Afmelden van de *VM myvmmgmt* -VM. Voer *myVmWeb* `curl <publicIpAddress>` vanaf uw eigen computer in om te controleren of u toegang hebt tot de VM myvmweb-webserver vanaf een andere locatie dan Azure. De verbinding is geslaagd, omdat poort 80 is toegestaan van het internet naar de *myAsgWebServers* -toepassings beveiligings groep waaraan de netwerk interface is gekoppeld met de *VM myvmweb* -VM.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik [AZ Group delete](/cli/azure/group) om de resource groep en alle resources die deze bevat te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een netwerk beveiligings groep gemaakt en gekoppeld aan een subnet van een virtueel netwerk. Zie [Overzicht van netwerkbeveiligingsgroepen](security-overview.md) en [Een beveiligingsgroep beheren](manage-network-security-group.md) voor meer informatie over netwerkbeveiligingsgroepen.

Azure routeert standaard verkeer tussen subnetten. In plaats daarvan kunt u verkeer routeren tussen subnetten via een virtuele machine, die bijvoorbeeld als een firewall fungeert. Zie [een route tabel maken](tutorial-create-route-table-cli.md)voor meer informatie.
