---
title: Schema's beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u een planning in een Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/29/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f119c15cbbfd9586bdb06fdffad0b12c9a441eea
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832640"
---
# <a name="manage-schedules-in-azure-automation"></a>Schema's beheren in Azure Automation

Als u een runbook in een Azure Automation om te beginnen op een opgegeven tijdstip, koppelt u het aan een of meer schema's. Een planning kan worden geconfigureerd om één keer of volgens een terugkerend uur- of dagelijks schema voor runbooks in de Azure Portal. U kunt ze ook plannen voor wekelijkse, maandelijkse, specifieke dagen van de week of dagen van de maand of een bepaalde dag van de maand. Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld.

> [!NOTE]
> Azure Automation biedt ondersteuning voor zomer- en wintertijd en wordt deze op de juiste manier gepland voor automatiseringsbewerkingen.

> [!NOTE]
> Schema's zijn momenteel niet ingeschakeld voor Azure Automation DSC-configuraties.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>PowerShell-cmdlets die worden gebruikt voor toegang tot schema's

De cmdlets in de volgende tabel maken en beheren Automation-schema's met PowerShell. Ze worden als onderdeel van de [Az-modules verzenden.](modules.md#az-modules)

| Cmdlets | Description |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Hiermee haalt u een schema op. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Hiermee worden geplande runbooks opgehaald. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Hiermee maakt u een nieuwe planning. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Koppelt een runbook aan een schema. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Hiermee verwijdert u een schema. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Hiermee stelt u de eigenschappen voor een bestaande planning. |
| [Registratie van AzAutomationScheduledRunbook ongedaan maken](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Een runbook wordt losgekoppeld van een schema. |

## <a name="create-a-schedule"></a>Een planning maken

U kunt een nieuw schema voor uw runbooks maken vanuit de Azure Portal, met PowerShell of met behulp van een Azure Resource Manager (ARM)-sjabloon. Om te voorkomen dat dit van invloed is op uw runbooks en de processen die ze automatiseren, moet u eerst alle runbooks met gekoppelde planningen testen met een Automation-account dat is toegewezen voor testen. Een test controleert of uw geplande runbooks correct blijven werken. Als u een probleem ziet, kunt u eventuele vereiste wijzigingen oplossen en toepassen voordat u de bijgewerkte runbookversie naar productie migreert.

> [!NOTE]
> Uw Automation-account krijgt niet automatisch nieuwe versies van modules, tenzij u deze handmatig hebt bijgewerkt door de optie [Azure-modules](../automation-update-azure-modules.md) bijwerken te selecteren in **Modules.** Azure Automation maakt gebruik van de nieuwste modules in uw Automation-account wanneer een nieuwe geplande taak wordt uitgevoerd. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Maak een nieuw schema in de Azure Portal

1. Selecteer in uw Automation-account in het **linkerdeelvenster Planningen** onder **Gedeelde resources.**
2. Selecteer op **de pagina Schema's** **de optie Een schema toevoegen.**
3. Voer op **de pagina** Nieuwe planning een naam in en voer eventueel een beschrijving in voor de nieuwe planning.

    >[!NOTE]
    >Automatiseringsschema's bieden momenteel geen ondersteuning voor het gebruik van speciale tekens in de naam van de planning.
    >

4. Selecteer of het schema eenmaal of volgens een terugkerend schema wordt uitgevoerd door **Eenmaal** of Terugkerend **te selecteren.** Als u Eenmaal **selecteert,** geeft u een begintijd op en selecteert u **vervolgens Maken.** Als u **Terugkerend selecteert,** geeft u een begintijd op. Selecteer **bij Elke herhalen** hoe vaak u wilt dat het runbook wordt herhaald. Selecteer per uur, dag, week of maand.

    * Als u **Week selecteert,** kunt u kiezen uit de dagen van de week. Selecteer zoveel dagen als u wilt. De eerste keer dat uw planning wordt uitgevoerd, vindt plaats op de eerste dag die is geselecteerd na de begintijd. Als u bijvoorbeeld een weekendschema wilt kiezen, selecteert u Zaterdag en Zondag.

    ![Terugkerende planning in het weekend instellen](../media/schedules/week-end-weekly-recurrence.png)

    * Als u **Maand selecteert,** krijgt u verschillende opties. Voor de **optie Maandelijkse exemplaren** selecteert u **Maanddagen** of **Weekdagen.** Als u **Maanddagen selecteert,** wordt er een kalender weergegeven, zodat u zoveel dagen kunt kiezen als u wilt. Als u een datum kiest, zoals de 31e die niet in de huidige maand voorkomt, wordt de planning niet uitgevoerd. Als u wilt dat het schema op de laatste dag wordt uitgevoerd, selecteert u **Ja** onder **Uitvoeren op de laatste dag van de maand.** Als u **Weekdagen selecteert,** wordt de optie Elke **recur** weergegeven. Kies **eerste,** **tweede,** **derde,** **vierde** of **laatste**. Kies ten slotte een dag om op te herhalen.

    ![Maandelijks schema op de eerste, vijftiende en laatste dag van de maand](../media/schedules/monthly-first-fifteenth-last.png)

5. Wanneer u klaar bent, selecteert u **Maken**.

### <a name="create-a-new-schedule-with-powershell"></a>Een nieuw schema maken met PowerShell

Gebruik de [cmdlet New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) om schema's te maken. U geeft de begintijd voor de planning en de frequentie op die moet worden uitgevoerd. De volgende voorbeelden laten zien hoe u veel verschillende planningsscenario's kunt maken.

>[!NOTE]
>Automatiseringsschema's bieden momenteel geen ondersteuning voor het gebruik van speciale tekens in de naam van de planning.
>

#### <a name="create-a-one-time-schedule"></a>Een een time-time-planning maken

In het volgende voorbeeld wordt een een time-schedule gemaakt.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Een terugkerend schema maken

In het volgende voorbeeld ziet u hoe u een terugkerend schema maakt dat een jaar lang elke dag om 13:00 uur wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Een wekelijks terugkerend schema maken

In het volgende voorbeeld ziet u hoe u een wekelijkse planning maakt die alleen op weekdagen wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Een wekelijks terugkerend schema voor het weekend maken

In het volgende voorbeeld ziet u hoe u een wekelijkse planning maakt die alleen in het weekend wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Een terugkerend schema maken voor de eerste, vijftiende en laatste dag van de maand

In het volgende voorbeeld ziet u hoe u een terugkerend schema maakt dat wordt uitgevoerd op de eerste, vijftiende en laatste dag van een maand.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Een planning maken met een Resource Manager sjabloon

In dit voorbeeld gebruiken we een ARM-sjabloon (Automation Resource Manager) die een nieuwe taakplanning maakt. Zie [Microsoft.Automation AutomationAccounts/jobSchedules template reference (Microsoft.Automation automationAccounts/jobSchedules-sjabloonverwijzing)](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates)voor algemene informatie over deze sjabloon voor het beheren van Automation-taakschema's.

Kopieer dit sjabloonbestand naar een teksteditor:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Bewerk de volgende parameterwaarden en sla de sjabloon op als een JSON-bestand:

* Objectnaam van taakplanning: Er wordt een GUID (Globally Unique Identifier) gebruikt als de naam van het taakplanningsobject.

   >[!IMPORTANT]
   > Voor elke taakplanning die is geïmplementeerd met een ARM-sjabloon, moet de GUID uniek zijn. Zelfs als u een bestaande planning wilt plannen, moet u de GUID wijzigen. Dit geldt zelfs als u eerder een bestaande taakplanning hebt verwijderd die met dezelfde sjabloon is gemaakt. Het hergebruiken van dezelfde GUID resulteert in een mislukte implementatie.</br></br>
   > Er zijn online services die een nieuwe GUID voor u kunnen genereren, zoals deze [gratis online GUID Generator.](https://guidgenerator.com/)

* Naam van planning: Vertegenwoordigt de naam van de Automation-taakplanning die wordt gekoppeld aan het opgegeven runbook.
* Runbooknaam: Vertegenwoordigt de naam van het Automation-runbook waar de taakplanning aan moet worden gekoppeld.

Zodra het bestand is opgeslagen, kunt u het runbook-taakschema maken met de volgende PowerShell-opdracht. De opdracht gebruikt de `TemplateFile` parameter om het pad en de bestandsnaam van de sjabloon op te geven.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Een planning koppelen aan een runbook

Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld. Als een runbook parameters bevat, kunt u er waarden voor opgeven. U moet waarden opgeven voor verplichte parameters en u kunt ook waarden opgeven voor eventuele optionele parameters. Deze waarden worden gebruikt telkens als het runbook door dit schema wordt gestart. U kunt hetzelfde runbook aan een andere planning koppelen en verschillende parameterwaarden opgeven.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Een planning koppelen aan een runbook met de Azure Portal

1. Selecteer in Azure Portal Automation-account **runbooks onder** **Procesautomatisering.**
1. Selecteer de naam van het runbook dat u wilt plannen.
1. Als het runbook momenteel niet is gekoppeld aan een schema, krijgt u de optie om een nieuwe planning of koppeling naar een bestaande planning te maken.
1. Als het runbook parameters bevat, kunt u de optie **Runinstellingen wijzigen (Standaard:Azure)** selecteren en wordt **het deelvenster Parameters** weergegeven. U kunt hier parametergegevens invoeren.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Een planning koppelen aan een runbook met PowerShell

Gebruik de cmdlet [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) om een schema te koppelen. U kunt waarden voor de parameters van het runbook opgeven met de parameter Parameters . Zie Starting a Runbook in Azure Automation voor meer informatie over het [opgeven van parameterwaarden.](../start-runbooks.md)
In het volgende voorbeeld ziet u hoe u een planning aan een runbook koppelt met behulp van een Azure Resource Manager cmdlet met parameters.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ScheduleName $scheduleName -Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Plannen dat runbooks vaker worden uitgevoerd

Het meest frequente interval waarvoor een planning in Azure Automation kan worden geconfigureerd, is één uur. Als schema's vaker moeten worden uitgevoerd, zijn er twee opties:

* Maak een [webhook voor](../automation-webhooks.md) het runbook en gebruik [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) om de webhook aan te roepen. Azure Logic Apps biedt gedetailleerdere granulariteit om een planning te definiëren.

* Maak vier planningen die allemaal binnen 15 minuten van elkaar beginnen en één keer per uur worden uitgevoerd. In dit scenario kan het runbook elke 15 minuten worden uitgevoerd met de verschillende planningen.

## <a name="disable-a-schedule"></a>Een schema uitschakelen

Wanneer u een planning uit schakelt, wordt elk gekoppeld runbook niet meer volgens dat schema uitgevoerd. U kunt een planning handmatig uitschakelen of een verlooptijd instellen voor schema's met een frequentie wanneer u ze maakt. Wanneer de vervaltijd is bereikt, wordt de planning uitgeschakeld.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Een planning uitschakelen vanuit de Azure Portal

1. Selecteer in uw Automation-account in het linkerdeelvenster **Planningen** onder **Gedeelde resources.**
1. Selecteer de naam van een planning om het detailvenster te openen.
1. Wijzig **Ingeschakeld in** **Nee.**

> [!NOTE]
> Als u een planning met een begintijd in het verleden wilt uitschakelen, moet u de begindatum in de toekomst wijzigen in een tijdstip voordat u deze op kunt slaan.

### <a name="disable-a-schedule-with-powershell"></a>Een planning uitschakelen met PowerShell

Gebruik de cmdlet [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) om de eigenschappen van een bestaande planning te wijzigen. Als u het schema wilt uitschakelen, geeft u False op voor de `IsEnabled` parameter .

In het volgende voorbeeld ziet u hoe u een planning voor een runbook uit kunt schakelen met behulp van een Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Een schema verwijderen

Wanneer u klaar bent om uw schema's te verwijderen, kunt u de Azure Portal of PowerShell gebruiken. Vergeet niet dat u alleen een planning kunt verwijderen die is uitgeschakeld, zoals beschreven in de vorige sectie.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Een planning verwijderen met behulp van de Azure Portal

1. Selecteer in uw Automation-account in het linkerdeelvenster De optie **Schema's** onder **Gedeelde resources.**
2. Selecteer de naam van een planning om het detailvenster te openen.
3. Klik op **Verwijderen**.

### <a name="remove-a-schedule-with-powershell"></a>Een schema verwijderen met PowerShell

U kunt de `Remove-AzAutomationSchedule` cmdlet gebruiken zoals hieronder wordt weergegeven om een bestaand schema te verwijderen.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Volgende stappen

* Zie Modules beheren in Azure Automation voor meer informatie over de cmdlets die worden gebruikt [voor toegang tot schema'Azure Automation.](modules.md)
* Zie Runbookuitvoering in Azure Automation voor algemene [informatie over runbooks.](../automation-runbook-execution.md)
