---
title: Bewakings oplossingen in Azure Monitor | Microsoft Docs
description: Bewakings oplossingen in Azure Monitor zijn een verzameling regels voor logica, visualisatie en gegevens aanschaf die gegevens over een bepaald probleem gebied hebben gedraaid.  Dit artikel bevat informatie over het installeren en gebruiken van bewakings oplossingen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c38fa77951aaeb5559dc2030f1401a896d7c238d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841917"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Bewakings oplossingen in Azure Monitor

Bewakings oplossingen in Azure Monitor bieden een analyse van de werking van een bepaalde Azure-toepassing of-service. Dit artikel bevat een kort overzicht van de bewakings oplossingen in Azure en informatie over het gebruik en de installatie ervan. U kunt bewakings oplossingen toevoegen aan Azure Monitor voor alle toepassingen en services die u gebruikt. Deze zijn doorgaans gratis beschikbaar, maar verzamelen gegevens die gebruiks kosten kunnen aanroepen.

## <a name="use-monitoring-solutions"></a>Bewakingsoplossingen gebruiken

Op de pagina **overzicht** van oplossingen in azure monitor wordt een tegel weer gegeven voor elke oplossing die in een log Analytics-werk ruimte is geïnstalleerd. Als u deze pagina wilt openen, gaat u naar **Azure monitor** in het [Azure Portal](https://ms.portal.azure.com). Selecteer in het menu **inzichten** **meer** om de Insights- **hub** te openen en klik vervolgens op **log Analytics-werk ruimten**.

[![Insights-hub](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Gebruik de vervolg keuzelijsten aan de bovenkant van het scherm om de werk ruimte of het tijds bereik te wijzigen dat wordt gebruikt voor de tegels. Klik op de tegel voor een oplossing om de bijbehorende weer gave te openen met meer gedetailleerde analyse van de verzamelde gegevens.

[![Scherm afbeelding toont het Azure Portal menu met de geselecteerde oplossingen en oplossingen die worden weer gegeven in het deel venster oplossingen.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Bewakings oplossingen kunnen meerdere typen Azure-resources bevatten en u kunt alle resources die zijn opgenomen in een oplossing, net als elke andere resource bekijken. Alle logboek query's die in de oplossing zijn opgenomen, worden bijvoorbeeld vermeld onder **oplossingen query's** in [query Explorer](../log-query/get-started-portal.md#load-queries) . u kunt deze query's gebruiken bij het uitvoeren van ad hoc analyses met [logboek query's](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Geïnstalleerde bewakings oplossingen weer geven

### <a name="portal"></a>[Portal](#tab/portal)

Gebruik de volgende procedure om de bewakings oplossingen weer te geven die in uw abonnement zijn geïnstalleerd.

1. Ga naar de [Azure Portal](https://ms.portal.azure.com). Zoek en selecteer **oplossingen**.
1. Oplossingen die in al uw werk ruimten zijn geïnstalleerd, worden weer gegeven. De naam van de oplossing wordt gevolgd door de naam van de werk ruimte waarin deze is geïnstalleerd.
1. Gebruik de vervolg keuzelijsten aan de bovenkant van het scherm om te filteren op abonnement of resource groep.

![Alle oplossingen weer geven](media/solutions/list-solutions-all.png)

Klik op de naam van een oplossing om de bijbehorende pagina samen vatting te openen. Op deze pagina ziet u alle weer gaven die zijn opgenomen in de oplossing en worden verschillende opties geboden voor de oplossing zelf en de bijbehorende werk ruimte. Bekijk de overzichts pagina voor een oplossing met behulp van een van de bovenstaande procedures om oplossingen weer te geven en klik vervolgens op de naam van de oplossing.

![Eigenschappen van oplossing](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [AZ monitor log-Analytics oplossingen List](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) om de bewakings oplossingen weer te geven die in uw abonnement zijn geïnstalleerd.   Voordat u de `list` opdracht uitvoert, volgt u de vereisten voor het [installeren van een bewakings oplossing](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Gebruik de cmdlet [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) om de bewakings oplossingen weer te geven die in uw abonnement zijn geïnstalleerd. Voordat u deze opdrachten uitvoert, volgt u de vereisten voor het [installeren van een bewakings oplossing](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Een bewakings oplossing installeren

### <a name="portal"></a>[Portal](#tab/portal)

Bewakings oplossingen van micro soft en partners zijn beschikbaar via [Azure Marketplace](https://azuremarketplace.microsoft.com). U kunt zoeken naar beschik bare oplossingen en deze installeren met behulp van de volgende procedure. Wanneer u een oplossing installeert, moet u een [log Analytics-werk ruimte](../platform/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld.

1. Klik in de [lijst met oplossingen voor uw abonnement](#list-installed-monitoring-solutions)op **toevoegen**.
1. Blader of zoek naar een oplossing. U kunt ook naar oplossingen bladeren via [deze zoek koppeling](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Zoek de gewenste bewakings oplossing en lees de beschrijving.
1. Klik op **maken** om het installatie proces te starten.
1. Wanneer het installatie proces wordt gestart, wordt u gevraagd om de Log Analytics-werk ruimte op te geven en de vereiste configuratie voor de oplossing op te geven.

![Een oplossing installeren](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Een oplossing installeren vanuit de Community

Leden van de community kunnen beheer oplossingen verzenden naar Azure Quick Start-sjablonen. U kunt deze oplossingen direct installeren of de sjablonen downloaden voor een latere installatie.

1. Volg het proces dat wordt beschreven in [log Analytics werk ruimte en het Automation-account](#log-analytics-workspace-and-automation-account) om een werk ruimte en een account te koppelen.
2. Ga naar de [Azure Quick](https://azure.microsoft.com/documentation/templates/)start-sjablonen.
3. Zoek naar een oplossing waarin u bent geïnteresseerd.
4. Selecteer de oplossing in de resultaten om de details ervan weer te geven.
5. Klik op de knop **Implementeren in Azure**.
6. U wordt gevraagd om informatie op te geven, zoals de resource groep en de locatie, naast de waarden voor eventuele para meters in de oplossing.
7. Klik op **kopen** om de oplossing te installeren.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Azure-CLI installeren

   U moet [de Azure cli installeren](/cli/azure/install-azure-cli) voordat u de CLI-verwijzings opdrachten uitvoert.  Als u wilt, kunt u ook Azure Cloud Shell gebruiken om de stappen in dit artikel uit te voeren.  Azure Cloud Shell is een interactieve shell-omgeving die u via uw browser gebruikt.  Start Cloud Shell met een van de volgende methoden:

   - Open Cloud Shell door naar te gaan [https://shell.azure.com](https://shell.azure.com)

   - Selecteer de knop **Cloud shell** in de menu balk in de rechter bovenhoek van de [Azure Portal](https://portal.azure.com)

1. Meld u aan.

   Als u een lokale installatie van de CLI gebruikt, meldt u zich aan met de opdracht [AZ login](/cli/azure/reference-index#az-login) .  Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

    ```azurecli
    az login
    ```

1. De extensie `log-analytics-solution` installeren

   De `log-analytics-solution` opdracht is een experimentele uitbrei ding van de Azure cli-kern. Meer informatie over extensie Verwijzingen in [use extension with Azure cli](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   De volgende waarschuwing wordt verwacht.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Een oplossing met Azure CLI installeren

Wanneer u een oplossing installeert, moet u een [log Analytics-werk ruimte](../platform/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld.  Met de Azure CLI beheert u werk ruimten met behulp van de opdracht [AZ monitor log-Analytics werk ruimte](/cli/azure/monitor/log-analytics/workspace) .  Volg het proces dat wordt beschreven in [log Analytics werk ruimte en het Automation-account](#log-analytics-workspace-and-automation-account) om een werk ruimte en een account te koppelen.

Gebruik de [AZ monitor log-Analytics oplossing Create](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) om een bewakings oplossing te installeren.  Para meters tussen vier Kante haken zijn optioneel.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Hier volgt een code voorbeeld voor het maken van een oplossing voor logboek analyse voor het plan product OMSGallery/containers.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Azure PowerShell installeren

   U moet [Azure PowerShell installeren](/powershell/azure/install-az-ps) voordat u Azure PowerShell-verwijzings opdrachten uitvoert. Als u wilt, kunt u ook Azure Cloud Shell gebruiken om de stappen in dit artikel uit te voeren. Azure Cloud Shell is een interactieve shell-omgeving die u via uw browser gebruikt. Start Cloud Shell met een van de volgende methoden:

   - Open Cloud Shell door naar te gaan [https://shell.azure.com](https://shell.azure.com)

   - Selecteer de knop **Cloud shell** in de menu balk in de rechter bovenhoek van de [Azure Portal](https://portal.azure.com)

   > [!IMPORTANT]
   > Hoewel de Power shell-module **AZ. MonitoringSolutions** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Meld u aan.

   Als u een lokale installatie van Power shell gebruikt, meldt u zich aan met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Volg de stappen die worden weer gegeven in Power shell om het verificatie proces te volt ooien.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Een oplossing met Azure PowerShell installeren

Wanneer u een oplossing installeert, moet u een [log Analytics-werk ruimte](../platform/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld. Met Azure PowerShell beheert u werk ruimten met behulp van de cmdlets in de Power shell-module [AZ. MonitoringSolutions](/powershell/module/az.monitoringsolutions) . Volg het proces dat wordt beschreven in [log Analytics werk ruimte en het Automation-account](#log-analytics-workspace-and-automation-account) om een werk ruimte en een account te koppelen.

Gebruik de cmdlet [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) om een bewakings oplossing te installeren. Para meters tussen vier Kante haken zijn optioneel.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

In het volgende voor beeld wordt een bewaking log Analytics-oplossing voor de log Analytics-werk ruimte gemaakt.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-werk ruimte en Automation-account

Voor alle bewakings oplossingen is een [log Analytics-werk ruimte](../platform/manage-access.md) vereist voor het opslaan van gegevens die zijn verzameld door de oplossing en het vinden van zoek opdrachten in Logboeken en weer gaven. Sommige oplossingen hebben ook een [Automation-account](../../automation/automation-security-overview.md) nodig om runbooks en gerelateerde resources te bevatten. De werk ruimte en het account moeten voldoen aan de volgende vereisten.

* Bij elke installatie van een oplossing kan slechts één Log Analytics-werk ruimte en één Automation-account worden gebruikt. U kunt de oplossing afzonderlijk in meerdere werk ruimten installeren.
* Als voor een oplossing een Automation-account is vereist, moet de Log Analytics-werk ruimte en het Automation-account aan elkaar zijn gekoppeld. Een Log Analytics-werk ruimte kan alleen worden gekoppeld aan een Automation-account en een Automation-account kan alleen worden gekoppeld aan een Log Analytics-werk ruimte.

Wanneer u een oplossing installeert via Azure Marketplace, wordt u gevraagd naar een werk ruimte en een Automation-account. De koppeling tussen deze wordt gemaakt als ze nog niet zijn gekoppeld.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>De koppeling tussen een Log Analytics-werk ruimte en een Automation-account controleren

U kunt de koppeling tussen een Log Analytics-werk ruimte en een Automation-account controleren aan de hand van de volgende procedure.

1. Selecteer het Automation-account in de Azure Portal.
1. Ga naar de sectie **Verwante resources** van het menu en selecteer **gekoppelde werk ruimte**.
1. Als de **werk ruimte** is gekoppeld aan een Automation-account, wordt op deze pagina de werk ruimte weer gegeven waaraan deze is gekoppeld. Als u de naam van de weer gegeven werk ruimte selecteert, wordt u omgeleid naar de overzichts pagina voor die werk ruimte.

## <a name="remove-a-monitoring-solution"></a>Een bewakings oplossing verwijderen

### <a name="portal"></a>[Portal](#tab/portal)

Als u een geïnstalleerde oplossing wilt verwijderen met behulp van de portal, zoekt u deze in de [lijst met geïnstalleerde oplossingen](#list-installed-monitoring-solutions). Klik op de naam van de oplossing om de bijbehorende overzichts pagina te openen en klik vervolgens op **verwijderen**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een geïnstalleerde oplossing wilt verwijderen met behulp van de Azure CLI, gebruikt u de opdracht [AZ monitor log-Analytics Solution delete](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete) .

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Als u een geïnstalleerde oplossing met behulp van Azure PowerShell wilt verwijderen, gebruikt u de cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Volgende stappen

* Een [lijst met bewakings oplossingen van micro soft](../monitor-reference.md)ophalen.
* Meer informatie over het [maken van query's](../log-query/log-query-overview.md) voor het analyseren van gegevens die zijn verzameld door bewakings oplossingen.
* Bekijk alle [Azure cli-opdrachten voor Azure monitor](/cli/azure/azure-cli-reference-for-monitor).
