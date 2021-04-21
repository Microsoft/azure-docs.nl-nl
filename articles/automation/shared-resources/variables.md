---
title: Variabelen beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u werkt met variabelen in runbooks en DSC-configuraties.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f1ace00356583dbb6102317e3d157fb58682710
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832575"
---
# <a name="manage-variables-in-azure-automation"></a>Variabelen beheren in Azure Automation

Variabele assets zijn waarden die beschikbaar zijn voor alle runbooks en DSC-configuraties in uw Automation-account. U kunt deze beheren vanuit Azure Portal, vanuit PowerShell, binnen een runbook of in een DSC-configuratie.

Automation-variabelen zijn handig voor de volgende scenario's:

- Een waarde delen tussen meerdere runbooks of DSC-configuraties.

- Een waarde delen tussen meerdere taken uit hetzelfde runbook of DSC-configuratie.

- Een waarde beheren die wordt gebruikt door runbooks of DSC-configuraties vanuit de portal of vanaf de PowerShell-opdrachtregel. Een voorbeeld is een set algemene configuratie-items, zoals een specifieke lijst met VM-namen, een specifieke resourcegroep, een AD-domeinnaam en meer.  

Azure Automation worden variabelen persistent gemaakt en beschikbaar gemaakt, zelfs als een runbook- of DSC-configuratie mislukt. Met dit gedrag kan één runbook of DSC-configuratie een waarde instellen die vervolgens wordt gebruikt door een ander runbook, of door hetzelfde runbook of DSC-configuratie de volgende keer dat het wordt uitgevoerd.

