---
title: Start a runbook in Azure Automation (Een runbook starten in Azure Automation)
description: In dit artikel wordt beschreven hoe u een runbook in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 01b6e060fcab9c7dab4934aad3d1ab6047ec5236
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829850"
---
# <a name="start-a-runbook-in-azure-automation"></a>Start a runbook in Azure Automation (Een runbook starten in Azure Automation)

De volgende tabel helpt u bij het bepalen van de methode voor het starten van een runbook in Azure Automation die het meest geschikt is voor uw specifieke scenario. Dit artikel bevat informatie over het starten van een runbook met de Azure Portal en met Windows PowerShell. Meer informatie over de andere methoden vindt u in andere documentatie die u kunt openen via de onderstaande koppelingen.

| **Methode** | **Kenmerken** |
| --- | --- |
| [Azure-portal](#start-a-runbook-with-the-azure-portal) |<li>Eenvoudigste methode met interactieve gebruikersinterface.<br> <li>Formulier om eenvoudige parameterwaarden op te geven.<br> <li>Houd de taaktoestand eenvoudig bij.<br> <li>Toegang geverifieerd met azure-aanmelding. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Roep aan vanaf de opdrachtregel met Windows PowerShell cmdlets.<br> <li>Kan worden opgenomen in de geautomatiseerde functie met meerdere stappen.<br> <li>De aanvraag wordt geverifieerd met een certificaat of een OAuth-gebruikersprincipaal/service-principal.<br> <li>Geef eenvoudige en complexe parameterwaarden op.<br> <li>Taaktoestand bijhouden.<br> <li>Client vereist ter ondersteuning van PowerShell-cmdlets. |
| [Azure Automation API](/rest/api/automation/) |<li>Meest flexibele methode, maar ook meest complex.<br> <li>Roep aan vanuit aangepaste code die HTTP-aanvragen kan doen.<br> <li>Aanvraag geverifieerd met certificaat of Oauth-gebruikersprincipaal/service-principal.<br> <li>Geef eenvoudige en complexe parameterwaarden op. *Als u een Python-runbook aanroept met behulp van de API, moet de JSON-nettolading worden geseraliseerd.*<br> <li>Taaktoestand bijhouden. |
| [Webhooks](automation-webhooks.md) |<li>Runbook starten vanuit één HTTP-aanvraag.<br> <li>Geverifieerd met beveiliging token in URL.<br> <li>Client kan geen parameterwaarden overschrijven die zijn opgegeven bij het maken van een webhook. Runbook kan één parameter definiëren die wordt gevuld met de details van de HTTP-aanvraag.<br> <li>Er is geen mogelijkheid om de taaktoestand bij te houden via een webhook-URL. |
| [Reageren op Azure-waarschuwing](../azure-monitor/alerts/alerts-overview.md) |<li>Start een runbook als reactie op een Azure-waarschuwing.<br> <li>Configureer webhook voor runbook en koppeling naar waarschuwing.<br> <li>Geverifieerd met beveiliging token in URL. |
| [Schema](./shared-resources/schedules.md) |<li>Runbook automatisch starten volgens een schema per uur, dagelijks, wekelijks of maandelijks.<br> <li>Schema bewerken via Azure Portal, PowerShell-cmdlets of Azure API.<br> <li>Geef parameterwaarden op die moeten worden gebruikt met de planning. |
| [Uit een ander runbook](automation-child-runbooks.md) |<li>Gebruik een runbook als een activiteit in een ander runbook.<br> <li>Handig voor functionaliteit die wordt gebruikt door meerdere runbooks.<br> <li>Geef parameterwaarden op voor het onderliggende runbook en gebruik uitvoer in bovenliggend runbook. |

In de volgende afbeelding ziet u een gedetailleerd stapsgewijs proces in de levenscyclus van een runbook. Het bevat verschillende manieren waarop een runbook wordt gestart in Azure Automation, welke onderdelen vereist zijn voor Hybrid Runbook Worker om Azure Automation runbooks en interacties tussen verschillende onderdelen uit te voeren. Raadpleeg Hybrid Runbook Workers voor meer informatie over het uitvoeren van [Automation-runbooks](automation-hybrid-runbook-worker.md) in uw datacenter

![Runbookarchitectuur](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Werken met runbookparameters

Wanneer u een runbook start vanuit Azure Portal of Windows PowerShell, wordt de instructie verzonden via Azure Automation webservice. Deze service biedt geen ondersteuning voor parameters met complexe gegevenstypen. Als u een waarde voor een complexe parameter moet opgeven, moet u deze inline aanroepen vanuit een ander runbook, zoals beschreven in Onderliggende [runbooks in Azure Automation](automation-child-runbooks.md).

De Azure Automation-webservice biedt speciale functionaliteit voor parameters die gebruikmaken van bepaalde gegevenstypen, zoals beschreven in de volgende secties:

### <a name="named-values"></a>Benoemde waarden

Als de parameter het gegevenstype [object] is, kunt u de volgende JSON-indeling gebruiken om een lijst met benoemde waarden te verzenden: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Deze waarden moeten eenvoudige typen zijn. Het runbook ontvangt de parameter als een [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) met eigenschappen die overeenkomen met elke benoemde waarde.

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

Als de parameter een matrix is, zoals [array] of [string[]], kunt u de volgende JSON-indeling gebruiken om een lijst met waarden te verzenden: *[Value1, Value2, Value3]*. Deze waarden moeten eenvoudige typen zijn.

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

Als de parameter een gegevenstype is, kunt u de naam van een Azure Automation `PSCredential` [referentie-asset opgeven.](./shared-resources/credentials.md) Het runbook haalt de referentie op met de naam die u opgeeft. Het volgende testrunbook accepteert een parameter met de naam `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

De volgende tekst kan worden gebruikt voor de gebruikersparameter, ervan uitgaande dat er een referentie-asset met de naam `My Credential` is.

```input
My Credential
```

Ervan uitgaande dat de gebruikersnaam in de referentie `jsmith` is, wordt de volgende uitvoer weergegeven.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Een runbook starten met de Azure Portal

1. Selecteer in Azure Portal automation **en** klik vervolgens op de naam van een Automation-account.
2. Selecteer runbooks in het menu **Hub.**
3. Selecteer op de pagina Runbooks een runbook en klik vervolgens op **Starten.**
4. Als het runbook parameters bevat, wordt u gevraagd waarden op te geven met een tekstvak voor elke parameter. Zie Runbookparameters voor [meer informatie over parameters.](#work-with-runbook-parameters)
5. In het deelvenster Taak kunt u de status van de runbook-taak bekijken.

## <a name="start-a-runbook-with-powershell"></a>Een runbook starten met PowerShell

U kunt [Start-AzAutomationRunbook gebruiken](/powershell/module/az.automation/start-azautomationrunbook) om een runbook te starten met Windows PowerShell. Met de volgende voorbeeldcode wordt een runbook met de **naam Test-Runbook gestart.**

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` retourneert een taakobject dat u kunt gebruiken om de status bij te houden zodra het runbook is gestart. U kunt dit taakobject vervolgens gebruiken met [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) om de status van de taak te bepalen en [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) om de uitvoer op te halen. In het volgende voorbeeld wordt een runbook met de **naam Test-Runbook** gestart, wordt gewacht tot het is voltooid en wordt vervolgens de uitvoer weergegeven.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook -AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup -Stream Output
```

Als het runbook parameters vereist, moet u deze opgeven als [een hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables). De sleutel van de hashtabel moet overeenkomen met de parameternaam en de waarde is de parameterwaarde. Het volgende voorbeeld laat zien hoe u een runbook met twee reeksparameters met de naam FirstName en LastName, een geheel getal met de naam RepeatCount met de naam en een Boole-parameter met de naam Show start. Zie Runbookparameters voor [meer informatie over parameters.](#work-with-runbook-parameters)

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" -Parameters $params
```

## <a name="next-steps"></a>Volgende stappen

* Zie Runbooks beheren in Azure Automation voor meer [informatie over runbookbeheer.](manage-runbooks.md)
* Zie [PowerShell Docs voor meer informatie over PowerShell.](/powershell/scripting/overview)
* Zie Problemen met runbook oplossen voor het oplossen van problemen met [runbookuitvoering.](troubleshoot/runbooks.md)
