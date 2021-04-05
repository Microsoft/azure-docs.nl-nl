---
title: Azure Event Hubs als Event Grid bron
description: Beschrijft de eigenschappen die worden gegeven voor Event hubs-gebeurtenissen met Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: e9bb4b5a27173181c7295e96a1eb0654a1a929e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363506"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Azure Event Hubs als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor Event hubs-gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's.

## <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Event Hubs verzendt het gebeurtenis type **micro soft. EventHub. CaptureFileCreated** wanneer een capture-bestand wordt gemaakt.

## <a name="example-event"></a>Voorbeeld gebeurtenis

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)

Deze voorbeeld gebeurtenis toont het schema van een event hubs-gebeurtenis die optreedt wanneer de functie Capture een bestand opslaat: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

Deze voorbeeld gebeurtenis toont het schema van een event hubs-gebeurtenis die optreedt wanneer de functie Capture een bestand opslaat: 

```json
[
    {
        "source": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "type": "Microsoft.EventHub.CaptureFileCreated",
        "time": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "specversion": "1.0"
    }
]
```


---


## <a name="event-properties"></a>Gebeurtenis eigenschappen

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)
Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `topic` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| `eventType` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `eventTime` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Event hub-gebeurtenis gegevens. |
| `dataVersion` | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| `metadataVersion` | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `source` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| `type` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `time` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Event hub-gebeurtenis gegevens. |
| `specversion` | tekenreeks | CloudEvents-schema specificatie versie. |

---

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `fileUrl` | tekenreeks | Het pad naar het opname bestand. |
| `fileType` | tekenreeks | Het bestands type van het opname bestand. |
| `partitionId` | tekenreeks | De Shard-ID. |
| `sizeInBytes` | geheel getal | De bestands grootte. |
| `eventCount` | geheel getal | Het aantal gebeurtenissen in het bestand. |
| `firstSequenceNumber` | geheel getal | Het kleinste Volg nummer uit de wachtrij. |
| `lastSequenceNumber` | geheel getal | Het laatste Volg nummer van de wachtrij. |
| `firstEnqueueTime` | tekenreeks | De eerste keer van de wachtrij. |
| `lastEnqueueTime` | tekenreeks | De laatste tijd van de wachtrij. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: stream big data naar een Data Warehouse](event-grid-event-hubs-integration.md) | Wanneer Event Hubs een opname bestand maakt, wordt Event Grid een gebeurtenis naar een functie-app verzonden. De app haalt het opname bestand op en migreert gegevens naar een Data Warehouse. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](overview.md) voor een inleiding tot Azure Event Grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
* Zie [Stream Big data in een Data Warehouse](event-grid-event-hubs-integration.md)voor meer informatie over het afhandelen van Event hubs-gebeurtenissen.