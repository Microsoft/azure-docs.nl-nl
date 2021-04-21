---
title: Runbooks beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u runbooks in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a172189d8b52a80fc50e7d8c882859f7855aeca8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830084"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbooks beheren in Azure Automation

U kunt een runbook toevoegen aan Azure Automation door een nieuw runbook te maken of een bestaand runbook uit een bestand of de [Runbook Gallery te importeren.](automation-runbook-gallery.md) Dit artikel bevat informatie over het beheren van een runbook dat is geïmporteerd uit een bestand. U vindt alle details over het openen van community-runbooks en -modules in [Runbook-](automation-runbook-gallery.md)en modulegalerieën voor Azure Automation.

## <a name="create-a-runbook"></a>Een runbook maken

Maak een nieuw runbook in Azure Automation met behulp van Azure Portal of Windows PowerShell. Zodra het runbook is gemaakt, kunt u het bewerken met behulp van informatie in:

* [Tekstrunbook bewerken in Azure Automation](automation-edit-textual-runbook.md)
* [Meer informatie over Windows PowerShell workflowconcepten voor Automation-runbooks](automation-powershell-workflow.md)
* [Python 2-pakketten beheren in Azure Automation](python-packages.md)
* [Python 3-pakketten beheren (preview) in Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Een runbook maken in de Azure Portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks onder** **Procesautomatisering om** de lijst met runbooks te openen.
3. Klik **op Een runbook maken.**
4. Voer een naam in voor het runbook en selecteer het [type](automation-runbook-types.md). De runbooknaam moet beginnen met een letter en kan letters, cijfers, onderstrepingstekens en streepjes bevatten.
5. Klik **op Maken** om het runbook te maken en de editor te openen.

### <a name="create-a-runbook-with-powershell"></a>Een runbook maken met PowerShell

Gebruik de [cmdlet New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) om een leeg runbook te maken. Gebruik de `Type` parameter om een van de runbooktypen op te geven die zijn gedefinieerd voor `New-AzAutomationRunbook` .

In het volgende voorbeeld ziet u hoe u een nieuw leeg runbook maakt.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Een runbook importeren

U kunt een PowerShell- of PowerShell Workflow(.ps1)-script, een grafisch runbook **(.graphrunbook)** of een Python 2- of Python 3-script **(.py)** importeren om uw eigen runbook te maken. U moet het [type runbook opgeven](automation-runbook-types.md) dat tijdens het importeren wordt gemaakt, waarbij u rekening houdt met de volgende overwegingen.

* U kunt een **PS1-bestand dat** geen werkstroom bevat, importeren in een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) of een [PowerShell Workflow-runbook.](automation-runbook-types.md#powershell-workflow-runbooks) Als u het in een PowerShell Workflow-runbook importeert, wordt het geconverteerd naar een werkstroom. In dit geval worden opmerkingen opgenomen in het runbook om de aangebrachte wijzigingen te beschrijven.

* U kunt alleen een **PS1-bestand met** een PowerShell Workflow importeren in een [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Als het bestand meerdere PowerShell-werkstromen bevat, mislukt het importeren. U moet elke werkstroom opslaan in een eigen bestand en elke werkstroom afzonderlijk importeren.

* Importeer geen **.ps1-bestand** met een PowerShell Workflow in een [PowerShell-runbook,](automation-runbook-types.md#powershell-runbooks)omdat de PowerShell-scripten engine dit niet kan herkennen.

* Importeer alleen **een .graphrunbook-bestand** in een nieuw [grafisch runbook](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Een runbook importeren uit de Azure Portal

U kunt de volgende procedure gebruiken om een scriptbestand te importeren in Azure Automation.

> [!NOTE]
> U kunt alleen een **PS1-bestand importeren** in een PowerShell Workflow-runbook via de portal.

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst van runbooks te openen.
3. Klik **op Een runbook importeren.**
4. Klik **op Runbookbestand** en selecteer het bestand dat u wilt importeren.
5. Als het **veld Naam** is ingeschakeld, kunt u de naam van het runbook wijzigen. De naam moet beginnen met een letter en mag letters, cijfers, onderstrepingstekens en streepjes bevatten.
6. Het [runbooktype](automation-runbook-types.md) wordt automatisch geselecteerd, maar u kunt het type wijzigen nadat u rekening hebt gehouden met de toepasselijke beperkingen.
7. Klik op **Create**. Het nieuwe runbook wordt weergegeven in de lijst met runbooks voor het Automation-account.
8. U moet [het runbook publiceren voordat](#publish-a-runbook) u het kunt uitvoeren.

> [!NOTE]
> Nadat u een grafisch runbook hebt geïmporteerd, kunt u dit converteren naar een ander type. U kunt een grafisch runbook echter niet converteren naar een tekstueel runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Een runbook importeren met Windows PowerShell

Gebruik de cmdlet [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) om een scriptbestand als conceptrunbook te importeren. Als het runbook al bestaat, mislukt het importeren, tenzij u de `Force` parameter met de cmdlet gebruikt.

In het volgende voorbeeld ziet u hoe u een scriptbestand in een runbook importeert.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Resources verwerken

Als uw runbook een [resource maakt,](automation-runbook-execution.md#resources)moet het script controleren of de resource al bestaat voordat u deze probeert te maken. Hier is een eenvoudig voorbeeld.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Details ophalen uit activiteitenlogboek

U kunt runbookgegevens, zoals de persoon of het account die een runbook heeft gestart, ophalen uit het activiteitenlogboek [voor](automation-runbook-execution.md#activity-logging) het Automation-account. Het volgende PowerShell-voorbeeld bevat de laatste gebruiker die het opgegeven runbook heeft uitgevoerd.

```powershell-interactive
$SubID = '00000000-0000-0000-0000-000000000000'
$AutoRgName = 'MyResourceGroup'
$aaName = 'MyAutomationAccount'
$RunbookName = 'MyRunbook'
$StartTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $AutoRgName
    StartTime         = $StartTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $RunbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $AutoRgName
            AutomationAccountName = $aaName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $RunbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Voortgang bijhouden

Het is een goed idee om uw runbooks modulair van aard te maken, met logica die eenvoudig opnieuw kan worden gebruikt en opnieuw kan worden gestart. Het bijhouden van de voortgang in een runbook zorgt ervoor dat de runbooklogica correct wordt uitgevoerd als er problemen zijn.

U kunt de voortgang van een runbook volgen met behulp van een externe bron, zoals een opslagaccount, een database of gedeelde bestanden. Maak logica in uw runbook om eerst de status van de laatst ondernomen actie te controleren. Op basis van de resultaten van de controle kan de logica specifieke taken in het runbook overslaan of doorgaan.

## <a name="prevent-concurrent-jobs"></a>Gelijktijdige taken voorkomen

Sommige runbooks gedragen zich vreemd als ze op meerdere taken tegelijk worden uitgevoerd. In dit geval is het belangrijk dat een runbook logica implementeert om te bepalen of er al een taak wordt uitgevoerd. Hier is een eenvoudig voorbeeld.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams
$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook [$runbookName] is already running"
    exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Tijdelijke fouten in een tijdafhankelijk script verwerken

Uw runbooks moeten robuust zijn en fouten kunnen [verwerken,](automation-runbook-execution.md#errors)inclusief tijdelijke fouten die ervoor kunnen zorgen dat ze opnieuw worden opgestart of mislukken. Als een runbook uitvalt, Azure Automation het opnieuw proberen.

Als uw runbook normaal gesproken binnen een tijdsbeperking wordt uitgevoerd, laat u het script logica implementeren om de uitvoeringstijd te controleren. Deze controle zorgt ervoor dat bewerkingen zoals opstarten, afsluiten of uitschalen alleen op specifieke tijdstippen worden uitgevoerd.

> [!NOTE]
> De lokale tijd in het Azure-sandboxproces is ingesteld op UTC. Berekeningen voor datum en tijd in uw runbooks moeten rekening houden met dit feit.

## <a name="work-with-multiple-subscriptions"></a>Met meerdere abonnementen werken

Uw runbook moet kunnen werken met [abonnementen](automation-runbook-execution.md#subscriptions). Als u bijvoorbeeld meerdere abonnementen wilt verwerken, gebruikt het runbook de cmdlet [Disable-AzContextAutosave.](/powershell/module/Az.Accounts/Disable-AzContextAutosave) Deze cmdlet zorgt ervoor dat de verificatiecontext niet wordt opgehaald uit een ander runbook dat in dezelfde sandbox wordt uitgevoerd. Het runbook gebruikt ook de cmdlet om de context van de huidige sessie op te halen `Get-AzContext` en toe te wijzen aan de variabele `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams

$ChildRunbookName = 'ChildRunbookDemo'
$aaName = 'MyAutomationAccount'
$rgName = 'MyResourceGroup'

$startParams = @{
    ResourceGroupName     = $rgName
    AutomationAccountName = $aaName
    Name                  = $ChildRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

## <a name="work-with-a-custom-script"></a>Werken met een aangepast script

> [!NOTE]
> Normaal gesproken kunt u geen aangepaste scripts en runbooks uitvoeren op de host met een Log Analytics-agent geïnstalleerd.

Een aangepast script gebruiken:

1. Maak een Automation-account en verkrijg de [rol Inzender.](automation-role-based-access-control.md)
2. [Koppel het account aan de Azure-werkruimte](../security-center/security-center-enable-data-collection.md).
3. Schakel [Hybrid Runbook Worker,](automation-hybrid-runbook-worker.md) [Updatebeheer](./update-management/overview.md)of een andere Automation-functie in. 
4. Als u een Linux-computer gebruikt, hebt u hoge machtigingen nodig. Meld u aan om [handtekeningcontroles uit te schakelen.](automation-linux-hrw-install.md#turn-off-signature-validation)

## <a name="test-a-runbook"></a>Een runbook testen

Wanneer u een runbook test, wordt [de Concept-versie](#publish-a-runbook) uitgevoerd en alle acties die worden uitgevoerd, worden voltooid. Er wordt geen taakgeschiedenis gemaakt, maar de [uitvoer-](automation-runbook-output-and-messages.md#use-the-output-stream) en waarschuwings- en [foutstromen](automation-runbook-output-and-messages.md#working-with-message-streams) worden weergegeven in het deelvenster Testuitvoer. Berichten naar [de uitgebreide stroom worden](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) alleen weergegeven in het deelvenster Uitvoer als de variabele [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) is ingesteld op `Continue` .

Hoewel de conceptversie wordt uitgevoerd, wordt het runbook nog steeds normaal uitgevoerd en worden er acties uitgevoerd op resources in de omgeving. Daarom moet u runbooks alleen testen op niet-productiebronnen.

De procedure voor het testen [van elk type runbook](automation-runbook-types.md) is hetzelfde. Er is geen verschil in het testen tussen de teksteditor en de grafische editor in de Azure Portal.

1. Open de Concept-versie van het runbook in de [teksteditor](automation-edit-textual-runbook.md) of de [grafische editor](automation-graphical-authoring-intro.md).
1. Klik **op Testen** om de pagina Testen te openen.
1. Als het runbook parameters bevat, worden deze weergegeven in het linkerdeelvenster, waar u waarden kunt opgeven die voor de test moeten worden gebruikt.
1. Als u de test wilt uitvoeren op een  [Hybrid Runbook Worker,](automation-hybrid-runbook-worker.md)wijzigt u Instellingen uitvoeren in **Hybrid Worker** selecteert u de naam van de doelgroep.  Laat anders de azure-standaardwaarde **staan om** de test uit te voeren in de cloud.
1. Klik op **Start** om de test te starten.
1. U kunt de knoppen onder het deelvenster Uitvoer gebruiken om een [PowerShell-werkstroom](automation-runbook-types.md#powershell-workflow-runbooks) of grafisch [runbook](automation-runbook-types.md#graphical-runbooks) te stoppen of op te schorten terwijl het wordt getest. Wanneer u het runbook onderbreekt, wordt de huidige activiteit voltooid voordat het runbook wordt onderbroken. Als het runbook is onderbroken, kunt u het stoppen of opnieuw starten.
1. Inspecteer de uitvoer van het runbook in het deelvenster Uitvoer.

## <a name="publish-a-runbook"></a>Een runbook publiceren

Wanneer u een nieuw runbook maakt of importeert, moet u het publiceren voordat u het kunt uitvoeren. Elk runbook in Azure Automation heeft een conceptversie en een gepubliceerde versie. Alleen de gepubliceerde versie is beschikbaar om te worden uitgevoerd en alleen de conceptversie kan worden bewerkt. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de conceptversie beschikbaar moet worden gesteld, publiceert u deze en overschrijft u de huidige gepubliceerde versie met de conceptversie.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Een runbook publiceren in de Azure Portal

1. Open het runbook in de Azure Portal.
2. Klik op **Bewerken**.
3. Klik **op Publiceren** en vervolgens op **Ja** als reactie op het verificatiebericht.

### <a name="publish-a-runbook-using-powershell"></a>Een runbook publiceren met PowerShell

Gebruik de cmdlet [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) om uw runbook te publiceren. 

```azurepowershell-interactive
$aaName = "MyAutomationAccount"
$RunbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $aaName
    ResourceGroupName     = $rgName
    Name                  = $RunbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Een runbook inplannen in Azure Portal

Wanneer uw runbook is gepubliceerd, kunt u dit plannen voor bewerking:

1. Open het runbook in de Azure Portal.
2. Selecteer **Schema's** onder **Resources.**
3. Selecteer **Een schema toevoegen.**
4. Selecteer in het deelvenster Runbook plannen de optie **Een planning aan uw runbook koppelen.**
5. Kies **Een nieuw schema maken** in het deelvenster Planning.
6. Voer een naam, beschrijving en andere parameters in het deelvenster Nieuw schema in.
7. Zodra de planning is gemaakt, markeert u deze en klikt u op **OK.** Deze moet nu worden gekoppeld aan uw runbook.
8. Zoek een e-mailbericht in uw postvak om u op de hoogte te stellen van de status van het runbook.

## <a name="obtain-job-statuses"></a>Taakstatussen verkrijgen

### <a name="view-statuses-in-the-azure-portal"></a>Statussen in de Azure Portal

Details van de taakafhandeling in Azure Automation worden gegeven in [Taken](automation-runbook-execution.md#jobs). Wanneer u klaar bent om uw runbooktaken te zien, gebruikt u Azure Portal en hebt u toegang tot uw Automation-account. Aan de rechterkant ziet u een samenvatting van alle runbooktaken in **Taakstatistieken.**

![Tegel Taakstatistieken](./media/manage-runbooks/automation-account-job-status-summary.png)

In de samenvatting ziet u een telling en grafische weergave van de taakstatus voor elke uitgevoerde taak.

Als u op de tegel klikt, wordt de pagina Taken weergegeven, met een overzicht van alle taken die worden uitgevoerd. Op deze pagina worden de status, de naam van het runbook, de begintijd en de voltooiingstijd voor elke taak weergegeven.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Schermopname van de pagina Taken.":::

U kunt de lijst met taken filteren door **Taken filteren te selecteren.** Filter op een specifiek runbook, taakstatus of een keuze uit de vervolgkeuzelijst en geef het tijdsbereik voor de zoekopdracht op.

![Taakstatus filteren](./media/manage-runbooks/automation-account-jobs-filter.png)

U kunt ook de details van het taakoverzicht voor een specifiek runbook weergeven door dat runbook te selecteren op de pagina Runbooks in uw Automation-account en vervolgens **Taken te selecteren.** Met deze actie wordt de pagina Taken weergegeven. Hier kunt u op een taakrecord klikken om de details en uitvoer ervan weer te geven.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Schermopname van de pagina Taken met de knop Fouten gemarkeerd.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Taakstatussen ophalen met Behulp van PowerShell

Gebruik de cmdlet [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) om de taken op te halen die zijn gemaakt voor een runbook en de details van een bepaalde taak. Als u een runbook start met `Start-AzAutomationRunbook` behulp van , wordt de resulterende taak retourneert. Gebruik [Get-AzAutomationJobOutput om taakuitvoer](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) op te halen.

In het volgende voorbeeld wordt de laatste taak voor een voorbeeldrunbook opgenomen en worden de status, de opgegeven waarden voor de runbookparameters en de taakuitvoer weergegeven.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

In het volgende voorbeeld wordt de uitvoer voor een specifieke taak opgehaald en wordt elke record opgehaald. Als er een uitzondering is [voor een](automation-runbook-execution.md#exceptions) van de records, schrijft het script de uitzondering in plaats van de waarde . Dit gedrag is handig omdat uitzonderingen aanvullende informatie kunnen bieden die mogelijk niet normaal wordt geregistreerd tijdens de uitvoer.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie Runbookuitvoering in Azure Automation voor meer [informatie over runbookbeheer.](automation-runbook-execution.md)
* Zie Tekstuele runbooks bewerken in Azure Automation om een [PowerShell-runbook voor te Azure Automation.](automation-edit-textual-runbook.md)
* Zie Problemen met runbook oplossen voor het oplossen van problemen met [runbookuitvoering.](troubleshoot/runbooks.md)
