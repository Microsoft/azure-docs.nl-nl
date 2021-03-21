---
title: Runbook-uitvoer en-berichten stromen configureren
description: In dit artikel leest u hoe u fout afhandeling kunt implementeren en worden uitvoer-en bericht stromen in Azure Automation runbooks beschreven.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.openlocfilehash: beed3ec50d0c7990168ee75976c732796cdbe246
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324429"
---
# <a name="configure-runbook-output-and-message-streams"></a>Runbook-uitvoer en-berichten stromen configureren

De meeste Azure Automation runbooks hebben een vorm van uitvoer. Deze uitvoer kan een fout bericht voor de gebruiker zijn of een complex object dat is bedoeld om te worden gebruikt met een ander runbook. Windows Power shell biedt [meerdere streams](/powershell/module/microsoft.powershell.core/about/about_redirection) voor het verzenden van uitvoer van een script of werk stroom. Azure Automation werkt met elk van deze stromen anders. Volg de aanbevolen procedures voor het gebruik van de streams wanneer u een runbook maakt.

De volgende tabel geeft een korte beschrijving van elke stroom met het gedrag ervan in de Azure Portal voor gepubliceerde runbooks en tijdens [het testen van een runbook](./manage-runbooks.md). De uitvoer stroom is de hoofd stroom die wordt gebruikt voor de communicatie tussen runbooks. De andere stromen worden geclassificeerd als berichten stromen, bedoeld om informatie naar de gebruiker te communiceren.

| Stream | Beschrijving | Gepubliceerd | Testen |
|:--- |:--- |:--- |:--- |
| Fout |Foutbericht bedoeld voor de gebruiker. In tegens telling tot een uitzonde ring, wordt het runbook na een fout bericht standaard voortgezet. |Naar taak geschiedenis geschreven |Weer gegeven in het deel venster test uitvoer |
| Fouten opsporen |Berichten die zijn bedoeld voor een interactieve gebruiker. Mag niet worden gebruikt in runbooks. |Niet naar taak geschiedenis geschreven |Niet weer gegeven in het deel venster test uitvoer |
| Uitvoer |Objecten die zijn bedoeld om te worden verbruikt door andere runbooks. |Naar taak geschiedenis geschreven |Weer gegeven in het deel venster test uitvoer |
| Voortgang |Records die automatisch worden gegenereerd voor en na elke activiteit in het runbook. Het runbook mag geen eigen voortgangs records maken, omdat deze zijn bedoeld voor een interactieve gebruiker. |Wordt alleen naar de taak geschiedenis geschreven als de voortgang van de logboek registratie is ingeschakeld voor het runbook |Niet weer gegeven in het deel venster test uitvoer |
| Uitgebreid |Berichten die algemene informatie of fout opsporingsgegevens geven. |Wordt alleen naar de taak geschiedenis geschreven als uitgebreide logboek registratie is ingeschakeld voor het runbook |Wordt alleen weer gegeven in het deel venster test uitvoer als er een `VerbosePreference` variabele is ingesteld om door te gaan in het runbook |
| Waarschuwing |Waarschuwingsbericht bedoeld voor de gebruiker. |Naar taak geschiedenis geschreven |Weer gegeven in het deel venster test uitvoer |

## <a name="use-the-output-stream"></a>De uitvoer stroom gebruiken

De uitvoer stroom wordt gebruikt voor de uitvoer van objecten die zijn gemaakt door een script of werk stroom wanneer deze op de juiste wijze wordt uitgevoerd. Azure Automation maakt hoofd zakelijk gebruik van deze stroom voor objecten die worden gebruikt door bovenliggende runbooks die het [huidige runbook](automation-child-runbooks.md)aanroepen. Wanneer een bovenliggend [runbook inline aanroept](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), retourneert het onderliggende gegevens van de uitvoer stroom naar het bovenliggende item.

