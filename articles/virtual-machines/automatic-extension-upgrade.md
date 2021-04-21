---
title: Automatische extensie-upgrade voor VM's en schaalsets in Azure
description: Meer informatie over het inschakelen van de automatische extensie-upgrade voor uw virtuele machines en virtuele-machineschaalsets in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: bf9e802e2485e84211044ce650c7748e789e752e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762601"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Preview: Automatische extensie-upgrade voor VM's en schaalsets in Azure

Automatische extensie-upgrade is beschikbaar in preview voor Azure-VM's en Azure Virtual Machine Scale Sets. Wanneer Automatische extensie-upgrade is ingeschakeld op een VM of schaalset, wordt de extensie automatisch bijgewerkt wanneer de uitgever van de extensie een nieuwe versie voor die extensie uit brengt.

 Automatische extensie-upgrade heeft de volgende functies:
- Ondersteund voor Azure-VM's en Azure Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets worden momenteel niet ondersteund.
- Upgrades worden toegepast in een implementatiemodel met de eerste beschikbaarheid (hieronder beschreven).
- Voor een virtuele-machineschaalset wordt niet meer dan 20% van de virtuele machines van de schaalset in één batch bijgewerkt. De minimale batchgrootte is één virtuele machine.
- Werkt voor alle VM-grootten en voor zowel Windows- als Linux-extensies.
- U kunt op elk moment kiezen voor automatische upgrades.
- Automatische extensie-upgrade kan worden ingeschakeld op een Virtual Machine Scale Sets elke grootte.
- Elke ondersteunde extensie wordt afzonderlijk geregistreerd en u kunt kiezen welke extensies automatisch moeten worden bijgewerkt.
- Ondersteund in alle openbare cloudregio's.


> [!IMPORTANT]
> Automatische extensie-upgrade is momenteel beschikbaar als openbare preview. Er is een opt-in-procedure nodig om de openbare preview-functionaliteit te gebruiken die hieronder wordt beschreven.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="how-does-automatic-extension-upgrade-work"></a>Hoe werkt automatische extensie-upgrade?
Het upgradeproces van de extensie vervangt de bestaande extensieversie op een VM door een nieuwe versie van dezelfde extensie wanneer deze wordt gepubliceerd door de uitgever van de extensie. De status van de VM wordt bewaakt nadat de nieuwe extensie is geïnstalleerd. Als de status van de VM niet binnen vijf minuten na voltooiing van de upgrade in orde is, wordt de versie van de extensie teruggedraaid naar de vorige versie.

Er wordt automatisch een mislukte extensie-update opnieuw proberen uit te proberen. Er wordt om de paar dagen automatisch een nieuwe poging gedaan zonder tussenkomst van de gebruiker.

### <a name="availability-first-updates"></a>Beschikbaarheids-eerste updates
Het beschikbaarheids-eerste model voor platformgeschikt updates zorgt ervoor dat beschikbaarheidsconfiguraties in Azure worden gerespecteerd op meerdere beschikbaarheidsniveaus.

Voor een groep virtuele machines die een update ondergaan, worden updates door het Azure-platform geseed:

**Tussen regio's:**
- Een update wordt gefaseerd over Azure verplaatst om implementatiefouten voor de hele Azure-regio te voorkomen.
- Een 'fase' kan een of meer regio's hebben en een update wordt alleen gefaseerd uitgevoerd als de in aanmerking komende VM's in de vorige fase zijn bijgewerkt.
- Geografisch gekoppelde regio's worden niet gelijktijdig bijgewerkt en kunnen zich niet in dezelfde regionale fase.
- Het succes van een update wordt gemeten door de status van een VM na de update bij te houden. De VM-status wordt bij te houden via platform health indicators voor de VM. Voor Virtual Machine Scale Sets wordt de status van de VM bij te houden via toepassings statustests of de toepassingstoestandextensie, indien toegepast op de schaalset.

**Binnen een regio:**
- VM's in Beschikbaarheidszones worden niet gelijktijdig bijgewerkt.
- Eén VM die geen deel uitmaakt van een beschikbaarheidsset wordt op basis van een batch in batch uitgevoerd om gelijktijdige updates voor alle VM's in een abonnement te voorkomen.  

**Binnen een 'set':**
- Alle VM's in een gemeenschappelijke beschikbaarheidsset of schaalset worden niet gelijktijdig bijgewerkt.  
- VM's in een algemene beschikbaarheidsset worden bijgewerkt binnen de grenzen van updatedomeinen en VM's in meerdere updatedomeinen worden niet gelijktijdig bijgewerkt.  
- VM's in een algemene virtuele-machineschaalset worden gegroepeerd in batches en bijgewerkt binnen de grenzen van updatedomeinen.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Upgradeproces voor Virtual Machine Scale Sets
1. Voordat u het upgradeproces start, zorgt de orchestrator ervoor dat niet meer dan 20% van de VM's in de hele schaalset een slechte status heeft (om welke reden dan ook).

