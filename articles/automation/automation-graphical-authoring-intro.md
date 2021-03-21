---
title: Grafische runbooks ontwerpen in Azure Automation
description: In dit artikel leest u hoe u een grafisch runbook kunt ontwerpen zonder met code te werken.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: bbac794263fec176e03c7148d860c479a2ed9d39
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501225"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Grafische runbooks ontwerpen in Azure Automation

Alle runbooks in Azure Automation zijn Windows Power shell-werk stromen. Grafische runbooks en grafisch Power shell-werk stroom-runbooks genereren Power shell-code die de automatiserings werk rollen uitvoeren, maar die u niet kunt weer geven of wijzigen. U kunt een grafisch runbook converteren naar een grafisch Power shell-werk stroom-runbook en andersom. U kunt deze runbooks echter niet converteren naar een tekst runbook. Daarnaast kan de grafische editor van Automation geen tekst runbook importeren.

Met grafische ontwerpen kunt u runbooks voor Azure Automation maken zonder de complexiteit van de onderliggende Windows Power shell-of Power shell-werk stroom code. U kunt activiteiten toevoegen aan het canvas vanuit een bibliotheek met cmdlets en runbooks, ze samen koppelen en configureren om een werk stroom te vormen. Als u ooit met System Center Orchestrator of Service Management Automation (SMA) hebt gewerkt, zou grafische ontwerping er vertrouwd moeten uitzien. Dit artikel bevat een inleiding tot de concepten die u nodig hebt om aan de slag te gaan met het maken van een grafisch runbook.

## <a name="overview-of-graphical-editor"></a>Overzicht van grafische editor

U kunt de grafische editor openen in de Azure Portal door een grafisch runbook te maken of te bewerken.

