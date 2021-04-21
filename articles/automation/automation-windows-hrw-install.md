---
title: Een Windows-Hybrid Runbook Worker implementeren in Azure Automation
description: In dit artikel wordt beschreven hoe u Hybrid Runbook Worker implementeert die u kunt gebruiken om runbooks uit te voeren op Windows-computers in uw lokale datacenter of cloudomgeving.
services: automation
ms.subservice: process-automation
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4bf27ffc888e189f15e1c435309cbeddb1c11fec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830336"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Een Windows-Hybrid Runbook Worker

U kunt de gebruikersfunctie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op een Azure- of niet-Azure-machine, inclusief servers die zijn geregistreerd [bij Azure Arc-servers](../azure-arc/servers/overview.md). Vanaf de computer of server die als host voor de rol wordt gebruikt, kunt u runbooks rechtstreeks op de rol uitvoeren en op resources in de omgeving om deze lokale resources te beheren.

Azure Automation slaat runbooks op en beheert ze en levert ze vervolgens aan een of meer aangewezen machines. In dit artikel wordt beschreven hoe u een Hybrid Runbook Worker implementeert op een Windows-computer, hoe u de werkrol verwijdert en hoe u een Hybrid Runbook Worker verwijdert.

Nadat u een runbook worker hebt geïmplementeerd, bekijkt u [Runbooks](automation-hrw-run-runbooks.md) uitvoeren op een Hybrid Runbook Worker voor meer informatie over het configureren van uw runbooks voor het automatiseren van processen in uw on-premises datacenter of andere cloudomgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u het volgende hebt.

### <a name="a-log-analytics-workspace"></a>Een Log Analytics-werkruimte

