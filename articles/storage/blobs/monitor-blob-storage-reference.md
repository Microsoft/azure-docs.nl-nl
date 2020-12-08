---
title: Naslag informatie voor Azure Blob Storage-bewakings gegevens | Microsoft Docs
description: Naslag informatie over Logboeken en metrieken voor het bewaken van gegevens uit Azure Blob-opslag.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 16ae2f9e74202aff47e58a22dbe21a28d8280a7e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780719"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Naslag informatie voor Azure Blob Storage-bewakings gegevens

Zie [bewaking Azure Storage](monitor-blob-storage.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure Storage.

## <a name="metrics"></a>Metrische gegevens

De volgende tabellen geven een lijst van de platform gegevens die zijn verzameld voor Azure Storage. 

### <a name="capacity-metrics"></a>Metrische gegevens over capaciteit

Metrische waarden voor capaciteit worden dagelijks vernieuwd (Maxi maal 24 uur). De tijdgranulariteit definieert het tijds interval waarvoor metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens over capaciteit zijn één uur (PT1H).

Azure Storage biedt de volgende metrische gegevens over capaciteit in Azure Monitor.

#### <a name="account-level"></a>Account niveau

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob Storage

In deze tabel worden [metrische gegevens voor Blob-opslag](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices)weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| BlobCapacity | Het totaal van de Blob-opslag die in het opslag account wordt gebruikt. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 <br/> Dimensies: **BlobType** en **BlobTier** ([definitie](#metrics-dimensions)) |
| BlobCount    | Het aantal BLOB-objecten dat is opgeslagen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 <br/> Dimensies: **BlobType** en **BlobTier** ([definitie](#metrics-dimensions)) |
| BlobProvisionedSize | De hoeveelheid opslag die is ingericht in het opslag account. Deze metriek geldt alleen voor Premium Storage-accounts. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld |
| ContainerCount    | Het aantal containers in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| IndexCapacity     | De hoeveelheid opslag die wordt gebruikt door ADLS Gen2 hiërarchische index <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

### <a name="transaction-metrics"></a>Metrische gegevens voor transacties

Metrische gegevens over trans acties worden verzonden voor elke aanvraag naar een opslag account van Azure Storage naar Azure Monitor. In het geval van geen activiteit in uw opslag account, worden er geen gegevens in de periode weer gegeven. Alle metrische gegevens over trans acties zijn beschikbaar op zowel account-als Blob Storage-service niveau. De tijd korrel definieert het tijds interval dat metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens van de trans actie zijn PT1H en PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrische dimensies

Azure Storage ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor.

### <a name="dimensions-available-to-all-storage-services"></a>Beschik bare dimensies voor alle opslag Services

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Dimensies die specifiek zijn voor Blob Storage

| Dimensie naam | Beschrijving |
| ------------------- | ----------------- |
| **BlobType** | Het type BLOB voor alleen metrische gegevens van blobs. De ondersteunde waarden zijn **BlockBlob**, **PageBlob** en **Azure data Lake Storage**. Toevoeg-blobs zijn opgenomen in **BlockBlob**. |
| **BlobTier** | Azure Storage biedt verschillende toegangs lagen, waarmee u gegevens van blob-objecten op de meest rendabele manier kunt opslaan. Meer weer geven in [Azure Storage BLOB-laag](../blobs/storage-blob-storage-tiers.md). De ondersteunde waarden zijn onder andere: <br/> <li>**Warm**: warme laag</li> <li>**Cool**: cool-laag</li> <li>**Archief**: laag van archief</li> <li>**Premium**: Premium-laag voor blok-BLOB</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: laag typen voor Premium-pagina-BLOB</li> <li>**Standaard**: laag type voor de standaard pagina-BLOB</li> <li>**Ongetierd**: laag type voor algemeen gebruik v1-opslag account</li> |

Voor de metrische gegevens die maat staven ondersteunen, moet u de dimensie waarde opgeven om de bijbehorende metrische waarden weer te geven. Als u bijvoorbeeld de  **transactie** waarde voor geslaagde reacties bekijkt, moet u de **ResponseType** -dimensie filteren op **geslaagd**. Als u de **BlobCount** -waarde voor blok-BLOB bekijkt, moet u de dimensie **BlobType** filteren met **BlockBlob**.

## <a name="resource-logs-preview"></a>Resource Logboeken (preview-versie)

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview-versie en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Met deze preview-versie kunt u logboeken maken voor blobs (inclusief Azure Data Lake Storage Gen2), bestanden, wacht rijen, tabellen, Premium-opslag accounts in versie 1 voor algemeen gebruik en voor algemeen gebruik v2-opslag accounts. Klassieke opslag accounts worden niet ondersteund.

De volgende tabel geeft een lijst van de eigenschappen voor Azure Storage bron Logboeken wanneer ze worden verzameld in Azure Monitor Logboeken of Azure Storage. De eigenschappen beschrijven de bewerking, de service en het type autorisatie dat is gebruikt om de bewerking uit te voeren.

### <a name="fields-that-describe-the-operation"></a>Velden waarin de bewerking wordt beschreven

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Velden die beschrijven hoe de bewerking is geverifieerd

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Velden waarin de service wordt beschreven

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Zie ook

- Zie [bewaking Azure Storage](monitor-blob-storage.md) voor een beschrijving van de bewakings Azure Storage.
- Zie [Azure-resources bewaken met Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.