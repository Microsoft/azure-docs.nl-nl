---
title: Start a runbook in Azure Automation (Een runbook starten in Azure Automation)
description: In dit artikel leest u hoe u een runbook start in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b5c5166785ad8c82c114fb7193cd49716536b408
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896593"
---
# <a name="start-a-runbook-in-azure-automation"></a>Start a runbook in Azure Automation (Een runbook starten in Azure Automation)

De volgende tabel helpt u bij het bepalen van de methode voor het starten van een runbook in Azure Automation dat het meest geschikt is voor uw specifieke scenario. Dit artikel bevat informatie over het starten van een runbook met de Azure Portal en met Windows Power shell. Meer informatie over de andere methoden vindt u in andere documentatie die u kunt openen via de onderstaande koppelingen.

| **Methode** | **Kenmerken** |
| --- | --- |
| [Azure-portal](#start-a-runbook-with-the-azure-portal) |<li>Eenvoudigste methode met interactieve gebruikers interface.<br> <li>Formulier om eenvoudige parameter waarden op te geven.<br> <li>De taak status eenvoudig bijhouden.<br> <li>Toegang is geverifieerd met Azure-aanmelding. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Aanroepen vanaf de opdracht regel met Windows Power shell-cmdlets.<br> <li>Kan worden opgenomen in de geautomatiseerde functie met meerdere stappen.<br> <li>De aanvraag is geverifieerd met het certificaat of de OAuth User Principal/Service-Principal.<br> <li>Eenvoudige en complexe parameter waarden opgeven.<br> <li>De taak status bijhouden.<br> <li>De client is vereist voor de ondersteuning van Power shell-cmdlets. |
| [Azure Automation-API](/rest/api/automation/) |<li>De meest flexibele methode, maar ook het meest complexe.<br> <li>Roep een aangepaste code aan die HTTP-aanvragen kan maken.<br> <li>Aanvraag is geverifieerd met certificaat, of OAuth User Principal/Service Principal.<br> <li>Eenvoudige en complexe parameter waarden opgeven. *Als u een python-runbook aanroept met behulp van de API, moet de JSON-nettolading worden geserialiseerd.*<br> <li>De taak status bijhouden. |
| [Webhooks](automation-webhooks.md) |<li>Het runbook starten vanuit een enkele HTTP-aanvraag.<br> <li>Is geverifieerd met een beveiligings token in een URL.<br> <li>De client kan de parameter waarden die zijn opgegeven tijdens het maken van de webhook niet overschrijven. Met Runbook kan één para meter worden gedefinieerd die wordt gevuld met de details van de HTTP-aanvraag.<br> <li>Het is niet mogelijk om de taak status via de webhook-URL bij te houden. |
| [Reageren op de Azure-waarschuwing](../azure-monitor/platform/alerts-overview.md) |<li>Een runbook starten in reactie op de Azure-waarschuwing.<br> <li>Configureer webhook voor runbook en koppel deze aan een waarschuwing.<br> <li>Is geverifieerd met een beveiligings token in een URL. |
| [Schema](./shared-resources/schedules.md) |<li>Runbook automatisch starten op elk uur, dagelijks, wekelijks of maandelijks schema.<br> <li>Bewerk het schema via Azure Portal, Power shell-cmdlets of de Azure-API.<br> <li>Geef parameter waarden op die met schema moeten worden gebruikt. |
| [Vanuit een ander Runbook](automation-child-runbooks.md) |<li>Gebruik een runbook als een activiteit in een ander runbook.<br> <li>Handig voor functionaliteit die wordt gebruikt door meerdere runbooks.<br> <li>Geef parameter waarden op als onderliggend runbook en gebruik uitvoer in het bovenliggende runbook. |

In de volgende afbeelding ziet u een gedetailleerd stapsgewijs proces in de levens cyclus van een runbook. Het bevat verschillende manieren waarop een runbook wordt gestart in Azure Automation, welke onderdelen vereist zijn voor het uitvoeren Hybrid Runbook Worker van Azure Automation runbooks en interacties tussen verschillende onderdelen. Voor meer informatie over het uitvoeren van Automation-runbooks in uw Data Center raadpleegt u [Hybrid runbook Workers](automation-hybrid-runbook-worker.md)

![Runbook-architectuur](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Werken met runbook-para meters

Wanneer u een runbook start vanuit de Azure Portal of Windows Power shell, wordt de instructie verzonden via de Azure Automation-webservice. Deze service biedt geen ondersteuning voor para meters met complexe gegevens typen. Als u een waarde moet opgeven voor een complexe para meter, moet u deze inline vanuit een ander runbook aanroepen, zoals wordt beschreven in [onderliggende runbooks in azure Automation](automation-child-runbooks.md).

De Azure Automation-webservice biedt speciale functionaliteit voor para meters die bepaalde gegevens typen gebruiken, zoals beschreven in de volgende secties:

### <a name="named-values"></a>Benoemde waarden

Als de para meter van het gegevens type [object] is, kunt u de volgende JSON-indeling gebruiken om een lijst met benoemde waarden te verzenden: *{Name1: ' waarde1 ', naam2: ' Value2 ', Name3: ' Value3 '}*. Deze waarden moeten eenvoudige typen zijn. Het runbook ontvangt de para meter als een [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) met eigenschappen die overeenkomen met elke benoemde waarde.

Bekijk het volgende testrunbook dat de parameter useraccepteert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

De volgende tekst kan worden gebruikt voor de parameter user.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Dit resulteert in de volgende uitvoer:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matrices

Als de para meter een matrix is, zoals [array] of [string []], kunt u de volgende JSON-indeling gebruiken om een lijst met waarden te verzenden: *[waarde1, Value2, Value3]*. Deze waarden moeten eenvoudige typen zijn.

Bekijk het volgende testrunbook dat de parameter *user* accepteert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

De volgende tekst kan worden gebruikt voor de parameter user.

```input
["Joe","Smith",2,true]
```

Dit resulteert in de volgende uitvoer:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Referenties

Als de para meter van het gegevens type is `PSCredential` , kunt u de naam van een Azure Automation [referentie-element](./shared-resources/credentials.md)opgeven. Het runbook haalt de referentie op met de naam die u opgeeft. Het volgende test runbook accepteert een para meter met de naam `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

De volgende tekst kan worden gebruikt voor de para meter User, ervan uitgaande dat er een referentie Asset is aangeroepen `My Credential` .

```input
My Credential
```

Ervan uitgaande dat de gebruikers naam in de referentie is `jsmith` , wordt de volgende uitvoer weer gegeven.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Een runbook starten met de Azure Portal

1. Selecteer in de Azure Portal **Automation** en klik vervolgens op de naam van een Automation-account.
2. Selecteer **Runbooks** in het menu hub.
3. Selecteer op de pagina Runbooks een runbook en klik vervolgens op **starten**.
4. Als het runbook para meters heeft, wordt u gevraagd waarden op te geven voor elke para meter in een tekstvak. Zie [Runbook para meters](#work-with-runbook-parameters)voor meer informatie over para meters.
5. In het taak venster kunt u de status van de runbook-taak bekijken.

## <a name="start-a-runbook-with-powershell"></a>Een runbook starten met Power shell

U kunt de [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) gebruiken om een runbook te starten met Windows Power shell. Met de volgende voorbeeld code wordt een runbook **met de naam test-runbook** gestart.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` retourneert een taak object dat u kunt gebruiken om de status bij te houden zodra het runbook is gestart. U kunt dit taak object vervolgens gebruiken met [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) om de status van de taak te bepalen en [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) om de uitvoer op te halen. In het volgende voor beeld wordt een runbook met de naam **test-runbook** gestart, wordt gewacht tot het is voltooid en wordt vervolgens de uitvoer weer gegeven.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Als het runbook para meters vereist, moet u deze opgeven als [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables). De sleutel van de hashtabel moet overeenkomen met de naam van de para meter en de waarde is de parameter waarde. Het volgende voorbeeld laat zien hoe u een runbook met twee reeksparameters met de naam FirstName en LastName, een geheel getal met de naam RepeatCount met de naam en een Boole-parameter met de naam Show start. Zie [Runbook para meters](#work-with-runbook-parameters)voor meer informatie over para meters.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor meer informatie over het beheer van runbook.
* Zie Power shell- [documenten](/powershell/scripting/overview)voor meer informatie over Power shell.
* Zie problemen met [Runbook oplossen](troubleshoot/runbooks.md)voor informatie over het oplossen van problemen met het uitvoeren van een runbook.
