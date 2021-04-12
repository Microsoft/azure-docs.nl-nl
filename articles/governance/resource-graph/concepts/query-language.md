---
title: Inzicht krijgen in de querytaal
description: Hierin worden resource grafiek tabellen en de beschik bare Kusto-gegevens typen,-Opera tors en-functies die bruikbaar zijn met Azure resource Graph beschreven.
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 5e600439d54a89dd9bd2510b2e47b71b60ee93a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557680"
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

|Resource grafiek tabel |Kunnen `join` andere tabellen? |Description |
|---|---|---|
|Resources |Yes |De standaard tabel als niets is gedefinieerd in de query. De resource typen en eigenschappen van Resource Manager zijn hier beschikbaar. |
|ResourceContainers |Yes |Bevat een abonnement (in Preview-- `Microsoft.Resources/subscriptions` ) en resource groep ( `Microsoft.Resources/subscriptions/resourcegroups` )-resource typen en-gegevens. |
|AdvisorResources |Ja (preview-versie) |Bevat resources met _betrekking_ tot `Microsoft.Advisor` . |
|AlertsManagementResources |Ja (preview-versie) |Bevat resources met _betrekking_ tot `Microsoft.AlertsManagement` . |
|ExtendedLocationResources |No |Bevat resources met _betrekking_ tot `Microsoft.ExtendedLocation` . |
|GuestConfigurationResources |No |Bevat resources met _betrekking_ tot `Microsoft.GuestConfiguration` . |
|KubernetesConfigurationResources |No |Bevat resources met _betrekking_ tot `Microsoft.KubernetesConfiguration` . |
|MaintenanceResources |Gedeeltelijk, alleen toevoegen _aan_ . (preview) |Bevat resources met _betrekking_ tot `Microsoft.Maintenance` . |
|PatchAssessmentResources|No |Bevat resources met _betrekking_ tot de evaluatie van de Azure virtual machines-patch. |
|PatchInstallationResources|No |Bevat informatie _over_ de installatie van de Azure virtual machines-patch. |
|PolicyResources |No |Bevat resources met _betrekking_ tot `Microsoft.PolicyInsights` . (**Preview-versie**)|
|RecoveryServicesResources |Gedeeltelijk, alleen toevoegen _aan_ . (preview) |Bevat resources met _betrekking_ tot `Microsoft.DataProtection` en `Microsoft.RecoveryServices` . |
|SecurityResources |Gedeeltelijk, alleen toevoegen _aan_ . (preview) |Bevat resources met _betrekking_ tot `Microsoft.Security` . |
|ServiceHealthResources |No |Bevat resources met _betrekking_ tot `Microsoft.ResourceHealth` . |
|WorkloadMonitorResources |No |Bevat resources met _betrekking_ tot `Microsoft.WorkloadMonitor` . |

