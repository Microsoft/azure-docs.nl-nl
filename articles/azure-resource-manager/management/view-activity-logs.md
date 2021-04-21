---
title: Azure-activiteitenlogboeken weergeven om resources te bewaken
description: Gebruik de activiteitenlogboeken om gebruikersacties en -fouten te controleren. Toont Azure Portal PowerShell, Azure CLI en REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 7612146a0f9407663631f87c57f30ea4c590c7a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773923"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Activiteitenlogboeken weergeven voor het bewaken van acties op resources

Met activiteitenlogboeken kunt u het volgende bepalen:

* welke bewerkingen zijn uitgevoerd voor de resources in uw abonnement
* wie de bewerking heeft gestart
* wanneer de bewerking heeft plaatsgevonden
* de status van de bewerking
* de waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van de bewerking

Het activiteitenlogboek bevat alle schrijfbewerkingen (PUT, POST, DELETE) voor uw resources. Het bevat geen leesbewerkingen (GET). Zie Bewerkingen voor Azure-resourceproviders voor een [lijst met resourceacties.](../../role-based-access-control/resource-provider-operations.md) U kunt de activiteitenlogboeken gebruiken om fouten te vinden bij foutoplossing of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Activiteitenlogboek worden gedurende negentig dagen bewaard. U kunt een query uitvoeren voor een willekeurig datumbereik, zolang de begindatum niet meer dan negentig dagen in het verleden ligt.

