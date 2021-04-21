---
title: Azure Automation VM's buiten bedrijfsuren starten/stoppen overzicht
description: In dit artikel wordt de functie VM's buiten bedrijfsuren starten/stoppen, waarmee VM's volgens een schema worden gestart of gestopt en proactief worden bewaakt vanuit Azure Monitor logboeken.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b28367aa242d5fab71dc5046ff6188c634883f03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834512"
---
# <a name="startstop-vms-during-off-hours-overview"></a>VM's buiten bedrijfsuren starten/stoppen overzicht

De VM's buiten bedrijfsuren starten/stoppen functie azure-VM's starten of stoppen. Het start of stopt machines volgens door de gebruiker gedefinieerde planningen, biedt inzichten via Azure Monitor logboeken en verzendt optionele e-mailberichten met behulp van [actiegroepen.](../azure-monitor/alerts/action-groups.md) De functie kan voor de meeste scenario's worden ingeschakeld Azure Resource Manager virtuele en klassieke VM's.

Deze functie maakt gebruik van de cmdlet [Start-AzVm](/powershell/module/az.compute/start-azvm) om VM's te starten. Er wordt [gebruikgemaakt van Stop-AzVM voor het](/powershell/module/az.compute/stop-azvm) stoppen van VM's.

> [!NOTE]
> Hoewel de runbooks zijn bijgewerkt voor het gebruik van de nieuwe Azure Az-module-cmdlets, gebruiken ze de azureRM-voorvoegselalias.

> [!NOTE]
> VM's buiten bedrijfsuren starten/stoppen is bijgewerkt ter ondersteuning van de nieuwste versies van de Azure-modules die beschikbaar zijn. De bijgewerkte versie van deze functie, die beschikbaar is in Marketplace, biedt geen ondersteuning voor AzureRM-modules omdat we van AzureRM-modules naar Az-modules zijn gemigreerd.

De functie biedt een gedecentraliseerde automatiseringsoptie tegen lage kosten voor gebruikers die hun VM-kosten willen optimaliseren. U kunt de functie gebruiken voor het volgende:

