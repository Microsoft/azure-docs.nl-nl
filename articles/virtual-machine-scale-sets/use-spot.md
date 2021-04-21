---
title: Een schaalset maken die gebruikmaakt van Azure Spot Virtual Machines
description: Meer informatie over het maken van virtuele-machineschaalsets in Azure die gebruikmaken van Azure Spot Virtual Machines kosten te besparen.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762943"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Azure Spot Virtual Machines voor virtuele-machineschaalsets 

Als u Azure Spot Virtual Machines schaalsets gebruikt, kunt u profiteren van onze ongebruikte capaciteit tegen aanzienlijke kostenbesparingen. Op elk moment dat Azure de capaciteit terug nodig heeft, worden azure Spot Virtual Machine-exemplaren door de Azure-infrastructuur buiten gebruik gezet. Daarom zijn Azure Spot Virtual Machine-exemplaren geweldig voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/test-omgevingen, grote rekenworkloads en meer.

De hoeveelheid beschikbare capaciteit kan variëren op basis van grootte, regio, tijdstip en meer. Bij het implementeren van Azure Spot Virtual Machine-exemplaren op schaalsets wijst Azure het exemplaar alleen toe als er capaciteit beschikbaar is, maar er is geen SLA voor deze exemplaren. Een virtuele-machineschaalset van Azure Spot wordt geïmplementeerd in één foutdomein en biedt geen garanties voor hoge beschikbaarheid.


## <a name="pricing"></a>Prijzen

