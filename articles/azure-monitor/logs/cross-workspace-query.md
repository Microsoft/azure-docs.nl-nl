---
title: Query's uitvoeren op resources met Azure Monitor | Microsoft Docs
description: In dit artikel wordt beschreven hoe u query's kunt uitvoeren op resources uit meerdere werkruimten en app App Insights in uw abonnement.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/11/2021
ms.openlocfilehash: 19cc85751fc5e4a165b646ac89d9d6b6e90c4408
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379550"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Logboekquery's uitvoeren in Azure Monitor werkruimten en apps

Azure Monitor logboeken ondersteunen query's in meerdere Log Analytics-werkruimten en Application Insights-app in dezelfde resourcegroep, een andere resourcegroep of een ander abonnement. Dit biedt u een systeembreed overzicht van uw gegevens.

Er zijn twee methoden om query's uit te voeren op gegevens die zijn opgeslagen in meerdere werkruimten en apps:
1. Expliciet door de details van de werkruimte en app op te geven. Deze techniek wordt beschreven in dit artikel.
2. Impliciet met behulp [van resourcecontextquery's.](./design-logs-deployment.md#access-mode) Wanneer u een query uitvoert in de context van een specifieke resource, resourcegroep of abonnement, worden de relevante gegevens opgehaald uit alle werkruimten die gegevens voor deze resources bevatten. Application Insights gegevens die zijn opgeslagen in apps, worden niet opgehaald.

> [!IMPORTANT]
> Als u een op werkruimte gebaseerde Application Insights wordt de resource-telemetrie opgeslagen in een Log [Analytics-werkruimte](../app/create-workspace-resource.md) met alle andere logboekgegevens. Gebruik de expressie workspace() om een query te schrijven die een toepassing in meerdere werkruimten bevat. Voor meerdere toepassingen in dezelfde werkruimte hebt u geen query voor meerdere werkruimten nodig.


## <a name="cross-resource-query-limits"></a>Querylimieten voor andere resources 

* Het aantal Application Insights resources en Log Analytics-werkruimten dat u in één query kunt opnemen, is beperkt tot 100.
* Query's voor meerdere resources worden niet ondersteund in View Designer. U kunt een query maken in Log Analytics en deze vastmaken aan het Azure-dashboard om een [logboekquery te](../visualize/tutorial-logs-dashboards.md) visualiseren of op te nemen in [Workbooks.](../visualize/workbooks-overview.md)
* Query's tussen resources in logboekwaarschuwingen worden alleen ondersteund in de huidige [geplandeQueryRules-API.](/rest/api/monitor/scheduledqueryrules) Als u de verouderde Api voor Log Analytics-waarschuwingen gebruikt, moet u overschakelen [naar de huidige API](../alerts/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Query's uitvoeren in Log Analytics-werkruimten en Application Insights
Als u wilt verwijzen naar [](../logs/workspace-expression.md) een andere werkruimte in uw query, gebruikt u de werkruimte-id en gebruikt u voor een app Application Insights [*app-id.*](./app-expression.md)  

### <a name="identifying-workspace-resources"></a>Werkruimte-resources identificeren
In de volgende voorbeelden ziet u query's in Log Analytics-werkruimten om samengevatte tellingen van logboeken uit de tabel Update te retourneren in een werkruimte met de naam *contosoretail-it.* 

Het identificeren van een werkruimte kan op een van de volgende manieren worden bereikt:

* Resourcenaam: is een voor mensen leesbare naam van de werkruimte, ook wel de *onderdeelnaam genoemd.* 

    >[!IMPORTANT]
    >Omdat namen van apps en werkruimten niet uniek zijn, kan deze id ambigu zijn. Het wordt aanbevolen dat de verwijzing wordt uitgevoerd op Gekwalificeerde naam, Werkruimte-id of Azure-resource-id.

    `workspace("contosoretail-it").Update | count`

* Gekwalificeerde naam: is de volledige naam van de werkruimte, bestaande uit de abonnementsnaam, resourcegroep en onderdeelnaam in deze indeling: *subscriptionName/resourceGroup/componentName.* 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Omdat de namen van Azure-abonnementen niet uniek zijn, kan deze id ambigu zijn.

* Werkruimte-id: een werkruimte-id is de unieke, onveranderbare id die wordt toegewezen aan elke werkruimte die wordt weergegeven als een GUID (Globally Unique Identifier).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure-resource-id: de door Azure gedefinieerde unieke identiteit van de werkruimte. U gebruikt de resource-id wanneer de resourcenaam ambigu is.  Voor werkruimten is de indeling: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Bijvoorbeeld:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Een toepassing identificeren
In de volgende voorbeelden wordt een samengevat aantal aanvragen voor een app met de naam *fabrikamapp* in Application Insights. 

Het identificeren van een toepassing in Application Insights kan worden bereikt met de *app(Identifier)-expressie.*  Het *argument Id* geeft de app aan met behulp van een van de volgende opties:

* Resourcenaam: is een voor mensen leesbare naam van de app, ook wel de *onderdeelnaam genoemd.*  

    `app("fabrikamapp")`

    >[!NOTE]
    >Bij het identificeren van een toepassing op naam wordt uitgenomen dat alle toegankelijke abonnementen uniek zijn. Als u meerdere toepassingen met de opgegeven naam hebt, mislukt de query vanwege de dubbelzinnigheid. In dit geval moet u een van de andere id's gebruiken.

* Gekwalificeerde naam: is de volledige naam van de app, die bestaat uit de naam van het abonnement, de resourcegroep en de onderdeelnaam in deze indeling: *subscriptionName/resourceGroup/componentName.* 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Omdat namen van Azure-abonnementen niet uniek zijn, kan deze id ambigu zijn. 
    >

* Id: de app-GUID van de toepassing.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure-resource-id: de door Azure gedefinieerde unieke identiteit van de app. U gebruikt de resource-id wanneer de resourcenaam ambigu is. De indeling is: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName.*  

    Bijvoorbeeld:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Een query uitvoeren voor meerdere resources
U kunt query's uitvoeren op meerdere resources van elk van uw resource-exemplaren. Dit kunnen werkruimten en apps zijn gecombineerd.
    
Voorbeeld voor query's in twee werkruimten:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Query's op meerdere resources gebruiken voor meerdere resources
Wanneer u query's op meerdere resources gebruikt om gegevens uit meerdere Log Analytics-werkruimten en Application Insights te correleren, kan de query complex en moeilijk te onderhouden zijn. U moet gebruikmaken [van functies in Azure Monitor logboekquery's](./functions.md) om de querylogica te scheiden van het bereik van de querybronnen, waardoor de querystructuur wordt vereenvoudigd. In het volgende voorbeeld ziet u hoe u meerdere Application Insights kunt bewaken en het aantal mislukte aanvragen kunt visualiseren op toepassingsnaam. 

