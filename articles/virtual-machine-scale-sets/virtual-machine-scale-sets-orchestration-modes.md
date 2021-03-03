---
title: Orchestration-modi voor virtuele-machine schaal sets in azure
description: Meer informatie over het gebruik van flexibele en uniforme indelings modi voor schaal sets voor virtuele machines in Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 356edcefc522296c5a15cbeb76a1b5e8abf851c1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679675"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Voor beeld: Orchestration-modi voor virtuele-machine schaal sets in azure 

Virtual Machines schaal sets bieden een logische groepering van door het platform beheerde virtuele machines. Met schaal sets kunt u een configuratie model voor virtuele machines maken, automatisch extra exemplaren toevoegen of verwijderen op basis van CPU-of geheugen belasting en automatisch upgraden naar de meest recente versie van het besturings systeem. Traditioneel kunt u met schaal sets virtuele machines maken met behulp van een VM-configuratie model dat is opgegeven op het moment dat de schaalset wordt gemaakt, en de schaalset kan alleen virtuele machines beheren die impliciet zijn gemaakt op basis van het configuratie model. 

Met de indelings modi voor schaal sets kunt u de controle over de werking van virtuele machines beheren door de schaalset. 

> [!IMPORTANT]
> De Orchestration-modus wordt gedefinieerd wanneer u de schaalset maakt en kan later niet worden gewijzigd of bijgewerkt.


## <a name="scale-sets-with-uniform-orchestration"></a>Schaal sets met een uniforme indeling
Geoptimaliseerd voor grootschalige workloads met een hoge schaal met identieke exemplaren.

Virtuele-machine schaal sets met een uniforme indeling gebruik een virtuele-machine profiel of-sjabloon om omhoog te schalen naar de gewenste capaciteit. Hoewel het mogelijk is om afzonderlijke exemplaren van virtuele machines te beheren of aan te passen, gebruikt eenvormig identieke VM-exemplaren. Afzonderlijke uniforme VM-exemplaren worden weer gegeven via de VM-API-opdrachten voor de virtuele-machine schaal sets. Afzonderlijke exemplaren zijn niet compatibel met de standaard Azure IaaS VM API-opdrachten, Azure-beheer functies zoals Azure Resource Manager resource tagging RBAC-machtigingen, Azure Backup of Azure Site Recovery. Uniforme indeling biedt garanties voor een hoge Beschik baarheid van fout domeinen wanneer deze zijn geconfigureerd met minder dan 100 exemplaren. Uniforme indeling is algemeen beschikbaar en biedt ondersteuning voor een breed scala aan beheer en indeling van schaal sets, inclusief metrische gegevens op basis van automatisch schalen, beveiliging tegen instanties en automatische upgrades van besturings systemen. 


## <a name="scale-sets-with-flexible-orchestration"></a>Schaal sets met flexibele indeling 
Behaal hoge Beschik baarheid op schaal met identieke of meerdere typen virtuele machines.

