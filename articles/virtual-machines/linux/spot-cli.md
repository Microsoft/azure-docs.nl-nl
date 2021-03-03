---
title: CLI gebruiken voor het implementeren van Azure Spot Virtual Machines
description: Meer informatie over het gebruik van de CLI voor het implementeren van Azure Spot Virtual Machines om kosten te besparen.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: f1ad1dff695755e88881773bdcbebc2da283b75d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669377"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure-Spot Virtual Machines implementeren met behulp van Azure CLI

Met behulp van [Azure Spot Virtual Machines](../spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur Azure Spot Virtual Machines. Daarom zijn Azure Spot Virtual Machines geweldig voor workloads die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

Prijzen voor Azure Spot Virtual Machines is variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 

U hebt de mogelijkheid om een maximum prijs voor de virtuele machine in te stellen die u wilt betalen, per uur. U kunt de maximale prijs voor een virtuele machine van Azure spot instellen in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765` is bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de VM is de huidige prijs voor de virtuele Azure-machine of de prijs voor een standaard-VM, die ooit minder is, zolang er capaciteit en quota beschikbaar zijn. Zie [Azure Spot Virtual Machines-prijzen](../spot-vms.md#pricing)voor meer informatie over het instellen van de maximum prijs.

Het proces voor het maken van een virtuele Azure-machine met behulp van de Azure CLI is hetzelfde als die in het [artikel Quick](./quick-create-cli.md)start. U hoeft alleen de para meter---Priority spot toe te voegen, de `--eviction-policy` toewijzing in te stellen op ofwel ongedaan maken (dit is de standaard instelling) of `Delete` , en een maximum prijs te geven of `-1` . 


## <a name="install-azure-cli"></a>Azure CLI installeren

Als u Azure Spot Virtual Machines wilt maken, moet u de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer **AZ--version** uit om de versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

Meld u aan bij Azure met [AZ login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Een Azure spot-virtuele machine maken

In dit voor beeld ziet u hoe u een virtuele Linux-locatie van Azure implementeert die niet wordt verwijderd op basis van de prijs. Het verwijderings beleid is ingesteld om de toewijzing van de virtuele machine ongedaan te maken, zodat deze op een later tijdstip opnieuw kan worden opgestart. Als u de virtuele machine en de onderliggende schijf wilt verwijderen wanneer de virtuele machine wordt verwijderd, stelt u `--eviction-policy` in op `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Nadat de VM is gemaakt, kunt u een query uitvoeren om de maximale facturerings prijs voor alle virtuele machines in de resource groep weer te geven.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Een verwijdering simuleren

U kunt een virtuele machine van Azure spot [simuleren](/rest/api/compute/virtualmachines/simulateeviction) om te testen hoe goed uw toepassing terugvalt op een plotselinge verwijdering. 

Vervang het volgende door uw gegevens: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Volgende stappen**

U kunt ook een virtuele Azure-machine maken met behulp van [Azure PowerShell](../windows/spot-powershell.md), [Portal](../spot-portal.md)of een [sjabloon](spot-template.md).

Vraag actuele prijs informatie op met behulp van de [Azure retail-prijs-API](/rest/api/cost-management/retail-prices/azure-retail-prices) voor informatie over de virtuele machine van Azure spot. De `meterName` en `skuName` zijn beide opgenomen `Spot` .

Als er een fout optreedt, raadpleegt u [fout codes](../error-codes-spot.md).