Maak een query zoals hieronder die verwijst naar het bereik van Application Insights resources. Met `withsource= SourceApp` de opdracht wordt een kolom toegevoegd die de toepassingsnaam aanwijzen die het logboek heeft verzonden. [Sla de query op als functie met](./functions.md#create-a-function) de _aliastoepassingenScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



U kunt deze [functie nu gebruiken](./functions.md#use-a-function) in een query voor resourceoverschrijdende resources, zoals hieronder wordt bevraagd. De _functiealiastoepassingenScoping_ retourneert de union van de aanvraagtabel van alle gedefinieerde toepassingen. De query filtert vervolgens op mislukte aanvragen en visualiseert de trends per toepassing. De _parse-operator_ is in dit voorbeeld optioneel. De toepassingsnaam wordt geëxtraheert uit _de eigenschap SourceApp._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Deze methode kan niet worden gebruikt met logboekwaarschuwingen omdat de toegangsvalidatie van de waarschuwingsregelbronnen, inclusief werkruimten en toepassingen, wordt uitgevoerd tijdens het maken van waarschuwingen. Het toevoegen van nieuwe resources aan de functie nadat de waarschuwing is gemaakt, wordt niet ondersteund. Als u de functie liever gebruikt voor het bereik van resources in logboekwaarschuwingen, moet u de waarschuwingsregel bewerken in de portal of met een Resource Manager-sjabloon om de resources binnen het bereik bij te werken. U kunt ook de lijst met resources opnemen in de logboekwaarschuwingsquery.


![Tijdsdiagram](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Volgende stappen

- Bekijk [Logboekgegevens analyseren in Azure Monitor](./log-query-overview.md) voor een overzicht van logboekquery's en hoe Azure Monitor logboekgegevens zijn gestructureerd.