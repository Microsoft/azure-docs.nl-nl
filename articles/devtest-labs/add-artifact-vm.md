---
title: Een artefact toevoegen aan een virtuele machine in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het toevoegen van een artefact aan een virtuele machine in een lab in Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483887"
---
# <a name="add-an-artifact-to-a-vm"></a>Een artefact toevoegen aan een VM
U kunt bij het maken van een virtuele machine bestaande artefacten toevoegen. Deze artefacten kunnen afkomstig zijn uit de [open bare DevTest Labs-opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) of vanuit uw eigen Git-opslag plaats. In dit artikel leest u hoe u artefacten kunt toevoegen in de Azure Portal en met behulp van Azure PowerShell. 

Met Azure DevTest Labs *artefacten* kunt u *acties* opgeven die worden uitgevoerd wanneer de virtuele machine wordt ingericht, zoals het uitvoeren van Windows Power shell-scripts, het uitvoeren van bash-opdrachten en het installeren van software. Met artefact *parameters* kunt u het artefact aanpassen voor uw specifieke scenario.

Zie het artikel: [aangepaste artefacten maken](devtest-lab-artifact-author.md)voor meer informatie over het maken van aangepaste artefacten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Azure Portal gebruiken 
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met Labs het lab met de virtuele machine waarmee u wilt werken.  
1. Selecteer **mijn virtuele machines**.
1. Selecteer de gewenste VM.
1. Selecteer **artefacten beheren**. 
1. Selecteer **artefacten Toep assen**.
1. Selecteer in het deel venster **artefacten Toep assen** het artefact dat u aan de virtuele machine wilt toevoegen.
1. Voer in het deel venster **artefact toevoegen** de vereiste parameter waarden in en eventuele optionele para meters die u nodig hebt.  
1. Selecteer **toevoegen** om het artefact toe te voegen en terug te keren naar het deel venster **artefacten Toep assen** .
1. Ga door met het toevoegen van artefacten die nodig zijn voor uw VM.
1. Wanneer u uw artefacten hebt toegevoegd, kunt u [de volg orde wijzigen waarin de artefacten worden uitgevoerd](#change-the-order-in-which-artifacts-are-run). U kunt ook terugkeren om [een artefact weer te geven of te wijzigen](#view-or-modify-an-artifact).
1. Wanneer u klaar bent met het toevoegen van artefacten, selecteert u **Toep assen**

### <a name="change-the-order-in-which-artifacts-are-run"></a>De volg orde wijzigen waarin artefacten worden uitgevoerd
De acties van de artefacten worden standaard uitgevoerd in de volg orde waarin ze worden toegevoegd aan de virtuele machine. De volgende stappen laten zien hoe u de volg orde wijzigt waarin de artefacten worden uitgevoerd.

1. Selecteer boven in het deel venster **artefacten Toep assen** de koppeling waarmee het aantal artefacten wordt aangegeven dat aan de virtuele machine is toegevoegd.
   
    ![Aantal artefacten dat aan de virtuele machine is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sleep in het deel venster **geselecteerde artefacten** de artefacten naar de gewenste volg orde. Als u het artefact niet kunt slepen, moet u ervoor zorgen dat u vanaf de linkerkant van het artefact sleept. 
1. Selecteer **OK** wanneer u gereed bent.  

### <a name="view-or-modify-an-artifact"></a>Een artefact weer geven of wijzigen
De volgende stappen laten zien hoe u de para meters van een artefact kunt weer geven of wijzigen:

1. Selecteer boven in het deel venster **artefacten Toep assen** de koppeling waarmee het aantal artefacten wordt aangegeven dat aan de virtuele machine is toegevoegd.
   
    ![Aantal artefacten dat aan de virtuele machine is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Selecteer in het deel venster **geselecteerde artefacten** het artefact dat u wilt weer geven of bewerken.  
1. Breng de gewenste wijzigingen aan in het deel venster **artefact toevoegen** en selecteer **OK** om het deel venster **artefact toevoegen** te sluiten.
1. Selecteer **OK** om het **geselecteerde artefacten** venster te sluiten.

## <a name="use-powershell"></a>PowerShell gebruiken
Met het volgende script wordt het opgegeven artefact toegepast op de opgegeven virtuele machine. De opdracht [invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) is het account waarmee de bewerking wordt uitgevoerd.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen over artefacten:

- [Verplichte artefacten opgeven voor uw Lab](devtest-lab-mandatory-artifacts.md)
- [Aangepast artefacten maken](devtest-lab-artifact-author.md)
- [Een opslagplaats voor artefacten toevoegen aan een lab](devtest-lab-artifact-author.md)
- [Problemen met artefacten vaststellen](devtest-lab-troubleshoot-artifact-failure.md)