Uw runbook gebruikt de uitvoer stroom om algemene informatie alleen te communiceren met de client als deze nooit wordt aangeroepen door een ander runbook. Als best practice is het echter meestal gebruikelijk dat runbooks de [uitgebreide stroom](#write-output-to-verbose-stream) gebruiken om algemene informatie aan de gebruiker te communiceren.

Laat uw runbook gegevens schrijven naar de uitvoer stroom met behulp van [Write-output](/powershell/module/microsoft.powershell.utility/write-output). U kunt het object ook op een eigen regel in het script plaatsen.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Uitvoer van een functie verwerken

Wanneer een runbook-functie naar de uitvoer stroom schrijft, wordt de uitvoer opnieuw door gegeven aan het runbook. Als het runbook die uitvoer toewijst aan een variabele, wordt de uitvoer niet naar de uitvoer stroom geschreven. Schrijven naar andere stromen vanuit de functie schrijft naar de bijbehorende stroom voor het runbook. Bekijk het volgende voor beeld van een Power shell-werk stroom runbook.

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

De uitvoer stroom voor de runbook-taak is:

```output
Output inside of function
Output outside of function
```

De uitgebreide stroom voor de runbook-taak is:

```output
Verbose outside of function
Verbose inside of function
```

Nadat u het runbook hebt gepubliceerd en voordat u het hebt gestart, moet u ook uitgebreide logboek registratie inschakelen in de runbook-instellingen om de uitgebreide uitvoer van de stream te verkrijgen.

### <a name="declare-output-data-type"></a>Type uitvoer gegevens declareren

Hier volgen enkele voor beelden van uitvoer gegevens typen:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Het uitvoer gegevens type in een werk stroom declareren

In een werk stroom wordt het gegevens type van de uitvoer opgegeven met behulp van het [kenmerk output](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)schema. Dit kenmerk heeft geen effect tijdens runtime, maar biedt u een indicatie tijdens de ontwerp fase van de verwachte uitvoer van het runbook. Wanneer het hulp programma voor runbooks blijft ontwikkelen, neemt het belang van het declareren van uitvoer gegevens typen tijdens de ontwerp fase toe. Daarom is het een best practice om deze declaratie in te voegen in een runbooks die u maakt.

Het volgende voorbeeldrunbook voert een tekenreeksobject uit en bevat een declaratie van het uitvoertype. Als uw runbook een matrix van een bepaald type uitvoert, moet u nog steeds het type opgeven in tegenstelling tot een matrix van het type.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Het uitvoer gegevens type in een grafisch runbook declareren

Als u een uitvoer type in een grafisch of grafisch Power shell-werk stroom runbook wilt declareren, kunt u de menu optie **invoer en uitvoer** selecteren en het uitvoer type opgeven. Het is raadzaam om de volledige naam van een .NET-klasse te gebruiken om het type gemakkelijk te identificeren wanneer een bovenliggend runbook ernaar verwijst. Als u de volledige naam gebruikt, worden alle eigenschappen van de klasse weer gegeven in de gegevensbus in het runbook en wordt de flexibiliteit verhoogd wanneer de eigenschappen worden gebruikt voor voorwaardelijke logica, logboek registratie en verwijzingen als waarden voor andere runbook-activiteiten.<br> ![De optie Runbook-invoer en-uitvoer](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Nadat u een waarde hebt ingevoerd in het veld **uitvoer type** in het deel venster invoer-en uitvoer eigenschappen, moet u buiten het besturings element klikken, zodat de invoer wordt herkend.

In het volgende voor beeld ziet u twee grafische runbooks voor het demonstreren van de functie voor invoer en uitvoer. Voor het Toep assen van het modulaire runbook-ontwerp model hebt u één runbook als de sjabloon voor het verifiëren van het runbook met behulp van het run as-account om verificatie met Azure te beheren. Het tweede runbook, waarmee normaal gesp roken basis logica wordt uitgevoerd om een bepaald scenario te automatiseren, wordt in dit geval de sjabloon voor het verifiëren van het Runbook uitgevoerd. De resultaten worden weer gegeven in het deel venster test uitvoer. Onder normale omstandigheden zou u dit runbook iets doen tegen een resource door gebruik te maken van de uitvoer van het onderliggende runbook.

Dit is de basis logica van het **AuthenticateTo-Azure-** runbook.<br> ![Voor beeld van een Runbook-sjabloon verifiëren ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) .

Het runbook bevat het uitvoer type `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` , waarmee de eigenschappen van het verificatie profiel worden geretourneerd.<br> ![Voor beeld van Runbook-uitvoer type](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Hoewel dit runbook eenvoudig is, is er één configuratie-item om hier aan te roepen. Met de laatste activiteit wordt de `Write-Output` cmdlet uitgevoerd om profiel gegevens naar een variabele te schrijven met behulp van een Power shell-expressie voor de `Inputobject` para meter. Deze para meter is vereist voor `Write-Output` .

Het tweede runbook in dit voor beeld, met de naam **test-ChildOutputType**, definieert twee activiteiten.<br> ![Voor beeld van een onderliggende uitvoer van het type Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Met de eerste activiteit wordt het **AuthenticateTo-Azure-** runbook aangeroepen. Met de tweede activiteit wordt de `Write-Verbose` cmdlet uitgevoerd met de **gegevens bron** ingesteld op **uitvoer van activiteit**. Ook is het **veld pad** ingesteld op **context. Subscription. subscriptionname**, de context uitvoer van het **AuthenticateTo-Azure-** runbook.<br> ![Gegevens bron voor cmdlet write-verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

De resulterende uitvoer is de naam van het abonnement.<br> ![Test-ChildOutputType Runbook-resultaten](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Werken met bericht stromen

In tegens telling tot de uitvoer stroom communiceren bericht stromen informatie naar de gebruiker. Er zijn meerdere berichten stromen voor verschillende soorten informatie en Azure Automation elke stroom anders verwerkt.

### <a name="write-output-to-warning-and-error-streams"></a>Uitvoer naar waarschuwings-en fout stromen schrijven

De waarschuwing en fout stromen registreren logboek problemen die optreden in een runbook. Azure Automation deze stromen naar de taak geschiedenis schrijft bij het uitvoeren van een runbook. Automation bevat de stromen in het deel venster test uitvoer in het Azure Portal wanneer een runbook wordt getest.

Een runbook blijft standaard uitgevoerd na een waarschuwing of fout. U kunt opgeven dat uw runbook moet worden onderbroken op een waarschuwing of fout door het runbook een [Voorkeurs variabele](#work-with-preference-variables) instellen voordat het bericht wordt gemaakt. Als u bijvoorbeeld wilt dat het runbook wordt onderbroken als er een fout optreedt tijdens een uitzonde ring, stelt `ErrorActionPreference` u de variabele in op stoppen.

Maak een waarschuwing of fout bericht met behulp van de cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) of [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) . Activiteiten kunnen ook worden geschreven naar de waarschuwings-en fout stromen.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Uitvoer naar stroom voor fout opsporing schrijven

Azure Automation maakt gebruik van de berichten stroom voor fout opsporing voor interactieve gebruikers. Standaard Azure Automation geen stroom gegevens voor fout opsporing vastlegt, worden alleen uitvoer-, fout-en waarschuwings gegevens vastgelegd, evenals uitgebreide gegevens als het runbook zo is geconfigureerd dat het kan worden vastgelegd.

Als u gegevens voor fout opsporing wilt vastleggen, moet u twee acties uitvoeren in uw runbooks:

1. Stel de variabele `$GLOBAL:DebugPreference="Continue"` in, die aangeeft dat Power shell moet door gaan wanneer er een fout bericht wordt aangetroffen.  In het gedeelte **$Global:** wordt aangegeven dat Power shell dit moet doen in het globale bereik in plaats van op welke lokale scope het script zich bevindt op het moment dat de instructie wordt uitgevoerd.

1. Omleiding van de stroom voor fout opsporing die niet wordt vastgelegd in een stroom die wordt vastgelegd als *uitvoer*. Dit doet u door Power shell-omleiding in te stellen op basis van de instructie die moet worden uitgevoerd. Zie [over omleiding](/powershell/module/microsoft.powershell.core/about/about_redirection)voor meer informatie over het omleiden van Power shell.

#### <a name="examples"></a>Voorbeelden

In dit voor beeld wordt het runbook geconfigureerd met de `Write-Output` `Write-Debug` cmdlets en de bedoeling om twee verschillende stromen uit te voeren.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Als dit runbook zou worden uitgevoerd, zou het uitvoer deel venster voor de runbook-taak de volgende uitvoer streamen:

```output
This is an output message.
```

In dit voor beeld is het runbook geconfigureerd zoals in het vorige voor beeld, behalve dat de instructie `$GLOBAL:DebugPreference="Continue"` is opgenomen in de toevoeging aan `5>&1` het einde van de `Write-Debug` instructie.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Als dit runbook zou worden uitgevoerd, zou het uitvoer deel venster voor de runbook-taak de volgende uitvoer streamen:

```output
This is an output message.
This is a debug message.
```

Dit probleem doet zich `$GLOBAL:DebugPreference="Continue"` voor omdat de instructie Power shell vertelt dat fout opsporingsgegevens worden weer gegeven en de toevoeging `5>&1` aan het einde van de `Write-Debug` instructie geeft aan dat Power shell stream 5 (debug) omleidt naar stream 1 (uitvoer).

### <a name="write-output-to-verbose-stream"></a>Uitvoer naar uitgebreide stroom schrijven

De uitgebreide berichten stroom ondersteunt algemene informatie over de runbook-bewerking. Omdat de stream voor fout opsporing niet beschikbaar is voor een runbook, moet uw runbook uitgebreide berichten gebruiken voor fout opsporingsgegevens.

Standaard slaat de taak geschiedenis geen uitgebreide berichten van gepubliceerde runbooks op om prestatie redenen. Als u uitgebreide berichten wilt opslaan, gebruikt u het tabblad Azure Portal **configureren** met de instelling **uitgebreide records registreren** om uw gepubliceerde runbooks te configureren om uitgebreide berichten te registreren. Schakel deze optie alleen in voor probleemoplossing of foutopsporing van een runbook. In de meeste gevallen moet u de standaard instelling voor het niet registreren van uitgebreide records.

Bij het [testen van een runbook](./manage-runbooks.md)worden uitgebreide berichten niet weer gegeven, zelfs niet als het runbook is geconfigureerd voor het registreren van uitgebreide records. Als u uitgebreide berichten wilt weer geven tijdens [het testen van een runbook](./manage-runbooks.md), moet u de `VerbosePreference` variabele instellen om door te gaan. Als deze variabele is ingesteld, worden uitgebreide berichten weer gegeven in het deel venster test uitvoer van de Azure Portal.

Met de volgende code wordt een uitgebreid bericht gemaakt met behulp van de cmdlet [Write-verbose](/powershell/module/microsoft.powershell.utility/write-verbose) .

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Voortgangs records verwerken

U kunt het tabblad **configureren** van de Azure Portal gebruiken om een runbook te configureren voor het registreren van voortgangs records. De standaard instelling is om de records niet te registreren om de prestaties te maximaliseren. In de meeste gevallen moet u de standaard instelling blijven gebruiken. Schakel deze optie alleen in voor probleemoplossing of foutopsporing van een runbook.

Als u voortgangs record logboek registratie inschakelt, schrijft uw runbook een record naar de taak geschiedenis voor en na elke uitvoering van de activiteit. Als u een runbook test, worden er geen voortgangs berichten weer gegeven, zelfs niet als het runbook is geconfigureerd voor het registreren van voortgangs records.

>[!NOTE]
>De cmdlet [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) is niet geldig in een runbook, omdat deze cmdlet is bedoeld voor gebruik met een interactieve gebruiker.

## <a name="work-with-preference-variables"></a>Werken met voorkeurs variabelen

U kunt bepaalde Windows Power shell- [Voorkeurs variabelen](/powershell/module/microsoft.powershell.core/about/about_preference_variables) in uw runbooks zo instellen dat de reactie op gegevens die worden verzonden naar verschillende uitvoer stromen worden beheerd. De volgende tabel bevat de voorkeurs variabelen die kunnen worden gebruikt in runbooks, met de standaard waarde en geldige waarden. Er zijn aanvullende waarden beschikbaar voor de voorkeurs variabelen wanneer deze worden gebruikt in Windows Power shell buiten Azure Automation.

| Variabele | Standaardwaarde | Geldige waarden |
|:--- |:--- |:--- |
| `WarningPreference` |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| `ErrorActionPreference` |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stoppen<br>Doorgaan<br>SilentlyContinue |

De volgende tabel bevat het gedrag voor de waarden van de voorkeurs variabelen die geldig zijn in runbooks.

| Waarde | Gedrag |
|:--- |:--- |
| Doorgaan |Registreert het bericht en blijft het runbook uitvoeren. |
| SilentlyContinue |Blijft het runbook uitvoeren zonder het bericht te registreren. Deze waarde heeft als gevolg dat het bericht wordt genegeerd. |
| Stoppen |Het bericht wordt geregistreerd en het runbook wordt onderbroken. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbook-uitvoer en-berichten ophalen

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Runbook-uitvoer en-berichten ophalen in Azure Portal

U kunt de details van een runbook-taak weer geven in de Azure Portal met het tabblad **taken** voor het runbook. Het taak overzicht geeft de invoer parameters en de [uitvoer stroom](#use-the-output-stream)weer, naast algemene informatie over de taak en eventuele uitzonde ringen die zijn opgetreden. De taak geschiedenis bevat berichten van de uitvoer stroom en [waarschuwings-en fout stromen](#write-output-to-warning-and-error-streams). Het bevat ook berichten van de [uitgebreide stroom](#write-output-to-verbose-stream) en de [voortgangs records](#handle-progress-records) als het runbook is geconfigureerd voor het registreren van uitgebreide records en de voortgang.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Runbook-uitvoer en-berichten ophalen in Windows Power shell

In Windows Power shell kunt u uitvoer en berichten ophalen uit een runbook met behulp van de cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) . Deze cmdlet vereist de ID van de taak en heeft een para meter `Stream` met de naam waarin de te verkrijgen stroom moet worden opgegeven. U kunt een waarde van elk voor deze para meter opgeven om alle streams voor de taak op te halen.

In het volgende voorbeeld wordt een voorbeeldrunbook gestart en wordt gewacht tot dit is voltooid. Zodra het runbook is voltooid, wordt de uitvoer stroom van het runbook van de taak door het script verzameld.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Runbook-uitvoer en-berichten ophalen in grafische runbooks

Voor grafische runbooks is extra logboek registratie van uitvoer en berichten beschikbaar in de vorm van tracering op activiteit niveau. Er zijn twee tracerings niveaus: basis en gedetailleerd. Bij basis tracering wordt de begin-en eind tijd voor elke activiteit in het runbook weer gegeven, plus informatie met betrekking tot een nieuwe activiteit. Enkele voor beelden zijn het aantal pogingen en de begin tijd van de activiteit. Gedetailleerde tracering omvat elementaire traceer functies plus logboek registratie van invoer-en uitvoer gegevens voor elke activiteit. 

Met tracering op huidige activiteit worden records geschreven met de uitgebreide stroom. Daarom moet u uitgebreide logboek registratie inschakelen wanneer tracering is ingeschakeld. Voor grafische runbooks waarvoor tracering is ingeschakeld, hoeft u geen voortgangs records te registreren. Basis tracering heeft hetzelfde doel en is meer informatie.

![Werk stroom voor het maken van een grafische taak](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

U kunt in de afbeelding zien dat uitgebreide logboek registratie en tracering voor grafische runbooks veel meer informatie beschikbaar maakt in de weer gave productie **taak stromen** . Deze extra informatie kan essentieel zijn voor het oplossen van problemen met productie met een runbook. 

Tenzij u deze informatie nodig hebt voor het volgen van de voortgang van een runbook voor het oplossen van problemen, kunt u het beste de tracering uitschakelen als een algemene procedure. De tracerings records kunnen zeer groot zijn. Met grafische runbook-tracering kunt u twee tot vier records per activiteit verkrijgen, afhankelijk van uw configuratie van basis-of gedetailleerde tracering.

**Tracering op activiteit niveau inschakelen:**

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst van runbooks te openen.
3. Selecteer op de pagina Runbooks een grafisch runbook in de lijst met Runbooks.
4. Klik onder **instellingen** op **logboek registratie en tracering**.
5. Klik op de pagina logboek registratie en tracering onder **uitgebreide records vastleggen** op **ingeschakeld** om uitgebreide logboek registratie in te scha kelen.
6. Wijzig onder **tracering op activiteit niveau** het tracerings niveau in **Basic** of **gedetailleerd**, op basis van het tracerings niveau dat u nodig hebt.<br>

   ![De pagina logboek registratie en tracering voor grafisch ontwerpen](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Runbook-uitvoer en-berichten ophalen in Microsoft Azure controle logboeken

Azure Automation kunt de status van de runbook-taak en taak stromen verzenden naar uw Log Analytics-werk ruimte. Azure Monitor ondersteunt logboeken waarmee u het volgende kunt doen:

* Krijg inzicht in uw automatiserings taken.
* Een e-mail of waarschuwing activeren op basis van de status van uw runbook-taak, bijvoorbeeld mislukt of opgeschort.
* Schrijf geavanceerde query's over de taak stromen.
* Verdeel taken tussen Automation-accounts.
* Taak geschiedenis visualiseren.

Voor meer informatie over het configureren van integratie met Azure Monitor logboeken voor het verzamelen, correleren en uitvoeren van taak gegevens raadpleegt [u de taak status door sturen en taak stromen van automatisering naar Azure monitor-logboeken](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor het werken met runbooks.
* Als u niet bekend bent met Power shell scripting, raadpleegt u [Power shell](/powershell/scripting/overview) -documentatie.
* Zie [AZ. Automation](/powershell/module/az.automation)(Engelstalig) voor de Azure Automation Power shell-cmdlet-verwijzing.
