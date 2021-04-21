---
title: Grafische runbooks maken in Azure Automation
description: In dit artikel wordt beschreven hoe u een grafisch runbook kunt maken zonder met code te werken.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 88492d914b710c7a738dd6d7f501e22d490065b6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833810"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Grafische runbooks maken in Azure Automation

Alle runbooks in Azure Automation zijn Windows PowerShell werkstromen. Grafische runbooks en grafische PowerShell Workflow-runbooks genereren PowerShell-code die de Automation-werkbalken uitvoeren, maar die u niet kunt weergeven of wijzigen. U kunt een grafisch runbook converteren naar een grafisch PowerShell Workflow-runbook en vice versa. U kunt deze runbooks echter niet converteren naar een tekstueel runbook. Bovendien kan de grafische Automation-editor geen tekstueel runbook importeren.

Met grafisch ontwerp kunt u runbooks maken voor Azure Automation zonder de complexiteit van de onderliggende Windows PowerShell of PowerShell Workflow-code. U kunt activiteiten aan het canvas toevoegen vanuit een bibliotheek met cmdlets en runbooks, deze aan elkaar koppelen en ze configureren om een werkstroom te vormen. Als u ooit hebt gewerkt met System Center Orchestrator of Service Management Automation (SMA), moet grafisch ontwerp er vertrouwd uitzien. Dit artikel bevat een inleiding tot de concepten die u nodig hebt om aan de slag te gaan met het maken van een grafisch runbook.

## <a name="overview-of-graphical-editor"></a>Overzicht van grafische editor

U kunt de grafische editor in de Azure Portal door een grafisch runbook te maken of te bewerken.

