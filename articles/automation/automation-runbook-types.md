---
title: Azure Automation-runbooktypen
description: In dit artikel worden de typen runbooks beschreven die u kunt gebruiken in Azure Automation en overwegingen om te bepalen welk type u wilt gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 02/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3e4f90372c2da22e8df3430ce340477352e5033b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830444"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbooktypen

De Azure Automation-functie Procesautomatisering ondersteunt verschillende typen runbooks, zoals gedefinieerd in de volgende tabel. Zie Runbookuitvoering in Azure Automation voor meer informatie [over de omgeving voor procesautomatisering.](automation-runbook-execution.md)

| Type | Description |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Grafisch runbook op basis van Windows PowerShell en volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [Grafische PowerShell-werkstroom](#graphical-runbooks)|Grafisch runbook op basis van Windows PowerShell Workflow en volledig gemaakt en bewerkt in de grafische editor in Azure Portal. |
| [PowerShell](#powershell-runbooks) |Tekstueel runbook op basis van Windows PowerShell scripting. |
| [PowerShell-werkstroom](#powershell-workflow-runbooks)|Tekstueel runbook op basis van Windows PowerShell Workflow-scripts. |
| [Python](#python-runbooks) |Tekstueel runbook op basis van Python-scripts. |

Bij het bepalen welk type u voor een bepaald runbook wilt gebruiken, moet u rekening houden met de volgende overwegingen.

* U kunt runbooks niet converteren van grafisch naar teksttype of andersom.
* Er gelden beperkingen bij het gebruik van runbooks van verschillende typen als onderliggende runbooks. Zie Onderliggende [runbooks in Azure Automation.](automation-child-runbooks.md)

## <a name="graphical-runbooks"></a>Grafische runbooks

U kunt grafische PowerShell Werkstroom-runbooks maken en bewerken met behulp van de grafische editor in Azure Portal. U kunt dit type runbook echter niet maken of bewerken met een ander hulpprogramma. Belangrijkste functies van grafische runbooks:

* Geëxporteerd naar bestanden in uw Automation-account en vervolgens geïmporteerd in een ander Automation-account.
* PowerShell-code genereren.
* Geconverteerd naar of van grafische PowerShell Workflow-runbooks tijdens het importeren.

### <a name="advantages"></a>Voordelen

* Gebruik het ontwerpmodel voor het configureren van visuele invoegkoppelingen.
* Richt u op de manier waarop gegevens door het proces stromen.
* Visueel beheerprocessen vertegenwoordigen.
* Neem andere runbooks op als onderliggende runbooks om werkstromen op hoog niveau te maken.
* Modulair programmeren stimuleren.

### <a name="limitations"></a>Beperkingen

* Kan niet maken of bewerken buiten de Azure Portal.
* Mogelijk is een codeactiviteit met PowerShell-code vereist om complexe logica uit te voeren.
* U kunt niet converteren naar een van de [tekstindelingen](automation-runbook-types.md)en u kunt ook geen tekstrunbook converteren naar een grafische indeling. 
* Kan PowerShell-code die door de grafische werkstroom wordt gemaakt, niet weergeven of rechtstreeks bewerken. U kunt de code die u maakt, weergeven in alle codeactiviteiten.
* Kan runbooks niet uitvoeren op een Linux-Hybrid Runbook Worker. Zie [Resources in uw datacenter of cloud automatiseren met behulp van Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runbooks

PowerShell-runbooks zijn gebaseerd op Windows PowerShell. U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal.  U kunt ook elke offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeert alle complexe logica met PowerShell-code zonder de andere complexiteit van PowerShell Workflow.
* Start sneller dan PowerShell Workflow-runbooks, omdat deze niet hoeven te worden gecompileerd voordat ze worden uitgevoerd.
* Voer uit in Azure en op Hybrid Runbook Workers voor zowel Windows als Linux.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met Het uitvoeren van PowerShell-scripts.
* Runbooks kunnen geen parallelle verwerking [gebruiken om](automation-powershell-workflow.md#use-parallel-processing) meerdere acties parallel uit te voeren.
* Runbooks kunnen geen controlepunten [gebruiken om](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) het runbook te hervatten als er een fout is opgetreden.
* U kunt alleen PowerShell Workflow-runbooks en grafische runbooks als onderliggende runbooks opnemen met behulp van de cmdlet [Start-AzAutomationRunbook,](/powershell/module/az.automation/start-azautomationrunbook) waarmee een nieuwe taak wordt gemaakt.

### <a name="known-issues"></a>Bekende problemen

Hier volgen de huidige bekende problemen met PowerShell-runbooks:

* PowerShell-runbooks kunnen een niet-versleutelde variabele asset met [een](./shared-resources/variables.md) null-waarde niet ophalen.
* PowerShell-runbooks kunnen geen variabele asset ophalen met `*~*` in de naam.
* Een [Get-Process-bewerking](/powershell/module/microsoft.powershell.management/get-process) in een lus in een PowerShell-runbook kan na ongeveer 80 iteraties vastlopen.
* Een PowerShell-runbook kan mislukken als wordt geprobeerd een grote hoeveelheid gegevens tegelijk naar de uitvoerstroom te schrijven. U kunt dit probleem meestal oplossen door de runbookuitvoer alleen de informatie te laten zien die nodig is om met grote objecten te werken. In plaats van bijvoorbeeld zonder beperkingen te gebruiken, kunt u de cmdlet-uitvoer alleen de `Get-Process` vereiste parameters laten gebruiken, zoals in `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>PowerShell Workflow-runbooks

PowerShell Workflow-runbooks zijn tekstrunbooks op basis [van Windows PowerShell Workflow.](automation-powershell-workflow.md) U bewerkt de code van het runbook rechtstreeks met de teksteditor in de Azure-portal. U kunt ook elke offline teksteditor gebruiken en [het runbook importeren](manage-runbooks.md) in Azure Automation.

### <a name="advantages"></a>Voordelen

* Implementeert alle complexe logica met PowerShell Workflow-code.
* Gebruik [controlepunten](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) om de bewerking te hervatten als er een fout is opgetreden.
* Gebruik [parallelle verwerking om](automation-powershell-workflow.md#use-parallel-processing) meerdere acties parallel uit te voeren.
* Kan andere grafische runbooks en PowerShell Workflow-runbooks bevatten als onderliggende runbooks om werkstromen op hoog niveau te maken.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met PowerShell Workflow.
* Runbooks moeten omgaan met de extra complexiteit van PowerShell Workflow, zoals [gedeserialiseerde objecten](automation-powershell-workflow.md#deserialized-objects).
* Het duurt langer om runbooks te starten dan PowerShell-runbooks, omdat ze moeten worden gecompileerd voordat ze worden uitgevoerd.
* U kunt PowerShell-runbooks alleen opnemen als onderliggende runbooks met behulp van de `Start-AzAutomationRunbook` cmdlet .
* Runbooks kunnen niet worden uitgevoerd op een Linux-Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python-runbooks

Python-runbooks worden ge compileerd onder Python 2 en Python 3. Python 3-runbooks zijn momenteel beschikbaar als preview-versie. U kunt de code van het runbook rechtstreeks bewerken met de teksteditor in de Azure-portal. U kunt ook een offline teksteditor gebruiken [en het runbook importeren](manage-runbooks.md) in Azure Automation.

Python 3-runbooks worden ondersteund in de volgende wereldwijde Azure-infrastructuren:

* Azure Global
* Azure Government

### <a name="advantages"></a>Voordelen

* Gebruik de robuuste Python-bibliotheken.
* Kan worden uitgevoerd in Azure of op Hybrid Runbook Workers.
* Voor Python 2 worden Windows Hybrid Runbook Workers ondersteund met [python 2.7](https://www.python.org/downloads/release/latest/python2) geïnstalleerd.
* Voor Python 3 Cloud-taken wordt versie Python 3.8 ondersteund. Scripts en pakketten van een 3.x-versie kunnen werken als de code compatibel is in verschillende versies.  
* Voor hybride Python 3-taken op Windows-machines kunt u ervoor kiezen om een 3.x-versie te installeren die u mogelijk wilt gebruiken.  
* Voor hybride Python 3-taken op Linux-machines zijn we afhankelijk van de Python 3-versie die op de computer is geïnstalleerd om DSC OMSConfig en de Linux-Hybrid Worker. U wordt aangeraden 3.6 te installeren op Linux-machines. Verschillende versies moeten echter ook werken als er geen wijzigingen zijn die wijzigingen aanbrengen in handtekeningen of contracten tussen versies van Python 3.

### <a name="limitations"></a>Beperkingen

* U moet bekend zijn met Het uitvoeren van Python-scripts.
* Als u bibliotheken van derden wilt gebruiken, moet u [de pakketten importeren](python-packages.md) in het Automation-account.
* Het gebruik van de cmdlet **Start-AutomationRunbook**   in PowerShell/PowerShell Workflow om een Python 3-runbook (preview) te starten, werkt niet. U kunt de cmdlet **Start-AzAutomationRunbook** uit de Az.Automation-module of de cmdlet **Start-AzureRmAutomationRunbook** van de AzureRm.Automation-module gebruiken om deze beperking te omzeilen.  
* Python 3-runbooks (preview) en pakketten werken niet met PowerShell.
* Azure Automation biedt geen ondersteuning **voor sys.stderr.**

### <a name="known-issues"></a>Bekende problemen

Python 3-taken kunnen soms niet worden uitgevoerd met een ongeldig *interpreteruitvoerbaar pad*. Mogelijk ziet u deze uitzondering als een taak is vertraagd, meer dan tien minuten wordt uitgevoerd of als u **Start-AutomationRunbook** gebruikt om Python 3-runbooks te starten. Als de taak is vertraagd, moet het opnieuw opstarten van het runbook voldoende zijn.

## <a name="next-steps"></a>Volgende stappen

* Zie Zelfstudie: Een PowerShell-runbook maken voor meer informatie over [PowerShell-runbooks.](learn/automation-tutorial-runbook-textual-powershell.md)
* Zie Zelfstudie: Een PowerShell Workflow-runbook maken voor meer informatie over [PowerShell Workflow-runbooks.](learn/automation-tutorial-runbook-textual.md)
* Zie Zelfstudie: Een grafisch runbook maken voor meer informatie [over grafische runbooks.](learn/automation-tutorial-runbook-graphical.md)
* Zie Zelfstudie: Een [Python-runbook maken voor meer informatie over Python-runbooks.](learn/automation-tutorial-runbook-textual-python2.md)
