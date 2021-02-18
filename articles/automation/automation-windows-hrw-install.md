---
title: Een Windows-Hybrid Runbook Worker implementeren in Azure Automation
description: In dit artikel wordt uitgelegd hoe u een Hybrid Runbook Worker implementeert die u kunt gebruiken om runbooks uit te voeren op Windows-computers in uw lokale Data Center of in de cloud omgeving.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: f6858c7350e6c72a096b2f2bd5f4a4ff606bf023
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651354"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Een Windows-Hybrid Runbook Worker implementeren

U kunt de functie gebruikers Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks op een Azure-of niet-Azure-machine uit te voeren, inclusief servers die zijn geregistreerd bij [servers met Azure Arc-functionaliteit](../azure-arc/servers/overview.md). Op de machine of de server die als host fungeert voor de rol, kunt u runbooks rechtstreeks uitvoeren op de computer en resources in de omgeving om deze lokale resources te beheren.

Azure Automation runbooks opslaat en beheert en vervolgens aan een of meer aangewezen computers worden geleverd. In dit artikel wordt beschreven hoe u een gebruikers Hybrid Runbook Worker implementeert op een Windows-computer, hoe u de werk nemer verwijdert en hoe u een Hybrid Runbook Worker groep verwijdert.

Nadat u een runbook worker hebt geïmplementeerd, raadpleegt u [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md) voor meer informatie over het configureren van runbooks voor het automatiseren van processen in uw on-premises Data Center of een andere cloud omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt.

### <a name="a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte

