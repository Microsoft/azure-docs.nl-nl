---
title: CLI gebruiken voor het implementeren van Azure Spot Virtual Machines
description: Meer informatie over het gebruik van de CLI voor het implementeren van Azure Spot Virtual Machines om kosten te besparen.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 90ad35757834c14abdffb017ff31b3296074ca24
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802434"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure-Spot Virtual Machines implementeren met behulp van Azure CLI

Met behulp van [Azure Spot Virtual Machines](../spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur Azure Spot Virtual Machines. Daarom zijn Azure Spot Virtual Machines geweldig voor workloads die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

Prijzen voor Azure Spot Virtual Machines is variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 

U hebt de mogelijkheid om een maximum prijs voor de virtuele machine in te stellen die u wilt betalen, per uur. U kunt de maximale prijs voor een virtuele machine van Azure spot instellen in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765` is bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de VM is de huidige prijs voor de virtuele Azure-machine of de prijs voor een standaard-VM, die ooit minder is, zolang er capaciteit en quota beschikbaar zijn. Zie [Azure Spot Virtual Machines-prijzen](../spot-vms.md#pricing)voor meer informatie over het instellen van de maximum prijs.

Het proces voor het maken van een virtuele Azure-machine met behulp van de Azure CLI is hetzelfde als die in het [artikel Quick](./quick-create-cli.md)start. U hoeft alleen de para meter---Priority spot toe te voegen, de `--eviction-policy` toewijzing in te stellen op ofwel ongedaan maken (dit is de standaard instelling) of `Delete` , en een maximum prijs te geven of `-1` . 


## <a name="install-azure-cli"></a>Azure CLI installeren

Als u Azure Spot Virtual Machines wilt maken, moet u de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer **AZ--version** uit om de versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

Meld u aan bij Azure met [AZ login](/cli/azure/reference-index#az-login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Een Azure spot-virtuele machine maken

In dit voor beeld ziet u hoe u een virtuele Linux-locatie van Azure implementeert die niet wordt verwijderd op basis van de prijs. Het verwijderings beleid is ingesteld om de toewijzing van de virtuele machine ongedaan te maken, zodat deze op een later tijdstip opnieuw kan worden opgestart. Als u de virtuele machine en de onderliggende schijf wilt verwijderen wanneer de virtuele machine wordt verwijderd, stelt u `--eviction-policy` in op `Delete` .

```azurecli-interactive
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

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Een verwijdering simuleren

U kunt een virtuele machine van Azure spot simuleren met behulp van REST, Power shell of de CLI om te testen hoe goed uw toepassing reageert op een plotselinge verwijdering.

In de meeste gevallen moet u de REST API [virtual machines simuleren](/rest/api/compute/virtualmachines/simulateeviction) gebruiken om te helpen bij het automatisch testen van toepassingen. Voor REST, een `Response Code: 204` betekent dat de gesimuleerde verwijdering is geslaagd. U kunt gesimuleerde verwijderingen combi neren met de [geplande gebeurtenis service](scheduled-events.md), om te automatiseren hoe uw app reageert wanneer de virtuele machine wordt verwijderd.

Als u geplande gebeurtenissen in actie wilt zien, kunt u [Azure vrijdag bekijken-azure Scheduled Events gebruiken om het onderhoud van vm's voor te bereiden](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Snelle test

Voor een snelle test om te laten zien hoe een gesimuleerde verwijdering werkt, gaat u verder met het uitvoeren van query's op de geplande gebeurtenis service om te zien hoe deze eruitziet wanneer u een verwijdering simuleert met behulp van de Azure CLI.

De geplande gebeurtenis service is ingeschakeld voor uw service, de eerste keer dat u een aanvraag voor gebeurtenissen doet. 

Extern in uw virtuele machine en open vervolgens een opdracht prompt. 

Typ het volgende vanaf de opdracht prompt op uw virtuele machine:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Dit eerste antwoord kan Maxi maal twee minuten duren. Vanaf nu wordt de uitvoer bijna onmiddellijk weer gegeven.

Op een computer waarop de Azure CLI is geïnstalleerd (zoals uw lokale machine), simuleert u een verwijdering met [AZ VM simuleren-verwijderen](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Vervang de naam van de resource groep en de virtuele machine door uw eigen naam. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Als de aanvraag is uitgevoerd, is de reactie-uitvoer `Status: Succeeded` .

Ga snel terug naar uw externe verbinding met de virtuele spot machine en zoek het Scheduled Events-eind punt opnieuw op. Herhaal de volgende opdracht totdat u een uitvoer krijgt die meer informatie bevat:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Wanneer de geplande gebeurtenis service het verwijderings bericht ontvangt, krijgt u een antwoord dat er ongeveer als volgt uitziet:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

U ziet dat `"EventType":"Preempt"` en de resource de VM-resource is `"Resources":["myspotvm"]` . 

U kunt ook zien wanneer de virtuele machine wordt verwijderd door te controleren of de `"NotBefore"` virtuele machine niet vóór de opgegeven tijd wordt verwijderd, zodat het venster voor uw toepassing op een correcte manier kan worden afgesloten.


## <a name="next-steps"></a>Volgende stappen

U kunt ook een virtuele Azure-machine maken met behulp van [Azure PowerShell](../windows/spot-powershell.md), [Portal](../spot-portal.md)of een [sjabloon](spot-template.md).

Vraag actuele prijs informatie op met behulp van de [Azure retail-prijs-API](/rest/api/cost-management/retail-prices/azure-retail-prices) voor informatie over de virtuele machine van Azure spot. De `meterName` en `skuName` zijn beide opgenomen `Spot` .

Als er een fout optreedt, raadpleegt u [fout codes](../error-codes-spot.md).
