---
title: Machines starten met Automation-runbooks in Azure DevTest Labs
description: Meer informatie over het starten van virtuele machines in een lab in Azure DevTest Labs met behulp van Azure Automation runbooks.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 231e79d594aab7c59fa21f9ee512abaa9ac67043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282259"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Virtuele machines in een lab op volg orde starten met Azure Automation runbooks
Met de functie [automatisch starten](devtest-lab-set-lab-policy.md#set-autostart) van DevTest Labs kunt u vm's zodanig configureren dat deze automatisch worden gestart op een opgegeven tijdstip. Deze functie biedt echter geen ondersteuning voor machines om in een specifieke volg orde te starten. Er zijn verschillende scenario's waarbij dit type automatisering nuttig zou zijn.  In één scenario moet een JumpBox-VM binnen een Lab eerst worden gestart, vóór de andere virtuele machines, omdat de JumpBox wordt gebruikt als het toegangs punt voor de andere Vm's.  In dit artikel wordt beschreven hoe u een Azure Automation account instelt met een Power shell-runbook dat een script uitvoert. Het script maakt gebruik van labels op Vm's in het Lab zodat u de opstart volgorde kunt beheren zonder het script te hoeven wijzigen.

## <a name="setup"></a>Instellen
In dit voor beeld moet de virtuele machine in het lab de tag **StartupOrder** toevoegen met de juiste waarde (0, 1, 2, enzovoort). Wijs een computer aan die niet moet worden gestart als-1.

## <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken
Maak een Azure Automation-account door de instructies in [dit artikel](../automation/automation-create-standalone-account.md)te volgen. Kies de optie **uitvoeren als-accounts** bij het maken van het account. Zodra het Automation-account is gemaakt, opent u de pagina **modules** en selecteert u **Azure-modules bijwerken** op de menu balk. De standaard modules zijn verschillende versies oud en zonder de update kan het script niet worden gebruikt.

## <a name="add-a-runbook"></a>Een runbook toevoegen
Als u nu een runbook wilt toevoegen aan het Automation-account, selecteert u **Runbooks** in het menu links. Selecteer **een Runbook toevoegen** in het menu en volg de instructies voor [het maken van een Power shell-runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell-script
Het volgende script neemt de naam van het abonnement, de naam van het Lab als para meters. De stroom van het script bestaat uit het ophalen van alle virtuele machines in het lab en vervolgens het parseren van de label gegevens om een lijst met de namen van de virtuele machines en de bijbehorende opstart volgorde te maken. Het script doorloopt de Vm's in de juiste volg orde en start de Vm's. Als er meerdere Vm's in een specifiek Volg nummer staan, worden ze asynchroon met Power shell-taken gestart. Voor de virtuele machines die geen tag hebben, stelt u de opstart waarde in op de laatste (10). de waarden worden standaard als laatste gestart.  Als de virtuele machine niet automatisch moet worden gestart door de Lab, stelt u de waarde van de tag in op 11 en wordt deze genegeerd.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Een planning maken
Als u dit script dagelijks wilt uitvoeren, [maakt u een planning](../automation/shared-resources/schedules.md#create-a-schedule) in het Automation-account. Zodra de planning is gemaakt, [koppelt u deze aan het runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

In een grote situatie waarbij meerdere abonnementen met meerdere Labs zijn, slaat u de parameter gegevens op in een bestand voor verschillende Labs en geeft u het bestand door aan het script in plaats van de afzonderlijke para meters. Het script moet worden gewijzigd, maar de kern uitvoering zou hetzelfde zijn. Hoewel in dit voor beeld de Azure Automation wordt gebruikt om het Power shell-script uit te voeren, zijn er nog andere opties, zoals het gebruik van een taak in een pijp lijn voor Build/release.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel voor meer informatie over Azure Automation: [een inleiding tot Azure Automation](../automation/automation-intro.md).