U kunt informatie ophalen uit de activiteitenlogboeken via de portal, PowerShell, Azure CLI, Insights REST API of [Insights .NET Library.](https://www.nuget.org/packages/Microsoft.Azure.Insights/)

## <a name="azure-portal"></a>Azure Portal

Volg deze stappen om de activiteitenlogboeken via de portal weer te geven:

1. Selecteer in Azure Portal menu Monitor of zoek en selecteer **Monitor** op een pagina.

    ![Monitor selecteren](./media/view-activity-logs/select-monitor-from-menu.png)

1. Selecteer **Activiteitenlogboek.**

    ![Activiteitenlogboek selecteren](./media/view-activity-logs/select-activity-log.png)

1. U ziet een samenvatting van recente bewerkingen. Er wordt een standaardset filters toegepast op de bewerkingen. U ziet dat de informatie in de samenvatting bevat wie de actie heeft gestart en wanneer deze heeft plaatsgevonden.

    ![Overzicht van recente bewerkingen weergeven](./media/view-activity-logs/audit-summary.png)

1. Als u snel een vooraf gedefinieerde set filters wilt uitvoeren, selecteert **u Snelle inzichten**.

    ![Snelle inzichten selecteren](./media/view-activity-logs/select-quick-insights.png)

1. Selecteer een van de opties. Selecteer bijvoorbeeld Mislukte **implementaties om** fouten van implementaties weer te geven.

    ![Mislukte implementaties selecteren](./media/view-activity-logs/select-failed-deployments.png)

1. U ziet dat de filters zijn gewijzigd om te focussen op implementatiefouten in de afgelopen 24 uur. Alleen bewerkingen die overeenkomen met de filters worden weergegeven.

    ![Weergavefilters](./media/view-activity-logs/view-filters.png)

1. Als u zich wilt richten op specifieke bewerkingen, wijzigt u de filters of pas u nieuwe toe. In de volgende afbeelding ziet u bijvoorbeeld een nieuwe waarde voor **de** periode en **Resourcetype** is ingesteld op opslagaccounts.

    ![Filteropties instellen](./media/view-activity-logs/set-filter.png)

1. Als u de query later opnieuw moet uitvoeren, selecteert u **Huidige filters vastmaken.**

    ![Filters vastmaken](./media/view-activity-logs/pin-filters.png)

1. Geef het filter een naam.

    ![Naamfilters](./media/view-activity-logs/name-filters.png)

1. Het filter is beschikbaar in het dashboard. Selecteer **Dashboard** in het menu van Azure Portal.

    ![Filter op dashboard tonen](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Vanuit de portal kunt u wijzigingen in een resource weergeven. Terug de standaardweergave in Monitor en selecteer een bewerking voor het wijzigen van een resource.

    ![Bewerking selecteren](./media/view-activity-logs/select-operation.png)

1. Selecteer **Wijzigingsgeschiedenis (preview)** en kies een van de beschikbare bewerkingen.

    ![Wijzigingsgeschiedenis selecteren](./media/view-activity-logs/select-change-history.png)

1. De wijzigingen in de resource worden weergegeven.

    ![Wijzigingen tonen](./media/view-activity-logs/show-changes.png)

Zie Resourcewijzigingen op halen voor meer informatie [over de wijzigingsgeschiedenis.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voer de opdracht **Get-AzLog** uit om logboekgegevens op te halen. U geeft aanvullende parameters op om de lijst met vermeldingen te filteren. Als u geen begin- en eindtijd opgeeft, worden de vermeldingen voor de afgelopen zeven dagen geretourneerd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

In het volgende voorbeeld ziet u hoe u het activiteitenlogboek gebruikt om bewerkingen te onderzoeken die gedurende een opgegeven periode zijn uitgevoerd. De begin- en einddatums worden opgegeven in een datumnotatie.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

U kunt ook datumfuncties gebruiken om het datumbereik op te geven, zoals de afgelopen 14 dagen.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

U kunt de acties op zoeken die door een bepaalde gebruiker zijn ondernomen.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

U kunt filteren op mislukte bewerkingen.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

U kunt zich richten op één fout door het statusbericht voor die vermelding te bekijken.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

U kunt specifieke waarden selecteren om de gegevens te beperken die worden geretourneerd.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Afhankelijk van de begintijd die u opgeeft, kunnen de vorige opdrachten een lange lijst met bewerkingen voor de resourcegroep retourneren. U kunt de resultaten filteren op wat u zoekt door zoekcriteria op te geven. U kunt bijvoorbeeld filteren op het type bewerking.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

U kunt de Resource Graph om de wijzigingsgeschiedenis voor een resource te bekijken. Zie Resourcewijzigingen verkrijgen [voor meer informatie.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="azure-cli"></a>Azure CLI

Voer de opdracht az [monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) uit met een offset om de tijdspanne aan te geven om logboekgegevens op te halen.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

In het volgende voorbeeld ziet u hoe u het activiteitenlogboek gebruikt om bewerkingen te onderzoeken die gedurende een opgegeven periode zijn uitgevoerd. De begin- en einddatums worden opgegeven in een datumnotatie.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

U kunt de acties op zoeken die door een bepaalde gebruiker zijn ondernomen, zelfs voor een resourcegroep die niet meer bestaat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

U kunt filteren op mislukte bewerkingen.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

U kunt zich richten op één fout door het statusbericht voor die vermelding te bekijken.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

U kunt specifieke waarden selecteren om de gegevens te beperken die worden geretourneerd.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Afhankelijk van de begintijd die u opgeeft, kunnen de vorige opdrachten een lange lijst met bewerkingen voor de resourcegroep retourneren. U kunt de resultaten filteren op wat u zoekt door zoekcriteria op te geven. U kunt bijvoorbeeld filteren op het type bewerking.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

U kunt deze Resource Graph de wijzigingsgeschiedenis voor een resource te bekijken. Zie Resourcewijzigingen verkrijgen [voor meer informatie.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="rest-api"></a>REST-API

De REST-bewerkingen voor het werken met het activiteitenlogboek maken deel uit van de [Insights-REST API](/rest/api/monitor/). Als u gebeurtenissen van activiteitenlogboeken wilt ophalen, raadpleegt u [de beheergebeurtenissen in een abonnement weergeven](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Volgende stappen

* Azure-activiteitenlogboeken kunnen worden gebruikt Power BI meer inzicht te krijgen in de acties in uw abonnement. Zie [Azure-activiteitenlogboeken weergeven en analyseren in Power BI en meer.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)
* Zie Op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van beveiligingsbeleid.
* Zie Use wijzigingsanalyse voor toepassingen in Azure Monitor voor meer informatie over de wijzigingen [in uw toepassingen](../../azure-monitor/app/change-analysis.md)vanaf de infrastructuurlaag tot aan de implementatie van wijzigingsanalyse voor toepassingen toepassingen.
* Zie Implementatiebewerkingen weergeven voor meer informatie over de opdrachten voor het weergeven [van implementatiebewerkingen.](../templates/deployment-history.md)
* Zie Resources vergrendelen met Azure Resource Manager voor meer informatie over het voorkomen van verwijderingen van [een resource voor Azure Resource Manager.](lock-resources.md)
* Zie Bewerkingen van [Azure-resourceproviders](../../role-based-access-control/resource-provider-operations.md) voor een overzicht van Microsoft Azure Resource Manager bewerkingen die beschikbaar zijn voor elke provider
