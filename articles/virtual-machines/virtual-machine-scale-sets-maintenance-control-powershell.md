---
title: Onderhouds beheer voor installatie kopie-upgrades van besturings systemen op virtuele-machine schaal sets van Azure met Power shell
description: Meer informatie over hoe u kunt bepalen wanneer automatische installatie kopieën van besturings systemen worden geïmplementeerd naar uw schaal sets voor virtuele Azure-machines met behulp van onderhouds beheer en Power shell.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91974835"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Voor beeld: onderhouds beheer voor installatie kopie van besturingssysteem upgrades op virtuele-machine schaal sets van Azure met behulp van Power shell

Met de onderhouds controle kunt u bepalen wanneer automatische installatie kopieën van gast besturingssystemen moeten worden toegepast op de schaal sets van virtuele machines. In dit onderwerp worden de Azure PowerShell opties voor onderhouds beheer beschreven. Zie [onderhouds beheer voor virtuele-machine schaal sets van Azure](virtual-machine-scale-sets-maintenance-control.md)voor meer informatie over het gebruik van onderhouds beheer.

> [!IMPORTANT]
> Onderhouds beheer voor installatie kopieën van besturings systemen op virtuele-machine schaal sets van Azure is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="enable-the-powershell-module"></a>De Power shell-module inschakelen

Zorg ervoor dat `PowerShellGet` het up-to-date is.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Installeer de `Az.Maintenance` Power shell-module.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Als u lokaal installeert, moet u ervoor zorgen dat u de Power shell-prompt opent als beheerder.

U wordt mogelijk ook gevraagd om te bevestigen dat u wilt installeren vanuit een *niet-vertrouwde opslag plaats*. Typ `Y` of selecteer **Ja om alles** om de module te installeren.

## <a name="connect-to-an-azure-account"></a>Verbinding maken met een Azure-account

Maak verbinding met uw gewenste Azure-account via [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en [set-AzAccount](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Een onderhoudsconfiguratie maken

Maak een resource groep als een container voor uw configuratie. In dit voor beeld wordt een resource groep met de naam *myMaintenanceRG* gemaakt in *eastus2*. Als u al een resource groep hebt die u wilt gebruiken, kunt u dit gedeelte overs Laan. U hoeft alleen de naam van de resource groep te vervangen door uw eigen in de rest van de voor beelden.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Gebruik [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) om een onderhouds configuratie te maken. In dit voor beeld wordt er een onderhouds configuratie met de naam *myConfig* scoped gemaakt voor de besturingssysteem installatie kopie. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> De **duur** van het onderhoud moet *5 uur* of langer zijn. **Terugkeer patroon** van onderhoud moet worden ingesteld op *dag*.

Met `-MaintenanceScope OSImage` kunt u ervoor zorgen dat de onderhouds configuratie wordt gebruikt voor het beheren van updates voor het gast besturingssysteem.

Als u probeert een configuratie met dezelfde naam te maken, maar op een andere locatie, krijgt u een fout melding. Configuratie namen moeten uniek zijn voor uw resource groep.

U kunt een query uitvoeren voor beschik bare onderhouds configuraties met [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Uw schaalset voor virtuele machines koppelen aan de onderhouds configuratie

Een schaalset voor virtuele machines kan worden gekoppeld aan een onderhouds configuratie, ongeacht de regio en het abonnement van de onderhouds configuratie. Door in te stellen op de onderhouds configuratie, worden nieuwe updates voor installatie kopieën van besturings systemen voor de schaalset automatisch gepland in het volgende beschik bare onderhouds venster.

Gebruik [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) om de onderhouds configuratie in te stellen voor het koppelen van uw schaalset voor virtuele machines.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Automatische upgrade van het besturings systeem inschakelen

U kunt automatische upgrades voor het besturings systeem inschakelen voor elke schaalset voor virtuele machines die onderhoud gaat gebruiken. Raadpleeg voor [Azure virtual machine-schaal sets voor virtuele machines automatisch upgrades voor installatie kopieën van besturings](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) systemen voor het inschakelen van automatische upgrades van besturings systemen op de schaalset voor virtuele machines. 


## <a name="next-steps"></a>Volgende stappen

Meer informatie over onderhoud en updates voor virtuele machines die worden uitgevoerd in Azure.

> [!div class="nextstepaction"]
> [Onderhoud en updates](maintenance-and-updates.md)