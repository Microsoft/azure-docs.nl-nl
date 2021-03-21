---
title: Een VM in de zone maken met Azure CLI
description: Een virtuele machine maken in een beschikbaarheids zone met de Azure CLI
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: 3405468c64a7130bb0d71543403dd4ce5ef9c241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558369"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-cli"></a>Een virtuele machine maken in een beschikbaarheids zone met behulp van Azure CLI

In dit artikel wordt stapsgewijs beschreven hoe u de Azure CLI gebruikt om een virtuele Linux-machine te maken in een Azure-beschikbaarheids zone. Een [beschikbaarheidszone](../../availability-zones/az-overview.md) is een fysiek afgescheiden zone in een Azure-regio. Gebruik beschikbaarheidszones om uw apps en gegevens te beschermen tegen het onwaarschijnlijke risico van een storing of het verloren gaan van een heel datacenter.

Als u een beschikbaarheidszone wilt gebruiken, maakt u een virtuele machine in een [ondersteunde Azure-regio](../../availability-zones/az-region.md).

Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en bent aangemeld bij een Azure-account met [AZ login](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Beschikbaarheid van VM-SKU controleren
De beschikbaarheid van VM-grootten, of SKU's, kan verschillen per regio en zone. Als hulp bij de planning voor het gebruik van de beschikbaarheidszones kunt u een overzicht maken van de beschikbare VM-SKU's per Azure-regio en zone. Hiermee zorgt u ervoor dat u een correcte VM-grootte kiest en de gewenste tolerantie in zones verkrijgt. Voor meer informatie over de verschillende VM-typen en -grootten raadpleegt u het [Overzicht van de VM-grootten](../sizes.md).

U kunt de beschik bare VM-Sku's weer geven met de opdracht [AZ VM List-sku's](/cli/azure/vm) . Met het volgende voorbeeld wordt een lijst weergegeven van de beschikbare VM-SKU's in de regio *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

De uitvoer is vergelijkbaar met het volgende verkorte voorbeeld, waarin de beschikbaarheidszones worden weergegeven waarin elke VM-grootte beschikbaar is:

```output
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group).  

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt. In dit voor beeld wordt een resource groep met de naam *myResourceGroupVM* gemaakt in de regio *eastus2* . VS-Oost 2 is een van de Azure-regio's die beschikbaarheids zones ondersteunt.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

De resource groep wordt opgegeven bij het maken of wijzigen van een virtuele machine, die in dit artikel kan worden weer gegeven.

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een virtuele machine met de opdracht [az vm create](/cli/azure/vm). 

Wanneer u een virtuele machine maakt, zijn er diverse opties beschikbaar zoals besturingssysteeminstallatiekopie, schijfgrootte en beheerdersreferenties. In dit voorbeeld wordt een virtuele machine gemaakt met de naam *myVM* waarop Ubuntu-server wordt uitgevoerd. De virtuele machine wordt gemaakt in beschikbaarheids zone *1*. De virtuele machine wordt standaard gemaakt in de *Standard_DS1_v2* grootte.

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Het maken van de virtuele machine kan een paar minuten duren. Wanneer de virtuele machine is gemaakt, biedt de Azure CLI informatie over de virtuele machine. Noteer de `zones` waarde die de beschikbaarheids zone aangeeft waarin de virtuele machine wordt uitgevoerd. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Zone voor beheerde schijf en IP-adres bevestigen

Wanneer de virtuele machine in een beschikbaarheids zone is geïmplementeerd, wordt een beheerde schijf voor de virtuele machine in dezelfde beschikbaarheids zone gemaakt. Een openbaar IP-adres wordt standaard ook in die zone gemaakt. In de volgende voor beelden wordt informatie over deze resources weer gegeven.

Als u wilt controleren of de beheerde schijf van de virtuele machine zich in de beschikbaarheids zone bevindt, gebruikt u de opdracht [AZ VM show](/cli/azure/vm) om de schijf-id te retour neren. In dit voor beeld wordt de schijf-ID opgeslagen in een variabele die in een latere stap wordt gebruikt. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
U kunt nu informatie over de beheerde schijf ophalen:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

In de uitvoer ziet u dat de beheerde schijf zich in dezelfde beschikbaarheidszone bevindt als de virtuele machine:

```output
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Gebruik de opdracht [AZ VM List-IP-addresses](/cli/azure/vm) om de naam van de open bare IP-adres bron te retour neren in *myVM*. In dit voor beeld wordt de naam opgeslagen in een variabele die in een latere stap wordt gebruikt.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Nu kunt u informatie ophalen over het IP-adres:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

In de uitvoer ziet u dat het IP-adres zich in dezelfde beschikbaarheids zone bevindt als de virtuele machine:

```output
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een VM maakt in een beschikbaarheidszone. Meer informatie over [Beschik baarheid](../availability.md) voor virtuele Azure-machines.