![Grafische werk ruimte](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

In de volgende secties worden de besturings elementen in de grafische editor beschreven.

### <a name="canvas-control"></a>Besturings element voor papier

Met het besturings element canvas kunt u uw runbook ontwerpen. U kunt activiteiten van de knoop punten in het besturings element bibliotheek toevoegen aan het runbook en ze verbinden met koppelingen om de runbook-logica te definiëren. Aan de onderkant van het canvas staan besturings elementen waarmee u kunt in-en uitzoomen.

### <a name="library-control"></a>Besturings element voor bibliotheek

Met het besturings element bibliotheek kunt u [activiteiten](#use-activities) selecteren om aan uw runbook toe te voegen. U voegt deze toe aan het canvas, waar u ze kunt verbinden met andere activiteiten. Het besturings element bibliotheek bevat de secties die in de volgende tabel zijn gedefinieerd.

| Sectie | Beschrijving |
|:--- |:--- |
| Cmdlets |Alle cmdlets die kunnen worden gebruikt in uw runbook. Cmdlets zijn ingedeeld op module. Alle modules die u hebt geïnstalleerd in uw Automation-account zijn beschikbaar. |
| Runbooks |De runbooks in uw Automation-account. U kunt deze runbooks toevoegen aan het canvas dat moet worden gebruikt als onderliggende runbooks. Alleen runbooks van hetzelfde kern type worden weer gegeven als het runbook dat wordt bewerkt. Voor grafische runbooks worden alleen op Power shell gebaseerde runbooks weer gegeven. Voor grafische runbooks in Power shell-werk stromen worden alleen runbooks op basis van Power shell-werk stromen weer gegeven. |
| Assets |De [Automation-assets](/previous-versions/azure/dn939988(v=azure.100)) in uw Automation-account dat u in uw runbook kunt gebruiken. Als u een activum toevoegt aan een runbook, wordt er een werk stroom activiteit toegevoegd waarmee de geselecteerde activa worden opgehaald. In het geval van variabele assets kunt u selecteren of u een activiteit wilt toevoegen om de variabele op te halen of de variabele in te stellen. |
| Runbookbesturing |Beheer activiteiten die kunnen worden gebruikt in uw huidige runbook. Een verbindings activiteit heeft meerdere invoer en wacht totdat alle taken zijn voltooid voordat u doorgaat met de werk stroom. Een code activiteit voert een of meer regels Power shell-of Power shell-werk stroom code uit, afhankelijk van het type van het grafische runbook. U kunt deze activiteit gebruiken voor aangepaste code of voor functionaliteit die moeilijk te vergemakkelijkt is met andere activiteiten. |

### <a name="configuration-control"></a>Configuratie beheer

Met configuratie beheer kunt u details opgeven voor een object dat is geselecteerd op het canvas. De eigenschappen die beschikbaar zijn in dit besturings element, zijn afhankelijk van het type object dat is geselecteerd. Wanneer u een optie kiest in het configuratie besturings element, worden er aanvullende Blades geopend om meer informatie te geven.

### <a name="test-control"></a>Besturings element testen

Het besturings element test wordt niet weer gegeven wanneer de grafische editor voor het eerst wordt gestart. Het wordt geopend wanneer u interactief een grafisch runbook test.

## <a name="use-activities"></a>Activiteiten gebruiken

Activiteiten zijn de bouw stenen van een runbook. Een activiteit kan een Power shell-cmdlet, een onderliggend runbook of een werk stroom zijn. U kunt een activiteit aan het runbook toevoegen door er met de rechter muisknop op te klikken in het besturings element bibliotheek en vervolgens **toevoegen aan canvas** te selecteren. Vervolgens klikt u op de activiteit en sleept u deze naar een wille keurige plek op het canvas dat u wilt. De locatie van de activiteit op het canvas heeft geen invloed op de werking van het runbook. U kunt uw runbook indelen op elke manier die u het meest geschikt vindt om de werking ervan te visualiseren.

![Toevoegen aan canvas](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selecteer een activiteit op het canvas om de eigenschappen en para meters te configureren op de Blade configuratie. U kunt het label van de activiteit wijzigen in een naam die u beschrijft. De oorspronkelijke cmdlet wordt nog steeds uitgevoerd met het runbook. U hoeft alleen de weergave naam te wijzigen die door de grafische editor wordt gebruikt. Houd er rekening mee dat het label uniek moet zijn binnen het runbook.

### <a name="parameter-sets"></a>Parameter sets

Een parameterset definieert de verplichte en optionele para meters waarmee waarden voor een bepaalde cmdlet worden geaccepteerd. Alle cmdlets hebben ten minste één parameterset en sommige hebben verschillende sets. Als een cmdlet meerdere parameter sets heeft, moet u het abonnement selecteren dat u wilt gebruiken voordat u para meters kunt configureren. U kunt de parameterset die door een activiteit wordt gebruikt, wijzigen door **para meters** in te stellen en een andere set te kiezen. In dit geval gaan alle parameter waarden die u al hebt geconfigureerd verloren.

In het volgende voor beeld heeft de cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) drie parameter sets. In het voor beeld wordt één set met de naam **ListVirtualMachineInResourceGroupParamSet**, met één optionele para meter, gebruikt voor het retour neren van alle virtuele machines in een resource groep. In het voor beeld wordt ook de para meter **GetVirtualMachineInResourceGroupParamSet** gebruikt voor het opgeven van de virtuele machine die moet worden geretourneerd. Deze set heeft twee verplichte para meters en een optionele para meter.

![Parameterset](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parameterwaarden

Wanneer u een waarde voor een para meter opgeeft, selecteert u een gegevens bron om te bepalen hoe de waarde is opgegeven. De gegevens bronnen die beschikbaar zijn voor een bepaalde para meter, zijn afhankelijk van de geldige waarden voor die para meter. Zo is null bijvoorbeeld geen beschik bare optie voor een para meter die geen Null-waarden toestaat.

| Gegevensbron | Beschrijving |
|:--- |:--- |
| Constante waarde |Typ een waarde voor de para meter. Deze gegevens bron is alleen beschikbaar voor de volgende gegevens typen: Int32, Int64, String, Boolean, DateTime, Switch. |
| Uitvoer van activiteit |Gebruik uitvoer van een activiteit die voorafgaat aan de huidige activiteit in de werk stroom. Alle geldige activiteiten worden weer gegeven. Gebruik voor de parameter waarde alleen de activiteit die de uitvoer produceert. Als met de activiteit een object met meerdere eigenschappen wordt uitgevoerd, kunt u de naam van een specifieke eigenschap typen nadat u de activiteit hebt geselecteerd. |
| Runbook-invoer |Selecteer een invoer van het runbook als invoer voor de para meter activiteit. |
| Variabel activum |Selecteer een Automation-variabele als invoer. |
| Referentie-Asset |Selecteer een Automation-referentie als invoer. |
| Certificaat Asset |Selecteer een Automation-certificaat als invoer. |
| Verbindings Asset |Selecteer een Automation-verbinding als invoer. |
| PowerShell-expressie |Geef een eenvoudige [Power shell-expressie](#work-with-powershell-expressions)op. De expressie wordt geëvalueerd voordat de activiteit wordt uitgevoerd en het resultaat wordt gebruikt voor de parameter waarde. U kunt variabelen gebruiken om te verwijzen naar de uitvoer van een activiteit of een invoer parameter van een runbook. |
| Niet geconfigureerd |Wis alle eerder geconfigureerde waarden. |

#### <a name="optional-additional-parameters"></a>Optionele aanvullende para meters

Alle cmdlets hebben de mogelijkheid om extra para meters op te geven. Dit zijn Power shell-algemene para meters of andere aangepaste para meters. De grafische editor geeft een tekstvak waarin u para meters kunt opgeven met behulp van de Power shell-syntaxis. Als u bijvoorbeeld de `Verbose` algemene para meter wilt gebruiken, moet u opgeven `-Verbose:$True` .

### <a name="retry-activity"></a>Activiteit voor opnieuw proberen

Met de functionaliteit voor opnieuw proberen voor een activiteit kan deze meerdere keren worden uitgevoerd tot aan een bepaalde voor waarde wordt voldaan, vergelijkbaar met een lus. U kunt deze functie gebruiken voor activiteiten die meerdere keren worden uitgevoerd, fout gevoelig is. mogelijk hebt u meer dan één poging om te slagen of de uitvoer gegevens van de activiteit testen op geldige gegevens.

Wanneer u opnieuw proberen voor een activiteit inschakelt, kunt u een vertraging en een voor waarde instellen. De vertraging is de tijd (gemeten in seconden of minuten) die het runbook wacht voordat de activiteit opnieuw wordt uitgevoerd. Als u geen vertraging opgeeft, wordt de activiteit opnieuw uitgevoerd direct nadat deze is voltooid.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Scherm afbeelding van de functie-instellingen voor opnieuw proberen in te scha kelen.":::

De voor waarde voor opnieuw proberen is een Power shell-expressie die wordt geëvalueerd na elke keer dat de activiteit wordt uitgevoerd. Als de expressie wordt omgezet in ' True ', wordt de activiteit opnieuw uitgevoerd. Als de expressie wordt omgezet in False, wordt de activiteit niet opnieuw uitgevoerd en wordt het runbook verplaatst naar de volgende activiteit.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Scherm afbeelding met de nieuwe poging tot deze voor waarde is waar veld en voor beelden van Power shell-expressies die kunnen worden gebruikt in de voor waarde voor opnieuw proberen.":::

De voor waarde voor opnieuw proberen kan een variabele hebben met de naam `RetryData` die toegang geeft tot informatie over de nieuwe pogingen van de activiteit. Deze variabele heeft de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| `NumberOfAttempts` |Aantal keren dat de activiteit is uitgevoerd. |
| `Output` |Uitvoer van de laatste uitvoering van de activiteit. |
| `TotalDuration` |De tijd die is verstreken sinds de activiteit voor het eerst is gestart. |
| `StartedAt` |Tijd (in UTC-notatie) toen de activiteit voor het eerst werd gestart. |

Hier volgen enkele voor beelden van voor waarden voor een nieuwe activiteit.

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

Nadat u een voor waarde voor opnieuw proberen voor een activiteit hebt geconfigureerd, bevat de activiteit twee visuele hints om u eraan te herinneren. De ene wordt weer gegeven in de activiteit en de andere wordt weer geven wanneer u de configuratie van de activiteit bekijkt.

![Visuele indica toren voor activiteit opnieuw proberen](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Besturings element voor werk stroom script

Een besturings element werk stroom script is een speciale activiteit die Power shell-of Power shell-werk stroom script accepteert, afhankelijk van het type grafisch runbook dat wordt gemaakt. Dit besturings element biedt functionaliteit die mogelijk niet op een andere manier beschikbaar is. Het kan geen para meters accepteren, maar kan wel variabelen gebruiken voor uitvoer van de activiteit en de invoer parameters voor het runbook. Alle uitvoer van de activiteit wordt toegevoegd aan de gegevensbus. Er wordt een uitzonde ring uitgevoerd zonder uitgaande koppeling, in welk geval de uitvoer wordt toegevoegd aan de uitvoer van het runbook.

Met de volgende code worden bijvoorbeeld datums berekend met behulp van een runbook-invoer variabele met de naam `NumberOfDays` . Vervolgens wordt een berekende DateTime-waarde verzonden als uitvoer die door volgende activiteiten in het runbook moet worden gebruikt.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Koppelingen voor werk stroom gebruiken

Een koppeling in een grafisch runbook verbindt twee activiteiten. Het wordt op het canvas weer gegeven als een pijl die van de bron activiteit naar de doel activiteit wijst. De activiteiten worden uitgevoerd in de richting van de pijl met de doel activiteit vanaf het volt ooien van de bron activiteit.

### <a name="create-a-link"></a>Een koppeling maken

U kunt een koppeling tussen twee activiteiten maken door de bron activiteit te selecteren en op de cirkel aan de onderkant van de vorm te klikken. Sleep de pijl naar de doel activiteit en laat deze los.

![Een koppeling maken](media/automation-graphical-authoring-intro/create-link-options.png)

Selecteer de koppeling om de eigenschappen ervan te configureren in de Blade configuratie. Eigenschappen bevatten het koppelings type, dat wordt beschreven in de volgende tabel.

| Koppelings type | Beschrijving |
|:--- |:--- |
| Pijplijn |De doel activiteit wordt één keer uitgevoerd voor elke object uitvoer van de bron activiteit. De doel activiteit wordt niet uitgevoerd als de bron activiteit resulteert in geen uitvoer. Uitvoer van de bron activiteit is beschikbaar als een object. |
| Reeks |De doel activiteit wordt slechts eenmaal uitgevoerd wanneer de uitvoer van de bron activiteit wordt ontvangen. Uitvoer van de bron activiteit is beschikbaar als een matrix met objecten. |

### <a name="start-runbook-activity"></a>Runbook-activiteit starten

Een grafisch runbook begint met alle activiteiten die geen binnenkomende koppeling hebben. Er is vaak maar één activiteit die fungeert als de begin activiteit voor het runbook. Als meerdere activiteiten geen binnenkomende koppeling hebben, wordt het runbook gestart door deze parallel uit te voeren. De koppelingen worden gevolgd om andere activiteiten uit te voeren, aangezien elke bewerking is voltooid.

### <a name="specify-link-conditions"></a>Koppelings voorwaarden opgeven

Wanneer u een voor waarde op een koppeling opgeeft, wordt de doel activiteit alleen uitgevoerd als de voor waarde wordt omgezet in True. Normaal gesp roken gebruikt u een `ActivityOutput` variabele in een voor waarde om de uitvoer op te halen uit de bron activiteit.

Voor een pijplijn koppeling moet u een voor waarde voor een enkel object opgeven. Het runbook evalueert de voor waarde voor elke object uitvoer door de bron activiteit. Vervolgens wordt de doel activiteit uitgevoerd voor elk object dat voldoet aan de voor waarde. Met een bron activiteit van `Get-AzVM` kunt u bijvoorbeeld de volgende syntaxis voor een voorwaardelijke pijplijn koppeling gebruiken om alleen virtuele machines op te halen in de resource groep met de naam Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Voor een reeks koppeling evalueert het runbook de voor waarde alleen eenmaal, aangezien er een enkele matrix met alle objecten van de bron activiteit wordt geretourneerd. Als gevolg hiervan kan het runbook geen Volg koppeling gebruiken voor filteren, zoals het kan met een pijplijn koppeling. De volg orde van de koppeling kan eenvoudigweg bepalen of de volgende activiteit wordt uitgevoerd.

Neem bijvoorbeeld de volgende serie activiteiten in het runbook **VM starten** :

![Voorwaardelijke koppeling met reeksen](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Het runbook gebruikt drie verschillende volg orde-koppelingen die de waarden van de invoer parameters controleren `VMName` en `ResourceGroupName` om te bepalen welke actie moet worden ondernomen. Mogelijke acties worden gestart op één virtuele machine, alle virtuele machines in de resource groep starten of alle virtuele machines in een abonnement starten. Voor de reeks koppeling tussen `Connect to Azure` en `Get single VM` , hier volgt de voor waarde logica:

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

Wanneer u een voorwaardelijke koppeling gebruikt, worden de gegevens die beschikbaar zijn van de bron activiteit naar andere activiteiten in die vertakking gefilterd op basis van de voor waarde. Als een activiteit de bron voor meerdere koppelingen is, zijn de gegevens die beschikbaar zijn voor activiteiten in elke vertakking afhankelijk van de voor waarde in de koppeling verbinding maken met die vertakking.

`Start-AzVM`Met de activiteit in het onderstaande runbook worden bijvoorbeeld alle virtuele machines gestart. Het heeft twee voorwaardelijke koppelingen. De eerste voorwaardelijke koppeling gebruikt de expressie `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` om te filteren als de `Start-AzVM` activiteit is voltooid. De tweede voorwaardelijke koppeling gebruikt de expressie `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` om te filteren als de `Start-AzVm` virtuele machine niet kan worden gestart door de activiteit.

![Voor beeld van voorwaardelijke koppeling](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Elke activiteit die de eerste koppeling volgt en de uitvoer van de activiteit gebruikt, `Get-AzureVM` haalt alleen de virtuele machines op die zijn gestart op het moment waarop de `Get-AzureVM` bewerking werd uitgevoerd. Elke activiteit die volgt op de tweede koppeling, haalt alleen de virtuele machines op die zijn gestopt op het moment dat de `Get-AzureVM` bewerking werd uitgevoerd. Bij elke activiteit die volgt op de derde koppeling, worden alle virtuele machines opgehaald, ongeacht hun uitvoerings status.

### <a name="use-junctions"></a>Koppelingen gebruiken

Een verbinding is een speciale activiteit die wacht totdat alle binnenkomende branches zijn voltooid. Hierdoor kan het runbook meerdere activiteiten parallel uitvoeren en ervoor zorgen dat alle taken zijn voltooid voordat ze worden verplaatst.

Hoewel een verbinding een onbeperkt aantal binnenkomende koppelingen kan hebben, kan slechts één van deze koppelingen een pijp lijn zijn. Het aantal binnenkomende reeks koppelingen is niet beperkt. U kunt de verbinding met meerdere inkomende pijplijn koppelingen maken en het runbook opslaan, maar het kan mislukken wanneer het wordt uitgevoerd.

Het onderstaande voor beeld maakt deel uit van een runbook dat een set virtuele machines start terwijl er tegelijkertijd patches worden gedownload die op deze computers moeten worden toegepast. Er wordt een verbinding gebruikt om ervoor te zorgen dat beide processen zijn voltooid voordat het runbook wordt voortgezet.

![Verbinding](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Werken met cycli

Er wordt een cyclus gevormd wanneer een doel activiteit een koppeling maakt naar de bron activiteit of een andere activiteit die uiteindelijk wordt gekoppeld aan de bron. Grafisch ontwerpen biedt momenteel geen ondersteuning voor cycli. Als uw runbook een cyclus heeft, wordt het programma op de juiste manier opgeslagen, maar ontvangt deze een fout melding wanneer deze wordt uitgevoerd.

![Cyclus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Gegevens delen tussen activiteiten

Alle gegevens die een activiteit uitvoert met een uitgaande koppeling, worden geschreven naar de gegevensbus voor het runbook. Elke activiteit in het runbook kan gegevens op de gegevensbus gebruiken om parameter waarden te vullen of in script code op te neemt. Een activiteit kan toegang krijgen tot de uitvoer van een vorige activiteit in de werk stroom.

Hoe de gegevens worden geschreven naar de gegevensbus, is afhankelijk van het type koppeling op de activiteit. Voor een pijplijn koppeling worden de gegevens als meerdere objecten uitgevoerd. Voor een sequentie koppeling worden de gegevens uitgevoerd als een matrix. Als er slechts één waarde is, wordt deze uitgevoerd als een matrix met één element.

Uw runbook heeft twee manieren om toegang te krijgen tot gegevens op de gegevensbus: 
* Een gegevens bron voor de uitvoer van een activiteit gebruiken.
* Een Power shell-expressie gegevens bron gebruiken.

Het eerste mechanisme gebruikt een gegevens bron voor de uitvoer van de activiteit om een para meter van een andere activiteit te vullen. Als de uitvoer een object is, kan het runbook een enkele eigenschap opgeven.

![uitvoer van activiteit](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Het tweede mechanisme voor gegevens toegang haalt de uitvoer van een activiteit op in een Power shell-expressie gegevens bron of een werk stroom script activiteit met een `ActivityOutput` variabele, met behulp van de volgende syntaxis. Als de uitvoer een object is, kan uw runbook een enkele eigenschap opgeven.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Controle punten gebruiken

U kunt [controle punten](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) instellen in een grafisch Power shell-werk stroom-runbook door in een wille keurige activiteit een **controle punt runbook** te selecteren. Dit zorgt ervoor dat een controle punt wordt ingesteld nadat de activiteit is uitgevoerd.

![Controlepunt](media/automation-graphical-authoring-intro/set-checkpoint.png)

Controle punten worden alleen ingeschakeld in grafische runbooks voor Power shell-werk stromen en zijn niet beschikbaar in grafische runbooks. Als het runbook gebruikmaakt van Azure-cmdlets, moet dit elke activiteit met een controle punt volgen met een `Connect-AzAccount` activiteit. De verbindings bewerking wordt gebruikt als het runbook is onderbroken en opnieuw moet worden gestart vanuit dit controle punt op een andere werk nemer.

## <a name="handle-runbook-input"></a>Invoer van het runbook verwerken

Een runbook vereist invoer van een gebruiker die het runbook start met behulp van de Azure Portal of van een ander runbook, als de huidige wordt gebruikt als een onderliggende. Een voor beeld: voor een runbook dat een virtuele machine maakt, moet de gebruiker deze gegevens mogelijk als de naam van de virtuele machine en andere eigenschappen opgeven telkens wanneer het runbook wordt gestart.

Het runbook accepteert invoer door een of meer invoer parameters te definiëren. De gebruiker geeft de waarden voor deze para meters telkens wanneer het runbook wordt gestart. Wanneer de gebruiker het runbook start met behulp van de Azure Portal, wordt de gebruiker gevraagd waarden op te geven voor elke invoer parameter die door het runbook wordt ondersteund.

Bij het ontwerpen van uw runbook kunt u toegang krijgen tot de invoer parameters door te klikken op **invoer en uitvoer** op de werk balk van runbook. Hiermee opent u het besturings element voor invoer en uitvoer, waar u een bestaande invoer parameter kunt bewerken of een nieuw item maakt door te klikken op **invoer toevoegen**.

![Invoer toevoegen](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Elke invoer parameter wordt gedefinieerd door de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Name | Vereist. De naam van de para meter. De naam moet uniek zijn binnen het runbook. De naam moet beginnen met een letter en mag alleen letters, cijfers en onderstrepings tekens bevatten. De naam mag geen spatie bevatten. |
| Beschrijving |Optioneel. Beschrijving van het doel van de invoer parameter. |
| Type | Optioneel. Er wordt een gegevens type verwacht voor de parameter waarde. De Azure Portal biedt een geschikt besturings element voor het gegevens type voor elke para meter bij het vragen om invoer. Ondersteunde parameter typen zijn String, Int32, Int64, Decimal, Boolean, DateTime en object. Als er geen gegevens type is geselecteerd, wordt standaard de teken reeks gebruikt.|
| Verplicht | Optioneel. Instelling die aangeeft of er een waarde moet worden opgegeven voor de para meter. Als u kiest `yes` , moet u een waarde opgeven wanneer het runbook wordt gestart. Als u kiest `no` , is een waarde niet vereist wanneer het runbook wordt gestart en kan een standaard waarde worden gebruikt. Het runbook kan niet worden gestart als u geen waarde opgeeft voor elke verplichte para meter waarvoor geen standaard waarde is gedefinieerd. |
| Standaardwaarde | Optioneel. De waarde die wordt gebruikt voor een para meter als deze niet wordt door gegeven wanneer het runbook wordt gestart. Kies om een standaard waarde in te stellen `Custom` . Selecteer deze optie `None` Als u geen standaard waarde wilt opgeven. |

## <a name="handle-runbook-output"></a>Runbook-uitvoer verwerken

Met grafische ontwerpen worden gegevens opgeslagen die zijn gemaakt door een activiteit die geen uitgaande koppeling heeft naar de [uitvoer van het runbook](./automation-runbook-output-and-messages.md). De uitvoer wordt opgeslagen met de runbook-taak en is beschikbaar voor een bovenliggend runbook wanneer het runbook als onderliggend wordt gebruikt.

## <a name="work-with-powershell-expressions"></a>Werken met Power shell-expressies

Een van de voor delen van het ontwerpen van afbeeldingen is dat u een runbook met minimale kennis van Power shell kunt bouwen. Op dit moment moet u echter wel een bit van Power shell kennen voor het invullen van bepaalde [parameter waarden](#use-activities) en voor het instellen van [koppelings voorwaarden](#use-links-for-workflow). In deze sectie vindt u een korte inleiding tot Power shell-expressies. Volledige informatie over Power shell is beschikbaar op [Scripting met Windows Power shell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Een Power shell-expressie gebruiken als gegevens bron

U kunt een Power shell-expressie als gegevens bron gebruiken om de waarde van een [activiteit parameter](#use-activities) te vullen met de resultaten van Power shell-code. De expressie kan een enkele regel code zijn die een eenvoudige functie of meerdere regels uitvoert waarmee een complexe logica wordt uitgevoerd. Uitvoer van een opdracht die niet is toegewezen aan een variabele wordt uitgevoerd naar de parameter waarde.

Met de volgende opdracht wordt bijvoorbeeld de huidige datum uitgevoerd.

```powershell-interactive
Get-Date
```

Het volgende code fragment bouwt een teken reeks van de huidige datum en wijst deze toe aan een variabele. De code verzendt de inhoud van de variabele naar de uitvoer.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

De volgende opdrachten evalueren de huidige datum en retour neren een teken reeks die aangeeft of de huidige dag een weekend of een weekdag is.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Uitvoer van activiteit gebruiken

Als u de uitvoer van een vorige activiteit in uw runbook wilt gebruiken, gebruikt u de `ActivityOutput` variabele met de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

U kunt bijvoorbeeld een activiteit hebben met een eigenschap waarvoor de naam van een virtuele machine is vereist. In dit geval kan uw runbook gebruikmaken van de volgende expressie.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Als voor de eigenschap het virtuele-machine object is vereist in plaats van alleen een naam, retourneert het runbook het volledige object met behulp van de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Het runbook kan de uitvoer van een activiteit in een complexere expressie gebruiken, zoals in het volgende voor schrift. Deze expressie voegt tekst toe aan de naam van de virtuele machine.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Waarden vergelijken

Gebruik [vergelijkings operatoren](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) om waarden te vergelijken of te bepalen of een waarde overeenkomt met een opgegeven patroon. Een vergelijking retourneert een waarde van waar of onwaar.

De volgende voor waarde bepaalt bijvoorbeeld of de virtuele machine van een activiteit met de naam `Get-AzureVM` momenteel is gestopt.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

De volgende voor waarde bepaalt of dezelfde virtuele machine zich in een andere status bevindt dan gestopt.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

U kunt meerdere voor waarden in uw runbook samen voegen met behulp van een [logische operator](/powershell/module/microsoft.powershell.core/about/about_logical_operators), zoals `-and` of `-or` . Met de volgende voor waarde wordt bijvoorbeeld gecontroleerd of de virtuele machine in het vorige voor beeld de status beëindigd of gestopt heeft.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Hashtabellen gebruiken

[Hashtabellen](/powershell/module/microsoft.powershell.core/about/about_hash_tables) zijn naam/waarde-paren die nuttig zijn voor het retour neren van een set waarden. U kunt ook een hashtabel als een woorden lijst weer geven. Eigenschappen voor bepaalde activiteiten verwachten een hashtabel in plaats van een eenvoudige waarde.

Maak een hashtabel met de volgende syntaxis. Het kan een wille keurig aantal vermeldingen bevatten, maar deze worden gedefinieerd door een naam en een waarde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Met de volgende expressie maakt u bijvoorbeeld een hashtabel die moet worden gebruikt als de gegevens bron voor een activiteit parameter die een hashtabel van waarden voor een Internet-zoek opdracht verwacht.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

In het volgende voor beeld wordt de uitvoer van een activiteit aangeroepen `Get Twitter Connection` om een hashtabel in te vullen.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Verifiëren bij Azure-resources

Runbooks in Azure Automation die Azure-resources beheren, moeten worden geverifieerd voor Azure. Het [uitvoeren als-account](./automation-security-overview.md), ook wel een Service-Principal genoemd, is het standaard mechanisme dat een Automation-runbook gebruikt om toegang te krijgen tot Azure Resource Manager resources in uw abonnement. U kunt deze functionaliteit toevoegen aan een grafisch runbook door het verbindings element toe te voegen `AzureRunAsConnection` , dat gebruikmaakt van de cmdlet [Get-AutomationConnection](/system-center/sma/manage-global-assets) van Power shell, op het canvas. U kunt ook de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) toevoegen. Dit scenario wordt geïllustreerd in het volgende voor beeld.

![Run as-verificatie activiteiten](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

De `Get Run As Connection` activiteit of `Get-AutomationConnection` is geconfigureerd met een gegevens bron met de naam constante waarde `AzureRunAsConnection` .

![Configuratie van run as-verbinding](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

De volgende activiteit, `Connect-AzAccount` , voegt het geverifieerde run as-account toe voor gebruik in het runbook.

![Connect-AzAccount parameterset](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook kan alleen worden gebruikt `Connect-AzAccount` .

Voor de parameter velden **APPLICATIONID**, **CERTIFICATETHUMBPRINT** en **TENANTID** geeft u de naam van de eigenschap voor het pad naar het veld op, omdat de activiteit een object met meerdere eigenschappen uitvoert. Anders mislukt de verificatie wanneer het runbook wordt uitgevoerd. Dit is wat u Mini maal nodig hebt om uw runbook te verifiëren met het run as-account.

Sommige abonnees maken een Automation-account met behulp van een [Azure AD-gebruikers account](./shared-resources/credentials.md) voor het beheren van klassieke Azure-implementatie of voor Azure Resource Manager resources. Als u achterwaartse compatibiliteit voor deze abonnees wilt behouden, is het verificatie mechanisme dat in uw runbook moet worden gebruikt de `Add-AzureAccount` cmdlet met een [referentie-element](./shared-resources/credentials.md). De Asset vertegenwoordigt een Active Directory gebruiker met toegang tot het Azure-account.

U kunt deze functionaliteit voor uw grafische runbook inschakelen door een referentie-element toe te voegen aan het canvas, gevolgd door een `Add-AzureAccount` activiteit die gebruikmaakt van het referentie-element voor de invoer. Zie het volgende voorbeeld

![Verificatie activiteiten](media/automation-graphical-authoring-intro/authentication-activities.png)

Het runbook moet worden geverifieerd bij de start en na elk controle punt. Daarom moet u een `Add-AzureAccount` activiteit gebruiken na elke `Checkpoint-Workflow` activiteit. U hoeft geen aanvullende referentie activiteit te gebruiken.

![Uitvoer van activiteit](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Een grafisch runbook exporteren

U kunt alleen de gepubliceerde versie van een grafisch runbook exporteren. Als het runbook nog niet is gepubliceerd, wordt de knop **exporteren** uitgeschakeld. Wanneer u op de knop **exporteren** klikt, wordt het runbook gedownload naar uw lokale computer. De naam van het bestand komt overeen met de naam van het runbook met de extensie **. graphrunbook** .

## <a name="import-a-graphical-runbook"></a>Een grafisch runbook importeren

U kunt een grafisch of grafisch runbook van een Power shell-werk stroom bestand importeren door de optie **importeren** te selecteren bij het toevoegen van een runbook. Wanneer u het bestand selecteert dat u wilt importeren, kunt u dezelfde naam blijven of een nieuw item opgeven. In het veld type **runbook** wordt het type runbook weer gegeven nadat het geselecteerde bestand is geëvalueerd. Als u probeert een ander type te selecteren dat niet juist is, wordt in de grafische editor een bericht weer gegeven dat er mogelijke conflicten zijn en dat er syntaxis fouten zijn tijdens de conversie.

![Runbook importeren](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Een grafisch runbook testen

Elk grafisch runbook in Azure Automation heeft een concept versie en een gepubliceerde versie. U kunt alleen de gepubliceerde versie uitvoeren, maar u kunt de concept versie alleen bewerken. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de concept versie gereed is voor gebruik, publiceert u deze, waardoor de huidige gepubliceerde versie wordt overschreven door uw concept versie.

U kunt de concept versie van een runbook testen in de Azure Portal terwijl de gepubliceerde versie ongewijzigd blijft. U kunt ook een nieuw runbook testen voordat het is gepubliceerd, zodat u kunt controleren of het runbook correct werkt voordat alle versies worden vervangen. Wanneer een runbook wordt getest, wordt de concept versie uitgevoerd en worden alle acties uitgevoerd die worden uitgevoerd. Er is geen taak geschiedenis gemaakt, maar in het deel venster test uitvoer wordt de uitvoer weer gegeven.

Open het besturings element test voor uw grafische runbook door het runbook te openen voor bewerken en vervolgens te klikken op **test venster**. De test controle vraagt om invoer parameters en u kunt het runbook starten door op **Start** te klikken.

## <a name="publish-a-graphical-runbook"></a>Een grafisch runbook publiceren

Publiceer een grafisch runbook door het runbook te openen voor bewerking en vervolgens te klikken op **publiceren**. Mogelijke statussen voor het runbook zijn:

* Nieuw--het runbook is nog niet gepubliceerd. 
* Gepubliceerd: het runbook is gepubliceerd.
* In bewerken--het runbook is bewerkt nadat het is gepubliceerd en het concept en de gepubliceerde versies verschillen.

![Runbook-statussen](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

U hebt de mogelijkheid om terug te keren naar de gepubliceerde versie van een runbook. Met deze bewerking worden alle wijzigingen die zijn aangebracht sinds het runbook voor het laatst is gepubliceerd, verwijderd. De concept versie van het runbook wordt vervangen door de gepubliceerde versie.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een grafisch Runbook maken](learn/automation-tutorial-runbook-graphical.md)om aan de slag te gaan met grafische runbooks.
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen en hun voordelen en beperkingen.
* Zie [uitvoeren als-account](automation-security-overview.md#run-as-account)voor informatie over het verifiëren met behulp van het uitvoeren als-account voor Automation.
* Zie [Az.Automation](/powershell/module/az.automation/#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