De Hybrid Runbook Worker is afhankelijk van een Azure Monitor Log Analytics-werkruimte om de rol te installeren en te configureren. U kunt deze maken [via Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)of in [de Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Als u geen log analytics Azure Monitor werkruimte hebt, bekijkt u de Azure Monitor [logontwerp voordat](../azure-monitor/logs/design-logs-deployment.md) u de werkruimte maakt.

### <a name="log-analytics-agent"></a>Log Analytics-agent

De Hybrid Runbook Worker vereist de [Log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md) voor het ondersteunde Windows-besturingssysteem. Voor servers of machines die buiten Azure worden gehost, kunt u de Log Analytics-agent installeren met behulp [Azure Arc ingeschakelde servers.](../azure-arc/servers/overview.md)

### <a name="supported-windows-operating-system"></a>Ondersteund Windows-besturingssysteem

De Hybrid Runbook Worker ondersteunt de volgende besturingssystemen:

* Windows Server 2019 (inclusief Server Core)
* Windows Server 2016, versie 1709 en 1803 (met uitzondering van Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inclusief meerdere sessies) en Pro
* Windows 8 Enterprise en Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimale vereisten

De minimale vereisten voor een Windows-systeem en Hybrid Runbook Worker zijn:

* Windows PowerShell 5.1[(download WMF 5.1).](https://www.microsoft.com/download/details.aspx?id=54616) PowerShell Core wordt niet ondersteund.
* .NET framework 4.6.2 of hoger
* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="network-configuration"></a>Netwerkconfiguratie

Zie Uw netwerk configureren Hybrid Runbook Worker voor [netwerkvereisten voor de Hybrid Runbook Worker.](automation-hybrid-runbook-worker.md#network-planning)

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Een machine toevoegen aan een Hybrid Runbook Worker groep

U kunt de werkmachine toevoegen aan een Hybrid Runbook Worker in een van uw Automation-accounts. Voor computers die als host voor de Hybrid Runbook Worker van het systeem worden Updatebeheer, kunnen ze worden toegevoegd aan een Hybrid Runbook Worker groep. U moet echter hetzelfde Automation-account gebruiken voor zowel Updatebeheer als Hybrid Runbook Worker groepslidmaatschap.

>[!NOTE]
>Azure Automation [Updatebeheer](./update-management/overview.md) installeert automatisch de systeem-Hybrid Runbook Worker op een Azure- of niet-Azure-machine die is ingeschakeld voor Updatebeheer. Deze werkmedewerker is echter niet geregistreerd bij Hybrid Runbook Worker in uw Automation-account. Als u uw runbooks op deze machines wilt uitvoeren, moet u ze toevoegen aan een Hybrid Runbook Worker groep. Volg stap 6 in de sectie [Handmatige implementatie om](#manual-deployment) deze toe te voegen aan een groep.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Inschakelen voor beheer met Azure Automation State Configuration

Zie Machines inschakelen voor beheer door Azure Automation State Configuration voor meer informatie over het inschakelen van [computers voor Azure Automation State Configuration.](automation-dsc-onboarding.md)

> [!NOTE]
> Voor het beheren van de configuratie van machines die de Hybrid Runbook Worker-rol met Desired State Configuration (DSC) ondersteunen, moet u de machines toevoegen als DSC-knooppunten.

## <a name="installation-options"></a>Installatieopties

Als u een Windows-gebruikersaccount wilt Hybrid Runbook Worker configureren, kunt u een van de volgende methoden gebruiken.

* Gebruik een opgegeven PowerShell-script om [het](#automated-deployment) proces voor het configureren van een of meer Windows-machines volledig te automatiseren. Dit is de aanbevolen methode voor machines in uw datacenter of een andere cloudomgeving.
* Importeer de Automation-oplossing handmatig, installeer de Log Analytics-agent voor Windows en configureer de werkrol op de computer.

## <a name="automated-deployment"></a>Geautomatiseerde implementatie

Er zijn twee methoden om automatisch een Hybrid Runbook Worker. U kunt een runbook importeren uit de Runbook Gallery in de Azure Portal en uitvoeren, of u kunt handmatig een script downloaden van de PowerShell Gallery.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Een runbook importeren uit de Runbook Gallery

De importprocedure wordt gedetailleerd beschreven in [Runbooks importeren vanuit GitHub met de Azure Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). De naam van het te importeren runbook is **Create Automation Windows HybridWorker**.

Het runbook maakt gebruik van de volgende parameters.

| Parameter | Status | Beschrijving |
| ------- | ----- | ----------- |
| `Location` | Verplicht | De locatie voor de Log Analytics-werkruimte. |
| `ResourceGroupName` | Verplicht | De resourcegroep voor uw Automation-account. |
| `AccountName` | Verplicht | De Automation-accountnaam waarin de Hybrid Run Worker wordt geregistreerd. |
| `CreateLA` | Verplicht | Indien waar, gebruikt de waarde van om `WorkspaceName` een Log Analytics-werkruimte te maken. Indien onwaar, moet de `WorkspaceName` waarde van verwijzen naar een bestaande werkruimte. |
| `LAlocation` | Optioneel | De locatie waar de Log Analytics-werkruimte wordt gemaakt of waar deze al bestaat. |
| `WorkspaceName` | Optioneel | De naam van de Log Analytics-werkruimte die moet worden gebruikt. |
| `CreateVM` | Verplicht | Indien waar, gebruikt u de waarde `VMName` van als de naam van een nieuwe VM. Indien onwaar, gebruikt `VMName` u om een bestaande VM te zoeken en te registreren. |
| `VMName` | Optioneel | De naam van de virtuele machine die wordt gemaakt of geregistreerd, afhankelijk van de waarde van `CreateVM` . |
| `VMImage` | Optioneel | De naam van de VM-afbeelding die moet worden gemaakt. |
| `VMlocation` | Optioneel | Locatie van de VM die is gemaakt of geregistreerd. Als deze locatie niet is opgegeven, wordt de waarde `LAlocation` van gebruikt. |
| `RegisterHW` | Verplicht | Indien waar, registreert u de VM als hybrid worker. |
| `WorkerGroupName` | Verplicht | Naam van de Hybrid Worker groep. |

### <a name="download-a-script-from-the-powershell-gallery"></a>Download een script van de PowerShell Gallery

Deze geautomatiseerde implementatiemethode maakt gebruik van het PowerShell-script **New-OnPremiseHybridWorker.ps1** voor het automatiseren en configureren van de Windows Hybrid Runbook Worker rol. Hiermee wordt het volgende uitgevoerd:

* Installeert de benodigde modules
* Meldt u aan met uw Azure-account
* Controleert of de opgegeven resourcegroep en het Automation-account bestaan
* Hiermee maakt u verwijzingen naar Automation-accountkenmerken
* Maakt een Azure Monitor Log Analytics-werkruimte als deze niet is opgegeven
* De oplossing Azure Automation inschakelen in de werkruimte
* De Log Analytics-agent voor Windows downloaden en installeren
* Registreer de machine als Hybrid Runbook Worker

Voer de volgende stappen uit om de rol op uw Windows-computer te installeren met behulp van het script .

1. Download het **New-OnPremiseHybridWorker.ps1script** van de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Nadat u het script hebt gedownload, kopieert of voer het uit op de doelmachine. Het script maakt gebruik van de volgende parameters.

    | Parameter | Status | Beschrijving |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Verplicht | De naam van de resourcegroep die is gekoppeld aan uw Automation-account. |
    | `AutomationAccountName` | Verplicht | De naam van uw Automation-account.
    | `Credential` | Optioneel | De referenties die moeten worden gebruikt bij het aanmelden bij de Azure-omgeving. |
    | `HybridGroupName` | Verplicht | De naam van een Hybrid Runbook Worker die u opgeeft als doel voor de runbooks die ondersteuning bieden voor dit scenario. |
    | `OMSResourceGroupName` | Optioneel | De naam van de resourcegroep voor de Log Analytics-werkruimte. Als deze resourcegroep niet is opgegeven, wordt de waarde `AAResourceGroupName` van gebruikt. |
    | `SubscriptionID` | Verplicht | De id van het Azure-abonnement dat is gekoppeld aan uw Automation-account. |
    | `TenantID` | Optioneel | De id van de tenantorganisatie die is gekoppeld aan uw Automation-account. |
    | `WorkspaceName` | Optioneel | De naam van de Log Analytics-werkruimte. Als u geen Log Analytics-werkruimte hebt, maakt en configureert het script er een. |

1. Open een 64-bits PowerShell-opdrachtprompt met verhoogde bevoegdheden.

1. Blader vanaf de PowerShell-opdrachtprompt naar de map met het script dat u hebt gedownload. Wijzig de waarden voor de parameters `AutomationAccountName` , , , , en `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` `WorkspaceName` . Voer vervolgens het script uit.

    U wordt gevraagd om u te verifiëren bij Azure nadat u het script hebt uitgevoerd. U moet zich aanmelden met een account dat lid is van de rol **Abonnementsbeheerders** en medebeheerder is van het abonnement.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

1. U wordt gevraagd om akkoord te gaan met de installatie van NuGet en om u te verifiëren met uw Azure-referenties. Als u niet de nieuwste NuGet-versie hebt, kunt u deze downloaden via Beschikbare [NuGet-distributieversies.](https://www.nuget.org/downloads)

1. Controleer de implementatie nadat het script is voltooid. Op de **pagina Hybrid Runbook Worker in** uw Automation-account, onder het tabblad Gebruikers van de groep Hybrid **Runbook Workers,** worden de nieuwe groep en het aantal leden weergegeven. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina. Kies in het linkermenu **Hybrid Workers.** Op de **pagina Hybrid Workers** ziet u elk lid van de groep.

## <a name="manual-deployment"></a>Handmatige implementatie

Voer de volgende stappen uit om Hybrid Runbook Worker Windows-apparaat te installeren en configureren.

1. Schakel de Azure Automation-oplossing in uw Log Analytics-werkruimte in door de volgende opdracht uit te voeren in een PowerShell-opdrachtprompt met verhoogde bevoegdheid of in Cloud Shell in [Azure Portal](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Implementeer de Log Analytics-agent op de doelmachine.

    * Voor Azure-VM's installeert u de Log Analytics-agent voor Windows met behulp van [de extensie voor virtuele machines voor Windows.](../virtual-machines/extensions/oms-windows.md) Met de extensie wordt de Log Analytics-agent geïnstalleerd op virtuele Azure-machines en worden virtuele machines ingeschreven bij een bestaande Log Analytics-werkruimte. U kunt een Azure Resource Manager, PowerShell of Azure Policy gebruiken om het ingebouwde beleid Log Analytics-agent implementeren voor [ *Linux-* of Windows-VM's](../governance/policy/samples/built-in-policies.md#monitoring) toe te wijzen. Zodra de agent is geïnstalleerd, kan de computer worden toegevoegd aan een Hybrid Runbook Worker in uw Automation-account.
    
    * Voor niet-Azure-machines kunt u de Log Analytics-agent installeren met behulp [Azure Arc ingeschakelde servers.](../azure-arc/servers/overview.md) Arc-servers ondersteunen het implementeren van de Log Analytics-agent met behulp van de volgende methoden:
    
        - Het framework voor VM-extensies gebruiken.
        
            Met deze functie in Azure Arc ingeschakelde servers kunt u de VM-extensie van de Log Analytics-agent implementeren op een niet-Azure Windows- en/of Linux-server. VM-extensies kunnen worden beheerd met de volgende methoden op uw hybride machines of servers die worden beheerd door Arc-servers:
        
            - De [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - De [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager sjablonen](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Met Azure Policy.
        
            Met deze methode gebruikt u het ingebouwde beleid Azure Policy Deploy Log Analytics agent to Linux or [Windows Azure Arc machines (Log Analytics-agent](../governance/policy/samples/built-in-policies.md#monitoring) implementeren op Linux- of Windows Azure Arc-machines) om te controleren of de Log Analytics-agent op de Arc-server is geïnstalleerd. Als de agent niet is geïnstalleerd, wordt deze automatisch geïmplementeerd met behulp van een hersteltaak. Als u van plan bent om de machines te bewaken met Azure Monitor voor VM's, gebruikt u in plaats daarvan het initiatief [Enable Azure Monitor voor VM's](../governance/policy/samples/built-in-initiatives.md#monitoring) om de Log Analytics-agent te installeren en configureren.

    U wordt aangeraden de Log Analytics-agent voor Windows of Linux te installeren met behulp van Azure Policy.

1. Controleren of de agent rapporteert aan de werkruimte

    De Log Analytics-agent voor Windows verbindt machines met een Azure Monitor Log Analytics-werkruimte. Wanneer u de agent op uw computer installeert en deze verbindt met uw werkruimte, worden automatisch de onderdelen gedownload die vereist zijn voor de Hybrid Runbook Worker.

    Wanneer de agent na enkele minuten verbinding heeft met uw Log Analytics-werkruimte, kunt u de volgende query uitvoeren om te controleren of deze heartbeatgegevens naar de werkruimte stuurt.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In de zoekresultaten ziet u heartbeatrecords voor de machine, waarmee wordt aangegeven dat deze is verbonden en rapporteert aan de service. Standaard wordt door elke agent een heartbeat-record doorgestuurd naar de toegewezen werkruimte. Gebruik de volgende stappen om de installatie en installatie van de agent te voltooien.

1. Bevestig de versie van de Hybrid Runbook Worker op de computer die als host voor de Log Analytics-agent wordt gebruikt. Blader naar en `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` noteer de  submap versie. Deze map wordt enkele minuten nadat de oplossing is ingeschakeld in de werkruimte weergegeven op de computer.

1. Installeer de runbookomgeving en maak verbinding met Azure Automation. Wanneer u een agent configureert om te rapporteren aan een Log Analytics-werkruimte en de **Automation-oplossing** importeert, wordt de `HybridRegistration` PowerShell-module naar beneden ge pusht. Deze module bevat de `Add-HybridRunbookWorker` cmdlet . Gebruik deze cmdlet om de runbookomgeving op de computer te installeren en deze te registreren bij Azure Automation.

    Open een PowerShell-sessie in de beheerdersmodus en voer de volgende opdrachten uit om de module te importeren.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. Voer de `Add-HybridRunbookWorker` cmdlet uit die de waarden voor de parameters `Url` , en `Key` `GroupName` opgeeft.

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    U kunt de vereiste gegevens voor de parameters en `Url` op de pagina `Key` **Sleutels** in uw Automation-account ophalen. Selecteer **Sleutels** in **de sectie Accountinstellingen** aan de linkerkant van de pagina.

    ![Pagina Sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Kopieer voor `Url` de parameter de waarde voor **URL**.

    * Kopieer voor `Key` de parameter de waarde voor PRIMARY ACCESS **KEY.**

    * Gebruik voor `GroupName` de parameter de naam van de Hybrid Runbook Worker groep. Als deze groep al in het Automation-account bestaat, wordt de huidige computer eraan toegevoegd. Als deze groep niet bestaat, wordt deze toegevoegd.

    * Stel indien nodig de `Verbose` parameter in om details over de installatie te ontvangen.

1. Controleer de implementatie nadat de opdracht is voltooid. Op de **Hybrid Runbook Worker groepen** in uw Automation-account, onder het tabblad Gebruikers van de groep Hybrid **Runbook Workers,** ziet u de nieuwe of bestaande groep en het aantal leden. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina. Kies in het linkermenu **Hybrid Workers.** Op de **pagina Hybrid Workers** ziet u elk lid van de groep.

## <a name="install-powershell-modules"></a>PowerShell-modules installeren

Runbooks kunnen gebruikmaken van alle activiteiten en cmdlets die zijn gedefinieerd in de modules die in uw Azure Automation geïnstalleerd. Omdat deze modules niet automatisch worden geïmplementeerd op on-premises machines, moet u ze handmatig installeren. De uitzondering hierop is de Azure-module. Deze module is standaard geïnstalleerd en biedt toegang tot cmdlets voor alle Azure-services en -activiteiten voor Azure Automation.

Omdat het primaire doel van de Hybrid Runbook Worker is om lokale resources te beheren, moet u waarschijnlijk de modules installeren die ondersteuning bieden voor deze resources, met name de `PowerShellGet` module. Zie voor meer informatie Windows PowerShell het installeren [van Windows PowerShell.](/powershell/scripting/developer/windows-powershell)

Modules die zijn geïnstalleerd, moeten zich bevinden op een locatie waarnaar wordt verwezen door de omgevingsvariabele, zodat de `PSModulePath` Hybrid Worker ze automatisch kan importeren. Zie Modules installeren [in PSModulePath voor meer informatie.](/powershell/scripting/developer/module/installing-a-powershell-module)

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>De Hybrid Runbook Worker

1. Ga in Azure Portal naar uw Automation-account.

1. Selecteer **onder Accountinstellingen** de optie **Sleutels** en noteer de waarden voor **URL** en **Primaire toegangssleutel.**

1. Open een PowerShell-sessie in de beheerdersmodus en voer de volgende opdracht uit met de waarden voor uw URL en primaire toegangssleutel. Gebruik de `Verbose` parameter voor een gedetailleerd logboek van het verwijderingsproces. Als u verouderde machines wilt verwijderen uit Hybrid Worker groep, gebruikt u de optionele `machineName` parameter .

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een Hybrid Runbook Worker groep wilt verwijderen, moet u eerst de Hybrid Runbook Worker verwijderen van elke computer die lid is van de groep. Gebruik vervolgens de volgende stappen om de groep te verwijderen:

1. Open het Automation-account in het Azure Portal.

1. Selecteer **Hybrid Worker-groepen** onder **Procesautomatisering.** Selecteer de groep die u wilt verwijderen. De eigenschappenpagina voor die groep wordt weergegeven.

   ![De pagina Eigenschappen](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Selecteer verwijderen op de eigenschappenpagina voor de **geselecteerde groep.** Er wordt een bericht weergegeven waarin u wordt gevraagd om deze actie te bevestigen. Selecteer **Ja** als u zeker weet dat u wilt doorgaan.

   ![Bevestigingsbericht](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dit proces kan enkele seconden duren. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

## <a name="next-steps"></a>Volgende stappen

* Zie Runbooks uitvoeren op een locatie voor meer informatie over het configureren van uw run Hybrid Runbook Worker books voor het automatiseren van processen in uw on-premises datacenter of andere [cloudomgeving.](automation-hrw-run-runbooks.md)

* Zie Problemen met Hybrid Runbook Workers oplossen voor meer informatie over het oplossen [Hybrid Runbook Worker problemen.](troubleshoot/hybrid-runbook-worker.md#general)
