---
title: Filters, ordening en paginering van entiteiten
description: Krijg meer informatie over het filteren, rangschikken en pagineren van Azure Media Services v3-entiteiten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 19febc833f087afdfecde3274a1044598d082521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955306"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Media Services-entiteiten filteren, rangschikken en pagineren

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit onderwerp worden de OData-queryopties en ondersteuning voor paginering besproken die beschikbaar zijn wanneer u Azure Media Services v3-entiteiten vermeldt.

## <a name="considerations"></a>Overwegingen

* Eigenschappen van entiteiten van het type `Datetime` hebben altijd de UTC-indeling.
* Spaties in de querytekenreeks moeten met een URL zijn versleuteld voordat u een aanvraag verzendt.

## <a name="comparison-operators"></a>Vergelijkingsoperatoren

U kunt de volgende operators gebruiken om een veld te vergelijken met een constante waarde:

Gelijkheidsoperators:

- `eq`: Test of een veld *gelijk is aan* een constante waarde.
- `ne`: Test of een veld *niet gelijk is aan* een constante waarde.

Bereikoperators:

- `gt`: Test of een veld *groter is dan* een constante waarde.
- `lt`: Test of een veld *kleiner is dan* een constante waarde.
- `ge`: Test of een veld *groter is dan of gelijk is aan* een constante waarde.
- `le`: Test of een veld *kleiner is dan of gelijk is aan* een constante waarde.

## <a name="filter"></a>Filteren

Gebruik `$filter` om een OData-filterparameter op te geven om alleen de objecten te zoeken waarin u geïnteresseerd bent.

In het volgende REST-voorbeeld wordt gefilterd op de `alternateId`-waarde van een asset:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

In het volgende C#-voorbeeld wordt gefilterd op de datum waarop de asset is gemaakt:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Sorteren op

Gebruik `$orderby` om de geretourneerde objecten te sorteren op de opgegeven parameter. Bijvoorbeeld:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Voor het sorteren van de resultaten in oplopende of aflopende volgorde voegt u `asc` of `desc` toe aan de veldnaam, gescheiden door een spatie. Bijvoorbeeld: `$orderby properties/created desc`.

## <a name="skip-token"></a>Skip-token

Als een query-antwoord veel items bevat, retourneert de service een `$skiptoken`- (`@odata.nextLink`)-waarde die u gebruikt om de volgende pagina met resultaten op te halen. Gebruik de waarde om door de hele resultatenset te bladeren.

In Media Services v3 kunt u de paginagrootte niet configureren. De paginagrootte varieert per entiteitstype. Lees de volgende afzonderlijke secties voor details.

Als entiteiten worden gemaakt of verwijderd terwijl u door de verzameling bladert, worden de wijzigingen weerspiegeld in de geretourneerde resultaten (als die wijzigingen zich bevinden in het deel van de verzameling dat niet is gedownload).

> [!TIP]
> Gebruik altijd `nextLink` om de verzameling op te sommen en wees niet afhankelijk van een specifieke paginagrootte.
>
> De `nextLink`-waarde wordt alleen weergegeven als er meer dan één pagina met entiteiten is.

Kijk eens naar het volgende voorbeeld waarin `$skiptoken` wordt gebruikt. Vergeet niet *amstestaccount* te vervangen door uw accountnaam en de *api-version*-waarde in te stellen op de meest recente versie.

Als u een lijst met assets zoals deze aanvraagt:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Wordt een antwoord geretourneerd dat vergelijkbaar is met deze:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Vervolgens vraagt u de volgende pagina aan door een get-aanvraag te verzenden voor:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

In het volgende C#-voorbeeld ziet u hoe u alle streaminglocators in het account kunt opsommen.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Logische operators gebruiken om query-opties te combineren

Media Services v3 biedt ondersteuning voor de logische operators **OR** en **AND**. 

In het volgende REST-voorbeeld wordt de status van de taak gecontroleerd:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

U bouwt als volgt dezelfde query in C#: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opties van entiteiten filteren en rangschikken

In de volgende tabel ziet u hoe u de filter- en rangschikopties kunt toepassen op verschillende entiteiten:

|Entiteitsnaam|Naam van eigenschap|Filteren|Bestellen|
|---|---|---|---|
|[Assets](/rest/api/media/assets/)|naam|`eq`, `gt`, `lt`, `ge`, `le`|`asc` en `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` en `desc`|
|[Beleid voor inhoudssleutels](/rest/api/media/contentkeypolicies)|naam|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||properties.policyId|`eq`, `ne`||
|[Taken](/rest/api/media/jobs)| naam  | `eq`            | `asc` en `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` en `desc`| 
|[Streaming-locators](/rest/api/media/streaminglocators)|naam|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` en `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
|[Beleid voor streaming](/rest/api/media/streamingpolicies)|naam|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` en `desc`|
|[Transformaties](/rest/api/media/transforms)| naam | `eq`            | `asc` en `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` en `desc`|

## <a name="next-steps"></a>Volgende stappen

* [Assets vermelden](/rest/api/media/assets/list)
* [Beleidsregels voor inhoudssleutels vermelden](/rest/api/media/contentkeypolicies/list)
* [Taken vermelden](/rest/api/media/jobs/list)
* [Beleid voor streaming vermelden](/rest/api/media/streamingpolicies/list)
* [Streaminglocators vermelden](/rest/api/media/streaminglocators/list)
* [Een bestand streamen](stream-files-dotnet-quickstart.md)
* [Quota en limieten](limits-quotas-constraints.md)