2. De upgrade-orchestrator identificeert de batch VM-exemplaren die moeten worden geupgraded. Een upgradebatch kan maximaal 20% van het totale aantal VM's hebben, afhankelijk van een minimale batchgrootte van één virtuele machine.

3. Voor schaalsets met geconfigureerde statustests of application health-extensies wacht de upgrade maximaal vijf minuten (of de gedefinieerde statustestconfiguratie) totdat de VM in orde is voordat de volgende batch wordt geupgraded. Als een VM de status na een upgrade niet herstelt, wordt standaard de vorige extensieversie op de VM opnieuw geïnstalleerd.

4. De upgrade-orchestrator houdt ook het percentage VM's bij dat na een upgrade niet in orde is. De upgrade wordt gestopt als meer dan 20% van de bijgewerkte exemplaren tijdens het upgradeproces niet in orde is.

Het bovenstaande proces wordt voortgezet totdat alle exemplaren in de schaalset zijn bijgewerkt.

De orchestrator voor schaalsetupgrade controleert de algehele status van de schaalset voordat elke batch wordt geupgraded. Tijdens het upgraden van een batch kunnen er andere gelijktijdige geplande of ongeplande onderhoudsactiviteiten zijn die van invloed kunnen zijn op de status van uw virtuele machines in de schaalset. Als in dergelijke gevallen meer dan 20% van de exemplaren van de schaalset niet in orde is, stopt de upgrade van de schaalset aan het einde van de huidige batch.

## <a name="supported-extensions"></a>Ondersteunde extensies
De preview van Automatische extensie-upgrade ondersteunt de volgende extensies (en er worden periodiek meer toegevoegd):
- Dependency Agent: [Windows](./extensions/agent-dependency-windows.md) en [Linux](./extensions/agent-dependency-linux.md)
- [Toepassings health-extensie](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) - Windows en Linux


## <a name="enabling-preview-access"></a>Preview-toegang inschakelen
Voor het inschakelen van de preview-functionaliteit is een een time-opt-in vereist voor de functie **AutomaticExtensionUpgradePreview** per abonnement, zoals hieronder wordt beschreven.

### <a name="rest-api"></a>REST-API
In het volgende voorbeeld wordt beschreven hoe u de preview-versie voor uw abonnement inschakelen:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in te stellen.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
Gebruik [az feature register om](/cli/azure/feature#az_feature_register) de preview voor uw abonnement in te stellen.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Automatische extensie-upgrade inschakelen
Als u Automatische extensie-upgrade wilt inschakelen voor een extensie, moet u ervoor zorgen dat de eigenschap *enableAutomaticUpgrade* is ingesteld op *true* en afzonderlijk aan elke extensiedefinitie wordt toegevoegd.


### <a name="rest-api-for-virtual-machines"></a>REST API voor Virtual Machines
Als u automatische extensie-upgrade wilt inschakelen voor een extensie (in dit voorbeeld de extensie Dependency Agent) op een Azure-VM, gebruikt u het volgende:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>REST API voor Virtual Machine Scale Sets
Gebruik het volgende om de extensie toe te voegen aan het schaalsetmodel:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell voor Virtual Machines
Gebruik de [cmdlet Set-AzVMExtension:](/powershell/module/az.compute/set-azvmextension)

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell voor Virtual Machine Scale Sets
Gebruik de cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de extensie toe te voegen aan het schaalsetmodel:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Werk de schaalset bij met [Update-AzVmss nadat](/powershell/module/az.compute/update-azvmss) u de extensie toevoegt.


### <a name="azure-cli-for-virtual-machines"></a>Azure CLI voor Virtual Machines
Gebruik de [cmdlet az vm extension set:](/cli/azure/vm/extension#az_vm_extension_set)

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Azure CLI voor Virtual Machine Scale Sets
Gebruik de [cmdlet az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) om de extensie toe te voegen aan het schaalsetmodel:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Extensie-upgrades met meerdere extensies

Een VM of virtuele-machineschaalset kan meerdere extensies hebben met automatische extensie-upgrade ingeschakeld. Dezelfde VM of schaalset kan ook andere extensies hebben zonder dat automatische extensie-upgrade is ingeschakeld.  

Als er meerdere extensie-upgrades beschikbaar zijn voor een virtuele machine, kunnen de upgrades tegelijk worden gebatcheerd, maar elke extensie-upgrade wordt afzonderlijk toegepast op een virtuele machine. Een fout in de ene extensie heeft geen invloed op de andere extensies die mogelijk worden ge upgraden. Als er bijvoorbeeld twee extensies zijn gepland voor een upgrade en de eerste extensie-upgrade mislukt, wordt de tweede extensie nog steeds bijgewerkt.

Automatische extensie-upgrades kunnen ook worden toegepast wanneer voor een virtuele machine of virtuele-machineschaalset meerdere extensies zijn geconfigureerd met [extensiesequencing.](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md) Extensie-sequencing is van toepassing op de eerste implementatie van de VM en eventuele toekomstige extensie-upgrades voor een extensie worden onafhankelijk van elkaar toegepast.


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over de toepassings health-extensie](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
