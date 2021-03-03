---
title: Automatische extensie-upgrade voor Vm's en schaal sets in azure
description: Meer informatie over het inschakelen van de automatische extensie-upgrade voor uw virtuele machines en virtuele-machine schaal sets in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrades
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: 104eada6dc342c21b8da2f409756e9f34c103936
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668330"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Preview: automatische upgrade van de extensie voor Vm's en schaal sets in azure

Automatische extensie-upgrades zijn beschikbaar als preview-versie van Azure Vm's en Azure Virtual Machine Scale Sets. Als automatische extensie-upgrade is ingeschakeld op een VM of schaalset, wordt de extensie automatisch bijgewerkt wanneer de uitbrei ding Publisher een nieuwe versie voor die uitbrei ding uitgeeft.

 Automatische extensie-upgrade heeft de volgende functies:
- Ondersteund voor Azure-Vm's en Azure-Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets worden momenteel niet ondersteund.
- Upgrades worden toegepast in een Beschik baarheid-eerste implementatie model (hieronder beschreven).
- Voor een Schaalset voor een virtuele machine wordt niet meer dan 20% van de virtuele machines van de schaalset in één batch bijgewerkt. De minimale Batch grootte is één virtuele machine.
- Werkt voor alle VM-grootten en voor Windows-en Linux-uitbrei dingen.
- U kunt op elk gewenst moment automatische upgrades afmelden.
- Automatische extensie-upgrades kunnen worden ingeschakeld voor een Virtual Machine Scale Sets van elke grootte.
- Elke ondersteunde uitbrei ding wordt afzonderlijk geregistreerd en u kunt kiezen welke extensies automatisch moeten worden bijgewerkt.
- Ondersteund in alle open bare Cloud regio's.


> [!IMPORTANT]
> Automatische extensie-upgrade is momenteel beschikbaar als open bare preview. Een opt-in-procedure is vereist voor het gebruik van de open bare Preview-functionaliteit die hieronder wordt beschreven.
> Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="how-does-automatic-extension-upgrade-work"></a>Hoe werkt automatische extensie-upgrade?
Het upgrade proces van de extensie vervangt de bestaande extensie versie op een virtuele machine door een nieuwe versie van dezelfde extensie wanneer deze door de extensie uitgever wordt gepubliceerd. De status van de virtuele machine wordt gecontroleerd nadat de nieuwe uitbrei ding is geïnstalleerd. Als de virtuele machine binnen vijf minuten na de voltooiing van de upgrade niet in orde is, wordt de versie van de uitbrei ding teruggezet naar de vorige versie.

Er wordt automatisch een mislukte extensie-update uitgevoerd. Een nieuwe poging wordt om de paar dagen automatisch uitgevoerd zonder tussen komst van de gebruiker.

### <a name="availability-first-updates"></a>Beschik baarheid-eerste updates
Het model Beschik baarheid-eerste voor platform gemodelleerde updates zorgt ervoor dat beschikbaarheids configuraties in Azure worden geëerbiedigd op meerdere beschikbaarheids niveaus.

Voor een groep virtuele machines die een update ondergaat, worden de updates door het Azure-platform georganiseert:

**In verschillende regio's:**
- Een update gaat overal in azure wereld wijd over om te voor komen dat implementatie fouten in azure optreden.
- Een ' fase ' kan een of meer regio's hebben en een update wordt alleen over fasen verplaatst als de in aanmerking komende virtuele machines in de vorige fase zijn bijgewerkt.
- Geografisch gekoppelde regio's worden niet gelijktijdig bijgewerkt en kunnen zich niet in dezelfde regionale fase betreden.
- Het slagen van een update wordt gemeten door de status van een update van een VM na te houden. De status van de virtuele machine wordt getraceerd via platform status indicatoren voor de virtuele machine. Voor Virtual Machine Scale Sets wordt de status van de virtuele machine gevolgd door de status tests van de toepassing of de uitbrei ding van de toepassings status, indien toegepast op de schaalset.

**Binnen een regio:**
- Vm's in verschillende Beschikbaarheidszones worden niet gelijktijdig bijgewerkt.
- Enkele Vm's die geen deel uitmaken van een beschikbaarheidsset, worden op basis van de beste inspanningen gebatcheerd om gelijktijdige updates voor alle Vm's in een abonnement te voor komen.  

**In een ' set ':**
- Alle Vm's in een gemeen schappelijke beschikbaarheidsset of schaal sets worden niet gelijktijdig bijgewerkt.  
- Vm's in een gemeen schappelijke beschikbaarheidsset worden bijgewerkt binnen update domein grenzen en Vm's in meerdere update domeinen worden niet gelijktijdig bijgewerkt.  
- Vm's in een gemeen schappelijke schaalset voor virtuele machines worden gegroepeerd in batches en bijgewerkt binnen de grenzen van het update domein.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Upgrade proces voor Virtual Machine Scale Sets
1. Voordat u met het upgrade proces begint, zorgt u ervoor dat niet meer dan 20% van de virtuele machines in de gehele schaalset een slechte status heeft (om welke reden dan ook).