Zie voor een volledige lijst, inclusief resource typen, [verwijzing: ondersteunde tabellen en resource typen](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resources_ is de standaard tabel. Tijdens het uitvoeren van een query op de tabel _resources_ is het niet nodig om de tabel naam op te geven, tenzij `join` of wordt `union` gebruikt. De aanbevolen procedure is echter om altijd de eerste tabel in de query op te halen.

Gebruik resource Graph Explorer in de portal om te ontdekken welke resource typen beschikbaar zijn in elke tabel. Als alternatief kunt u een query gebruiken `<tableName> | distinct type` om een lijst met resource typen op te halen. de gegeven resource grafiek tabel ondersteunt in uw omgeving.

De volgende query bevat een eenvoudige `join` . In het query resultaat worden de kolommen samengebracht en dubbele kolom namen uit de gekoppelde tabel, _ResourceContainers_ in dit voor beeld, worden toegevoegd aan **1**. Als _ResourceContainers_ -tabel heeft typen voor beide abonnementen en resource groepen, kan een van beide typen worden gebruikt om lid te worden van de tabel Resource van _resources_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Met de volgende query wordt een complexere gebruik van weer gegeven `join` . Eerst gebruikt de query `project` om de velden van _resources_ voor het resource type Azure Key Vault kluizen op te halen. De volgende stap wordt gebruikt `join` voor het samen voegen van de resultaten met _ResourceContainers_ waarbij het type een abonnement is _op_ een eigenschap die zowel in de eerste tabel `project` als in de gekoppelde tabel voor komt `project` . De naam van het veld wordt voor komen dat `join` het wordt toegevoegd als _NAME1_ omdat de eigenschap al is geprojecteerd vanuit _resources_. Het query resultaat is een enkele sleutel kluis die het type, de naam, de locatie en de resource groep van de sleutel kluis weergeeft, samen met de naam van het abonnement waarin deze zich bevindt.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
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

|KQL |Voorbeeld query resource grafiek |Notities |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Sleutel kluizen tellen](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Resources weergeven die opslag bevatten](../samples/starter.md#show-storage) | |
|[uitbreidbaar](/azure/kusto/query/extendoperator) |[Virtuele machines tellen op type besturingssysteem](../samples/starter.md#count-os) | |
|[Jointypen](/azure/kusto/query/joinoperator) |[Sleutel kluis met de naam van het abonnement](../samples/advanced.md#join) |Ondersteunde jointypen: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). De limiet van 3 `join` in één query, waarvan 1 een kruis tabel kan zijn `join` . Als alle verschillende tabellen `join` worden gebruikt tussen _resource_ en _ResourceContainers_, is 3 kruis tabel `join` toegestaan. Aangepaste deelname strategieën, zoals broadcast toevoegen, zijn niet toegestaan. `join`Zie [resource Graph Tables](#resource-graph-tables)(Engelstalig) voor welke tabellen kunnen worden gebruikt. |
|[limiet](/azure/kusto/query/limitoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van `take` . Werkt niet met [overs Laan](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Verouderde operator `mv-expand` . gebruik in plaats daarvan. _RowLimit_ maximum van 400. De standaard waarde is 128. |
|[MV-uitvouwen](/azure/kusto/query/mvexpandoperator) |[Een lijst met Cosmos DB met specifieke schrijflocaties weergeven](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ maximum van 400. De standaard waarde is 128. De limiet van 2 `mv-expand` in één query.|
|[order](/azure/kusto/query/orderoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van `sort` |
|[project](/azure/kusto/query/projectoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) | |
|[project-weg](/azure/kusto/query/projectawayoperator) |[Kolommen verwijderen uit resultaten](../samples/advanced.md#remove-column) | |
|[acties](/azure/kusto/query/sortoperator) |[Een lijst van resources weergeven, gesorteerd op naam](../samples/starter.md#list-resources) |Synoniem van `order` |
|[samenvatten](/azure/kusto/query/summarizeoperator) |[Azure-resources tellen](../samples/starter.md#count-resources) |Alleen de eerste vereenvoudigde pagina |
|[Houd](/azure/kusto/query/takeoperator) |[Een lijst van alle openbare IP-adressen weergeven](../samples/starter.md#list-publicip) |Synoniem van `limit` . Werkt niet met [overs Laan](./work-with-data.md#skipping-records). |
|[top](/azure/kusto/query/topoperator) |[De eerste vijf virtuele machines weergeven op naam en met hun type besturingssysteem](../samples/starter.md#show-sorted) | |
|[Réunion](/azure/kusto/query/unionoperator) |[Resultaten van twee query's combineren tot één resultaat](../samples/advanced.md#unionresults) |Eén tabel _toegestaan:_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=`  \] _tabel kolom naam_. Maxi maal drie `union` zijden in één query. Het is niet toegestaan om de tabel met fuzzy op te lossen `union` . Kan worden gebruikt binnen één tabel of tussen de tabellen _resources_ en _ResourceContainers_ . |
|[waar](/azure/kusto/query/whereoperator) |[Resources weergeven die opslag bevatten](../samples/starter.md#show-storage) | |

Er is een standaard limiet van 3 `join` en 3 `mv-expand` Opera tors in één resource Graph SDK-query. U kunt een verhoging van deze limieten voor uw Tenant aanvragen via **Help en ondersteuning**.

Voor het ondersteunen van de portal-ervaring ' query openen ' heeft Azure resource Graph Explorer een hogere wereld wijde limiet dan resource Graph SDK.

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
