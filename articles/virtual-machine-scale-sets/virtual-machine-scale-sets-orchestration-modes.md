---
title: Orchestration modes for virtual machine scale sets in Azure
description: Meer informatie over het gebruik van flexibele en uniforme orchestration-modi voor virtuele-machineschaalsets in Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 72e36a942eeaea00699f346db99a7ca3503495da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481647"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Preview: Orchestration modes for virtual machine scale sets in Azure 

Virtual Machines schaalsets bieden een logische groepering van door het platform beheerde virtuele machines. Met schaalsets maakt u een configuratiemodel voor virtuele machines, voegt u automatisch extra exemplaren toe of verwijdert u deze op basis van CPU- of geheugenbelasting en upgradet u automatisch naar de nieuwste versie van het besturingssysteem. Van oudsher kunt u met schaalsets virtuele machines maken met behulp van een VM-configuratiemodel dat werd opgegeven op het moment dat de schaalset werd gemaakt. De schaalset kan alleen virtuele machines beheren die impliciet zijn gemaakt op basis van het configuratiemodel. 

Met de orchestration-modi van schaalsets hebt u meer controle over de manier waarop exemplaren van virtuele machines worden beheerd door de schaalset. 

> [!IMPORTANT]
> De orchestration-modus wordt gedefinieerd wanneer u de schaalset maakt en kan later niet worden gewijzigd of bijgewerkt.


## <a name="scale-sets-with-uniform-orchestration"></a>Schaalsets met Uniform Orchestration
Geoptimaliseerd voor grootschalige staatloze workloads met identieke exemplaren.

Virtuele-machineschaalsets met Uniform Orchestration gebruiken een profiel of sjabloon voor virtuele machines om omhoog te schalen naar de gewenste capaciteit. Hoewel er een mogelijkheid is om exemplaren van afzonderlijke virtuele machines te beheren of aan te passen, maakt Uniform gebruik van identieke VM-exemplaren. Afzonderlijke exemplaren van uniforme VM's worden beschikbaar gemaakt via de VM API-opdrachten van de virtuele-machineschaalset. Afzonderlijke exemplaren zijn niet compatibel met de standaard azure IaaS VM API-opdrachten, Azure-beheerfuncties zoals Azure Resource Manager-resourcetagging RBAC-machtigingen, Azure Backup of Azure Site Recovery. Uniforme orchestration biedt garanties voor hoge beschikbaarheid van foutdomeinen wanneer deze zijn geconfigureerd met minder dan 100 exemplaren. Uniforme orchestration is algemeen beschikbaar en biedt ondersteuning voor een volledig scala aan beheer en orchestration van schaalsets, waaronder automatisch schalen op basis van metrische gegevens, exemplaarbeveiliging en automatische upgrades van het besturingssysteem. 


## <a name="scale-sets-with-flexible-orchestration"></a>Schaalsets met flexibele orchestration 
Hoge beschikbaarheid op schaal met identieke of meerdere typen virtuele machines.

