---
title: Een linux-VM maken in Azure met meerdere NIC's
description: Meer informatie over het maken van een linux-VM met meerdere NIC's die eraan zijn gekoppeld met behulp van de Azure CLI of Resource Manager sjablonen.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b08e8ebbba3ba91c1c1aa0f135c4cba37ba038b1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769909"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Een virtuele Linux-machine maken in Azure met meerdere netwerkinterfacekaarten


In dit artikel wordt beschreven hoe u een VM met meerdere NIC's maakt met de Azure CLI.

## <a name="create-supporting-resources"></a>Ondersteunende resources maken
Installeer de nieuwste [Versie van Azure CLI](/cli/azure/install-az-cli2) en meld u aan bij een Azure-account met az [login](/cli/azure/reference-index).

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeeld van parameternamen *zijn myResourceGroup,* *mystorageaccount* en *myVM.*

Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Maak het virtuele netwerk met [az network vnet create.](/cli/azure/network/vnet) In het volgende voorbeeld wordt een virtueel netwerk met de *naam myVnet en* het subnet *mySubnetFrontEnd gemaakt:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Maak een subnet voor het back-endverkeer met [az network vnet subnet create.](/cli/azure/network/vnet/subnet) In het volgende voorbeeld wordt een subnet met de *naam mySubnetBackEnd gemaakt:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Maak een netwerkbeveiligingsgroep met [az network nsg create.](/cli/azure/network/nsg) In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup* gemaakt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Meerdere NIC's maken en configureren
Maak twee NIC's [met az network nic create.](/cli/azure/network/nic) In het volgende voorbeeld worden twee NIC's met de naam *myNic1* en *myNic2* gemaakt, die de netwerkbeveiligingsgroep hebben verbonden met één NIC die verbinding maakt met elk subnet:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Een VM maken en de NIC's koppelen
Wanneer u de VM maakt, geeft u de NIC's op die u met hebt `--nics` gemaakt. U moet ook goed op uw hoede zijn wanneer u de VM-grootte selecteert. Er zijn limieten voor het totale aantal NIC's dat u aan een VM kunt toevoegen. Meer informatie over [VM-grootten voor Linux.](../sizes.md)

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Voeg routeringstabellen toe aan het gast-besturingssysteem door de stappen in [Het gast-besturingssysteem](#configure-guest-os-for-multiple-nics)voor meerdere NIC's configureren uit te voeren.

## <a name="add-a-nic-to-a-vm"></a>Een NIC toevoegen aan een VM
In de vorige stappen is een VM met meerdere NIC's gemaakt. U kunt ook NIC's toevoegen aan een bestaande VM met de Azure CLI. Verschillende [VM-grootten ondersteunen](../sizes.md) een verschillend aantal NIC's, dus de grootte van uw VM dienovereenkomstig. Indien nodig kunt u de [VM-grootten.](change-vm-size.md)

Maak nog een NIC met [az network nic create](/cli/azure/network/nic). In het volgende voorbeeld wordt een NIC met de *naam myNic3* gemaakt die is verbonden met het back-endsubnet en de netwerkbeveiligingsgroep die in de vorige stappen zijn gemaakt:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Als u een NIC wilt toevoegen aan een bestaande VM, moet u eerst de toewijzing van de VM met [az vm deallocate herstellen.](/cli/azure/vm) In het volgende voorbeeld wordt de toewijzing van de VM met de *naam myVM terugvervolgd:*


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Voeg de NIC toe met [az vm nic add.](/cli/azure/vm/nic) In het volgende voorbeeld wordt *myNic3 toegevoegd* *aan myVM:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Start de VM met [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Voeg routeringstabellen toe aan het gast-besturingssysteem door de stappen in [Het gast-besturingssysteem](#configure-guest-os-for-multiple-nics)voor meerdere NIC's configureren uit te voeren.

## <a name="remove-a-nic-from-a-vm"></a>Een NIC verwijderen uit een VM
Als u een NIC van een bestaande VM wilt verwijderen, moet u eerst de toewijzing van de VM verwijderen [met az vm deallocate](/cli/azure/vm). In het volgende voorbeeld wordt de toewijzing van de VM met de *naam myVM terugvervolgd:*

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Verwijder de NIC met [az vm nic remove.](/cli/azure/vm/nic) In het volgende voorbeeld wordt *myNic3 uit* *myVM verwijderd:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Start de VM met [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Meerdere NIC's maken met behulp Resource Manager sjablonen
Azure Resource Manager gebruiken declaratieve JSON-bestanden om uw omgeving te definiëren. U kunt een overzicht [van de Azure Resource Manager.](../../azure-resource-manager/management/overview.md) Resource Manager sjablonen bieden een manier om meerdere exemplaren van een resource te maken tijdens de implementatie, zoals het maken van meerdere NIC's. U gebruikt *kopiëren om* het aantal exemplaren op te geven dat moet worden gemaakt:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Lees meer over het [maken van meerdere exemplaren met behulp van *copy*](../../azure-resource-manager/templates/copy-resources.md). 

U kunt ook een gebruiken om vervolgens een getal toe te appen aan een resourcenaam, waarmee u `copyIndex()` , , enzovoort kunt `myNic1` `myNic2` maken. Hieronder ziet u een voorbeeld van het toevoegen van de indexwaarde:

```json
"name": "[concat('myNic', copyIndex())]", 
```

U kunt een volledig voorbeeld lezen van het maken [van meerdere NIC's met behulp Resource Manager sjablonen.](../../virtual-network/template-samples.md)

Voeg routeringstabellen toe aan het gast-besturingssysteem door de stappen in [Het gast-besturingssysteem](#configure-guest-os-for-multiple-nics)voor meerdere NIC's configureren uit te voeren.

## <a name="configure-guest-os-for-multiple-nics"></a>Gast-besturingssysteem configureren voor meerdere NIC's

In de vorige stappen zijn een virtueel netwerk en subnet gemaakt, NIC's gekoppeld en vervolgens een virtuele machine gemaakt. Er zijn geen regels voor openbare IP-adressen en netwerkbeveiligingsgroepsregels gemaakt die SSH-verkeer toestaan. Als u het gast-besturingssysteem voor meerdere NIC's wilt configureren, moet u externe verbindingen toestaan en opdrachten lokaal uitvoeren op de virtueleM.

Als u SSH-verkeer wilt toestaan, maakt u als volgt een netwerkbeveiligingsgroepsregel [met az network nsg rule create:](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Maak een openbaar IP-adres [met az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) en wijs dit toe aan de eerste NIC met az network [nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Als u het openbare IP-adres van de VM wilt weergeven, gebruikt [u az vm show](/cli/azure/vm#az_vm_show) als volgt::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Ga nu met SSH naar het openbare IP-adres van uw VM. De standaard gebruikersnaam die in een vorige stap is opgegeven, is *azureuser*. Geef uw eigen gebruikersnaam en openbaar IP-adres op:

```bash
ssh azureuser@137.117.58.232
```

Als u wilt verzenden naar of van een secundaire netwerkinterface, moet u handmatig permanente routes toevoegen aan het besturingssysteem voor elke secundaire netwerkinterface. In dit artikel is *eth1 de* secundaire interface. Instructies voor het toevoegen van permanente routes aan het besturingssysteem variëren per distributie. Zie de documentatie voor uw distributie voor instructies.

Wanneer u de route toevoegt aan het besturingssysteem, is het gatewayadres *.1* voor welk subnet de netwerkinterface zich ook in. Als aan de netwerkinterface bijvoorbeeld het adres *10.0.2.4* is toegewezen, is de gateway die u voor de route opgeeft *10.0.2.1.* U kunt een specifiek netwerk definiëren voor de bestemming van de route of een bestemming *van 0.0.0.0 opgeven* als u wilt dat al het verkeer voor de interface via de opgegeven gateway gaat. De gateway voor elk subnet wordt beheerd door het virtuele netwerk.

Nadat u de route voor een secundaire interface hebt toegevoegd, controleert u of de route zich in uw routetabel `route -n` met . De volgende voorbeelduitvoer is voor de routetabel met de twee netwerkinterfaces die in dit artikel aan de VM zijn toegevoegd:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Controleer of de route die u hebt toegevoegd, blijft behouden tijdens het opnieuw opstarten door de routetabel opnieuw te controleren nadat de route opnieuw is opgestart. Als u de connectiviteit wilt testen, kunt u de volgende opdracht invoeren, bijvoorbeeld waarbij *eth1* de naam is van een secundaire netwerkinterface:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Volgende stappen
Controleer [de grootte van linux-VM's](../sizes.md) bij het maken van een VM met meerdere NIC's. Let op het maximum aantal NIC's dat door elke VM-grootte wordt ondersteund.

Gebruik Just-In-Time-VM-toegang om uw VM's verder te beveiligen. Met deze functie worden regels voor netwerkbeveiligingsgroep geopend voor SSH-verkeer wanneer dat nodig is, en voor een bepaalde periode. Zie [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (VM-toegang beheren met behulp van JIT) voor meer informatie.