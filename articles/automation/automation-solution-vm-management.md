---
title: Overzicht van Azure Automation VM's buiten bedrijfsuren starten/stoppen
description: In dit artikel wordt de functie VM's buiten bedrijfsuren starten/stoppen beschreven, waarmee Vm's op basis van een planning worden gestart of gestopt, en deze vanuit Azure Monitor-logboeken proactief worden bewaakt.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: e9a5427f7c3a057f291067ac83d3d9032d7e693d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559355"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Overzicht van VM's buiten bedrijfsuren starten/stoppen

Met de functie VM's buiten bedrijfsuren starten/stoppen worden de ingeschakelde Azure-Vm's gestart of gestopt. Hiermee worden computers op door de gebruiker gedefinieerde planningen gestart of gestopt, vindt u inzichten via Azure Monitor-logboeken en verzendt u optionele e-mail berichten met behulp van [actie groepen](../azure-monitor/alerts/action-groups.md). De functie kan worden ingeschakeld op zowel Azure Resource Manager als klassieke Vm's voor de meeste scenario's.

Deze functie maakt gebruik van de cmdlet [Start-AzVm](/powershell/module/az.compute/start-azvm) om vm's te starten. Er wordt gebruikgemaakt van [Stop-AzVM](/powershell/module/az.compute/stop-azvm) voor het stoppen van vm's.

> [!NOTE]
> Hoewel de runbooks zijn bijgewerkt voor het gebruik van de nieuwe Azure AZ module-cmdlets, gebruiken ze de AzureRM-voor voegsel alias.

> [!NOTE]
> VM's buiten bedrijfsuren starten/stoppen is bijgewerkt ter ondersteuning van de nieuwste versies van de Azure-modules die beschikbaar zijn. De bijgewerkte versie van deze functie, beschikbaar in Marketplace, biedt geen ondersteuning voor AzureRM-modules omdat we hebben gemigreerd van AzureRM naar AZ-modules.

De functie biedt een gecentraliseerde automatiserings optie met lage kosten voor gebruikers die hun VM-kosten willen optimaliseren. U kunt deze functie gebruiken voor het volgende:

