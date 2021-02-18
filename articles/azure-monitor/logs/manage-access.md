---
title: Log Analytics-werk ruimten beheren in Azure Monitor | Microsoft Docs
description: U kunt de toegang beheren tot gegevens die zijn opgeslagen in een Log Analytics werk ruimte in Azure Monitor met behulp van resource, werk ruimte of machtiging op tabel niveau. In dit artikel wordt beschreven hoe u dit kunt volt ooien.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: f16e5363af7de2152a7fa73bb52219d87971a249
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091202"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Toegang tot logboekgegevens en werkruimten beheren in Azure Monitor

Azure Monitor worden [logboek](../logs/data-platform-logs.md) gegevens opgeslagen in een log Analytics-werk ruimte. Een werkruimte is een container die gegevens en configuratie-informatie bevat. Als u de toegang tot logboek gegevens wilt beheren, voert u verschillende beheer taken uit die betrekking hebben op uw werk ruimte.

In dit artikel wordt uitgelegd hoe u de toegang tot logboeken beheert en hoe u de werk ruimten die deze bevat, kunt beheren, met inbegrip van het verlenen van toegang tot: 

* De werk ruimte met werkruimte machtigingen.
* Gebruikers die toegang nodig hebben tot logboek gegevens van specifieke bronnen met behulp van Azure op rollen gebaseerd toegangs beheer (Azure RBAC), ook wel bekend als [resource-context](../logs/design-logs-deployment.md#access-mode)
* Gebruikers die toegang nodig hebben tot logboek gegevens in een specifieke tabel in de werk ruimte met behulp van Azure RBAC.

Lees de [implementatie van uw Azure monitor-logboeken](../logs/design-logs-deployment.md) om inzicht te krijgen in de logboeken van de concepten van Azure RBAC en toegangs strategieën.

## <a name="configure-access-control-mode"></a>Toegangs beheer modus configureren

U kunt de [toegangs beheer modus](../logs/design-logs-deployment.md) die is geconfigureerd op een werk ruimte weer geven vanuit de Azure portal of met Azure PowerShell.  U kunt deze instelling wijzigen met een van de volgende ondersteunde methoden:

* Azure Portal

* Azure PowerShell

* Azure Resource Manager-sjabloon

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

U kunt de huidige toegangs beheer modus voor de werk ruimte weer geven op de pagina **overzicht** van de werk ruimte in het menu van de **log Analytics werk ruimte** .

![Toegangs beheer modus voor werk ruimte weer geven](media/manage-access/view-access-control-mode.png)

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
1. Selecteer in de Azure Portal Log Analytics werk ruimten > uw werk ruimte.

U kunt deze instelling wijzigen op de pagina **Eigenschappen** van de werk ruimte. Het wijzigen van de instelling wordt uitgeschakeld als u geen machtigingen hebt om de werk ruimte te configureren.

![Toegangs modus voor werk ruimte wijzigen](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>PowerShell gebruiken

Gebruik de volgende opdracht om de toegangs beheer modus voor alle werk ruimten in het abonnement te controleren:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

De uitvoer moet er als volgt uitzien:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Een waarde `False` betekent dat de werk ruimte is geconfigureerd met de toegangs modus voor de werk ruimte-context.  Een waarde `True` betekent dat de werk ruimte is geconfigureerd met de toegangs modus voor de resource context.

> [!NOTE]
> Als een werk ruimte wordt geretourneerd zonder een Booleaanse waarde en deze leeg is, komt dit ook overeen met de resultaten van een `False` waarde.
>

Gebruik het volgende script om de toegangs beheer modus voor een specifieke werk ruimte in te stellen op de machtiging resource-context:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Gebruik het volgende script om de toegangs beheer modus voor alle werk ruimten in het abonnement in te stellen op de resource-context machtiging:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Een Resource Manager-sjabloon gebruiken

Als u de toegangs modus in een Azure Resource Manager sjabloon wilt configureren, stelt u de **enableLogAccessUsingOnlyResourcePermissions** -functie vlag in de werk ruimte in op een van de volgende waarden.

* **Onwaar**: Stel de werk ruimte in op de werk ruimte context machtigingen. Dit is de standaard instelling als de vlag niet is ingesteld.
* **waar**: Stel de werk ruimte in op bron-context machtigingen.

## <a name="manage-access-using-workspace-permissions"></a>Toegang beheren met werkruimte machtigingen

Aan elke werk ruimte kunnen meerdere accounts worden gekoppeld en elk account kan toegang hebben tot meerdere werk ruimten. Toegang wordt beheerd met behulp [van Azure RBAC (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/role-assignments-portal.md).

Voor de volgende activiteiten zijn ook Azure-machtigingen vereist:

|Bewerking |Azure-machtigingen nodig |Notities |
|-------|-------------------------|------|
| Bewakings oplossingen toevoegen en verwijderen | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Deze machtigingen moeten worden toegekend op het niveau van de resourcegroep of het abonnement. |
| De prijscategorie wijzigen | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Gegevens weergeven op de tegels *Back-up* en *Site Recovery* | Beheerder/medebeheerder | Heeft toegang tot resources die zijn geïmplementeerd met behulp van het klassieke implementatiemodel |
| Een werkruimte maken in Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Basis eigenschappen van werk ruimte weer geven en de Blade werk ruimte in de portal invoeren | `Microsoft.OperationalInsights/workspaces/read` ||
| Query logboeken met een wille keurige interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Toegang tot alle logboek typen met behulp van query's | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Toegang tot een specifieke logboek tabel | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| De werkruimte sleutels voor het verzenden van logboeken naar deze werk ruimte lezen | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Toegang beheren met Azure-machtigingen

Als u toegang wilt verlenen tot de Log Analytics-werk ruimte met behulp van Azure-machtigingen, volgt u de stappen in [Azure-rollen toewijzen om de toegang tot uw Azure-abonnements resources te beheren](../../role-based-access-control/role-assignments-portal.md). Voor beeld van aangepaste rollen, Zie [voor beelden van aangepaste rollen](#custom-role-examples)

Azure heeft twee ingebouwde gebruikers rollen voor Log Analytics-werk ruimten:

* Lezer van Log Analytics
* Inzender van Log Analytics

Leden van de rol *Lezer van Log Analytics* kunnen:

* Alle controlegegevens weergeven en doorzoeken
* Controlegegevens weergeven, inclusief de configuratie van Azure Diagnostics voor alle Azure-resources.

De rol van Log Analytics lezer omvat de volgende acties van Azure:

| Type    | Machtiging | Beschrijving |
| ------- | ---------- | ----------- |
| Actie | `*/read`   | De mogelijkheid om alle Azure-resources en-resource configuratie weer te geven. Omvat: <br> Status van de VM-extensie <br> Configuratie van Azure Diagnostics voor resources <br> Alle eigenschappen en instellingen van alle resources. <br> Voor werk ruimten kunnen volledige onbeperkte machtigingen de werk ruimte-instellingen lezen en query's uitvoeren op de gegevens. Bekijk meer gedetailleerde opties hierboven. |
| Bewerking | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Afgeschaft, u hoeft ze niet aan gebruikers toe te wijzen. |
| Bewerking | `Microsoft.OperationalInsights/workspaces/search/action` | Afgeschaft, u hoeft ze niet aan gebruikers toe te wijzen. |
| Bewerking | `Microsoft.Support/*` | Mogelijkheid ondersteuningsaanvragen te openen |
|Geen bewerking | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Hiermee wordt voor komen dat de werkruimte sleutel die is vereist voor het gebruik van de gegevensverzamelings-API en agents wordt gelezen. Hiermee wordt voor komen dat de gebruiker nieuwe resources aan de werk ruimte toevoegt |

Leden van de rol *Inzender van Log Analytics* kunnen:

* Bevat alle bevoegdheden van de *rol van log Analytics lezer*, zodat de gebruiker alle bewakings gegevens kan lezen
* Automation-accounts maken en configureren
* Beheeroplossingen toevoegen en verwijderen

    > [!NOTE]
    > Als u de laatste twee acties wilt uitvoeren, moet deze machtiging worden verleend op het niveau van de resource groep of het abonnement.

* Sleutels van opslag account lezen
* De verzameling logboeken van Azure Storage configureren
* De controle-instellingen voor Azure-resources bewerken, inclusief
  * De VM-extensie toevoegen aan virtuele machines
  * Azure Diagnostics configureren op alle Azure-resources

> [!NOTE]
> U kunt de mogelijkheid om een VM-extensie toe te voegen aan een virtuele machine, gebruiken om volledige controle over een virtuele machine te krijgen.

De rol Log Analytics Inzender omvat de volgende acties van Azure:

| Machtiging | Beschrijving |
| ---------- | ----------- |
| `*/read`     | De mogelijkheid om alle resources en de resourceconfiguratie weer te geven. Omvat: <br> Status van de VM-extensie <br> Configuratie van Azure Diagnostics voor resources <br> Alle eigenschappen en instellingen van alle resources. <br> Voor werk ruimten kunnen volledige onbeperkte machtigingen de werk ruimte-instelling lezen en query's uitvoeren op de gegevens. Bekijk meer gedetailleerde opties hierboven. |
| `Microsoft.Automation/automationAccounts/*` | De mogelijkheid om Azure Automation-accounts te maken en te configureren, inclusief het toevoegen en bewerken van runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | VM-extensies toevoegen, bijwerken en verwijderen, met inbegrip van de Microsoft Monitoring Agent-extensie en de OMS Agent for Linux-extensie |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Geef de opslagaccountsleutel weer. Vereist om Log Analytics te configureren om logboeken uit Azure-opslagaccounts te lezen |
| `Microsoft.Insights/alertRules/*` | Waarschuwingsregels toevoegen, bijwerken en verwijderen |
| `Microsoft.Insights/diagnosticSettings/*` | Diagnostische instellingen bij Azure-resources toevoegen, bijwerken en verwijderen |
| `Microsoft.OperationalInsights/*` | De configuratie voor Log Analytics-werk ruimten toevoegen, bijwerken en verwijderen. Gebruikers wensen om geavanceerde instellingen voor de werk ruimte te bewerken `Microsoft.OperationalInsights/workspaces/write` . |
| `Microsoft.OperationsManagement/*` | Beheeroplossingen toevoegen en verwijderen |
| `Microsoft.Resources/deployments/*` | Maak en verwijder implementaties. Vereist voor het toevoegen en verwijderen van oplossingen, werkruimten en Automation-accounts |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Maak en verwijder implementaties. Vereist voor het toevoegen en verwijderen van oplossingen, werkruimten en Automation-accounts |

Als u gebruikers wilt toevoegen aan en verwijderen uit een gebruikersrol, moet u beschikken over machtigingen voor `Microsoft.Authorization/*/Delete` en `Microsoft.Authorization/*/Write`.

Gebruik deze rollen om gebruikers toegang te geven op verschillende niveaus:

* Abonnement: toegang tot alle werkruimten in het abonnement
* Resourcegroep: toegang tot alle werkruimten in de resourcegroep
* Resource: alleen toegang tot de opgegeven werkruimte

We raden aan om toewijzingen op het niveau van de resource (werk ruimte) uit te voeren om nauw keurig toegangs beheer te garanderen. Gebruik [aangepaste rollen](../../role-based-access-control/custom-roles.md) om rollen te maken met de specifieke machtigingen die nodig zijn.

### <a name="resource-permissions"></a>Resource machtigingen

Wanneer gebruikers een query uitvoeren op Logboeken vanuit een werk ruimte met behulp van resource-context toegang, hebben ze de volgende machtigingen voor de resource:

| Machtiging | Beschrijving |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Voorbeelden:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | De mogelijkheid om alle logboek gegevens voor de resource weer te geven.  |
| `Microsoft.Insights/diagnosticSettings/write` | De mogelijkheid om Diagnostische instellingen te configureren om Logboeken in te stellen voor deze bron. |

`/read`machtigingen worden meestal verleend vanuit een rol die _\* /Read of_ _\*_ machtigingen bevat, zoals de ingebouwde functie [lezer](../../role-based-access-control/built-in-roles.md#reader) en [Inzender](../../role-based-access-control/built-in-roles.md#contributor) . Aangepaste rollen met specifieke acties of speciale ingebouwde rollen bevatten mogelijk niet deze machtiging.

Zie [definiëren per-tabel toegangs beheer](#table-level-azure-rbac) hieronder als u een ander toegangs beheer voor verschillende tabellen wilt maken.

## <a name="custom-role-examples"></a>Voor beelden van aangepaste rollen

1. Als u een gebruiker toegang wilt geven tot logboek gegevens vanuit hun resources, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Ken gebruikers `*/read` of `Microsoft.Insights/logs/*/read` machtigingen toe aan hun resources. Als ze al de rol van [log Analytics lezer](../../role-based-access-control/built-in-roles.md#reader) in de werk ruimte zijn toegewezen, is het voldoende.

2. Als u een gebruiker toegang wilt geven tot logboek gegevens van hun resources en de resources wilt configureren voor het verzenden van logboeken naar de werk ruimte, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Gebruikers de volgende machtigingen verlenen voor de werk ruimte: `Microsoft.OperationalInsights/workspaces/read` en `Microsoft.OperationalInsights/workspaces/sharedKeys/action` . Met deze machtigingen kunnen gebruikers geen query's op werkruimte niveau uitvoeren. Ze kunnen de werk ruimte alleen inventariseren en gebruiken als doel voor Diagnostische instellingen of de configuratie van de agent.

    * Gebruikers de volgende machtigingen verlenen voor hun resources: `Microsoft.Insights/logs/*/read` en `Microsoft.Insights/diagnosticSettings/write` . Als aan de gebruiker [log Analytics](../../role-based-access-control/built-in-roles.md#contributor) de rol van rol van lezer is toegewezen, of als aan `*/read` deze resource machtigingen zijn toegekend, is deze voldoende.

3. Als u een gebruiker toegang wilt geven tot logboek gegevens vanuit hun resources zonder dat er beveiligings gebeurtenissen kunnen worden gelezen en gegevens worden verzonden, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Gebruikers de volgende machtigingen verlenen voor hun resources: `Microsoft.Insights/logs/*/read` .

    * Voeg de volgende niet-actie toe om te voor komen dat gebruikers het type SecurityEvent lezen: `Microsoft.Insights/logs/SecurityEvent/read` . De niet-actie moet in dezelfde aangepaste rol worden uitgevoerd als de actie die de Lees machtiging ( `Microsoft.Insights/logs/*/read` ) biedt. Als de gebruiker de actie lezen inherent van een andere rol die is toegewezen aan deze resource of aan het abonnement of de resource groep, kunnen ze alle logboek typen lezen. Dit geldt ook als ze worden overgenomen `*/read` , bijvoorbeeld met de rol lezer of Inzender.

4. Als u een gebruiker toegang wilt geven tot logboek gegevens van hun resources en alle Azure AD-aanmelding wilt lezen en Updatebeheer gegevens van het oplossings logboek van de werk ruimte wilt lezen, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Gebruikers de volgende machtigingen verlenen voor de werk ruimte: 

        * `Microsoft.OperationalInsights/workspaces/read` – vereist zodat de gebruiker de werk ruimte kan opsommen en de Blade werk ruimte kan openen in de Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read` : vereist voor elke gebruiker die query's kan uitvoeren
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – Als u logboeken van Azure AD-aanmelding wilt kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` : Updatebeheer oplossingen voor logboeken kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` : Updatebeheer oplossingen voor logboeken kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – Als u Logboeken voor update beheer wilt lezen
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` -vereist om Updatebeheer oplossing te kunnen gebruiken
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` -vereist om Updatebeheer oplossing te kunnen gebruiken

    * Gebruikers de volgende machtigingen verlenen voor hun resources: `*/read` , toegewezen aan de rol van lezer of `Microsoft.Insights/logs/*/read` . 

## <a name="table-level-azure-rbac"></a>Azure RBAC voor tabel niveau

Met **Azure RBAC op tabel niveau** kunt u naast de andere machtigingen nauw keurigere controle definiëren voor gegevens in een log Analytics-werk ruimte. Met dit besturings element kunt u specifieke gegevens typen definiëren die alleen toegankelijk zijn voor een specifieke groep gebruikers.

U implementeert Table Access Control met [aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md) om toegang te verlenen tot specifieke [tabellen](../logs/data-platform-logs.md) in de werk ruimte. Deze rollen worden toegepast op werk ruimten met [toegangs beheer modi](../logs/design-logs-deployment.md#access-control-mode) werk ruimte-context of resource-context, ongeacht de [toegangs modus](../logs/design-logs-deployment.md#access-mode)van de gebruiker.

Maak een [aangepaste rol](../../role-based-access-control/custom-roles.md) met de volgende acties om toegang te definiëren tot Table Access Control.

* Als u toegang wilt verlenen aan een tabel, neemt u deze op in de sectie **acties** van de roldefinitie. Als u de toegang wilt aftrekken van de toegestane **acties**, neemt u deze op in de sectie **intact** .
* Gebruik micro soft. OperationalInsights/Workspaces/query/* om alle tabellen op te geven.

Als u bijvoorbeeld een rol wilt maken met toegang tot de tabellen _heartbeat_ en _AzureActivity_ , maakt u een aangepaste rol met behulp van de volgende acties:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Als u een rol wilt maken met alleen toegang tot de tabel _Security Baseline Baseline_ , maakt u een aangepaste rol met behulp van de volgende acties:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
In de bovenstaande voor beelden wordt een lijst met toegestane tabellen gedefinieerd. In dit voor beeld wordt de geblokkeerde lijst definitie weer gegeven wanneer een gebruiker toegang heeft tot alle tabellen, maar de _SecurityAlert_ -tabel:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>Aangepaste logboeken

 Aangepaste logboeken worden gemaakt op basis van gegevens bronnen, zoals aangepaste logboeken en HTTP-gegevens verzamelaar-API. De eenvoudigste manier om het type logboek te identificeren, is door de tabellen die worden vermeld onder [aangepaste Logboeken in het logboek schema](../log-query/log-analytics-tutorial.md#table-schema)te controleren.

 U kunt geen toegang verlenen aan afzonderlijke aangepaste logboeken, maar u hebt toegang tot alle aangepaste Logboeken. Als u een rol wilt maken met toegang tot alle aangepaste logboeken, maakt u een aangepaste rol met behulp van de volgende acties:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Een alternatieve methode om de toegang tot aangepaste logboeken te beheren, is door ze toe te wijzen aan een Azure-resource en de toegang te beheren met behulp van het resource-context-paradigma. Als u deze methode wilt gebruiken, moet u de resource-ID opnemen door deze op te geven in de [x-MS-AzureResourceId-](../logs/data-collector-api.md#request-headers) header wanneer gegevens worden opgenomen in log Analytics via de [http data collector-API](../logs/data-collector-api.md). De resource-ID moet geldig zijn en er toegangs regels op zijn toegepast. Nadat de logboeken zijn opgenomen, zijn ze toegankelijk voor degenen met lees toegang tot de resource, zoals hier wordt uitgelegd.

Soms zijn aangepaste logboeken afkomstig uit bronnen die niet rechtstreeks zijn gekoppeld aan een specifieke resource. In dit geval moet u een resource groep maken om de toegang tot deze logboeken te beheren. De resource groep maakt geen kosten in rekening, maar geeft u een geldige resource-ID om de toegang tot de aangepaste logboeken te beheren. Als een specifieke firewall bijvoorbeeld aangepaste logboeken verzendt, maakt u een resource groep met de naam ' MyFireWallLogs ' en zorgt u ervoor dat de API-aanvragen de resource-ID ' MyFireWallLogs ' bevatten. De logboek records van de firewall zijn dan alleen toegankelijk voor gebruikers die toegang hebben gekregen tot MyFireWallLogs of met volledige toegang tot de werk ruimte.          

### <a name="considerations"></a>Overwegingen

* Als een gebruiker algemene Lees machtigingen heeft met de Standard Reader-of Inzender rollen die de _\* /Read_ -actie bevatten, wordt het toegangs beheer per tabel overschreven en hebben ze toegang tot alle logboek gegevens.
* Als een gebruiker toegang verleent per tabel, maar geen andere machtigingen heeft, zouden ze toegang kunnen krijgen tot logboek gegevens vanuit de API, maar niet van de Azure Portal. Als u toegang wilt bieden vanaf de Azure Portal, gebruikt u Log Analytics Reader als basis functie.
* Beheerders en eigen aars van het abonnement hebben toegang tot alle gegevens typen, ongeacht andere machtigings instellingen.
* Werkruimte eigenaren worden beschouwd als elke andere gebruiker voor toegangs beheer per tabel.
* U kunt het beste rollen toewijzen aan beveiligings groepen in plaats van afzonderlijke gebruikers om het aantal toewijzingen te verminderen. Hiermee kunt u ook bestaande hulpprogram ma's voor groeps beheer gebruiken om de toegang te configureren en te controleren.

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van log Analytics agent](../agents/log-analytics-agent.md) voor het verzamelen van gegevens van computers in uw Data Center of een andere cloud omgeving.

* Zie [gegevens verzamelen over Azure virtual machines](../learn/quick-collect-azurevm.md) voor het configureren van gegevens verzameling vanuit Azure vm's.