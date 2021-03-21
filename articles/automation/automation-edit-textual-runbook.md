---
title: De tekst runbooks in Azure Automation bewerken
description: In dit artikel leest u hoe u de Azure Automation tekstuele editor gebruikt om met Power shell-en Power shell-werk stroom runbooks te werken.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8d6b786ffaf309e147de27e8cd8be314a3d8a5fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896983"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>De tekst runbooks in Azure Automation bewerken

U kunt de tekst editor in Azure Automation gebruiken om [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks) en [Power shell workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks)te bewerken. Deze editor bevat de typische functies van andere code-editors, zoals IntelliSense. Daarnaast wordt kleur codering gebruikt met extra speciale functies om u te helpen bij het openen van resources die gemeen schappelijk zijn voor runbooks. 

De tekst editor bevat een functie voor het invoegen van code voor cmdlets, assets en onderliggende runbooks in een runbook. In plaats van de code zelf zelf te typen, kunt u een keuze uit een lijst met beschik bare resources selecteren. in de editor wordt de juiste code in het runbook ingevoegd.

Elk runbook in Azure Automation heeft twee versies: concept en gepubliceerd. U bewerkt de concept versie van het runbook en publiceert deze zodat het kan worden uitgevoerd. De gepubliceerde versie kan niet worden bewerkt. Zie [een Runbook publiceren](manage-runbooks.md#publish-a-runbook)voor meer informatie.

In dit artikel vindt u gedetailleerde stappen voor het uitvoeren van verschillende functies met deze editor. Deze zijn niet van toepassing op [grafische runbooks](automation-runbook-types.md#graphical-runbooks). Als u met deze runbooks wilt werken, raadpleegt u [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Een runbook bewerken met de Azure Portal

1. Selecteer uw Automation-account in het Azure Portal.
2. Selecteer **runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.
3. Kies het runbook dat u wilt bewerken en klik vervolgens op **bewerken**.
4. Bewerk het runbook.
5. Klik op **Opslaan** wanneer uw bewerkingen zijn voltooid.
6. Klik op **publiceren** als u de laatste concept versie van het runbook wilt publiceren.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Een cmdlet in een runbook invoegen

1. In het canvas van de tekst editor plaatst u de cursor waar u de cmdlet wilt plaatsen.
2. Vouw het knoop punt **cmdlets** uit in het besturings element bibliotheek.
3. Vouw de module uit die de cmdlet bevat die moet worden gebruikt.
4. Klik met de rechter muisknop op de naam van de cmdlet die u wilt invoegen en selecteer **toevoegen aan canvas**. Als de cmdlet meer dan een parameterset heeft, voegt de editor de standaardset toe. U kunt ook de cmdlet uitvouwen om een andere parameterset te selecteren.
5. Houd er rekening mee dat de code voor de cmdlet wordt ingevoegd met de volledige lijst met para meters.
6. Geef een geschikte waarde op in plaats van de waarde tussen punt haken (<>) voor vereiste para meters. Verwijder alle para meters die u niet nodig hebt.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Code voor een onderliggend runbook in een runbook invoegen

1. Plaats de cursor in het canvas van de tekst editor op de plaats waar u de code voor het [onderliggende runbook](automation-child-runbooks.md)wilt plaatsen.
2. Vouw het knoop punt **Runbooks** uit in het besturings element bibliotheek.
3. Klik met de rechter muisknop op het runbook dat u wilt invoegen en selecteer **toevoegen aan canvas**.
4. De code voor het onderliggende runbook wordt ingevoegd met eventuele tijdelijke aanduidingen voor runbook-para meters.
5. Vervang de tijdelijke aanduidingen door de juiste waarden voor elke para meter.

### <a name="insert-an-asset-into-a-runbook"></a>Een asset in een runbook invoegen

1. In het besturings element canvas van de tekst editor plaatst u de cursor waar u de code voor het onderliggende runbook wilt plaatsen.
2. Vouw het knoop punt **assets** uit in het besturings element bibliotheek.
3. Vouw het knoop punt uit voor het gewenste type activa.
4. Klik met de rechter muisknop op de naam van de Asset die u wilt invoegen en selecteer **toevoegen aan canvas**. Voor [variabele assets](./shared-resources/variables.md)selecteert u **' variabele ophalen ' toevoegen aan canvas** of **' variabele instellen ' toevoegen aan canvas**, afhankelijk van of u de variabele wilt ophalen of instellen.
5. Houd er rekening mee dat de code voor de Asset wordt ingevoegd in het runbook.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Een Azure Automation runbook bewerken met behulp van Windows Power shell

Als u een runbook wilt bewerken met Windows Power shell, gebruikt u de editor van uw keuze en slaat u het runbook op in een **. ps1** -bestand. U kunt de cmdlet [export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) gebruiken om de inhoud van het runbook op te halen. U kunt de cmdlet  [import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) gebruiken om het bestaande concept runbook te vervangen door de gewijzigde.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>De inhoud van een runbook ophalen met behulp van Windows Power shell

De volgende voorbeeldopdrachten laten zien hoe u het script voor een runbook ophaalt en dit opslaat in een scriptbestand. In dit voorbeeld wordt de conceptversie opgehaald. Het is ook mogelijk om de gepubliceerde versie van het runbook op te halen, maar deze versie kan niet worden gewijzigd.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>De inhoud van een runbook wijzigen met Windows Power shell

De volgende voorbeeld opdrachten laten zien hoe u de bestaande inhoud van een runbook vervangt door de inhoud van een script bestand. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

* [Runbooks in azure Automation beheren](manage-runbooks.md).
* [Power shell-werk stroom leren](automation-powershell-workflow.md).
* [Grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).
* [Certificaten](./shared-resources/certificates.md).
* [Verbindingen](automation-connections.md).
* [Referenties](./shared-resources/credentials.md).
* [Schema's](./shared-resources/schedules.md).
* [Variabelen](./shared-resources/variables.md).
* [Naslag informatie over Power shell-cmdlets](/powershell/module/az.automation).