Met flexibele indeling biedt Azure een uniforme ervaring in het VM-ecosysteem van Azure. Flexibele indeling biedt garanties voor hoge Beschik baarheid (Maxi maal 1000 Vm's) door Vm's te verspreiden over fout domeinen in een regio of binnen een beschikbaarheids zone. Op deze manier kunt u uw toepassing opschalen terwijl u de isolatie van het fout domein inschakelt. Dit is essentieel voor het uitvoeren van op quorum of stateful werk belastingen, waaronder:
- Op quorum gebaseerde workloads
- Open-Source-data bases
- Stateful toepassingen
- Services waarvoor hoge Beschik baarheid en grote schaal vereist zijn
- Services die virtuele-machine typen willen combi neren of gebruikmaken van on-demand Vm's samen
- Bestaande toepassingen voor Beschikbaarheidsset  

> [!IMPORTANT]
> Virtuele-machine schaal sets in de flexibele Orchestration-modus is momenteel beschikbaar als open bare preview. Een opt-in-procedure is vereist voor het gebruik van de open bare Preview-functionaliteit die hieronder wordt beschreven.
> Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Wat is er gewijzigd met flexibele Orchestration-modus?
Een van de belangrijkste voor delen van flexibele indeling is dat deze functies biedt voor de indeling van de standaard virtuele machines van Azure IaaS, in plaats van een schaalset van onderliggende Vm's. Dit betekent dat u alle standaard-VM-Api's kunt gebruiken bij het beheren van flexibele indelings instanties, in plaats van de VM-Api's van de virtuele machine Scale set die u gebruikt met een uniforme indeling. Tijdens de preview-periode zijn er verschillende verschillen tussen het beheren van instanties in flexibele indeling en eenvormige indeling. Over het algemeen raden we u aan om de standaard Azure IaaS VM-Api's waar mogelijk te gebruiken. In deze sectie worden voor beelden van aanbevolen procedures voor het beheren van VM-instanties met flexibele indeling gemarkeerd.  

### <a name="assign-fault-domain-during-vm-creation"></a>Fout domein toewijzen tijdens maken van VM
U kunt het aantal fout domeinen voor de flexibele Orchestration-schaalset kiezen. Wanneer u een virtuele machine aan een flexibele schaalset toevoegt, worden de standaard exemplaren gelijkmatig verdeeld over fout domeinen. We raden u aan Azure het fout domein toe te wijzen, voor geavanceerde of probleemoplossings scenario's kunt u dit standaard gedrag negeren en het fout domein opgeven waar het exemplaar wordt gelandd.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Exemplaar naamgeving 
Wanneer u een virtuele machine maakt en toevoegt aan een flexibele schaalset, hebt u volledige controle over exemplaar namen binnen de regels van de Azure-naamgevings Conventie. Wanneer Vm's automatisch worden toegevoegd aan de schaalset via automatische schaling, geeft u een voor voegsel op en voegt Azure een uniek nummer toe aan het einde van de naam.

### <a name="query-instances-for-power-state"></a>Query instanties voor energie status
De aanbevolen methode is om Azure resource Graph te gebruiken voor het opvragen van alle virtuele machines in een Schaalset met virtuele machines. Azure resource Graph biedt efficiënte query mogelijkheden voor Azure-resources op schaal in verschillende abonnementen. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Het opvragen van resources met [Azure resource Graph](../governance/resource-graph/overview) is een handige en efficiënte manier om een query uit te voeren op Azure-resources en API-aanroepen naar de resource provider te minimaliseren. Azure resource Graph is een uiteindelijk consistente cache waar nieuwe of bijgewerkte bronnen niet meer dan 60 seconden worden weer gegeven. U kunt:
- Een lijst met virtuele machines in een resource groep of een abonnement.
- Gebruik de Uitvouw optie om de weer gave van het exemplaar (fout domein toewijzing, energie-en inrichtings status) op te halen voor alle virtuele machines in uw abonnement.
- Gebruik de API en opdrachten voor het ophalen van de model-en instantie weergave voor één exemplaar.

### <a name="scale-sets-vm-batch-operations"></a>VM-batch bewerkingen schalen
Gebruik de standaard-VM-opdrachten om exemplaren te starten, te stoppen, opnieuw op te starten, te verwijderen in plaats van de VM-Api's van de virtuele-machine Schaalset. De VM-batch bewerkingen voor virtuele-machine schaal sets (alle starten, alle alle installatie kopieën, enzovoort) worden niet gebruikt met de flexibele Orchestration-modus. 

### <a name="monitor-application-health"></a>De status van de toepassing bewaken 
Met de functie voor status controle van toepassingen kan uw toepassing Azure voorzien van een heartbeat om te bepalen of uw toepassing in orde of beschadigd is. Azure kan automatisch de VM-instanties vervangen die niet in orde zijn. Voor flexibele instanties van schaal sets moet u de toepassings status extensie op de virtuele machine installeren en configureren. Voor instanties van een uniforme schaalset kunt u de uitbrei ding voor de toepassings status gebruiken of de status meten met een Azure Load Balancer aangepaste status test. 

### <a name="list-scale-sets-vm-api-changes"></a>Wijzigingen in VM-API'S voor lijst schaal sets 
Met Virtual Machine Scale Sets kunt u de exemplaren weer geven die deel uitmaken van de schaalset. Met flexibele indeling biedt de lijst Virtual Machine Scale Sets VM-opdracht een lijst met VM-Id's van schaal sets. Vervolgens roept u de opdrachten GET Virtual Machine Scale Sets VM aan om meer informatie te krijgen over de manier waarop de schaalset werkt met het VM-exemplaar. Als u de volledige details van de virtuele machine wilt weer geven, gebruikt u de Standard-opdracht GET VM of [Azure resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview). 

### <a name="retrieve-boot-diagnostics-data"></a>Diagnostische gegevens over opstarten ophalen 
Gebruik de standaard VM-Api's en-opdrachten om de diagnostische gegevens en scherm afbeeldingen voor het opstarten van de instantie op te halen. De Virtual Machine Scale Sets-Api's en-opdrachten voor het opstarten van de VM worden niet gebruikt met flexibele instanties voor de indelings modus.

### <a name="vm-extensions"></a>VM-extensies 
Gebruik uitbrei dingen die zijn gericht op standaard virtuele machines, in plaats van extensies die zijn gericht op uniforme indelings modus-instanties.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Een vergelijking van flexibele, uniforme en beschikbaarheids sets 
De volgende tabel vergelijkt de flexibele Orchestration-modus, uniforme indelings modus en beschikbaarheids sets op basis van hun functies.

| Functie | Ondersteund door flexibele indeling (preview-versie) | Ondersteund door uniforme indeling (algemene Beschik baarheid) | Ondersteund door AvSets (algemene Beschik baarheid) |
|-|-|-|-|
|         Type virtuele machine  | Standard Azure IaaS VM (micro soft. Compute/virtualmachines)  | Schaal sets specifieke Vm's (micro soft. Compute/virtualmachinescalesets/virtualmachines)  | Standard Azure IaaS VM (micro soft. Compute/virtualmachines)  |
|         SKU's ondersteund  |            D-serie, E-reeks, F-serie, A-serie, B-serie, Intel, AMD  |            Alle Sku's  |            Alle Sku's  |
|         Beschikbaarheidszones  |            Geef eventueel alle exemplaren van het land op in één beschikbaarheids zone |            Geef exemplaren van het land over 1, 2 of 3 Beschikbaarheids zones op  |            Niet ondersteund  |
|         Volledige controle over VM, Nic's, schijven  |            Ja  |            Beperkte controle met virtuele-machine schaal sets VM API  |            Ja  |
|         Automatisch schalen  |            Nee  |            Ja  |            Nee  |
|         Virtuele machine aan een specifiek fout domein toewijzen  |            Ja  |             Nee   |            Nee  |
|         Nic's en schijven verwijderen bij het verwijderen van VM-exemplaren  |            Nee  |            Ja  |            Nee  |
|         Upgrade beleid (VM-schaal sets) |            Nee  |            Automatisch, Rolling, hand matig  |            N.v.t.  |
|         Automatische updates van het besturings systeem (VM-schaal sets) |            Nee  |            Ja  |            N.v.t.  |
|         In gast beveiligings patches  |            Ja  |            Nee  |            Ja  |
|         Meldingen beëindigen (VM-schaal sets) |            Nee  |            Ja  |            N.v.t.  |
|         Exemplaar herstellen (VM-schaal sets) |            Nee  |            Ja   |            N.v.t.  |
|         Versneld netwerken  |            Ja  |            Ja  |            Ja  |
|         Steun instanties en prijzen   |            Ja, u kunt exemplaren met een positie en normale prioriteit hebben  |            Ja, instanties moeten al een plaats of gewoon zijn  |            Nee, alleen reguliere prioriteits instanties  |
|         Besturings systemen combi neren  |            Ja, Linux en Windows kunnen zich in dezelfde flexibele schaalset bevinden |            Nee, exemplaren zijn hetzelfde besturings systeem  |               Ja, Linux en Windows kunnen zich in dezelfde flexibele schaalset bevinden |
|         Toepassings status bewaken  |            Toepassings status uitbreiding  |            Status extensie van de toepassing of Azure Load Balancer-test  |            Toepassings status uitbreiding  |
|         UltraSSD-schijven   |            Ja  |            Ja, alleen voor zonegebonden-implementaties  |            Nee  |
|         InfiniBand   |            Nee  |            Ja, alleen één plaatsings groep  |            Ja  |
|         Write Accelerator   |            Nee  |            Ja  |            Ja  |
|         Proximity-plaatsings groepen   |            Ja  |            Ja  |            Ja  |
|         Met Azure toegewezen hosts   |            Nee  |            Ja  |            Ja  |
|         Basic SLB   |            Nee  |            Ja  |            Ja  |
|         Azure Load Balancer standaard-SKU |            Ja  |            Ja  |            Ja  |
|         Application Gateway  |            Nee  |            Ja  |            Ja  |
|         Onderhouds beheer   |            Nee  |            Ja  |            Ja  |
|         Vm's in set weer geven  |            Ja  |            Ja  |            Ja, virtuele machines weer geven in AvSet  |
|         Azure-waarschuwingen  |            Nee  |            Ja  |            Ja  |
|         VM Insights  |            Nee  |            Ja  |            Ja  |
|         Azure Backup  |            Ja  |            Ja  |            Ja  |
|         Azure Site Recovery  |            Ja, alleen Power shell  |            Ja  |            Ja  |
|         Bestaande virtuele machine toevoegen aan/verwijderen uit de groep  |            Nee  |            Nee  |            Nee  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registreren voor flexibele Orchestration-modus
Voordat u schaal sets voor virtuele machines kunt implementeren in de flexibele Orchestration-modus, moet u eerst uw abonnement registreren voor de preview-functie. Het volt ooien van de registratie kan enkele minuten duren. U kunt de volgende Azure PowerShell of Azure CLI-opdrachten gebruiken om te registreren.

### <a name="azure-powershell"></a>Azure PowerShell 
Gebruik de cmdlet [REGI ster-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in te scha kelen. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
Gebruik [AZ feature Regis](/cli/azure/feature#az-feature-register) om de preview voor uw abonnement in te scha kelen. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Aan de slag met flexibele Orchestration-modus

Ga aan de slag met flexibele Orchestration-modus voor uw schaal sets via de Azure Portal, Azure CLI, terraform of REST API.

### <a name="azure-portal"></a>Azure Portal

Maak een schaalset voor virtuele machines in de flexibele Orchestration-modus via de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **virtuele-machine schaal sets** in de zoek balk. 
1. Selecteer **maken** op de pagina **schaal sets voor virtuele machines** .
1. Op de pagina **een virtuele-machine schaal set maken** bekijkt u de sectie **Orchestration** .
1. Voor de **Orchestration-modus** selecteert u de optie **flexibele** .
1. Het **aantal fout domeinen** instellen.
1. Het maken van de schaalset volt ooien. Zie [een schaalset maken in de Azure Portal](quick-create-portal.md#create-virtual-machine-scale-set) voor meer informatie over het maken van een schaalset.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Orchestration-modus in portal bij het maken van een schaalset":::

Voeg vervolgens een virtuele machine toe aan de schaalset in de flexibele Orchestration-modus.

1. Zoek en selecteer **virtuele machines** in de zoek balk.
1. Selecteer **toevoegen** op de pagina **virtuele machines** .
1. Op het tabblad **basis beginselen** bekijkt u de sectie **Details van exemplaar** .
1. Voeg uw virtuele machine toe aan de schaalset in de flexibele Orchestration-modus door de schaalset te selecteren in de **beschikbaarheids opties**. U kunt de virtuele machine toevoegen aan een schaalset in dezelfde regio, zone en resource groep.
1. Het maken van de virtuele machine volt ooien. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Een virtuele machine toevoegen aan de schaalset flexibele Orchestration-modus":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Een flexibele virtuele-machine schaalset maken met Azure CLI. In het volgende voor beeld ziet u hoe een flexibele schaalset wordt gemaakt waarbij het aantal fout domeinen is ingesteld op 3, er een virtuele machine wordt gemaakt en vervolgens wordt toegevoegd aan de flexibele schaalset. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode vm --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Maak een flexibele virtuele-machine schaalset met terraform. Dit proces vereist **terraform Azurerm provider v 2.15.0** of hoger. Houd rekening met de volgende para meters:
- Als er geen zone is opgegeven, `platform_fault_domain_count` kan 1, 2 of 3 zijn, afhankelijk van de regio.
- Wanneer een zone is opgegeven, `the fault domain count` kan 1 zijn.
- `single_placement_group` de para meter moet `false` voor flexibele virtuele-machine schaal sets zijn.
- Als u een regionale implementatie uitvoert, hoeft u niet op te geven `zones` .

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

1. Een lege schaalset maken. De volgende parameters zijn vereist:
    - API-versie 2019-12-01 (of hoger) 
    - Eén plaatsings groep moet zijn `false` Wanneer u een flexibele schaalset maakt

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
    1. Wijs de `virtualMachineScaleSet` eigenschap toe aan de schaalset die u eerder hebt gemaakt. U moet de `virtualMachineScaleSet` eigenschap opgeven op het moment dat de VM wordt gemaakt. 
    1. U kunt de functie **Copy () Azure Resource Manager-** sjabloon gebruiken om meerdere vm's tegelijk te maken. Zie [resource herhaling](https://docs.microsoft.com/azure/azure-resource-manager/templates/copy-resources#iteration-for-a-child-resource) in azure Resource Manager sjablonen. 

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

Zie [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) start voor een volledig voor beeld.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Wat is de schaal van flexibele Orchestration-ondersteuning?**

U kunt Maxi maal 1000 Vm's toevoegen aan een schaalset in de flexibele Orchestration-modus.

**Hoe kan de beschik baarheid met flexibele integratie worden vergeleken met beschikbaarheids sets of een uniforme indeling?**

|   | Flexibele indeling  | Uniforme indeling  | Beschikbaarheidssets  |
|-|-|-|-|
| Implementeren in verschillende beschikbaarheids zones  | Nee  | Ja  | Nee  |
| Gegarandeerde Beschik baarheid van fouten domein binnen een regio  | Ja, Maxi maal 1000 exemplaren kunnen worden gespreid over Maxi maal drie fout domeinen in de regio. Maximum aantal fout domeinen varieert per regio  | Ja, Maxi maal 100 exemplaren  | Ja, Maxi maal 200 exemplaren  |
| Plaatsingsgroepen  | Flexibele modus maakt altijd gebruik van meerdere plaatsings groepen (singlePlacementGroup = false)  | U kunt kiezen uit één plaatsings groep of meerdere plaatsings groepen | N.v.t.  |
| Updatedomeinen  | Geen, onderhoud of host-updates worden uitgevoerd fout domein per fout domein  | Maxi maal 5 update domeinen  | Maxi maal 20 Update domeinen  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Problemen met schaal sets oplossen met flexibele indeling
Zoek de juiste oplossing voor uw probleemoplossings scenario. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Oorzaak:** Het abonnement is niet geregistreerd voor de open bare preview van de flexibele Orchestration-modus. 

**Oplossing:** Volg de bovenstaande instructies om u te registreren voor de open bare preview van de flexibele Orchestration-modus. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Oorzaak:** De `platformFaultDomainCount` para meter is ongeldig voor de geselecteerde regio of zone. 

**Oplossing:** U moet een geldige `platformFaultDomainCount` waarde selecteren. Voor zonegebonden-implementaties is de maximum `platformFaultDomainCount` waarde 1. Voor regionale implementaties waarvoor geen zone is opgegeven, is het maximum `platformFaultDomainCount` afhankelijk van de regio. Zie [de beschik baarheid van vm's voor scripts beheren](../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) om het maximum aantal fout domeinen per regio te bepalen. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Oorzaak:** Poging tot het verwijderen van een schaalset in de flexibele Orchestration-modus die is gekoppeld aan een of meer virtuele machines. 

**Oplossing:** Verwijder alle virtuele machines die zijn gekoppeld aan de schaalset in de flexibele Orchestration-modus. vervolgens kunt u de schaalset verwijderen.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Oorzaak:** Het abonnement is geregistreerd voor de flexibele Orchestration-modus preview. de `singlePlacementGroup` para meter is echter ingesteld op *True*. 

**Oplossing:** De `singlePlacementGroup` moet worden ingesteld op *Onwaar*. 


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van uw toepassing in een schaalset.](virtual-machine-scale-sets-deploy-app.md)
