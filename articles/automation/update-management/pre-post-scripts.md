---
title: Prescripts en post-scripts beheren in uw Updatebeheer-implementatie in Azure
description: In dit artikel wordt beschreven hoe u scripts vóór en na de scripts voor update-implementaties configureert en beheert.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51067095b7ebb33da61908b1424752b481668f5f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830802"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Scripts voorafgaand aan de back-up en scripts die erop volgen, beheren

Scripts vóór en na de scripts zijn runbooks die in uw Azure Automation-account worden uitgevoerd vóór (pre-taak) en na (post-task) een update-implementatie. Scripts vóór en na de scripts worden uitgevoerd in de Azure-context, niet lokaal. Prescripts worden uitgevoerd aan het begin van de update-implementatie. Na de scripts worden uitgevoerd aan het einde van de implementatie en na opnieuw opstarten die zijn geconfigureerd.

## <a name="pre-script-and-post-script-requirements"></a>Vereisten voor prescript en post-script

Als u een runbook wilt gebruiken als een prescript of post-script, moet u het importeren in uw Automation-account en het [runbook publiceren.](../manage-runbooks.md#publish-a-runbook)

Momenteel worden alleen PowerShell- en Python 2-runbooks ondersteund als pre-/post-scripts. Andere runbooktypen, zoals Python 3, Graphical, PowerShell Workflow en Graphical PowerShell Workflow, worden momenteel niet ondersteund als Pre/Post-scripts.

## <a name="pre-script-and-post-script-parameters"></a>Parameters pre-script en post-script

Wanneer u scripts vóór en na de scripts configureert, kunt u parameters doorgeven, net als bij het plannen van een runbook. Parameters worden gedefinieerd op het moment dat de update-implementatie wordt gemaakt. Scripts vóór en na de scripts ondersteunen de volgende typen:

* [char]
* [byte]
* [int]
* [lang]
* [decimaal]
* [enkel]
* [dubbel]
* [DateTime]
* [tekenreeks]

Runbookparameters voor prescript en post-script bieden geen ondersteuning voor booleaanse, object- of matrixtypen. Deze waarden zorgen ervoor dat de runbooks mislukken. 

Als u een ander objecttype nodig hebt, kunt u het casten naar een ander type met uw eigen logica in het runbook.

Naast de standaardrunbookparameters wordt `SoftwareUpdateConfigurationRunContext` de parameter (type JSON-tekenreeks) opgegeven. Als u de parameter definieert in uw prescript- of postscript-runbook, wordt deze automatisch doorgegeven door de update-implementatie. De parameter bevat informatie over de update-implementatie. Dit is een subset van informatie die wordt geretourneerd door de [SoftwareUpdateconfigurations-API.](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) In de onderstaande secties worden de bijbehorende eigenschappen bepaald.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Eigenschappen van SoftwareUpdateConfigurationRunContext

|Eigenschap  |Beschrijving  |
|---------|---------|
|SoftwareUpdateConfigurationName     | De naam van de configuratie van de software-update.        |
|SoftwareUpdateConfigurationRunId     | De unieke id voor de run.        |
|SoftwareUpdateConfigurationSettings     | Een verzameling eigenschappen met betrekking tot de configuratie van software-updates.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | De besturingssystemen die zijn bedoeld voor de update-implementatie.         |
|SoftwareUpdateConfigurationSettings.duration     | De maximale duur van de update-implementatie wordt `PT[n]H[n]M[n]S` uitgevoerd volgens ISO8601, ook wel het onderhoudsvenster genoemd.          |
|SoftwareUpdateConfigurationSettings.Windows     | Een verzameling eigenschappen met betrekking tot Windows-computers.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Een lijst met KB's die zijn uitgesloten van de update-implementatie.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Updateclassificaties die zijn geselecteerd voor de update-implementatie.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Instellingen voor opnieuw opstarten voor de update-implementatie.        |
|azureVirtualMachines     | Een lijst met resourceIds voor de Azure-VM's in de update-implementatie.        |
|nonAzureComputerNames|Een lijst met de FQDN's van niet-Azure-computers in de update-implementatie.|

In het volgende voorbeeld wordt een JSON-tekenreeks doorgegeven aan de parameter **SoftwareUpdateConfigurationRunContext:**

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "PT2H0M",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

Een volledig voorbeeld met alle eigenschappen vindt u op: [Configuratie van software-update op basis van naam op halen.](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Het `SoftwareUpdateConfigurationRunContext` -object kan dubbele vermeldingen voor machines bevatten. Dit kan ertoe leiden dat scripts vóór en na de scripts meerdere keren op dezelfde computer worden uitgevoerd. Als u dit gedrag wilt omdraaien, `Sort-Object -Unique` gebruikt u om alleen unieke VM-namen te selecteren.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Een prescript of post-script gebruiken in een implementatie

Als u een prescript of post-script wilt gebruiken in een update-implementatie, begint u met het maken van een update-implementatie. Selecteer **Pre-scripts + Post-Scripts.** Met deze actie wordt **de pagina Prescripts en na scripts selecteren** geopend.

![Scripts selecteren](./media/pre-post-scripts/select-scripts.png)

Selecteer het script dat u wilt gebruiken. In dit voorbeeld gebruiken we het runbook **UpdateManagement-TurnOnVms.** Wanneer u het runbook selecteert, wordt **de pagina Script configureren** geopend. Selecteer **Pre-Script** en vervolgens **OK.**

Herhaal dit proces voor het script **UpdateManagement-TurnOffVms.** Maar wanneer u het **scripttype kiest,** selecteert **u Post-Script**.

In **de sectie Geselecteerde items** ziet u nu beide geselecteerde scripts. Het ene is een prescript en het andere is een post-script:

![Geselecteerde items](./media/pre-post-scripts/selected-items.png)

De configuratie van uw update-implementatie voltooien.

Wanneer de update-implementatie is voltooid, kunt u naar **Update-implementaties gaan om** de resultaten weer te geven. Zoals u ziet, wordt de status opgegeven voor het prescript en het postscript:

![Resultaten bijwerken](./media/pre-post-scripts/update-results.png)

Als u de update-implementatierun selecteert, ziet u aanvullende details van de scripts vóór en na de scripts. Er wordt een koppeling naar de scriptbron gegeven op het moment van de run.

![Resultaten van de implementatieuit voeren](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Een implementatie stoppen

Als u een implementatie wilt stoppen op basis van een prescript, moet u [een uitzondering](../automation-runbook-execution.md#throw) geven. Als u dit niet hebt, worden de implementatie en het nascript nog steeds uitgevoerd. Het volgende codefragment laat zien hoe u een uitzondering kunt maken met behulp van PowerShell.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

In Python 2 wordt de afhandeling van uitzonderingen beheerd in een [try-blok.](https://www.python-course.eu/exception_handling.php)

## <a name="interact-with-machines"></a>Interactie met computers

Scripts vóór en na de scripts worden uitgevoerd als runbooks in uw Automation-account en niet rechtstreeks op de computers in uw implementatie. Pre-taken en post-taken worden ook uitgevoerd in de Azure-context en hebben geen toegang tot niet-Azure-machines. In de volgende secties ziet u hoe u rechtstreeks met de machines kunt werken, ongeacht of het Azure-VM's of niet-Azure-machines zijn.

### <a name="interact-with-azure-machines"></a>Interactie met Azure-machines

Pre-taken en post-taken worden uitgevoerd als runbooks en worden niet standaard uitgevoerd op uw Azure-VM's in uw implementatie. Voor interactie met uw Azure-VM's hebt u het volgende nodig:

* Een Uitvoeren als-account
* Een runbook dat u wilt uitvoeren

Als u wilt communiceren met Azure-machines, moet u de cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) gebruiken om te communiceren met uw Azure-VM's. Zie voor een voorbeeld van hoe u dit doet het [runbookvoorbeeld Updatebeheer - script uitvoeren met de opdracht Uitvoeren.](https://github.com/azureautomation/update-management-run-script-with-run-command)

### <a name="interact-with-non-azure-machines"></a>Interactie met niet-Azure-machines

Pre-taken en post-taken worden uitgevoerd in de Azure-context en hebben geen toegang tot niet-Azure-machines. Als u wilt communiceren met de niet-Azure-machines, hebt u het volgende nodig:

* Een Uitvoeren als-account
* Hybrid Runbook Worker geïnstalleerd op de computer
* Een runbook dat u lokaal wilt uitvoeren
* Een bovenliggend runbook

Voor interactie met niet-Azure-machines wordt een bovenliggend runbook uitgevoerd in de Azure-context. Met dit runbook wordt een onderliggend runbook aanroepen met de cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) U moet de parameter opgeven en de naam van de Hybrid Runbook Worker `RunOn` voor het uitvoeren van het script. Zie het runbookvoorbeeld [Updatebeheer - script lokaal uitvoeren.](https://github.com/azureautomation/update-management-run-script-locally)

## <a name="abort-patch-deployment"></a>Implementatie van patches afbreken

Als uw prescript een fout retourneert, kunt u uw implementatie afbreken. Als u dit wilt doen, [moet](/powershell/module/microsoft.powershell.core/about/about_throw) u een fout in uw script veroorzaken voor elke logica die een fout zou vormen.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Als u in Python 2 een fout wilt geven wanneer een bepaalde voorwaarde optreedt, gebruikt u een [raise-instructie.](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement)

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>Voorbeelden

Voorbeelden voor scripts vóór en na de scripts vindt u in de [GitHub-organisatie Azure Automation](https://github.com/azureautomation) en [de PowerShell Gallery,](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)of u kunt ze importeren via de Azure Portal. Selecteer in uw Automation-account onder **Procesautomatisering** de optie **Runbooks Gallery**. Gebruik **Updatebeheer** voor het filter.

![Galerielijst](./media/pre-post-scripts/runbook-gallery.png)

U kunt ze ook zoeken op de naam van hun script, zoals wordt weergegeven in de volgende lijst:

* Updatebeheer- VM's in bedrijf
* Updatebeheer- VM's uitschakelen
* Updatebeheer- Script lokaal uitvoeren
* Updatebeheer - Sjabloon voor pre-/postscripts
* Updatebeheer- Script uitvoeren met Uitvoeropdracht

> [!IMPORTANT]
> Nadat u de runbooks hebt geïmporteerd, moet u ze publiceren voordat ze kunnen worden gebruikt. Als u dit wilt doen, gaat u naar het runbook in uw Automation-account, selecteert u **Bewerken** en selecteert u **vervolgens Publiceren.**

De voorbeelden zijn allemaal gebaseerd op de basissjabloon die in het volgende voorbeeld is gedefinieerd. Deze sjabloon kan worden gebruikt om uw eigen runbook te maken voor gebruik met prescripts en postscripts. De benodigde logica voor het authenticeren met Azure en het verwerken van `SoftwareUpdateConfigurationRunContext` de parameter is opgenomen.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Voor niet-grafische PowerShell-runbooks `Add-AzAccount` en zijn `Add-AzureRMAccount` aliassen voor [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) U kunt deze cmdlets gebruiken of u kunt [uw modules bijwerken](../automation-update-azure-modules.md) naar de nieuwste versie in uw Automation-account. Zelfs wanneer u juist een nieuw Automation-account heeft aangemaakt, moet u mogelijk uw modules bijwerken.

## <a name="next-steps"></a>Volgende stappen

Zie Updates en patches voor uw [VM's beheren](manage-updates-for-vm.md)voor meer informatie over updatebeheer.
