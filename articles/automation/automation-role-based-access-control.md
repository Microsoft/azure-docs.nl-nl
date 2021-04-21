---
title: Rolmachtigingen en beveiliging beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gebruikt, waarmee toegangsbeheer voor Azure-resources mogelijk is.
keywords: automatisering rbac, rolgebaseerde toegangscontrole, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0727d3342c73d9aa4d15e84aacb82bd8fea01d65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833576"
---
# <a name="manage-role-permissions-and-security"></a>Rolmachtigingen en beveiliging beheren

Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) is toegangsbeheer voor Azure-resources mogelijk. Met [Azure RBAC](../role-based-access-control/overview.md)kunt u taken scheiden binnen uw team en alleen de hoeveelheid toegang verlenen aan gebruikers, groepen en toepassingen die ze nodig hebben om hun taken uit te voeren. U kunt gebruikers op rollen gebaseerde toegang verlenen met behulp van de Azure Portal, Azure Command-Line-hulpprogramma's of Azure Management-API's.

## <a name="roles-in-automation-accounts"></a>Rollen in Automation-accounts

In Azure Automation wordt toegang verleend door de juiste Azure-rol toe te wijzen aan gebruikers, groepen en toepassingen in het bereik van het Automation-account. Hieronder vindt u de ingebouwde rollen die worden ondersteund met een Automation-account:

| **Role** | **Beschrijving** |
|:--- |:--- |
| Eigenaar |Met de rol Eigenaar hebt u toegang tot alle resources en acties binnen een Automation-account, inclusief toegang tot andere gebruikers, groepen en toepassingen voor het beheren van het Automation-account. |
| Inzender |De rol van Bijdrager maakt het mogelijk om alles te beheren, behalve de toegangsrechten van andere gebruikers te wijzigen naar een Automation-account. |
| Lezer |Met de rol van Lezer kunt u alle resources in een Automation-account bekijken, maar niets wijzigen. |
| Automation-operator |Met de rol Automation-operator kunt u de naam en eigenschappen van het runbook weergeven en taken voor alle runbooks in een Automation-account maken en beheren. Deze rol is handig als u uw Automation-accountresources, zoals referentie-assets en runbooks, wilt beveiligen tegen weergave of wijziging, maar nog steeds leden van uw organisatie wilt toestaan om deze runbooks uit te voeren. |
|Automation-taakoperator|Met de rol Automation-taakoperator kunt u taken maken en beheren voor alle runbooks in een Automation-account.|
|Automation-runbookoperator|Met de rol Automation Runbook Operator kunt u de naam en eigenschappen van een runbook weergeven.|
| Inzender van Log Analytics | Met de rol Log Analytics-inzender kunt u alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Het bewerken van bewakingsinstellingen omvat het toevoegen van de VM-extensie aan VM's, het lezen van opslagaccountsleutels voor het configureren van het verzamelen van logboeken uit Azure Storage, het maken en configureren van Automation-accounts, het toevoegen van Azure Automation-functies en het configureren van Diagnostische gegevens van Azure voor alle Azure-resources.|
| Lezer van Log Analytics | Met de rol Lezer van Log Analytics kunt u alle bewakingsgegevens weergeven en doorzoeken, en bewakingsinstellingen weergeven. Dit omvat het weergeven van de configuratie van Diagnostische gegevens van Azure op alle Azure-resources. |
| Controlebijdrager | Met de rol Controlebijdrager kunt u alle bewakingsgegevens lezen en de bewakingsinstellingen bijwerken.|
| Lezer voor bewaking | Met de rol Lezer voor bewaking kunt u alle bewakingsgegevens lezen. |
| Beheerder van gebruikerstoegang |De beheerdersrol gebruiker toegang kunt u gebruikerstoegang tot Azure Automation-accounts beheren. |

## <a name="role-permissions"></a>Rolmachtigingen

In de volgende tabellen worden de specifieke machtigingen voor elke rol beschreven. Dit kunnen acties zijn, die machtigingen geven, en NotActions, die deze beperken.

### <a name="owner"></a>Eigenaar

Een eigenaar kan alles beheren, inclusief toegang. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|Acties|Beschrijving|
|---|---|
|Microsoft.Automation/automationAccounts/|Maak en beheer resources van alle typen.|

### <a name="contributor"></a>Inzender

