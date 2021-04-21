---
title: Meer informatie over PowerShell Workflow voor Azure Automation
description: In dit artikel leert u de verschillen tussen PowerShell Workflow en PowerShell en concepten die van toepassing zijn op Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b3beb8b3eda4dfabf9240aa328a24d5f855689b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833504"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Meer informatie over PowerShell Workflow voor Azure Automation

Runbooks in Azure Automation worden geïmplementeerd als Windows PowerShell werkstromen, Windows PowerShell scripts die gebruikmaken van Windows Workflow Foundation. Een werkstroom is een opeenvolging van geprogrammeerde, met elkaar verbonden stappen waarmee langlopende taken worden uitgevoerd of die de coördinatie vereisen van meerdere stappen op meerdere apparaten of beheerde knooppunten. 

Hoewel een werkstroom is geschreven Windows PowerShell syntaxis en wordt gestart door Windows PowerShell, wordt deze door de Windows Workflow Foundation. De voordelen van een werkstroom ten opzichte van een normaal script zijn onder andere gelijktijdige prestaties van een actie op meerdere apparaten en automatisch herstel van fouten. 

> [!NOTE]
> Een PowerShell Workflow-script is vergelijkbaar met een Windows PowerShell script, maar er zijn enkele belangrijke verschillen die verwarrend kunnen zijn voor een nieuwe gebruiker. Daarom raden we u aan om uw runbooks alleen met Behulp van PowerShell Workflow te schrijven als u controlepunten [moet gebruiken.](#use-checkpoints-in-a-workflow) 

Zie voor meer informatie over de onderwerpen in dit artikel [Aan de slag met Windows PowerShell Workflow.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))

## <a name="use-workflow-keyword"></a>Werkstroomtrefwoord gebruiken

De eerste stap voor het converteren van een PowerShell-script naar een PowerShell-werkstroom is omsluiten met het `Workflow` trefwoord . Een werkstroom begint met het `Workflow` trefwoord gevolgd door de body van het script tussen accolades. De naam van de werkstroom volgt het `Workflow` trefwoord zoals weergegeven in de volgende syntaxis:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

De naam van de werkstroom moet overeenkomen met de naam van het Automation-runbook. Als het runbook wordt geïmporteerd, moet de bestandsnaam overeenkomen met de naam van de werkstroom en moet deze eindigen op **.ps1.**

Als u parameters wilt toevoegen aan de werkstroom, gebruikt u het `Param` trefwoord op dezelfde als in een script.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Meer informatie over verschillen tussen PowerShell Workflow-code en PowerShell-scriptcode

PowerShell Workflow-code ziet er bijna identiek uit aan PowerShell-scriptcode, met uitzondering van enkele belangrijke wijzigingen. In de volgende secties worden wijzigingen beschreven die u moet aanbrengen in een PowerShell-script om het in een werkstroom uit te voeren.

### <a name="activities"></a>Activiteiten

Een activiteit is een specifieke taak in een werkstroom die in een reeks wordt uitgevoerd. Windows PowerShell-werkstroom converteert automatisch veel van de Windows PowerShell cmdlets in activiteiten wanneer het een werkstroom uitvoert. Wanneer u een van deze cmdlets in uw runbook opgeeft, wordt de bijbehorende activiteit uitgevoerd door Windows Workflow Foundation. 

