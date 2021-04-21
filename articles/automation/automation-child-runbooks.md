---
title: Modulaire runbooks maken in Azure Automation
description: In dit artikel wordt beschreven hoe u een runbook maakt dat wordt aangeroepen door een ander runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 101ff9affe43dcc97de6cf5a535c82559aafeced
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834908"
---
# <a name="create-modular-runbooks"></a>Modulaire runbooks maken

Het wordt aanbevolen om te Azure Automation herbruikbare, modulaire runbooks te schrijven met een discrete functie die wordt aangeroepen door andere runbooks. Een bovenliggend runbook roept vaak een of meer onderliggende runbooks aan om de vereiste functionaliteit uit te voeren. 

Er zijn twee manieren om een onderliggend runbook aan te roepen en er zijn verschillende verschillen die u moet begrijpen om te kunnen bepalen wat het beste is voor uw scenario's. De volgende tabel bevat een overzicht van de verschillen tussen de twee manieren om het ene runbook vanuit een ander runbook aan te roepen.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| **Taak** |Onderliggende runbooks worden uitgevoerd in dezelfde taak als het bovenliggende runbook. |Er wordt een afzonderlijke taak gemaakt voor het onderliggende runbook. |
| **Uitvoering** |Bovenliggend runbook wacht tot het onderliggende runbook is voltooid voordat het verdergaat. |Bovenliggend runbook wordt onmiddellijk voortgezet nadat het onderliggende runbook is gestart *of* het bovenliggende runbook wacht tot de onderliggende taak is uitgevoerd. |
| **Uitvoer** |Bovenliggend runbook kan uitvoer rechtstreeks uit onderliggend runbook verkrijgen. |Bovenliggend runbook moet uitvoer  ophalen van onderliggende runbook-taak, anders kan bovenliggend runbook rechtstreeks uitvoer van onderliggend runbook ophalen. |
| **Parameters** |Waarden voor de parameters van onderliggend runbook worden afzonderlijk opgegeven en kunnen elk gegevenstype hebben. |Waarden voor de parameters van het onderliggende runbook moeten worden gecombineerd tot één hashtabel. Deze hashtabel kan alleen eenvoudige, matrix- en objectgegevenstypen bevatten die gebruikmaken van JSON-serialisatie. |
| **Automation-account** |Bovenliggend runbook kan alleen onderliggend runbook in hetzelfde Automation-account gebruiken. |Bovenliggende runbooks kunnen een onderliggend runbook gebruiken uit elk Automation-account, uit hetzelfde Azure-abonnement en zelfs vanuit een ander abonnement waarmee u verbinding hebt. |
| **Publiceren** |Onderliggend runbook moet zijn gepubliceerd voordat bovenliggend runbook wordt gepubliceerd. |Onderliggend runbook wordt op elk moment gepubliceerd voordat het bovenliggende runbook wordt gestart. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Een onderliggend runbook aanroepen met behulp van inline-uitvoering

Als u een runbook inline wilt aanroepen vanuit een ander runbook, gebruikt u de naam van het runbook en geeft u waarden op voor de parameters, net zoals u een activiteit of een cmdlet zou gebruiken. Alle runbooks in hetzelfde Automation-account zijn beschikbaar voor alle andere runbooks om op deze manier te worden gebruikt. Het bovenliggende runbook wacht tot het onderliggende runbook is voltooid voordat het naar de volgende regel gaat. Eventuele uitvoer wordt rechtstreeks naar het bovenliggende runbook teruggeboekt.

Als u een runbook inline aanroept, wordt dit uitgevoerd in dezelfde taak als het bovenliggende runbook. Er is geen indicatie in de taakgeschiedenis van het onderliggende runbook. Eventuele uitzonderingen en stroomuitvoer van het onderliggende runbook zijn gekoppeld aan het bovenliggende runbook. Dit gedrag leidt tot minder taken en maakt het gemakkelijker om taken bij te houden en problemen op te lossen.

Wanneer een runbook wordt gepubliceerd, moeten alle onderliggende runbooks die het aanroept, al zijn gepubliceerd. De reden hiervoor is dat Azure Automation een verband met onderliggende runbooks maakt wanneer een runbook wordt ge compileerd. Als de onderliggende runbooks nog niet zijn gepubliceerd, lijkt het bovenliggende runbook correct te publiceren, maar wordt er een uitzondering gegenereerd wanneer het wordt gestart. Als dit gebeurt, kunt u het bovenliggende runbook opnieuw publiceren om naar de onderliggende runbooks te verwijzen. U hoeft het bovenliggende runbook niet opnieuw te publiceren als een onderliggend runbook is gewijzigd omdat de associatie al is gemaakt.

