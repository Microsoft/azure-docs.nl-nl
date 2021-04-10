---
title: Richtlijnen voor vertraagde aanvragen
description: Meer informatie over Group, sprei ding, pagineren en query's parallel om te voor komen dat aanvragen worden beperkt door Azure resource Graph.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd3cf4d411733e831c94039c3bc9aeaf0e95271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917704"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Richt lijnen voor vertraagde aanvragen in azure resource Graph

Bij het maken van een programma en veelvuldig gebruik van gegevens van Azure-resource grafiek, moet u rekening houden met de manier waarop beperking invloed heeft op de resultaten van de query's. Het wijzigen van de manier waarop gegevens worden aangevraagd, kan u helpen om te voor komen dat uw organisatie wordt beperkt en de stroom van gegevens van uw Azure-resources behouden.

In dit artikel worden vier gebieden en patronen besproken die betrekking hebben op het maken van query's in azure resource Graph:

- Informatie over headers voor bandbreedtebeperking
- Query's groeperen
- Query's spreiden
- De impact van de paginering

## <a name="understand-throttling-headers"></a>Informatie over headers voor bandbreedtebeperking

In azure resource Graph wordt een quotum nummer toegewezen voor elke gebruiker op basis van een tijd venster. Een gebruiker kan bijvoorbeeld Maxi maal 15 query's verzenden binnen elk 5-seconde venster zonder dat dit wordt beperkt. De quotum waarde wordt bepaald door een groot aantal factoren en kan worden gewijzigd.

In elke query-antwoord voegt Azure-resource grafiek twee beperkings koppen toe:

- `x-ms-user-quota-remaining` (int): Het resterende resourcequotum voor de gebruiker. Deze waarde wordt toegewezen aan het aantal query's.
- `x-ms-user-quota-resets-after` (UU: mm: SS): de tijds duur tot het quotum verbruik van een gebruiker opnieuw wordt ingesteld.

Wanneer een beveiligingsprincipal toegang heeft tot meer dan 5000 abonnementen binnen het [query bereik](./query-language.md#query-scope)van de Tenant of beheer groep, wordt het antwoord beperkt tot de eerste 5000-abonnementen en `x-ms-tenant-subscription-limit-hit` wordt de header geretourneerd als `true` .

Om te laten zien hoe de headers werken, bekijken we een query-antwoord met de koptekst en de waarden van `x-ms-user-quota-remaining: 10` en `x-ms-user-quota-resets-after: 00:00:03` .

- Binnen de komende 3 seconden kunnen Maxi maal 10 query's worden verzonden zonder te zijn beperkt.
- In 3 seconden worden de waarden van `x-ms-user-quota-remaining` en `x-ms-user-quota-resets-after` teruggezet naar `15` en `00:00:05` respectievelijk.

Als u een voor beeld wilt van het gebruik van de kopteksten voor _uitstel_ op query aanvragen, raadpleegt u het voor beeld in [query parallel](#query-in-parallel).

## <a name="grouping-queries"></a>Query's groeperen

Het groeperen van query's op het abonnement, de resource groep of de afzonderlijke resource is efficiënter dan gelijktijdig query's. De quota kosten van een grotere query zijn vaak lager dan de quota kosten van veel kleine en doel query's. De groeps grootte wordt aanbevolen om minder dan _300_ te zijn.

- Voor beeld van een slecht geoptimaliseerde benadering

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Voor beeld #1 van een geoptimaliseerde groeperings benadering

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Voor beeld #2 van een geoptimaliseerde groeperings benadering voor het ophalen van meerdere resources in één query

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Query's spreiden

Vanwege de manier waarop het beperken van beperkingen wordt afgedwongen, wordt aangeraden query's te spreiden. Dat wil zeggen, in plaats van 60 query's tegelijkertijd te verzenden naar 4 5-Second-Vensters:

- Niet-gespreide query planning

  | Aantal query's         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Tijds interval (sec.) | 0-5 | 5-10 | 10-15 | 15-20 |

- Verspringend query schema

  | Aantal query's         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Tijds interval (sec.) | 0-5 | 5-10 | 10-15 | 15-20 |

Hier volgt een voor beeld van het respecteren van beperkings koppen bij het uitvoeren van een query op Azure resource Graph:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Parallelle query

Hoewel groepering wordt aanbevolen voor parallel Lise ring, zijn er momenten waarop query's niet eenvoudig kunnen worden gegroepeerd. In dergelijke gevallen kunt u een query uitvoeren op Azure resource Graph door meerdere query's op parallelle wijze te verzenden. Hier volgt een voor beeld van hoe u _uitstel_ op basis van het beperken van headers in dergelijke scenario's:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginering

Omdat Azure resource Graph Maxi maal 1000 vermeldingen in één query antwoord retourneert, moet u mogelijk uw query's [pagineren](./work-with-data.md#paging-results) om de volledige gegevensset te verkrijgen die u zoekt. Sommige Azure-resource grafiek-clients verwerken de paginering echter anders dan andere.

- C# SDK

  Wanneer u de ResourceGraph-SDK gebruikt, moet u de paginering afhandelen door het Skip-token door te geven dat wordt geretourneerd door de vorige query respons naar de volgende gepagineerde query. Dit ontwerp houdt in dat u de resultaten van alle gepagineerde aanroepen moet verzamelen en samen aan het eind kunt combi neren. In dit geval neemt elke gepagineerde query die u verzendt één query quotum:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  Wanneer u Azure CLI of Azure PowerShell gebruikt, worden query's naar Azure resource Graph automatisch gepagineerd om Maxi maal 5000 vermeldingen op te halen. De query resultaten retour neren een gecombineerde lijst met vermeldingen van alle gepagineerde aanroepen. In dit geval, afhankelijk van het aantal items in het query resultaat, kan één gepagineerde query meer dan één query quotum verbruiken. In de volgende voor beelden kan één uitvoering van de query bijvoorbeeld tot vijf query quota verbruiken:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Nog steeds beperkt?

Als u na het uitvoeren van de bovenstaande aanbevelingen wilt worden beperkt, neemt u contact op met het team [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com) .

Geef de volgende details op:

- Uw specifieke gebruiks voorbeeld-en Business-stuur programma moeten een hogere beperkings limiet hebben.
- Hoeveel resources hebt u toegang tot? Hoeveel van de retour neren van één query
- Van welke typen resources bent u geïnteresseerd?
- Wat is uw query patroon? X-query's per Y seconden, enzovoort.

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie [Geavanceerde query's](../samples/advanced.md) voor geavanceerde gebruikswijzen.
- Lees meer over het [verkennen van resources](explore-resources.md).