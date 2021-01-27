---
title: Power shell-werk stroom voor Azure Automation leren
description: In dit artikel leert u de verschillen tussen Power shell-werk stroom en Power shell en concepten die van toepassing zijn op Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 483d4c16f1b77bf7328857eb25e1571a741d144e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896915"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Power shell-werk stroom voor Azure Automation leren

Runbooks in Azure Automation worden geïmplementeerd als Windows Power shell-werk stromen, Windows Power shell-scripts die gebruikmaken van Windows Workflow Foundation. Een werkstroom is een opeenvolging van geprogrammeerde, met elkaar verbonden stappen waarmee langlopende taken worden uitgevoerd of die de coördinatie vereisen van meerdere stappen op meerdere apparaten of beheerde knooppunten. 

Terwijl een werk stroom is geschreven met de Windows Power shell-syntaxis en wordt gestart door Windows Power shell, wordt deze verwerkt door Windows Workflow Foundation. De voor delen van een werk stroom over een normaal script zijn de gelijktijdige prestaties van een actie op meerdere apparaten en automatisch herstel van fouten. 

> [!NOTE]
> Een Power shell-werk stroom script is vergelijkbaar met een Windows Power shell-script, maar heeft een aantal belang rijke verschillen die verwarrend kunnen zijn voor een nieuwe gebruiker. Daarom raden we u aan om uw runbooks te schrijven met behulp van de Power shell-werk stroom alleen als u [controle punten](#use-checkpoints-in-a-workflow)moet gebruiken. 

Zie aan de slag [met Windows Power shell-werk stroom](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))voor meer informatie over de onderwerpen in dit artikel.

## <a name="use-workflow-keyword"></a>Werk stroom trefwoord gebruiken

De eerste stap voor het converteren van een Power shell-script naar een Power shell-werk stroom is gekoppeld aan het `Workflow` sleutel woord. Een werk stroom begint met het `Workflow` sleutel woord gevolgd door de hoofd tekst van het script tussen accolades. De naam van de werk stroom volgt het `Workflow` tref woord, zoals wordt weer gegeven in de volgende syntaxis:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

De naam van de werk stroom moet overeenkomen met de naam van het Automation-runbook. Als het runbook wordt geïmporteerd, moet de bestands naam overeenkomen met de naam van de werk stroom en moet eindigen op **. ps1**.

Als u para meters wilt toevoegen aan de werk stroom, gebruikt `Param` u het sleutel woord net zoals u zou doen in een script.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Verschillen tussen de Power shell-werk stroom code en Power shell-script code leren

Power shell-werk stroom code lijkt bijna identiek te zijn met Power shell-script code, met uitzonde ring van enkele belang rijke wijzigingen. In de volgende secties wordt beschreven welke wijzigingen u moet aanbrengen in een Power shell-script om deze in een werk stroom uit te voeren.

### <a name="activities"></a>Activiteiten

Een activiteit is een specifieke taak in een werk stroom die wordt uitgevoerd in een reeks. Windows PowerShell-werkstroom converteert automatisch veel van de Windows PowerShell cmdlets in activiteiten wanneer het een werkstroom uitvoert. Wanneer u een van deze cmdlets in uw runbook opgeeft, wordt de bijbehorende activiteit uitgevoerd door Windows Workflow Foundation. 