- [Plan vm's om te starten en te stoppen](automation-solution-vm-management-config.md#schedule).
- Plan Vm's om in oplopende volg orde te starten en te stoppen met [behulp van Azure-Tags](automation-solution-vm-management-config.md#tags). Deze activiteit wordt niet ondersteund voor klassieke virtuele machines.
- Werk Vm's autostop op basis van [laag CPU-gebruik](automation-solution-vm-management-config.md#cpuutil).

Hier volgen enkele beperkingen met betrekking tot de huidige functie:

- Het beheert Vm's in een wille keurige regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account.
- Het is beschikbaar in Azure en Azure Government voor elke regio die een Log Analytics-werk ruimte, een Azure Automation-account en waarschuwingen ondersteunt. Azure Government regio's ondersteunen momenteel geen e-mail functionaliteit.

> [!NOTE]
> Voordat u deze versie installeert, willen we u weten over de [volgende versie](https://github.com/microsoft/startstopv2-deployments), die nu in preview is.  Deze nieuwe versie (v2) biedt dezelfde functionaliteit als deze, maar is ontworpen om te profiteren van de nieuwe technologie in Azure. Er worden enkele van de vaakst aangevraagde functies van klanten toegevoegd, zoals ondersteuning voor meerdere abonnementen van één start/stop-exemplaar.

## <a name="prerequisites"></a>Vereisten

- De runbooks voor het starten/stoppen van Vm's buiten kantoor uren functie werken met een [Azure uitvoeren als-account](./automation-security-overview.md#run-as-accounts). Het run as-account is de voorkeurs verificatie methode omdat deze gebruikmaakt van certificaat verificatie in plaats van een wacht woord dat regel matig verloopt of kan worden gewijzigd.

- Een [Azure Monitor Log Analytics-werk ruimte](../azure-monitor/logs/design-logs-deployment.md) waarin de runbook-taak logboeken en taak stroom resultaten worden opgeslagen in een werk ruimte om op te vragen en te analyseren. Het Automation-account kan worden gekoppeld aan een nieuwe of bestaande Log Analytics werk ruimte en beide resources moeten zich in dezelfde resource groep bevinden.

U wordt aangeraden een afzonderlijk Automation-account te gebruiken voor het werken met Vm's die zijn ingeschakeld voor de functie VM's buiten bedrijfsuren starten/stoppen. Versies van Azure-modules worden vaak geüpgraded en de bijbehorende para meters kunnen veranderen. De functie wordt niet bijgewerkt op dezelfde uitgebracht en is mogelijk niet geschikt voor nieuwere versies van de cmdlets die worden gebruikt. Voordat u de bijgewerkte modules in uw productie Automation-account (s) importeert, raden we u aan deze te importeren in een test Automation-account om te controleren of er compatibiliteits problemen zijn.

## <a name="permissions"></a>Machtigingen

U moet bepaalde machtigingen hebben om Vm's in te scha kelen voor de functie VM's buiten bedrijfsuren starten/stoppen. De machtigingen verschillen, afhankelijk van of de functie een vooraf gemaakt Automation-account en Log Analytics werk ruimte gebruikt of een nieuw account en een nieuwe werk ruimte maakt.

U hoeft geen machtigingen te configureren als u een bijdrager aan het abonnement en een globale beheerder in uw Azure Active Directory-Tenant (AD) bent. Als u niet over deze rechten beschikt of een aangepaste rol moet configureren, moet u ervoor zorgen dat u de machtigingen hebt die hieronder worden beschreven.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Machtigingen voor bestaande Automation-accounts en Log Analytics werk ruimte

Als u Vm's wilt inschakelen voor de functie VM's buiten bedrijfsuren starten/stoppen met behulp van een bestaand Automation-account en Log Analytics werk ruimte, hebt u de volgende machtigingen nodig voor het bereik van de resource groep. Zie [aangepaste rollen in azure](../role-based-access-control/custom-roles.md)voor meer informatie over rollen.

| Machtiging | Bereik|
| --- | --- |
| Micro soft. Automation/automationAccounts/lezen | Resourcegroep |
| Micro soft. Automation/automationAccounts/Varia bles/-schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/schedules/write | Resourcegroep |
| Micro soft. Automation/automationAccounts/runbooks/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/Connections/write | Resourcegroep |
| Micro soft. Automation/automationAccounts/certificaten/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/modules/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/modules/lezen | Resourcegroep |
| Micro soft. Automation/automationAccounts/jobSchedules/write | Resourcegroep |
| Micro soft. Automation/automationAccounts/Jobs/schrijven | Resourcegroep |
| Micro soft. Automation/automationAccounts/Jobs/lezen | Resourcegroep |
| Micro soft. OperationsManagement/oplossingen/schrijven | Resourcegroep |
| Micro soft. OperationalInsights/werk ruimten/* | Resourcegroep |
| Micro soft. Insights/diagnosticSettings/schrijven | Resourcegroep |
| Micro soft. Insights/ActionGroups/schrijven | Resourcegroep |
| Micro soft. Insights/ActionGroups/lezen | Resourcegroep |
| Micro soft. resources/abonnementen/resourceGroups/lezen | Resourcegroep |
| Micro soft. resources/implementaties/* | Resourcegroep |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Machtigingen voor het nieuwe Automation-account en de nieuwe Log Analytics-werk ruimte

U kunt Vm's voor de VM's buiten bedrijfsuren starten/stoppen-functie inschakelen met behulp van een nieuw Automation-account en Log Analytics werk ruimte. In dit geval hebt u de machtigingen die zijn gedefinieerd in de voor gaande sectie en de machtigingen die in deze sectie zijn gedefinieerd. U hebt ook de volgende rollen nodig:

- Co-Administrator op het abonnement. Deze rol is vereist voor het maken van het klassieke uitvoeren als-account als u klassieke Vm's wilt beheren. [Klassieke uitvoeren als-accounts](automation-create-standalone-account.md#create-a-classic-run-as-account) worden niet meer standaard gemaakt.
- Lidmaatschap van de functie voor ontwikkel aars van [Azure AD](../active-directory/roles/permissions-reference.md) -toepassingen. Zie [machtigingen voor het configureren van run as-accounts](automation-security-overview.md#permissions)voor meer informatie over het configureren van uitvoeren als-accounts.
- Inzender voor het abonnement of de volgende machtigingen.

| Machtiging |Bereik|
| --- | --- |
| Micro soft. Authorization/Operations/Read | Abonnement|
| Micro soft. Authorization/permissions/Read |Abonnement|
| Micro soft. Authorization/roleAssignments/lezen | Abonnement |
| Microsoft.Authorization/roleAssignments/write | Abonnement |
| Micro soft. Authorization/roleAssignments/verwijderen | Abonnement || Micro soft. Automation/automationAccounts/Connections/Read | Resourcegroep |
| Micro soft. Automation/automationAccounts/certificaten/lezen | Resourcegroep |
| Micro soft. Automation/automationAccounts/schrijven | Resourcegroep |
| Micro soft. OperationalInsights/werk ruimten/schrijven | Resourcegroep |

## <a name="components"></a>Onderdelen

De functie VM's buiten bedrijfsuren starten/stoppen omvat vooraf geconfigureerde runbooks, schema's en integratie met Azure Monitor-Logboeken. U kunt deze elementen gebruiken om het opstarten en afsluiten van uw virtuele machines aan te passen aan de behoeften van uw bedrijf.

### <a name="runbooks"></a>Runbooks

De volgende tabel geeft een lijst van de runbooks die de functie implementeert voor uw Automation-account. Breng geen wijzigingen aan in de runbook-code. In plaats daarvan schrijft u uw eigen runbook voor nieuwe functionaliteit.

> [!IMPORTANT]
> Voer geen wille keurig runbook direct uit met een **onderliggend element** dat is toegevoegd aan de naam.

Alle bovenliggende runbooks bevatten de `WhatIf` para meter. Als deze eigenschap is ingesteld op True, wordt de para meter gebruikt om het exacte gedrag te bepalen dat het runbook uitvoert wanneer het wordt uitgevoerd zonder de para meter en wordt gevalideerd of de juiste Vm's zijn gericht. Een runbook voert alleen de gedefinieerde acties uit wanneer de `WhatIf` para meter is ingesteld op ONWAAR.

|Runbook | Parameters | Beschrijving|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Aangeroepen vanuit het bovenliggende runbook. Dit runbook maakt waarschuwingen per resource voor het scenario voor automatisch stoppen.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True of False  | Hiermee worden Azure-waarschuwings regels gemaakt of bijgewerkt op Vm's in het doel abonnement of resource groepen. <br> `VMList` is een door komma's gescheiden lijst met virtuele machines (zonder spaties), bijvoorbeeld `vm1,vm2,vm3` .<br> `WhatIf` Hiermee wordt de validatie van de runbook-logica mogelijk zonder dat deze wordt uitgevoerd.|
|AutoStop_Disable | Geen | Hiermee schakelt u waarschuwingen voor automatisch stoppen en standaard schema uit.|
|AutoStop_VM_Child | WebHookData | Aangeroepen vanuit het bovenliggende runbook. Waarschuwings regels roepen dit runbook aan om een klassieke virtuele machine te stoppen.|
|AutoStop_VM_Child_ARM | WebHookData |Aangeroepen vanuit het bovenliggende runbook. Waarschuwings regels roepen dit runbook aan om een virtuele machine te stoppen.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Actie: starten of stoppen<br> VMList  | Hiermee wordt de actie voor het starten of stoppen van een klassieke VM-groep door Cloud Services uitgevoerd. |
|ScheduledStartStop_Child | VMName <br> Actie: starten of stoppen <br> ResourceGroupName | Aangeroepen vanuit het bovenliggende runbook. Hiermee wordt een start-of stop actie uitgevoerd voor de geplande beëindiging.|
|ScheduledStartStop_Child_Classic | VMName<br> Actie: starten of stoppen<br> ResourceGroupName | Aangeroepen vanuit het bovenliggende runbook. Hiermee wordt een start-of stop actie uitgevoerd voor de geplande stop voor klassieke Vm's. |
|ScheduledStartStop_Parent | Actie: starten of stoppen <br>VMList <br> WhatIf: True of False | Hiermee worden alle virtuele machines in het abonnement gestart of gestopt. Bewerk de variabelen `External_Start_ResourceGroupNames` en `External_Stop_ResourceGroupNames` alleen voor het uitvoeren van deze doel resource groepen. U kunt ook specifieke Vm's uitsluiten door de variabele bij te werken `External_ExcludeVMNames` .|
|SequencedStartStop_Parent | Actie: starten of stoppen <br> WhatIf: True of False<br>VMList| Maakt labels met de naam **sequencestart** en **sequencestop** op elke virtuele machine waarvoor u de activiteit start/stop wilt sequentieren. Deze label namen zijn hoofdletter gevoelig. De waarde van het label moet een lijst met positieve gehele getallen zijn, bijvoorbeeld `1,2,3` , die overeenkomt met de volg orde waarin u wilt starten of stoppen. <br>**Opmerking**: vm's moeten zich in resource groepen bezien die zijn gedefinieerd in `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` en `External_ExcludeVMNames` variabelen. Ze moeten de juiste labels hebben om de acties van kracht te laten worden.|

### <a name="variables"></a>Variabelen

De volgende tabel bevat de variabelen die zijn gemaakt in uw Automation-account. Wijzig variabelen alleen met de voor voegsels `External` . Het wijzigen van variabelen die met een probleem worden opgelost, `Internal` leidt tot ongewenste effecten.

> [!NOTE]
> De beperkingen voor de VM-naam en resource groep zijn grotendeels het resultaat van de variabele grootte. Zie [variabele assets in azure Automation](./shared-resources/variables.md).

|Variabele | Beschrijving|
|---------|------------|
|External_AutoStop_Condition | De voorwaardelijke operator die is vereist voor het configureren van de voor waarde voordat een waarschuwing wordt geactiveerd. Acceptabele waarden zijn `GreaterThan` , `GreaterThanOrEqual` , `LessThan` en `LessThanOrEqual` .|
|External_AutoStop_Description | De waarschuwing voor het stoppen van de virtuele machine als het CPU-percentage de drempel waarde overschrijdt.|
|External_AutoStop_Frequency | De evaluatie frequentie voor de regel. Deze para meter accepteert invoer in time span-indeling. Mogelijke waarden zijn 5 minuten tot zes uur. |
|External_AutoStop_MetricName | De naam van de prestatie gegevens waarvoor de Azure-waarschuwings regel moet worden geconfigureerd.|
|External_AutoStop_Severity | Ernst van de metrische waarschuwing, die tussen 0 en 4 ligt. |
|External_AutoStop_Threshold | De drempel waarde voor de Azure-waarschuwings regel die is opgegeven in de variabele `External_AutoStop_MetricName` . Percentage waarden variëren van 1 tot en met 100.|
|External_AutoStop_TimeAggregationOperator | De tijd aggregatie operator die wordt toegepast op de geselecteerde venster grootte om de voor waarde te evalueren. Acceptabele waarden zijn,,, `Average` `Minimum` `Maximum` `Total` en `Last` .|
|External_AutoStop_TimeWindow | De grootte van het venster waarin Azure geselecteerde metrische gegevens analyseert voor het activeren van een waarschuwing. Deze para meter accepteert invoer in time span-indeling. Mogelijke waarden zijn 5 minuten tot zes uur.|
|External_EnableClassicVMs| Waarde die aangeeft of klassieke Vm's zijn gericht op de functie. De standaard waarde is True. Stel deze variabele in op False voor Azure Cloud Solution Provider-abonnementen (CSP). Klassieke Vm's vereisen een [klassiek uitvoeren als-account](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Een door komma's gescheiden lijst met VM-namen die moeten worden uitgesloten, beperkt tot 140 Vm's. Als u meer dan 140 Vm's aan de lijst toevoegt, kunnen de virtuele machines die zijn opgegeven voor uitsluiting per ongeluk worden gestart of gestopt.|
|External_Start_ResourceGroupNames | Een door komma's gescheiden lijst van een of meer resource groepen die zijn gericht op Start acties.|
|External_Stop_ResourceGroupNames | Een door komma's gescheiden lijst van een of meer resource groepen die zijn gericht op Stop acties.|
|External_WaitTimeForVMRetrySeconds |De wacht tijd in seconden voor de acties die moeten worden uitgevoerd op de Vm's voor het **SequencedStartStop_Parent** runbook. Met deze variabele kan het runbook wachten op onderliggende bewerkingen gedurende een opgegeven aantal seconden voordat u doorgaat met de volgende actie. De maximale wacht tijd is 10800 of drie uur. De standaard waarde is 2100 seconden.|
|Internal_AutomationAccountName | Hiermee geeft u de naam van het Automation-account.|
|Internal_AutoSnooze_ARM_WebhookURI | De webhook-URI die wordt aangeroepen voor het scenario voor autostop voor Vm's.|
|Internal_AutoSnooze_WebhookUri | De webhook-URI die wordt aangeroepen voor het scenario voor autostop voor klassieke Vm's.|
|Internal_AzureSubscriptionId | De ID van het Azure-abonnement.|
|Internal_ResourceGroupName | De naam van de resource groep voor het Automation-account.|

>[!NOTE]
>`External_WaitTimeForVMRetryInSeconds`De standaard waarde voor de variabele is bijgewerkt van 600 naar 2100.

In alle scenario's, de variabelen `External_Start_ResourceGroupNames` ,  `External_Stop_ResourceGroupNames` en `External_ExcludeVMNames` zijn nodig voor het richten op vm's, met uitzonde ring van de lijsten met door komma's gescheiden vm's voor de **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** en **ScheduledStartStop_Parent** runbooks. Dat wil zeggen dat uw Vm's deel moeten uitmaken van de doel resource groepen voor het starten en stoppen van de acties. De logica werkt op soort gelijke wijze als Azure Policy, in dat u het abonnement of de resource groep kunt bereiken en acties moet hebben overgenomen door nieuw gemaakte Vm's. Op deze manier wordt voor komen dat u een afzonderlijke planning moet onderhouden voor elke VM en het beheer begint en stopt op schaal.

### <a name="schedules"></a>Schema's

De volgende tabel bevat een overzicht van de standaard schema's die zijn gemaakt in uw Automation-account. U kunt ze wijzigen of uw eigen aangepaste schema's maken. Standaard zijn alle schema's uitgeschakeld, met uitzonde ring van de **Scheduled_StartVM** -en **Scheduled_StopVM** -schema's.

Schakel niet alle schema's in, omdat dit mogelijk overlappende plannings acties kan maken. U kunt het beste bepalen welke optimalisaties u wilt uitvoeren en ze dienovereenkomstig aanpassen. Zie de voorbeeld scenario's in de sectie Overzicht voor meer informatie.

|Schema naam | Frequentie | Beschrijving|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Om de 8 uur | Voert het **AutoStop_CreateAlert_Parent** runbook uit om de 8 uur, waardoor de op virtuele machines gebaseerde waarden in `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` en variabelen op zijn beurt worden gestopt `External_ExcludeVMNames` . U kunt ook een door komma's gescheiden lijst met Vm's opgeven met behulp van de `VMList` para meter.|
|Scheduled_StopVM | Door de gebruiker gedefinieerd, dagelijks | Voert het **ScheduledStopStart_Parent** runbook uit met een para meter van `Stop` elke dag op de opgegeven tijd. Stopt automatisch alle virtuele machines die voldoen aan de regels die zijn gedefinieerd door variabele assets. Schakel de planning **StartVM**.|
|Scheduled_StartVM | Door de gebruiker gedefinieerd, dagelijks | Voert het **ScheduledStopStart_Parent** runbook uit met een parameter waarde van `Start` elke dag op de opgegeven tijd. Alle Vm's die voldoen aan de regels die zijn gedefinieerd door variabele assets, worden automatisch gestart. Schakel de planning **StopVM**.|
|Sequenced-StopVM | 1:00 uur (UTC), elke vrijdag | Voert het **Sequenced_StopStop_Parent** runbook uit met een parameter waarde van `Stop` elke vrijdag op de opgegeven tijd. Opeenvolgend (oplopend) stopt alle virtuele machines met een tag van **SequenceStop** die zijn gedefinieerd door de juiste variabelen. Zie [Runbooks](#runbooks)voor meer informatie over label waarden en activa variabelen. Schakel het gerelateerde schema in, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 uur (UTC), elke maandag | Voert het **SequencedStopStart_Parent** runbook uit met een parameter waarde van `Start` elke maandag op de opgegeven tijd. Na elkaar (aflopend) worden alle virtuele machines gestart met een tag van **SequenceStart** dat is gedefinieerd door de juiste variabelen. Zie [Runbooks](#runbooks)voor meer informatie over label waarden en variabele assets. Schakel het gerelateerde schema in, **Sequenced-StopVM**.

## <a name="use-the-feature-with-classic-vms"></a>De functie gebruiken met klassieke Vm's

Als u de functie VM's buiten bedrijfsuren starten/stoppen gebruikt voor klassieke Vm's, worden alle virtuele machines opeenvolgend per Cloud service verwerkt door Automation. Vm's worden nog steeds parallel verwerkt in verschillende Cloud Services. 

Voor het gebruik van de functie met klassieke Vm's hebt u een klassiek uitvoeren als-account nodig, dat niet standaard wordt gemaakt. Zie [een klassiek uitvoeren als-account maken](automation-create-standalone-account.md#create-a-classic-run-as-account)voor instructies voor het maken van een klassiek uitvoeren als-account.

Als u meer dan 20 Vm's per Cloud service hebt, zijn hier enkele aanbevelingen:

* Meerdere planningen maken met het bovenliggende runbook **ScheduledStartStop_Parent** en 20 vm's per schema opgeven.
* In de schema-eigenschappen gebruikt `VMList` u de para meter om VM-namen op te geven als een lijst met door komma's gescheiden waarden (geen spaties).

Als de Automation-taak voor deze functie meer dan drie uur wordt uitgevoerd, wordt deze tijdelijk uit het geheugen verwijderd of gestopt volgens de limiet voor de [billijke share](automation-runbook-execution.md#fair-share) .

Azure CSP-abonnementen bieden alleen ondersteuning voor het Azure Resource Manager model. Niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer de VM's buiten bedrijfsuren starten/stoppen functie wordt uitgevoerd, kunnen er fouten optreden omdat deze cmdlets bevat om klassieke resources te beheren. Zie [beschik bare Services in CSP-abonnementen](/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP. Als u een CSP-abonnement gebruikt, moet u de variabele [External_EnableClassicVMs](#variables) instellen op False na de implementatie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>De functie weer geven

Gebruik een van de volgende mechanismen om toegang te krijgen tot de ingeschakelde functie:

* Selecteer in uw Automation-account de optie **VM starten/stoppen** onder **gerelateerde resources**. Op de pagina VM starten/stoppen selecteert u **de oplossing beheren** onder **oplossingen voor het starten/stoppen van vm's beheren**.

* Ga naar de Log Analytics werkruimte die aan uw Automation-account is gekoppeld. Nadat u de werk ruimte hebt geselecteerd, kiest u **oplossingen** in het linkerdeel venster. Selecteer op de pagina oplossingen de optie **Start-Stop-VM [werk ruimte]** in de lijst.  

Als u de functie selecteert, wordt de pagina **Start-Stop-VM [werkruimte]** weer gegeven. Hier kunt u belang rijke informatie bekijken, zoals de informatie in de tegel **StartStopVM** . Net als in uw Log Analytics-werk ruimte worden in deze tegel een aantal en een grafische weer gave weer gegeven van de runbook-taken voor de functie die is gestart en voltooid.

![Updatebeheer pagina Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

U kunt verdere analyse van de taak records uitvoeren door op de ring-tegel te klikken. In het dash board worden taak geschiedenis en vooraf gedefinieerde zoek opdrachten in logboeken weer gegeven. Ga naar de geavanceerde log Analytics-Portal om te zoeken op basis van uw zoek query's.

## <a name="update-the-feature"></a>De functie bijwerken

Als u een eerdere versie van VM's buiten bedrijfsuren starten/stoppen hebt geïmplementeerd, verwijdert u deze uit uw account voordat u een bijgewerkte versie implementeert. Volg de stappen om [de functie te verwijderen](automation-solution-vm-management-remove.md#delete-the-feature) en volg de stappen om [deze in te scha kelen](automation-solution-vm-management-enable.md).

## <a name="next-steps"></a>Volgende stappen

Zie [VM's buiten bedrijfsuren starten/stoppen inschakelen](automation-solution-vm-management-enable.md)als u de functie wilt inschakelen op vm's in uw omgeving.