- [Plan VM's om te starten en te stoppen.](automation-solution-vm-management-config.md#schedule)
- Plan VM's om in oplopende volgorde te starten en te stoppen met [behulp van Azure-tags.](automation-solution-vm-management-config.md#tags) Deze activiteit wordt niet ondersteund voor klassieke VM's.
- VM's automatisch stoppen op basis van [een laag CPU-gebruik.](automation-solution-vm-management-config.md#cpuutil)

Hier volgen enkele beperkingen met de huidige functie:

- Het beheert VM's in elke regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation account.
- Het is beschikbaar in Azure en Azure Government voor elke regio die ondersteuning biedt voor een Log Analytics-werkruimte, een Azure Automation account en waarschuwingen. Azure Government regio's bieden momenteel geen ondersteuning voor e-mailfunctionaliteit.

> [!NOTE]
> Voordat u deze versie installeert, willen we u graag weten welke [volgende](https://github.com/microsoft/startstopv2-deployments)versie op dit moment in preview is.  Deze nieuwe versie (V2) biedt dezelfde functionaliteit als deze, maar is ontworpen om te profiteren van nieuwere technologie in Azure. Hiermee worden enkele van de veelgebruikte functies van klanten toegevoegd, zoals ondersteuning voor meerdere abonnementen vanuit één start-/stop-exemplaar.

## <a name="prerequisites"></a>Vereisten

- De runbooks voor de functie VM's starten/stoppen buiten werkuren werken met een [Uitvoeren als-account van Azure.](./automation-security-overview.md#run-as-accounts) Het Uitvoeren als-account is de voorkeursverificatiemethode omdat hierbij gebruik wordt gemaakt van certificaatverificatie in plaats van een wachtwoord dat vaak verloopt of wordt gewijzigd.

- Een [Azure Monitor Log Analytics-werkruimte](../azure-monitor/logs/design-logs-deployment.md) waarin de logboeken van de runbook-taak en de taakstroom worden opgevraagd en geanalyseerd. Het Automation-account kan worden gekoppeld aan een nieuwe of bestaande Log Analytics-werkruimte en beide resources moeten zich in dezelfde resourcegroep.

U wordt aangeraden een afzonderlijk Automation-account te gebruiken voor het werken met VM's die zijn ingeschakeld voor VM's buiten bedrijfsuren starten/stoppen functie. Azure-moduleversies worden vaak bijgewerkt en de parameters kunnen worden gewijzigd. De functie wordt niet bijgewerkt met dezelfde snelheid en werkt mogelijk niet met nieuwere versies van de cmdlets die worden gebruikt. Voordat u de bijgewerkte modules importeert in uw Automation-productieaccount(s), raden we u aan deze te importeren in een Automation-testaccount om te controleren of er geen compatibiliteitsproblemen zijn.

## <a name="permissions"></a>Machtigingen

U moet over bepaalde machtigingen beschikken om VM's in te kunnen VM's buiten bedrijfsuren starten/stoppen functie. De machtigingen verschillen afhankelijk van of de functie gebruikmaakt van een vooraf gemaakt Automation-account en Log Analytics-werkruimte of een nieuw account en een nieuwe werkruimte maakt.

U hoeft geen machtigingen te configureren als u een Inzender voor het abonnement bent en een globale beheerder in uw Azure Active Directory (AD)-tenant. Als u deze rechten niet hebt of een aangepaste rol moet configureren, moet u ervoor zorgen dat u de machtigingen hebt die hieronder worden beschreven.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Machtigingen voor bestaande Automation-account en Log Analytics-werkruimte

Als u VM's voor de VM's buiten bedrijfsuren starten/stoppen wilt inschakelen met behulp van een bestaand Automation-account en log analytics-werkruimte, hebt u de volgende machtigingen nodig voor het bereik van de resourcegroep. Zie Aangepaste Azure-rollen voor [meer informatie over rollen.](../role-based-access-control/custom-roles.md)

| Machtiging | Bereik|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Resourcegroep |
| Microsoft.Automation/automationAccounts/variables/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/schedules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/runbooks/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/connections/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/certificates/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/modules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/modules/read | Resourcegroep |
| Microsoft.automation/automationAccounts/jobSchedules/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/jobs/write | Resourcegroep |
| Microsoft.Automation/automationAccounts/jobs/read | Resourcegroep |
| Microsoft.OperationsManagement/solutions/write | Resourcegroep |
| Microsoft.OperationalInsights/workspaces/* | Resourcegroep |
| Microsoft.Insights/diagnosticSettings/write | Resourcegroep |
| Microsoft.Insights/ActionGroups/Write | Resourcegroep |
| Microsoft.Insights/ActionGroups/read | Resourcegroep |
| Microsoft.Resources/subscriptions/resourceGroups/read | Resourcegroep |
| Microsoft.Resources/deployments/* | Resourcegroep |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Machtigingen voor een nieuw Automation-account en een nieuwe Log Analytics-werkruimte

U kunt VM's inschakelen voor VM's buiten bedrijfsuren starten/stoppen functie met behulp van een nieuw Automation-account en een nieuwe Log Analytics-werkruimte. In dit geval hebt u de machtigingen nodig die in de vorige sectie zijn gedefinieerd, evenals de machtigingen die in deze sectie zijn gedefinieerd. U hebt ook de volgende rollen nodig:

- Co-Administrator op abonnement. Deze rol is vereist om het klassieke Uitvoeren als-account te maken als u klassieke VM's wilt beheren. [Klassieke Uitvoeren als-accounts](automation-create-standalone-account.md#create-a-classic-run-as-account) worden niet meer standaard gemaakt.
- Lidmaatschap van de rol Azure AD-toepassingsontwikkelaar. [](../active-directory/roles/permissions-reference.md) Zie Machtigingen voor het configureren van Uitvoeren als-accounts voor meer informatie over het [configureren van Uitvoeren als-accounts.](automation-security-overview.md#permissions)
- Inzender voor het abonnement of de volgende machtigingen.

| Machtiging |Bereik|
| --- | --- |
| Microsoft.Authorization/Operations/read | Abonnement|
| Microsoft.Authorization/permissions/read |Abonnement|
| Microsoft.Authorization/roleAssignments/read | Abonnement |
| Microsoft.Authorization/roleAssignments/write | Abonnement |
| Microsoft.Authorization/roleAssignments/delete | Abonnement |
| Microsoft.Automation/automationAccounts/connections/read | Resourcegroep |
| Microsoft.Automation/automationAccounts/certificates/read | Resourcegroep |
| Microsoft.Automation/automationAccounts/write | Resourcegroep |
| Microsoft.OperationalInsights/workspaces/write | Resourcegroep |

## <a name="components"></a>Onderdelen

De VM's buiten bedrijfsuren starten/stoppen omvat vooraf geconfigureerde runbooks, schema's en integratie met Azure Monitor logboeken. U kunt deze elementen gebruiken om het opstarten en afsluiten van uw VM's aan te passen aan uw bedrijfsbehoeften.

### <a name="runbooks"></a>Runbooks

De volgende tabel bevat de runbooks die de functie implementeert in uw Automation-account. Wijzig de runbookcode NIET. Schrijf in plaats daarvan uw eigen runbook voor nieuwe functionaliteit.

> [!IMPORTANT]
> Voer niet rechtstreeks een runbook uit met **onderliggend** toegevoegd aan de naam.

Alle bovenliggende runbooks bevatten de `WhatIf` parameter . Als deze waarde is ingesteld op True, ondersteunt de parameter gedetailleerde informatie over het exacte gedrag dat het runbook gebruikt wanneer het wordt uitgevoerd zonder de parameter en wordt gevalideerd of de juiste VM's zijn gericht. Een runbook voert alleen de gedefinieerde acties uit wanneer `WhatIf` de parameter is ingesteld op False.

|Runbook | Parameters | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Aangeroepen vanuit het bovenliggende runbook. Dit runbook maakt waarschuwingen per resource voor het scenario voor automatisch stoppen.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Waar of Onwaar  | Hiermee maakt of werkt u Azure-waarschuwingsregels bij op VM's in het doelabonnement of de doelresourcegroepen. <br> `VMList` is een door komma's gescheiden lijst met VM's (zonder witruimten), bijvoorbeeld `vm1,vm2,vm3` .<br> `WhatIf` schakelt validatie van runbooklogica in zonder uit te voeren.|
|AutoStop_Disable | Geen | Schakelt waarschuwingen voor automatisch stoppen en de standaardplanning uit.|
|AutoStop_VM_Child | WebHookData | Aangeroepen vanuit het bovenliggende runbook. Waarschuwingsregels roepen dit runbook aan om een klassieke VM te stoppen.|
|AutoStop_VM_Child_ARM | WebHookData |Aangeroepen vanuit het bovenliggende runbook. Waarschuwingsregels roepen dit runbook aan om een VM te stoppen.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Actie: Starten of stoppen<br> VMList  | Voert actie starten of stoppen in klassieke VM-groep door Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Actie: Starten of stoppen <br> ResourceGroupName | Aangeroepen vanuit het bovenliggende runbook. Hiermee wordt een start- of stopactie uitgevoerd voor de geplande stop.|
|ScheduledStartStop_Child_Classic | VMName<br> Actie: Starten of Stoppen<br> ResourceGroupName | Aangeroepen vanuit het bovenliggende runbook. Hiermee wordt een start- of stopactie uitgevoerd voor de geplande stop voor klassieke VM's. |
|ScheduledStartStop_Parent | Actie: Starten of Stoppen <br>VMList <br> WhatIf: Waar of Onwaar | Alle VM's in het abonnement worden gestart of gestopt. Bewerk de variabelen `External_Start_ResourceGroupNames` en om deze alleen uit te voeren op deze `External_Stop_ResourceGroupNames` doelresourcegroepen. U kunt ook specifieke VM's uitsluiten door de variabele bij te `External_ExcludeVMNames` werken.|
|SequencedStartStop_Parent | Actie: Starten of Stoppen <br> WhatIf: Waar of Onwaar<br>VMList| Hiermee maakt u tags met de naam **sequencestart** en **sequencestop** op elke VM waarvoor u de activiteit start/stop wilt sequentie. Deze tagnamen zijn casegevoelig. De waarde van de tag moet een lijst met positieve gehele getallen zijn, bijvoorbeeld , die overeenkomt met de volgorde waarin u `1,2,3` wilt starten of stoppen. <br>**Opmerking:** VM's moeten zich binnen resourcegroepen die zijn gedefinieerd in `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` , en `External_ExcludeVMNames` variabelen. Ze moeten de juiste tags hebben om acties van kracht te laten worden.|

### <a name="variables"></a>Variabelen

De volgende tabel bevat de variabelen die zijn gemaakt in uw Automation-account. Wijzig alleen variabelen met het voorvoegsel `External` . Het wijzigen van variabelen met het voorvoegsel `Internal` veroorzaakt ongewenste effecten.

> [!NOTE]
> Beperkingen voor de VM-naam en resourcegroep zijn grotendeels het gevolg van de variabele grootte. Zie [Variabele assets in Azure Automation](./shared-resources/variables.md).

|Variabele | Beschrijving|
|---------|------------|
|External_AutoStop_Condition | De voorwaardelijke operator die vereist is voor het configureren van de voorwaarde voordat een waarschuwing wordt activeren. Acceptabele waarden `GreaterThan` zijn , , en `GreaterThanOrEqual` `LessThan` `LessThanOrEqual` .|
|External_AutoStop_Description | De waarschuwing om de VM te stoppen als het CPU-percentage de drempelwaarde overschrijdt.|
|External_AutoStop_Frequency | De evaluatiefrequentie voor de regel. Deze parameter accepteert invoer in de indeling van de periode. Mogelijke waarden liggen tussen 5 minuten en 6 uur. |
|External_AutoStop_MetricName | De naam van de prestatiemetrische gegevens waarvoor de Azure-waarschuwingsregel moet worden geconfigureerd.|
|External_AutoStop_Severity | De ernst van de waarschuwing voor metrische gegevens, die kan variëren van 0 tot 4. |
|External_AutoStop_Threshold | De drempelwaarde voor de Azure-waarschuwingsregel die is opgegeven in de variabele `External_AutoStop_MetricName` . Percentagewaarden variëren van 1 tot 100.|
|External_AutoStop_TimeAggregationOperator | De tijdaggregatieoperator die wordt toegepast op de geselecteerde venstergrootte om de voorwaarde te evalueren. Acceptabele `Average` waarden zijn `Minimum` , , , `Maximum` en `Total` `Last` .|
|External_AutoStop_TimeWindow | De grootte van het venster waarin Azure geselecteerde metrische gegevens analyseert voor het activeren van een waarschuwing. Deze parameter accepteert invoer in de indeling van de periode. Mogelijke waarden liggen tussen 5 minuten en 6 uur.|
|External_EnableClassicVMs| Waarde die opgeeft of klassieke VM's het doel zijn van de functie. De standaardwaarde is Waar. Stel deze variabele in op False voor Azure Cloud Solution Provider (CSP)-abonnementen. Voor klassieke VM's is [een klassiek Uitvoeren als-account vereist.](automation-create-standalone-account.md#create-a-classic-run-as-account)|
|External_ExcludeVMNames | Door komma's gescheiden lijst met VM-namen die moeten worden uitgesloten, beperkt tot 140 VM's. Als u meer dan 140 VM's aan de lijst toevoegt, kunnen VM's die zijn opgegeven voor uitsluiting per ongeluk worden gestart of gestopt.|
|External_Start_ResourceGroupNames | Door komma's gescheiden lijst met een of meer resourcegroepen die zijn bedoeld voor startacties.|
|External_Stop_ResourceGroupNames | Door komma's gescheiden lijst met een of meer resourcegroepen die zijn bedoeld voor stopacties.|
|External_WaitTimeForVMRetrySeconds |De wachttijd in seconden voor de acties die moeten worden uitgevoerd op de VM's voor het **SequencedStartStop_Parent** runbook. Met deze variabele kan het runbook een opgegeven aantal seconden wachten op onderliggende bewerkingen voordat de volgende actie wordt uitgevoerd. De maximale wachttijd is 10800 of drie uur. De standaardwaarde is 2100 seconden.|
|Internal_AutomationAccountName | Hiermee geeft u de naam van het Automation-account op.|
|Internal_AutoSnooze_ARM_WebhookURI | De webhook-URI die wordt aangeroepen voor het AutoStop-scenario voor VM's.|
|Internal_AutoSnooze_WebhookUri | De webhook-URI die wordt aangeroepen voor het AutoStop-scenario voor klassieke VM's.|
|Internal_AzureSubscriptionId | De azure-abonnements-id.|
|Internal_ResourceGroupName | De resourcegroepnaam van het Automation-account.|

>[!NOTE]
>Voor de variabele `External_WaitTimeForVMRetryInSeconds` is de standaardwaarde bijgewerkt van 600 naar 2100.

In alle scenario's zijn de variabelen , en nodig voor de doel-VM's, met uitzondering van de door komma's gescheiden VM-lijsten voor de `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` **runbooks AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent** **en ScheduledStartStop_Parent.** Dat wil zeggen dat uw VM's moeten behoren tot de doelresourcegroepen om acties voor starten en stoppen uit te voeren. De logica werkt vergelijkbaar met Azure Policy, omdat u zich kunt richten op het abonnement of de resourcegroep en dat acties worden overgenomen door nieuw gemaakte VM's. Met deze aanpak voorkomt u dat u een afzonderlijk schema voor elke VM moet onderhouden en starts en stops op schaal moet beheren.

### <a name="schedules"></a>Schema's

De volgende tabel bevat alle standaardschema's die in uw Automation-account zijn gemaakt. U kunt deze wijzigen of uw eigen aangepaste planningen maken. Standaard zijn alle planningen uitgeschakeld, met uitzondering van de **Scheduled_StartVM** en **Scheduled_StopVM** schema's.

Schakel niet alle planningen in, omdat dit kan leiden tot overlappende planningsacties. Het is het beste om te bepalen welke optimalisaties u wilt doen en deze dienovereenkomstig aan te passen. Zie de voorbeeldscenario's in de overzichtssectie voor meer uitleg.

|Naam van schema | Frequentie | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Om de 8 uur | Het **AutoStop_CreateAlert_Parent** runbook wordt elke 8 uur uitgevoerd, waardoor de op VM gebaseerde waarden in `External_Start_ResourceGroupNames` , en variabelen worden `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` gestopt. U kunt ook een door komma's gescheiden lijst met VM's opgeven met behulp van de `VMList` parameter .|
|Scheduled_StopVM | Door de gebruiker gedefinieerd, dagelijks | Voert het **ScheduledStopStart_Parent** runbook met een parameter van `Stop` elke dag op het opgegeven tijdstip. Stopt automatisch alle VM's die voldoen aan de regels die zijn gedefinieerd door variabele assets. Schakel de gerelateerde planning **Scheduled-StartVM in.**|
|Scheduled_StartVM | Door de gebruiker gedefinieerd, dagelijks | Hiermee wordt **ScheduledStopStart_Parent** runbook uitgevoerd met een parameterwaarde van `Start` elke dag op het opgegeven tijdstip. Start automatisch alle VM's die voldoen aan de regels die zijn gedefinieerd door variabele assets. Schakel de gerelateerde geplande **StopVM in.**|
|Sequenced-StopVM | Elke vrijdag om 01:00 uur (UTC) | Voert het **Sequenced_StopStop_Parent** runbook met een parameterwaarde van `Stop` elke vrijdag op de opgegeven tijd. Sequentaal (oplopend) stopt alle VM's met de tag **SequenceStop** die is gedefinieerd door de juiste variabelen. Zie Runbooks voor meer informatie over tagwaarden en [assetvariabelen.](#runbooks) Schakel het gerelateerde schema **Sequenced-StartVM in.**|
|Sequenced-StartVM | 13:00 pm (UTC), elke maandag | Voert het **SequencedStopStart_Parent** runbook met een parameterwaarde van `Start` elke maandag op de opgegeven tijd. Met sequentaal (aflopend) worden alle VM's gestart met de tag **SequenceStart** die is gedefinieerd door de juiste variabelen. Zie Runbooks voor meer informatie over tagwaarden en variabele [assets.](#runbooks) Schakel het gerelateerde schema **Sequenced-StopVM in.**|

## <a name="use-the-feature-with-classic-vms"></a>De functie gebruiken met klassieke VM's

Als u de functie VM's buiten bedrijfsuren starten/stoppen klassieke VM's gebruikt, verwerkt Automation alle VM's opeenvolgend per cloudservice. VM's worden nog steeds parallel verwerkt in verschillende cloudservices. 

Voor het gebruik van de functie met klassieke VM's hebt u een klassiek Uitvoeren als-account nodig, dat niet standaard wordt gemaakt. Zie Een klassiek Uitvoeren als-account maken voor instructies over het maken van een [klassiek Uitvoeren als-account.](automation-create-standalone-account.md#create-a-classic-run-as-account)

Als u meer dan 20 VM's per cloudservice hebt, volgen hier enkele aanbevelingen:

* Maak meerdere schema's met het bovenliggende runbook **ScheduledStartStop_Parent** en geef 20 VM's per schema op.
* Gebruik in de planningseigenschappen de parameter om VM-namen op te geven als een door komma's gescheiden lijst `VMList` (geen witruimten).

Als de Automation-taak voor deze functie langer dan drie uur wordt uitgevoerd, wordt deze tijdelijk verwijderd of gestopt volgens de [fair share-limiet.](automation-runbook-execution.md#fair-share)

Azure CSP-abonnementen ondersteunen alleen het Azure Resource Manager model. Niet-Azure Resource Manager-services zijn niet beschikbaar in het programma. Wanneer de VM's buiten bedrijfsuren starten/stoppen functie wordt uitgevoerd, ontvangt u mogelijk fouten omdat deze cmdlets heeft voor het beheren van klassieke resources. Zie Beschikbare services in [CSP-abonnementen](/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP. Als u een CSP-abonnement gebruikt, moet u de variabele [External_EnableClassicVMs](#variables) na de implementatie instellen op Onwaar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>De functie weergeven

Gebruik een van de volgende mechanismen om toegang te krijgen tot de ingeschakelde functie:

* Selecteer in uw Automation-account de optie **VM starten/stoppen** onder **Gerelateerde resources.** Selecteer op de pagina VM starten/stoppen de optie De oplossing **beheren** **onder VM-oplossingen beheren/starten/stoppen.**

* Navigeer naar de Log Analytics-werkruimte die is gekoppeld aan uw Automation-account. Nadat u de werkruimte hebt geselecteerd, kiest **u Oplossingen** in het linkerdeelvenster. Selecteer op de pagina Oplossingen **de optie Start-Stop-VM[werkruimte]** in de lijst.  

Als u de functie selecteert, wordt de pagina **Start-Stop-VM[workspace]** weergegeven. Hier kunt u belangrijke details bekijken, zoals de informatie in de **tegel StartStopVM.** Net als in uw Log Analytics-werkruimte geeft deze tegel een telling en een grafische weergave weer van de runbooktaken voor de functie die is gestart en voltooid.

![Automation Updatebeheer pagina](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

U kunt de taakrecords verder analyseren door op de tegel van de donut te klikken. Het dashboard toont de taakgeschiedenis en vooraf gedefinieerde zoekquery's voor logboeken. Schakel over naar de geavanceerde portal van Log Analytics om te zoeken op basis van uw zoekquery's.

## <a name="update-the-feature"></a>De functie bijwerken

Als u een eerdere versie van de VM's buiten bedrijfsuren starten/stoppen, verwijdert u deze uit uw account voordat u een bijgewerkte release implementeert. Volg de stappen om [de functie te verwijderen](automation-solution-vm-management-remove.md#delete-the-feature) en volg vervolgens de stappen om deze in te [stellen.](automation-solution-vm-management-enable.md)

## <a name="next-steps"></a>Volgende stappen

Zie Enable VM's buiten bedrijfsuren starten/stoppen om de functie in te [VM's buiten bedrijfsuren starten/stoppen.](automation-solution-vm-management-enable.md)
