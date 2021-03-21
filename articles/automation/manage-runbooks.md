---
title: Runbooks in Azure Automation beheren
description: In dit artikel leest u hoe u runbooks beheert in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.openlocfilehash: af767ab37e8e77195b7d13b24ea78f4fb88485fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122133"
---
# <a name="manage-runbooks-in-azure-automation"></a>Runbooks in Azure Automation beheren

U kunt een runbook toevoegen aan Azure Automation door ofwel een nieuwe te maken of een bestaande te importeren uit een bestand of de [runbook-galerie](automation-runbook-gallery.md). Dit artikel bevat informatie over het beheren van een runbook dat is geïmporteerd uit een bestand. U kunt alle details van de toegang tot de Community-runbooks en-modules in [Runbook en module galerieën voor Azure Automation](automation-runbook-gallery.md)vinden.

## <a name="create-a-runbook"></a>Een runbook maken

Een nieuw runbook maken in Azure Automation met behulp van de Azure Portal of Windows Power shell. Zodra het runbook is gemaakt, kunt u het bewerken met behulp van informatie in:

* [Tekst runbook bewerken in Azure Automation](automation-edit-textual-runbook.md)
* [Meer informatie over belang rijke Windows Power shell-werk stroom concepten voor Automation-runbooks](automation-powershell-workflow.md)
* [Python 2-pakketten beheren in Azure Automation](python-packages.md)
* [Python 3-pakketten (preview) in Azure Automation beheren](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Een runbook maken in de Azure Portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer in de hub **Runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.
3. Klik op **een Runbook maken**.
4. Voer een naam in voor het runbook en selecteer het bijbehorende [type](automation-runbook-types.md). De naam van het runbook moet beginnen met een letter en mag letters, cijfers, onderstrepings tekens en streepjes bevatten.
5. Klik op **maken** om het runbook te maken en de editor te openen.

### <a name="create-a-runbook-with-powershell"></a>Een runbook maken met Power shell

Gebruik de cmdlet [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) om een leeg runbook te maken. Gebruik de `Type` para meter om een van de typen runbook op te geven die zijn gedefinieerd voor `New-AzAutomationRunbook` .

In het volgende voor beeld ziet u hoe u een nieuw leeg runbook maakt.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Een runbook importeren

U kunt een Power shell-of Power shell workflow-script (**. ps1**), een grafisch runbook (**. graphrunbook**) of een python 2-of python 3-script (**. py**) importeren om uw eigen runbook te maken. U moet het [type runbook](automation-runbook-types.md) opgeven dat tijdens het importeren wordt gemaakt, waarbij rekening wordt gehouden met de volgende overwegingen.

* U kunt een **. ps1** -bestand importeren dat geen werk stroom bevat in een [Power shell-Runbook](automation-runbook-types.md#powershell-runbooks) of een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Als u deze importeert in een Power shell workflow-runbook, wordt dit geconverteerd naar een werk stroom. In dit geval worden opmerkingen in het runbook opgenomen om de aangebrachte wijzigingen te beschrijven.

* U kunt alleen een **. ps1** -bestand met een Power shell-werk stroom importeren in een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Als het bestand meerdere Power shell-werk stromen bevat, mislukt het importeren. U moet elke werk stroom opslaan in een eigen bestand en deze afzonderlijk importeren.

* Importeer geen **. ps1** -bestand met een Power shell-werk stroom in een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks), omdat het niet door de Power shell-script engine kan worden herkend.

* Importeer alleen een **. graphrunbook** -bestand in een nieuw [grafisch runbook](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Een runbook importeren vanuit de Azure Portal

U kunt de volgende procedure gebruiken om een script bestand te importeren in Azure Automation.

> [!NOTE]
> U kunt een **. ps1** -bestand alleen importeren in een Power shell workflow-runbook met behulp van de portal.

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst van runbooks te openen.
3. Klik op **een Runbook importeren**.
4. Klik op **Runbook-bestand** en selecteer het bestand dat u wilt importeren.
5. Als het veld **naam** is ingeschakeld, kunt u de naam van het runbook wijzigen. De naam moet beginnen met een letter en mag letters, cijfers, onderstrepings tekens en streepjes bevatten.
6. Het [runbook-type](automation-runbook-types.md) wordt automatisch geselecteerd, maar u kunt het type wijzigen nadat u de toepasselijke beperkingen in het account hebt genomen.
7. Klik op **Create**. Het nieuwe runbook wordt weer gegeven in de lijst met runbooks voor het Automation-account.
8. U moet [het runbook publiceren](#publish-a-runbook) voordat u het kunt uitvoeren.

> [!NOTE]
> Nadat u een grafisch runbook hebt geïmporteerd, kunt u het naar een ander type converteren. U kunt een grafisch runbook echter niet converteren naar een tekst runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Een runbook importeren met Windows Power shell

Gebruik de cmdlet [import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) om een script bestand te importeren als een concept-runbook. Als het runbook al bestaat, mislukt het importeren tenzij u de `Force` para meter met de cmdlet gebruikt.

In het volgende voor beeld ziet u hoe u een script bestand importeert in een runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Resources verwerken

Als uw runbook een [resource](automation-runbook-execution.md#resources)maakt, moet u controleren of de resource al bestaat voordat u deze probeert te maken. Hier volgt een voor beeld van een basis.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Details ophalen uit het activiteiten logboek

U kunt de details van het runbook ophalen, zoals de persoon of het account waarmee een runbook is gestart, vanuit het [activiteiten logboek](automation-runbook-execution.md#activity-logging) voor het Automation-account. Het volgende Power shell-voor beeld bevat de laatste gebruiker om het opgegeven runbook uit te voeren.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Voortgang bijhouden

Het is een goed idee om uw runbooks te maken die kunnen worden gemigreerd, met logica die opnieuw kan worden gebruikt en eenvoudig opnieuw kan worden gestart. Het bijhouden van de voortgang in een runbook zorgt ervoor dat de runbook-logica correct wordt uitgevoerd als er problemen zijn.

U kunt de voortgang van een runbook volgen met behulp van een externe bron, zoals een opslag account, een Data Base of gedeelde bestanden. Maak logica in uw runbook om eerst de status van de laatste uitgevoerde actie te controleren. Op basis van de resultaten van de controle kan de logica specifieke taken in het runbook overs Laan of voortzetten.

## <a name="prevent-concurrent-jobs"></a>Gelijktijdige taken voor komen

Sommige runbooks gedragen zich niet alleen als ze in meerdere taken tegelijk worden uitgevoerd. In dit geval is het belang rijk dat een runbook logica implementeert om te bepalen of er al een actieve taak is. Hier volgt een voor beeld van een basis.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Tijdelijke fouten verwerken in een script dat afhankelijk is van tijd

Uw runbooks moeten robuust en geschikt zijn voor het afhandelen van [fouten](automation-runbook-execution.md#errors), met inbegrip van tijdelijke fouten die ertoe kunnen leiden dat ze opnieuw worden opgestart of mislukken. Als een runbook is mislukt, Azure Automation opnieuw proberen.

Als uw runbook normaal gesp roken binnen een tijds beperking wordt uitgevoerd, laat u het script de logica implementeren om de uitvoerings tijd te controleren. Met deze controle wordt de uitvoering van bewerkingen, zoals opstarten, afsluiten of uitschalen, alleen tijdens bepaalde tijden gegarandeerd.

> [!NOTE]
> De lokale tijd op het Azure sandbox-proces is ingesteld op UTC. Berekeningen voor de datum en tijd in uw runbooks moeten in overweging worden genomen.

## <a name="work-with-multiple-subscriptions"></a>Met meerdere abonnementen werken

Uw runbook moet kunnen werken met [abonnementen](automation-runbook-execution.md#subscriptions). Als u bijvoorbeeld meerdere abonnementen wilt verwerken, gebruikt het runbook de cmdlet [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) . Met deze cmdlet zorgt u ervoor dat de verificatie context niet wordt opgehaald uit een ander runbook dat wordt uitgevoerd in dezelfde sandbox. Het runbook gebruikt ook de `Get-AzContext` cmdlet om de context van de huidige sessie op te halen en deze toe te wijzen aan de variabele `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
$AzureContext = Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint `
-Subscription $Conn.SubscriptionId

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
-ResourceGroupName $ResourceGroupName `
-AutomationAccountName $AutomationAccountName `
-Name $ChildRunbookName `
-DefaultProfile $AzureContext
```

## <a name="work-with-a-custom-script"></a>Werken met een aangepast script

> [!NOTE]
> U kunt doorgaans geen aangepaste scripts en runbooks uitvoeren op de host waarop een Log Analytics-agent is geïnstalleerd.

Een aangepast script gebruiken:

1. Een Automation-account maken en een [rol voor Inzender](automation-role-based-access-control.md)verkrijgen.
2. [Koppel het account aan de Azure-werk ruimte](../security-center/security-center-enable-data-collection.md).
3. Schakel [Hybrid Runbook worker](automation-hybrid-runbook-worker.md), [updatebeheer](./update-management/overview.md)of een andere functie voor automatisering in. 
4. Als u op een Linux-computer beschikt over hoge machtigingen. Meld u aan om [handtekening controles uit te scha kelen](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Een runbook testen

Wanneer u een runbook test, wordt de [concept versie](#publish-a-runbook) uitgevoerd en de acties die worden uitgevoerd, worden voltooid. Er is geen taak geschiedenis gemaakt, maar de [uitvoer](automation-runbook-output-and-messages.md#use-the-output-stream) -en [waarschuwings-en fout](automation-runbook-output-and-messages.md#working-with-message-streams) stromen worden weer gegeven in het deel venster test uitvoer. Berichten naar de [uitgebreide stroom](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) worden alleen weer gegeven in het deel venster uitvoer als de variabele [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) is ingesteld op `Continue` .

Hoewel de concept versie wordt uitgevoerd, wordt het runbook nog steeds normaal uitgevoerd en worden alle acties uitgevoerd voor resources in de omgeving. Daarom moet u runbooks alleen testen op niet-productie resources.

De procedure voor het testen [van elk type runbook](automation-runbook-types.md) is hetzelfde. Er is geen verschil in het testen tussen de tekst editor en de grafische editor in de Azure Portal.

1. Open de concept versie van het runbook in de [tekst editor](automation-edit-textual-runbook.md) of de [grafische editor](automation-graphical-authoring-intro.md).
1. Klik op **testen** om de pagina test te openen.
1. Als het runbook para meters heeft, worden deze weer gegeven in het linkerdeel venster, waar u waarden kunt opgeven die moeten worden gebruikt voor de test.
1. Als u de test wilt uitvoeren op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md), wijzigt u de instellingen voor het **uitvoeren** van de **Hybrid worker** en selecteert u de naam van de doel groep.  Als dat niet het geval is, moet u de test in de Cloud uitvoeren met de standaard **Azure** .
1. Klik op **Start** om de test te starten.
1. U kunt de knoppen onder het deel venster uitvoer gebruiken om een [Power shell-werk stroom](automation-runbook-types.md#powershell-workflow-runbooks) of [grafisch](automation-runbook-types.md#graphical-runbooks) runbook te stoppen of te onderbreken tijdens het testen. Wanneer u het runbook onderbreekt, wordt de huidige activiteit voltooid voordat het runbook wordt onderbroken. Als het runbook is onderbroken, kunt u het stoppen of opnieuw starten.
1. Controleer de uitvoer van het runbook in het deel venster uitvoer.

## <a name="publish-a-runbook"></a>Een runbook publiceren

Wanneer u een nieuw runbook maakt of importeert, moet u het publiceren voordat u het kunt uitvoeren. Elk runbook in Azure Automation heeft een concept versie en een gepubliceerde versie. Alleen de gepubliceerde versie is beschikbaar om te worden uitgevoerd en alleen de conceptversie kan worden bewerkt. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de concept versie beschikbaar moet worden gemaakt, publiceert u deze en wordt de huidige gepubliceerde versie overschreven met de concept versie.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Een runbook publiceren in de Azure Portal

1. Open het runbook in de Azure Portal.
2. Klik op **Bewerken**.
3. Klik op **publiceren** en vervolgens op **Ja** in antwoord op het verificatie bericht.

### <a name="publish-a-runbook-using-powershell"></a>Een runbook publiceren met Power shell

Gebruik de cmdlet [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) om uw runbook te publiceren. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Een runbook inplannen in Azure Portal

Als uw runbook is gepubliceerd, kunt u het voor de bewerking plannen:

1. Open het runbook in de Azure Portal.
2. Selecteer **schema's** onder **resources**.
3. Selecteer **een schema toevoegen**.
4. Selecteer **een planning aan uw Runbook koppelen** in het deel venster Runbook plannen.
5. Kies **een nieuw schema maken** in het deel venster planning.
6. Voer een naam, beschrijving en andere para meters in het deel venster nieuwe planning in.
7. Wanneer de planning is gemaakt, markeert u deze en klikt u op **OK**. Deze moet nu zijn gekoppeld aan uw runbook.
8. Zoek een e-mail in uw postvak om u te informeren over de status van het runbook.

## <a name="obtain-job-statuses"></a>Taak statussen ophalen

### <a name="view-statuses-in-the-azure-portal"></a>Statussen weer geven in de Azure Portal

Details over de verwerking van taken in Azure Automation zijn in [taken](automation-runbook-execution.md#jobs)opgenomen. Wanneer u klaar bent om uw runbook-taken weer te geven, gebruikt u Azure Portal en opent u uw Automation-account. Aan de rechter kant ziet u een overzicht van alle runbook-taken in **taak statistieken**.

![Taak statistieken tegel](./media/manage-runbooks/automation-account-job-status-summary.png)

In de samen vatting wordt een telling en grafische weer gave van de taak status weer gegeven voor elke uitgevoerde taak.

Als u op de tegel klikt, wordt de pagina taken weer gegeven, die een overzicht bevat van alle uitgevoerde taken. Op deze pagina worden de status, de runbook-naam, de start tijd en de voltooiings tijd voor elke taak weer gegeven.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Scherm afbeelding van de pagina taken.":::

U kunt de lijst met taken filteren door **taken filteren** te selecteren. Filter op een specifiek runbook, de taak status of een keuze in de vervolg keuzelijst en geef het tijds bereik voor de zoek opdracht op.

![Taak status filteren](./media/manage-runbooks/automation-account-jobs-filter.png)

U kunt ook samenvattings Details van taken weer geven voor een specifiek runbook door dat runbook te selecteren op de pagina Runbooks in uw Automation-account en vervolgens **taken** te selecteren. Deze actie geeft de pagina taken weer. Hier kunt u klikken op een taak record om de details en de uitvoer weer te geven.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Scherm afbeelding van de pagina taken met de knop fouten gemarkeerd.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Taak statussen ophalen met behulp van Power shell

Gebruik de cmdlet [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) om de taken op te halen die voor een runbook zijn gemaakt en de details van een bepaalde taak. Als u een runbook start met `Start-AzAutomationRunbook` , wordt de resulterende taak geretourneerd. Gebruik [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) om taak uitvoer op te halen.

In het volgende voor beeld wordt de laatste taak voor een voor beeld-runbook opgehaald en worden de status, de opgegeven waarden voor de runbook-para meters en de taak uitvoer weer gegeven.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

In het volgende voor beeld wordt de uitvoer voor een specifieke taak opgehaald en wordt elke record geretourneerd. Als er een [uitzonde ring](automation-runbook-execution.md#exceptions) is voor een van de records, schrijft het script de uitzonde ring in plaats van de waarde. Dit gedrag is nuttig omdat uitzonde ringen aanvullende informatie kunnen bieden die mogelijk niet normaal in de uitvoer wordt geregistreerd.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [runbook-uitvoering in azure Automation](automation-runbook-execution.md)voor meer informatie over het beheren van runbook.
* Zie [tekst Runbooks bewerken in azure Automation](automation-edit-textual-runbook.md)om een Power shell-runbook voor te bereiden.
* Zie problemen met [Runbook oplossen](troubleshoot/runbooks.md)voor informatie over het oplossen van problemen met het uitvoeren van een runbook.