De parameters van een onderliggend runbook met de naam inline kunnen van elk gegevenstype zijn, inclusief complexe objecten. Er is geen [JSON-serialisatie,](start-runbooks.md#work-with-runbook-parameters)zoals wanneer u het runbook start met behulp van de Azure Portal of met de cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Runbooktypen

Welke runbooktypen kunnen elkaar aanroepen?

* Een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) en [een grafisch runbook](automation-runbook-types.md#graphical-runbooks) kunnen elkaar inline aanroepen, omdat beide op PowerShell zijn gebaseerd.
* Een [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) en een grafisch PowerShell Workflow-runbook kunnen elkaar inline aanroepen, omdat beide zijn gebaseerd op PowerShell Workflow.
* De PowerShell-typen en de PowerShell Workflow-typen kunnen elkaar niet inline aanroepen en moeten `Start-AzAutomationRunbook` gebruiken.

Wanneer is de publicatieorder belangrijk?

De publicatieorder van runbooks is alleen van belang voor PowerShell Workflow- en grafische PowerShell Workflow-runbooks.

Wanneer uw runbook een onderliggend runbook van een grafische of PowerShell Workflow aanroept met behulp van inline-uitvoering, wordt de naam van het runbook gebruikt. De naam moet beginnen met `.\\` om op te geven dat het script zich in de lokale map bevindt.

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een onderliggend testrunbook gestart dat een complex object, een geheel getal en een Booleaanse waarde accepteert. De uitvoer van het onderliggende runbook is toegewezen aan een variabele. In dit geval is het onderliggende runbook een PowerShell Workflow-runbook.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

Hier is hetzelfde voorbeeld met een PowerShell-runbook als het onderliggende runbook.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Een onderliggend runbook starten met behulp van een cmdlet

> [!IMPORTANT]
> Als uw runbook een onderliggend runbook aanroept met de cmdlet met de parameter en het onderliggende runbook een objectresultaat produceert, kan de bewerking `Start-AzAutomationRunbook` `Wait` een fout tegenkomen. Zie Onderliggende [runbooks](troubleshoot/runbooks.md#child-runbook-object) met objectuitvoer om de fout te voorkomen voor meer informatie over het implementeren van de logica om de resultaten te peilen met behulp van de cmdlet [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

U kunt gebruiken om `Start-AzAutomationRunbook` een runbook te starten zoals beschreven in Een [runbook starten met Windows PowerShell.](start-runbooks.md#start-a-runbook-with-powershell) Er zijn twee gebruiksmodi voor deze cmdlet. In één modus retourneert de cmdlet de taak-id wanneer de taak voor het onderliggende runbook wordt gemaakt. In de andere modus, die door het script wordt mogelijk gemaakt door de *parameter* Wachten op te geven, wacht de cmdlet totdat de onderliggende taak is uitgevoerd en retourneert de uitvoer van het onderliggende runbook.

De taak van een onderliggend runbook dat is gestart met een cmdlet wordt afzonderlijk van de bovenliggende runbook-taak uitgevoerd. Dit gedrag resulteert in meer taken dan het inline starten van het runbook en maakt het moeilijker om de taken bij te houden. Het bovenliggende runbook kan asynchroon meer dan één onderliggend runbook starten zonder te wachten tot elk runbook is voltooid. Voor deze parallelle uitvoering die de onderliggende runbooks inline aanroept, moet het bovenliggende runbook het [parallelle trefwoord gebruiken.](automation-powershell-workflow.md#use-parallel-processing)

Onderliggende runbookuitvoer keert niet betrouwbaar terug naar het bovenliggende runbook vanwege timing. Bovendien worden variabelen zoals , en andere mogelijk niet `$VerbosePreference` `$WarningPreference` doorgegeven aan de onderliggende runbooks. Om deze problemen te voorkomen, kunt u de onderliggende runbooks starten als afzonderlijke Automation-taken met `Start-AzAutomationRunbook` behulp van met de parameter `Wait` . Met deze techniek wordt het bovenliggende runbook blokkeert totdat het onderliggende runbook is voltooid.

Als u niet wilt dat het bovenliggende runbook wordt geblokkeerd bij het wachten, kunt u het onderliggende runbook starten met behulp `Start-AzAutomationRunbook` van zonder de parameter `Wait` . In dit geval moet uw runbook [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) gebruiken om te wachten tot de taak is voltooid. Ook moeten [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) en [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) worden gebruikt om de resultaten op te halen.

Parameters voor een onderliggend runbook dat is gestart met een cmdlet worden opgegeven als een hashtabel, zoals beschreven in [Runbookparameters.](start-runbooks.md#work-with-runbook-parameters) Alleen eenvoudige gegevenstypen kunnen worden gebruikt. Als het runbook een parameter met een complex gegevenstype heeft, moet dit inline worden aangeroepen.

De abonnementscontext kan verloren gaan bij het starten van onderliggende runbooks als afzonderlijke taken. Om ervoor te zorgen dat het onderliggende runbook Az-module-cmdlets uitvoert voor een specifiek Azure-abonnement, moet het onderliggende runbook onafhankelijk van het bovenliggende runbook worden geverifieerd bij dit abonnement.

Als taken binnen hetzelfde Automation-account met meer dan één abonnement werken, kan het selecteren van een abonnement in de ene taak de geselecteerde abonnementscontext voor andere taken wijzigen. Gebruik aan het begin van elk runbook om deze `Disable-AzContextAutosave -Scope Process` situatie te voorkomen. Met deze actie wordt alleen de context op de uitvoering van het runbook op slaat.

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een onderliggend runbook gestart met parameters en wordt gewacht tot het is voltooid met behulp van de `Start-AzAutomationRunbook` cmdlet met de `Wait` parameter . Zodra dit is voltooid, verzamelt het voorbeeld cmdlet-uitvoer van het onderliggende runbook. Als u `Start-AzAutomationRunbook` wilt gebruiken, moet het script worden geverifieerd bij uw Azure-abonnement.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    -Parameters $params -Wait
```

## <a name="next-steps"></a>Volgende stappen

* Zie Een runbook starten in Azure Automation om uw [runbook uit Azure Automation.](start-runbooks.md)
* Zie Runbookuitvoer en -berichten in Azure Automation voor het controleren [van runbookbewerkingen.](automation-runbook-output-and-messages.md)
