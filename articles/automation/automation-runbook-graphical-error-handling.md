---
title: Fouten in Azure Automation grafische runbooks afhandelen
description: In dit artikel wordt beschreven hoe u foutafhandelingslogica implementeert in grafische runbooks.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00ed73a1b95558f256caa01ad8eafbefe11b0f32
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830480"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Fouten in grafische runbooks afhandelen

Een belangrijk ontwerpprincipe om rekening mee te houden voor Azure Automation grafische runbook is de identificatie van problemen die het runbook tijdens de uitvoering kan ervaren. Deze problemen kunnen geslaagde pogingen, verwachte foutstatussen en onverwachte foutvoorwaarden zijn.

Als er een niet-beëindigingsfout optreedt bij een runbookactiviteit, verwerkt Windows PowerShell de activiteit vaak door alle volgende activiteiten te verwerken, ongeacht de fout. De fout genereert waarschijnlijk een uitzondering, maar de volgende activiteit mag wel worden uitgevoerd.

Uw grafische runbook moet code voor foutafhandeling bevatten om problemen met de uitvoering op te lossen. Als u de uitvoer van een activiteit wilt valideren of een fout wilt afhandelen, kunt u een PowerShell-codeactiviteit gebruiken, voorwaardelijke logica definiëren voor de uitvoerkoppeling van de activiteit of een andere methode toepassen.

Grafische Azure Automation-runbooks zijn verbeterd en bieden nu de mogelijkheid om foutafhandeling op te nemen. U kunt nu van uitzonderingen niet-afsluitfouten maken en foutkoppelingen tussen activiteiten maken. Dankzij het verbeterde proces kan uw runbook fouten ondervangen en gerealiseerde of onverwachte omstandigheden beheren. 

## <a name="powershell-error-types"></a>PowerShell-fouttypen

De typen PowerShell-fouten die kunnen optreden tijdens het uitvoeren van een runbook zijn beëindigingsfouten en niet-beëindigingsfouten.
 
### <a name="terminating-error"></a>Fout bij het beëindigen

Een beëindigingsfout is een ernstige fout tijdens de uitvoering, die de uitvoering van een opdracht of script volledig stopt. Voorbeelden zijn niet-bestaande cmdlets, syntaxisfouten die verhinderen dat een cmdlet wordt uitgevoerd en andere fatale fouten.

### <a name="non-terminating-error"></a>Niet-beëindigingsfout

Een niet-beëindigingsfout is een niet-ernstige fout waardoor de uitvoering ondanks de foutvoorwaarde kan worden voortgezet. Voorbeelden zijn operationele fouten, zoals fouten met bestand niet gevonden en problemen met machtigingen.

## <a name="when-to-use-error-handling"></a>Wanneer foutafhandeling gebruiken

Gebruik foutafhandeling in uw runbook wanneer een kritieke activiteit een fout of uitzondering veroorzaakt. Het is belangrijk om te voorkomen dat de volgende activiteit in het runbook wordt verwerkt en dat de fout op de juiste wijze wordt verwerkt. Het afhandelen van de fout is met name essentieel wanneer uw runbooks ondersteuning bieden voor een bedrijfsproces of een proces voor servicebewerkingen.

## <a name="add-error-links"></a>Foutkoppelingen toevoegen

Voor elke activiteit die een fout kan veroorzaken, kunt u een foutkoppeling toevoegen die verwijst naar een andere activiteit. De doelactiviteit kan van elk type zijn, waaronder codeactiviteit, het aanroepen van een cmdlet, het aanroepen van een ander runbook, en meer. De doelactiviteit kan ook uitgaande koppelingen hebben, ofwel reguliere koppelingen of foutkoppelingen. Met de koppelingen kan het runbook complexe foutafhandelingslogica implementeren zonder gebruik te maken van een codeactiviteit.

Het wordt aanbevolen om een toegewezen runbook voor foutafhandeling te maken met algemene functionaliteit, maar dit is niet verplicht. Denk bijvoorbeeld aan een runbook dat probeert een virtuele machine te starten en er een toepassing op te installeren. Als de VM niet goed start, gebeurt het volgende:

1. Hiermee wordt een melding over dit probleem verzendt.
2. Start in plaats daarvan een ander runbook dat automatisch een nieuwe VM in artikelen inst.

Eén oplossing is om een foutkoppeling in het runbook te hebben die verwijst naar een activiteit die stap 1 afwerkt. Het runbook kan bijvoorbeeld de cmdlet verbinden met een activiteit voor stap twee, zoals de `Write-Warning` cmdlet [Start-AzAutomationRunbook.](/powershell/module/az.automation/start-azautomationrunbook)

U kunt dit gedrag ook generaliseren voor gebruik in veel runbooks door deze twee activiteiten in een afzonderlijk runbook voor foutafhandeling te plaatsen. Voordat het oorspronkelijke runbook dit runbook voor foutafhandeling aanroept, kan het een aangepast bericht van de gegevens maken en het vervolgens als parameter doorgeven aan het runbook voor foutafhandeling.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Uitzonderingen veranderen in niet-beëindigingsfouten

Elke activiteit in uw runbook heeft een configuratie-instelling die uitzonderingen in niet-beëindigingsfouten verandert. Deze instelling is standaard uitgeschakeld. U wordt aangeraden deze instelling in te stellen voor elke activiteit waarbij uw runbook fouten afwerkt. Deze instelling zorgt ervoor dat het runbook zowel beëindigings- als niet-beëindigingsfouten in de activiteit verwerkt als niet-beëindigingsfouten, met behulp van een foutkoppeling.  

Nadat u de configuratie-instelling hebt inschakelen, moet u uw runbook een activiteit laten maken waarmee de fout wordt verwerkt. Als de activiteit een fout veroorzaakt, worden de uitgaande foutkoppelingen gevolgd. De reguliere koppelingen worden niet gevolgd, zelfs niet als de activiteit ook reguliere uitvoer produceert.<br><br> ![Voorbeeld van foutkoppeling voor Automation-runbook](media/automation-runbook-graphical-error-handling/error-link-example.png)

In het volgende voorbeeld haalt een runbook een variabele op die de computernaam van een VM bevat. Vervolgens wordt geprobeerd de VM te starten met de volgende activiteit.<br><br> ![Voorbeeld van foutafhandeling in Automation-runbook](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

De `Get-AutomationVariable` activiteit en de cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) zijn geconfigureerd om uitzonderingen om te zetten in fouten. Als er problemen zijn met het verkrijgen van de variabele of het starten van de VM, genereert de code fouten.<br><br> ![Activiteitsinstellingen voor foutafhandeling voor Automation-runbook. ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Foutkoppelingen stromen van deze activiteiten naar één `error management` codeactiviteit. Deze activiteit is geconfigureerd met een eenvoudige PowerShell-expressie die het trefwoord gebruikt om de verwerking te stoppen, samen met om het bericht op te halen waarin de `throw` `$Error.Exception.Message` huidige uitzondering wordt beschreven.<br><br> ![Voorbeeld van foutafhandeling in Automation-runbook](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Volgende stappen

* Zie Problemen met runbook oplossen voor meer informatie over het oplossen van fouten in [grafische runbooks.](troubleshoot/runbooks.md)
