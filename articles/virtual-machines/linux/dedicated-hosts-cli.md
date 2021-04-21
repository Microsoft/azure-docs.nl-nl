---
title: VM's en schaalset-exemplaren implementeren op toegewezen hosts met behulp van de CLI
description: Implementeer VM's en schaalset-exemplaren op toegewezen hosts met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: adc09bf2572be563ff52cf9fa3d0dea51263d032
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774409"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Implementeren op toegewezen hosts met behulp van de Azure CLI
 

Dit artikel begeleidt u bij het maken van een toegewezen [Azure-host voor](../dedicated-hosts.md) het hosten van uw virtuele machines (VM's). 

Zorg ervoor dat u Azure CLI versie 2.16.0 of hoger hebt geïnstalleerd en aangemeld bij een Azure-account met behulp van `az login` . 


## <a name="limitations"></a>Beperkingen

- De grootten en hardwaretypen die beschikbaar zijn voor toegewezen hosts variëren per regio. Raadpleeg de pagina met [hostprijzen voor](https://aka.ms/ADHPricing) meer informatie.

## <a name="create-resource-group"></a>Een resourcegroep maken 
Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak de resourcegroep met az group create. In het volgende voorbeeld wordt een resourcegroep met de *naam myDHResourceGroup gemaakt* op de *locatie VS -* oost.

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Lijst met beschikbare host-SKU's in een regio

Niet alle host-SKU's zijn beschikbaar in alle regio's en beschikbaarheidszones. 

Vermeld de beschikbaarheid van de host en eventuele aanbiedingsbeperkingen voordat u begint met het inrichten van toegewezen hosts. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Een hostgroep maken 

Een **hostgroep** is een resource die een verzameling toegewezen hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheidszone en voegt er hosts aan toe. Bij het plannen van hoge beschikbaarheid zijn er aanvullende opties. U kunt een of beide van de volgende opties gebruiken met uw toegewezen hosts: 
- Meerdere beschikbaarheidszones overspannen. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Overspannen meerdere foutdomeinen die zijn toe te staan aan fysieke rekken. 
 
In beide gevallen moet u het aantal foutdomeinen voor uw hostgroep verstrekken. Als u geen foutdomeinen in uw groep wilt omspannen, gebruikt u het aantal foutdomeinen van 1. 

U kunt ook besluiten om zowel beschikbaarheidszones als foutdomeinen te gebruiken. 


In dit voorbeeld gebruiken we [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) om een hostgroep te maken met zowel beschikbaarheidszones als foutdomeinen. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Voeg de `--automatic-placement true` parameter toe om uw VM's en schaalset-exemplaren automatisch op hosts in een hostgroep te plaatsen. Zie Handmatige [versus automatische plaatsing voor meer informatie. ](../dedicated-hosts.md#manual-vs-automatic-placement)


### <a name="other-examples"></a>Andere voorbeelden

U kunt ook [az vm host group create gebruiken om](/cli/azure/vm/host/group#az_vm_host_group_create) een hostgroep te maken in beschikbaarheidszone 1 (en geen foutdomeinen).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Hieronder wordt [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) gebruikt om een hostgroep te maken met behulp van alleen foutdomeinen (te gebruiken in regio's waar beschikbaarheidszones niet worden ondersteund). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Een host maken 

We gaan nu een toegewezen host maken in de hostgroep. Naast een naam voor de host moet u de SKU voor de host verstrekken. Host-SKU legt de ondersteunde VM-serie vast, evenals de hardwaregeneratie voor uw toegewezen host.  

Zie prijzen voor Azure Dedicated Host host-SKU's [en prijzen.](https://aka.ms/ADHPricing)

Gebruik [az vm host create om](/cli/azure/vm/host#az_vm_host_create) een host te maken. Als u het aantal foutdomeinen voor uw hostgroep in stelt, wordt u gevraagd om het foutdomein voor uw host op te geven.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Een virtuele machine maken 
Maak een virtuele machine binnen een toegewezen host met [az vm create.](/cli/azure/vm#az_vm_create) Als u een beschikbaarheidszone hebt opgegeven bij het maken van uw hostgroep, moet u dezelfde zone gebruiken bij het maken van de virtuele machine.

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

Als u de VM op een specifieke host wilt plaatsen, gebruikt u in plaats `--host` van de hostgroep op te geven met `--host-group` .
 
> [!WARNING]
> Als u een virtuele machine maakt op een host die onvoldoende resources heeft, wordt de virtuele machine gemaakt met de status MISLUKT. 

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

Als u handmatig wilt kiezen op welke host de schaalset moet worden geïmplementeerd, voegt u en `--host` de naam van de host toe.


## <a name="check-the-status-of-the-host"></a>De status van de host controleren

U kunt de status van de host controleren en met az [vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view)controleren hoeveel virtuele machines u nog steeds op de host kunt implementeren.

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 De uitvoer ziet er ongeveer als de volgende uit:
 
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
 
## <a name="export-as-a-template"></a>Exporteren als sjabloon 
U kunt een sjabloon exporteren als u nu een extra ontwikkelomgeving wilt maken met dezelfde parameters of een productieomgeving die er overeenkomt. Resource Manager JSON-sjablonen gebruikt die alle parameters voor uw omgeving definiëren. U bouwt volledige omgevingen uit door te verwijzen naar deze JSON-sjabloon. U kunt JSON-sjablonen handmatig bouwen of een bestaande omgeving exporteren om de JSON-sjabloon voor u te maken. Gebruik [az group export om](/cli/azure/group#az_group_export) uw resourcegroep te exporteren.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Met deze opdracht maakt `myDHResourceGroup.json` u het bestand in uw huidige werkmap. Wanneer u een omgeving maakt op basis van deze sjabloon, wordt u gevraagd om alle resourcenamen. U kunt deze namen in uw sjabloonbestand in vullen door de `--include-parameter-default-value` parameter toe te voegen aan de opdracht `az group export` . Bewerk uw JSON-sjabloon om de resourcenamen op te geven of maak een parameters.jsbestand waarin de resourcenamen worden opgegeven.
 
Gebruik az deployment group create om een omgeving te maken op basis van [uw sjabloon.](/cli/azure/deployment/group#az_deployment_group_create)

```azurecli-interactive
az deployment group create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Opschonen 

Er worden kosten in rekening gebracht voor uw toegewezen hosts, zelfs wanneer er geen virtuele machines zijn geïmplementeerd. Verwijder alle hosts die u momenteel niet gebruikt om kosten te besparen.  

U kunt een host alleen verwijderen wanneer er geen virtuele machines meer zijn die deze gebruiken. Verwijder de VM's [met az vm delete.](/cli/azure/vm#az_vm_delete)

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Nadat u de VM's hebt verwijderd, kunt u de host verwijderen met [az vm host delete.](/cli/azure/vm/host#az_vm_host_delete)

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Nadat u al uw hosts hebt verwijderd, kunt u de hostgroep verwijderen met [az vm host group delete.](/cli/azure/vm/host/group#az_vm_host_group_delete)  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
U kunt ook de hele resourcegroep verwijderen met één opdracht. Hiermee verwijdert u alle resources die in de groep zijn gemaakt, met inbegrip van alle VM's, hosts en hostgroepen.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Volgende stappen

- Zie overzicht van [Toegewezen hosts](../dedicated-hosts.md) voor meer informatie.

- U kunt ook toegewezen hosts maken met behulp van [Azure Portal](../dedicated-hosts-portal.md).

- Hier vindt u [](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)een voorbeeldsjabloon, die zowel zones als foutdomeinen gebruikt voor maximale tolerantie in een regio.
