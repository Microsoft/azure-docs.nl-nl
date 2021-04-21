---
title: Runbookuitvoer en berichtstromen configureren
description: In dit artikel wordt beschreven hoe u logica voor foutafhandeling implementeert en beschrijft u uitvoer- en Azure Automation runbooks.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 033900ddd0bd19332b4a9a996c68b3b187d631c4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833558"
---
# <a name="configure-runbook-output-and-message-streams"></a>Runbookuitvoer en berichtstromen configureren

De Azure Automation runbooks hebben een vorm van uitvoer. Deze uitvoer kan een foutbericht zijn voor de gebruiker of een complex object dat is bedoeld om te worden gebruikt met een ander runbook. Windows PowerShell meerdere [stromen voor](/powershell/module/microsoft.powershell.core/about/about_redirection) het verzenden van uitvoer van een script of werkstroom. Azure Automation werkt op een andere manier met elk van deze stromen. Volg de best practices voor het gebruik van de streams wanneer u een runbook maakt.

De volgende tabel beschrijft kort elke stroom met het gedrag in de Azure Portal voor gepubliceerde runbooks en tijdens het testen [van een runbook](./manage-runbooks.md). De uitvoerstroom is de hoofdstroom die wordt gebruikt voor communicatie tussen runbooks. De andere stromen worden geclassificeerd als berichtstromen, bedoeld om informatie aan de gebruiker te communiceren.

| Stream | Description | Gepubliceerd | Testen |
|:--- |:--- |:--- |:--- |
| Fout |Foutbericht bedoeld voor de gebruiker. In tegenstelling tot een uitzondering gaat het runbook standaard verder na een foutbericht. |Geschreven naar taakgeschiedenis |Weergegeven in het deelvenster Testuitvoer |
| Fouten opsporen |Berichten die zijn bedoeld voor een interactieve gebruiker. Mag niet worden gebruikt in runbooks. |Niet geschreven naar taakgeschiedenis |Niet weergegeven in het deelvenster Testuitvoer |
| Uitvoer |Objecten die zijn bedoeld om te worden verbruikt door andere runbooks. |Geschreven naar taakgeschiedenis |Weergegeven in het deelvenster Testuitvoer |
| Voortgang |Records die automatisch worden gegenereerd voor en na elke activiteit in het runbook. Het runbook mag niet proberen om eigen voortgangsrecords te maken, omdat deze zijn bedoeld voor een interactieve gebruiker. |Alleen naar de taakgeschiedenis geschreven als voortgangslogboekregistratie is ingeschakeld voor het runbook |Niet weergegeven in het deelvenster Testuitvoer |
| Uitgebreid |Berichten met algemene informatie of informatie over debuggen. |Alleen naar de taakgeschiedenis geschreven als uitgebreide logboekregistratie is ingeschakeld voor het runbook |Wordt alleen weergegeven in het deelvenster Testuitvoer `VerbosePreference` als variabele is ingesteld op Doorgaan in runbook |
| Waarschuwing |Waarschuwingsbericht bedoeld voor de gebruiker. |Geschreven naar taakgeschiedenis |Weergegeven in het deelvenster Testuitvoer |

## <a name="use-the-output-stream"></a>De uitvoerstroom gebruiken

De uitvoerstroom wordt gebruikt voor de uitvoer van objecten die zijn gemaakt door een script of werkstroom wanneer deze correct wordt uitgevoerd. Azure Automation gebruikt deze stroom voornamelijk voor objecten die moeten worden gebruikt door bovenliggende runbooks die het huidige [runbook aanroepen.](automation-child-runbooks.md) Wanneer een [bovenliggend runbook inline wordt aanroept,](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution)retourneert het onderliggende runbook gegevens uit de uitvoerstroom naar het bovenliggende runbook.