2. De upgrade Orchestrator identificeert de batch van de VM-exemplaren die moeten worden bijgewerkt. Een upgrade batch kan Maxi maal 20% van het totale aantal VM'S bevatten, afhankelijk van een minimale Batch grootte van één virtuele machine.

3. Voor schaal sets met geconfigureerde status tests voor toepassingen of een uitbrei ding van de toepassings status, wacht de upgrade tot vijf minuten (of de gedefinieerde status test configuratie) zodat de virtuele machine in orde is voordat de volgende batch wordt bijgewerkt. Als een virtuele machine de status niet herstelt na een upgrade, wordt de vorige uitbrei ding versie op de VM standaard opnieuw geïnstalleerd.

4. De upgrade Orchestrator houdt ook het percentage Vm's bij dat na een upgrade niet meer in orde is. De upgrade wordt gestopt als er meer dan 20% van de bijgewerkte exemplaren een slechte status krijgt tijdens het upgrade proces.

Het bovenstaande proces wordt voortgezet totdat alle exemplaren in de schaalset zijn bijgewerkt.

Met de upgrade functie van de schaalset wordt de status van de algehele schaalset gecontroleerd voordat elke batch wordt geüpgraded. Tijdens het bijwerken van een batch kunnen er andere gelijktijdige geplande of niet-geplande onderhouds activiteiten optreden die van invloed kunnen zijn op de status van de virtuele machines van uw schaalset. Als er in dergelijke gevallen meer dan 20% van de instanties van de schaalset een slechte status heeft, stopt de upgrade van de schaalset aan het einde van de huidige batch.

## <a name="supported-extensions"></a>Ondersteunde extensies
De preview-versie van automatische extensie-upgrades ondersteunt de volgende extensies (en er worden regel matig meer toegevoegd):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) en [Linux](./extensions/agent-dependency-linux.md)
- [Toepassings status extensie](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) : Windows en Linux


## <a name="enabling-preview-access"></a>Preview-toegang inschakelen
Het inschakelen van de Preview-functionaliteit vereist eenmalige aanmelding voor de functie **AutomaticExtensionUpgradePreview** per abonnement, zoals hieronder wordt beschreven.

### <a name="rest-api"></a>REST-API
In het volgende voor beeld wordt beschreven hoe u de preview-versie van uw abonnement kunt inschakelen:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [REGI ster-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in te scha kelen.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
Gebruik [AZ feature Regis](/cli/azure/feature#az-feature-register) om de preview voor uw abonnement in te scha kelen.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Automatische upgrade van de extensie inschakelen
Om automatische extensie-upgrade voor een uitbrei ding in te scha kelen, moet u ervoor zorgen dat de eigenschap *enableAutomaticUpgrade* is ingesteld op *True* en wordt toegevoegd aan elke extensie definitie afzonderlijk.


### <a name="rest-api-for-virtual-machines"></a>REST API voor Virtual Machines
Als u automatische extensie-upgrade voor een uitbrei ding (in dit voor beeld de Dependency Agent-extensie) wilt inschakelen op een virtuele Azure-machine, gebruikt u het volgende:

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
Gebruik het volgende om de extensie toe te voegen aan het model met de schaalset:

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
Gebruik de cmdlet [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) :

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
Gebruik de cmdlet [add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de extensie toe te voegen aan het model met de schaalset:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Werk de schaalset bij met [Update-AzVmss](/powershell/module/az.compute/update-azvmss) nadat u de extensie hebt toegevoegd.


### <a name="azure-cli-for-virtual-machines"></a>Azure CLI voor Virtual Machines
Gebruik de cmdlet [AZ VM extension set](/cli/azure/vm/extension#az_vm_extension_set) :

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
Gebruik de cmdlet [AZ vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) om de extensie toe te voegen aan het model met de schaalset:

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

Een VM-of virtuele-machine Schaalset kan meerdere uitbrei dingen hebben waarvoor automatische extensie-upgrade is ingeschakeld. Dezelfde VM of schaalset kan ook andere uitbrei dingen hebben zonder dat automatische extensie-upgrade is ingeschakeld.  

Als er meerdere uitbreidings uitbreidingen beschikbaar zijn voor een virtuele machine, kunnen de upgrades samen worden gebatcheerd, maar elke upgrade van de uitbrei ding wordt afzonderlijk toegepast op een virtuele machine. Een fout in de ene extensie heeft geen invloed op de andere uitbrei dingen die kunnen worden bijgewerkt. Als er bijvoorbeeld twee uitbrei dingen zijn gepland voor een upgrade en de eerste upgrade van de extensie mislukt, wordt de tweede uitbrei ding nog steeds bijgewerkt.

Automatische extensie-upgrades kunnen ook worden toegepast wanneer een VM-of virtuele-machine schaalset meerdere extensies heeft geconfigureerd met [uitbrei ding van extensie](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Extensie volgorde bepaling is van toepassing op de eerste implementatie van de virtuele machine, en eventuele toekomstige verlengings upgrades voor een uitbrei ding worden onafhankelijk toegepast.


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over de toepassings status uitbreiding](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
