---
title: Azure cache voor redis als Event Grid bron
description: Hierin worden de eigenschappen beschreven die voor Azure cache worden verschaft voor redis-gebeurtenissen met Azure Event Grid
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055970"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure cache voor redis als een Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor Azure cache voor redis-gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's. 

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Lijst met gebeurtenissen voor Azure cache voor redis REST-Api's

Deze gebeurtenissen worden geactiveerd wanneer een client een Azure-cache exporteert, importeert of schaalt voor redis REST-Api's. De patch gebeurtenis wordt geactiveerd door de redis-update.

 |Gebeurtenis naam |Description|
 |----------|-----------|
 |**Micro soft. cache. ExportRDBCompleted** |Wordt geactiveerd wanneer de cache gegevens worden geëxporteerd. |
 |**Micro soft. cache. ImportRDBCompleted** |Wordt geactiveerd wanneer de cache gegevens worden geïmporteerd. |
 |**Micro soft. cache. PatchingCompleted** |Wordt geactiveerd wanneer de patching is voltooid. |
 |**Micro soft. cache. ScalingCompleted** |Wordt geactiveerd wanneer het schalen is voltooid. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>De inhoud van een gebeurtenis reactie

Wanneer een gebeurtenis wordt geactiveerd, verzendt de Event Grid-Service gegevens over die gebeurtenis om het eind punt te abonneren.

Deze sectie bevat een voor beeld van hoe die gegevens eruitzien voor elke Azure-cache voor redis-gebeurtenis.

### <a name="microsoftcachepatchingcompleted-event"></a>Micro soft. cache. PatchingCompleted-gebeurtenis

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Micro soft. cache. ImportRDBCompleted-gebeurtenis

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Micro soft. cache. ExportRDBCompleted-gebeurtenis

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Micro soft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Gebeurtenis eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| Type | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| gegevens | object | Azure cache voor redis-gebeurtenis gegevens. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| tijdstempel | tekenreeks | Het tijdstip waarop de gebeurtenis heeft plaatsgevonden. |
| naam | tekenreeks | De naam van de gebeurtenis. |
| status | tekenreeks | De status van de gebeurtenis. Mislukt of geslaagd. |


## <a name="quickstarts"></a>Quickstarts

Als u Azure cache wilt proberen voor redis-gebeurtenissen, raadpleegt u een van de volgende Quick Start-artikelen:

|Als u dit hulp programma wilt gebruiken:    |Zie dit artikel: |
|--|-|
|Azure Portal    |[Quick Start: Azure cache naar redis-gebeurtenissen naar een webeindpunt door sturen met de Azure Portal](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Snelstartgids: Azure cache voor redis-gebeurtenissen naar een webeindpunt door sturen met Power shell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Snelstartgids: Azure-cache voor redis-gebeurtenissen naar een webeindpunt door sturen met Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](overview.md) voor een inleiding tot Azure Event Grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.