Uw runbook gebruikt de uitvoerstroom om algemene informatie alleen met de client te communiceren als deze nooit wordt aangeroepen door een ander runbook. Als een best practice runbooks doorgaans echter de uitgebreide [stroom gebruiken om](#write-output-to-verbose-stream) algemene informatie aan de gebruiker te communiceren.

Uw runbook gegevens laten schrijven naar de uitvoerstroom met [behulp van Write-Output.](/powershell/module/microsoft.powershell.utility/write-output) U kunt het object ook op een eigen regel in het script zetten.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Uitvoer van een functie verwerken

Wanneer een runbookfunctie naar de uitvoerstroom schrijft, wordt de uitvoer doorgegeven aan het runbook. Als het runbook die uitvoer toewijst aan een variabele, wordt de uitvoer niet naar de uitvoerstroom geschreven. Schrijven naar andere stromen vanuit de functie schrijft naar de bijbehorende stroom voor het runbook. Kijk eens naar het volgende PowerShell Workflow-runbook.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

De uitvoerstroom voor de runbook-taak is:

```output
Output inside of function
Output outside of function
```

De uitgebreide stroom voor de runbook-taak is:

```output
Verbose outside of function
Verbose inside of function
```

Zodra u het runbook hebt gepubliceerd en voordat u het start, moet u ook uitgebreide logboekregistratie inschakelen in de runbookinstellingen om de uitgebreide stroomuitvoer op te halen.

### <a name="declare-output-data-type"></a>Uitvoergegevenstype declareer

Hier volgen enkele voorbeelden van uitvoergegevenstypen:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Uitvoergegevenstype declareer in een werkstroom

Een werkstroom geeft het gegevenstype van de uitvoer aan met behulp van [het kenmerk OutputType.](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) Dit kenmerk heeft geen effect tijdens runtime, maar geeft u een indicatie tijdens de ontwerptijd van de verwachte uitvoer van het runbook. Naarmate de set hulpprogramma's voor runbooks zich blijft ontwikkelen, neemt het belang van het declareren van uitvoergegevenstypen tijdens het ontwerpen toe. Daarom is het een best practice deze declaratie op te nemen in runbooks die u maakt.

Het volgende voorbeeldrunbook voert een tekenreeksobject uit en bevat een declaratie van het uitvoertype. Als uw runbook een matrix van een bepaald type uitvoert, moet u nog steeds het type opgeven in tegenstelling tot een matrix van het type.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Uitvoergegevenstype declareer in een grafisch runbook

Als u een uitvoertype in een grafisch of grafisch PowerShell Workflow-runbook wilt declaren, kunt u de menuoptie **Invoer** en uitvoer selecteren en het uitvoertype invoeren. Het is raadzaam om de volledige .NET-klassenaam te gebruiken om het type gemakkelijk te identificeren wanneer een bovenliggend runbook hier naar verwijst. Met de volledige naam worden alle eigenschappen van de klasse beschikbaar gemaakt voor de gegevensbus in het runbook en wordt de flexibiliteit vergroot wanneer de eigenschappen worden gebruikt voor voorwaardelijke logica, logboekregistratie en verwijzing als waarden voor andere runbookactiviteiten.<br> ![De optie Invoer en uitvoer van runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Nadat u een waarde  hebt ingevoerd in het veld Uitvoertype in het deelvenster Invoer- en uitvoereigenschappen, moet u buiten het besturingselement klikken zodat uw vermelding wordt herkend.

In het volgende voorbeeld ziet u twee grafische runbooks ter demonstratie van de functie Invoer en Uitvoer. Door het modulaire runbookontwerpmodel toe te passen, hebt u één runbook als de sjabloon Runbook verifiëren die verificatie beheert met Azure met behulp van het Uitvoeren als-account. Het tweede runbook, dat normaal gesproken kernlogica uitvoert om een bepaald scenario te automatiseren, voert in dit geval de sjabloon Runbook verifiëren uit. De resultaten worden weergegeven in het deelvenster Testuitvoer. Onder normale omstandigheden zou u dit runbook iets laten doen met een resource die gebruik maakt van de uitvoer van het onderliggende runbook.

Dit is de basislogica van het **runbook AuthenticateTo-Azure.**<br> ![Voorbeeld van een runbooksjabloon ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) verifiëren.

Het runbook bevat het uitvoertype `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` , dat de eigenschappen van het verificatieprofiel retourneert.<br> ![Voorbeeld van runbookuitvoertype](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Hoewel dit runbook eenvoudig is, is er één configuratie-item dat u hier kunt aanroepen. Met de laatste activiteit wordt de cmdlet uitgevoerd om profielgegevens naar een variabele te schrijven met behulp van `Write-Output` een PowerShell-expressie voor de `Inputobject` parameter . Deze parameter is vereist voor `Write-Output` .

Het tweede runbook in dit voorbeeld, met de **naam Test-ChildOutputType,** definieert gewoon twee activiteiten.<br> ![Voorbeeld van runbook voor onderliggend uitvoertype](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Met de eerste activiteit wordt het **runbook AuthenticateTo-Azure** aanroepen. Met de tweede activiteit wordt de `Write-Verbose` cmdlet uitgevoerd **met Gegevensbron** ingesteld **op Activiteituitvoer.** Veldpad **is ook** ingesteld op **Context.Subscription.SubscriptionName,** de contextuitvoer van het **runbook AuthenticateTo-Azure.**<br> ![Parametergegevensbron write-verbose cmdlet](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

De resulterende uitvoer is de naam van het abonnement.<br> ![Test-ChildOutputType runbook](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Werken met berichtstromen

In tegenstelling tot de uitvoerstroom communiceren berichtstromen informatie met de gebruiker. Er zijn meerdere berichtstromen voor verschillende soorten informatie en Azure Automation elke stroom op een andere manier verwerkt.

### <a name="write-output-to-warning-and-error-streams"></a>Uitvoer schrijven naar waarschuwings- en foutstromen

Met de waarschuwing en fout worden logboekproblemen gestreamd die zich in een runbook voordoen. Azure Automation schrijft deze stromen naar de taakgeschiedenis bij het uitvoeren van een runbook. Automation bevat de streams in het deelvenster Testuitvoer in de Azure Portal wanneer een runbook wordt getest.

Standaard wordt een runbook nog steeds uitgevoerd na een waarschuwing of fout. U kunt opgeven dat uw runbook moet worden opgeschort bij een waarschuwing of fout door het runbook een voorkeursvariabele in te stellen [voordat](#work-with-preference-variables) u het bericht maakt. Stel de variabele bijvoorbeeld in op Stoppen om ervoor te zorgen dat het runbook wordt opgeschort bij een fout zoals bij een `ErrorActionPreference` uitzondering.

Maak een waarschuwing of foutbericht met behulp van de cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) [of Write-Error.](/powershell/module/microsoft.powershell.utility/write-error) Activiteiten kunnen ook schrijven naar de waarschuwings- en foutstromen.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Uitvoer schrijven voor foutopsporingsstroom

Azure Automation maakt gebruik van de foutopsporingsberichtstroom voor interactieve gebruikers. Standaard Azure Automation geen foutopsporingsstroomgegevens vastgelegd, worden alleen uitvoer-, fout- en waarschuwingsgegevens vastgelegd, evenals uitgebreide gegevens als het runbook is geconfigureerd om deze vast te leggen.

Als u foutopsporingsstroomgegevens wilt vastleggen, moet u twee acties uitvoeren in uw runbooks:

1. Stel de variabele `$GLOBAL:DebugPreference="Continue"` in, waardoor PowerShell moet doorgaan wanneer er een foutopsporingsbericht wordt aangetroffen.  De **$GLOBAL: vertelt** PowerShell om dit te doen in het globale bereik in plaats van het lokale bereik waarin het script zich op het moment dat de instructie wordt uitgevoerd.

1. U kunt de foutopsporingsstroom die we niet vastleggen, omleiden naar een stream die we wel vastleggen, zoals *uitvoer*. Dit wordt gedaan door PowerShell-omleiding in te stellen op de instructie die moet worden uitgevoerd. Zie Over omleiding voor meer informatie over [PowerShell-omleiding.](/powershell/module/microsoft.powershell.core/about/about_redirection)

#### <a name="examples"></a>Voorbeelden

In dit voorbeeld wordt het runbook geconfigureerd met behulp van de cmdlets en met de bedoeling twee verschillende stromen `Write-Output` `Write-Debug` uit te voeren.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Als dit runbook als volgt zou worden uitgevoerd, zou het uitvoervenster voor de runbook-taak de volgende uitvoer streamen:

```output
This is an output message.
```

In dit voorbeeld is het runbook geconfigureerd zoals in het vorige voorbeeld, behalve dat de instructie is opgenomen in de toevoeging van aan het `$GLOBAL:DebugPreference="Continue"` `5>&1` einde van de `Write-Debug` instructie.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Als dit runbook moet worden uitgevoerd, wordt in het uitvoervenster voor de runbook-taak de volgende uitvoer gestreamd:

```output
This is an output message.
This is a debug message.
```

Dit komt doordat de instructie powerShell vertelt om foutopsporingsberichten weer te geven en de toevoeging van aan het einde van de instructie PowerShell vertelt dat Stream 5 (foutopsporing) moet worden omgeleid naar `$GLOBAL:DebugPreference="Continue"` `5>&1` stream `Write-Debug` 1 (uitvoer).

### <a name="write-output-to-verbose-stream"></a>Uitvoer schrijven naar uitgebreide stroom

De uitgebreide berichtenstroom ondersteunt algemene informatie over runbookbewerkingen. Omdat de foutopsporingsstroom niet beschikbaar is voor een runbook, moet uw runbook uitgebreide berichten gebruiken voor foutopsporingsinformatie.

Standaard worden in de taakgeschiedenis geen uitgebreide berichten van gepubliceerde runbooks opgeslagen, om prestatieredenen. Als u uitgebreide berichten wilt opslaan,  gebruikt u de Azure Portal tabblad Configureren met de instelling Log **Verbose Records** om uw gepubliceerde runbooks te configureren om uitgebreide berichten te loggen. Schakel deze optie alleen in voor probleemoplossing of foutopsporing van een runbook. In de meeste gevallen moet u de standaardinstelling voor het niet registreren van uitgebreide records behouden.

Bij [het testen van een runbook](./manage-runbooks.md)worden uitgebreide berichten niet weergegeven, zelfs niet als het runbook is geconfigureerd om uitgebreide records te loggen. Als u uitgebreide berichten wilt weergeven [tijdens het testen van een runbook,](./manage-runbooks.md)moet u de variabele instellen op `VerbosePreference` Doorgaan. Als deze variabele is ingesteld, worden uitgebreide berichten weergegeven in het deelvenster Testuitvoer van de Azure Portal.

Met de volgende code maakt u een uitgebreid bericht met behulp van de cmdlet [Write-Verbose.](/powershell/module/microsoft.powershell.utility/write-verbose)

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Voortgangsrecords verwerken

U kunt het tabblad **Configureren van** de Azure Portal runbook configureren om voortgangsrecords te noteren. De standaardinstelling is om de records niet te noteer, om de prestaties te maximaliseren. In de meeste gevallen moet u de standaardinstelling behouden. Schakel deze optie alleen in voor probleemoplossing of foutopsporing van een runbook.

Als u logboekregistratie van voortgangsrecords inschakelen, schrijft uw runbook een record naar de taakgeschiedenis vóór en nadat elke activiteit wordt uitgevoerd. Bij het testen van een runbook worden geen voortgangsberichten weergegeven, zelfs niet als het runbook is geconfigureerd om voortgangsrecords te noteren.

>[!NOTE]
>De [write-progress](/powershell/module/microsoft.powershell.utility/write-progress) cmdlet is niet geldig in een runbook, omdat deze cmdlet is bedoeld voor gebruik met een interactieve gebruiker.

## <a name="work-with-preference-variables"></a>Werken met voorkeursvariabelen

U kunt bepaalde Windows PowerShell [voorkeursvariabelen](/powershell/module/microsoft.powershell.core/about/about_preference_variables) in uw runbooks instellen om de reactie te bepalen op gegevens die naar verschillende uitvoerstromen worden verzonden. De volgende tabel bevat de voorkeursvariabelen die kunnen worden gebruikt in runbooks, met hun standaard- en geldige waarden. Er zijn extra waarden beschikbaar voor de voorkeursvariabelen bij gebruik in Windows PowerShell buiten Azure Automation.

| Variabele | Standaardwaarde | Geldige waarden |
|:--- |:--- |:--- |
| `WarningPreference` |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| `ErrorActionPreference` |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stoppen<br>Doorgaan<br>SilentlyContinue |

De volgende tabel bevat het gedrag voor de voorkeursvariabelewaarden die geldig zijn in runbooks.

| Waarde | Gedrag |
|:--- |:--- |
| Doorgaan |Registreert het bericht en blijft het runbook uitvoeren. |
| SilentlyContinue |Blijft het runbook uitvoeren zonder het bericht te registreren. Deze waarde heeft als gevolg dat het bericht wordt genegeerd. |
| Stoppen |Het bericht wordt geregistreerd en het runbook wordt onderbroken. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbookuitvoer en -berichten ophalen

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Runbookuitvoer en -berichten ophalen in Azure Portal

U kunt de details van een runbook-taak in de Azure Portal met behulp van het **tabblad Taken** voor het runbook. In de taaksamenvatting worden de invoerparameters en de uitvoerstroom [weergegeven,](#use-the-output-stream)naast algemene informatie over de taak en eventuele uitzonderingen die zijn opgetreden. De taakgeschiedenis bevat berichten uit de uitvoerstroom en [waarschuwings- en foutstromen](#write-output-to-warning-and-error-streams). Het bevat ook berichten van [](#handle-progress-records) de [uitgebreide stroom](#write-output-to-verbose-stream) en voortgangsrecords als het runbook is geconfigureerd om uitgebreide records en voortgangsrecords te loggen.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Runbookuitvoer en -berichten ophalen in Windows PowerShell

In Windows PowerShell kunt u uitvoer en berichten uit een runbook ophalen met behulp van de cmdlet [Get-AzAutomationJobOutput.](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) Deze cmdlet vereist de id van de taak en heeft een parameter met de naam waarin de `Stream` stroom moet worden opgehaald. U kunt voor deze parameter de waarde Any opgeven om alle stromen voor de taak op te halen.

In het volgende voorbeeld wordt een voorbeeldrunbook gestart en wordt gewacht tot dit is voltooid. Zodra het runbook is uitgevoerd, verzamelt het script de runbookuitvoerstroom van de taak.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    -AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Runbookuitvoer en -berichten ophalen in grafische runbooks

Voor grafische runbooks is extra logboekregistratie van uitvoer en berichten beschikbaar in de vorm van tracering op activiteitsniveau. Er zijn twee traceringsniveaus: Basic en Detailed. Eenvoudige tracering geeft de begin- en eindtijd weer voor elke activiteit in het runbook, plus informatie met betrekking tot nieuwe activiteiten. Enkele voorbeelden zijn het aantal pogingen en de begintijd van de activiteit. Gedetailleerde tracering bevat basisfuncties voor tracering plus logboekregistratie van invoer- en uitvoergegevens voor elke activiteit. 

Op dit moment schrijft tracering op activiteitsniveau records met behulp van de uitgebreide stroom. Daarom moet u uitgebreide logboekregistratie inschakelen wanneer u tracering inschakelen. Voor grafische runbooks met tracering ingeschakeld, is het niet nodig om voortgangsrecords te logboeken. Eenvoudige tracering heeft hetzelfde doel en is meer informatief.

![Weergave voor grafisch ontwerp van taakstreams](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

U kunt in de afbeelding zien dat het inschakelen van uitgebreide logboekregistratie en tracering voor grafische runbooks veel meer informatie beschikbaar maakt in de weergave **Taakstromen voor** productie. Deze extra informatie kan essentieel zijn voor het oplossen van productieproblemen met een runbook. 

Tenzij u deze informatie echter nodig hebt om de voortgang van een runbook bij te houden voor het oplossen van problemen, kunt u tracering uitschakelen als een algemene praktijk. De traceerrecords kunnen bijzonder groot zijn. Met grafische runbooktracing kunt u twee tot vier records per activiteit krijgen, afhankelijk van uw configuratie van Basis- of Gedetailleerde tracering.

**Tracering op activiteitsniveau inschakelen:**

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst van runbooks te openen.
3. Selecteer op de pagina Runbooks een grafisch runbook in uw lijst met runbooks.
4. Klik **onder Instellingen** op **Logboekregistratie en tracering.**
5. Klik op de pagina Logboekregistratie en tracering onder **Uitgebreide records** vastleggen op **Aan om** uitgebreide logboekregistratie in teschakelen.
6. Wijzig **onder Tracering op activiteitsniveau** het traceringsniveau in **Basic** of **Detailed** op basis van het traceringsniveau dat u nodig hebt.<br>

   ![De pagina Logboekregistratie en tracering van grafische ontwerp](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Runbookuitvoer en -berichten ophalen in Microsoft Azure Monitor-logboeken

Azure Automation kunt de taakstatus en taakstromen van het runbook verzenden naar uw Log Analytics-werkruimte. Azure Monitor ondersteunt logboeken waarmee u het volgende kunt doen:

* Krijg inzicht in uw Automation-taken.
* Activeer een e-mailbericht of waarschuwing op basis van de status van uw runbook-taak, bijvoorbeeld Mislukt of Tijdelijk tijdelijk.
* Geavanceerde query's schrijven over taakstromen.
* Taken correleren in Automation-accounts.
* Taakgeschiedenis visualiseren.

Zie Forward job status and job streams from Automation to Azure Monitor Logs (Taakstatus en taakstromen van Automation doorsturen naar logboeken) voor meer informatie over het configureren van de integratie met Azure Monitor-logboeken voor het verzamelen, correleren en reageren [op taakgegevens.](automation-manage-send-joblogs-log-analytics.md)

## <a name="next-steps"></a>Volgende stappen

* Zie Runbooks beheren in Azure Automation voor meer [Azure Automation.](manage-runbooks.md)
* Zie [PowerShell-documentatie](/powershell/scripting/overview) als u niet bekend bent met PowerShell-scripts.
* Zie [Az.Automation](/powershell/module/az.automation)Azure Automation naslag voor powershell-cmdlet.
