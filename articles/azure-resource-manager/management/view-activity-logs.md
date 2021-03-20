---
title: Activiteiten logboeken van Azure weer geven voor het bewaken van resources
description: Gebruik de activiteiten Logboeken om de gebruikers acties en-fouten te controleren. Toont Azure Portal Power shell, Azure CLI en REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 14015e9b2792515e6818af551b8bd9f54c686bee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91371589"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Activiteiten logboeken weer geven om acties op resources te controleren

Met activiteitenlogboeken kunt u het volgende bepalen:

* welke bewerkingen zijn uitgevoerd op de resources in uw abonnement
* wie de bewerking heeft gestart
* Wanneer de bewerking is uitgevoerd
* de status van de bewerking
* de waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van de bewerking

Het activiteitenlogboek bevat alle schrijfbewerkingen (PUT, POST, DELETE) voor uw resources. Het bevat geen leesbewerkingen (GET). Voor een lijst met resource acties raadpleegt u bewerkingen van de [Azure-resource provider](../../role-based-access-control/resource-provider-operations.md). U kunt de activiteitenlogboeken gebruiken om fouten te vinden bij foutoplossing of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Activiteitenlogboek worden gedurende negentig dagen bewaard. U kunt een query uitvoeren voor een willekeurig datumbereik, zolang de begindatum niet meer dan negentig dagen in het verleden ligt.