Als een cmdlet geen bijbehorende activiteit heeft, voert Windows Power shell-werk stroom automatisch de cmdlet uit in een [InlineScript](#use-inlinescript) -activiteit. Sommige cmdlets worden uitgesloten en kunnen niet worden gebruikt in een werk stroom, tenzij u ze expliciet opneemt in een InlineScript-blok. Zie voor meer informatie [activiteiten gebruiken in script werk stromen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

Werkstroomactiviteiten delen een aantal gemeenschappelijke parameters om hun werking te configureren. Zie [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Positionele para meters

U kunt geen positionele para meters gebruiken met activiteiten en cmdlets in een werk stroom. Daarom moet u parameter namen gebruiken. Bekijk de volgende code waarmee alle actieve services worden opgehaald:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Als u probeert deze code in een werk stroom uit te voeren, ontvangt u een bericht zoals u kunt `Parameter set cannot be resolved using the specified named parameters.` corrigeren voor dit probleem en geeft u de parameter naam op, zoals in het volgende voor beeld:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Gedeserialiseerd objecten

Objecten in werk stromen worden gedeserialiseerd, wat inhoudt dat hun eigenschappen nog steeds beschikbaar zijn, maar niet hun methoden.  Denk bijvoorbeeld aan de volgende Power shell-code, die een service stopt met de `Stop` methode van het `Service` object.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Als u dit probeert uit te voeren in een werk stroom, ontvangt u een fout melding `Method invocation is not supported in a Windows PowerShell Workflow.`

Een optie is om deze twee regels code in een [InlineScript](#use-inlinescript) -blok te laten teruglopen. In dit geval `Service` vertegenwoordigt een service object binnen het blok.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Een andere optie is het gebruik van een andere cmdlet die dezelfde functionaliteit als de methode heeft, als er een beschikbaar is. In ons voor beeld `Stop-Service` biedt de cmdlet dezelfde functionaliteit als de `Stop` -methode en kunt u de volgende code voor een werk stroom gebruiken.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>InlineScript gebruiken

De `InlineScript` activiteit is handig als u een of meer opdrachten wilt uitvoeren als traditioneel Power shell-script in plaats van Power shell-werk stroom.  Waar opdrachten in een werkstroom naar Windows Workflow Foundation worden verzonden voor verwerking, worden opdrachten in een InlineScript blok verwerkt door Windows PowerShell.

InlineScript maakt gebruik van de volgende syntaxis die hieronder wordt weer gegeven.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

U kunt de uitvoer van een InlineScript retour neren door de uitvoer toe te wijzen aan een variabele. In het volgende voor beeld wordt een service gestopt en wordt de naam van de service vervolgens uitgevoerd.

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

U kunt waarden door geven in een InlineScript-blok, maar u moet **$using** bereik wijzigings functie gebruiken.  Het volgende voor beeld is identiek aan het vorige voor beeld, behalve dat de service naam wordt verschaft door een variabele.

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

Hoewel InlineScript-activiteiten in bepaalde werk stromen kritiek kunnen zijn, bieden ze geen ondersteuning voor werk stroom constructies. U moet deze alleen gebruiken wanneer dit nodig is om de volgende redenen:

* U kunt geen [controle punten](#use-checkpoints-in-a-workflow) gebruiken in een InlineScript-blok. Als er een fout optreedt in het blok, moet het worden hervat vanaf het begin van het blok.
* U kunt geen [parallelle uitvoering](#use-parallel-processing) binnen een InlineScript-blok gebruiken.
* InlineScript is van invloed op de schaal baarheid van de werk stroom, omdat deze de Windows Power shell-sessie voor de volledige lengte van het blok InlineScript bevat.

Zie [Windows Power shell-opdrachten uitvoeren in een werk stroom](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) en [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript)voor meer informatie over het gebruik van InlineScript.

## <a name="use-parallel-processing"></a>Parallelle verwerking gebruiken

Een voordeel van Windows PowerShell-werkstromen is de mogelijkheid om een ​​reeks opdrachten parallel uit te voeren in plaats van opeenvolgend zoals bij een typische script.

U kunt het `Parallel` sleutel woord gebruiken om een script blok te maken met meerdere opdrachten die gelijktijdig worden uitgevoerd. Dit maakt gebruik van de volgende syntaxis die hieronder wordt weer gegeven. In dit geval worden Activiteit1 en Activiteit2 op hetzelfde moment gestart. Activiteit3 start pas als zowel Activiteit1 als Activiteit2 zijn voltooid.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Denk bijvoorbeeld aan de volgende Power shell-opdrachten waarmee meerdere bestanden naar een netwerk bestemming worden gekopieerd. Deze opdrachten worden sequentieel uitgevoerd, zodat één bestand moet worden gekopieerd voordat de volgende wordt gestart.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

In de volgende werk stroom worden dezelfde opdrachten parallel uitgevoerd, zodat ze allemaal op hetzelfde moment worden gekopieerd.  Alleen als deze zijn gekopieerd, wordt het voltooiings bericht weer gegeven.

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

U kunt de `ForEach -Parallel` Construct gebruiken om opdrachten voor elk item in een verzameling tegelijk te verwerken. De items in de verzameling worden parallel verwerkt, terwijl de opdrachten in het scriptblok sequentieel worden uitgevoerd. Dit proces maakt gebruik van de volgende syntaxis die hieronder wordt weer gegeven. In dit geval wordt Activiteit1 op hetzelfde moment gestart voor alle items in de verzameling. Activiteit2 wordt voor elk item gestart nadat Activiteit1 is voltooid. Activiteit3 start pas als zowel Activiteit1 als Activiteit2 zijn voltooid voor alle items. We gebruiken de `ThrottleLimit` para meter om de parallellisme te beperken. Te hoog van een `ThrottleLimit` kan problemen veroorzaken. De ideale waarde voor de `ThrottleLimit` para meter is afhankelijk van een groot aantal factoren in uw omgeving. Begin met een lage waarde en probeer verschillende toenemende waarden totdat u er een hebt gevonden die geschikt is voor uw specifieke omstandigheid.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Het volgende voor beeld is vergelijkbaar met het vorige voor beeld van het kopiëren van bestanden parallel.  In dit geval wordt een bericht weer gegeven voor elk bestand nadat het is gekopieerd.  Alleen als deze zijn gekopieerd, wordt het laatste voltooiings bericht weer gegeven.

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
> Het is niet raadzaam om onderliggende runbooks parallel uit te voeren, omdat deze is weer gegeven om onbetrouwbare resultaten te geven. De uitvoer van het onderliggende runbook wordt soms niet weer gegeven en instellingen in één onderliggend runbook kunnen van invloed zijn op de andere parallelle onderliggende runbooks. Variabelen zoals `VerbosePreference` , `WarningPreference` , en andere kunnen niet worden door gegeven aan de onderliggende runbooks. En als het onderliggende runbook deze waarden wijzigt, zijn ze mogelijk niet juist teruggezet na het aanroepen.

## <a name="use-checkpoints-in-a-workflow"></a>Controle punten in een werk stroom gebruiken

Een controle punt is een moment opname van de huidige status van de werk stroom die de huidige waarden voor variabelen bevat en een uitvoer die op dat punt is gegenereerd. Als een werk stroom eindigt met een fout of wordt onderbroken, wordt deze gestart vanaf het laatste controle punt wanneer deze wordt uitgevoerd, in plaats van vanaf het begin. 

U kunt een controle punt instellen in een werk stroom met de `Checkpoint-Workflow` activiteit. Azure Automation heeft een functie met de naam [fair share](automation-runbook-execution.md#fair-share), waarvoor elk runbook dat gedurende drie uur wordt uitgevoerd, wordt verwijderd zodat andere runbooks kunnen worden uitgevoerd. Uiteindelijk wordt het verwijderde runbook opnieuw geladen. Als dit het geval is, wordt de uitvoering hervat vanaf het laatste controle punt dat in het runbook is gemaakt.

Om ervoor te zorgen dat het runbook uiteindelijk wordt voltooid, moet u controle punten toevoegen met intervallen die minder dan drie uur worden uitgevoerd. Als tijdens elke uitvoering een nieuw controle punt wordt toegevoegd en als het runbook na drie uur wordt verwijderd vanwege een fout, wordt het runbook voor onbepaalde tijd hervat.

In het volgende voor beeld treedt er een uitzonde ring op na Activiteit2, waardoor de werk stroom wordt beëindigd. Wanneer de werk stroom opnieuw wordt uitgevoerd, wordt deze gestart door Activiteit2 uit te voeren, omdat deze activiteit net nadat het laatste controle punt is ingesteld.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Stel controle punten in een werk stroom in na activiteiten die gevoelig zijn voor uitzonde ring en niet moeten worden herhaald als de werk stroom wordt hervat. Uw werk stroom kan bijvoorbeeld een virtuele machine maken. U kunt vóór en na de opdrachten een controle punt instellen voor het maken van de virtuele machine. Als het maken mislukt, worden de opdrachten herhaald als de werk stroom opnieuw wordt gestart. Als de werk stroom mislukt nadat het maken is voltooid, wordt de virtuele machine niet opnieuw gemaakt wanneer de werk stroom wordt hervat.

In het volgende voor beeld worden meerdere bestanden gekopieerd naar een netwerk locatie en wordt na elk bestand een controle punt ingesteld.  Als de netwerk locatie is verbroken, eindigt de werk stroom met de fout.  Wanneer het opnieuw wordt gestart, wordt het hervat op het laatste controle punt. Alleen de bestanden die al zijn gekopieerd, worden overgeslagen.

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

Omdat de referenties van de gebruikers naam niet worden bewaard nadat u de activiteit [suspend-workflow](/powershell/module/psworkflow/about/about_suspend-workflow) of na het laatste controle punt hebt aangeroepen, moet u de referenties instellen op null en ze vervolgens opnieuw uit het activa archief ophalen nadat `Suspend-Workflow` of het controle punt is aangeroepen.  Anders wordt mogelijk het volgende fout bericht weer gegeven: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

In de volgende code ziet u hoe u deze situatie kunt afhandelen in uw Power shell workflow-runbooks.

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
> Voor niet-grafische power shell-runbooks `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). U kunt deze cmdlets gebruiken of u kunt [uw modules bijwerken](automation-update-azure-modules.md) naar de nieuwste versie in uw Automation-account. Zelfs wanneer u juist een nieuw Automation-account heeft aangemaakt, moet u mogelijk uw modules bijwerken. Het gebruik van deze cmdlets is niet vereist als u verificatie uitvoert met een run as-account dat is geconfigureerd met een service-principal.

Zie [controle punten toevoegen aan een script werk stroom](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11))voor meer informatie over controle punten.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een Power shell workflow-Runbook maken](learn/automation-tutorial-runbook-textual.md)voor meer informatie over Power shell workflow-runbooks.