De functie Hybrid Runbook Worker is afhankelijk van een Azure Monitor Log Analytics-werk ruimte om de rol te installeren en te configureren. U kunt dit maken via [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), via [Power shell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)of in de [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Als u geen Azure Monitor Log Analytics-werk ruimte hebt, raadpleegt u de [ontwerp richtlijnen voor Azure monitor logboek](../azure-monitor/logs/design-logs-deployment.md) voordat u de werk ruimte maakt.

### <a name="log-analytics-agent"></a>Log Analytics-agent

De Hybrid Runbook Worker-rol vereist de [log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md) voor het ondersteunde Windows-besturings systeem. Voor servers of computers die buiten Azure worden gehost, kunt u de Log Analytics-agent installeren met [servers met Azure Arc-functionaliteit](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Ondersteund Windows-besturings systeem

De functie Hybrid Runbook Worker ondersteunt de volgende besturings systemen:

* Windows Server 2019 (inclusief Server Core)
* Windows Server 2016, versie 1709 en 1803 (exclusief Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enter prise (inclusief meerdere sessies) en Pro
* Windows 8 Enter prise en Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimale vereisten

De minimale vereisten voor een Windows-systeem en-gebruikers Hybrid Runbook Worker zijn:

* Windows Power shell 5,1 ([down load WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)). Power shell core wordt niet ondersteund.
* .NET framework 4.6.2 of hoger
* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

### <a name="network-configuration"></a>Netwerkconfiguratie

Zie [uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor de netwerk vereisten voor de Hybrid Runbook Worker.

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Een machine toevoegen aan een Hybrid Runbook Worker groep

U kunt de werk computer toevoegen aan een Hybrid Runbook Worker groep in een van uw Automation-accounts. Voor computers die als host fungeren voor het systeem Hybrid Runbook worker die wordt beheerd door Updatebeheer, kunnen ze worden toegevoegd aan een Hybrid Runbook Worker groep. Maar u moet hetzelfde Automation-account gebruiken voor zowel Updatebeheer als het lidmaatschap van de Hybrid Runbook Worker groep.

>[!NOTE]
>Azure Automation [updatebeheer](./update-management/overview.md) installeert automatisch het systeem Hybrid Runbook worker op een Azure-of niet-Azure-machine die is ingeschakeld voor updatebeheer. Deze werk nemer is echter niet geregistreerd met Hybrid Runbook Worker groepen in uw Automation-account. Als u uw runbooks op deze computers wilt uitvoeren, moet u deze toevoegen aan een Hybrid Runbook Worker groep. Volg stap 6 onder de sectie [hand matige implementatie](#manual-deployment) om deze toe te voegen aan een groep.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Inschakelen voor beheer met Azure Automation status configuratie

Zie voor meer informatie over het inschakelen van machines voor beheer met Azure Automation status configuratie [machines inschakelen voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

> [!NOTE]
> Als u de configuratie wilt beheren van machines die ondersteuning bieden voor de Hybrid Runbook Worker rol met behulp van desired state Configuration (DSC), moet u de computers als DSC-knoop punten toevoegen.

## <a name="installation-options"></a>Installatieopties

Als u een Windows-gebruikers Hybrid Runbook Worker wilt installeren en configureren, kunt u een van de volgende methoden gebruiken.

* Gebruik een meegeleverd Power shell-script om het proces van het configureren van een of meer Windows-computers volledig te [automatiseren](#automated-deployment) . Dit is de aanbevolen methode voor machines in uw Data Center of een andere cloud omgeving.
* Importeer de Automation-oplossing hand matig, installeer de Log Analytics-agent voor Windows en configureer de rol werk nemer op de computer.

## <a name="automated-deployment"></a>Geautomatiseerde implementatie

De geautomatiseerde implementatie methode maakt gebruik van het Power shell-script **New-OnPremiseHybridWorker.ps1** voor het automatiseren en configureren van de Windows Hybrid Runbook worker-functie. Hiermee worden de volgende bewerkingen uitgevoerd:

* Installeert de benodigde modules
* Meld u aan met uw Azure-account
* Hiermee wordt gecontroleerd of de opgegeven resource groep en het Automation-account bestaan
* Hiermee maakt u verwijzingen naar de kenmerken van het Automation-account
* Hiermee wordt een Azure Monitor Log Analytics werk ruimte gemaakt als deze niet is opgegeven
* De Azure Automation oplossing in de werk ruimte inschakelen
* De Log Analytics-agent voor Windows downloaden en installeren
* Registreer de machine als Hybrid Runbook Worker

Voer de volgende stappen uit om de functie te installeren op de Windows-computer met behulp van het script.

1. Down load het **New-OnPremiseHybridWorker.ps1** script van de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Nadat u het script hebt gedownload, kopieert of uitvoert u het op de doel computer. Het **New-OnPremiseHybridWorker.ps1** script maakt gebruik van de volgende para meters tijdens de uitvoering.

    | Parameter | Status | Beschrijving |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Verplicht | De naam van de resource groep die is gekoppeld aan uw Automation-account. |
    | `AutomationAccountName` | Verplicht | De naam van uw Automation-account.
    | `Credential` | Optioneel | De referenties die moeten worden gebruikt wanneer u zich aanmeldt bij de Azure-omgeving. |
    | `HybridGroupName` | Verplicht | De naam van een Hybrid Runbook Worker groep die u opgeeft als doel voor de runbooks die dit scenario ondersteunen. |
    | `OMSResourceGroupName` | Optioneel | De naam van de resource groep voor de Log Analytics-werk ruimte. Als deze resource groep niet is opgegeven, wordt de waarde van `AAResourceGroupName` gebruikt. |
    | `SubscriptionID` | Verplicht | De id van het Azure-abonnement dat is gekoppeld aan uw Automation-account. |
    | `TenantID` | Optioneel | De id van de Tenant organisatie die aan uw Automation-account is gekoppeld. |
    | `WorkspaceName` | Optioneel | De naam van de Log Analytics werkruimte. Als u geen Log Analytics-werk ruimte hebt, maakt en configureert het script een. |

2. Open een 64-bits PowerShell-opdrachtprompt met verhoogde bevoegdheden.

3. Blader vanuit de Power shell-opdracht prompt naar de map die het script bevat dat u hebt gedownload. Wijzig de waarden voor de para meters,,,, `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` en `WorkspaceName` . Voer het script uit.

    Nadat u het script hebt uitgevoerd, wordt u gevraagd om te verifiëren bij Azure. U moet zich aanmelden met een account dat lid is van de rol **abonnements beheerders** en mede beheerder van het abonnement.

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

4. U wordt gevraagd om akkoord te gaan met de installatie van NuGet en om te verifiëren met uw Azure-referenties. Als u niet de nieuwste versie van NuGet hebt, kunt u deze downloaden via de [beschik bare NuGet-distributie versies](https://www.nuget.org/downloads).

5. Controleer de implementatie nadat het script is voltooid. Op de pagina **Hybrid Runbook worker groepen** in uw Automation-account, onder het tabblad **gebruikers Hybrid Runbook Workers Group** , worden de nieuwe groep en het aantal leden weer gegeven. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina vanuit het menu aan de linkerkant de optie **Hybrid Workers** kiezen. Op de pagina **Hybrid Workers** kunt u elk lid van de groep weer geven.

## <a name="manual-deployment"></a>Handmatige implementatie

Voer de volgende stappen uit om een Windows-Hybrid Runbook Worker te installeren en te configureren.

1. Schakel de Azure Automation oplossing in uw Log Analytics-werk ruimte in door de volgende opdracht uit te voeren in een Power shell-opdracht prompt met verhoogde bevoegdheid of in Cloud Shell in de [Azure Portal](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Implementeer de Log Analytics-agent op de doel computer.

    * Voor virtuele machines van Azure installeert u de Log Analytics agent voor Windows met behulp [van de Virtual Machine-extensie voor Windows](../virtual-machines/extensions/oms-windows.md). Met de uitbrei ding wordt de Log Analytics agent op virtuele machines van Azure geïnstalleerd en worden virtuele machines geregistreerd in een bestaande Log Analytics-werk ruimte. U kunt een Azure Resource Manager sjabloon, Power shell of Azure Policy gebruiken om de [Deploy log Analytics agent voor ingebouwde *Linux* -of *Windows* vm's](../governance/policy/samples/built-in-policies.md#monitoring) -beleid toe te wijzen. Zodra de agent is geïnstalleerd, kan de computer worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account.
    
    * Voor niet-Azure-machines kunt u de Log Analytics-agent installeren met [servers voor Azure Arc ingeschakeld](../azure-arc/servers/overview.md). Arc ingeschakelde servers ondersteunen de implementatie van de Log Analytics agent met behulp van de volgende methoden:
    
        - Het Framework van VM-extensies gebruiken.
        
            Met deze functie in azure Arc enabled servers kunt u de VM-extensie van Log Analytics agent implementeren op een niet-Azure Windows-en/of Linux-server. VM-extensies kunnen worden beheerd met behulp van de volgende methoden op uw hybride computers of servers die worden beheerd door servers met Arc-functionaliteit:
        
            - De [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - De [Azure cli](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager-sjablonen](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Azure Policy gebruiken.
        
            Met deze methode gebruikt u de Azure Policy [log Analytics agent implementeren voor Linux of Windows Azure Arc machines](../governance/policy/samples/built-in-policies.md#monitoring) ingebouwd beleid om te controleren of de op de Arc ingeschakelde server de log Analytics-agent is geïnstalleerd. Als de agent niet is geïnstalleerd, wordt deze automatisch geïmplementeerd met een herstel taak. Als u van plan bent om de machines met Azure Monitor voor VM's te controleren, gebruikt u in plaats daarvan het Azure Monitor voor VM's-initiatief [inschakelen](../governance/policy/samples/built-in-initiatives.md#monitoring) en configureert u de log Analytics agent.

    U kunt het beste de Log Analytics-agent voor Windows of Linux installeren met behulp van Azure Policy.

3. Controleren of de agent wordt gerapporteerd aan de werk ruimte

    De Log Analytics-agent voor Windows verbindt computers met een Azure Monitor Log Analytics-werk ruimte. Wanneer u de agent op de computer installeert en verbindt met uw werk ruimte, worden automatisch de onderdelen gedownload die vereist zijn voor de Hybrid Runbook Worker.

    Wanneer de agent na een paar minuten verbinding heeft gemaakt met uw Log Analytics-werk ruimte, kunt u de volgende query uitvoeren om te controleren of er heartbeat-gegevens naar de werk ruimte worden verzonden.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In de zoek resultaten ziet u heartbeat-records voor de machine, waarmee wordt aangegeven dat deze is verbonden en dat er wordt gerapporteerd aan de service. Standaard stuurt elke agent een heartbeat-record naar de toegewezen werk ruimte. Gebruik de volgende stappen om de installatie van de agent en de installatie te volt ooien.

4. Controleer de versie van de Hybrid Runbook Worker op de computer die als host fungeert voor de Log Analytics agent, blader naar `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` de submap **versie** en noteer deze. Deze map wordt enkele minuten weer gegeven nadat de oplossing is ingeschakeld in de werk ruimte.

5. Installeer de runbook-omgeving en maak verbinding met Azure Automation. Wanneer u een agent configureert om te rapporteren aan een Log Analytics-werk ruimte en de **Automation** -oplossing importeert, wordt de oplossing door de Power shell-module gepusht `HybridRegistration` . Deze module bevat de `Add-HybridRunbookWorker` cmdlet. Gebruik deze cmdlet om de runbook-omgeving op de computer te installeren en te registreren bij Azure Automation.

    Open een Power shell-sessie in de beheerders modus en voer de volgende opdrachten uit om de module te importeren.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Voer de `Add-HybridRunbookWorker` cmdlet uit om de waarden op te geven voor de para meters `Url` , `Key` en `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    U kunt de informatie ophalen die vereist is voor de para meters `Url` en `Key` van de pagina **sleutels** in uw Automation-account. Selecteer **sleutels** onder de sectie **account instellingen** aan de linkerkant van de pagina.

    ![Pagina sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `Url`Kopieer de waarde voor **URL** voor de para meter.

    * Kopieer voor de `Key` para meter de waarde voor de **primaire toegangs sleutel**.

    * Gebruik de `GroupName` naam van de Hybrid Runbook worker groep voor de para meter. Als deze groep al bestaat in het Automation-account, wordt de huidige machine hieraan toegevoegd. Als deze groep niet bestaat, wordt deze toegevoegd.

    * Stel, indien nodig, de `Verbose` para meter in om details over de installatie te ontvangen.

7. Controleer de implementatie nadat de opdracht is voltooid. Op de pagina **Hybrid Runbook worker groepen** in uw Automation-account, onder het tabblad **gebruikers Hybrid Runbook Workers Group** , worden de nieuwe of bestaande groep en het aantal leden weer gegeven. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina vanuit het menu aan de linkerkant de optie **Hybrid Workers** kiezen. Op de pagina **Hybrid Workers** kunt u elk lid van de groep weer geven.

## <a name="install-powershell-modules"></a>Power shell-modules installeren

Runbooks kunnen gebruikmaken van de activiteiten en cmdlets die zijn gedefinieerd in de modules die in uw Azure Automation omgeving zijn geïnstalleerd. Omdat deze modules niet automatisch worden geïmplementeerd op on-premises machines, moet u ze hand matig installeren. De uitzonde ring hierop is de Azure-module. Deze module wordt standaard geïnstalleerd en biedt toegang tot cmdlets voor alle Azure-Services en-activiteiten voor Azure Automation.

Omdat het primaire doel van de Hybrid Runbook Worker is om lokale bronnen te beheren, moet u waarschijnlijk de modules installeren die deze bronnen ondersteunen, met name de `PowerShellGet` module. Zie [Windows Power shell](/powershell/scripting/developer/windows-powershell)voor meer informatie over het installeren van Windows Power shell-modules.

Modules die zijn geïnstalleerd, moeten zich bevinden in een locatie waarnaar wordt verwezen door de `PSModulePath` omgevings variabele, zodat de Hybrid worker deze automatisch kan importeren. Zie [Installing modules in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module)(Engelstalig) voor meer informatie.

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>De Hybrid Runbook Worker verwijderen

1. Ga in het Azure Portal naar uw Automation-account.

2. Onder **account instellingen** selecteert u **sleutels** en noteert u de waarden voor **URL** en **primaire toegangs sleutel**.

3. Open een Power shell-sessie in de beheerders modus en voer de volgende opdracht uit met uw URL en primaire toegangs sleutel waarden. Gebruik de `Verbose` para meter voor een gedetailleerd logboek van het verwijderings proces. Als u verouderde machines uit uw Hybrid Worker groep wilt verwijderen, gebruikt u de optionele `machineName` para meter.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een Hybrid Runbook Worker groep wilt verwijderen, moet u eerst de Hybrid Runbook Worker verwijderen van elke computer die lid is van de groep. Gebruik vervolgens de volgende stappen om de groep te verwijderen:

1. Open het Automation-account in de Azure Portal.

2. Selecteer **Hybrid worker groups** onder **Process Automation**. Selecteer de groep die u wilt verwijderen. De eigenschappen pagina voor die groep wordt weer gegeven.

   ![De pagina Eigenschappen](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Selecteer op de pagina eigenschappen voor de geselecteerde groep de optie **verwijderen**. Er wordt een bericht gevraagd om deze actie te bevestigen. Selecteer **Ja** als u zeker weet dat u wilt door gaan.

   ![Bevestigingsbericht](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Het kan enkele seconden duren voordat dit proces is voltooid. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.

* Zie [problemen met Hybrid Runbook worker oplossen](troubleshoot/hybrid-runbook-worker.md#general)voor meer informatie over het oplossen van problemen met uw Hybrid Runbook Workers.