U kunt informatie ophalen uit de activiteiten logboeken via de portal, Power shell, de Azure CLI, inzichten REST API of de [.net-bibliotheek van Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

Als u de activiteiten logboeken wilt weer geven via de portal, volgt u deze stappen:

1. Selecteer in het menu Azure Portal de optie **monitor** of zoek en selecteer op een wille keurige pagina de optie **monitor** .

    ![Monitor selecteren](./media/view-activity-logs/select-monitor-from-menu.png)

1. Selecteer **activiteiten logboek**.

    ![Activiteiten logboek selecteren](./media/view-activity-logs/select-activity-log.png)

1. U ziet een samen vatting van recente bewerkingen. Er wordt een standaardset filters toegepast op de bewerkingen. U ziet dat de informatie in de samen vatting bevat die de actie heeft gestart en wanneer deze is opgetreden.

    ![Samen vatting van recente bewerkingen weer geven](./media/view-activity-logs/audit-summary.png)

1. Als u snel een vooraf gedefinieerde set filters wilt uitvoeren, selecteert u **snelle inzichten**.

    ![Snelle inzichten selecteren](./media/view-activity-logs/select-quick-insights.png)

1. Selecteer een van de opties. Selecteer bijvoorbeeld **mislukte implementaties** om fouten van implementaties te bekijken.

    ![Selecteer mislukte implementaties](./media/view-activity-logs/select-failed-deployments.png)

1. U ziet dat de filters zijn gewijzigd om te focussen op implementatie fouten in de afgelopen 24 uur. Alleen bewerkingen die overeenkomen met de filters worden weer gegeven.

    ![Weergavefilters](./media/view-activity-logs/view-filters.png)

1. Wijzig de filters of pas nieuwe toe om te focussen op specifieke bewerkingen. De volgende afbeelding toont bijvoorbeeld een nieuwe waarde voor de **tijds duur** en het **resource type** is ingesteld op opslag accounts.

    ![Filter opties instellen](./media/view-activity-logs/set-filter.png)

1. Als u de query later opnieuw moet uitvoeren, selecteert u **huidige filters vastmaken**.

    ![Filter vastmaken](./media/view-activity-logs/pin-filters.png)

1. Geef het filter een naam.

    ![Naam filters](./media/view-activity-logs/name-filters.png)

1. Het filter is beschikbaar in het dash board. Selecteer **Dashboard** in het menu van Azure Portal.

    ![Filter weer geven op het dash board](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Vanuit de portal kunt u wijzigingen in een resource bekijken. Ga terug naar de standaard weergave in monitor en selecteer een bewerking die betrokken is bij het wijzigen van een resource.

    ![Bewerking selecteren](./media/view-activity-logs/select-operation.png)

1. Selecteer **wijzigings geschiedenis (preview)** en kies een van de beschik bare bewerkingen.

    ![Wijzigings overzicht selecteren](./media/view-activity-logs/select-change-history.png)

1. De wijzigingen in de resource worden weer gegeven.

    ![Wijzigingen weer geven](./media/view-activity-logs/show-changes.png)

Zie [resource wijzigingen ophalen](../../governance/resource-graph/how-to/get-resource-changes.md)voor meer informatie over wijzigings geschiedenis.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u logboek vermeldingen wilt ophalen, voert u de opdracht **Get-AzLog** uit. U geeft aanvullende para meters voor het filteren van de lijst met vermeldingen. Als u geen begin-en eind tijd opgeeft, worden de gegevens van de afgelopen zeven dagen geretourneerd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

In het volgende voor beeld ziet u hoe u het activiteiten logboek gebruikt voor onderzoek bewerkingen die zijn uitgevoerd tijdens een opgegeven periode. De begin-en eind datum worden opgegeven in een datum notatie.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

U kunt ook datum functies gebruiken om het datum bereik op te geven, zoals de laatste 14 dagen.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

U kunt de acties opzoeken die door een bepaalde gebruiker worden uitgevoerd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

U kunt filteren op mislukte bewerkingen.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

U kunt zich richten op één fout door te kijken naar het status bericht voor die vermelding.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

U kunt specifieke waarden selecteren om de geretourneerde gegevens te beperken.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Afhankelijk van de start tijd die u opgeeft, kunnen de vorige opdrachten een lange lijst met bewerkingen voor de resource groep retour neren. U kunt de resultaten voor wat u zoekt filteren door zoek criteria op te geven. U kunt bijvoorbeeld filteren op het type bewerking.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

U kunt resource grafiek gebruiken om de wijzigings geschiedenis voor een resource weer te geven. Zie [resource wijzigingen ophalen](../../governance/resource-graph/how-to/get-resource-changes.md)voor meer informatie.

## <a name="azure-cli"></a>Azure CLI

Als u logboek vermeldingen wilt ophalen, voert u de opdracht [AZ monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) uit met een offset om de tijds Panne aan te geven.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

In het volgende voor beeld ziet u hoe u het activiteiten logboek gebruikt voor onderzoek bewerkingen die zijn uitgevoerd tijdens een opgegeven periode. De begin-en eind datum worden opgegeven in een datum notatie.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

U kunt de acties zoeken die door een bepaalde gebruiker worden uitgevoerd, zelfs voor een resource groep die niet meer bestaat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

U kunt filteren op mislukte bewerkingen.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

U kunt zich richten op één fout door te kijken naar het status bericht voor die vermelding.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

U kunt specifieke waarden selecteren om de geretourneerde gegevens te beperken.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Afhankelijk van de start tijd die u opgeeft, kunnen de vorige opdrachten een lange lijst met bewerkingen voor de resource groep retour neren. U kunt de resultaten voor wat u zoekt filteren door zoek criteria op te geven. U kunt bijvoorbeeld filteren op het type bewerking.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

U kunt resource grafiek gebruiken om de wijzigings geschiedenis voor een resource weer te geven. Zie [resource wijzigingen ophalen](../../governance/resource-graph/how-to/get-resource-changes.md)voor meer informatie.

## <a name="rest-api"></a>REST-API

De REST-bewerkingen voor het werken met het activiteitenlogboek maken deel uit van de [Insights-REST API](/rest/api/monitor/). Als u gebeurtenissen van activiteitenlogboeken wilt ophalen, raadpleegt u [de beheergebeurtenissen in een abonnement weergeven](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Volgende stappen

* Azure-activiteiten logboeken kunnen worden gebruikt met Power BI om meer inzicht te krijgen in de acties in uw abonnement. Zie [Azure-activiteiten logboeken weer geven en analyseren in Power bi en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Zie voor meer informatie over het instellen van beveiligings beleid [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Zie [toepassings wijzigingen in azure monitor gebruiken](../../azure-monitor/app/change-analysis.md)voor meer informatie over de wijzigingen in uw toepassingen vanuit de laag van de infra structuur voor de implementatie van toepassingen.
* Zie [implementatie bewerkingen weer geven](../templates/deployment-history.md)voor meer informatie over de opdrachten voor het weer geven van implementatie bewerkingen.
* Zie [resources vergren delen met Azure Resource Manager](lock-resources.md)voor meer informatie over het voor komen van het verwijderen van een resource voor alle gebruikers.
* Zie [Azure resource provider-bewerkingen](../../role-based-access-control/resource-provider-operations.md) voor een overzicht van de beschik bare bewerkingen voor elke Microsoft Azure Resource Manager-provider.