Prijzen voor Azure Spot Virtual Machine-exemplaren zijn variabel, op basis van regio en SKU. Zie Prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) en Windows voor meer [informatie.](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 


Met variabele prijzen hebt u de mogelijkheid om een maximumprijs in te stellen, in Amerikaanse dollars (USD), met maximaal vijf decimalen. De waarde is bijvoorbeeld `0.98765` een maximumprijs van $ 0,98765 USD per uur. Als u de maximumprijs in stelt op , wordt het exemplaar niet `-1` onbetaald op basis van de prijs. De prijs voor het exemplaar is de huidige prijs voor de virtuele Spot-machine van Azure of de prijs voor een standard-exemplaar, die ooit lager is, zolang er capaciteit en quotum beschikbaar zijn.


## <a name="limitations"></a>Beperkingen

De volgende grootten worden niet ondersteund voor Azure Spot Virtual Machines:
 - B-serie
 - Promotieversies van elke grootte (zoals Dv2, NV, NC, H-promotiegrootten)

Azure Spot Virtual Machine kan worden geïmplementeerd in elke regio, behalve Microsoft Azure China 21Vianet.

<a name="channel"></a>

De volgende [aanbiedingstypen](https://azure.microsoft.com/support/legal/offer-details/) worden momenteel ondersteund:

-   Enterprise Agreement
-   Aanbiedingscode betalen per gebruikt (003P)
-   Sponsored (0036P en 0136P)
- Zie de informatie over cloudserviceproviders (CSP Partner Center [of](/partner-center/azure-plan-get-started) neem rechtstreeks contact op met uw partner.

## <a name="eviction-policy"></a>Verwijderingsbeleid

Wanneer u een schaalset maakt met behulp van Azure Spot Virtual Machines, kunt u het verwijderbeleid instellen op *Toewijzing* verwijderen (standaard) of *Verwijderen.* 

Met *het beleid Toewijzing* van de toewijzing wordt de status van uw onbestroeste exemplaren verplaatst naar de status gestopt, zodat u de onbedexeerde exemplaren opnieuw kuntploeren. Er is echter geen garantie dat de toewijzing slaagt. De VM's die niet zijn toegewezen, tellen mee voor het quotum van uw schaalset-exemplaar en er worden kosten in rekening gebracht voor uw onderliggende schijven. 

Als u wilt dat uw exemplaren worden verwijderd wanneer ze worden verwijderd, kunt u het verwijderbeleid instellen om te *verwijderen.* Als het verwijderbeleid is ingesteld op verwijderen, kunt u nieuwe VM's maken door de eigenschap aantal instanties van schaalsets te verhogen. De verwijderde VM's worden samen met hun onderliggende schijven verwijderd en daarom worden er geen kosten in rekening gebracht voor de opslag. U kunt ook de functie voor automatisch schalen van schaalsets gebruiken om automatisch te proberen om te compenseren voor de uit de VM's geplaatste VM's. Er is echter geen garantie dat de toewijzing slaagt. Het is raadzaam om alleen de functie voor automatisch schalen te gebruiken in azure Spot Virtual Machine-schaalsets wanneer u het verwijderbeleid in stelt om te verwijderen om de kosten van uw schijven te voorkomen en quotumlimieten te overschrijden. 

Gebruikers kunnen zich ervoor kiezen om meldingen in de VM te ontvangen via [Azure Scheduled Events.](../virtual-machines/linux/scheduled-events.md) U krijgt een melding als uw VM's worden uitgeschakeld en u hebt 30 seconden om taken te voltooien en afsluittaken uit te voeren vóór de uitzetting. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Probeer & herstellen (preview)

Deze nieuwe functie op platformniveau maakt gebruik van AI om automatisch te proberen verwijderde exemplaren van azure spot-virtuele machines te herstellen binnen een schaalset om het aantal doel-exemplaren te behouden. 

> [!IMPORTANT]
> Probeer & herstellen momenteel in openbare preview is.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Probeer & te herstellen:
- Pogingen om Azure Spot te Virtual Machines verwijderd vanwege capaciteit.
- Herstelde Azure Spot Virtual Machines worden naar verwachting gedurende een langere periode uitgevoerd met een lagere waarschijnlijkheid van een door capaciteit geactiveerde uitzetting.
- Verbetert de levensduur van een virtuele Azure Spot-machine, zodat workloads langer worden uitgevoerd.
- Helpt Virtual Machine Scale Sets om het doel aantal voor Azure Spot Virtual Machines te behouden, vergelijkbaar met de functie voor het aantal doel die al bestaat voor VM's met betalen per gebruik.

Probeer & herstellen is uitgeschakeld in schaalsets die automatisch [schalen gebruiken.](virtual-machine-scale-sets-autoscale-overview.md) Het aantal VM's in de schaalset wordt aangestuurd door de regels voor automatisch schalen.

### <a name="register-for-try--restore"></a>Registreren om te & herstellen

Voordat u de functie voor het & herstellen kunt gebruiken, moet u uw abonnement registreren voor de preview. Het kan enkele minuten duren voordat de registratie is voltooid. U kunt de Azure CLI of PowerShell gebruiken om de functieregistratie te voltooien.


**CLI gebruiken**

Gebruik [az feature register om](/cli/azure/feature#az_feature_register) de preview voor uw abonnement in teschakelen. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**PowerShell gebruiken** 

Gebruik de cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in te stellen. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Plaatsingsgroepen

Plaatsingsgroep is een constructie die vergelijkbaar is met een Azure-beschikbaarheidsset, met eigen foutdomeinen en upgradedomeinen. Standaard bestaat een schaalset uit één plaatsingsgroep met een omvang van maximaal 100 virtuele machines. Als de eigenschap van de schaalset met de naam is ingesteld op false , kan de schaalset bestaan uit meerdere plaatsingsgroepen en heeft deze een bereik van `singlePlacementGroup` 0-1000 VM's.  

> [!IMPORTANT]
> Tenzij u Infiniband met HPC gebruikt, wordt het sterk aanbevolen om de eigenschap van de schaalset in te stellen op false om meerdere plaatsingsgroepen in te stellen voor betere schaalbaarheid in de regio of `singlePlacementGroup` zone.  

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Azure Spot-Virtual Machines in schaalsets

Als u Azure Spot Virtual Machines schaalsets wilt implementeren, kunt u de nieuwe vlag *Prioriteit* instellen op *Spot.* Alle VM's in uw schaalset worden ingesteld op Spot. Als u een schaalset wilt maken met Azure Spot Virtual Machines, gebruikt u een van de volgende methoden:
- [Azure-portal](#portal)
- [Azure-CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sjablonen](#resource-manager-templates)

## <a name="portal"></a>Portal

Het proces voor het maken van een schaalset die gebruikmaakt van Azure Spot Virtual Machines is hetzelfde als beschreven in het [aan de slag-artikel.](quick-create-portal.md) Wanneer u een schaalset implementeert, kunt u ervoor kiezen om de spot-vlag en het uitzettingsbeleid in te stellen: Een schaalset maken met ![ Azure Spot Virtual Machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Het proces voor het maken van een schaalset met Azure Spot Virtual Machines is hetzelfde als beschreven in het [artikel Aan de slag.](quick-create-cli.md) Voeg gewoon de '--Priority Spot' toe en voeg `--max-price` toe. In dit voorbeeld gebruiken we voor , zodat het exemplaar niet wordt `-1` onbetaald op basis van de `--max-price` prijs.

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

Het proces voor het maken van een schaalset met Azure Spot Virtual Machines is hetzelfde als beschreven in het [artikel Aan de slag.](quick-create-powershell.md)
Voeg '-Priority Spot' toe en geef een `-max-price` op voor [new-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Het proces voor het maken van een schaalset die gebruikmaakt van Azure Spot Virtual Machines is hetzelfde als beschreven in het aan de slag-artikel [voor Linux](quick-create-template-linux.md) of [Windows.](quick-create-template-windows.md) 

Gebruik of hoger voor sjabloonimplementaties voor virtuele `"apiVersion": "2019-03-01"` Spot-machines in Azure. 

Voeg de `priority` eigenschappen , en toe aan de sectie en de eigenschap aan de sectie in `evictionPolicy` uw `billingProfile` `"virtualMachineProfile":` `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` sjabloon:

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

Als u het exemplaar wilt verwijderen nadat deze is verwijderd, wijzigt u de `evictionPolicy` parameter in `Delete` .


## <a name="simulate-an-eviction"></a>Een uitzetting simuleren

U kunt [een azure spot-virtuele](/rest/api/compute/virtualmachines/simulateeviction) machine simuleren om te testen hoe goed uw toepassing reageert op een plotselinge uitzetting. 

Vervang het volgende door uw gegevens: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` betekent dat de gesimuleerde uitzetting is geslaagd. 

## <a name="faq"></a>Veelgestelde vragen

**V:** Is een exemplaar van een virtuele spot-machine van Azure hetzelfde als het standaard exemplaar?

**A:** Ja, behalve dat er geen SLA voor Azure Spot Virtual Machines en ze kunnen op elk moment worden gebruikt.


**V:** Wat te doen wanneer u wordt uit huis gezet, maar nog steeds capaciteit nodig hebt?

**A:** U wordt aangeraden standaard-VM's te gebruiken in plaats van Azure Spot Virtual Machines als u meteen capaciteit nodig hebt.


**V:** Hoe wordt het quotum beheerd voor azure spot-virtuele machine?

**A:** Azure Spot Virtual Machine-exemplaren en standaard-exemplaren hebben afzonderlijke quotumgroepen. Het quotum voor virtuele Spot-machines van Azure wordt gedeeld tussen VM's en instanties van schaalsets. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor meer informatie.


**V:** Kan ik een extra quotum aanvragen voor een virtuele Spot-machine van Azure?

**A:** Ja, u kunt de aanvraag indienen om uw quotum voor Azure Spot Virtual Machines verhogen via het [standaardproces voor quotumaanvraag.](../azure-portal/supportability/per-vm-quota-requests.md)


**V:** Kan ik bestaande schaalsets converteren naar virtuele-machineschaalsets van Azure Spot?

**A:** Nee, het instellen van `Spot` de vlag wordt alleen ondersteund tijdens het maken.


**V:** Als ik gebruikte voor `low` schaalsets met lage prioriteit, moet ik dan in plaats daarvan `Spot` gaan gebruiken?

**A:** Op dit moment werkt zowel als, maar u moet beginnen over te gaan `low` naar het gebruik van `Spot` `Spot` .


**V:** Kan ik een schaalset maken met zowel gewone VM's als Azure Spot Virtual Machines?

**A:** Nee, een schaalset kan niet meer dan één prioriteitstype ondersteunen.


**V:**  Kan ik automatisch schalen gebruiken met virtuele-machineschaalsets van Azure Spot?

**A:** Ja, u kunt regels voor automatisch schalen instellen op uw virtuele-machineschaalset van Azure Spot. Als uw VM's zijn uitbesteed, kan automatisch schalen proberen om nieuwe Azure Spot-Virtual Machines. Vergeet niet dat u deze capaciteit echter niet kunt garanderen. 


**V:**  Werkt automatisch schalen met zowel het verwijderbeleid (toewijzingen verwijderen als verwijderen)?

**A:** Ja, maar het wordt aanbevolen dat u het verwijderbeleid zo in stelt dat deze wordt verwijderd wanneer u automatisch schalen gebruikt. Dit komt doordat instanties die de toewijzing niet hebben toegewezen, worden meegetelde in uw capaciteitstelling voor de schaalset. Wanneer u automatisch schalen gebruikt, bereikt u waarschijnlijk snel het aantal doel-exemplaren als gevolg van de niet-toegewezen, uitbesteed exemplaren. Ook kunnen uw schaalbewerkingen worden beïnvloed door spotverzettingen. Instanties van virtuele-machineschaalsets kunnen bijvoorbeeld onder het minimum aantal in te stellen vallen als gevolg van meerdere locaties tijdens schaalbewerkingen. 


**V:** Waar kan ik vragen stellen?

**A:** U kunt uw vraag posten en taggen met `azure-spot` op Q&[A](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Volgende stappen

Bekijk de pagina [met prijzen voor virtuele-machineschaalsets](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) voor prijsdetails.