Met Flexibele orchestration biedt Azure een uniforme ervaring binnen het Azure VM-ecosysteem. Flexibele orchestration biedt garanties voor hoge beschikbaarheid (maximaal 1000 VM's) door VM's te spreiden over foutdomeinen in een regio of binnen een beschikbaarheidszone. Hierdoor kunt u uw toepassing uitschalen met behoud van de isolatie van foutdomeinen die essentieel is voor het uitvoeren van quorumgebaseerde of stateful workloads, waaronder:
- Workloads op basis van quorum
- Open-Source databases
- Stateful toepassingen
- Services waarvoor hoge beschikbaarheid en grootschalige schaal vereist zijn
- Services die typen virtuele machines willen combineren of spot- en on-demand VM's willen combineren
- Bestaande beschikbaarheidssettoepassingen  

> [!IMPORTANT]
> Virtuele-machineschaalsets in de modus Flexibele orchestration zijn momenteel beschikbaar als openbare preview. Er is een opt-in-procedure nodig om de openbare preview-functionaliteit te gebruiken die hieronder wordt beschreven.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Wat is er gewijzigd in de flexibele orchestrationmodus?
Een van de belangrijkste voordelen van Flexibele orchestration is dat het orchestration-functies biedt ten opzichte van standaard virtuele Azure IaaS-machines, in plaats van onderliggende virtuele machines in schaalsets. Dit betekent dat u alle standaard-VM-API's kunt gebruiken bij het beheren van flexibele orchestration-exemplaren, in plaats van de VM-API's van de virtuele-machineschaalset die u gebruikt met Uniform Orchestration. Tijdens de preview-periode zijn er verschillende verschillen tussen het beheren van exemplaren in Flexibele orchestration en Uniform Orchestration. Over het algemeen raden we u aan om waar mogelijk de standaard Azure IaaS VM-API's te gebruiken. In deze sectie worden voorbeelden belicht van best practices voor het beheren van VM-exemplaren met flexibele orchestration.  

### <a name="assign-fault-domain-during-vm-creation"></a>Foutdomein toewijzen tijdens het maken van de VM
U kunt het aantal foutdomeinen voor de flexibele orchestration-schaalset kiezen. Wanneer u een VM toevoegt aan een flexibele schaalset, verspreidt Azure exemplaren standaard gelijkmatig over foutdomeinen. Hoewel het raadzaam is om Azure het foutdomein toe te wijzen, kunt u voor geavanceerde of probleemoplossingsscenario's dit standaardgedrag overschrijven en het foutdomein opgeven waar het exemplaar wordt geland.

```azurecli-interactive 
az vm create â€“vmss "myVMSS"  â€“-platform_fault_domain 1
```

### <a name="instance-naming"></a>Naamgeving van exemplaren 
Wanneer u een VM maakt en toevoegt aan een flexibele schaalset, hebt u volledige controle over de namen van exemplaren binnen de regels voor de Azure-naamconventie. Wanneer VM's automatisch aan de schaalset worden toegevoegd via automatisch schalen, geeft u een voorvoegsel op en voegt Azure een uniek nummer toe aan het einde van de naam.

### <a name="query-instances-for-power-state"></a>Query-exemplaren voor energietoestand
De voorkeursmethode is het gebruik van Azure Resource Graph voor het uitvoeren van query's voor alle virtuele machines in een virtuele-machineschaalset. Azure Resource Graph biedt efficiënte querymogelijkheden voor Azure-resources op schaal voor abonnementen. 

``` 
|â€¯whereâ€¯typeâ€¯=~â€¯'Microsoft.Compute/virtualMachines' 
|â€¯whereâ€¯properties.virtualMachineScaleSetâ€¯containsâ€¯"demo" 
|â€¯extendâ€¯powerStateâ€¯=â€¯properties.extended.instanceView.powerState.code 
|â€¯projectâ€¯name,â€¯resourceGroup,â€¯location,â€¯powerState 
|â€¯orderâ€¯byâ€¯resourceGroupâ€¯desc,â€¯nameâ€¯desc 
```

Het opvragen van resources [Azure Resource Graph](../governance/resource-graph/overview.md) is een handige en efficiënte manier om query's uit te voeren op Azure-resources en om API-aanroepen naar de resourceprovider te minimaliseren. Azure Resource Graph is een uiteindelijk consistente cache waarbij nieuwe of bijgewerkte resources maximaal 60 seconden niet worden weergegeven. U kunt:
- VM's in een resourcegroep of abonnement weer te geven.
- Gebruik de uitv expand-optie om de exemplaarweergave (foutdomeintoewijzing, energie- en inrichtings staten) op te halen voor alle VM's in uw abonnement.
- Gebruik de GET VM-API en opdrachten om de model- en exemplaarweergave voor één exemplaar op te halen.

### <a name="scale-sets-vm-batch-operations"></a>VM Batch-bewerkingen voor schaalsets
Gebruik de standaard-VM-opdrachten om exemplaren te starten, te stoppen, opnieuw op te starten, te verwijderen in plaats van de VM-API's van de virtuele-machineschaalset. De batchbewerkingen van de virtuele-machineschaalset VM (alles starten, alles stoppen, alles opnieuw maken, enzovoort) worden niet gebruikt met de flexibele orchestration-modus. 

### <a name="monitor-application-health"></a>De status van de toepassing bewaken 
Met toepassings health monitoring kan uw toepassing een heartbeat aan Azure geven om te bepalen of uw toepassing in orde of slecht is. Azure kan automatisch VM-exemplaren vervangen die niet in orde zijn. Voor exemplaren van flexibele schaalsets moet u de toepassingstoestandsextensie installeren en configureren op de virtuele machine. Voor exemplaren van een uniforme schaalset kunt u de toepassingstoestandsextensie gebruiken of de status meten met een Azure Load Balancer Aangepaste statustest. 

### <a name="list-scale-sets-vm-api-changes"></a>Wijzigingen in de VM-API voor schaalsets opsnets 
Virtual Machine Scale Sets kunt u een lijst maken met de exemplaren die deel uitmaken van de schaalset. Met Flexibele orchestration biedt de lijst Virtual Machine Scale Sets VM-opdracht een lijst met VM-ID's van schaalsets. Vervolgens kunt u de OPDRACHTEN GET Virtual Machine Scale Sets VM aanroepen voor meer informatie over hoe de schaalset werkt met het VM-exemplaar. Als u de volledige details van de VM wilt bekijken, gebruikt u de standaardOPDRACHTEN voor virtuele VM's of [Azure Resource Graph](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Diagnostische gegevens over opstarten ophalen 
Gebruik de standaard-VM-API's en -opdrachten om diagnostische gegevens en schermopnamen van het exemplaar op te halen. De Virtual Machine Scale Sets api's en opdrachten voor diagnostische opstart-VM's worden niet gebruikt met exemplaren van de modus Flexibele orchestration.

### <a name="vm-extensions"></a>VM-extensies 
Gebruik extensies die zijn gericht op standaard virtuele machines, in plaats van extensies die zijn gericht op exemplaren van de modus Uniform Orchestration.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Een vergelijking van flexibele, uniforme en beschikbaarheidssets 
In de volgende tabel worden de flexibele orchestrationmodus, de modus Uniform orchestration en beschikbaarheidssets vergeleken op hun functies.

| Functie | Ondersteund door Flexibele orchestration (preview) | Ondersteund door Uniform Orchestration (algemene beschikbaarheid) | Ondersteund door AvSets (algemene beschikbaarheid) |
|-|-|-|-|
|         Type virtuele machine  | Standaard Azure IaaS-VM (Microsoft.compute /virtualmachines)  | Specifieke VM's van schaalsets (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Standaard Azure IaaS-VM (Microsoft.compute /virtualmachines)  |
|         SKU's ondersteund  |            D-serie, E-serie, F-serie, A-serie, B-serie, Intel, AMD  |            Alle SKU's  |            Alle SKU's  |
|         Beschikbaarheidszones  |            Geef desgewenst alle exemplaren op in één beschikbaarheidszone |            Exemplaren in 1, 2 of 3 beschikbaarheidszones opgeven  |            Niet ondersteund  |
|         Volledige controle over VM, NIC's, schijven  |            Ja  |            Beperkt beheer met VM-API voor virtuele-machineschaalsets  |            Ja  |
|         Automatisch schalen  |            Nee  |            Ja  |            Nee  |
|         VM toewijzen aan een specifiek foutdomein  |            Ja  |             Nee   |            Nee  |
|         NIC's en schijven verwijderen bij het verwijderen van VM-exemplaren  |            Nee  |            Ja  |            Nee  |
|         Upgradebeleid (VM-schaalsets) |            Nee  |            Automatisch, Rollend, Handmatig  |            N.v.t.  |
|         Automatische updates van het besturingssysteem (VM-schaalsets) |            Nee  |            Ja  |            N.v.t.  |
|         In Gastbeveiligingspatching  |            Ja  |            Nee  |            Ja  |
|         Meldingen beëindigen (VM-schaalsets) |            Nee  |            Ja  |            N.v.t.  |
|         Exemplaarherstel (VM-schaalsets) |            Nee  |            Ja   |            N.v.t.  |
|         Versneld netwerken  |            Ja  |            Ja  |            Ja  |
|         Spotâ€ 1instances en pricingâ€ à  |            Ja, u kunt zowel Spot- als Regular-prioriteits instances hebben  |            Ja, instanties moeten alle Spot- of Regular-exemplaren zijn  |            Nee, alleen instanties met reguliere prioriteit  |
|         Besturingssystemen combineren  |            Ja, Linux en Windows kunnen zich in dezelfde flexibele schaalset bevinden |            Nee, exemplaren zijn hetzelfde besturingssysteem  |               Ja, Linux en Windows kunnen zich in dezelfde flexibele schaalset bevinden |
|         Toepassings health bewaken  |            Toepassings statusextensie  |            Toepassingstoestandsextensie of Azure Load Balancer-test  |            Toepassings statusextensie  |
|         UltraSSDâ€ âDisksâ€ â  |            Ja  |            Ja, alleen voornale implementaties  |            Nee  |
|         Infinibandâ€ à  |            Nee  |            Ja, alleen één plaatsingsgroep  |            Ja  |
|         Writeâ€ âAcceleratorâ€ à  |            Nee  |            Ja  |            Ja  |
|         Proximityâ€ âPlacement Groupsâ€ â  |            Ja  |            Ja  |            Ja  |
|         Azure Dedicated Hostsa€ â  |            Nee  |            Ja  |            Ja  |
|         Basic SLBâ€ â  |            Nee  |            Ja  |            Ja  |
|         Azure Load Balancer Standard-SKU |            Ja  |            Ja  |            Ja  |
|         Application Gateway  |            Nee  |            Ja  |            Ja  |
|         Onderhoudsbeheer...  |            Nee  |            Ja  |            Ja  |
|         Lijst met VM's in set  |            Ja  |            Ja  |            Ja, lijst met VM's in AvSet  |
|         Azure-waarschuwingen  |            Nee  |            Ja  |            Ja  |
|         VM Insights  |            Nee  |            Ja  |            Ja  |
|         Azure Backup  |            Ja  |            Ja  |            Ja  |
|         Azure Site Recovery  |     Nee  |            Nee  |            Ja  |
|         Bestaande VM aan de groep toevoegen/verwijderen  |            Nee  |            Nee  |            Nee  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registreren voor flexibele orchestration-modus
Voordat u virtuele-machineschaalsets in de flexibele orchestration-modus kunt implementeren, moet u eerst uw abonnement registreren voor de preview-functie. Het kan enkele minuten duren voordat de registratie is voltooid. U kunt de volgende Azure PowerShell of Azure CLI-opdrachten gebruiken om u te registreren.

### <a name="azure-portal"></a>Azure Portal
Navigeer naar de detailpagina voor het abonnement waarin u een schaalset wilt maken in de modus Flexibele orchestration en selecteer Preview-functies in het menu. Selecteer de twee orchestratorfuncties die u wilt inschakelen: _VMOrchestratorSingleFD_ en _VMOrchestratorMultiFD_ en druk op de knop Registreren. De registratie van functies kan maximaal 15 minuten duren.

![Functieregistratie.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Zodra de functies zijn geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider. Navigeer naar het tabblad Resourceproviders voor uw abonnement, selecteer Microsoft.compute en klik op Opnieuw registreren.

![Opnieuw registreren](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Gebruik de cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in teschakelen. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
Gebruik [az feature register om](/cli/azure/feature#az-feature-register) de preview voor uw abonnement in teschakelen. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Aan de slag met flexibele orchestrationmodus

Ga aan de slag met de flexibele orchestrationmodus voor uw schaalsets via Azure Portal, Azure CLI, Terraform of REST API.

### <a name="azure-portal"></a>Azure Portal

Maak een virtuele-machineschaalset in de modus Flexibele orchestration via de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer Virtuele-machineschaalsets in de **zoekbalk.** 
1. Selecteer **Maken op** de pagina **Virtuele-machineschaalsets.**
1. Bekijk op **de pagina Een virtuele-machineschaalset** maken de **sectie Orchestration.**
1. Selecteer voor **de Orchestration-modus** de **optie** Flexibel.
1. Stel het **aantal foutdomeinen in.**
1. Maak het maken van de schaalset af. Zie [Een schaalset maken in de Azure Portal](quick-create-portal.md#create-virtual-machine-scale-set) voor meer informatie over het maken van een schaalset.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Orchestration-modus in portal bij het maken van een schaalset":::

Voeg vervolgens een virtuele machine toe aan de schaalset in de modus Flexibele orchestration.

1. Zoek en selecteer Virtuele machines in de **zoekbalk.**
1. Selecteer **Toevoegen** op de **pagina Virtuele machines.**
1. Bekijk op **het** tabblad Basisinformatie de sectie **Instantiedetails.**
1. Voeg uw VM toe aan de schaalset in de modus Flexibele orchestration door de schaalset te selecteren in **beschikbaarheidsopties.** U kunt de virtuele machine toevoegen aan een schaalset in dezelfde regio, zone en resourcegroep.
1. Maak de virtuele machine af. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="VM toevoegen aan de schaalset flexibele orchestrationmodus":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Maak een flexibele virtuele-machineschaalset met Azure CLI. In het volgende voorbeeld ziet u het maken van een flexibele schaalset waarbij het aantal foutdomeinen is ingesteld op 3, een virtuele machine wordt gemaakt en vervolgens wordt toegevoegd aan de flexibele schaalset. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Maak een flexibele virtuele-machineschaalset met Terraform. Voor dit proces is **terraform Azurerm-provider v2.15.0** of hoger vereist. Let op de volgende parameters:
- Wanneer er geen zone is opgegeven, `platform_fault_domain_count` kan 1, 2 of 3 zijn, afhankelijk van de regio.
- Wanneer een zone is opgegeven, `the fault domain count` kan 1 zijn.
- `single_placement_group` parameter moet zijn `false` voor flexibele virtuele-machineschaalsets.
- Als u een regionale implementatie hebt, hoeft u niet op te `zones` geven.

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>REST-API

1. Maak een lege schaalset. De volgende parameters zijn vereist:
    - API-versie 2019-12-01 (of hoger) 
    - Eén plaatsingsgroep moet zijn `false` bij het maken van een flexibele schaalset

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Voeg virtuele machines toe aan de schaalset.
    1. Wijs de `virtualMachineScaleSet` eigenschap toe aan de schaalset die u eerder hebt gemaakt. U moet de eigenschap opgeven `virtualMachineScaleSet` op het moment dat de VM wordt gemaakt. 
    1. U kunt de **functie copy()** Azure Resource Manager gebruiken om meerdere VM's tegelijk te maken. Zie [Resource-iteratie](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) in Azure Resource Manager sjablonen. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Zie [Azure-quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) voor een volledig voorbeeld.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Hoeveel schaal biedt Flexibele orchestration ondersteuning?**

U kunt maximaal 1000 VM's toevoegen aan een schaalset in de flexibele orchestrationmodus.

**Hoe verhoudt beschikbaarheid met Flexibele orchestration zich tot beschikbaarheidssets of uniform orchestration?**

| Beschikbaarheidskenmerk  | Flexibele orchestration  | Uniforme orchestration  | Beschikbaarheidssets  |
|-|-|-|-|
| Implementeren in beschikbaarheidszones  | Nee  | Ja  | Nee  |
| Beschikbaarheidsgaranties voor foutdomeinen binnen een regio  | Ja, er kunnen maximaal 1000 exemplaren worden verdeeld over maximaal 3 foutdomeinen in de regio. Het maximum aantal foutdomeinen varieert per regio  | Ja, maximaal 100 exemplaren  | Ja, maximaal 200 exemplaren  |
| Plaatsingsgroepen  | Flexibele modus maakt altijd gebruik van meerdere plaatsingsgroepen (singlePlacementGroup = false)  | U kunt één plaatsingsgroep of meerdere plaatsingsgroepen kiezen | N.v.t.  |
| Updatedomeinen  | Geen, onderhouds- of hostupdates worden foutdomein uitgevoerd op foutdomein  | Maximaal 5 updatedomeinen  | Maximaal 20 updatedomeinen  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Problemen met schaalsets oplossen met Flexibele orchestration
Zoek de juiste oplossing voor uw probleemoplossingsscenario. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Oorzaak:** Het abonnement is niet geregistreerd voor de openbare preview-versie van de flexibele orchestrationmodus. 

**Oplossing:** Volg de bovenstaande instructies om u te registreren voor de openbare preview-versie van de flexibele orchestrationmodus. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Oorzaak:** De `platformFaultDomainCount` parameter is ongeldig voor de geselecteerde regio of zone. 

**Oplossing:** U moet een geldige waarde `platformFaultDomainCount` selecteren. Voor zonale implementaties is de `platformFaultDomainCount` maximumwaarde 1. Voor regionale implementaties waarbij geen zone is opgegeven, varieert het maximum `platformFaultDomainCount` afhankelijk van de regio. Zie Manage the availability of VMs for scripts (De beschikbaarheid van [VM's voor scripts beheren)](../virtual-machines/availability.md) om het maximum aantal foutdomeinen per regio te bepalen. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Oorzaak:** Een schaalset verwijderen in de flexibele orchestration-modus die is gekoppeld aan een of meer virtuele machines. 

**Oplossing:** Verwijder alle virtuele machines die zijn gekoppeld aan de schaalset in de modus Flexibele orchestration. Vervolgens kunt u de schaalset verwijderen.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Oorzaak:** Het abonnement is geregistreerd voor de preview-versie van de flexibele orchestration-modus; De `singlePlacementGroup` parameter is echter ingesteld op *Waar.* 

**Oplossing:** De `singlePlacementGroup` moet worden ingesteld op *False*. 


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van uw toepassing in een schaalset.](virtual-machine-scale-sets-deploy-app.md)
