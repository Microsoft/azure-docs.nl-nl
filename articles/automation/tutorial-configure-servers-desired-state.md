---
title: Machines configureren met een gewenste status in Azure Automation
description: In dit artikel wordt beschreven hoe u machines naar een gewenste status configureert met behulp Azure Automation State Configuration.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70631490e248c801b9465c51548efe8dff704a3e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830138"
---
# <a name="configure-machines-to-a-desired-state"></a>Machines configureren met een gewenste status

Azure Automation State Configuration kunt u configuraties voor uw servers opgeven en ervoor zorgen dat deze servers gedurende een bepaalde periode de opgegeven status hebben.

> [!div class="checklist"]
> - Onboarding van een VM die moet worden beheerd door Azure Automation DSC
> - Een configuratie uploaden naar Azure Automation
> - Een configuratie compileren in een knooppuntconfiguratie
> - Een knooppuntconfiguratie toewijzen aan een beheerd knooppunt
> - De nalevingsstatus van een beheerd knooppunt controleren

Voor deze zelfstudie gebruiken we een eenvoudige [DSC-configuratie die](/powershell/scripting/dsc/configurations/configurations) ervoor zorgt dat IIS op de VM wordt geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure Automation-account. Zie Overzicht van Automation-accountverificatie voor meer informatie over een [Automation-account en de vereisten ervan.](./automation-security-overview.md)
- Een Azure Resource Manager -VM (niet klassiek) met Windows Server 2008 R2 of hoger. Zie Uw eerste virtuele [Windows-machine](../virtual-machines/windows/quick-create-portal.md)maken in de Azure Portal voor instructies over het maken van een Azure Portal.
- Azure PowerShell module versie 3.6 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/azurerm/install-azurerm-ps).
- Bekendheid met Desired State Configuration (DSC). Zie overzicht voor meer informatie [Windows PowerShell Desired State Configuration DSC.](/powershell/scripting/dsc/overview/overview)

## <a name="support-for-partial-configurations"></a>Ondersteuning voor gedeeltelijke configuraties

Azure Automation State Configuration ondersteunt het gebruik van [gedeeltelijke configuraties.](/powershell/scripting/dsc/pull-server/partialconfigs) In dit scenario is DSC geconfigureerd voor het onafhankelijk beheren van meerdere configuraties en wordt elke configuratie opgehaald uit Azure Automation. Er kan echter slechts één configuratie worden toegewezen aan een knooppunt per Automation-account. Dit betekent dat als u twee configuraties voor een knooppunt gebruikt, u twee Automation-accounts nodig hebt.

Zie de documentatie voor gedeeltelijke configuraties voor meer informatie over het registreren van een gedeeltelijke configuratie van een [pull-service.](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)

Zie Understanding [DSC's role in a CI/CD Pipeline (Informatie](/powershell/scripting/dsc/overview/authoringadvanced)over de rol van DSC in een CI/CD-pijplijn) voor meer informatie over hoe teams gezamenlijk servers kunnen beheren met behulp van configuratie als code.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Een configuratie maken en uploaden naar Azure Automation

Typ het volgende in een teksteditor en sla het lokaal op als **TestConfig.ps1**.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> In geavanceerdere scenario's waarin u wilt dat meerdere modules worden geïmporteerd die DSC-resources bieden, moet u ervoor zorgen dat elke module een unieke `Import-DscResource` regel in uw configuratie heeft.

Roep de cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) aan om de configuratie te uploaden naar uw Automation-account.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Een configuratie compileren in een knooppuntconfiguratie

Een DSC-configuratie moet worden gecompileerd in een knooppuntconfiguratie voordat deze kan worden toegewezen aan een knooppunt. Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations).

Roep de cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) aan om de configuratie te compileren in een knooppuntconfiguratie met `TestConfig` de naam in uw `TestConfig.WebServer` Automation-account.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Een VM registreren die moet worden beheerd door State Configuration

U kunt Azure Automation State Configuration gebruiken voor het beheren van Azure-VM's (zowel klassieke als Resource Manager), on-premises VM's, Linux-machines, AWS-VM's en on-premises fysieke machines. In dit onderwerp wordt besproken hoe u alleen virtuele Azure Resource Manager registreren. Zie Onboarding machines for management by Azure Automation State Configuration [(Machines onboarden](automation-dsc-onboarding.md)voor beheer door ) voor meer informatie over het registreren Azure Automation State Configuration .

