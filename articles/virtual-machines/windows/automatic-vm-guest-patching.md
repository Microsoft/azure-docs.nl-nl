---
title: Automatische VM-gast patches voor Windows-Vm's in azure
description: Meer informatie over het automatisch patchen van virtuele Windows-machines in azure
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 12/23/2020
ms.author: manayar
ms.openlocfilehash: e22e8b81382614c2930c72a8150606f859be501d
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762976"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>Preview: Automatische VM-gastpatches voor Windows VM's in Azure

Het inschakelen van automatische VM-gast patches voor uw Windows-Vm's helpt het update beheer te vereenvoudigen door een veilige en automatische reparatie van virtuele machines uit te voeren om beveiligings naleving te hand haven.

Automatische VM-gast patching heeft de volgende kenmerken:
- Patches die zijn geclassificeerd als *kritiek* of *beveiliging* , worden automatisch gedownload en toegepast op de virtuele machine.
- Patches worden toegepast tijdens rustige uren in de tijd zone van de virtuele machine.
- Patch indeling wordt beheerd door Azure en patches worden toegepast na de eerste principes van Beschik baarheid.
- De status van de virtuele machine, zoals wordt bepaald door platform status signalen, wordt bewaakt om patch fouten te detecteren.
- Werkt voor alle VM-grootten.

> [!IMPORTANT]
> Automatische VM-gast patching is momenteel beschikbaar als open bare preview. Een opt-in-procedure is vereist voor het gebruik van de open bare Preview-functionaliteit die hieronder wordt beschreven.
> Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="how-does-automatic-vm-guest-patching-work"></a>Hoe werkt automatische VM-gast patches?

Als automatische VM-gast patches is ingeschakeld op een VM, worden de beschik bare *essentiële* en *beveiligings* patches automatisch gedownload en toegepast op de VM. Dit proces wordt elke maand automatisch uitgevoerd wanneer er nieuwe patches worden uitgebracht via Windows Update. De evaluatie en installatie van patches worden automatisch uitgevoerd en het proces omvat het opnieuw opstarten van de virtuele machine, indien nodig.

De virtuele machine wordt regel matig elke paar dagen en meerdere keren binnen een periode van 30 dagen geëvalueerd om de toepasselijke patches voor die VM te bepalen. De patches kunnen elke wille keurige dag op de virtuele machine worden geïnstalleerd tijdens daluren van de virtuele machine. Deze automatische beoordeling zorgt ervoor dat eventuele ontbrekende patches zo snel mogelijk worden gedetecteerd.

Patches worden binnen 30 dagen na de maandelijkse Windows Update versie geïnstalleerd, met de volgende hieronder beschreven Beschik baarheid: eerste Orchestration. Patches worden alleen geïnstalleerd tijdens daluren voor de virtuele machine, afhankelijk van de tijd zone van de virtuele machine. De virtuele machine moet worden uitgevoerd gedurende de daluren van de patches die automatisch worden geïnstalleerd. Als een virtuele machine tijdens een periodieke evaluatie is uitgeschakeld, wordt de VM automatisch beoordeeld en worden toepasselijke patches automatisch geïnstalleerd tijdens de volgende periodieke evaluatie (meestal binnen een paar dagen) wanneer de virtuele machine is ingeschakeld.