Als een cmdlet geen bijbehorende activiteit heeft, wordt Windows PowerShell de cmdlet automatisch uitgevoerd in [een InlineScript-activiteit.](#use-inlinescript) Sommige cmdlets worden uitgesloten en kunnen niet worden gebruikt in een werkstroom, tenzij u ze expliciet opsluit in een InlineScript-blok. Zie Using [Activities in Script Workflows (Activiteiten gebruiken in scriptwerkstromen) voor meer informatie.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11))

Werkstroomactiviteiten delen een aantal gemeenschappelijke parameters om hun werking te configureren. Zie [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Positionele parameters

U kunt geen positionele parameters gebruiken met activiteiten en cmdlets in een werkstroom. Daarom moet u parameternamen gebruiken. Overweeg de volgende code om alle services uit te voeren:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Als u deze code in een werkstroom probeert uit te voeren, ontvangt u een bericht als Om dit probleem op te lossen, geeft u de `Parameter set cannot be resolved using the specified named parameters.` parameternaam op, zoals in het volgende voorbeeld:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Gedeserialiseerde objecten

Objecten in werkstromen worden gedeserialiseerd, wat betekent dat hun eigenschappen nog steeds beschikbaar zijn, maar niet hun methoden.  Denk bijvoorbeeld aan de volgende PowerShell-code, waarmee een service wordt gestopt met behulp van `Stop` de -methode van het `Service` -object.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Als u dit in een werkstroom probeert uit te voeren, ontvangt u een foutmelding `Method invocation is not supported in a Windows PowerShell Workflow.`

Een optie is om deze twee regels code in te pakken in een [InlineScript-blok.](#use-inlinescript) In dit geval `Service` vertegenwoordigt een serviceobject binnen het blok.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Een andere optie is om een andere cmdlet te gebruiken die dezelfde functionaliteit heeft als de methode , als er een beschikbaar is. In ons voorbeeld biedt de cmdlet dezelfde functionaliteit als de methode en kunt u de `Stop-Service` volgende code gebruiken voor een `Stop` werkstroom.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>InlineScript gebruiken

De activiteit is handig wanneer u een of meer opdrachten moet uitvoeren als een traditioneel `InlineScript` PowerShell-script in plaats van een PowerShell-werkstroom.  Waar opdrachten in een werkstroom naar Windows Workflow Foundation worden verzonden voor verwerking, worden opdrachten in een InlineScript blok verwerkt door Windows PowerShell.

InlineScript maakt gebruik van de volgende syntaxis die hieronder wordt weergegeven.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

U kunt uitvoer van een InlineScript retourneren door de uitvoer toe te wijzen aan een variabele. In het volgende voorbeeld wordt een service gestopt en wordt vervolgens de servicenaam uitgevoerd.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

U kunt waarden doorgeven aan een InlineScript-blok, maar u moet de $Using gebruiken.   Het volgende voorbeeld is identiek aan het vorige voorbeeld, behalve dat de servicenaam wordt opgegeven door een variabele.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Hoewel InlineScript-activiteiten essentieel kunnen zijn in bepaalde werkstromen, bieden ze geen ondersteuning voor werkstroom constructs. Gebruik deze alleen wanneer dit nodig is om de volgende redenen:

* U kunt geen controlepunten [in een](#use-checkpoints-in-a-workflow) InlineScript-blok gebruiken. Als er een fout optreedt in het blok, moet deze worden hervat vanaf het begin van het blok.
* U kunt geen parallelle [uitvoering in een](#use-parallel-processing) InlineScript-blok gebruiken.
* InlineScript is van invloed op de schaalbaarheid van de werkstroom omdat deze de Windows PowerShell voor de volledige lengte van het InlineScript-blok bevat.

Zie Running Windows PowerShell Commands in [a Workflow](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) (Opdrachten uitvoeren in een werkstroom) en about_InlineScript voor meer informatie [over het gebruik van InlineScript.](/powershell/module/psworkflow/about/about_inlinescript)

## <a name="use-parallel-processing"></a>Parallelle verwerking gebruiken

Een voordeel van Windows PowerShell-werkstromen is de mogelijkheid om een ​​reeks opdrachten parallel uit te voeren in plaats van opeenvolgend zoals bij een typische script.

U kunt het `Parallel` trefwoord gebruiken om een scriptblok te maken met meerdere opdrachten die gelijktijdig worden uitgevoerd. Hiervoor wordt de volgende syntaxis gebruikt die hieronder wordt weergegeven. In dit geval worden Activity1 en Activity2 op hetzelfde moment gestart. Activiteit3 wordt pas gestart nadat zowel Activity1 als Activity2 zijn voltooid.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Denk bijvoorbeeld aan de volgende PowerShell-opdrachten die meerdere bestanden naar een netwerkbestemming kopiëren. Deze opdrachten worden opeenvolgend uitgevoerd, zodat het kopiëren van één bestand moet worden uitgevoerd voordat het volgende wordt gestart.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

De volgende werkstroom voert dezelfde opdrachten parallel uit, zodat ze allemaal op hetzelfde moment beginnen te kopiëren.  Pas nadat ze allemaal zijn gekopieerd, wordt het voltooiingsbericht weergegeven.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

U kunt de `ForEach -Parallel` -constructie gebruiken om opdrachten voor elk item in een verzameling gelijktijdig te verwerken. De items in de verzameling worden parallel verwerkt, terwijl de opdrachten in het scriptblok sequentieel worden uitgevoerd. Dit proces maakt gebruik van de volgende syntaxis die hieronder wordt weergegeven. In dit geval wordt Activity1 op hetzelfde moment gestart voor alle items in de verzameling. Voor elk item wordt Activiteit2 gestart nadat Activiteit1 is voltooid. Activiteit3 wordt pas gestart nadat zowel Activity1 als Activity2 zijn voltooid voor alle items. We gebruiken de `ThrottleLimit` parameter om de parallellisme te beperken. Te hoog van een `ThrottleLimit` kan problemen veroorzaken. De ideale waarde voor de `ThrottleLimit` parameter is afhankelijk van veel factoren in uw omgeving. Begin met een lage waarde en probeer verschillende oplopende waarden totdat u er een hebt gevonden die voor uw specifieke situatie werkt.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Het volgende voorbeeld is vergelijkbaar met het vorige voorbeeld van het parallel kopiëren van bestanden.  In dit geval wordt een bericht weergegeven voor elk bestand nadat het is gekopieerd.  Pas nadat ze allemaal zijn gekopieerd, wordt het laatste voltooiingsbericht weergegeven.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Het is niet raadzaam onderliggende runbooks parallel uit te voeren, omdat dit onbetrouwbare resultaten oplevert. De uitvoer van het onderliggende runbook wordt soms niet weergeven en de instellingen in het ene onderliggende runbook kunnen van invloed zijn op de andere parallelle onderliggende runbooks. Variabelen zoals , en andere worden mogelijk niet `VerbosePreference` `WarningPreference` doorgegeven aan de onderliggende runbooks. En als het onderliggende runbook deze waarden wijzigt, worden ze mogelijk niet goed hersteld na aanroepen.

## <a name="use-checkpoints-in-a-workflow"></a>Controlepunten in een werkstroom gebruiken

Een controlepunt is een momentopname van de huidige status van de werkstroom met de huidige waarden voor variabelen en eventuele uitvoer die tot dat punt wordt gegenereerd. Als een werkstroom in een fout eindigt of wordt opgeschort, begint deze vanaf het laatste controlepunt de volgende keer dat deze wordt uitgevoerd, in plaats van bij het begin te beginnen. 

U kunt een controlepunt in een werkstroom instellen met de `Checkpoint-Workflow` activiteit . Azure Automation heeft een functie met de naam [fair share,](automation-runbook-execution.md#fair-share)waarvoor elk runbook dat drie uur wordt uitgevoerd, wordt verwijderd zodat andere runbooks kunnen worden uitgevoerd. Uiteindelijk wordt het verwijderde runbook opnieuw geladen. Wanneer dit het is, wordt de uitvoering hervat vanaf het laatste controlepunt dat in het runbook is genomen.

Om ervoor te zorgen dat het runbook uiteindelijk wordt voltooid, moet u controlepunten toevoegen met intervallen die minder dan drie uur worden uitgevoerd. Als er tijdens elke run een nieuw controlepunt wordt toegevoegd en het runbook na drie uur als gevolg van een fout wordt onbepaald, wordt het runbook voor onbepaalde tijd hervat.

In het volgende voorbeeld treedt er een uitzondering op na Activiteit2, waardoor de werkstroom wordt beëindigen. Wanneer de werkstroom opnieuw wordt uitgevoerd, begint deze met het uitvoeren van Activiteit2, omdat deze activiteit net na het laatste controlepunt is ingesteld.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Stel controlepunten in een werkstroom in na activiteiten die mogelijk gevoelig zijn voor uitzonderingen en die niet moeten worden herhaald als de werkstroom wordt hervat. Uw werkstroom kan bijvoorbeeld een virtuele machine maken. U kunt zowel vóór als na de opdrachten een controlepunt instellen om de virtuele machine te maken. Als het maken mislukt, worden de opdrachten herhaald als de werkstroom opnieuw wordt gestart. Als de werkstroom mislukt nadat het maken is geslaagd, wordt de virtuele machine niet opnieuw gemaakt wanneer de werkstroom wordt hervat.

In het volgende voorbeeld worden meerdere bestanden gekopieerd naar een netwerklocatie en wordt na elk bestand een controlepunt in stellen.  Als de netwerklocatie verloren is gegaan, wordt de werkstroom beëindigd in een fout.  Wanneer deze opnieuw wordt gestart, wordt deze hervat op het laatste controlepunt. Alleen de bestanden die al zijn gekopieerd, worden overgeslagen.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Omdat de referenties voor de gebruikersnaam niet worden bewaard nadat u de activiteit [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) aanroept of na het laatste controlepunt, moet u de referenties instellen op null en deze vervolgens opnieuw ophalen uit de assetopslag nadat of het controlepunt `Suspend-Workflow` is aangeroepen.  Anders ontvangt u mogelijk het volgende foutbericht: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

De volgende code laat zien hoe u deze situatie in uw PowerShell Workflow-runbooks kunt afhandelen.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Voor niet-grafische PowerShell-runbooks `Add-AzAccount` zijn en `Add-AzureRMAccount` aliassen voor [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) U kunt deze cmdlets gebruiken of u kunt [uw modules bijwerken](automation-update-azure-modules.md) naar de nieuwste versie in uw Automation-account. Zelfs wanneer u juist een nieuw Automation-account heeft aangemaakt, moet u mogelijk uw modules bijwerken. Gebruik van deze cmdlets is niet vereist als u een Uitvoeren als-account gebruikt dat is geconfigureerd met een service-principal.

Zie voor meer informatie over controlepunten [toevoegen controlepunten aan een scriptwerkstroom.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11))

## <a name="next-steps"></a>Volgende stappen

* Zie Zelfstudie: Een PowerShell Workflow-runbook maken voor meer informatie over [PowerShell Workflow-runbooks.](learn/automation-tutorial-runbook-textual.md)
