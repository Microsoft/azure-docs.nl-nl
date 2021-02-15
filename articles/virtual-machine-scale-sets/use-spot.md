---
title: Een schaalset maken die gebruikmaakt van Azure spot-Vm's
description: Meer informatie over het maken van virtuele-machine schaal sets van Azure die gebruikmaken van behulp van de functie voor het besparen van kosten.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: cynthn
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 03bf5e0ef7e6268e68139b6d73685f67d88f6231
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385928"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Azure spot-Vm's voor schaal sets voor virtuele machines 

Met Azure spot op schaal sets kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur spot instanties. Daarom zijn spot instanties geweldig voor werk belastingen die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grote Compute-workloads, en meer.

De hoeveelheid beschik bare capaciteit kan variëren op basis van grootte, regio, tijd van de dag en meer. Bij het implementeren van spot instanties op schaal sets wordt het exemplaar alleen door Azure toegewezen als er capaciteit beschikbaar is, maar er is geen SLA voor deze instanties. Er wordt een set met hoge Beschik baarheid in één fout domein geïmplementeerd.


## <a name="pricing"></a>Prijzen

Prijzen voor spot instanties zijn variabel, op basis van de regio en de SKU. Zie prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)voor meer informatie. 


Met variabele prijzen kunt u een maximum prijs instellen, in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765` is bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximale prijs instelt op `-1` , wordt het exemplaar niet op basis van de prijs verwijderd. De prijs voor de instantie is de huidige prijs voor de steun waarde of de prijs voor een standaard exemplaar, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.


## <a name="limitations"></a>Beperkingen

De volgende grootten worden niet ondersteund voor Azure spot:
 - B-serie
 - Promotie versies van elke grootte (zoals dv2, NV, NC, H promotie grootten)

Azure spot kan worden geïmplementeerd in elke regio, met uitzonde ring van Microsoft Azure-China 21Vianet.

<a name="channel"></a>

De volgende [aanbiedings typen](https://azure.microsoft.com/support/legal/offer-details/) worden momenteel ondersteund:

-   Enterprise Agreement
-   Betalen per gebruik-aanbieding code 003P
-   Gesponsorde
- Neem contact op met uw partner voor Cloud service provider (CSP)

## <a name="eviction-policy"></a>Verwijderingsbeleid

Bij het maken van een spot schaal sets kunt u het verwijderings beleid zo instellen dat de *toewijzing wordt opheffen* (standaard) of *verwijderen*. 

Het beleid voor het *opheffen* van de toewijzing verplaatst de verwijderde exemplaren naar de status stopped-disallocated, zodat u verwijderde exemplaren opnieuw kunt implementeren. Er is echter geen garantie dat de toewijzing slaagt. De toegewezen Vm's worden geteld voor het quotum van uw schaalset-exemplaar en er worden kosten in rekening gebracht voor de onderliggende schijven. 

Als u wilt dat uw instanties in uw schaalset worden verwijderd wanneer ze worden gewist, kunt u het verwijderings beleid instellen op *verwijderen*. Als het verwijderings beleid is ingesteld op verwijderen, kunt u nieuwe virtuele machines maken door de eigenschap aantal exemplaren van de schaalset te verhogen. De verwijderde Vm's worden samen met de onderliggende schijven verwijderd en daarom worden er geen kosten in rekening gebracht voor de opslag. U kunt ook de functie voor automatisch schalen van schaal sets gebruiken om automatisch te proberen en te compenseren voor verwijderde Vm's, maar er is echter geen garantie dat de toewijzing slaagt. Het wordt aanbevolen dat u de functie voor automatisch schalen op steun schaal sets gebruikt wanneer u het verwijderings beleid instelt op verwijderen om de kosten van uw schijven te voor komen en quotum limieten te bepalen. 

Gebruikers kunnen zich aanmelden om in-VM-meldingen te ontvangen via [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). Hiermee wordt u op de hoogte gesteld als uw Vm's worden verwijderd en u 30 seconden hebt om taken te volt ooien en afsluit taken uit te voeren vóór de verwijdering. 

## <a name="placement-groups"></a>Plaatsings groepen
Plaatsing groep is een construct die vergelijkbaar is met een Azure-beschikbaarheidsset, met een eigen fout domeinen en upgrade domeinen. Standaard bestaat een schaalset uit één plaatsingsgroep met een omvang van maximaal 100 virtuele machines. Als de eigenschap Scale set met de naam `singlePlacementGroup` is ingesteld op *False*, kan de schaalset bestaan uit meerdere plaatsings groepen en een bereik van 0-1000 vm's hebben. 

> [!IMPORTANT]
> Tenzij u InfiniBand met HPC gebruikt, wordt het ten zeerste aanbevolen de eigenschap Scale set `singlePlacementGroup` in te stellen op *False* om meerdere plaatsings groepen in te scha kelen voor een betere schaal baarheid in de regio of zone. 

## <a name="deploying-spot-vms-in-scale-sets"></a>Implementatie-Vm's in schaal sets implementeren

Als u virtuele-machine bewerkingen wilt implementeren op schaal sets, kunt u de vlag nieuwe *prioriteit* instellen op *Spot*. Alle virtuele machines in uw schaalset worden ingesteld op spot. Gebruik een van de volgende methoden voor het maken van een schaalset met behulp van spot-Vm's:
- [Azure-portal](#portal)
- [Azure-CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sjablonen](#resource-manager-templates)

## <a name="portal"></a>Portal

Het proces voor het maken van een schaalset die gebruikmaakt van stapsgewijze Vm's, is gelijk aan die in het [artikel aan](quick-create-portal.md)de slag. Wanneer u een schaalset implementeert, kunt u kiezen voor het instellen van de vlag spot en het verwijderings beleid: ![ een schaalset maken met behulp van een vaste virtuele machine](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Het proces voor het maken van een schaalset met stapsgewijze Vm's is hetzelfde als die in het [artikel aan](quick-create-cli.md)de slag. Voeg alleen de '---Priority spot ' toe en voeg toe `--max-price` . In dit voor beeld gebruiken we `-1` voor `--max-price` dat het exemplaar niet wordt verwijderd op basis van de prijs.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Het proces voor het maken van een schaalset met stapsgewijze Vm's is hetzelfde als die in het [artikel aan](quick-create-powershell.md)de slag.
Voeg alleen '-Priority spot ' toe en geef er een `-max-price` op bij de [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Het proces voor het maken van een schaalset die gebruikmaakt van stapsgewijze Vm's, is gelijk aan die in het artikel aan de slag voor [Linux](quick-create-template-linux.md) of [Windows](quick-create-template-windows.md). 

Gebruik of hoger voor implementaties van steun sjablonen `"apiVersion": "2019-03-01"` . 

Voeg de `priority` Eigenschappen en toe `evictionPolicy` `billingProfile` aan de `"virtualMachineProfile":` sectie en de `"singlePlacementGroup": false,` eigenschap aan de `"Microsoft.Compute/virtualMachineScaleSets"` sectie in de sjabloon:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Als u het exemplaar wilt verwijderen nadat het is verwijderd, wijzigt `evictionPolicy` u de para meter in `Delete` .

## <a name="faq"></a>Veelgestelde vragen

**V:** Nadat het is gemaakt, is dezelfde instantie hetzelfde als standaard exemplaar?

**A:** Ja, behalve als er geen SLA is voor de spot-Vm's, en ze kunnen op elk gewenst moment worden verwijderd.


**V:** Wat te doen wanneer u weggaat, maar nog steeds capaciteit nodig heeft?

**A:** U wordt aangeraden standaard Vm's te gebruiken in plaats van op de plek waar u de capaciteit direct nodig hebt.


**V:** Hoe wordt het quotum voor steun beheerd?

**A:** Spot instanties en standaard instanties hebben afzonderlijke quota groepen. Het steun quotum wordt gedeeld tussen Vm's en scale-set-exemplaren. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor meer informatie.


**V:** Kan ik een extra quotum voor plaats vragen?

**A:** Ja, u kunt de aanvraag indienen om uw quotum voor de begeleide Vm's te verhogen via het [standaard quotum aanvraag proces](../azure-portal/supportability/per-vm-quota-requests.md).


**V:** Kan ik bestaande schaal sets converteren naar een steun schaalset?

**A:** Nee, het instellen `Spot` van de vlag wordt alleen ondersteund tijdens de aanmaak tijd.


**V:** Als ik heb gebruikt `low` voor schaal sets met lage prioriteit, moet ik `Spot` dan in plaats daarvan aan de slag gaan met?

**A:** Voor nu geldt `low` dat beide en `Spot` werken, maar u moet overstappen op gebruiken `Spot` .


**V:** Kan ik een schaalset maken met zowel normale virtuele machines als virtuele machines met spot mogelijkheden?

**A:** Nee, een schaalset kan niet meer dan één prioriteits type ondersteunen.


**V:**  Kan ik automatisch schalen met schaal sets gebruiken?

**A:** Ja, u kunt regels voor automatisch schalen instellen op de schaalset met steun schalen. Als uw Vm's worden verwijderd, kunt u met automatisch schalen nieuwe spot-Vm's maken. Vergeet niet dat u deze capaciteit echter niet gegarandeerd. 


**V:**  Werkt automatisch schalen met beide verwijderings beleidsregels (toewijzing en verwijdering ongedaan maken)?

**A:** Ja, u wordt echter aangeraden het verwijderings beleid in te stellen dat u wilt verwijderen wanneer u automatisch schalen gebruikt. Dit komt doordat niet-toegewezen instanties worden geteld voor het aantal capaciteit van de schaalset. Wanneer u automatisch schalen gebruikt, bereikt u waarschijnlijk snel het aantal doel instanties als gevolg van de opgeheven, verwijderde exemplaren. Ook kunnen uw schaal bewerkingen worden beïnvloed door spot verwijderingen. Zo kunnen instanties van virtuele-machine schaal sets onder het aantal ingestelde minuten vallen vanwege meerdere spot verwijderingen tijdens schaal bewerkingen. 


**V:** Waar kan ik vragen plaatsen?

**A:** U kunt uw vraag met `azure-spot` op [Q&A](/answers/topics/azure-spot.html)plaatsen en labelen. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [prijs pagina voor de schaalset voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) voor meer informatie.