Definitie-updates en andere patches die niet zijn geclassificeerd als *kritiek* of *beveiliging* , worden niet geïnstalleerd via de automatische VM-gast patching. Als u patches met andere patch classificaties of installatie van patches wilt installeren binnen uw eigen aangepaste onderhouds venster, kunt u [updatebeheer](tutorial-config-management.md#manage-windows-updates)gebruiken.

### <a name="availability-first-patching"></a>Beschik baarheid-eerste patching

Het installatie proces van de patch wordt globaal door Azure gemodelleerd voor alle Vm's waarvoor automatische VM-gast patching is ingeschakeld. Deze indeling volgt de beschik baarheid-eerste principes van verschillende beschik bare niveaus van Azure.

Voor een groep virtuele machines die een update ondergaat, worden de updates door het Azure-platform georganiseert:

**In verschillende regio's:**
- Een maandelijkse update wordt overal in Azure in een gefaseerde manier ingedeeld om fouten in de globale implementatie te voor komen.
- Een fase kan een of meer regio's hebben en een update wordt alleen verplaatst naar de volgende fasen als de in aanmerking komende Vm's in een fase zijn bijgewerkt.
- Geografisch gekoppelde regio's worden niet gelijktijdig bijgewerkt en kunnen zich niet in dezelfde regionale fase bevinden.
- Het slagen van een update wordt gemeten door de status post van de VM bij te houden. De status van de virtuele machine wordt getraceerd via platform status indicatoren voor de virtuele machine.

**Binnen een regio:**
- Vm's in verschillende Beschikbaarheidszones worden niet gelijktijdig bijgewerkt.
- Vm's die geen deel uitmaken van een beschikbaarheidsset, worden op basis van de beste inspanningen gebatcheerd om gelijktijdige updates te voor komen voor alle virtuele machines in een abonnement.

**Binnen een beschikbaarheidsset:**
- Alle Vm's in een gemeen schappelijke beschikbaarheidsset worden niet gelijktijdig bijgewerkt.
-   Vm's in een gemeen schappelijke beschikbaarheidsset worden bijgewerkt binnen update domein grenzen en Vm's in meerdere update domeinen worden niet gelijktijdig bijgewerkt.

De installatie datum van de patch voor een bepaalde virtuele machine kan variëren van maand tot maand, omdat een specifieke virtuele machine kan worden opgenomen in een andere batch tussen maandelijkse patch cycli.

## <a name="supported-os-images"></a>Ondersteunde installatie kopieën van besturings systemen
In de preview-versie worden alleen Vm's ondersteund die zijn gemaakt op basis van bepaalde installatie kopieën van het besturings systeem. Aangepaste installatie kopieën worden momenteel niet ondersteund in de preview-versie.

De volgende platform-Sku's worden momenteel ondersteund (en worden regel matig toegevoegd):

| Publisher               | OS-aanbieding      |  Sku               |
|-------------------------|---------------|--------------------|
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Data Center    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Data Center-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Data Center-core |

## <a name="patch-orchestration-modes"></a>Patch Orchestration-modi
Virtuele Windows-machines in azure ondersteunen nu de volgende patch indelings modi:

**AutomaticByPlatform:**
- Deze modus maakt automatische VM-gast patches mogelijk voor de virtuele Windows-machine en de volgende patch-installatie wordt georganiseerd door Azure.
- Deze modus is vereist voor de beschik baarheid-eerste patches.
- Als u deze modus instelt, wordt de systeem eigen Automatische updates ook uitgeschakeld op de virtuele Windows-machine om te voor komen dat er dubbele bewerkingen worden uitgevoerd.
- Deze modus wordt alleen ondersteund voor virtuele machines die worden gemaakt met behulp van de ondersteunde installatie kopieën van het besturings systeem.
- Als u deze modus wilt gebruiken, stelt u de eigenschap `osProfile.windowsConfiguration.enableAutomaticUpdates=true` in en stelt u de eigenschap  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` in de VM-sjabloon in.

**AutomaticByOS:**
- In deze modus wordt Automatische updates op de virtuele Windows-machine ingeschakeld en worden patches op de VM geïnstalleerd via Automatische updates.
- Deze modus biedt geen ondersteuning voor de beschik baarheid-eerste patches.
- Deze modus wordt standaard ingesteld als er geen andere patch modus is opgegeven.
- Als u deze modus wilt gebruiken, stelt u de eigenschap `osProfile.windowsConfiguration.enableAutomaticUpdates=true` in en stelt u de eigenschap  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` in de VM-sjabloon in.

**Handmatig:**
- Deze modus schakelt Automatische updates op de virtuele Windows-machine uit.
- Deze modus biedt geen ondersteuning voor de beschik baarheid-eerste patches.
- Deze modus moet worden ingesteld wanneer aangepaste oplossingen voor patches worden gebruikt.
- Als u deze modus wilt gebruiken, stelt u de eigenschap `osProfile.windowsConfiguration.enableAutomaticUpdates=false` in en stelt u de eigenschap  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` in de VM-sjabloon in.

> [!NOTE]
>De eigenschap `osProfile.windowsConfiguration.enableAutomaticUpdates` kan momenteel alleen worden ingesteld wanneer de virtuele machine wordt gemaakt. Het overschakelen van hand matig naar een automatische modus of van automatische modi naar de hand matige modus wordt momenteel niet ondersteund. Overschakelen van de modus AutomaticByOS naar AutomaticByPlatfom modus wordt ondersteund

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Vereisten voor het inschakelen van automatische VM-gast patches

- Op de virtuele machine moet de [Azure VM-agent](../extensions/agent-windows.md) zijn geïnstalleerd.
- De Windows Update-service moet worden uitgevoerd op de virtuele machine.
- De virtuele machine moet toegang hebben tot Windows Update-eind punten. Als uw virtuele machine is geconfigureerd voor het gebruik van Windows Server Update Services (WSUS), moeten de relevante WSUS-server eindpunten toegankelijk zijn.
- Gebruik Compute API versie 2020-06-01 of hoger.

Het inschakelen van de Preview-functionaliteit vereist eenmalige aanmelding voor de functie **InGuestAutoPatchVMPreview** per abonnement, zoals hieronder wordt beschreven.

### <a name="rest-api"></a>REST-API
In het volgende voor beeld wordt beschreven hoe u de preview-versie van uw abonnement kunt inschakelen:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [REGI ster-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in te scha kelen.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ feature Regis](/cli/azure/feature#az-feature-register) om de preview voor uw abonnement in te scha kelen.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>Automatische VM-gastpatches
Als u automatische VM-gast patches wilt inschakelen, moet u ervoor zorgen dat de eigenschap *osProfile. windowsConfiguration. enableAutomaticUpdates* is ingesteld op *True* in de definitie van de VM-sjabloon. Deze eigenschap kan alleen worden ingesteld wanneer de virtuele machine wordt gemaakt.

### <a name="rest-api"></a>REST-API
In het volgende voor beeld wordt beschreven hoe u automatische VM-gast patches inschakelt:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) om automatische VM-gast patches in te scha kelen bij het maken of bijwerken van een virtuele machine.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ VM Create](/cli/azure/vm#az-vm-create) om automatische VM-gast patches in te scha kelen bij het maken van een nieuwe virtuele machine. In het volgende voor beeld wordt automatische VM-gast patches geconfigureerd voor een virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Als u een bestaande virtuele machine wilt wijzigen, gebruikt u [AZ VM update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Activering en evaluatie

> [!NOTE]
>Het kan meer dan drie uur duren om automatische VM-gast updates in te scha kelen op een VM, omdat de activering is voltooid tijdens de daluren van de virtuele machine. Als de evaluatie-en patch-installatie alleen plaatsvindt tijdens daluren, moet uw virtuele machine ook worden uitgevoerd tijdens daluren en kunnen er patches worden toegepast.

Als automatische VM-gast patches is ingeschakeld voor een virtuele machine, wordt een VM-extensie van `Microsoft.CPlat.Core.WindowsPatchExtension` het type geïnstalleerd op de VM. Deze uitbrei ding hoeft niet hand matig te worden geïnstalleerd of bijgewerkt, omdat deze extensie wordt beheerd door het Azure-platform als onderdeel van het automatische VM-gast patching proces.

Het kan meer dan drie uur duren om automatische VM-gast updates in te scha kelen op een VM, omdat de activering is voltooid tijdens de daluren van de virtuele machine. De uitbrei ding wordt ook geïnstalleerd en bijgewerkt tijdens rustige uren voor de virtuele machine. Als de virtuele machine buiten de piek uren eindigt voordat de activering kan worden voltooid, wordt het activerings proces hervat tijdens de volgende beschik bare time-outtijd. Als de virtuele machine eerder automatische Windows Update ingeschakeld heeft via de patch modus AutomaticByOS, wordt automatisch Windows Update uitgeschakeld voor de virtuele machine wanneer de uitbrei ding wordt geïnstalleerd.

Als u wilt controleren of automatische VM-gast patches zijn voltooid en de patch uitbreiding is geïnstalleerd op de virtuele machine, kunt u de instantie weergave van de virtuele machine bekijken. Als het activerings proces is voltooid, wordt de uitbrei ding geïnstalleerd en worden de evaluatie resultaten voor de virtuele machine beschikbaar onder `patchStatus` . De instantie weergave van de virtuele machine is toegankelijk via meerdere manieren, zoals hieronder wordt beschreven.

### <a name="rest-api"></a>REST-API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) met de `-Status` para meter voor toegang tot de instantie weergave voor uw VM.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Power shell biedt momenteel alleen informatie over de patch-uitbrei ding. Informatie over `patchStatus` is binnenkort ook beschikbaar via Power shell.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) om toegang te krijgen tot de instantie weergave voor uw VM.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Informatie over de patch status voor uw VM

De `patchStatus` sectie van het antwoord op de instantie weergave bevat details over de laatste evaluatie en de laatste patch-installatie voor uw VM.

De evaluatie resultaten voor uw virtuele machine kunnen worden gecontroleerd in de `availablePatchSummary` sectie. Er wordt regel matig een evaluatie uitgevoerd voor een VM waarvoor automatische VM-gast patching is ingeschakeld. Het aantal beschik bare patches nadat een evaluatie is gegeven onder `criticalAndSecurityPatchCount` en `otherPatchCount` resultaten. Bij automatische VM-gast patching worden alle patches geïnstalleerd die zijn beoordeeld onder de *essentiële* en *beveiligings* patch classificaties. Andere geraamde patches worden overgeslagen.

De resultaten van de installatie van de patch voor uw virtuele machine kunnen worden gecontroleerd in de `lastPatchInstallationSummary` sectie. Deze sectie bevat informatie over de laatste patch-installatie poging op de VM, inclusief het aantal patches dat is geïnstalleerd, in behandeling, mislukt of overgeslagen. Patches worden alleen geïnstalleerd tijdens het onderhouds venster voor de duur van de virtuele machine. In behandeling zijnde en mislukte patches worden automatisch opnieuw geprobeerd tijdens het onderhouds venster van de volgende piek uren.

## <a name="on-demand-patch-assessment"></a>Evaluatie op aanvraag van patches
Als automatische VM-gast patches al is ingeschakeld voor uw virtuele machine, wordt een periodieke patch-evaluatie uitgevoerd op de VM tijdens de daluren van de VM. Dit proces wordt automatisch uitgevoerd en de resultaten van de laatste evaluatie kunnen worden gecontroleerd via de instantie weergave van de virtuele machine, zoals eerder in dit document is beschreven. U kunt op elk gewenst moment een patch evaluatie op aanvraag voor uw virtuele machine activeren. Het kan een paar minuten duren voordat de patch-evaluatie is voltooid en de status van de laatste evaluatie is bijgewerkt op de instantie weergave van de virtuele machine.

Het inschakelen van de Preview-functionaliteit vereist eenmalige aanmelding voor de functie **InGuestPatchVMPreview** per abonnement. Deze functie voorbeeld wijkt af van de automatische registratie van de functie voor het automatisch uitvoeren van VM-gast patches die eerder is uitgevoerd voor **InGuestAutoPatchVMPreview**. Het inschakelen van de preview van extra functies is een afzonderlijke en extra vereiste. De preview-versie van de functie voor patch evaluatie op aanvraag kan worden ingeschakeld na het [voorzienings proces](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) dat eerder is beschreven voor automatische VM-gast patches.

> [!NOTE]
>Met patch evaluatie op aanvraag wordt niet automatisch de installatie van de patch geactiveerd. Als u automatische VM-gast patching hebt ingeschakeld, worden de geraamde en toepasselijke patches voor de virtuele machine geïnstalleerd tijdens de daluren van de VM, gevolgd door het proces voor eerste patches dat eerder in dit document is beschreven.

### <a name="rest-api"></a>REST-API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) om beschik bare patches voor uw virtuele machine te evalueren.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ VM beoordeling-patches](/cli/azure/vm#az-vm-assess-patches) om beschik bare patches voor uw virtuele machine te evalueren.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over het maken en beheren van virtuele Windows-machines](tutorial-manage-vm.md)