Een inzender kan alles beheren, behalve toegang. In de volgende tabel ziet u de machtigingen die zijn verleend en geweigerd voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Resources van alle typen maken en beheren|
|**Geen acties**||
|Microsoft.Authorization/*/Delete| Verwijder rollen en roltoewijzingen.       |
|Microsoft.Authorization/*/Write     |  Rollen en roltoewijzingen maken.       |
|Microsoft.Authorization/elevateAccess/Action    | De mogelijkheid om een beheerder voor gebruikerstoegang te maken, wordt niet door de gebruiker toe-eigen.       |

### <a name="reader"></a>Lezer

Een lezer kan alle resources in een Automation-account weergeven, maar kan geen wijzigingen aanbrengen.

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Alles weergeven resources in een Automation-account. |

### <a name="automation-operator"></a>Automation-operator

Een Automation-operator kan taken maken en beheren en runbooknamen en -eigenschappen lezen voor alle runbooks in een Automation-account.

>[!NOTE]
>Als u de operatortoegang tot afzonderlijke runbooks wilt bepalen, moet u deze rol niet instellen. Gebruik in plaats **daarvan de rollen Automation Job Operator** en Automation **Runbook Operator** in combinatie.

In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorisatie lezen.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Lees Hybrid Runbook Worker resources.|
|Microsoft.Automation/automationAccounts/jobs/read|Taken van het runbook op een lijst zetten.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Een taak hervatten die is onderbroken.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annuleer een taak die wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lees de taakstromen en -uitvoer.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Haal de uitvoer van een taak op.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Een taak onderbreken die wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/write|Taken maken.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Haal een Azure Automation taakplanning op.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Maak een Azure Automation taakplanning.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Haal de werkruimte op die is gekoppeld aan het Automation-account.|
|Microsoft.Automation/automationAccounts/read|Haal een Azure Automation account op.|
|Microsoft.Automation/automationAccounts/runbooks/read|Haal een Azure Automation runbook op.|
|Microsoft.Automation/automationAccounts/schedules/read|Haal een Azure Automation asset op.|
|Microsoft.Automation/automationAccounts/schedules/write|Een asset voor een Azure Automation of bijwerken.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Rollen en roltoewijzingen lezen.         |
|Microsoft.Resources/deployments/*      |Resourcegroepimplementaties maken en beheren.         |
|Microsoft.Insights/alertRules/*      | Waarschuwingsregels maken en beheren.        |
|Microsoft.Support/* |Ondersteuningstickets maken en beheren.|

### <a name="automation-job-operator"></a>Automation-taakoperator

De rol Automation-taakoperator wordt verleend in het bereik van het Automation-account.Hierdoor kan de operatormachtigingen taken maken en beheren voor alle runbooks in het account. Als aan de rol Taakoperator leesmachtigingen worden verleend voor de resourcegroep met het Automation-account, kunnen leden van de rol runbooks starten. Ze kunnen ze echter niet maken, bewerken of verwijderen.

In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorisatie lezen.|
|Microsoft.Automation/automationAccounts/jobs/read|Taken van het runbook opstaken.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Een taak hervatten die is onderbroken.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annuleer een taak die wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lees de taakstromen en -uitvoer.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Een taak onderbreken die wordt uitgevoerd.|
|Microsoft.Automation/automationAccounts/jobs/write|Taken maken.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Rollen en roltoewijzingen lezen.       |
|Microsoft.Resources/deployments/*      |Resourcegroepimplementaties maken en beheren.         |
|Microsoft.Insights/alertRules/*      | Waarschuwingsregels maken en beheren.        |
|Microsoft.Support/* |Maak en beheer ondersteuningstickets.|

### <a name="automation-runbook-operator"></a>Automation-runbookoperator

De rol Automation-runbookoperator wordt verleend in het bereik runbook. Een Automation-runbookoperator kan de naam en eigenschappen van het runbook weergeven.Met deze rol in combinatie met **de rol Automation-taakoperator** kan de operator ook taken voor het runbook maken en beheren. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | De runbooks weergeven.        |
|Microsoft.Authorization/*/read      | Autorisatie lezen.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Rollen en roltoewijzingen lezen.         |
|Microsoft.Resources/deployments/*      | Resourcegroepimplementaties maken en beheren.         |
|Microsoft.Insights/alertRules/*      | Waarschuwingsregels maken en beheren.        |
|Microsoft.Support/*      | Ondersteuningstickets maken en beheren.        |

### <a name="log-analytics-contributor"></a>Inzender van Log Analytics

Een Log Analytics-inzender kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Het bewerken van bewakingsinstellingen omvat het toevoegen van de VM-extensie aan VM's; lezen van opslagaccountsleutels om het verzamelen van logboeken van Azure Storage; Automation-accounts maken en configureren; functies toevoegen; en azure diagnostics configureren voor alle Azure-resources. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Lees alle typen resources, met uitzondering van geheimen.|
|Microsoft.Automation/automationAccounts/*|Automation-accounts beheren.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Extensies voor virtuele machines maken en beheren.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Een lijst met klassieke opslagaccountsleutels maken.|
|Microsoft.Compute/virtualMachines/extensions/*|Klassieke extensies voor virtuele machines maken en beheren.|
|Microsoft.Insights/alertRules/*|Waarschuwingsregels voor lezen/schrijven/verwijderen.|
|Microsoft.Insights/diagnosticSettings/*|Diagnostische instellingen lezen/schrijven/verwijderen.|
|Microsoft.OperationalInsights/*|Beheer Azure Monitor logboeken.|
|Microsoft.OperationsManagement/*|Beheer Azure Automation functies in werkruimten.|
|Microsoft.Resources/deployments/*|Resourcegroepimplementaties maken en beheren.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Resourcegroepimplementaties maken en beheren.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lijst met opslagaccountsleutels.|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren.|

### <a name="log-analytics-reader"></a>Lezer van Log Analytics

Een Log Analytics-lezer kan alle bewakingsgegevens bekijken en doorzoeken, evenals bewakingsinstellingen, inclusief het bekijken van de configuratie van Diagnostische gegevens van Azure op alle Azure-resources. In de volgende tabel ziet u de machtigingen die zijn verleend of geweigerd voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Lees resources van alle typen, met uitzondering van geheimen.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Query's beheren in Azure Monitor logboeken.|
|Microsoft.OperationalInsights/workspaces/search/action|Zoek Azure Monitor logboekgegevens.|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren.|
|**Geen acties**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Kan de gedeelde toegangssleutels niet lezen.|

### <a name="monitoring-contributor"></a>Controlebijdrager

Een controlebijdrager kan alle bewakingsgegevens lezen en bewakingsinstellingen bijwerken. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/lezen|Lees resources van alle typen, met uitzondering van geheimen.|
|Microsoft.AlertsManagement/alerts/*|Waarschuwingen beheren.|
|Microsoft.AlertsManagement/alertsSummary/*|Het dashboard Waarschuwing beheren.|
|Microsoft.Insights/AlertRules/*|Waarschuwingsregels beheren.|
|Microsoft.Insights/components/*|Beheer Application Insights onderdelen.|
|Microsoft.Insights/DiagnosticSettings/*|Diagnostische instellingen beheren.|
|Microsoft.Insights/eventtypes/*|Activiteitenlogboekgebeurtenissen (beheergebeurtenissen) in een abonnement op een lijst zetten. Deze machtiging is van toepassing op zowel programmatische als portaltoegang tot het activiteitenlogboek.|
|Microsoft.Insights/LogDefinitions/*|Deze machtiging is nodig voor gebruikers die toegang nodig hebben tot activiteitenlogboeken via de portal. Lijst met logboekcategorieën in activiteitenlogboek.|
|Microsoft.Insights/MetricDefinitions/*|Metrische definities lezen (lijst met beschikbare metrische typen voor een resource).|
|Microsoft.Insights/Metrics/*|Metrische gegevens voor een resource lezen.|
|Microsoft.Insights/Register/Action|Registreer de Microsoft.Insights-provider.|
|Microsoft.Insights/webtests/*|Beheer Application Insights webtests.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Beheer oplossingspakketten Azure Monitor logboeken.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Opgeslagen Azure Monitor logboeken beheren.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics-werkruimten doorzoeken.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lijst met sleutels voor een Log Analytics-werkruimte.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Opslaginzichtconfiguraties Azure Monitor logboeken beheren.|
|Microsoft.Support/*|Maak en beheer ondersteuningstickets.|
|Microsoft.WorkloadMonitor/workloads/*|Werkbelastingen beheren.|

### <a name="monitoring-reader"></a>Lezer voor bewaking

Een bewakingslezer kan alle bewakingsgegevens lezen. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/read|Lees alle typen resources, met uitzondering van geheimen.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics-werkruimten doorzoeken.|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren|

### <a name="user-access-administrator"></a>Beheerder van gebruikerstoegang

Een beheerder van gebruikerstoegang kan gebruikerstoegang tot Azure-resources beheren. In de volgende tabel ziet u de machtigingen die zijn verleend voor de rol:

|**Acties**  |**Beschrijving**  |
|---------|---------|
|*/read|Alle resources lezen|
|Microsoft.Authorization/*|Autorisatie beheren|
|Microsoft.Support/*|Ondersteuningstickets maken en beheren|

## <a name="feature-setup-permissions"></a>Installatiemachtigingen voor functies

In de volgende secties worden de minimaal vereiste machtigingen beschreven die nodig zijn om de functies Updatebeheer en Wijzigingen bijhouden en inventaris in te stellen.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Machtigingen voor het inschakelen Updatebeheer en Wijzigingen bijhouden en inventaris vanaf een VM

|**Actie**  |**Machtiging**  |**Minimumbereik**  |
|---------|---------|---------|
|Nieuwe implementatie schrijven      | Microsoft.Resources/deployments/*          |Abonnement          |
|Nieuwe resourcegroep schrijven      | Microsoft.Resources/subscriptions/resourceGroups/write        | Abonnement          |
|Nieuwe standaardwerkruimte maken      | Microsoft.OperationalInsights/workspaces/write         | Resourcegroep         |
|Nieuw account maken      |  Microsoft.Automation/automationAccounts/write        |Resourcegroep         |
|Werkruimte en account koppelen      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Werkruimte</br>Automation-account
|MMA-extensie maken      | Microsoft.Compute/virtualMachines/write         | Virtuele machine         |
|Opgeslagen zoekopdracht maken      | Microsoft.OperationalInsights/workspaces/write          | Werkruimte         |
|Scope-configuratie maken      | Microsoft.OperationalInsights/workspaces/write          | Werkruimte         |
|Statuscontrole van onboarding - Werkruimte lezen      | Microsoft.OperationalInsights/workspaces/read         | Werkruimte         |
|Statuscontrole van onboarding : de eigenschap Gekoppelde werkruimte van het account lezen     | Microsoft.Automation/automationAccounts/read      | Automation-account        |
|Statuscontrole van onboarding - Oplossing lezen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Oplossing         |
|Statuscontrole van onboarding - Lees-VM      | Microsoft.Compute/virtualMachines/read         | Virtuele machine         |
|Statuscontrole van onboarding - Leesaccount      | Microsoft.Automation/automationAccounts/read  |  Automation-account   |
| Werkruimtecontrole voor onboarding voor VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Abonnement         |
| De Log Analytics-provider registreren |Microsoft.Insights/register/action | Abonnement|

<sup>1 Deze</sup> machtiging is nodig om functies in te kunnenschakelen via de VM-portal.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Machtigingen voor het inschakelen Updatebeheer en Wijzigingen bijhouden en inventaris vanuit een Automation-account

|**Actie**  |**Machtiging** |**Minimumbereik**  |
|---------|---------|---------|
|Nieuwe implementatie maken     | Microsoft.Resources/deployments/*        | Abonnement         |
|Nieuwe resourcegroep maken     | Microsoft.Resources/subscriptions/resourceGroups/write         | Abonnement        |
|Blade AutomationOnboarding - Nieuwe werkruimte maken     |Microsoft.OperationalInsights/workspaces/write           | Resourcegroep        |
|Blade AutomationOnboarding - gekoppelde werkruimte lezen     | Microsoft.Automation/automationAccounts/read        | Automation-account       |
|Blade AutomationOnboarding - oplossing voor lezen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Oplossing        |
|Blade AutomationOnboarding - werkruimte lezen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Werkruimte        |
|Koppeling maken voor werkruimte en account     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
|Account schrijven voor schoenenbox      | Microsoft.Automation/automationAccounts/write        | Account        |
|Opgeslagen zoekopdracht maken/bewerken     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
|Configuratie van bereik maken/bewerken     | Microsoft.OperationalInsights/workspaces/write        | Werkruimte        |
| De Log Analytics-provider registreren |Microsoft.Insights/register/action | Abonnement|
|**Stap 2: meerdere VM's inschakelen**     |         |         |
|Blade VMOnboarding - MMA-extensie maken     | Microsoft.Compute/virtualMachines/write           | Virtuele machine        |
|Opgeslagen zoekopdracht maken/bewerken     | Microsoft.OperationalInsights/workspaces/write           | Werkruimte        |
|Configuratie van bereik maken/bewerken  | Microsoft.OperationalInsights/workspaces/write   | Werkruimte|

## <a name="update-management-permissions"></a>Machtigingen voor updatebeheer

Updatebeheer bereikt meerdere services om de service te leveren. In de volgende tabel ziet u de machtigingen die nodig zijn voor het beheren van updatebeheerimplementaties:

|**Resource**  |**Role**  |**Scope**  |
|---------|---------|---------|
|Automation-account     | Inzender van Log Analytics       | Automation-account        |
|Automation-account    | Inzender voor virtuele machines        | Resourcegroep voor het account        |
|Log Analytics-werkruimte     | Inzender van Log Analytics| Log Analytics-werkruimte        |
|Log Analytics-werkruimte |Lezer van Log Analytics| Abonnement|
|Oplossing     |Inzender van Log Analytics         | Oplossing|
|Virtuele machine     | Inzender voor virtuele machines        | Virtuele machine        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Azure RBAC configureren voor uw Automation-account

In de volgende sectie ziet u hoe u Azure RBAC configureert in uw Automation-account via [Azure Portal](#configure-azure-rbac-using-the-azure-portal) en [PowerShell.](#configure-azure-rbac-using-powershell)

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Azure RBAC configureren met behulp van de Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en open uw Automation-account via de pagina Automation-accounts.
2. Klik op **Toegangsbeheer (IAM) om** de pagina Toegangsbeheer (IAM) te openen. U kunt deze pagina gebruiken om nieuwe gebruikers, groepen en toepassingen toe te voegen om uw Automation-account te beheren en bestaande rollen weer te geven die kunnen worden geconfigureerd voor het Automation-account.
3. Klik op het tabblad **Roltoewijzingen**.

   ![De knop Toegang](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Een nieuwe gebruiker toevoegen en een rol toewijzen

1. Klik op de pagina Toegangsbeheer (IAM) op **+ Roltoewijzing toevoegen.** Met deze actie wordt de pagina Roltoewijzing toevoegen geopend, waar u een gebruiker, groep of toepassing kunt toevoegen en een bijbehorende rol kunt toewijzen.

2. Selecteer een rol in de lijst met beschikbare rollen. U kunt een van de beschikbare ingebouwde rollen kiezen die door een Automation-account worden ondersteund of een aangepaste rol die u mogelijk hebt gedefinieerd.

3. Typ de naam van de gebruiker aan wie u machtigingen wilt verlenen in het **veld** Selecteren. Kies de gebruiker in de lijst en klik op **Opslaan.**

   ![Gebruikers toevoegen](media/automation-role-based-access-control/automation-04-add-users.png)

   U ziet nu dat de gebruiker is toegevoegd aan de pagina Gebruikers, met de geselecteerde rol toegewezen.

   ![Gebruikers weergeven](media/automation-role-based-access-control/automation-05-list-users.png)

   U kunt ook een rol aan de gebruiker toewijzen via de pagina Rollen.

4. Klik **op Rollen** op de pagina Toegangsbeheer (IAM) om de pagina Rollen te openen. U kunt de naam van de rol en het aantal gebruikers en groepen weergeven dat aan die rol is toegewezen.

    ![Rol toewijzen vanaf pagina Gebruikers](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > U kunt op rollen gebaseerd toegangsbeheer alleen instellen in het bereik van het Automation-account en niet op een resource onder het Automation-account.

#### <a name="remove-a-user"></a>Een gebruiker verwijderen

U kunt de toegangsmachtiging verwijderen voor een gebruiker die het Automation-account niet beheert of die niet meer voor de organisatie werkt. Hieronder vindt u de stappen voor het verwijderen van een gebruiker:

1. Selecteer op de pagina Toegangsbeheer (IAM) de gebruiker die u wilt verwijderen en klik op **Verwijderen.**
2. Klik op de pagina met toewijzingsdetails op de knop **Verwijderen**.
3. Klik op **Ja** om het verwijderen te bevestigen.

   ![Gebruikers verwijderen](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Azure RBAC configureren met behulp van PowerShell

U kunt ook op rollen gebaseerde toegang tot een Automation-account configureren met behulp van de [volgende Azure PowerShell-cmdlets:](../role-based-access-control/role-assignments-powershell.md)

[Get-AzRoleDefinition bevat](/powershell/module/Az.Resources/Get-AzRoleDefinition) een lijst met alle Azure-rollen die beschikbaar zijn in Azure Active Directory. U kunt deze cmdlet gebruiken met de parameter om een lijst weer te geven van alle acties `Name` die een specifieke rol kan uitvoeren.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Hier volgt de voorbeelduitvoer:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment bevat](/powershell/module/az.resources/get-azroleassignment) een lijst met Azure-roltoewijzingen voor het opgegeven bereik. Zonder parameters retourneert deze cmdlet alle roltoewijzingen die zijn gemaakt onder het abonnement. Gebruik de parameter om toegangstoewijzingen voor de opgegeven gebruiker weer te geven, evenals de groepen `ExpandPrincipalGroups` waar de gebruiker bij hoort.

**Voorbeeld:** Gebruik de volgende cmdlet om alle gebruikers en hun rollen in een Automation-account weer te geven.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Hier volgt de voorbeelduitvoer:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Gebruik [New-AzRoleAssignment om](/powershell/module/Az.Resources/New-AzRoleAssignment) toegang tot gebruikers, groepen en toepassingen toe te wijzen aan een bepaald bereik.

**Voorbeeld:** Gebruik de volgende opdracht om de rol Automation-operator toe te wijzen aan een gebruiker in het Automation-accountbereik.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Hier volgt de voorbeelduitvoer:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Gebruik [Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment) om de toegang van een opgegeven gebruiker, groep of toepassing uit een bepaald bereik te verwijderen.

**Voorbeeld:** Gebruik de volgende opdracht om de gebruiker te verwijderen uit de rol Automation-operator in het Automation-accountbereik.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Vervang in het voorgaande voorbeeld `sign-in ID of a user you wish to remove` , , en door uw `SubscriptionID` `Resource Group Name` `Automation account name` accountgegevens. Kies **Ja wanneer** u wordt gevraagd om te bevestigen voordat u doorgaat met het verwijderen van gebruikersroltoewijzingen.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Gebruikerservaring voor de rol Automation-operator - Automation-account

Wanneer een gebruiker die is toegewezen aan de rol Automation-operator in het Bereik van het Automation-account het Automation-account bekijkt waaraan hij/zij is toegewezen, kan de gebruiker alleen de lijst weergeven met runbooks, runbooktaken en schema's die zijn gemaakt in het Automation-account. Deze gebruiker kan de definities van deze items niet weergeven. De gebruiker kan de runbook-taak starten, stoppen, opschorten, hervatten of plannen. De gebruiker heeft echter geen toegang tot andere Automation-resources, zoals configuraties, hybrid worker-groepen of DSC-knooppunten.

![Geen toegang tot resources](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Azure RBAC configureren voor runbooks

Azure Automation kunt u Azure-rollen toewijzen aan specifieke runbooks. Voer hiervoor het volgende script uit om een gebruiker toe te voegen aan een specifiek runbook. Een Automation-accountbeheerder of een Tenant Administrator kan dit script uitvoeren.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Nadat het script is uitgevoerd, moet de gebruiker zich aanmelden bij de Azure Portal selecteert u **Alle resources.** In de lijst kan de gebruiker het runbook zien waarvoor hij/zij is toegevoegd als automation-runbookoperator.

![Runbook Azure RBAC in de portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Gebruikerservaring voor automation-operatorrol - Runbook

Wanneer een gebruiker die is toegewezen aan de rol Automation-operator in het runbookbereik een toegewezen runbook bekijkt, kan de gebruiker het runbook alleen starten en de runbooktaken bekijken.

![Heeft alleen toegang om te starten](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Volgende stappen

* Zie Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell voor meer informatie over Azure RBAC [met behulp van PowerShell.](../role-based-access-control/role-assignments-powershell.md)
* Zie runbooktypen voor meer informatie over [de typen runbooks Azure Automation runbooktypen.](automation-runbook-types.md)
* Zie Een runbook starten in Azure Automation om [een runbook te Azure Automation.](start-runbooks.md)
