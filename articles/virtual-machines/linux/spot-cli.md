---
title: CLI gebruiken om Azure Spot-Virtual Machines
description: Meer informatie over het gebruik van de CLI voor het implementeren van Azure Spot Virtual Machines kosten te besparen.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789605"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure Spot Virtual Machines implementeren met behulp van de Azure CLI

Door [Azure Spot Virtual Machines](../spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen aanzienlijke kostenbesparingen. Op elk moment waarop Azure de capaciteit terug nodig heeft, zal de Azure-infrastructuur Azure Spot-Virtual Machines. Azure Spot Virtual Machines zijn daarom zeer goed voor workloads die onderbrekingen kunnen afhandelen, zoals batchverwerkingstaken, dev/test-omgevingen, grote rekenworkloads en meer.

Prijzen voor Azure Spot Virtual Machines zijn variabel, op basis van regio en SKU. Zie VM-prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en Windows voor meer [informatie.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

U hebt de mogelijkheid om een maximumprijs in te stellen die u per uur wilt betalen voor de VM. De maximumprijs voor een virtuele Spot-machine van Azure kan worden ingesteld in Amerikaanse dollars (USD), met maximaal 5 decimalen. De waarde zou bijvoorbeeld `0.98765` een maximale prijs van $ 0,98765 USD per uur zijn. Als u de maximumprijs in stelt op , wordt de VM niet op basis van `-1` de prijs onbetaald. De prijs voor de virtuele machine is de huidige prijs voor azure Spot Virtual Machine of de prijs voor een standaard-VM, die ooit lager is, zolang er capaciteit en quotum beschikbaar zijn. Zie Azure Spot Virtual Machines - [Pricing (Prijzen)](../spot-vms.md#pricing)voor meer informatie over het instellen van de maximumprijs.

Het proces voor het maken van een virtuele Azure Spot-machine met behulp van de Azure CLI is hetzelfde als beschreven in het [quickstart-artikel](./quick-create-cli.md). Voeg de parameter '--priority Spot' toe, stel de in op Toewijzing van de toewijzing (dit is de standaardinstelling) of , en `--eviction-policy` `Delete` geef een maximumprijs op of `-1` . 


## <a name="install-azure-cli"></a>Azure CLI installeren

Als u Azure Spot Virtual Machines, moet u Azure CLI versie 2.0.74 of hoger uitvoeren. Voer **az --version uit om** de versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

Meld u aan bij Azure met [az login](/cli/azure/reference-index#az_login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Een virtuele Spot-machine in Azure maken

In dit voorbeeld ziet u hoe u een virtuele Linux Azure Spot-machine implementeert die niet wordt onbezet op basis van de prijs. Het beleid voor de uitzetting is zo ingesteld dat de toewijzing van de VM wordt teruggeplaatst, zodat deze op een later tijdstip opnieuw kan worden opgestart. Als u de VM en de onderliggende schijf wilt verwijderen wanneer de VM wordt verwijderd, stelt u in `--eviction-policy` op `Delete` .

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



Nadat de VM is gemaakt, kunt u een query uitvoeren om de maximale factureringsprijs voor alle VM's in de resourcegroep te bekijken.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Een uitzetting simuleren

U kunt een azure spot-virtuele machine simuleren met BEHULP van REST, PowerShell of de CLI om te testen hoe goed uw toepassing reageert op een plotselinge uitzetting.

In de meeste gevallen wilt u de REST API [Virtual Machines- Simulate Eviction](/rest/api/compute/virtualmachines/simulateeviction) gebruiken om te helpen bij het automatisch testen van toepassingen. Voor REST betekent `Response Code: 204` een dat de gesimuleerde uitzetting is geslaagd. U kunt gesimuleerde uitzettingen combineren met de [service](scheduled-events.md)Geplande gebeurtenis om te automatiseren hoe uw app reageert wanneer de VM wordt verwijderen.

Als u geplande gebeurtenissen in actie wilt zien, bekijkt u [Azure Friday - Azure Scheduled Events voorbereiden op VM-onderhoud.](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)


### <a name="quick-test"></a>Snelle test

Voor een snelle test om te laten zien hoe een gesimuleerde uitzetting werkt, gaan we een query uitvoeren op de geplande gebeurtenisservice om te zien hoe deze eruit ziet wanneer u een uitzetting simuleert met behulp van de Azure CLI.

De service Geplande gebeurtenis wordt ingeschakeld voor uw service wanneer u voor het eerst een aanvraag voor gebeurtenissen doet. 

Ga op afstand naar uw VM en open vervolgens een opdrachtprompt. 

Typ het volgende in de opdrachtprompt op uw virtuele VM:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Dit eerste antwoord kan maximaal 2 minuten duren. Vanaf nu moeten ze vrijwel onmiddellijk uitvoer weergeven.

Simuleer op een computer met de Azure CLI (zoals uw lokale computer) een uitzetting met [az vm simulate-eviction.](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction) Vervang de naam van de resourcegroep en de VM door uw eigen naam. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

De antwoorduitvoer heeft `Status: Succeeded` als de aanvraag is gemaakt.

Ga snel terug naar uw externe verbinding met uw virtuele spot-machine en query's Scheduled Events eindpunt opnieuw. Herhaal de volgende opdracht totdat u een uitvoer met meer informatie krijgt:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Wanneer de geplande gebeurtenisservice de melding van de uitzetting ontvangt, krijgt u een antwoord dat er ongeveer als het volgende uitziet:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

U kunt zien dat `"EventType":"Preempt"` en de resource de VM-resource `"Resources":["myspotvm"]` is. 

U kunt ook zien wanneer de VM wordt gesloten door de te controleren: de VM wordt niet vóór de opgegeven tijd eruit gezet, zodat uw toepassing op een goede manier kan worden `"NotBefore"` afgesloten.


## <a name="next-steps"></a>Volgende stappen

U kunt ook een virtuele Azure Spot-machine maken [met behulp Azure PowerShell,](../windows/spot-powershell.md) [portal](../spot-portal.md)of een [sjabloon](spot-template.md).

Query's uitvoeren op actuele prijsinformatie met behulp [van de API voor Azure-retailprijzen](/rest/api/cost-management/retail-prices/azure-retail-prices) voor informatie over Azure Spot Virtual Machine. De `meterName` en bevatten beide `skuName` `Spot` .

Zie Foutcodes als er een fout [is opgetreden.](../error-codes-spot.md)
