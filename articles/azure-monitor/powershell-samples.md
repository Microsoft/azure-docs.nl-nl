---
title: Azure Monitor PowerShell-voorbeelden
description: Gebruik PowerShell om toegang te krijgen tot Azure Monitor-functies, zoals automatische schaalaanpassing, waarschuwingen, webhooks en zoeken naar activiteitenlogboeken.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 2/14/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1aa0d44dcb73a925494c0bc4aae43dc93e5a33d0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047057"
---
# <a name="azure-monitor-powershell-samples"></a>Azure Monitor PowerShell-voorbeelden
In dit artikel wordt beschreven hoe u PowerShell-opdrachten kunt gebruiken om toegang te krijgen tot Azure Monitor-functies.

> [!NOTE]
> Azure Monitor is de nieuwe naam voor de tool die tot 25 september 2016 'Azure Insights' werd genoemd. De naamruimten en de volgende opdrachten bevatten echter nog steeds het woord *Insights*.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>PowerShell instellen
Als u dat nog niet hebt gedaan, stelt u PowerShell in op uw computer. Zie [PowerShell installeren en configureren](/powershell/azure/) voor meer informatie.

## <a name="examples-in-this-article"></a>Voorbeelden in dit artikel
In de voorbeelden in dit artikel wordt beschreven hoe u Azure Monitor-cmdlets kunt gebruiken. U kunt ook de volledige lijst met Azure Monitor PowerShell-cmdlets bekijken via [Azure Monitor-cmdlets (Insights)](/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Aanmelden en abonnementen gebruiken
Meld u eerst aan bij uw Azure-abonnement.

```powershell
Connect-AzAccount
```

Er wordt een aanmeldingsscherm weergegeven. Zodra u zich hebt aangemeld met uw account, worden de tenant-id en standaardabonnements-id weergegeven. Alle Azure-cmdlets werken in de context van uw standaardabonnement. Als u de lijst met abonnementen waartoe u toegang hebt, wilt weergeven, gebruikt u de volgende opdracht:

```powershell
Get-AzSubscription
```

Gebruik de volgende opdracht om de werkcontext te bekijken (via welk abonnement uw opdrachten worden uitgevoerd):

```powershell
Get-AzContext
```
Als u uw werkcontext wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log"></a>Het activiteitenlogboek ophalen
Gebruik de cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog).  Hier volgen enkele algemene voorbeelden. Het activiteitenlogboek bevat de laatste 90 dagen aan bewerkingen. Het gebruik van datums vóór deze tijd resulteert in een foutbericht.  

Bekijk wat de huidige datum/tijd is om te controleren welke tijden moeten worden gebruikt in de onderstaande opdrachten:
```powershell
Get-Date
```

Logboekvermeldingen ophalen van deze tijd/datum tot het heden:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Logboekvermeldingen ophalen binnen een tijd-/datumbereik:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Logboekvermeldingen ophalen van een specifieke resourcegroep:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Logboekvermeldingen ophalen van een specifieke resourceprovider binnen een tijd-/datumbereik:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Alle logboekvermeldingen ophalen met een specifieke oproepende functie:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Met de volgende opdracht worden de laatste 1000 gebeurtenissen uit het activiteitenlogboek opgehaald:

```powershell
Get-AzLog -MaxRecord 1000
```

`Get-AzLog` ondersteunt allerlei andere parameters. Zie de referentie `Get-AzLog` voor meer informatie.

