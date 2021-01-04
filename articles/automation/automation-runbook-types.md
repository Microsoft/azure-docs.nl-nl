---
title: Azure Automation-runbooktypen
description: In dit artikel worden de typen runbooks beschreven die u kunt gebruiken in Azure Automation en overwegingen om te bepalen welk type u moet gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1a0c12297f19d30bf13ffbe594e0433c83914a8e
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733957"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbooktypen

De functie Azure Automation proces automatisering ondersteunt diverse typen runbooks, zoals gedefinieerd in de volgende tabel. Zie [Runbook-uitvoering in azure Automation](automation-runbook-execution.md)voor meer informatie over de proces automatiserings omgeving.

| Type | Beschrijving |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Grafisch runbook dat is gebaseerd op Windows Power shell en is volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [Grafische power shell-werk stroom](#graphical-runbooks)|Grafisch runbook op basis van een Windows Power shell-werk stroom en is volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [PowerShell](#powershell-runbooks) |Tekst runbook op basis van Windows Power shell-scripts. |
| [PowerShell-werkstroom](#powershell-workflow-runbooks)|Tekst runbook op basis van Windows Power shell workflow scripting. |
| [Python](#python-runbooks) |Tekst runbook op basis van python-scripts. |

Houd rekening met de volgende overwegingen wanneer u bepaalt welk type moet worden gebruikt voor een bepaald runbook.

* U kunt geen runbooks converteren van grafisch naar tekst type of andersom.
* Er zijn beperkingen bij het gebruik van runbooks van verschillende typen als onderliggende runbooks. Zie voor meer informatie [onderliggende runbooks in azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafische runbooks

U kunt grafische PowerShell Werkstroom-runbooks maken en bewerken met behulp van de grafische editor in Azure Portal. U kunt dit type runbook echter niet maken of bewerken met een ander hulp programma. Belangrijkste functies van grafische runbooks:

* Exporteren naar bestanden in uw Automation-account en vervolgens geïmporteerd in een ander Automation-account.
* Power shell-code genereren.
* Wordt geconverteerd naar of van grafische power shell-werk stroom-runbooks tijdens het importeren.

### <a name="advantages"></a>Voordelen

* Gebruik het ontwerp model voor invoegen en configureren van de Visual.
* Richt u op de manier waarop gegevens het proces door lopen.
* Visuele weer gave van beheer processen.
* Neem andere runbooks op als onderliggende runbooks om werk stromen op hoog niveau te maken.
* Stimuleer modulaire programmering.

### <a name="limitations"></a>Beperkingen

* Kan niet buiten de Azure Portal maken of bewerken.
* Voor het uitvoeren van complexe logica is mogelijk een code activiteit vereist die Power shell-code bevat.
* Kan niet converteren naar een van de [tekst indelingen](automation-runbook-types.md), noch kunt u een tekst runbook omzetten in een grafische indeling. 
* Kan Power shell-code die door de grafische werk stroom wordt gemaakt, niet weer geven of rechtstreeks bewerken. U kunt de code die u in code-activiteiten maakt, bekijken.
* Kan geen runbooks uitvoeren op een Linux-Hybrid Runbook Worker. Zie [resources automatiseren in uw Data Center of in de Cloud met behulp van Hybrid Runbook worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Power shell-runbooks

PowerShell-runbooks zijn gebaseerd op Windows PowerShell. U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal.  U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met Power shell-code zonder de andere complexiteit van Power shell-werk stroom.
* Start sneller dan Power shell workflow-runbooks, omdat ze niet hoeven te worden gecompileerd voordat ze worden uitgevoerd.
* Voer in Azure en in Hybrid Runbook Workers uit voor zowel Windows als Linux.

### <a name="limitations"></a>Beperkingen

* U moet vertrouwd zijn met het uitvoeren van Power shell-scripts.
* Runbooks kunnen geen [parallelle verwerking](automation-powershell-workflow.md#use-parallel-processing) gebruiken om meerdere acties parallel uit te voeren.
* Runbooks kunnen geen [controle punten](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) gebruiken om het runbook te hervatten als er een fout optreedt.
* U kunt alleen Power shell workflow-runbooks en grafische runbooks als onderliggende runbooks toevoegen met behulp van de cmdlet [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) , waarmee een nieuwe taak wordt gemaakt.

### <a name="known-issues"></a>Bekende problemen

Hieronder vindt u actuele bekende problemen met Power shell-runbooks:

* Power shell-runbooks kunnen een niet-versleutelde [variabele Asset](./shared-resources/variables.md) niet ophalen met een null-waarde.
* Power shell-runbooks kunnen een variabele Asset met `*~*` in de naam niet ophalen.
* Een [Get-process-](/powershell/module/microsoft.powershell.management/get-process) bewerking in een lus in een Power shell-runbook kan vastlopen na ongeveer 80 iteraties.
* Een Power shell-runbook kan mislukken als wordt geprobeerd een grote hoeveelheid gegevens naar de uitvoer stroom tegelijk te schrijven. Normaal gesp roken kunt u dit probleem omzeilen door het runbook uit te voeren alleen de informatie die nodig is om met grote objecten te werken. In plaats van zonder beperkingen te gebruiken `Get-Process` , kunt u bijvoorbeeld de cmdlet uitvoer alleen de vereiste para meters hebben als in `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>Power shell workflow-runbooks

Power shell workflow-runbooks zijn tekst-runbooks op basis van [Windows Power shell-werk stroom](automation-powershell-workflow.md). U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal. U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeer alle complexe logica met Power shell-werk stroom code.
* Gebruik [controle punten](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) om de bewerking te hervatten als er een fout optreedt.
* Gebruik [parallelle verwerking](automation-powershell-workflow.md#use-parallel-processing) om meerdere acties parallel uit te voeren.
* Kan andere grafische runbooks en Power shell-werk stroom runbooks als onderliggende runbooks bevatten om werk stromen op hoog niveau te maken.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met de Power shell-werk stroom.
* Runbooks moeten de extra complexiteit van Power shell-werk stroom, zoals [gedeserialiseerd objecten](automation-powershell-workflow.md#deserialized-objects), afhandelen.
* Runbooks nemen meer tijd in beslag dan Power shell-runbooks sinds ze moeten worden gecompileerd voordat ze kunnen worden uitgevoerd.
* U kunt Power shell-runbooks alleen als onderliggende runbooks toevoegen met behulp van de- `Start-AzAutomationRunbook` cmdlet.
* Runbooks kunnen niet worden uitgevoerd op een Linux-Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python-runbooks

Python runbooks compileren onder python 2 en python 3. Python 3-runbooks zijn momenteel beschikbaar als preview-versie. U kunt de code van het runbook rechtstreeks bewerken met de teksteditor in de Azure-portal. U kunt ook een editor voor offline tekst gebruiken en [het runbook importeren](manage-runbooks.md) in azure Automation.

### <a name="advantages"></a>Voordelen

* Gebruik de robuuste python-bibliotheken.
* Kan worden uitgevoerd in azure of op Hybrid Runbook Workers.
* Voor python 2 worden Windows Hybrid Runbook Workers ondersteund met [Python 2,7](https://www.python.org/downloads/release/latest/python2) geïnstalleerd.
* Voor python 3-Cloud taken wordt python 3,8-versie ondersteund. Scripts en pakketten van elke 3. x-versie kunnen werken als de code compatibel is tussen verschillende versies.  
* Voor python 3-taken op Windows-computers kunt u kiezen voor het installeren van een versie van 3. x die u wilt gebruiken.  
* Voor python 3-taken op Linux-machines is het afhankelijk van de python 3-versie die op de computer is geïnstalleerd om DSC-OMSConfig en de Linux-Hybrid Worker uit te voeren. We raden aan om 3,6 te installeren op Linux-computers. Verschillende versies moeten echter ook werken als er geen wijzigingen in de methode handtekeningen of contracten tussen versies van python 3 optreden.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met python-scripts.
* Als u bibliotheken van derden wilt gebruiken, moet u [de pakketten importeren](python-packages.md) in het Automation-account.
*    Het is niet mogelijk om met de cmdlet start-AutomationRunbook in de Power shell/Power shell-werk stroom een python 3-runbook (preview) te starten. U kunt de cmdlet **Start-AzAutomationRunbook** van de module AZ. Automation of de cmdlet **Start-AzureRmAutomationRunbook** van de AzureRm. Automation-module gebruiken om deze beperking te omzeilen.  
* Python 3-runbooks (preview) en pakketten werken niet met Power shell.
* Het gebruiken van een webhook om een Python-runbook te starten wordt niet ondersteund.
* Azure Automation biedt geen ondersteuning voor **sys. stderr**.

### <a name="known-issues"></a>Bekende problemen

Python 3-taken mislukken soms met een uitzonderings bericht *Ongeldige interpreter-pad naar een uitvoerbaar bestand*. Deze uitzonde ring kan optreden als een taak vertraging heeft ondergaan, die meer dan tien minuten begint of **Start-AutomationRunbook** gebruikt om python 3-runbooks te starten. Als de taak is vertraagd, moet het runbook opnieuw worden gestart om het opnieuw te starten.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een Power shell-Runbook maken](learn/automation-tutorial-runbook-textual-powershell.md)voor meer informatie over Power shell-runbooks.
* Zie [zelf studie: een Power shell workflow-Runbook maken](learn/automation-tutorial-runbook-textual.md)voor meer informatie over Power shell workflow-runbooks.
* Zie [zelf studie: een grafisch Runbook maken](learn/automation-tutorial-runbook-graphical.md)voor meer informatie over grafische runbooks.
* Zie [zelf studie: een python-Runbook maken](learn/automation-tutorial-runbook-textual-python2.md)voor meer informatie over python-runbooks.