Roep de cmdlet [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) aan om uw virtuele Azure Automation State Configuration te registreren als een beheerd knooppunt. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Instellingen voor de configuratiemodus opgeven

Gebruik de cmdlet [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) om een VM te registreren als een beheerd knooppunt en configuratie-eigenschappen op te geven. U kunt bijvoorbeeld opgeven dat de status van de machine slechts één keer moet worden toegepast door op te geven als de `ApplyOnly` waarde van de eigenschap `ConfigurationMode` . State Configuration probeert de configuratie niet toe te passen na de eerste controle.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

U kunt ook opgeven hoe vaak DSC de configuratietoestand controleert met behulp van de `ConfigurationModeFrequencyMins` eigenschap . Zie Configuring the Local Configuration Manager voor meer informatie [over DSC-configuratie-instellingen.](/powershell/scripting/dsc/managing-nodes/metaConfig)

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Een knooppuntconfiguratie toewijzen aan een beheerd knooppunt

Nu kunnen we de gecompileerde knooppuntconfiguratie toewijzen aan de VM die we willen configureren.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Hiermee wordt de knooppuntconfiguratie met de naam `TestConfig.WebServer` toegewezen aan het geregistreerde DSC-knooppunt `DscVm` . Standaard wordt het DSC-knooppunt elke 30 minuten gecontroleerd op naleving van de knooppuntconfiguratie. Zie Configuring the Local Configuration Manager voor meer informatie over het wijzigen van het [compatibiliteitscontrole-interval.](/powershell/scripting/dsc/managing-nodes/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>De nalevingsstatus van een beheerd knooppunt controleren

U kunt rapporten over de nalevingsstatus van een beheerd knooppunt krijgen met behulp van de cmdlet [Get-AzAutomationDscNodeReport.](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Knooppunten verwijderen uit service

Wanneer u een knooppunt aan Azure Automation State Configuration toevoegt, worden de instellingen in Local Configuration Manager ingesteld op registratie bij de service, pull-configuraties en vereiste modules om de machine te configureren.
Als u ervoor kiest om het knooppunt uit de service te verwijderen, kunt u dit doen met behulp van de Azure Portal of de Az-cmdlets.

> [!NOTE]
> Als u de registratie van een knooppunt bij de service ongedaan maakt, worden alleen de lokale Configuration Manager ingesteld, zodat het knooppunt geen verbinding meer maakt met de service.
> Dit heeft geen invloed op de configuratie die momenteel wordt toegepast op het knooppunt.
> Als u de huidige configuratie wilt verwijderen, gebruikt u [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) of verwijdert u het lokale configuratiebestand (dit is de enige optie voor Linux-knooppunten).

### <a name="azure-portal"></a>Azure Portal

Klik Azure Automation op **Statusconfiguratie (DSC)** in de inhoudsopgave.
Klik vervolgens **op Knooppunten** om de lijst met knooppunten weer te bieden die zijn geregistreerd bij de service.
Klik op de naam van het knooppunt dat u wilt verwijderen.
Klik in de knooppuntweergave die wordt geopend op **Registratie ongedaan maken.**

### <a name="powershell"></a>PowerShell

Als u de registratie van een knooppunt bij Azure Automation State Configuration-service ongedaan wilt maken met behulp van PowerShell, volgt u de documentatie voor de cmdlet [Unregister-AzAutomationDscNode.](/powershell/module/az.automation/unregister-azautomationdscnode)

## <a name="next-steps"></a>Volgende stappen

- Zie Aan de slag met Azure Automation State Configuration om aan de [slag te gaan.](automation-dsc-getting-started.md)
- Zie Enable Azure Automation State Configuration (Knooppunten [inschakelen) voor meer informatie over het inschakelen Azure Automation State Configuration.](automation-dsc-onboarding.md)
- Zie Compile DSC configurations in Azure Automation State Configuration (DSC-configuraties compileren in Azure Automation State Configuration) voor meer informatie over het compileren van [DSC-configuraties, zodat u ze kunt toewijzen aan doelknooppunten.](automation-dsc-compile.md)
- Zie Continue implementatie instellen met Chocolatey Azure Automation State Configuration een voorbeeld van het gebruik van een toepassing in [een pijplijn voor](automation-dsc-cd-chocolatey.md)continue implementatie.
- Zie prijzen voor Azure Automation State Configuration informatie over [prijzen.](https://azure.microsoft.com/pricing/details/automation/)
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