Azure Automation elke versleutelde variabele veilig opgeslagen. Wanneer u een variabele maakt, kunt u de versleuteling en opslag ervan Azure Automation als een beveiligde asset. Nadat u de variabele hebt gemaakt, kunt u de versleutelingsstatus ervan niet meer wijzigen zonder de variabele opnieuw te maken. Als u met Automation-accountvariabelen gevoelige gegevens opslaat die nog niet zijn versleuteld, moet u deze verwijderen en opnieuw maken als versleutelde variabelen. Azure Security Center raadt aan om alle Azure Automation-variabelen te versleutelen, zoals beschreven in [Automation-accountvariabelen moeten worden versleuteld](../../security-center/recommendations-reference.md#recs-compute). Als u niet-versleutelde variabelen hebt die u wilt uitsluiten van deze beveiligingsaanbeveling, raadpleegt u [Een resource uitsluiten van aanbevelingen en de beveiligingsscore](../../security-center/exempt-resource.md) om een uitzonderingsregel te maken.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Azure Automation slaat de sleutel op in de door het systeem beheerde Key Vault. Voordat een beveiligde asset wordt opgeslagen, laadt Automation de sleutel van Key Vault gebruikt om de asset te versleutelen.

## <a name="variable-types"></a>Variabeletypen

Wanneer u een variabele maakt met de Azure Portal, moet u een gegevenstype opgeven in de vervolgkeuzelijst, zodat in de portal het juiste besturingselement voor het invoeren van de variabele waarde kan worden weergegeven. Hier volgen de variabeletypen die beschikbaar zijn in Azure Automation:

* Tekenreeks
* Geheel getal
* DateTime
* Booleaans
* Null

De variabele is niet beperkt tot het opgegeven gegevenstype. U moet de variabele instellen met Windows PowerShell als u een waarde van een ander type wilt opgeven. Als u `Not defined` aangeeft, wordt de waarde van de variabele ingesteld op Null. U moet de waarde instellen met de cmdlet [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) of de `Set-AutomationVariable` interne cmdlet . U gebruikt de `Set-AutomationVariable` in uw runbooks die zijn bedoeld om te worden uitgevoerd in de Azure-sandboxomgeving of op een Windows-Hybrid Runbook Worker.

U kunt de Azure Portal gebruiken om de waarde voor een complex variabeletype te maken of te wijzigen. U kunt echter een waarde van elk type met behulp van Windows PowerShell. Complexe typen worden opgehaald als een [Newtonsoft.Jsop. Linq.JProperty voor](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) een complex objecttype in plaats van een PSObject-type [PSCustomObject.](/dotnet/api/system.management.automation.pscustomobject)

U kunt meerdere waarden opslaan in één variabele door een matrix of hashtabel te maken en deze op te slaan in de variabele .

>[!NOTE]
>VM-naamvariabelen mogen maximaal 80 tekens bevatten. Resourcegroepvariabelen mogen maximaal 90 tekens bevatten. Zie [Naamgevingsregels en -beperkingen voor Azure-resources.](../../azure-resource-manager/management/resource-name-rules.md)

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell-cmdlets voor toegang tot variabelen

De cmdlets in de volgende tabel maken en beheren Automation-variabelen met PowerShell. Ze worden als onderdeel van de [Az-modules verzenden.](modules.md#az-modules)

| Cmdlet | Beschrijving |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Hiermee haalt u de waarde van een bestaande variabele op. Als de waarde een eenvoudig type is, wordt datzelfde type opgehaald. Als het een complex type is, wordt `PSCustomObject` een type opgehaald. <sup>1</sup>|
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Hiermee maakt u een nieuwe variabele en stelt u de waarde ervan in.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Hiermee verwijdert u een bestaande variabele.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Hiermee stelt u de waarde voor een bestaande variabele in. |

<sup>1</sup> U kunt deze cmdlet niet gebruiken om de waarde van een versleutelde variabele op te halen. De enige manier om dit te doen, is door de interne `Get-AutomationVariable` cmdlet in een runbook of DSC-configuratie te gebruiken. Als u bijvoorbeeld de waarde van een versleutelde variabele wilt zien, kunt u een runbook maken om de variabele op te halen en deze vervolgens naar de uitvoerstroom te schrijven:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>Interne cmdlets voor toegang tot variabelen

De interne cmdlets in de volgende tabel worden gebruikt voor toegang tot variabelen in uw runbooks en DSC-configuraties. Deze cmdlets worden bij de globale module `Orchestrator.AssetManagement.Cmdlets` gebruikt. Zie Interne [cmdlets voor meer informatie.](modules.md#internal-cmdlets)

| Interne cmdlet | Description |
|:---|:---|
|`Get-AutomationVariable`|Hiermee haalt u de waarde van een bestaande variabele op.|
|`Set-AutomationVariable`|Hiermee stelt u de waarde voor een bestaande variabele in.|

> [!NOTE]
> Vermijd het gebruik van variabelen in `Name` de parameter van de `Get-AutomationVariable` cmdlet in een runbook- of DSC-configuratie. Het gebruik van een variabele kan de detectie van afhankelijkheden tussen runbooks en Automation-variabelen tijdens de ontwerptijd bemoeilijken.

## <a name="python-functions-to-access-variables"></a>Python-functies voor toegang tot variabelen

De functies in de volgende tabel worden gebruikt voor toegang tot variabelen in een Python 2- en 3-runbook. Python 3-runbooks zijn momenteel beschikbaar als preview-versie.

|Python Functions|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Hiermee haalt u de waarde van een bestaande variabele op. |
|`automationassets.set_automation_variable`|Hiermee stelt u de waarde voor een bestaande variabele in. |

> [!NOTE]
> U moet de `automationassets` module bovenaan uw Python-runbook importeren om toegang te krijgen tot de assetfuncties.

## <a name="create-and-get-a-variable"></a>Een variabele maken en op halen

>[!NOTE]
>Als u de versleuteling voor een variabele wilt verwijderen, moet u de variabele verwijderen en opnieuw maken als niet-versleuteld.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Maak een variabele en haal deze op met behulp van Azure Portal

1. Selecteer in uw Automation-account in het linkerdeelvenster **Variabelen onder** **Gedeelde resources.**
2. Selecteer op **de pagina Variabelen** de optie Een variabele **toevoegen.**
3. Voltooi de opties op de **pagina Nieuwe variabele** en selecteer vervolgens Maken **om** de nieuwe variabele op te slaan.

> [!NOTE]
> Nadat u een versleutelde variabele hebt opgeslagen, kan deze niet meer worden bekeken in de portal. Deze kan alleen worden bijgewerkt.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Maak en haal een variabele op in Windows PowerShell

Uw runbook of DSC-configuratie gebruikt de cmdlet om een nieuwe variabele te maken `New-AzAutomationVariable` en de initiële waarde in te stellen. Als de variabele is versleuteld, moet de aanroep de `Encrypted` parameter gebruiken. Met uw script kan de waarde van de variabele worden opgehaald met behulp van `Get-AzAutomationVariable` .

>[!NOTE]
>Een PowerShell-script kan geen versleutelde waarde ophalen. De enige manier om dit te doen, is met de interne `Get-AutomationVariable` cmdlet .

In het volgende voorbeeld ziet u hoe u een tekenreeksvariabele maakt en vervolgens de waarde retourneert.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"
$variableValue = "My String"

New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable' `
-Encrypted $false -Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable').Value
```

In het volgende voorbeeld ziet u hoe u een variabele met een complex type maakt en vervolgens de eigenschappen ervan op haalt. In dit geval wordt een virtuele-machineobject van [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) gebruikt om een subset van de eigenschappen op te geven.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" -Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" -AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable" -Encrypted $false -Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable"

$vmName = $vmValue.Value.Name
$vmTags = $vmValue.Value.Tags
```

## <a name="textual-runbook-examples"></a>Voorbeelden van tekstrunbook

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende voorbeeld ziet u hoe u een variabele in een tekstrunbook in kunt stellen en ophalen. In dit voorbeeld wordt ervan uitgenomen dat variabelen met een geheel getal met de naam **numberOfIterations** en **numberOfRunnings** en een tekenreeksvariabele met de **naam sampleMessage worden gemaakt.**

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$numberOfIterations = Get-AutomationVariable -Name "numberOfIterations"
$numberOfRunnings = Get-AutomationVariable -Name "numberOfRunnings"
$sampleMessage = Get-AutomationVariable -Name "sampleMessage"

Write-Output "Runbook has been run $numberOfRunnings times."

for ($i = 1; $i -le $numberOfIterations; $i++) {
    Write-Output "$i`: $sampleMessage"
}
Set-AutomationVariable -Name numberOfRunnings -Value ($numberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

In het volgende voorbeeld ziet u hoe u een variabele op kunt halen, een variabele kunt instellen en een uitzondering voor een niet-bestaande variabele in een Python 2-runbook kunt verwerken.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

# <a name="python-3"></a>[Python 3](#tab/python3)

In het volgende voorbeeld ziet u hoe u een variabele op kunt halen, een variabele kunt instellen en een uitzondering kunt afhandelen voor een niet-bestaande variabele in een Python 3-runbook (preview).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Grafische runbookvoorbeelden

In een grafisch runbook kunt u activiteiten toevoegen voor de interne cmdlets **Get-AutomationVariable** of **Set-AutomationVariable.** Klik met de rechtermuisknop op elke variabele in het deelvenster Bibliotheek van de grafische editor en selecteer de activiteit die u wilt.

![Variabele toevoegen aan canvas](../media/variables/runbook-variable-add-canvas.png)

In de volgende afbeelding ziet u voorbeeldactiviteiten voor het bijwerken van een variabele met een eenvoudige waarde in een grafisch runbook. In dit voorbeeld haalt de activiteit voor één virtuele Azure-machine op en slaat de computernaam op in een bestaande `Get-AzVM` Automation-tekenreeksvariabele. Het maakt niet uit of de [koppeling een pijplijn of](../automation-graphical-authoring-intro.md#use-links-for-workflow) reeks is, omdat in de code slechts één object in de uitvoer wordt verwacht.

![Eenvoudige variabele instellen](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Volgende stappen

- Zie Modules beheren in Azure Automation voor meer informatie over de cmdlets die worden gebruikt voor toegang [tot variabelen.](modules.md)

- Zie Runbookuitvoering in Azure Automation voor [algemene informatie over runbooks.](../automation-runbook-execution.md)

- Zie overzicht van Azure Automation State Configuration [DSC-configuraties.](../automation-dsc-overview.md)