> [!NOTE]
> `Get-AzLog` biedt slechts 15 dagen aan geschiedenis. Met de parameter **-MaxRecords** kunt u een query uitvoeren op de laatste N gebeurtenissen, na 15 dagen. Als u gebeurtenissen die ouder zijn dan 15 dagen wilt bekijken, gebruikt u de REST API of SDK (C#-voorbeeld met de SDK). Als u geen **Starttijd** toevoegt, wordt de standaardwaarde de **Eindtijd** minus één uur. Als u geen **Eindtijd** toevoegt, wordt de standaardwaarde de huidige tijd. Alle tijden zijn in UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Waarschuwingsgeschiedenis ophalen
Als u alle waarschuwingsgebeurtenissen wilt weergeven, kunt u een query uitvoeren op de Azure Resource Manager-logboeken met behulp van de volgende voorbeelden.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Als u de geschiedenis voor een specifieke waarschuwingsregel wilt weergeven, kunt u de cmdlet `Get-AzAlertHistory` gebruiken om de resource-id van de waarschuwingsregel door te geven.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

De cmdlet `Get-AzAlertHistory` ondersteunt verschillende parameters. Zie [Get-AlertHistory](/previous-versions/azure/mt282453(v=azure.100)) voor meer informatie.

## <a name="retrieve-information-on-alert-rules"></a>Informatie over het ophalen van waarschuwingsregels
Alle volgende opdrachten worden toegepast op een resourcegroep met de naam 'montest'.

Alle eigenschappen van de waarschuwingsregel ophalen:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Alle waarschuwingen ophalen voor een resourcegroep:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Alle waarschuwingsregels ophalen die zijn ingesteld voor een doelresource. Bijvoorbeeld alle waarschuwingsregels die zijn ingesteld op een virtuele machine.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` ondersteunt ook andere parameters. Zie [Get-AlertRule](/previous-versions/azure/mt282459(v=azure.100)) voor meer informatie.

## <a name="create-metric-alerts"></a>Metrische waarschuwingen maken
U kunt de cmdlet `Add-AlertRule` gebruiken om een waarschuwingsregel te maken, bij te werken of uit te schakelen.

U kunt de eigenschappen e-mail en webhook maken met behulp van `New-AzAlertRuleEmail` en `New-AzAlertRuleWebhook`. Wijs deze eigenschappen in de cmdlet Waarschuwingsregel toe als acties voor de eigenschap **Acties** van de waarschuwingsregel.

De volgende tabel beschrijft de parameters en waarden die worden gebruikt om een waarschuwing te maken met behulp van een metrische waarde.

| parameter | waarde |
| --- | --- |
| Naam |simpletestdiskwrite |
| Locatie van deze waarschuwingsregel |VS - oost |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Metrische naam van de waarschuwing die wordt gemaakt |\PhysicalDisk(_Total)\Disk Writes/sec. Zie de cmdlet `Get-MetricDefinitions` voor informatie over het ophalen van de exacte metrische namen |
| operator |GreaterThan |
| Drempelwaarde (aantal per seconde voor deze metrische waarde) |1 |
| WindowSize (uu:mm:ss-indeling) |00:05:00 |
| aggregator (statistiek van de metrische waarde, waarbij in dit geval Average wordt gebruikt) |Average |
| aangepaste e-mailberichten (tekenreeksmatrix) |'foo@example.com','bar@example.com' |
| e-mail verzenden naar eigenaars, inzenders en lezers |-SendToServiceOwners |

Een e-mailactie maken

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Een webhookactie maken

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

De waarschuwingsregel maken voor de metrische waarde CPU% op een klassieke VM

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

De waarschuwingsregel ophalen

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Met de cmdlet Waarschuwing toevoegen wordt de regel ook bijgewerkt als er al een waarschuwingsregel bestaat voor de opgegeven eigenschappen. Als u een waarschuwingsregel wilt uitschakelen, neemt u de parameter **-DisableRule** op.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Een lijst met beschikbare metrische gegevens voor waarschuwingen ophalen
U kunt de cmdlet `Get-AzMetricDefinition` gebruiken om de lijst met alle metrische gegevens voor een specifieke resource weer te geven.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

In het volgende voorbeeld wordt een tabel met de metrische naam en de bijbehorende eenheid gegenereerd.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Een volledige lijst met beschikbare opties voor `Get-AzMetricDefinition` is beschikbaar via [Get-MetricDefinitions](/previous-versions/azure/mt282458(v=azure.100)).

## <a name="create-and-manage-activity-log-alerts"></a>Waarschuwingen voor activiteitenlogboeken maken en beheren
U kunt de cmdlet `Set-AzActivityLogAlert` gebruiken om een waarschuwing voor een activiteitenlogboek in te stellen. Een waarschuwing voor een activiteitenlogboek vereist dat u eerst uw voorwaarden definieert als een woordenlijst en vervolgens een waarschuwing maakt die deze voorwaarden gebruikt.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

De aanvullende eigenschappen van de webhook zijn optioneel. U kunt de inhoud van een waarschuwing voor een activiteitenlogboek terughalen met behulp van `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Instellingen voor automatische schaalaanpassing maken en beheren

> [!NOTE] 
> Voor Cloud Services (Microsoft.ClassicCompute) ondersteunt automatisch schalen een tijdseenheid van 5 minuten (PT5M). Voor de andere services die automatisch worden geschaald, wordt een tijdseenheid van minimaal 1 minuut ondersteunt (PT1M)

Voor een resource (een web-app, VM, cloudservice of virtuele-machineschaalset) kan slechts één instelling voor automatische schaalaanpassing zijn geconfigureerd.
Elke instelling voor automatische schaalaanpassing kan echter meerdere profielen hebben. Bijvoorbeeld één voor een schaalprofiel op basis van prestaties en een tweede voor een op een planning gebaseerd profiel. Voor elk profiel kunnen meerdere regels worden geconfigureerd. Zie [Automatische schaalaanpassing gebruiken voor een toepassing](../cloud-services/cloud-services-how-to-scale-portal.md) voor meer informatie over automatische schaalaanpassing.

Dit zijn de stappen die u kunt gebruiken:

1. Maak regel(s).
2. Maak een of meer profielen waaraan u de regels toewijst die u eerder hebt gemaakt.
3. Optioneel: Maak meldingen voor automatische schaalaanpassing door webhook- en e-maileigenschappen te configureren.
4. Maak een instelling voor automatische schaalaanpassing met een naam op de doelresource door de profielen en meldingen toe te wijzen die u in de vorige stappen hebt gemaakt.

In de volgende voorbeelden ziet u hoe u een instelling voor automatische schaalaanpassing kunt maken voor een virtuele-machineschaalset voor een Windows-besturingssysteem op basis van de metrische waarde van het CPU-gebruik.

Maak eerst een regel om uit te breiden, waarbij het aantal exemplaren toeneemt.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Maak vervolgens een regel om te verminderen, waarbij het aantal exemplaren afneemt.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Maak vervolgens een profiel voor de regels.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Maak een webhookeigenschap.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Maak de meldingseigenschap voor de instelling voor automatische schaalaanpassing, inclusief e-mail en de webhook die u eerder hebt gemaakt.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Maak ten slotte de instelling voor automatische schaalaanpassing om het profiel toe te voegen dat u eerder hebt gemaakt. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Zie [Get-AutoscaleSetting](/previous-versions/azure/mt282461(v=azure.100)) voor meer informatie over het beheren van instellingen voor automatische schaalaanpassing.

## <a name="autoscale-history"></a>Geschiedenis van automatische schaalaanpassing
In het volgende voorbeeld ziet u hoe u recente automatische schaalaanpassingen en waarschuwingsgebeurtenissen kunt weergeven. Gebruik de zoekfunctie voor activiteitenlogboeken om de geschiedenis van automatische schaalaanpassingen weer te geven.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

U kunt de cmdlet `Get-AzAutoScaleHistory` gebruiken om de geschiedenis van automatische schaalaanpassingen op te halen.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Zie [Get-AutoscaleHistory](/previous-versions/azure/mt282464(v=azure.100)) voor meer informatie.

### <a name="view-details-for-an-autoscale-setting"></a>Details weergeven voor een instelling voor automatische schaalaanpassing
U kunt de cmdlet `Get-Autoscalesetting` gebruiken om meer informatie over de instelling voor automatische schaalaanpassing op te halen.

In het volgende voorbeeld ziet u details over alle instellingen voor automatische schaalaanpassing in de resourcegroep 'myrg1'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

In het volgende voorbeeld ziet u details over alle instellingen voor automatische schaalaanpassing in de resourcegroep 'myrg1' en met name de instelling voor automatische schaalaanpassing met de naam' MyScaleVMSSSetting'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Een instelling voor automatische schaalaanpassing verwijderen
U kunt de cmdlet `Remove-Autoscalesetting` gebruiken om een instelling voor automatische schaalaanpassing te verwijderen.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Logboekprofielen voor het activiteitenlogboek beheren
U kunt een *logboekprofiel* maken en gegevens uit uw activiteitenlogboek exporteren naar een opslagaccount. Hiervoor kunt u gegevensretentie configureren. U kunt eventueel ook de gegevens streamen naar uw Event Hub. Deze functie is momenteel beschikbaar als preview en u kunt slechts één logboekprofiel per abonnement maken. U kunt de volgende cmdlets gebruiken met uw huidige abonnement om logboekprofielen te maken en te beheren. U kunt ook een bepaald abonnement kiezen. Hoewel PowerShell standaard het huidige abonnement gebruikt, kunt u dit altijd wijzigen met behulp van `Set-AzContext`. U kunt het activiteitenlogboek configureren zodat gegevens naar een opslagaccount of Event Hub binnen dat abonnement worden doorgestuurd. Gegevens worden geschreven als blob-bestanden in JSON-indeling.

### <a name="get-a-log-profile"></a>Een logboekprofiel ophalen
Gebruik de cmdlet `Get-AzLogProfile` om uw bestaande logboekprofielen op te halen.

### <a name="add-a-log-profile-without-data-retention"></a>Een logboekprofiel zonder gegevensretentie toevoegen
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Een logboekprofiel verwijderen
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Een logboekprofiel met gegevensretentie toevoegen
U kunt de eigenschap **-RetentionInDays** met het aantal dagen opgeven als een positief geheel getal, waarbij de gegevens behouden blijven.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Een logboekprofiel met retentie en Event Hub toevoegen
U kunt uw gegevens niet alleen doorsturen naar een opslagaccount, maar ze ook streamen naar een Event Hub. In deze preview is de configuratie van het opslagaccount verplicht, maar is de Event Hub-configuratie optioneel.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Diagnostische logboeken configureren
Veel Azure-services bieden extra logboeken en telemetrie die een of meer van de volgende zaken kunnen doen: 
 - geconfigureerd worden om gegevens in uw Azure Storage-account op te slaan
 - verzenden naar Event Hubs
 - verzenden naar een Log Analytics-werkruimte. 

De bewerking kan alleen op resourceniveau worden uitgevoerd. Het opslagaccount of de Event Hub moet zich in dezelfde regio bevinden als de doelresource waar de diagnostische instelling is geconfigureerd.

### <a name="get-diagnostic-setting"></a>Diagnostische instelling ophalen
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnostische instelling uitschakelen

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Diagnostische instelling inschakelen zonder retentie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Diagnostische instelling inschakelen met retentie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnostische instelling inschakelen met retentie voor een specifieke logboekcategorie

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnostische instelling voor Event Hubs inschakelen

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Diagnostische instelling voor Log Analytics inschakelen

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Houd er rekening mee dat de eigenschap WorkspaceId de *resource-id* van de werkruimte gebruikt. U kunt de resource-id van uw Log Analytics-werkruimte verkrijgen met de volgende opdracht:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Deze opdrachten kunnen worden gecombineerd om gegevens naar meerdere doelvoorzieningen te verzenden.
