---
title: Tekstrunbooks bewerken in Azure Automation
description: In dit artikel wordt beschreven hoe u de Azure Automation-editor gebruikt om te werken met PowerShell- en PowerShell Workflow-runbooks.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 296d45fae4d59553b54a1b68923c91be4168d3a5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829382"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Tekstrunbooks bewerken in Azure Automation

U kunt de teksteditor in Azure Automation [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) en [PowerShell Workflow-runbooks bewerken.](automation-runbook-types.md#powershell-workflow-runbooks) Deze editor bevat de typische functies van andere code-editors, zoals IntelliSense. Het maakt ook gebruik van kleurcodering met extra speciale functies om u te helpen bij het openen van resources die gemeenschappelijk zijn voor runbooks. 

De teksteditor bevat een functie voor het invoegen van code voor cmdlets, assets en onderliggende runbooks in een runbook. In plaats van de code zelf te typen, kunt u kiezen uit een lijst met beschikbare resources en voegt de editor de juiste code in het runbook in.

Elk runbook in Azure Automation heeft twee versies: Draft en Published. U bewerkt de Concept-versie van het runbook en publiceert deze vervolgens zodat deze kan worden uitgevoerd. De gepubliceerde versie kan niet worden bewerkt. Zie Een [runbook publiceren voor meer informatie.](manage-runbooks.md#publish-a-runbook)

Dit artikel bevat gedetailleerde stappen voor het uitvoeren van verschillende functies met deze editor. Deze zijn niet van toepassing op [grafische runbooks](automation-runbook-types.md#graphical-runbooks). Zie Grafisch ontwerp in Azure Automation om met deze runbooks [te Azure Automation.](automation-graphical-authoring-intro.md)

## <a name="edit-a-runbook-with-the-azure-portal"></a>Een runbook bewerken met de Azure Portal

1. Selecteer in Azure Portal Automation-account.
2. Selecteer **runbooks onder** PROCESAUTOMATISERING om de lijst met runbooks te openen. 
3. Kies het runbook dat u wilt bewerken en klik vervolgens op **Bewerken.**
4. Bewerk het runbook.
5. Klik **op Opslaan** wanneer uw bewerkingen zijn voltooid.
6. Klik **op Publiceren** als u de nieuwste conceptversie van het runbook wilt publiceren.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Een cmdlet invoegen in een runbook

1. Plaats de cursor op het canvas van de teksteditor waar u de cmdlet wilt plaatsen.
2. Vouw het **knooppunt Cmdlets** uit in het besturingselement Bibliotheek.
3. Vouw de module uit die de te gebruiken cmdlet bevat.
4. Klik met de rechtermuisknop op de naam van de cmdlet die u wilt invoegen en selecteer **Toevoegen aan canvas.** Als de cmdlet meer dan één parameterset heeft, voegt de editor de standaardset toe. U kunt de cmdlet ook uitbreiden om een andere parameterset te selecteren.
5. Houd er rekening mee dat de code voor de cmdlet wordt ingevoegd met de volledige lijst met parameters.
6. Geef een geschikte waarde op in plaats van de waarde tussen vierkante haken (<>) voor alle vereiste parameters. Verwijder parameters die u niet nodig hebt.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Code voor een onderliggend runbook invoegen in een runbook

1. Plaats op het canvas van de teksteditor de cursor op de plaats waar u de code voor het [onderliggende runbook wilt plaatsen.](automation-child-runbooks.md)
2. Vouw het **knooppunt Runbooks** in het besturingselement Bibliotheek uit.
3. Klik met de rechtermuisknop op het runbook dat u wilt invoegen en selecteer **Toevoegen aan canvas.**
4. De code voor het onderliggende runbook wordt ingevoegd met tijdelijke aanduidingen voor runbookparameters.
5. Vervang de tijdelijke aanduidingen door de juiste waarden voor elke parameter.

### <a name="insert-an-asset-into-a-runbook"></a>Een asset invoegen in een runbook

1. Plaats in het besturingselement Canvas van de teksteditor de cursor op de plaats waar u de code voor het onderliggende runbook wilt plaatsen.
2. Vouw het **knooppunt Assets** in het besturingselement Bibliotheek uit.
3. Vouw het knooppunt uit voor het gewenste assettype.
4. Klik met de rechtermuisknop op de naam van de asset om deze in te voegen en selecteer **Toevoegen aan canvas.** Voor [variabele assets](./shared-resources/variables.md)selecteert u **'Variabele** toevoegen' aan canvas of 'Variabele instellen' toevoegen aan **canvas,** afhankelijk van of u de variabele wilt op halen of instellen.
5. Houd er rekening mee dat de code voor de asset wordt ingevoegd in het runbook.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Een Azure Automation runbook bewerken met Windows PowerShell

Als u een runbook wilt bewerken met Windows PowerShell, gebruikt u de editor van uw keuze en sla u het runbook op in een **PS1-bestand.** U kunt de cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) gebruiken om de inhoud van het runbook op te halen. U kunt de cmdlet  [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) gebruiken om het bestaande conceptrunbook te vervangen door het gewijzigde runbook.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>De inhoud van een runbook ophalen met behulp van Windows PowerShell

De volgende voorbeeldopdrachten laten zien hoe u het script voor een runbook ophaalt en dit opslaat in een scriptbestand. In dit voorbeeld wordt de conceptversie opgehaald. Het is ook mogelijk om de gepubliceerde versie van het runbook op te halen, hoewel deze versie niet kan worden gewijzigd.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Wijzig de inhoud van een runbook met behulp van Windows PowerShell

De volgende voorbeeldopdrachten laten zien hoe u de bestaande inhoud van een runbook kunt vervangen door de inhoud van een scriptbestand. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

* [Runbooks beheren in Azure Automation](manage-runbooks.md).
* [PowerShell-werkstroom leren](automation-powershell-workflow.md)gebruiken.
* [Grafisch ontwerp in Azure Automation](automation-graphical-authoring-intro.md).
* [Certificaten](./shared-resources/certificates.md).
* [Verbindingen](automation-connections.md).
* [Referenties](./shared-resources/credentials.md).
* [Schema's.](./shared-resources/schedules.md)
* [Variabelen](./shared-resources/variables.md).
* [PowerShell-cmdlet-verwijzing](/powershell/module/az.automation).
