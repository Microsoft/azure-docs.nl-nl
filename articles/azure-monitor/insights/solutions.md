---
title: Bewakingsoplossingen in Azure Monitor | Microsoft Docs
description: Bewakingsoplossingen in Azure Monitor zijn een verzameling regels voor logica, visualisatie en gegevensverzameling die metrische gegevens bieden die zijn rond een bepaald probleemgebied.  Dit artikel bevat informatie over het installeren en gebruiken van bewakingsoplossingen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8a18a47331eb5d4a9ed5578cca320beef5e0ba45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766983"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Bewakingsoplossingen in Azure Monitor

Bewakingsoplossingen in Azure Monitor analyse van de werking van een bepaalde Azure-toepassing of -service. Dit artikel biedt een kort overzicht van bewakingsoplossingen in Azure en details over het gebruik en de installatie ervan. U kunt bewakingsoplossingen toevoegen aan Azure Monitor toepassingen en services die u gebruikt. Ze zijn doorgaans gratis beschikbaar, maar verzamelen gegevens die gebruikskosten kunnen aanroepen.

## <a name="use-monitoring-solutions"></a>Bewakingsoplossingen gebruiken

Op de **pagina Overzicht** van oplossingen in Azure Monitor een tegel weergegeven voor elke oplossing die is geïnstalleerd in een Log Analytics-werkruimte. Als u deze pagina wilt openen, gaat **u naar Azure Monitor** in Azure Portal . [](https://ms.portal.azure.com) Selecteer in **het** menu Inzichten de optie **Meer om** de **Insights Hub te openen** en klik vervolgens op Log **Analytics-werkruimten.**

[![Insights Hub](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Gebruik de vervolgkeuzevakken boven aan het scherm om de werkruimte of het tijdsbereik voor de tegels te wijzigen. Klik op de tegel voor een oplossing om de weergave te openen die gedetailleerdere analyse van de verzamelde gegevens bevat.

[![Schermopname van Azure Portal menu met Oplossingen geselecteerd en oplossingen weergegeven in het deelvenster Oplossingen.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Bewakingsoplossingen kunnen meerdere typen Azure-resources bevatten en u kunt alle resources bekijken die zijn opgenomen in een oplossing, net als elke andere resource. Logboekquery's die in de oplossing zijn  opgenomen, worden bijvoorbeeld vermeld onder Oplossingsquery's in [Queryverkenner.](../logs/log-analytics-tutorial.md) U kunt deze query's gebruiken bij het uitvoeren van ad-hocanalyse met [logboekquery's.](../logs/log-query-overview.md)

## <a name="list-installed-monitoring-solutions"></a>Geïnstalleerde bewakingsoplossingen op een lijst zetten

### <a name="portal"></a>[Portal](#tab/portal)

Gebruik de volgende procedure om de bewakingsoplossingen weer te bieden die in uw abonnement zijn geïnstalleerd.

1. Ga naar de [Azure Portal](https://ms.portal.azure.com). Zoek en selecteer **Oplossingen.**
1. Oplossingen die in al uw werkruimten zijn geïnstalleerd, worden weergegeven. De naam van de oplossing wordt gevolgd door de naam van de werkruimte waarin deze is geïnstalleerd.
1. Gebruik de vervolgkeuzevakken bovenaan het scherm om te filteren op abonnement of resourcegroep.

![Een lijst met alle oplossingen maken](media/solutions/list-solutions-all.png)

Klik op de naam van een oplossing om de overzichtspagina te openen. Op deze pagina worden alle weergaven weergegeven die in de oplossing zijn opgenomen en vindt u verschillende opties voor de oplossing zelf en de werkruimte. Bekijk de overzichtspagina voor een oplossing met behulp van een van de bovenstaande procedures om oplossingen weer te geven en klik vervolgens op de naam van de oplossing.

![Oplossingseigenschappen](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de [opdracht az monitor log-analytics solution list](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) om de bewakingsoplossingen weer te geven die in uw abonnement zijn geïnstalleerd.   Voordat u de `list` opdracht gaat uitvoeren, volgt u de vereisten in [Een bewakingsoplossing installeren.](#install-a-monitoring-solution)

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Gebruik de cmdlet [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) om de bewakingsoplossingen weer te bieden die in uw abonnement zijn geïnstalleerd. Voordat u deze opdrachten gaat uitvoeren, volgt u de vereisten in [Een bewakingsoplossing installeren.](#install-a-monitoring-solution)

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Een bewakingsoplossing installeren

### <a name="portal"></a>[Portal](#tab/portal)

Bewakingsoplossingen van Microsoft en partners zijn beschikbaar via [Azure Marketplace.](https://azuremarketplace.microsoft.com) U kunt beschikbare oplossingen zoeken en deze installeren met behulp van de volgende procedure. Wanneer u een oplossing installeert, moet u een [Log Analytics-werkruimte](../logs/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld.

1. Klik in [de lijst met oplossingen voor uw abonnement](#list-installed-monitoring-solutions)op **Toevoegen.**
1. Blader of zoek naar een oplossing. U kunt ook door oplossingen bladeren via [deze zoekkoppeling.](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)
1. Zoek de bewakingsoplossing die u wilt en lees de beschrijving ervan door.
1. Klik **op Maken** om het installatieproces te starten.
1. Wanneer het installatieproces wordt gestart, wordt u gevraagd om de Log Analytics-werkruimte op te geven en de vereiste configuratie voor de oplossing op te geven.

![Een oplossing installeren](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Een oplossing van de community installeren

Leden van de community kunnen beheeroplossingen indienen bij Azure-quickstartsjablonen. U kunt deze oplossingen rechtstreeks installeren of deze sjablonen downloaden voor een latere installatie.

1. Volg het proces dat wordt beschreven in [Log Analytics-werkruimte en Automation-account](#log-analytics-workspace-and-automation-account) om een werkruimte en account te koppelen.
2. Ga naar [Azure-snelstartsjablonen.](https://azure.microsoft.com/documentation/templates/)
3. Zoek naar een oplossing waarin u bent geïnteresseerd.
4. Selecteer de oplossing in de resultaten om de details ervan weer te geven.
5. Klik op de knop **Implementeren in Azure**.
6. U wordt gevraagd om naast waarden voor parameters in de oplossing ook informatie op te geven, zoals de resourcegroep en locatie.
7. Klik **op Kopen** om de oplossing te installeren.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Azure-CLI installeren

   U moet de [Azure CLI installeren voordat u CLI-referentieopdrachten](/cli/azure/install-azure-cli) kunt uitvoeren.  Als u dat liever hebt, kunt u ook Azure Cloud Shell de stappen in dit artikel uit te voeren.  Azure Cloud Shell is een interactieve shell-omgeving die u via uw browser gebruikt.  Begin Cloud Shell met behulp van een van deze methoden:

   - Open Cloud Shell door naar te gaan [https://shell.azure.com](https://shell.azure.com)

   - Selecteer de **Cloud Shell** in de menubalk in de rechterbovenhoek van de [Azure Portal](https://portal.azure.com)

1. Meld u aan.

   Als u een lokale installatie van de CLI gebruikt, meld u zich dan aan met de [opdracht az login.](/cli/azure/reference-index#az_login)  Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

    ```azurecli
    az login
    ```

1. De extensie `log-analytics-solution` installeren

   De `log-analytics-solution` opdracht is een experimentele extensie van de Basis-Azure CLI. Meer informatie over extensieverwijzingen in [Extensie gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   De volgende waarschuwing wordt verwacht.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Een oplossing installeren met de Azure CLI

Wanneer u een oplossing installeert, moet u een [Log Analytics-werkruimte](../logs/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld.  Met de Azure CLI beheert u werkruimten met behulp van de [opdrachten az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace) reference.  Volg het proces dat wordt beschreven in [Log Analytics-werkruimte en Automation-account](#log-analytics-workspace-and-automation-account) om een werkruimte en account te koppelen.

Gebruik de [oplossing az monitor log-analytics create om](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) een bewakingsoplossing te installeren.  Parameters tussen vierkante haken zijn optioneel.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Hier ziet u een codevoorbeeld voor het maken van een log-analytics-oplossing voor het planproduct van OMSGallery/Containers.

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

   U moet een [Azure PowerShell](/powershell/azure/install-az-ps) installeren voordat u Azure PowerShell opdrachten kunt uitvoeren. Als u dat liever wilt, kunt u ook Azure Cloud Shell om de stappen in dit artikel uit te voeren. Azure Cloud Shell is een interactieve shell-omgeving die u via uw browser gebruikt. Begin Cloud Shell met behulp van een van deze methoden:

   - Open Cloud Shell door naar te gaan [https://shell.azure.com](https://shell.azure.com)

   - Selecteer de **Cloud Shell** in de menubalk in de rechterbovenhoek van de [Azure Portal](https://portal.azure.com)

   > [!IMPORTANT]
   > Hoewel de **PowerShell-module Az.MonitoringSolutions** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet . Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Meld u aan.

   Als u een lokale installatie van PowerShell gebruikt, meld u zich dan aan met de cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Volg de stappen die worden weergegeven in PowerShell om het verificatieproces te voltooien.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Een oplossing installeren met Azure PowerShell

Wanneer u een oplossing installeert, moet u een [Log Analytics-werkruimte](../logs/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld. Met Azure PowerShell beheert u werkruimten met behulp van de cmdlets in de PowerShell-module [Az.MonitoringSolutions.](/powershell/module/az.monitoringsolutions) Volg het proces dat wordt beschreven in [Log Analytics-werkruimte en Automation-account](#log-analytics-workspace-and-automation-account) om een werkruimte en account te koppelen.

Gebruik de cmdlet [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) om een bewakingsoplossing te installeren. Parameters tussen vierkante haken zijn optioneel.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

In het volgende voorbeeld wordt een oplossing voor logboekanalyse voor de log analytics-werkruimte gemaakt.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-werkruimte en Automation-account

Voor alle bewakingsoplossingen is een [Log Analytics-werkruimte vereist](../logs/manage-access.md) om gegevens op te slaan die door de oplossing zijn verzameld en om de zoekopdrachten en weergaven in logboeken te hosten. Voor sommige oplossingen is ook een [Automation-account](../../automation/automation-security-overview.md) vereist voor runbooks en gerelateerde resources. De werkruimte en het account moeten voldoen aan de volgende vereisten.

* Elke installatie van een oplossing kan slechts één Log Analytics-werkruimte en één Automation-account gebruiken. U kunt de oplossing afzonderlijk installeren in meerdere werkruimten.
* Als voor een oplossing een Automation-account is vereist, moeten de Log Analytics-werkruimte en het Automation-account aan elkaar zijn gekoppeld. Een Log Analytics-werkruimte kan slechts aan één Automation-account worden gekoppeld en een Automation-account kan slechts aan één Log Analytics-werkruimte worden gekoppeld.

Wanneer u een oplossing installeert via Azure Marketplace, wordt u gevraagd om een werkruimte en Een Automation-account. De koppeling tussen de twee wordt gemaakt als deze nog niet zijn gekoppeld.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Controleer de koppeling tussen een Log Analytics-werkruimte en een Automation-account

U kunt de koppeling tussen een Log Analytics-werkruimte en een Automation-account controleren met behulp van de volgende procedure.

1. Selecteer het Automation-account in het Azure Portal.
1. Schuif naar de **sectie Gerelateerde resources** van het menu en selecteer **Gekoppelde werkruimte.**
1. Als de **werkruimte** is gekoppeld aan een Automation-account, wordt op deze pagina de werkruimte vermeld waar deze aan is gekoppeld. Als u de naam van de vermelde werkruimte selecteert, wordt u omgeleid naar de overzichtspagina voor die werkruimte.

## <a name="remove-a-monitoring-solution"></a>Een bewakingsoplossing verwijderen

### <a name="portal"></a>[Portal](#tab/portal)

Als u een geïnstalleerde oplossing wilt verwijderen via de portal, zoekt u deze in de [lijst met geïnstalleerde oplossingen](#list-installed-monitoring-solutions). Klik op de naam van de oplossing om de overzichtspagina te openen en klik vervolgens op **Verwijderen.**

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een geïnstalleerde oplossing wilt verwijderen met behulp van de Azure CLI, gebruikt [u de opdracht az monitor log-analytics solution delete.](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete)

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Als u een geïnstalleerde oplossing wilt verwijderen Azure PowerShell, gebruikt u de cmdlet [Remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Volgende stappen

* Haal een [lijst met bewakingsoplossingen op van Microsoft](../monitor-reference.md).
* Meer informatie over het maken [van query's voor](../logs/log-query-overview.md) het analyseren van gegevens die zijn verzameld door bewakingsoplossingen.
* Zie alle [Azure CLI-opdrachten voor Azure Monitor.](/cli/azure/azure-cli-reference-for-monitor)