---
title: Migreren naar Azure Firewall Premium preview
description: Meer informatie over het migreren van Azure Firewall Standard naar Azure Firewall Premium preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549715"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Migreren naar Azure Firewall Premium preview

U kunt Azure Firewall Standard migreren naar Azure Firewall Premium Preview om te profiteren van de nieuwe Premium-mogelijkheden. Zie [Azure Firewall Premium preview-functies](premium-features.md)voor meer informatie over de preview-functies van Azure Firewall Premium.

In de volgende twee voor beelden ziet u hoe u:
- Een bestaand standaard beleid migreren met behulp van Azure PowerShell
- Migreer een bestaande standaard firewall (met klassieke regels) om Premium te Azure Firewall met een Premium-beleid.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Een bestaand beleid migreren met behulp van Azure PowerShell

`Transform-Policy.ps1` is een Azure PowerShell script waarmee een nieuw Premium-beleid wordt gemaakt op basis van een bestaand standaard beleid.

Op basis van een standaard firewall beleids-ID wordt het script getransformeerd naar een Premium-Azure Firewall beleid. Het script maakt eerst verbinding met uw Azure-account, haalt het beleid op, transformeert/voegt diverse para meters, en uploadt vervolgens een nieuw Premium-beleid. Het nieuwe Premium-beleid heet `<previous_policy_name>_premium` .

Voor beeld van gebruik:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Met het script worden geen instellingen voor bedreigings informatie gemigreerd. U moet deze instellingen noteren voordat u doorgaat en ze hand matig migreren.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Een bestaande standaard firewall migreren met behulp van de Azure Portal

In dit voor beeld ziet u hoe u met behulp van de Azure Portal een standaard firewall (klassieke regels) kunt migreren naar Azure Firewall Premium met een Premium-beleid.

1. Selecteer in de Azure Portal uw standaard firewall. Selecteer op de pagina **overzicht** de optie **migreren naar firewall-beleid**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migreren naar firewall beleid":::

1. Selecteer op de pagina **migreren naar firewall beleid** de optie **bekijken + maken**.
1. Selecteer **Maken**.

   Het duurt enkele minuten om de implementatie te voltooien.
1. Gebruik het `Transform-Policy.ps1` [Azure PowerShell script](#migrate-an-existing-policy-using-azure-powershell) om dit nieuwe standaard beleid om te zetten in een Premium-beleid.
1. Selecteer uw standaard firewall-resource op de portal. 
1. Selecteer onder **Automation** de optie **sjabloon exporteren**. Laat dit browser tabblad open. U komt later terug.
   > [!TIP]
   > Om ervoor te zorgen dat u de sjabloon niet kwijtraakt, downloadt en slaat u deze op voor het geval uw browser tabblad gesloten of vernieuwd wordt.
1. Open een nieuw browser tabblad, navigeer naar het Azure Portal en open de resource groep die uw firewall bevat.
1. Verwijder het bestaande Standard firewall-exemplaar.

   Dit duurt enkele minuten.

1. Ga terug naar het browser tabblad met de geëxporteerde sjabloon.
1. Selecteer **implementeren** en selecteer vervolgens op de pagina **aangepaste implementatie** de optie **sjabloon bewerken**.
1. Bewerk de sjabloon tekst:
   
   1. `Microsoft.Network/azureFirewalls` `Properties` `sku` Wijzig de `tier` van ' standaard ' in ' Premium ' onder de resource.
   1. Wijzig onder de sjabloon `Parameters` `defaultValue` voor de `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` van:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      in:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Selecteer **Opslaan**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

Wanneer de implementatie is voltooid, kunt u nu alle nieuwe Azure Firewall Premium preview-functies configureren.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Firewall Premium-functies](premium-features.md)