![Grafische werkruimte](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

In de volgende secties worden de besturingselementen in de grafische editor beschreven.

### <a name="canvas-control"></a>Besturingselement canvas

Met het besturingselement Canvas kunt u uw runbook ontwerpen. U kunt activiteiten van de knooppunten in het besturingselement Bibliotheek toevoegen aan het runbook en deze verbinden met koppelingen om runbooklogica te definiëren. Aan de onderkant van het canvas zijn er besturingselementen waarmee u kunt in- en uitzoomen.

### <a name="library-control"></a>Bibliotheekbesturingselement

Met het besturingselement Bibliotheek kunt u activiteiten [selecteren die](#use-activities) u aan uw runbook wilt toevoegen. U voegt ze toe aan het canvas, waar u ze kunt verbinden met andere activiteiten. Het besturingselement Bibliotheek bevat de secties die in de volgende tabel zijn gedefinieerd.

| Sectie | Beschrijving |
|:--- |:--- |
| Cmdlets |Alle cmdlets die in uw runbook kunnen worden gebruikt. Cmdlets zijn geordend op module. Alle modules die u in uw Automation-account hebt geïnstalleerd, zijn beschikbaar. |
| Runbooks |De runbooks in uw Automation-account. U kunt deze runbooks toevoegen aan het canvas om te worden gebruikt als onderliggende runbooks. Alleen runbooks van hetzelfde kerntype als het runbook dat wordt bewerkt, worden weergegeven. Voor grafische runbooks worden alleen op PowerShell gebaseerde runbooks weergegeven. Voor grafische PowerShell Workflow-runbooks worden alleen runbooks op basis van PowerShell Workflow weergegeven. |
| Assets |De [automatiseringsactiva](/previous-versions/azure/dn939988(v=azure.100)) in uw Automation-account die u in uw runbook kunt gebruiken. Als u een asset toevoegt aan een runbook, wordt een werkstroomactiviteit toegevoegd die de geselecteerde asset op haalt. In het geval van variabele assets kunt u selecteren of u een activiteit wilt toevoegen om de variabele op te halen of de variabele in te stellen. |
| Runbookbesturing |Beheeractiviteiten die kunnen worden gebruikt in uw huidige runbook. Een verbindingsactiviteit heeft meerdere invoerwaarden en wacht totdat alle zijn voltooid voordat de werkstroom wordt voortgezet. Bij een codeactiviteit worden een of meer regels PowerShell- of PowerShell Workflow-code uitgevoerd, afhankelijk van het type grafisch runbook. U kunt deze activiteit gebruiken voor aangepaste code of voor functionaliteit die moeilijk te bereiken is met andere activiteiten. |

### <a name="configuration-control"></a>Configuratiebeheer

Met het besturingselement Configuratie kunt u details verstrekken voor een object dat is geselecteerd op het canvas. De eigenschappen die beschikbaar zijn in dit besturingselement, zijn afhankelijk van het type object dat is geselecteerd. Wanneer u een optie in het besturingselement Configuratie kiest, worden er extra blades geopend voor meer informatie.

### <a name="test-control"></a>Besturingselement testen

Het besturingselement Testen wordt niet weergegeven wanneer de grafische editor voor het eerst wordt gestart. Deze wordt geopend wanneer u interactief een grafisch runbook test.

## <a name="use-activities"></a>Activiteiten gebruiken

Activiteiten zijn de bouwstenen van een runbook. Een activiteit kan een PowerShell-cmdlet, een onderliggend runbook of een werkstroom zijn. U kunt een activiteit toevoegen aan het runbook door er met de rechtermuisknop op te klikken in het besturingselement Bibliotheek en **Toevoegen aan canvas te selecteren.** U kunt vervolgens klikken en de activiteit slepen om deze ergens op het like canvas te plaatsen. De locatie van de activiteit op het canvas heeft geen invloed op de werking van het runbook. U kunt uw runbook op elke manier in kaart gebracht die het meest geschikt is om de werking ervan te visualiseren.

![Toevoegen aan canvas](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selecteer een activiteit op het canvas om de eigenschappen en parameters ervan te configureren op de blade Configuratie. U kunt het label van de activiteit wijzigen in een naam die u beschrijvend vindt. Het runbook voert nog steeds de oorspronkelijke cmdlet uit. U hoeft alleen de weergavenaam te wijzigen die in de grafische editor wordt gebruikt. Houd er rekening mee dat het label uniek moet zijn binnen het runbook.

### <a name="parameter-sets"></a>Parametersets

Een parameterset definieert de verplichte en optionele parameters die waarden accepteren voor een bepaalde cmdlet. Alle cmdlets hebben ten minste één parameterset en sommige hebben verschillende sets. Als een cmdlet meerdere parametersets heeft, moet u de cmdlet selecteren die u wilt gebruiken voordat u parameters kunt configureren. U kunt de parameterset wijzigen die door een activiteit wordt gebruikt door **Parameterset te selecteren** en een andere set te kiezen. In dit geval gaan alle parameterwaarden verloren die u al hebt geconfigureerd.

In het volgende voorbeeld heeft de cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) drie parametersets. In het voorbeeld wordt een set met de naam **ListVirtualMachineInResourceGroupParamSet** gebruikt, met één optionele parameter, voor het retourneren van alle virtuele machines in een resourcegroep. In het voorbeeld wordt ook de parameterset **GetVirtualMachineInResourceGroupParamSet** gebruikt om op te geven welke virtuele machine moet worden retourneren. Deze set heeft twee verplichte parameters en één optionele parameter.

![Parameterset](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parameterwaarden

Wanneer u een waarde voor een parameter opgeeft, selecteert u een gegevensbron om te bepalen hoe de waarde wordt opgegeven. De gegevensbronnen die beschikbaar zijn voor een bepaalde parameter zijn afhankelijk van de geldige waarden voor die parameter. Null is bijvoorbeeld geen beschikbare optie voor een parameter die geen null-waarden toestaat.

| Gegevensbron | Description |
|:--- |:--- |
| Constante waarde |Typ een waarde voor de parameter . Deze gegevensbron is alleen beschikbaar voor de volgende gegevenstypen: Int32, Int64, String, Boolean, DateTime, Switch. |
| Activiteitsuitvoer |Gebruik uitvoer van een activiteit die voorafgaat aan de huidige activiteit in de werkstroom. Alle geldige activiteiten worden weergegeven. Gebruik voor de parameterwaarde alleen de activiteit die de uitvoer produceert. Als de activiteit een object met meerdere eigenschappen als uitvoer heeft, kunt u de naam van een specifieke eigenschap typen nadat u de activiteit hebt geselecteerd. |
| Runbookinvoer |Selecteer een runbookinvoer als invoer voor de activiteitsparameter. |
| Variabele asset |Selecteer een Automation-variabele als invoer. |
| Referentie-asset |Selecteer een Automation-referentie als invoer. |
| Certificaatactivum |Selecteer een Automation-certificaat als invoer. |
| Verbindingsactivum |Selecteer een Automation-verbinding als invoer. |
| PowerShell-expressie |Geef een eenvoudige [PowerShell-expressie op.](#work-with-powershell-expressions) De expressie wordt geëvalueerd vóór de activiteit en het resultaat wordt gebruikt voor de parameterwaarde. U kunt variabelen gebruiken om te verwijzen naar de uitvoer van een activiteit of een runbookinvoerparameter. |
| Niet geconfigureerd |Hiermee kunt u een waarde die eerder is geconfigureerd, leeg maken. |

#### <a name="optional-additional-parameters"></a>Optionele aanvullende parameters

Alle cmdlets hebben de mogelijkheid om aanvullende parameters op te geven. Dit zijn veelgebruikte PowerShell-parameters of andere aangepaste parameters. In de grafische editor wordt een tekstvak weergegeven waarin u parameters kunt opgeven met behulp van de PowerShell-syntaxis. Als u bijvoorbeeld de algemene `Verbose` parameter wilt gebruiken, moet u `-Verbose:$True` opgeven.

### <a name="retry-activity"></a>Activiteit opnieuw proberen

Met de functionaliteit voor opnieuw proberen voor een activiteit kan deze meerdere keren worden uitgevoerd totdat aan een bepaalde voorwaarde wordt voldaan, zoals bij een lus. U kunt deze functie gebruiken voor activiteiten die meerdere keren moeten worden uitgevoerd, gevoelig zijn voor fouten, mogelijk meer dan één poging tot slagen nodig hebben, of de uitvoergegevens van de activiteit testen op geldige gegevens.

Wanneer u opnieuw proberen inschakelen voor een activiteit, kunt u een vertraging en een voorwaarde instellen. De vertraging is de tijd (gemeten in seconden of minuten) dat het runbook wacht voordat de activiteit opnieuw wordt uitgevoerd. Als u geen vertraging opgeeft, wordt de activiteit onmiddellijk nadat deze is voltooid opnieuw uitgevoerd.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Schermopname van de functie-instellingen Voor opnieuw proberen inschakelen.":::

De voorwaarde voor opnieuw proberen is een PowerShell-expressie die wordt geëvalueerd na elke keer dat de activiteit wordt uitgevoerd. Als de expressie wordt opgelost naar True, wordt de activiteit opnieuw uitgevoerd. Als de expressie wordt opgelost naar False, wordt de activiteit niet opnieuw uitgevoerd en gaat het runbook verder met de volgende activiteit.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Schermopname van het veld Opnieuw proberen totdat deze voorwaarde waar is en voorbeelden van PowerShell-expressies die kunnen worden gebruikt in de voorwaarde voor opnieuw proberen.":::

De voorwaarde voor opnieuw proberen kan een variabele met de naam `RetryData` gebruiken die toegang biedt tot informatie over de nieuwe activiteiten. Deze variabele heeft de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| `NumberOfAttempts` |Het aantal keren dat de activiteit is uitgevoerd. |
| `Output` |Uitvoer van de laatste uitvoer van de activiteit. |
| `TotalDuration` |De tijd is verstreken sinds de activiteit de eerste keer is gestart. |
| `StartedAt` |Tijd (in UTC-indeling) waarop de activiteit voor het eerst is gestart. |

Hier volgen enkele voorbeelden van voorwaarden voor het opnieuw proberen van activiteiten.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Nadat u een voorwaarde voor opnieuw proberen voor een activiteit hebt geconfigureerd, bevat de activiteit twee visuele aanwijzingen om u eraan te herinneren. De ene wordt weergegeven in de activiteit en de andere wordt weergegeven wanneer u de configuratie van de activiteit controleert.

![Visuele indicatoren voor opnieuw proberen van activiteit](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Besturingselement voor werkstroomscript

Een werkstroomscriptbesturingselement is een speciale activiteit die PowerShell- of PowerShell Workflow-script accepteert, afhankelijk van het type grafisch runbook dat wordt gemaakt. Dit besturingselement biedt functionaliteit die mogelijk niet op een andere manier beschikbaar is. Er kunnen geen parameters worden geaccepteerd, maar er kunnen variabelen worden gebruikt voor uitvoer van activiteiten en invoerparameters voor runbook. Uitvoer van de activiteit wordt toegevoegd aan de databus. Een uitzondering is uitvoer zonder uitgaande koppeling. In dat geval wordt de uitvoer toegevoegd aan de uitvoer van het runbook.

Met de volgende code worden bijvoorbeeld datumberekeningen uitgevoerd met behulp van een runbookinvoervariabele met de naam `NumberOfDays` . Vervolgens wordt een berekende Datum/tijd-waarde als uitvoer verzendt die door volgende activiteiten in het runbook moet worden gebruikt.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Koppelingen gebruiken voor werkstroom

Een koppeling in een grafisch runbook verbindt twee activiteiten. Deze wordt op het canvas weergegeven als een pijl die van de bronactiviteit naar de doelactiviteit wijst. De activiteiten worden uitgevoerd in de richting van de pijl, met de doelactiviteit die begint nadat de bronactiviteit is voltooid.

### <a name="create-a-link"></a>Een koppeling maken

U kunt een koppeling tussen twee activiteiten maken door de bronactiviteit te selecteren en op de cirkel onderaan de vorm te klikken. Sleep de pijl naar de doelactiviteit en release.

![Een koppeling maken](media/automation-graphical-authoring-intro/create-link-options.png)

Selecteer de koppeling om de eigenschappen ervan te configureren op de blade Configuratie. Eigenschappen zijn onder andere het koppelingstype, dat wordt beschreven in de volgende tabel.

| Koppelingstype | Description |
|:--- |:--- |
| Pijplijn |De doelactiviteit wordt eenmaal uitgevoerd voor elke objectuitvoer van de bronactiviteit. De doelactiviteit wordt niet uitgevoerd als de bronactiviteit resulteert in geen uitvoer. De uitvoer van de bronactiviteit is beschikbaar als een -object. |
| Reeks |De doelactiviteit wordt slechts één keer uitgevoerd wanneer deze uitvoer van de bronactiviteit ontvangt. De uitvoer van de bronactiviteit is beschikbaar als een matrix met objecten. |

### <a name="start-runbook-activity"></a>Runbookactiviteit starten

Een grafisch runbook begint met alle activiteiten die geen binnenkomende koppeling hebben. Er is vaak slechts één activiteit die fungeert als de beginactiviteit voor het runbook. Als meerdere activiteiten geen binnenkomende koppeling hebben, wordt het runbook gestart door ze parallel uit te voeren. Deze volgt de koppelingen om andere activiteiten uit te voeren wanneer ze zijn voltooid.

### <a name="specify-link-conditions"></a>Koppelingsvoorwaarden opgeven

Wanneer u een voorwaarde voor een koppeling opgeeft, wordt de doelactiviteit alleen uitgevoerd als de voorwaarde wordt opgelost in True. Doorgaans gebruikt u een variabele `ActivityOutput` in een voorwaarde om de uitvoer van de bronactiviteit op te halen.

Voor een pijplijnkoppeling moet u een voorwaarde opgeven voor één object. Het runbook evalueert de voorwaarde voor elk objectuitvoer door de bronactiviteit. Vervolgens wordt de doelactiviteit uitgevoerd voor elk object dat aan de voorwaarde voldoet. Met een bronactiviteit van kunt u bijvoorbeeld de volgende syntaxis voor een voorwaardelijke pijplijnkoppeling gebruiken om alleen virtuele machines in de resourcegroep met de naam `Get-AzVM` Group1 op te halen.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Voor een reekskoppeling evalueert het runbook de voorwaarde slechts één keer, omdat één matrix met alle objecten uit de bronactiviteit wordt geretourneerd. Daarom kan het runbook geen volgkoppeling gebruiken om te filteren, zoals bij een pijplijnkoppeling. Met de reekskoppeling kunt u eenvoudig bepalen of de volgende activiteit wordt uitgevoerd.

Neem bijvoorbeeld de volgende reeks activiteiten in het runbook **VM** starten:

![Voorwaardelijke koppeling met reeksen](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Het runbook gebruikt drie verschillende reekskoppelingen die waarden van de invoerparameters controleren en bepalen welke `VMName` `ResourceGroupName` actie moet worden ondernomen. Mogelijke acties zijn het starten van één VM, het starten van alle VM's in de resourcegroep of het starten van alle VM's in een abonnement. Voor de reekskoppeling tussen `Connect to Azure` en is dit de `Get single VM` voorwaardelogica:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Wanneer u een voorwaardelijke koppeling gebruikt, worden de gegevens die beschikbaar zijn via de bronactiviteit naar andere activiteiten in die vertakking gefilterd op de voorwaarde. Als een activiteit de bron is naar meerdere koppelingen, zijn de gegevens die beschikbaar zijn voor activiteiten in elke vertakking afhankelijk van de voorwaarde in de koppeling die verbinding maakt met die vertakking.

Met de activiteit `Start-AzVM` in het onderstaande runbook worden bijvoorbeeld alle virtuele machines gestart. Het bevat twee voorwaardelijke koppelingen. De eerste voorwaardelijke koppeling gebruikt de expressie `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` om te filteren als de activiteit is `Start-AzVM` voltooid. De tweede voorwaardelijke koppeling gebruikt de expressie `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` om te filteren als de activiteit de virtuele machine niet kan `Start-AzVm` starten.

![Voorbeeld van voorwaardelijke koppeling](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Elke activiteit die de eerste koppeling volgt en de activiteitsuitvoer van gebruikt, haalt alleen de virtuele machines op die zijn gestart op het moment `Get-AzureVM` dat `Get-AzureVM` deze werd uitgevoerd. Elke activiteit die volgt op de tweede koppeling, haalt alleen de virtuele machines op die zijn gestopt op het moment dat `Get-AzureVM` deze werd uitgevoerd. Elke activiteit die volgt op de derde koppeling, haalt alle virtuele machines op, ongeacht hun status.

### <a name="use-junctions"></a>Verbindingspunten gebruiken

Een verbinding is een speciale activiteit die wacht totdat alle binnenkomende vertakkingen zijn voltooid. Hierdoor kan het runbook meerdere activiteiten parallel uitvoeren en ervoor zorgen dat alle activiteiten zijn voltooid voordat u verder gaat.

Hoewel een koppeling een onbeperkt aantal binnenkomende koppelingen kan hebben, kan slechts één van deze koppelingen een pijplijn zijn. Het aantal binnenkomende reekskoppelingen is niet beperkt. U kunt de koppeling maken met meerdere binnenkomende pijplijnkoppelingen en het runbook opslaan, maar dit mislukt wanneer het wordt uitgevoerd.

Het onderstaande voorbeeld maakt deel uit van een runbook dat een set virtuele machines start terwijl tegelijkertijd patches worden gedownload die op deze machines moeten worden toegepast. Er wordt een verbinding gebruikt om ervoor te zorgen dat beide processen worden voltooid voordat het runbook wordt voortgezet.

![Verbinding](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Werken met cycli

Er wordt een cyclus gevormd wanneer een doelactiviteit wordt terugkoppelingen naar de bronactiviteit of naar een andere activiteit die uiteindelijk terug koppelt aan de bron. Grafische ontwerp biedt momenteel geen ondersteuning voor cycli. Als uw runbook een cyclus heeft, wordt er een foutbericht weergegeven wanneer het wordt uitgevoerd.

![Cyclus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Gegevens delen tussen activiteiten

Alle gegevens die door een activiteit worden uitgevoerd met een uitgaande koppeling, worden naar de databus voor het runbook geschreven. Elke activiteit in het runbook kan gegevens op de databus gebruiken om parameterwaarden in te vullen of op te nemen in scriptcode. Een activiteit heeft toegang tot de uitvoer van elke vorige activiteit in de werkstroom.

Hoe de gegevens naar de databus worden geschreven, is afhankelijk van het type koppeling van de activiteit. Voor een pijplijnkoppeling worden de gegevens uitgevoerd als meerdere objecten. Voor een reekskoppeling worden de gegevens uitgevoerd als een matrix. Als er slechts één waarde is, wordt deze uitgevoerd als een matrix met één element.

Uw runbook heeft twee manieren om toegang te krijgen tot gegevens op de databus: 
* Gebruik een gegevensbron voor activiteituitvoer.
* Gebruik een powershell-expressiegegevensbron.

Het eerste mechanisme maakt gebruik van een gegevensbron voor activiteituitvoer om een parameter van een andere activiteit in te vullen. Als de uitvoer een object is, kan het runbook één eigenschap opgeven.

![activiteitsuitvoer](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Het tweede mechanisme voor gegevenstoegang haalt de uitvoer op van een activiteit in een powershell-expressiegegevensbron of een werkstroomscriptactiviteit met een variabele, met behulp van de `ActivityOutput` syntaxis die hieronder wordt weergegeven. Als de uitvoer een object is, kan uw runbook één eigenschap opgeven.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Controlepunten gebruiken

U kunt [controlepunten instellen](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) in een grafisch PowerShell Workflow-runbook door **controlepuntrunbook te selecteren** voor elke activiteit. Hierdoor wordt een controlepunt ingesteld nadat de activiteit is uitgevoerd.

![Controlepunt](media/automation-graphical-authoring-intro/set-checkpoint.png)

Controlepunten zijn alleen ingeschakeld in grafische PowerShell Workflow-runbooks en zijn niet beschikbaar in grafische runbooks. Als het runbook Azure-cmdlets gebruikt, moet het elke activiteit met controlepunten met een activiteit `Connect-AzAccount` volgen. De verbindingsbewerking wordt gebruikt voor het geval het runbook is opgeschort en opnieuw moet worden opgestart vanaf dit controlepunt op een andere werker.

## <a name="handle-runbook-input"></a>Runbookinvoer verwerken

Een runbook vereist invoer van een gebruiker die het runbook start via de Azure Portal of van een ander runbook, als het huidige runbook wordt gebruikt als onderliggend runbook. Voor een runbook dat bijvoorbeeld een virtuele machine maakt, moet de gebruiker mogelijk gegevens zoals de naam van de virtuele machine en andere eigenschappen verstrekken telkens wanneer het runbook wordt gestart.

Het runbook accepteert invoer door een of meer invoerparameters te definiëren. Telkens als het runbook wordt gestart, geeft de gebruiker waarden voor deze parameters op. Wanneer de gebruiker het runbook start met behulp van de Azure Portal, wordt de gebruiker gevraagd waarden op te geven voor elke invoerparameter die door het runbook wordt ondersteund.

Wanneer u het runbook ontwerpt, hebt u toegang tot de invoerparameters door te klikken op **Invoer en uitvoer** op de werkbalk van het runbook. Hiermee opent u het besturingselement Invoer en uitvoer, waar u een bestaande invoerparameter kunt bewerken of een nieuwe kunt maken door op **Invoer toevoegen te klikken.**

![Invoer toevoegen](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Elke invoerparameter wordt gedefinieerd door de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Name | Vereist. De naam van de parameter. De naam moet uniek zijn binnen het runbook. De naam moet beginnen met een letter en mag alleen letters, cijfers en onderstrepingstekens bevatten. De naam mag geen spatie bevatten. |
| Beschrijving |Optioneel. Beschrijving van het doel voor de invoerparameter. |
| Type | Optioneel. Gegevenstype dat wordt verwacht voor de parameterwaarde. De Azure Portal een geschikt besturingselement voor het gegevenstype voor elke parameter wanneer u om invoer wordt gevraagd. Ondersteunde parametertypen zijn String, Int32, Int64, Decimal, Boolean, DateTime en Object. Als er geen gegevenstype is geselecteerd, wordt dit standaard ingesteld op Tekenreeks.|
| Verplicht | Optioneel. Instelling die aangeeft of er een waarde moet worden opgegeven voor de parameter . Als u `yes` kiest, moet er een waarde worden opgegeven wanneer het runbook wordt gestart. Als u kiest, is er geen waarde vereist wanneer het runbook wordt gestart en kan een `no` standaardwaarde worden gebruikt. Het runbook kan niet worden starten als u geen waarde opgeeft voor elke verplichte parameter die geen standaardwaarde heeft gedefinieerd. |
| Standaardwaarde | Optioneel. De waarde die wordt gebruikt voor een parameter als er geen wordt doorgegeven wanneer het runbook wordt gestart. Kies om een standaardwaarde in te `Custom` stellen. Selecteer `None` als u geen standaardwaarde wilt verstrekken. |

## <a name="handle-runbook-output"></a>Runbookuitvoer verwerken

Grafisch ontwerp slaat gegevens op die zijn gemaakt door activiteiten die geen uitgaande koppeling naar de uitvoer van [het runbook hebben.](./automation-runbook-output-and-messages.md) De uitvoer wordt opgeslagen met de runbook-taak en is beschikbaar voor een bovenliggend runbook wanneer het runbook wordt gebruikt als onderliggend runbook.

## <a name="work-with-powershell-expressions"></a>Werken met PowerShell-expressies

Een van de voordelen van grafisch ontwerp is dat u hiermee een runbook kunt bouwen met minimale kennis van PowerShell. Op dit moment moet u echter wel wat van PowerShell weten om bepaalde [parameterwaarden](#use-activities) in te vullen en om koppelingsvoorwaarden [in te stellen.](#use-links-for-workflow) Deze sectie bevat een korte inleiding tot PowerShell-expressies. Volledige details van PowerShell zijn beschikbaar op [Scripting with Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Een PowerShell-expressie gebruiken als gegevensbron

U kunt een PowerShell-expressie als gegevensbron gebruiken om de waarde van een activiteitsparameter te vullen [met](#use-activities) de resultaten van PowerShell-code. De expressie kan één regel code zijn die een eenvoudige functie of meerdere regels uitvoert die complexe logica uitvoeren. Uitvoer van een opdracht die niet is toegewezen aan een variabele wordt uitgevoerd naar de parameterwaarde.

Met de volgende opdracht wordt bijvoorbeeld de huidige datum uitgevoerd.

```powershell-interactive
Get-Date
```

Het volgende codefragment bouwt een tekenreeks van de huidige datum en wijst deze toe aan een variabele. De code verzendt de inhoud van de variabele naar de uitvoer.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

De volgende opdrachten evalueren de huidige datum en retourneren een tekenreeks die aangeeft of de huidige dag een weekend of een doordeweekse dag is.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Activiteitsuitvoer gebruiken

Als u de uitvoer van een vorige activiteit in uw runbook wilt gebruiken, gebruikt u de `ActivityOutput` variabele met de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

U kunt bijvoorbeeld een activiteit hebben met een eigenschap die de naam van een virtuele machine vereist. In dit geval kan uw runbook de volgende expressie gebruiken.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Als voor de eigenschap het virtuele-machineobject is vereist in plaats van alleen een naam, retourneert het runbook het hele object met behulp van de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Het runbook kan de uitvoer van een activiteit gebruiken in een complexere expressie, zoals de volgende. Met deze expressie wordt tekst aan de naam van de virtuele machine samenvoegd.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Waarden vergelijken

Gebruik [vergelijkingsoperators](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) om waarden te vergelijken of om te bepalen of een waarde overeenkomt met een opgegeven patroon. Een vergelijking retourneert een waarde van Waar of Onwaar.

De volgende voorwaarde bepaalt bijvoorbeeld of de virtuele machine van een activiteit met de naam `Get-AzureVM` momenteel is gestopt.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped"
```

De volgende voorwaarde bepaalt of dezelfde virtuele machine een andere status heeft dan gestopt.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -ne "Stopped"
```

U kunt meerdere voorwaarden aan uw runbook deelnemen met behulp van een [logische operator,](/powershell/module/microsoft.powershell.core/about/about_logical_operators) `-and` zoals of `-or` . Met de volgende voorwaarde wordt bijvoorbeeld gecontroleerd of de virtuele machine in het vorige voorbeeld de status Gestopt of Gestopt heeft.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopping")
```

### <a name="use-hashtables"></a>Hashtabels gebruiken

[Hashtabels](/powershell/module/microsoft.powershell.core/about/about_hash_tables) zijn naam-waardeparen die handig zijn voor het retourneren van een set waarden. Mogelijk ziet u ook een hashtabel die wordt aangeduid als een woordenlijst. Eigenschappen voor bepaalde activiteiten verwachten een hashtabel in plaats van een eenvoudige waarde.

Maak een hashtabel met behulp van de volgende syntaxis. Het kan een groot aantal vermeldingen bevatten, maar elke vermeldingen worden gedefinieerd door een naam en waarde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Met de volgende expressie wordt bijvoorbeeld een hashtabel gemaakt die moet worden gebruikt als de gegevensbron voor een activiteitsparameter die een hashtabel met waarden verwacht voor een zoekopdracht op internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

In het volgende voorbeeld wordt uitvoer van een activiteit met de naam `Get Twitter Connection` gebruikt om een hashtabel te vullen.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Verifiëren bij Azure-resources

Voor runbooks in Azure Automation die Azure-resources beheren, is verificatie bij Azure vereist. Het [Uitvoeren als-account,](./automation-security-overview.md)ook wel een service-principal genoemd, is het standaardmechanisme dat een Automation-runbook gebruikt om toegang te krijgen tot Azure Resource Manager resources in uw abonnement. U kunt deze functionaliteit toevoegen aan een grafisch runbook door de verbindingsactiva, die gebruikmaakt van de `AzureRunAsConnection` PowerShell [Get-AutomationConnection-cmdlet,](/system-center/sma/manage-global-assets) toe te voegen aan het canvas. U kunt ook de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) toevoegen. Dit scenario wordt geïllustreerd in het volgende voorbeeld.

![Uitvoeren als-verificatieactiviteiten](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

De `Get Run As Connection` activiteit, of `Get-AutomationConnection` , is geconfigureerd met een constante waardegegevensbron met de naam `AzureRunAsConnection` .

![Configuratie van Run As-verbinding](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Met de volgende activiteit, `Connect-AzAccount` , wordt het geverifieerde Uitvoeren als-account toegevoegd voor gebruik in het runbook.

![Connect-AzAccount parameterset](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook kan alleen zichzelf `Connect-AzAccount` gebruiken.

Geef voor de parametervelden **APPLICATIONID,** **CERTIFICATETHUMBPRINT** en **TENANTID** de naam op van de eigenschap voor het veldpad, omdat de activiteit een object met meerdere eigenschappen als uitvoer heeft. Anders mislukt het uitvoeren van het runbook tijdens het verifiëren. Dit is wat u minimaal nodig hebt om uw runbook te verifiëren met het Uitvoeren als-account.

Sommige abonnees maken een Automation-account met behulp van [een Azure AD-gebruikersaccount](./shared-resources/credentials.md) voor het beheren van klassieke Azure-implementaties of Azure Resource Manager resources. Om achterwaartse compatibiliteit voor deze abonnees te behouden, is het verificatiemechanisme dat in uw runbook moet worden gebruikt, de `Add-AzureAccount` cmdlet met een [referentie-asset](./shared-resources/credentials.md). De asset vertegenwoordigt een Active Directory-gebruiker met toegang tot het Azure-account.

U kunt deze functionaliteit voor uw grafische runbook inschakelen door een referentie-asset toe te voegen aan het canvas, gevolgd door een activiteit die gebruikmaakt van de `Add-AzureAccount` referentie-asset voor de invoer. Zie het volgende voorbeeld

![Verificatieactiviteiten](media/automation-graphical-authoring-intro/authentication-activities.png)

Het runbook moet worden geverifieerd aan het begin en na elk controlepunt. Daarom moet u na `Add-AzureAccount` elke activiteit een activiteit `Checkpoint-Workflow` gebruiken. U hoeft geen aanvullende referentieactiviteit te gebruiken.

![Activiteitsuitvoer](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Een grafisch runbook exporteren

U kunt alleen de gepubliceerde versie van een grafisch runbook exporteren. Als het runbook nog niet is gepubliceerd, wordt de **knop Exporteren** uitgeschakeld. Wanneer u op de **knop Exporteren** klikt, wordt het runbook gedownload naar uw lokale computer. De naam van het bestand komt overeen met de naam van het runbook met de **extensie .graphrunbook.**

## <a name="import-a-graphical-runbook"></a>Een grafisch runbook importeren

U kunt een grafisch of grafisch PowerShell Workflow-runbookbestand importeren door de **optie Importeren te** selecteren wanneer u een runbook toevoegt. Wanneer u het te importeren bestand selecteert, kunt u dezelfde naam behouden of een nieuwe invoeren. In **het veld Runbooktype** wordt het type runbook weergegeven nadat het geselecteerde bestand is beoordeeld. Als u probeert een ander type te selecteren dat niet juist is, wordt in de grafische editor een bericht weergegeven met de melding dat er mogelijke conflicten zijn en dat er mogelijk syntaxisfouten optreden tijdens de conversie.

![Runbook importeren](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Een grafisch runbook testen

Elk grafisch runbook in Azure Automation heeft een conceptversie en een gepubliceerde versie. U kunt alleen de gepubliceerde versie uitvoeren, terwijl u alleen de conceptversie kunt bewerken. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de Concept-versie gereed is voor gebruik, publiceert u deze, waardoor de huidige gepubliceerde versie wordt overschreven met uw conceptversie.

U kunt de conceptversie van een runbook testen in de Azure Portal terwijl de gepubliceerde versie ongewijzigd blijft. U kunt ook een nieuw runbook testen voordat het is gepubliceerd, zodat u kunt controleren of het runbook correct werkt voordat er versievervangingen worden uitgevoerd. Bij het testen van een runbook wordt de Concept-versie uitgevoerd en worden alle acties voltooid die worden uitgevoerd. Er wordt geen taakgeschiedenis gemaakt, maar in het deelvenster Testuitvoer wordt de uitvoer weergegeven.

Open het besturingselement Testen voor uw grafische runbook door het runbook te openen voor bewerken en vervolgens op **Testvenster te klikken.** Het besturingselement Testen vraagt om invoerparameters en u kunt het runbook starten door op **Start te klikken.**

## <a name="publish-a-graphical-runbook"></a>Een grafisch runbook publiceren

Publiceer een grafisch runbook door het runbook te openen voor bewerking en klik vervolgens op **Publiceren.** Mogelijke statussen voor het runbook zijn:

* Nieuw: het runbook is nog niet gepubliceerd. 
* Gepubliceerd: het runbook is gepubliceerd.
* In bewerking: het runbook is bewerkt nadat het is gepubliceerd en de concept- en gepubliceerde versies verschillen.

![Runbookstatussen](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

U hebt de mogelijkheid om terug te keren naar de gepubliceerde versie van een runbook. Met deze bewerking worden alle wijzigingen verwijderd die zijn aangebracht sinds het runbook voor het laatst is gepubliceerd. De conceptversie van het runbook wordt vervangen door de gepubliceerde versie.

## <a name="next-steps"></a>Volgende stappen

* Zie Zelfstudie: Een grafisch runbook maken om aan de slag te gaan [met grafische runbooks.](learn/automation-tutorial-runbook-graphical.md)
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen en hun voordelen en beperkingen.
* Zie Uitvoeren als-account voor meer inzicht in het verifiëren met behulp van het [Uitvoeren als-account van Automation.](automation-security-overview.md#run-as-account)
* Zie [Az.Automation](/powershell/module/az.automation/#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
