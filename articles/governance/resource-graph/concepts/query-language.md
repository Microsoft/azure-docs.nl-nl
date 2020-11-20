---
title: Inzicht krijgen in de querytaal
description: Hierin worden resource grafiek tabellen en de beschik bare Kusto-gegevens typen,-Opera tors en-functies die bruikbaar zijn met Azure resource Graph beschreven.
ms.date: 11/18/2020
ms.topic: conceptual
ms.openlocfilehash: 34aaaa60ed9d757cc1a63ffaebb2225900cff61f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966680"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informatie over de query taal van Azure resource Graph

De query taal voor de Azure-resource grafiek ondersteunt een aantal opera tors en functies. Elk werk en werkt op basis van [Kusto query language (KQL)](/azure/kusto/query/index). Als u meer wilt weten over de query taal die wordt gebruikt door resource grafiek, begint u met de [zelf studie voor KQL](/azure/kusto/query/tutorial).

In dit artikel worden de taal onderdelen beschreven die worden ondersteund door resource grafiek:

- [Resource grafiek tabellen](#resource-graph-tables)
- [Aangepaste taal elementen van resource grafiek](#resource-graph-custom-language-elements)
- [Ondersteunde KQL-taal elementen](#supported-kql-language-elements)
- [Bereik van de query](#query-scope)
- [Escape tekens](#escape-characters)

## <a name="resource-graph-tables"></a>Resource grafiek tabellen

Resource grafiek biedt verschillende tabellen voor de gegevens die worden opgeslagen over Azure Resource Manager resource typen en hun eigenschappen. Sommige tabellen kunnen met of- `join` `union` Opera tors worden gebruikt voor het ophalen van eigenschappen van gerelateerde resource typen. Hier volgt de lijst met tabellen die beschikbaar zijn in resource grafiek:

|Resource grafiek tabel |Kan `join` ? |Beschrijving |
|---|---|
|Resources |Ja |De standaard tabel als niets is gedefinieerd in de query. De resource typen en eigenschappen van Resource Manager zijn hier beschikbaar. |
|ResourceContainers |Ja |Bevat een abonnement (in Preview-- `Microsoft.Resources/subscriptions` ) en resource groep ( `Microsoft.Resources/subscriptions/resourcegroups` )-resource typen en-gegevens. |
|AdvisorResources |Nee |Bevat resources met _betrekking_ tot `Microsoft.Advisor` . |
|AlertsManagementResources |Nee |Bevat resources met _betrekking_ tot `Microsoft.AlertsManagement` . |
|GuestConfigurationResources |Nee |Bevat resources met _betrekking_ tot `Microsoft.GuestConfiguration` . |
|MaintenanceResources |Nee |Bevat resources met _betrekking_ tot `Microsoft.Maintenance` . |
|PolicyResources |Nee |Bevat resources met _betrekking_ tot `Microsoft.PolicyInsights` . (**Preview-versie**)|
|SecurityResources |Nee |Bevat resources met _betrekking_ tot `Microsoft.Security` . |
|ServiceHealthResources |Nee |Bevat resources met _betrekking_ tot `Microsoft.ResourceHealth` . |

Zie [verwijzing: ondersteunde tabellen en resource typen](../reference/supported-tables-resources.md)voor een volledige lijst met resource typen.

> [!NOTE]
> _Resources_ is de standaard tabel. Tijdens het uitvoeren van een query op de tabel _resources_ is het niet nodig om de tabel naam op te geven, tenzij `join` of wordt `union` gebruikt. De aanbevolen procedure is echter om altijd de eerste tabel in de query op te halen.

Gebruik resource Graph Explorer in de portal om te ontdekken welke resource typen beschikbaar zijn in elke tabel. Als alternatief kunt u een query gebruiken `<tableName> | distinct type` om een lijst met resource typen op te halen. de gegeven resource grafiek tabel ondersteunt in uw omgeving.

De volgende query bevat een eenvoudige `join` . In het query resultaat worden de kolommen samengebracht en dubbele kolom namen uit de gekoppelde tabel, _ResourceContainers_ in dit voor beeld, worden toegevoegd aan **1**. Als _ResourceContainers_ -tabel heeft typen voor beide abonnementen en resource groepen, kan een van beide typen worden gebruikt om lid te worden van de tabel Resource van _resources_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Met de volgende query wordt een complexere gebruik van weer gegeven `join` . Met de query wordt de gekoppelde tabel beperkt tot abonnementsresources, en met `project` wordt alleen het oorspronkelijke veld _subscriptionId_ opgenomen en het veld _name_ met de naam gewijzigd in _SubName_. De naam van het veld wordt voor komen dat `join` het wordt toegevoegd als _NAME1_ omdat het veld al in _resources_ bestaat. De oorspronkelijke tabel wordt gefilterd met `where` en het volgende `project` bevat kolommen uit beide tabellen. Het query resultaat is een enkele sleutel kluis met het type, de naam van de sleutel kluis en de naam van het abonnement dat in wordt weer gegeven.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Als de `join` resultaten worden beperkt met `project` , moet de eigenschap die wordt gebruikt `join` om de twee tabellen te koppelen, _subscriptionId_ in het bovenstaande voor beeld zijn opgenomen in `project` .

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Uitgebreide eigenschappen (preview-versie)

Als _Preview_ -functie bevatten sommige resource typen in resource Graph aanvullende eigenschappen die betrekking hebben op het type dat kan worden opgevraagd naast de eigenschappen van Azure Resource Manager. Deze reeks waarden, ook wel _uitgebreide eigenschappen_ genoemd, bestaat voor een ondersteund resource type in `properties.extended` . Gebruik de volgende query om te zien welke resource typen _uitgebreide eigenschappen_ hebben:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Voor beeld: het aantal virtuele machines ophalen op `instanceView.powerState.code` :

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Aangepaste taal elementen van resource grafiek

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Gedeelde query syntaxis (preview-versie)

Een preview-functie is dat een [gedeelde query](../tutorials/create-share-query.md) rechtstreeks kan worden geopend in een resource Graph-query. Dit scenario maakt het mogelijk om standaard query's te maken als gedeelde query's en deze opnieuw te gebruiken. Gebruik de syntaxis om een gedeelde query binnen een resource Graph-query aan te roepen `{{shared-query-uri}}` . De URI van de gedeelde query is de _resource-id_ van de gedeelde query op de **instellingen** pagina voor die query. In dit voor beeld is onze gedeelde query-URI `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` .
Deze URI verwijst naar het abonnement, de resource groep en de volledige naam van de gedeelde query waarnaar in een andere query moet worden verwezen. Deze query is hetzelfde als het bestand dat u hebt gemaakt in [zelf studie: een query maken en delen](../tutorials/create-share-query.md).

> [!NOTE]
> U kunt geen query opslaan die verwijst naar een gedeelde query als een gedeelde query.

Voor beeld 1: alleen de gedeelde query gebruiken

De resultaten van deze resource grafiek query zijn hetzelfde als de query die in de gedeelde query is opgeslagen.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Voor beeld 2: de gedeelde query opnemen als onderdeel van een grotere query

Deze query gebruikt eerst de gedeelde query en gebruikt deze `limit` om de resultaten verder te beperken.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Ondersteunde KQL-taal elementen

Resource grafiek ondersteunt een subset van KQL- [gegevens typen](/azure/kusto/query/scalar-data-types/), [scalaire functies](/azure/kusto/query/scalarfunctions), [scalaire Opera tors](/azure/kusto/query/binoperators)en [aggregatie functies](/azure/kusto/query/any-aggfunction). Specifieke [tabellaire Opera tors](/azure/kusto/query/queries) worden ondersteund door resource grafiek, waarvan sommige verschillende gedragingen hebben.

### <a name="supported-tabulartop-level-operators"></a>Ondersteunde Opera tors voor tabellaire/hoogste niveau

Hier volgt een lijst met KQL-Opera tors die worden ondersteund door resource grafiek met specifieke voor beelden:

|KQL |Voorbeeld query resource grafiek |Opmerkingen |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Sleutel kluizen tellen](../samples/starter.md#count-keyvaults) | |
|[onderscheiden](/azure/kusto/query/distinctoperator) |[Afzonderlijke waarden voor een specifieke alias tonen](../samples/starter.md#distinct-alias-values) | |
|[uitbreidbaar](/azure/kusto/query/extendoperator) |[Virtuele machines tellen op type besturingssysteem](../samples/starter.md#count-os) | |
|[Jointypen](/azure/kusto/query/joinoperator) |[Sleutel kluis met de naam van het abonnement](../samples/advanced.md#join) |Ondersteunde jointypen: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). De limiet van 3 `join` in één query. Aangepaste deelname strategieën, zoals broadcast toevoegen, zijn niet toegestaan. `join`Zie [resource Graph Tables](#resource-graph-tables)(Engelstalig) voor welke tabellen kunnen worden gebruikt. |
|[ondergrens](/azure/kusto/query/limitoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van `take` . Werkt niet met [overs Laan](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Verouderde operator `mv-expand` . gebruik in plaats daarvan. _RowLimit_ maximum van 400. De standaard waarde is 128. |
|[MV-uitvouwen](/azure/kusto/query/mvexpandoperator) |[Een lijst met Cosmos DB met specifieke schrijflocaties weergeven](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ maximum van 400. De standaard waarde is 128. |
|[order](/azure/kusto/query/orderoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van `sort` |
|[project](/azure/kusto/query/projectoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) | |
|[project-weg](/azure/kusto/query/projectawayoperator) |[Kolommen verwijderen uit resultaten](../samples/advanced.md#remove-column) | |
|[acties](/azure/kusto/query/sortoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van `order` |
|[samenvatten](/azure/kusto/query/summarizeoperator) |[Azure-resources tellen](../samples/starter.md#count-resources) |Alleen de eerste vereenvoudigde pagina |
|[take](/azure/kusto/query/takeoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van `limit` . Werkt niet met [overs Laan](./work-with-data.md#skipping-records). |
|[Boven](/azure/kusto/query/topoperator) |[De eerste vijf virtuele machines weergeven op naam en met hun type besturingssysteem](../samples/starter.md#show-sorted) | |
|[Réunion](/azure/kusto/query/unionoperator) |[Resultaten van twee query's combineren tot één resultaat](../samples/advanced.md#unionresults) |Eén tabel _toegestaan:_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _ColumnName_ \] _tabel kolom naam_. Maxi maal drie `union` zijden in één query. Het is niet toegestaan om de tabel met fuzzy op te lossen `union` . Kan worden gebruikt binnen één tabel of tussen de tabellen _resources_ en _ResourceContainers_ . |
|[positie](/azure/kusto/query/whereoperator) |[Resources weergeven die opslag bevatten](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>Querybereik

Het bereik van de abonnementen waaruit resources worden geretourneerd door een query is afhankelijk van de methode voor toegang tot de resource grafiek. Azure CLI en Azure PowerShell vullen de lijst met abonnementen die in de aanvraag moeten worden meegenomen op basis van de context van de geautoriseerde gebruiker. De lijst met abonnementen kan hand matig worden gedefinieerd voor elk met de **abonnementen** en **abonnements** parameters.
In REST API en alle andere Sdk's moet de lijst met abonnementen waaruit resources moeten worden opgenomen, expliciet worden gedefinieerd als onderdeel van de aanvraag.

Als **Preview** voegt rest API versie `2020-04-01-preview` een eigenschap toe om de query aan een [beheer groep](../../management-groups/overview.md)toe te voegen. Met deze preview-API wordt de abonnements eigenschap ook optioneel gemaakt. Als er geen beheer groep of abonnements lijst is gedefinieerd, is het query bereik alle resources, waaronder [Azure Lighthouse](../../../lighthouse/concepts/azure-delegated-resource-management.md) gedelegeerde resources, waartoe de geverifieerde gebruiker toegang heeft. De nieuwe `managementGroupId` eigenschap neemt de beheer groep-ID in, die verschilt van de naam van de beheer groep. Wanneer `managementGroupId` is opgegeven, worden resources van de eerste 5000-abonnementen in of onder de opgegeven beheer groep-hiërarchie opgenomen. `managementGroupId` kan niet worden gebruikt op hetzelfde moment als `subscriptions` .

Voor beeld: een query uitvoeren op alle resources binnen de hiërarchie van de beheer groep met de naam ' My-beheer groep ' met ID ' myMG '.

- REST API-URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Escape tekens

Sommige eigenschapnamen van eigenschappen, zoals die `.` van een or `$` , moeten in de query worden verpakt of worden geescaped of de naam van de eigenschap wordt onjuist geïnterpreteerd en levert niet de verwachte resultaten op.

- `.` -Laat de naam van de eigenschap als volgt teruglopen: `['propertyname.withaperiod']`
  
  Voorbeeld query waarmee de eigenschap _odata. type_ wordt geterugloopd:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Escape het teken in de naam van de eigenschap. Welk escape teken wordt gebruikt, is afhankelijk van de resource grafiek van de shell wordt uitgevoerd vanaf.

  - **bash** - `\`

    Voorbeeld query waarmee het eigenschaps _\$ type_ wordt verescapet in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** : laat het teken onescape `$` .

  - **Zo** - ``` ` ```

    Voorbeeld query waarmee het eigenschaps _\$ type_ in Power shell wordt geescapet:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie [Geavanceerde query's](../samples/advanced.md) voor geavanceerde gebruikswijzen.
- Lees meer over het [verkennen van resources](explore-resources.md).
