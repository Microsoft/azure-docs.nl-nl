---
title: Automatische VM-gastpatching voor Azure-VM's
description: Meer informatie over het automatisch patchen van virtuele machines in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 1a6a67fe43d4e0a6086154d71e61fe51680dbcd0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762583"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Preview: Automatische VM-gastpatching voor Azure-VM's

Het inschakelen van automatische VM-gastpatching voor uw Azure-VM's helpt updatebeheer te gemeenen door virtuele machines veilig en automatisch te patchen om de beveiligingsnading te handhaven.

Automatische VM-gastpatching heeft de volgende kenmerken:
- Patches die zijn *geclassificeerd als Kritiek* of Beveiliging worden automatisch gedownload en toegepast op de VM. 
- Patches worden toegepast tijdens daluren in de tijdzone van de VM.
- Patch-orchestration wordt beheerd door Azure en patches worden toegepast volgens [de beschikbaarheids-first principes.](#availability-first-patching)
- De status van virtuele machines, zoals bepaald door middel van statussignalen van het platform, wordt gecontroleerd om patchfouten te detecteren.
- Werkt voor alle VM-grootten.

> [!IMPORTANT]
> Automatische VM-gastpatching is momenteel beschikbaar als openbare preview. Er is een opt-in-procedure nodig om de openbare preview-functionaliteit te gebruiken die hieronder wordt beschreven.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="how-does-automatic-vm-guest-patching-work"></a>Hoe werkt automatische VM-gastpatching?

Als automatische VM-gastpatches is ingeschakeld op een  VM, worden de beschikbare essentiële en beveiligingspatches gedownload en automatisch toegepast op de VM.  Dit proces wordt elke maand automatisch van start gaat wanneer er nieuwe patches worden uitgebracht. Patch-evaluatie en -installatie worden automatisch uitgevoerd en het proces omvat het opnieuw opstarten van de VM als dat nodig is.

De VM wordt periodiek om de paar dagen en meerdere keren binnen een periode van 30 dagen beoordeeld om de toepasselijke patches voor die VM te bepalen. De patches kunnen elke dag op de VM worden geïnstalleerd buiten piekuren voor de VM. Deze automatische evaluatie zorgt ervoor dat ontbrekende patches zo snel mogelijk worden ontdekt.

Patches worden geïnstalleerd binnen 30 dagen na de maandelijkse patchreleases, volgens de hieronder beschreven beschikbaarheids-eerst-orchestration. Patches worden alleen geïnstalleerd buiten piekuren voor de VM, afhankelijk van de tijdzone van de VM. De VM moet actief zijn tijdens de daluren om ervoor te zorgen dat patches automatisch worden geïnstalleerd. Als een VM tijdens een periodieke evaluatie wordt uitgeschakeld, wordt de VM automatisch beoordeeld en worden de toepasselijke patches automatisch geïnstalleerd tijdens de volgende periodieke evaluatie (meestal binnen een paar dagen) wanneer de VM wordt ingeschakeld.

Definitie-updates en andere patches die niet zijn geclassificeerd *als Kritiek* of *Beveiliging* worden niet geïnstalleerd via automatische VM-gastpatches. Als u patches wilt installeren met andere patchclassificaties of patchinstallatie wilt plannen binnen uw eigen aangepaste onderhoudsvenster, kunt u [Updatebeheer](./windows/tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Availability-first patching

Het installatieproces van de patch wordt globaal door Azure georganiseerd voor alle VM's met automatische VM-gastpatching ingeschakeld. Deze orchestration volgt de principes van beschikbaarheid-eerst voor verschillende beschikbaarheidsniveaus die door Azure worden geboden.

Voor een groep virtuele machines die een update ondergaan, worden updates door het Azure-platform in de volgende groepen uitgevoerd:

**Tussen regio's:**
- Een maandelijkse update wordt gefaseerd in Azure georganiseerd om wereldwijde implementatiefouten te voorkomen.
- Een fase kan een of meer regio's hebben en een update wordt alleen naar de volgende fasen verplaatst als in aanmerking komende VM's in een fase-update succesvol zijn.
- Geografisch gekoppelde regio's worden niet gelijktijdig bijgewerkt en kunnen zich niet in dezelfde regionale fase.
- Het succes van een update wordt gemeten door de status van de VM na de update bij te houden. De VM-status wordt bij te houden via platform health indicators voor de VM.

**Binnen een regio:**
- VM's in Beschikbaarheidszones worden niet gelijktijdig bijgewerkt.
- VM's die geen deel uitmaken van een beschikbaarheidsset, worden batchgebatcheerd om gelijktijdige updates voor alle VM's in een abonnement te voorkomen.

**Binnen een beschikbaarheidsset:**
- Alle VM's in een gemeenschappelijke beschikbaarheidsset worden niet gelijktijdig bijgewerkt.
-   VM's in een algemene beschikbaarheidsset worden bijgewerkt binnen de grenzen van updatedomeinen en VM's in meerdere updatedomeinen worden niet gelijktijdig bijgewerkt.

De installatiedatum van de patch voor een bepaalde VM kan per maand verschillen, omdat een specifieke VM kan worden opgehaald in een andere batch tussen maandelijkse patchcycli.

### <a name="which-patches-are-installed"></a>Welke patches zijn geïnstalleerd?
De geïnstalleerde patches zijn afhankelijk van de implementatiefase voor de VM. Elke maand wordt een nieuwe wereldwijde implementatie gestart waarbij alle beveiligingspatches en kritieke patches die zijn beoordeeld voor een afzonderlijke VM, voor die VM worden geïnstalleerd. De implementatie wordt in alle Azure-regio's in batches verdeeld (beschreven in de sectie patching die het eerst beschikbaar is).

De exacte set patches die moet worden geïnstalleerd, is afhankelijk van de VM-configuratie, waaronder het type besturingssysteem en de timing van de evaluatie. Het is mogelijk dat twee identieke VM's in verschillende regio's verschillende patches installeren als er meer of minder patches beschikbaar zijn wanneer de patch-orchestration verschillende regio's op verschillende tijdstippen bereikt. Op dezelfde manier, maar minder vaak, kunnen VM's binnen dezelfde regio, maar geëvalueerd op verschillende tijdstippen (vanwege verschillende beschikbaarheidszone of beschikbaarheidssetbatchs) verschillende patches krijgen.

Omdat de patchbron niet wordt geconfigureerd door automatische VM-gastpatches, kunnen twee vergelijkbare VM's die zijn geconfigureerd voor verschillende patchbronnen, zoals een openbare opslagplaats of een privéopslagplaats, ook een verschil zien in de exacte set geïnstalleerde patches.

Voor besturingssysteemtypen die patches vrijgeven met een vaste snelheid, kunnen VM's die zijn geconfigureerd voor de openbare opslagplaats voor het besturingssysteem, verwachten dat ze in een maand dezelfde set patches ontvangen in de verschillende implementatiefasen. Bijvoorbeeld Windows-VM's die zijn geconfigureerd voor de openbare Windows Update opslagplaats.

Wanneer elke maand een nieuwe implementatie wordt geactiveerd, ontvangt een VM ten minste één patch-implementatie per maand als de VM buiten piekuren wordt ingeschakeld. Dit zorgt ervoor dat de VM maandelijks wordt bijgewerkt met de nieuwste beschikbare beveiligingspatches en essentiële patches. Om consistentie te garanderen in de set geïnstalleerde patches, kunt u uw VM's configureren om patches te beoordelen en te downloaden vanuit uw eigen privé-opslagplaatsen.

## <a name="supported-os-images"></a>Ondersteunde besturingssysteemafbeeldingen
Alleen VM's die zijn gemaakt op basis van bepaalde platformafbeeldingen van het besturingssysteem worden momenteel ondersteund in de preview-versie. Aangepaste afbeeldingen worden momenteel niet ondersteund in de preview-versie.

De volgende platform-SKU's worden momenteel ondersteund (en er worden periodiek meer toegevoegd):

| Publisher               | Aanbieding voor besturingssysteem      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| RedHat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Patch-orchestration-modi
VM's in Azure ondersteunen nu de volgende patch-orchestrationmodi:

**AutomaticByPlatform:**
- Deze modus wordt ondersteund voor virtuele Linux- en Windows-VM's.
- Met deze modus wordt automatische VM-gastpatching voor de virtuele machine mogelijk en wordt de volgende patchinstallatie door Azure uitgevoerd.
- Deze modus is vereist voor het patchen van de eerste beschikbaarheid.
- Deze modus wordt alleen ondersteund voor VM's die zijn gemaakt met behulp van de hierboven ondersteunde platformafbeeldingen van het besturingssysteem.
- Voor Windows-VM's schakelt u met deze modus ook de native Automatische updates op de virtuele Windows-machine uit om duplicatie te voorkomen.
- Als u deze modus wilt gebruiken op Linux-VM's, stelt u de eigenschap `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` in de VM-sjabloon in.
- Als u deze modus wilt gebruiken op Windows-VM's, stelt u de eigenschap `osProfile.windowsConfiguration.enableAutomaticUpdates=true` in en stelt u de eigenschap in de  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` VM-sjabloon in.

**AutomaticByOS:**
- Deze modus wordt alleen ondersteund voor Windows-VM's.
- Met deze modus Automatische updates op de virtuele Windows-machine en worden patches op de virtuele machine geïnstalleerd via Automatische updates.
- Deze modus biedt geen ondersteuning voor availability-first patching.
- Deze modus is standaard ingesteld als er geen andere patchmodus is opgegeven voor een Windows-VM.
- Als u deze modus wilt gebruiken op Windows-VM's, stelt u de eigenschap `osProfile.windowsConfiguration.enableAutomaticUpdates=true` in en stelt u de eigenschap in de  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` VM-sjabloon in.

**Handmatig:**
- Deze modus wordt alleen ondersteund voor Windows-VM's.
- Deze modus schakelt Automatische updates uit op de virtuele Windows-machine.
- Deze modus biedt geen ondersteuning voor availability-first patching.
- Deze modus moet worden ingesteld bij het gebruik van aangepaste patchoplossingen.
- Als u deze modus wilt gebruiken op Windows-VM's, stelt u de eigenschap `osProfile.windowsConfiguration.enableAutomaticUpdates=false` in en stelt u de eigenschap in de  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` VM-sjabloon in.

**ImageDefault:**
- Deze modus wordt alleen ondersteund voor linux-VM's.
- Deze modus biedt geen ondersteuning voor availability-first patching.
- In deze modus wordt de standaardconfiguratie voor patching in de installatielijst voor het maken van de VM in ere hersteld.
- Deze modus wordt standaard ingesteld als er geen andere patchmodus is opgegeven voor een Linux-VM.
- Als u deze modus wilt gebruiken op Linux-VM's, stelt u de eigenschap `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` in de VM-sjabloon in.

> [!NOTE]
>Voor Windows-VM's kan de `osProfile.windowsConfiguration.enableAutomaticUpdates` eigenschap momenteel alleen worden ingesteld wanneer de VM voor het eerst wordt gemaakt. Het overschakelen van Handmatig naar een automatische modus of van automatische modi naar handmatige modus wordt momenteel niet ondersteund. Het overschakelen van de AutomaticByOS-modus naar de modus AutomaticByPlatfom wordt ondersteund.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Vereisten voor het inschakelen van automatische VM-gastpatching

- Op de virtuele machine moet de Azure VM-agent voor [Windows](./extensions/agent-windows.md) of [Linux zijn](./extensions/agent-linux.md) geïnstalleerd.
- Voor Linux-VM's moet de Azure Linux-agent versie 2.2.53.1 of hoger zijn. [Werk de Linux-agent](./extensions/update-linux-agent.md) bij als de huidige versie lager is dan de vereiste versie.
- Voor Windows-VM's moet Windows Update-service worden uitgevoerd op de virtuele machine.
- De virtuele machine moet toegang hebben tot de geconfigureerde update-eindpunten. Als uw virtuele machine is geconfigureerd voor het gebruik van privé-opslagplaatsen voor Linux of Windows Server Update Services (WSUS) voor Windows-VM's, moeten de relevante update-eindpunten toegankelijk zijn.
- Gebruik Compute API versie 2020-12-01 of hoger. Compute-API versie 2020-06-01 kan worden gebruikt voor Windows-VM's met beperkte functionaliteit.

Als u de preview-functionaliteit wilt inschakelen, moet u zich een keer per abonnement inschrijven voor de functies **InGuestAutoPatchVMPreview** en **InGuestPatchVMPreview,** zoals beschreven in de volgende sectie.

### <a name="rest-api"></a>REST-API
In het volgende voorbeeld wordt beschreven hoe u de preview voor uw abonnement inschakelen:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de [cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) om de preview voor uw abonnement in te stellen.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [az feature register om](/cli/azure/feature#az_feature_register) de preview voor uw abonnement in te stellen.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te zetten in de Compute-resourceprovider.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Automatische VM-gastpatches
Als u automatische VM-gastpatches wilt inschakelen op een Windows-VM, moet u ervoor zorgen dat de eigenschap *osProfile.windowsConfiguration.enableAutomaticUpdates* is ingesteld op *true* in de definitie van de VM-sjabloon. Deze eigenschap kan alleen worden ingesteld bij het maken van de VM. Deze aanvullende eigenschap is niet van toepassing op linux-VM's.

### <a name="rest-api-for-linux-vms"></a>REST API voor Linux-VM's
In het volgende voorbeeld wordt beschreven hoe u automatische VM-gastpatching inschakelen:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>REST API voor Windows-VM's
In het volgende voorbeeld wordt beschreven hoe u automatische VM-gastpatching inschakelen:

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

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell voor Windows-VM's
Gebruik de cmdlet [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) om automatische VM-gastpatching in te stellen bij het maken of bijwerken van een VM.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI voor Windows-VM's
Gebruik [az vm create om](/cli/azure/vm#az_vm_create) automatische VM-gastpatching in te stellen bij het maken van een nieuwe VM. In het volgende voorbeeld wordt automatische VM-gastpatching geconfigureerd voor een VM met de naam *myVM* in de resourcegroep met de *naam myResourceGroup:*

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Als u een bestaande VM wilt wijzigen, gebruikt [u az vm update](/cli/azure/vm#az_vm_update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Inschakelen en beoordelen

> [!NOTE]
>Het kan meer dan drie uur duren voordat automatische VM-gastupdates op een VM zijn ingeschakeld, omdat de inschakelen is voltooid tijdens de daluren van de VM. Omdat de evaluatie en installatie van patches alleen buiten piekuren plaatsvinden, moet uw VM ook buiten piekuren worden uitgevoerd om patches toe te passen.

Wanneer automatische VM-gastpatching is ingeschakeld voor een VM, wordt een VM-extensie van het type geïnstalleerd op een Linux-VM of wordt een VM-extensie van het type geïnstalleerd op een `Microsoft.CPlat.Core.LinuxPatchExtension` `Microsoft.CPlat.Core.WindowsPatchExtension` Windows-VM. Deze extensie hoeft niet handmatig te worden geïnstalleerd of bijgewerkt, omdat deze extensie wordt beheerd door het Azure-platform als onderdeel van het automatische patchproces voor gast-VM's.

Het kan meer dan drie uur duren voordat automatische VM-gastupdates op een VM zijn ingeschakeld, omdat de inschakelen is voltooid tijdens de daluren van de VM. De extensie wordt ook geïnstalleerd en bijgewerkt tijdens daluren voor de VM. Als de VM buiten piekuren eindigt voordat de inschakelen kan worden voltooid, wordt het inschakelen hervat tijdens de volgende beschikbare daluren.

Automatische updates worden in de meeste scenario's uitgeschakeld en de installatie van patches wordt in de toekomst uitgevoerd via de extensie. De volgende voorwaarden zijn van toepassing.
- Als voor een Windows-VM voorheen Automatische Windows Update was ingeschakeld via de modus AutomaticByOS-patch, wordt Automatische Windows Update uitgeschakeld voor de VM wanneer de extensie wordt geïnstalleerd.
- Voor Ubuntu-VM's worden de standaard automatische updates automatisch uitgeschakeld wanneer automatische VM-gastpatching is ingeschakeld.
- Voor RHEL moeten automatische updates handmatig worden uitgeschakeld (dit is een preview-beperking). Uitvoeren:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Als u wilt controleren of automatische VM-gastpatching is voltooid en de extensie voor patching is geïnstalleerd op de VM, kunt u de exemplaarweergave van de VM bekijken. Als het inschakelen is voltooid, wordt de extensie geïnstalleerd en zijn de evaluatieresultaten voor de VM beschikbaar onder `patchStatus` . De weergave van het VM-exemplaar kan op meerdere manieren worden bekeken, zoals hieronder wordt beschreven.

### <a name="rest-api"></a>REST-API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de [cmdlet Get-AzVM](/powershell/module/az.compute/get-azvm) met de parameter om toegang te krijgen tot `-Status` de exemplaarweergave voor uw VM.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell biedt momenteel alleen informatie over de patchextensie. Informatie over `patchStatus` is binnenkort ook beschikbaar via PowerShell.

### <a name="azure-cli"></a>Azure CLI
Gebruik [az vm get-instance-view om toegang](/cli/azure/vm#az_vm_get_instance_view) te krijgen tot de exemplaarweergave voor uw VM.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Informatie over de patchstatus voor uw VM

De `patchStatus` sectie van het antwoord van de instantieweergave bevat details over de meest recente evaluatie en de installatie van de laatste patch voor uw VM.

De evaluatieresultaten voor uw VM kunnen worden gecontroleerd in de `availablePatchSummary` sectie . Er wordt periodiek een evaluatie uitgevoerd voor een VM met automatische VM-gastpatching ingeschakeld. Het aantal beschikbare patches na een evaluatie wordt gegeven onder `criticalAndSecurityPatchCount` en `otherPatchCount` de resultaten. Met automatische VM-gastpatches worden  alle patches geïnstalleerd die zijn beoordeeld onder de *classificaties* Kritieke en Beveiligingspatch. Elke andere geëvalueerde patch wordt overgeslagen.

De installatieresultaten van de patch voor uw VM kunnen worden gecontroleerd in de `lastPatchInstallationSummary` sectie . Deze sectie bevat details over de laatste patchinstallatiepoging op de VM, inclusief het aantal patches dat is geïnstalleerd, in behandeling, mislukt of overgeslagen. Patches worden alleen geïnstalleerd tijdens het onderhoudsvenster buiten piekuren voor de VM. In behandeling zijnde en mislukte patches worden automatisch opnieuw proberen uit te proberen tijdens het volgende onderhoudsvenster buiten de piekuren.

## <a name="on-demand-patch-assessment"></a>Patchevaluatie op aanvraag
Als automatische VM-gastpatching al is ingeschakeld voor uw VM, wordt er een periodieke patchevaluatie uitgevoerd op de VM tijdens de daluren van de VM. Dit proces is automatisch en de resultaten van de meest recente evaluatie kunnen worden gecontroleerd via de exemplaarweergave van de VM, zoals eerder in dit document is beschreven. U kunt ook op elk moment een patchevaluatie op aanvraag activeren voor uw VM. Het uitvoeren van de patchevaluatie kan enkele minuten duren en de status van de meest recente evaluatie wordt bijgewerkt in de weergave van de VM-instantie.

Als u de preview-functionaliteit wilt inschakelen, moet u zich een keer per abonnement inschrijven voor de functie **InGuestPatchVMPreview.** Deze preview-versie van deze functie verschilt van de automatische inschrijving van de functie voor gastpatching voor VM's die eerder is uitgevoerd **voor InGuestAutoPatchVMPreview.** Het inschakelen van de extra functiepreview is een afzonderlijke en aanvullende vereiste. De preview van de functie voor patch-evaluatie [](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) op aanvraag kan worden ingeschakeld volgens het preview-inschakelenproces dat eerder is beschreven voor automatische VM-gastpatching.

> [!NOTE]
>Patchevaluatie op aanvraag activeert niet automatisch de installatie van patches. Als u automatische VM-gastpatches hebt ingeschakeld, worden de geëvalueerde en toepasselijke patches voor de VM geïnstalleerd tijdens de daluren van de VM, na het patchingproces dat eerder in dit document is beschreven.

### <a name="rest-api"></a>REST-API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) om beschikbare patches voor uw virtuele machine te beoordelen.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Gebruik [az vm assess-patches om](/cli/azure/vm#az_vm_assess_patches) beschikbare patches voor uw virtuele machine te beoordelen.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Meer informatie over het maken en beheren van virtuele Windows-machines](./windows/tutorial-manage-vm.md)
