---
title: Pre-scripts en post scripts beheren in uw Updatebeheer-implementatie in azure
description: In dit artikel leest u hoe u vooraf-scripts en post scripts voor update-implementaties configureert en beheert.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.openlocfilehash: 676e5f03c8d0085a4d041662a80c63d385071919
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166714"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Scripts voorafgaand aan de back-up en scripts die erop volgen, beheren

Pre-scripts en post scripts zijn runbooks die in uw Azure Automation-account worden uitgevoerd voordat (vóór taak) en na (na taak) een update-implementatie. Scripts en post scripts die in de Azure-context worden uitgevoerd, zijn niet lokaal. Pre-scripts worden uitgevoerd aan het begin van de update-implementatie. Post-scripts worden aan het einde van de implementatie uitgevoerd en nadat opnieuw opstarten is geconfigureerd.

## <a name="pre-script-and-post-script-requirements"></a>Pre-script-en post script-vereisten

Een runbook kan alleen worden gebruikt als een pre-script of post script als u het wilt importeren in uw Automation-account en [het runbook wilt publiceren](../manage-runbooks.md#publish-a-runbook).

Op dit moment worden alleen runbooks van Power shell en Python 2 ondersteund als pre/post-scripts. Andere runbook-typen zoals python 3, grafische power shell-werk stroom, grafische power shell-werk stroom worden momenteel niet ondersteund als pre/post-scripts.

## <a name="pre-script-and-post-script-parameters"></a>Pre-script en post-script-para meters

Wanneer u pre-scripts en post scripts configureert, kunt u de para meters door geven, net zoals bij het plannen van een runbook. Para meters worden gedefinieerd op het moment dat de update-implementatie wordt gemaakt. Scripts en post scripts ondersteunen de volgende typen:

* char
* DBCS
* integer
* omvang
* komma
* afzonderlijke
* Dubbelklik
* DateTime
* tekenreeksexpressie

De runbook-para meters pre-script en post-script bieden geen ondersteuning voor Boole-, object-of matrix typen. Deze waarden zorgen ervoor dat de runbooks mislukken. 

Als u een ander object type nodig hebt, kunt u dit naar een ander type converteren met uw eigen logica in het runbook.

Naast uw standaard runbook-para meters `SoftwareUpdateConfigurationRunContext` wordt de para meter (type JSON-teken reeks) opgegeven. Als u de para meter in uw pre-script of post-script-runbook definieert, wordt deze automatisch door gegeven door de update-implementatie. De para meter bevat informatie over de update-implementatie. Dit is een subset van informatie die wordt geretourneerd door de [SoftwareUpdateconfigurations-API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). In de volgende secties worden de bijbehorende eigenschappen gedefinieerd.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext-eigenschappen

|Eigenschap  |Beschrijving  |
|---------|---------|
|SoftwareUpdateConfigurationName     | De naam van de configuratie van de software-update.        |
|SoftwareUpdateConfigurationRunId     | De unieke ID voor de uitvoering.        |
|SoftwareUpdateConfigurationSettings     | Een verzameling eigenschappen die betrekking hebben op de configuratie van de software-update.         |
|SoftwareUpdateConfigurationSettings. Operating     | De besturings systemen die zijn gericht op de update-implementatie.         |
|SoftwareUpdateConfigurationSettings. duur     | De maximale duur van de update-implementatie zoals `PT[n]H[n]M[n]S` iso8601, ook wel het onderhouds venster genoemd.          |
|SoftwareUpdateConfigurationSettings. Windows     | Een verzameling eigenschappen die betrekking hebben op Windows-computers.         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | Een lijst met Kb's die zijn uitgesloten van de update-implementatie.        |
|SoftwareUpdateConfigurationSettings. Windows. includedUpdateClassifications     | Update classificaties geselecteerd voor de update-implementatie.        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | Instellingen voor de update-implementatie opnieuw opstarten.        |
|azureVirtualMachines     | Een lijst met resourceIds voor de virtuele Azure-machines in de update-implementatie.        |
|nonAzureComputerNames|Een lijst met de FQDN-namen van niet-Azure-computers in de update-implementatie.|

Het volgende voor beeld is een JSON-teken reeks die is door gegeven aan de para meter **SoftwareUpdateConfigurationRunContext** :

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

Een volledig voor beeld met alle eigenschappen vindt u op: [Software-update configuratie op naam ophalen](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Het `SoftwareUpdateConfigurationRunContext` object kan dubbele vermeldingen voor machines bevatten. Dit kan ervoor zorgen dat scripts en post scripts meerdere keren op dezelfde computer worden uitgevoerd. U kunt dit probleem omzeilen door `Sort-Object -Unique` alleen unieke VM-namen te selecteren.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Een pre-script of post script gebruiken in een implementatie

Als u een pre-script of post script wilt gebruiken in een update-implementatie, moet u beginnen met het maken van een update-implementatie. Selecteer **pre-scripts + post scripts**. Met deze actie opent **u de pagina pre-scripts + post-scripts selecteren** .

![Scripts selecteren](./media/pre-post-scripts/select-scripts.png)

Selecteer het script dat u wilt gebruiken. In dit voor beeld gebruiken we het runbook **UpdateManagement-TurnOnVms** . Wanneer u het runbook selecteert, wordt de pagina **script configureren** geopend. Selecteer **vooraf script** en selecteer vervolgens **OK**.

Herhaal dit proces voor het script **UpdateManagement-TurnOffVms** . Maar wanneer u het **script type** kiest, selecteert u **post-script**.

In het gedeelte **geselecteerde items** worden nu de geselecteerde scripts weer gegeven. Een is een pre-script en de andere is een post script:

![Geselecteerde items](./media/pre-post-scripts/selected-items.png)

De configuratie van de update-implementatie volt ooien.

Wanneer de update-implementatie is voltooid, kunt u naar **Update-implementaties** gaan om de resultaten weer te geven. Zoals u kunt zien, is de status van het pre-script en het post script:

![Resultaten bijwerken](./media/pre-post-scripts/update-results.png)

Als u de update-implementatie-uitvoering selecteert, worden er aanvullende details van pre-scripts en post scripts weer gegeven. Er wordt een koppeling naar de script bron op het moment van de uitvoering gegeven.

![Resultaten van implementatie-uitvoering](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Een implementatie stoppen

Als u een implementatie op basis van een pre-script wilt stoppen, moet u een uitzonde ring [genereren](../automation-runbook-execution.md#throw) . Als dat niet het geval is, worden de implementatie en het post script nog steeds uitgevoerd. Het volgende code fragment laat zien hoe u een uitzonde ring genereert met behulp van Power shell.

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

In Python 2 wordt de verwerking van uitzonde ringen beheerd in een [try](https://www.python-course.eu/exception_handling.php) -blok.

## <a name="interact-with-machines"></a>Communiceren met computers

Scripts en post scripts worden uitgevoerd als runbooks in uw Automation-account en niet rechtstreeks op de computers in uw implementatie. Taken en post-taken worden ook uitgevoerd in de Azure-context en hebben geen toegang tot niet-Azure-machines. In de volgende secties ziet u hoe u rechtstreeks met de computers kunt werken, ongeacht of het nu virtuele machines van Azure of niet-Azure is.

### <a name="interact-with-azure-machines"></a>Communiceren met Azure-machines

Pre-tasks en post-tasks worden uitgevoerd als runbooks en worden niet systeem eigen uitgevoerd op uw virtuele Azure-machines in uw implementatie. Als u met uw Azure-Vm's wilt communiceren, hebt u de volgende items nodig:

* Een uitvoeren als-account
* Een runbook dat u wilt uitvoeren

Als u wilt communiceren met Azure-machines, moet u de cmdlet [invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) gebruiken om te communiceren met uw Azure-vm's. Zie voor een voor beeld van hoe u dit doet, het runbook [-voor beeld updatebeheer script uitvoeren met de opdracht Run](https://github.com/azureautomation/update-management-run-script-with-run-command).

### <a name="interact-with-non-azure-machines"></a>Communiceren met niet-Azure-machines

Taken en taken die na uitvoering worden uitgevoerd in de Azure-context en die geen toegang hebben tot niet-Azure-machines. Als u wilt communiceren met de niet-Azure-computers, hebt u de volgende items nodig:

* Een uitvoeren als-account
* Hybrid Runbook Worker geïnstalleerd op de computer
* Een runbook dat u lokaal wilt uitvoeren
* Een bovenliggend runbook

Een bovenliggend runbook wordt uitgevoerd in de Azure-context om te communiceren met niet-Azure-machines. Met dit runbook wordt een onderliggend runbook aangeroepen met de cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) . U moet de `RunOn` para meter opgeven en de naam opgeven van de Hybrid Runbook worker waarmee het script moet worden uitgevoerd. Zie het runbook-voor beeld [updatebeheer script lokaal uitvoeren](https://github.com/azureautomation/update-management-run-script-locally).

## <a name="abort-patch-deployment"></a>Patch implementatie afbreken

Als uw pre-script een fout retourneert, wilt u uw implementatie mogelijk afbreken. Hiervoor moet u een fout in het script [genereren](/powershell/module/microsoft.powershell.core/about/about_throw) voor eventuele logica die een fout zou vormen.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Als u in Python 2 een fout wilt genereren wanneer zich een bepaalde voor waarde voordoet, gebruikt u een instructie [Raise](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement) .

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>Voorbeelden

Voor beelden voor pre-scripts en post-scripts vindt u in de [Azure Automation github-organisatie](https://github.com/azureautomation) en de [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), of u kunt ze importeren via de Azure Portal. Als u dit wilt doen, selecteert u in uw Automation-account, onder **proces automatisering**, **Runbooks galerie**. Gebruik **updatebeheer** voor het filter.

![Galerie lijst](./media/pre-post-scripts/runbook-gallery.png)

U kunt ook zoeken naar de naam van het script, zoals wordt weer gegeven in de volgende lijst:

* Updatebeheer-Vm's inschakelen
* Updatebeheer-Vm's uitschakelen
* Updatebeheer script lokaal uitvoeren
* Updatebeheer-sjabloon voor pre/post-scripts
* Updatebeheer script uitvoeren met de opdracht uitvoeren

> [!IMPORTANT]
> Nadat u de runbooks hebt geïmporteerd, moet u deze publiceren voordat ze kunnen worden gebruikt. Hiertoe gaat u naar het runbook in uw Automation-account, selecteert u **bewerken** en selecteert u vervolgens **publiceren**.

De voor beelden zijn gebaseerd op de basis sjabloon die in het volgende voor beeld is gedefinieerd. Deze sjabloon kan worden gebruikt om uw eigen runbook te maken voor gebruik met pre-scripts en post scripts. De benodigde logica voor verificatie met Azure en het verwerken van de `SoftwareUpdateConfigurationRunContext` para meter is opgenomen.

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
> Voor niet-grafische power shell-runbooks `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). U kunt deze cmdlets gebruiken of u kunt [uw modules bijwerken](../automation-update-azure-modules.md) naar de nieuwste versie in uw Automation-account. Zelfs wanneer u juist een nieuw Automation-account heeft aangemaakt, moet u mogelijk uw modules bijwerken.

## <a name="next-steps"></a>Volgende stappen

Zie [updates en patches voor uw virtuele machines beheren](manage-updates-for-vm.md)voor meer informatie over update beheer.
