---
title: Vm's en scale set-instanties implementeren op toegewezen hosts met behulp van de CLI
description: Implementeer Vm's en scale set-instanties naar toegewezen hosts met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: d2cf78d328017f96552bd51794ac997c394d18f1
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008415"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Implementeren naar toegewezen hosts met behulp van de Azure CLI
 

Dit artikel begeleidt u bij het maken van een toegewezen Azure- [host](../dedicated-hosts.md) voor het hosten van uw virtuele machines (vm's). 

Zorg ervoor dat u Azure CLI-versie 2.0.70 of hoger hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account met `az login` . 


## <a name="limitations"></a>Beperkingen

- De grootten en typen hardware die beschikbaar zijn voor toegewezen hosts variëren per regio. Raadpleeg de pagina met [prijzen](https://aka.ms/ADHPricing) voor de host voor meer informatie.

## <a name="create-resource-group"></a>Een resourcegroep maken 
Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak de resource groep met AZ Group Create. In het volgende voor beeld wordt een resource groep met de naam *myDHResourceGroup* gemaakt op de locatie *VS-Oost* .

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Beschik bare host-Sku's in een regio weer geven

Niet alle host-Sku's zijn beschikbaar in alle regio's en beschikbaarheids zones. 

Geef de beschik baarheid van de host en eventuele beperkingen van de aanbieding weer voordat u begint met het inrichten van toegewezen hosts. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Een hostgroep maken 

Een **hostgroep** is een resource die een verzameling toegewezen hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheids zone en voegt hierop hosts toe. Bij het plannen van hoge Beschik baarheid zijn er extra opties. U kunt een of beide van de volgende opties gebruiken met uw toegewezen hosts: 
- Beschik over meerdere beschikbaarheids zones. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Over meerdere fout domeinen die zijn toegewezen aan fysieke racks. 
 
In beide gevallen moet u het aantal fout domeinen voor uw hostgroep opgeven. Als u geen fout domeinen in uw groep wilt beslaan, gebruikt u het aantal fouten domein 1. 

U kunt er ook voor kiezen om zowel beschikbaarheids zones als fout domeinen te gebruiken. 


In dit voor beeld wordt [AZ VM host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) gebruikt om een hostgroep te maken met behulp van zowel beschikbaarheids zones als fout domeinen. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Voeg de `--automatic-placement true` para meter toe om uw vm's en instanties van schaal sets automatisch op hosts in een hostgroep te plaatsen. Zie voor meer informatie [hand matig versus automatische plaatsing ](../dedicated-hosts.md#manual-vs-automatic-placement).


### <a name="other-examples"></a>Andere voorbeelden

U kunt ook [AZ VM host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) gebruiken om een hostgroep te maken in beschikbaarheids zone 1 (en geen fout domeinen).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Het volgende maakt gebruik van [AZ VM host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) om een hostgroep te maken met behulp van alleen fout domeinen (die moeten worden gebruikt in regio's waar beschikbaarheids zones niet worden ondersteund). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Een host maken 

We gaan nu een toegewezen host maken in de hostgroep. Naast een naam voor de host, moet u de SKU voor de host opgeven. Host SKU legt de ondersteunde VM-serie en de generatie van de hardware voor uw specifieke host vast.  

Zie voor meer informatie over de Sku's en prijzen van de host de [Azure dedicated host prijzen](https://aka.ms/ADHPricing).

Gebruik [AZ VM host Create](/cli/azure/vm/host#az-vm-host-create) om een host te maken. Als u het aantal fout domeinen voor uw hostgroep instelt, wordt u gevraagd om het fout domein voor uw host op te geven.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Een virtuele machine maken 
Maak een virtuele machine binnen een speciale host met behulp van [AZ VM Create](/cli/azure/vm#az-vm-create). Als u een beschikbaarheids zone hebt opgegeven bij het maken van uw hostgroep, moet u dezelfde zone gebruiken bij het maken van de virtuele machine.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Als u de virtuele machine op een specifieke host wilt plaatsen, gebruikt u `--host` in plaats van de hostgroep op te geven `--host-group` .
 
> [!WARNING]
> Als u een virtuele machine maakt op een host die onvoldoende bronnen heeft, wordt de virtuele machine gemaakt met de status mislukt. 

## <a name="create-a-scale-set"></a>Een schaalset maken 

Wanneer u een schaalset implementeert, geeft u de hostgroep op.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Als u hand matig wilt kiezen op welke host de schaalset moet worden geïmplementeerd, voegt u `--host` de naam van de host toe.


## <a name="check-the-status-of-the-host"></a>Controleer de status van de host

U kunt de status van de host controleren en het aantal virtuele machines dat u nog steeds op de host kunt implementeren met behulp van [AZ VM host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view).

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 De uitvoer ziet er ongeveer als volgt uit:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exporteren als een sjabloon 
U kunt een sjabloon exporteren als u nu een extra ontwikkel omgeving met dezelfde para meters of een productie omgeving wilt maken die overeenkomt met deze. Resource Manager maakt gebruik van JSON-sjablonen waarmee alle para meters voor uw omgeving worden gedefinieerd. U bouwt volledige omgevingen door te verwijzen naar deze JSON-sjabloon. U kunt JSON-sjablonen hand matig maken of een bestaande omgeving exporteren om de JSON-sjabloon voor u te maken. Gebruik [AZ Group export](/cli/azure/group#az-group-export) om de resource groep te exporteren.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Met deze opdracht maakt `myDHResourceGroup.json` u het bestand in de huidige werkmap. Wanneer u een omgeving maakt op basis van deze sjabloon, wordt u gevraagd om alle resource namen. U kunt deze namen invullen in het sjabloon bestand door de `--include-parameter-default-value` para meter toe te voegen aan de `az group export` opdracht. Bewerk de JSON-sjabloon om de resource namen op te geven of maak een parameters.jsvoor het bestand waarin de resource namen worden opgegeven.
 
Gebruik [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create)om een omgeving te maken op basis van uw sjabloon.

```azurecli-interactive
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Opschonen 

Er worden kosten in rekening gebracht voor uw specifieke hosts, zelfs wanneer er geen virtuele machines zijn geïmplementeerd. U moet alle hosts die u momenteel gebruikt, verwijderen om kosten te besparen.  

U kunt een host alleen verwijderen als er geen virtuele machines meer worden gebruikt. Verwijder de virtuele machines met [AZ VM delete](/cli/azure/vm#az-vm-delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Nadat u de Vm's hebt verwijderd, kunt u de host verwijderen met [AZ VM host delete](/cli/azure/vm/host#az-vm-host-delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Zodra u al uw hosts hebt verwijderd, kunt u de hostgroep verwijderen met [AZ VM host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
U kunt ook de hele resource groep verwijderen in één opdracht. Hiermee verwijdert u alle resources die zijn gemaakt in de groep, inclusief alle Vm's, hosts en hostgroepen.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie het overzicht [gespecialiseerde hosts](../dedicated-hosts.md) .

- U kunt ook toegewezen hosts maken met behulp van de [Azure Portal](../dedicated-hosts-portal.md).

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.