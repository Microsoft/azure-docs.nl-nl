---
title: Een virtuele Linux-machine in azure maken met meerdere Nic's
description: Meer informatie over het maken van een virtuele Linux-machine waaraan meerdere Nic's zijn gekoppeld met behulp van de Azure CLI-of Resource Manager-sjablonen.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: c0eea74890665297a0d450c8afd0a5d60dd1ae00
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551807"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Een virtuele Linux-machine in azure maken met meerdere netwerk interface kaarten


In dit artikel wordt beschreven hoe u een virtuele machine met meerdere Nic's maakt met de Azure CLI.

## <a name="create-supporting-resources"></a>Ondersteunende resources maken
Installeer de nieuwste [Azure cli](/cli/azure/install-az-cli2) en meld u aan bij een Azure-account met de opdracht [AZ login](/cli/azure/reference-index).

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *mystorageaccount* en *myVM*.

Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Maak het virtuele netwerk met [AZ Network vnet Create](/cli/azure/network/vnet). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *myVnet* en het subnet met de naam *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Maak een subnet voor het back-end-verkeer met [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet). In het volgende voor beeld wordt een subnet met de naam *mySubnetBackEnd* gemaakt:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Maak een netwerk beveiligings groep met [AZ Network NSG Create](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup* gemaakt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Meerdere Nic's maken en configureren
Maak twee Nic's met [AZ Network NIC Create](/cli/azure/network/nic). In het volgende voor beeld worden twee Nic's gemaakt met de naam *myNic1* en *myNic2*, verbonden met de netwerk beveiligings groep, met één NIC die verbinding maakt met elk subnet:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Een virtuele machine maken en de Nic's koppelen
Wanneer u de virtuele machine maakt, geeft u de Nic's op waarmee u hebt gemaakt `--nics` . U moet er ook voor zorgen dat u de VM-grootte selecteert. Er zijn limieten voor het totale aantal Nic's dat u aan een virtuele machine kunt toevoegen. Meer informatie over de [grootten van Linux VM](../sizes.md).

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

Voeg routerings tabellen toe aan het gast besturingssysteem door de stappen in [het gast besturingssysteem configureren voor meerdere nic's](#configure-guest-os-for-multiple-nics)uit te voeren.

## <a name="add-a-nic-to-a-vm"></a>Een NIC toevoegen aan een VM
In de vorige stappen is een virtuele machine met meerdere Nic's gemaakt. U kunt ook Nic's toevoegen aan een bestaande virtuele machine met de Azure CLI. Verschillende [VM-grootten](../sizes.md) ondersteunen een variërend aantal nic's, dus grootte van uw virtuele machine dienovereenkomstig. Indien nodig kunt u [de grootte van een virtuele machine wijzigen](change-vm-size.md).

Maak een andere NIC met [AZ Network NIC Create](/cli/azure/network/nic). In het volgende voor beeld wordt een NIC gemaakt met de naam *myNic3* die is verbonden met het back-end-subnet en de netwerk beveiligings groep die in de vorige stappen is gemaakt:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Als u een NIC aan een bestaande virtuele machine wilt toevoegen, moet u eerst de toewijzing van de VM ongedaan maken met [AZ VM deallocate](/cli/azure/vm). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam *myVM* ongedaan gemaakt:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Voeg de NIC toe met [AZ VM NIC add](/cli/azure/vm/nic). In het volgende voor beeld wordt *myNic3* toegevoegd aan *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Start de VM met [AZ VM start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Voeg routerings tabellen toe aan het gast besturingssysteem door de stappen in [het gast besturingssysteem configureren voor meerdere nic's](#configure-guest-os-for-multiple-nics)uit te voeren.

## <a name="remove-a-nic-from-a-vm"></a>Een NIC verwijderen uit een VM
Als u een NIC van een bestaande virtuele machine wilt verwijderen, moet u eerst de toewijzing van de VM ongedaan maken met [AZ VM deallocate](/cli/azure/vm). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam *myVM* ongedaan gemaakt:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Verwijder de NIC met [AZ VM NIC Remove](/cli/azure/vm/nic). In het volgende voor beeld wordt *myNic3* verwijderd uit *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Start de VM met [AZ VM start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Meerdere Nic's maken met behulp van Resource Manager-sjablonen
Azure Resource Manager sjablonen gebruiken declaratieve JSON-bestanden om uw omgeving te definiëren. U kunt een [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)lezen. Resource Manager-sjablonen bieden een manier om meerdere exemplaren van een resource te maken tijdens de implementatie, zoals het maken van meerdere Nic's. U gebruikt *kopiëren* om het aantal te maken exemplaren op te geven:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Meer informatie over [het maken van meerdere exemplaren met behulp van *kopiëren*](../../azure-resource-manager/templates/copy-resources.md). 

U kunt ook een gebruiken `copyIndex()` om een nummer toe te voegen aan een resource naam, zodat u `myNic1` ,, enzovoort, kunt maken `myNic2` . Hieronder ziet u een voor beeld van het toevoegen van de index waarde:

```json
"name": "[concat('myNic', copyIndex())]", 
```

U kunt een volledig voor beeld lezen van [het maken van meerdere nic's met behulp van Resource Manager-sjablonen](../../virtual-network/template-samples.md).

Voeg routerings tabellen toe aan het gast besturingssysteem door de stappen in [het gast besturingssysteem configureren voor meerdere nic's](#configure-guest-os-for-multiple-nics)uit te voeren.

## <a name="configure-guest-os-for-multiple-nics"></a>Gast besturingssysteem voor meerdere Nic's configureren

In de vorige stappen hebt u een virtueel netwerk en subnet aangesloten Nic's gemaakt en vervolgens een virtuele machine gemaakt. Een openbaar IP-adres en netwerk beveiligings groep regels waarmee SSH-verkeer is toegestaan, zijn niet gemaakt. Als u het gast besturingssysteem voor meerdere Nic's wilt configureren, moet u externe verbindingen toestaan en opdrachten lokaal uitvoeren op de virtuele machine.

Als u SSH-verkeer wilt toestaan, maakt u een regel voor een netwerk beveiligings groep met [AZ Network NSG regel Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) als volgt:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Maak een openbaar IP-adres met [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) en wijs dit toe aan de eerste NIC met [AZ Network NIC IP-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Gebruik [AZ VM show](/cli/azure/vm#az-vm-show) als volgt om het open bare IP-adres van de virtuele machine weer te geven::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

SSH nu naar het open bare IP-adres van uw virtuele machine. De standaard gebruikersnaam die in een vorige stap is gegeven, was *azureuser*. Geef uw eigen gebruikers naam en openbaar IP-adres op:

```bash
ssh azureuser@137.117.58.232
```

Als u wilt verzenden naar of van een secundaire netwerk interface, moet u hand matig permanente routes toevoegen aan het besturings systeem voor elke secundaire netwerk interface. In dit artikel is *eth1* de secundaire interface. Instructies voor het toevoegen van permanente routes aan het besturings systeem variëren per distributie. Raadpleeg de documentatie voor uw distributie voor instructies.

Bij het toevoegen van de route naar het besturings systeem is het gateway adres *1 voor het* subnet waarin de netwerk interface zich bevindt. Als de netwerk interface bijvoorbeeld het adres *10.0.2.4* toegewezen, is de gateway die u opgeeft voor de route *10.0.2.1*. U kunt een specifiek netwerk definiëren voor de bestemming van de route of een bestemming van *0.0.0.0* opgeven als u wilt dat al het verkeer voor de interface via de opgegeven gateway gaat. De gateway voor elk subnet wordt beheerd door het virtuele netwerk.

Wanneer u de route voor een secundaire interface hebt toegevoegd, controleert u of de route in de route tabel is opgenomen `route -n` . De volgende voorbeeld uitvoer is voor de route tabel met de twee netwerk interfaces die zijn toegevoegd aan de virtuele machine in dit artikel:

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

Controleer of de route die u hebt toegevoegd, aanwezig blijft tijdens het opnieuw opstarten door de route tabel opnieuw te controleren nadat de computer opnieuw is opgestart. Als u de connectiviteit wilt testen, kunt u de volgende opdracht invoeren, bijvoorbeeld waarbij *eth1* de naam is van een secundaire netwerk interface:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Volgende stappen
Controleer de [grootte van Linux VM](../sizes.md) bij het maken van een virtuele machine met meerdere nic's. Let op het maximum aantal Nic's dat elke VM-grootte ondersteunt.

Gebruik just-in-time-VM-toegang om uw Vm's verder te beveiligen. Met deze functie worden de regels voor de netwerk beveiligings groep voor SSH-verkeer geopend wanneer dit nodig is, en gedurende een bepaalde periode. Zie [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (VM-toegang beheren met behulp van JIT) voor meer informatie.