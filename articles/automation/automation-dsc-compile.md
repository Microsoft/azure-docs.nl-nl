---
title: DSC-configuraties compileren in Azure Automation State Configuration
description: In dit artikel wordt beschreven hoe u Desired State Configuration (DSC)-configuraties voor Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e0a8bc3057bc098855eb7ff65a8feeb83b7c746
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834818"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>DSC-configuraties compileren in Azure Automation State Configuration

U kunt Desired State Configuration configuraties (DSC) op de volgende Azure Automation State Configuration compileren:

- Azure State Configuration compilatieservice
  - Methode Beginner met interactieve gebruikersinterface
   - Taaktoestand eenvoudig bijhouden

- Windows PowerShell
  - Aanroepen vanuit Windows PowerShell lokaal werkstation of buildservice
  - Integreren met ontwikkelingstestpijplijn
  - Complexe parameterwaarden opgeven
  - Werken met knooppunt- en niet-knooppuntgegevens op schaal
  - Aanzienlijke prestatieverbetering

U kunt ook sjablonen Azure Resource Manager azure Desired State Configuration (DSC)-extensie gebruiken om configuraties naar uw Azure-VM's te pushen. De Azure DSC maakt gebruik van het Azure VM Agent-framework voor het leveren, uitvoeren en rapporteren van DSC-configuraties die worden uitgevoerd op Virtuele Azure-VM's. Zie de extensie Azure Resource Manager met sjablonen voor Azure Resource Manager compilatiedetails met [behulp Desired State Configuration.](../virtual-machines/extensions/dsc-template.md#details) 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Een DSC-configuratie compileren in Azure State Configuration

### <a name="portal"></a>Portal

1. Klik in uw Automation-account op **Statusconfiguratie (DSC)**.
1. Klik op het **tabblad Configuraties** en klik vervolgens op de configuratienaam die u wilt compileren.
1. Klik **op Compileren.**
1. Als de configuratie geen parameters heeft, wordt u gevraagd om te bevestigen of u deze wilt compileren. Als de configuratie parameters bevat, wordt de blade **Configuratie compileren** geopend, zodat u parameterwaarden kunt opgeven.
1. De pagina Compilatie job wordt geopend, zodat u de status van de compilatie job kunt volgen. U kunt deze pagina ook gebruiken om de knooppuntconfiguraties (MOF-configuratiedocumenten) bij te houden die op de Azure Automation State Configuration pull-server zijn geplaatst.

### <a name="azure-powershell"></a>Azure PowerShell

U kunt [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) gebruiken om te beginnen met compileren met Windows PowerShell. Met de volgende voorbeeldcode wordt de compilatie van een DSC-configuratie met de **naam SampleConfig gestart.**

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` retourneert een compilatie-taakobject dat u kunt gebruiken om de taakstatus bij te houden. Vervolgens kunt u dit compilatie-taakobject gebruiken met [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) om de status van de compilatie job te bepalen en [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) om de streams (uitvoer) weer te geven. In het volgende voorbeeld wordt de compilatie van de SampleConfig-configuratie gestart, wordt gewacht tot deze is voltooid en worden vervolgens de streams weergegeven.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Basisparameters declareer

Parameterdeclaratie in DSC-configuraties, inclusief parametertypen en eigenschappen, werkt hetzelfde als in Azure Automation runbooks. Zie [Een runbook starten in Azure Automation](./start-runbooks.md) voor meer informatie over runbookparameters.

In het volgende voorbeeld worden de parameters en gebruikt om de waarden van eigenschappen te bepalen in de knooppuntconfiguratie `FeatureName` `IsPresent` **ParametersExample.sample,** die tijdens de compilatie wordt gegenereerd.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

U kunt DSC-configuraties compileren die gebruikmaken van basisparameters in Azure Automation State Configuration portal of met Azure PowerShell.

#### <a name="portal"></a>Portal

In de portal kunt u parameterwaarden invoeren nadat u op **Compileren hebt geklikt.**

![Parameters voor het compileren van configuraties](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell vereist parameters in een [hashtabel,](/powershell/module/microsoft.powershell.core/about/about_hash_tables)waarbij de sleutel overeenkomt met de parameternaam en de waarde gelijk is aan de parameterwaarde.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Zie Referentie-assets voor meer informatie over het doorgeven van objecten `PSCredential` [als parameters.](#credential-assets)

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Configuraties compileren met samengestelde resources in Azure Automation

Met **de functie** Samengestelde resources kunt u DSC-configuraties gebruiken als geneste resources in een configuratie. Met deze functie kunt u meerdere configuraties op één resource toepassen. Zie [Samengestelde resources: Een DSC-configuratie gebruiken als resource](/powershell/scripting/dsc/resources/authoringresourcecomposite) voor meer informatie over samengestelde resources.

> [!NOTE]
> Om ervoor te zorgen dat configuraties met samengestelde resources correct worden gecomp compileerd, moet u eerst importeren in Azure Automation DSC-resources die afhankelijk zijn van de samengestelde resources. Het toevoegen van een samengestelde DSC-resource verschilt niet van het toevoegen van een PowerShell-module aan Azure Automation. Dit proces wordt beschreven in [Modules beheren in Azure Automation](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>ConfigurationData beheren bij het compileren van configuraties in Azure Automation

`ConfigurationData` is een ingebouwde DSC-parameter waarmee u structurele configuratie kunt scheiden van een omgevingsspecifieke configuratie tijdens het gebruik van PowerShell DSC. Zie ['What' scheiden van 'Where' in PowerShell DSC voor meer informatie.](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)

> [!NOTE]
> Bij het compileren in Azure Automation State Configuration, kunt u in Azure PowerShell `ConfigurationData` gebruiken, maar niet in de Azure Portal.

In het volgende voorbeeld wordt in de DSC-configuratie `ConfigurationData` gebruikgemaakt van de `$ConfigurationData` trefwoorden en `$AllNodes` . Voor dit voorbeeld hebt u ook de [module xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) nodig.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

U kunt de voorgaande DSC-configuratie compileren met Windows PowerShell. Met het volgende script worden twee knooppuntconfiguraties toegevoegd aan de Azure Automation State Configuration pull-service: **ConfigurationDataSample.MyVM1** en **ConfigurationDataSample.MyVM3.**

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Werken met assets in Azure Automation compilatie

Assetverwijzingen zijn hetzelfde in zowel Azure Automation State Configuration runbooks. Raadpleeg de volgende artikelen voor meer informatie:

- [Certificaten](./shared-resources/certificates.md)
- [Verbindingen](automation-connections.md)
- [Referenties](./shared-resources/credentials.md)
- [Variabelen](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Referentie-assets

DSC-configuraties in Azure Automation kunnen verwijzen naar Automation-referentie-assets met behulp van de `Get-AutomationPSCredential` cmdlet . Als een configuratie een parameter bevat die een object opgeeft, gebruikt u door de tekenreeksnaam van een Azure Automation referentie-asset door te geven aan de cmdlet om de referentie op `PSCredential` `Get-AutomationPSCredential` te halen. Gebruik dat object vervolgens voor de parameter waarvoor het -object is `PSCredential` vereist. Achter de schermen wordt de Azure Automation referentie-asset met die naam opgehaald en doorgegeven aan de configuratie. In het onderstaande voorbeeld wordt dit scenario in actie weergegeven.

Voor het beveiligen van referenties in knooppuntconfiguraties (MOF-configuratiedocumenten) moeten de referenties in het MOF-bestand van de knooppuntconfiguratie worden versleuteld. Op dit moment moet u PowerShell DSC toestemming geven om referenties in tekst zonder tekst uit te geven tijdens het genereren van de knooppuntconfiguratie MOF. PowerShell DSC is zich er niet van bewust dat Azure Automation het hele MOF-bestand na het genereren via een compilatie job versleutelt.

U kunt PowerShell DSC vertellen dat het prima is dat referenties als tekst zonder tekst worden uitgevoerd in de gegenereerde knooppuntconfiguratie-MOF's met behulp van configuratiegegevens. U moet doorgeven `PSDscAllowPlainTextPassword = $true` via `ConfigurationData` voor elke knooppuntbloknaam die wordt weergegeven in de DSC-configuratie en referenties gebruikt.

In het volgende voorbeeld ziet u een DSC-configuratie die gebruikmaakt van een Automation-referentie-asset.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

U kunt de voorgaande DSC-configuratie compileren met PowerShell. Met de volgende PowerShell-code worden twee knooppuntconfiguraties toegevoegd aan de Azure Automation State Configuration pull-server: **CredentialSample.MyVM1** en **CredentialSample.MyVM2.**

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Wanneer de compilatie is voltooid, ontvangt u mogelijk het foutbericht `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` U kunt dit bericht veilig negeren.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Uw DSC-configuratie compileren in Windows PowerShell

Het proces voor het compileren van DSC-configuraties in Windows PowerShell is opgenomen in de PowerShell DSC-documentatie Een [configuratie schrijven,](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)compileren en toepassen.
U kunt dit proces uitvoeren vanaf een werkstation voor ontwikkelaars of binnen een buildservice, zoals [Azure DevOps.](https://dev.azure.com) Vervolgens kunt u de MOF-bestanden importeren die worden geproduceerd door de configuratie te compileren in de Azure State Configuration service.

Compileren in Windows PowerShell biedt ook de optie om configuratie-inhoud te ondertekenen. De DSC-agent verifieert een ondertekende knooppuntconfiguratie lokaal op een beheerd knooppunt. Verificatie zorgt ervoor dat de configuratie die wordt toegepast op het knooppunt afkomstig is van een geautoriseerde bron.

U kunt ook knooppuntconfiguraties (MOF-bestanden) importeren die buiten Azure zijn gecompileerd. Het importeren omvat compilatie van een werkstation voor ontwikkelaars of in een service zoals [Azure DevOps.](https://dev.azure.com) Deze aanpak heeft meerdere voordelen, waaronder prestaties en betrouwbaarheid.

> [!NOTE]
> Een knooppuntconfiguratiebestand mag niet groter zijn dan 1 MB, zodat Azure Automation het kan importeren.

Zie Improvements [in WMF 5.1 - How to sign configuration and module (Verbeteringen in WMF 5.1 - Configuratie](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)en module ondertekenen) voor meer informatie over het ondertekenen van knooppuntconfiguraties.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Een knooppuntconfiguratie importeren in de Azure Portal

1. Klik in uw Automation-account op **Statusconfiguratie (DSC)** onder **Configuratiebeheer.**
1. Klik op de pagina Statusconfiguratie (DSC) op het **tabblad Configuraties** en klik vervolgens op **Toevoegen.**
1. Klik op de pagina Importeren op  het mappictogram naast het veld Knooppuntconfiguratiebestand om te bladeren naar een MOF-bestand voor knooppuntconfiguratie op uw lokale computer.

   ![Bladeren naar lokaal bestand](./media/automation-dsc-compile/import-browse.png)

1. Voer een naam in het **veld Configuratienaam** in. Deze naam moet overeenkomen met de naam van de configuratie van waaruit de knooppuntconfiguratie is gecompileerd.
1. Klik op **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Een knooppuntconfiguratie importeren met Azure PowerShell

U kunt de cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) gebruiken om een knooppuntconfiguratie te importeren in uw Automation-account.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Volgende stappen

- Zie Aan de slag met Azure Automation State Configuration om aan de [slag te gaan.](automation-dsc-getting-started.md)
- Zie Compile DSC configurations in Azure Automation State Configuration (DSC-configuraties compileren in Azure Automation State Configuration) voor meer informatie over het compileren van [DSC-configuraties, zodat u ze kunt toewijzen aan doelknooppunten.](automation-dsc-compile.md)
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
- Zie prijzen voor Azure Automation State Configuration informatie over [prijzen.](https://azure.microsoft.com/pricing/details/automation/)
- Zie Continue implementatie instellen met Chocolatey State Configuration voor een voorbeeld van het gebruik van State Configuration in een pijplijn voor [continue implementatie.](automation-dsc-cd-chocolatey.md)
