---
title: Beleids definities voor gast configuratie maken van groepsbeleid basis lijn voor Windows
description: Meer informatie over het converteren van groepsbeleid van de Windows Server 2019-beveiligings basislijn naar een beleids definitie.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: a49c8044914c8c23b4f99cad7838652eb94c4b92
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096577"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Beleids definities voor gast configuratie maken van groepsbeleid basis lijn voor Windows

Voordat u aangepaste beleids definities maakt, is het een goed idee om de conceptuele overzichts informatie te lezen op [Azure Policy-gast configuratie](../concepts/guest-configuration.md). Zie [gast configuratie beleidsregels voor Linux maken](./guest-configuration-create-linux.md)voor meer informatie over het maken van aangepaste gast configuratie beleids definities voor Linux. Zie [gast configuratie beleidsregels voor Windows maken](./guest-configuration-create.md)voor meer informatie over het maken van aangepaste gast configuratie beleids definities voor Windows.

Bij het controleren van Windows gebruikt gastconfiguratie de resourcemodule [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) om het configuratiebestand te maken. De DSC-configuratie definieert de toestand waarin de machine zich moet bevinden. Als de evaluatie van de configuratie **niet-compatibel** is, wordt het beleids effect *auditIfNotExists* geactiveerd.
[Azure Policy-gast configuratie](../concepts/guest-configuration.md) controleert alleen de instellingen binnen machines.

> [!IMPORTANT]
> De gastconfiguratie-extensie is vereist voor het uitvoeren van controles op virtuele machines van Azure. Als u de uitbrei ding op schaal op alle Windows-computers wilt implementeren, wijst u de volgende beleids definities toe:
> - [Vereisten voor het inschakelen van gastconfiguratiebeleid op Windows-VM's implementeren.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> Gebruik geen geheimen of vertrouwelijke informatie in aangepaste inhouds pakketten.

De DSC-Community heeft de [BaselineManagement-module](https://github.com/microsoft/BaselineManagement) gepubliceerd voor het converteren van geëxporteerde Groepsbeleid sjablonen naar de DSC-indeling. In combi natie met de GuestConfiguration-cmdlet maakt de BaselineManagement-module Azure Policy-gast configuratie pakket voor Windows van groepsbeleid inhoud. Meer informatie over het gebruik van de BaselineManagement-module vindt u in het artikel [Snelstartgids: Groepsbeleid converteren naar DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

In deze hand leiding wordt het proces door lopen om een Azure Policy-gast configuratie pakket te maken op basis van een groepsbeleid object (GPO). Tijdens de walkthrough wordt de conversie van de beveiligings basislijn van Windows Server 2019 beschreven. hetzelfde proces kan worden toegepast op andere groeps beleidsobjecten.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>De beveiligings basislijn van Windows Server 2019 downloaden en gerelateerde Power shell-modules installeren

De **DSC**-, **GuestConfiguration**-, **baseline Management**-en gerelateerde Azure-modules in Power Shell installeren:

1. Voer de volgende opdracht uit vanuit een Power shell-prompt:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Maak een map voor en down load de basis lijn voor Windows Server 2019-beveiliging vanuit de Windows Security compliance Toolkit.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Deblokkeren en uitvouwen van de gedownloade server 2019-basis lijn.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. De inhoud van de basis lijn van de server 2019 valideren met behulp van **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Converteren van groepsbeleid naar Azure Policy gast configuratie

Vervolgens wordt de gedownloade server 2019-basis lijn geconverteerd naar een gast configuratie pakket met behulp van de gast configuratie-en baseline management-modules.

1. Converteer het groepsbeleid naar de gewenste status configuratie met behulp van de module voor basislijn beheer.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Wijzig de naam, Format teer en voer de geconverteerde scripts uit voordat u een pakket met beleids inhoud maakt.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Een inhouds pakket voor de Azure Policy gast configuratie maken.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Azure Policy-gast configuratie maken

1. De volgende stap is het publiceren van het bestand naar Azure Blob Storage. Voor de opdracht `Publish-GuestConfigurationPackage` is de `Az.Storage` module vereist.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. Nadat een aangepast beleids pakket voor de gast configuratie is gemaakt en geüpload, maakt u de beleids definitie voor het gast configuratie beleid. Gebruik de `New-GuestConfigurationPolicy` cmdlet om de gast configuratie te maken.

   ```azurepowershell-interactive
   $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
   }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Publiceer de beleids definities met de `Publish-GuestConfigurationPolicy` cmdlet. De cmdlet heeft alleen de para meter **Path** die verwijst naar de locatie van de json-bestanden die zijn gemaakt door `New-GuestConfigurationPolicy` . Als u de opdracht publiceren wilt uitvoeren, moet u toegang hebben tot het maken van beleids definities in Azure. De specifieke autorisatie vereisten worden beschreven op de pagina [overzicht van Azure Policy](../overview.md#getting-started) . De beste ingebouwde rol is Inzender voor **resource beleid**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Beleids definitie voor gast configuratie toewijzen

Met het beleid dat is gemaakt in azure, is de laatste stap het initiatief toe te wijzen. Zie hoe u het initiatief toewijst met [Portal](../assign-policy-portal.md), [Azure cli](../assign-policy-azurecli.md)en [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Beleids definities voor gast configuraties moeten **altijd** worden toegewezen met het initiatief waarbij het beleid voor _AuditIfNotExists_ en _DeployIfNotExists_ wordt gecombineerd. Als alleen het _AuditIfNotExists_ -beleid is toegewezen, worden de vereisten niet geïmplementeerd en wordt in het beleid altijd weer gegeven dat ' 0 '-servers compatibel zijn.

Voor het toewijzen van een beleids definitie met _DeployIfNotExists_ -effect is een extra toegangs niveau vereist. Als u de minimale bevoegdheid wilt verlenen, kunt u een aangepaste roldefinitie maken die de **Inzender voor resource beleid** uitbreidt. In het onderstaande voor beeld maakt u een rol met de naam **resource Policy CONTRIBUTOR Dine** met de extra machtiging _micro soft. Authorization/roleAssignments/write_.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het controleren van Vm's met [gast configuratie](../concepts/guest-configuration.md).
- Meer informatie over het [programmatisch maken van beleids regels](./programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](./get-compliance-data.md).
