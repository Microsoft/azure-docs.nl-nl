---
title: Azure Blob Storage als Event Grid bron
description: Hierin worden de eigenschappen beschreven die worden gegeven voor Blob Storage-gebeurtenissen met Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a914edbb6f624617766c77b277d7ee8e6ad08bd9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023956"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Azure Blob Storage als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor Blob Storage-gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's. Het biedt ook een lijst met snel starten en zelf studies voor het gebruik van Azure Blob Storage als een gebeurtenis bron.


>[!NOTE]
> Alleen opslag accounts van het type **StorageV2 (algemeen gebruik v2)**, **BlockBlobStorage** en **BlobStorage** ondersteunings gebeurtenis integratie. **Storage (algemeen gebruik v1)** biedt *geen* ondersteuning voor integratie met Event grid.

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

### <a name="list-of-events-for-blob-rest-apis"></a>Lijst met gebeurtenissen voor BLOB REST-Api's

Deze gebeurtenissen worden geactiveerd wanneer een-client een BLOB maakt, vervangt of verwijdert door de BLOB REST-Api's aan te roepen.

> [!NOTE]
> De `$logs` containers en kunnen niet worden `$blobchangefeed` geïntegreerd met Event grid, waardoor de activiteit in deze containers geen gebeurtenissen genereert. Het gebruik van het DFS-eind punt *`(abfss://URI) `* voor niet-hiërarchische naam ruimte ingeschakelde accounts genereert geen gebeurtenissen, maar er worden door het BLOB-eind punt *`(wasb:// URI)`* gebeurtenissen gegenereerd.

 |Gebeurtenis naam |Description|
 |----------|-----------|
 |**Micro soft. storage. BlobCreated** |Wordt geactiveerd wanneer een BLOB wordt gemaakt of vervangen. <br>Deze gebeurtenis wordt met name geactiveerd wanneer clients de `PutBlob` , `PutBlockList` , of bewerkingen gebruiken `CopyBlob` die beschikbaar zijn in de BLOB-rest API.   |
 |**Micro soft. storage. BlobDeleted** |Wordt geactiveerd wanneer een BLOB wordt verwijderd. <br>Deze gebeurtenis wordt met name geactiveerd wanneer clients de bewerking aanroepen `DeleteBlob` die beschikbaar is in de BLOB-rest API. |

> [!NOTE]
> Als u ervoor wilt zorgen dat de gebeurtenis **micro soft. storage. BlobCreated** alleen wordt geactiveerd als een blok-BLOB volledig is doorgevoerd, filtert u de gebeurtenis voor de `CopyBlob` `PutBlob` `PutBlockList` aanroepen, en rest API. Met deze API-aanroepen wordt de gebeurtenis **micro soft. storage. BlobCreated** alleen geactiveerd wanneer gegevens volledig zijn doorgevoerd in een blok-blob. Zie [gebeurtenissen filteren voor Event grid voor](./how-to-filter-events.md)meer informatie over het maken van een filter.

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lijst met gebeurtenissen voor Azure Data Lake Storage REST-Api's van $2

Deze gebeurtenissen worden geactiveerd als u een hiërarchische naam ruimte in het opslag account inschakelt en clients Azure Data Lake Storage Gen2 REST-Api's aanroepen. Zie [Introduction to Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)voor meer informatie Abou Azure data Lake Storage Gen2.

|Gebeurtenis naam|Description|
|----------|-----------|
|**Micro soft. storage. BlobCreated** | Wordt geactiveerd wanneer een BLOB wordt gemaakt of vervangen. <br>Deze gebeurtenis wordt met name geactiveerd wanneer clients de `CreateFile` bewerkingen en gebruiken `FlushWithClose` die beschikbaar zijn in de Azure data Lake Storage Gen2 rest API. |
|**Micro soft. storage. BlobDeleted** |Wordt geactiveerd wanneer een BLOB wordt verwijderd. <br>Deze gebeurtenis wordt met name ook geactiveerd wanneer clients de bewerking aanroepen `DeleteFile` die beschikbaar is in de Azure Data Lake Storage Gen2 rest API. |
|**Micro soft. storage. BlobRenamed**|Wordt geactiveerd wanneer de naam van een BLOB wordt gewijzigd. <br>Deze gebeurtenis wordt met name geactiveerd wanneer clients de bewerking gebruiken `RenameFile` die beschikbaar is in de Azure Data Lake Storage Gen2 rest API.|
|**Micro soft. storage. DirectoryCreated**|Wordt geactiveerd wanneer een map wordt gemaakt. <br>Deze gebeurtenis wordt met name geactiveerd wanneer clients de bewerking gebruiken `CreateDirectory` die beschikbaar is in de Azure Data Lake Storage Gen2 rest API.|
|**Micro soft. storage. DirectoryRenamed**|Wordt geactiveerd wanneer de naam van een map wordt gewijzigd. <br>Deze gebeurtenis wordt met name geactiveerd wanneer clients de bewerking gebruiken `RenameDirectory` die beschikbaar is in de Azure Data Lake Storage Gen2 rest API.|
|**Micro soft. storage. DirectoryDeleted**|Wordt geactiveerd wanneer een map wordt verwijderd. <br>Deze gebeurtenis wordt met name geactiveerd wanneer clients de bewerking gebruiken `DeleteDirectory` die beschikbaar is in de Azure Data Lake Storage Gen2 rest API.|

> [!NOTE]
> Als u ervoor wilt zorgen dat de gebeurtenis **micro soft. storage. BlobCreated** alleen wordt geactiveerd als een blok-BLOB volledig is doorgevoerd, filtert u de gebeurtenis voor de aanroep van de `FlushWithClose` rest API. Deze API-aanroep activeert de gebeurtenis **micro soft. storage. BlobCreated** alleen nadat de gegevens volledig zijn doorgevoerd in een blok-blob. Zie [gebeurtenissen filteren voor Event grid voor](./how-to-filter-events.md)meer informatie over het maken van een filter.

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>De inhoud van een gebeurtenis reactie

Wanneer een gebeurtenis wordt geactiveerd, verzendt de Event Grid-Service gegevens over die gebeurtenis om het eind punt te abonneren.

Deze sectie bevat een voor beeld van wat de gegevens voor elke Blob-opslag gebeurtenis weer geven.

### <a name="microsoftstorageblobcreated-event"></a>Micro soft. storage. BlobCreated-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Micro soft. storage. BlobCreated-gebeurtenis (Data Lake Storage Gen2)

Als het Blob Storage-account een hiërarchische naam ruimte heeft, zien de gegevens er ongeveer uit als in het vorige voor beeld, met uitzonde ring van deze wijzigingen:

* De `dataVersion` sleutel wordt ingesteld op een waarde van `2` .

* De `data.api` sleutel wordt ingesteld op de teken reeks `CreateFile` of `FlushWithClose` .

* De `contentOffset` sleutel is opgenomen in de gegevensset.

> [!NOTE]
> Als toepassingen de `PutBlockList` bewerking gebruiken om een nieuwe BLOB naar het account te uploaden, bevatten de gegevens deze wijzigingen niet.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Micro soft. storage. BlobDeleted-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Micro soft. storage. BlobDeleted-gebeurtenis (Data Lake Storage Gen2)

Als het Blob Storage-account een hiërarchische naam ruimte heeft, zien de gegevens er ongeveer uit als in het vorige voor beeld, met uitzonde ring van deze wijzigingen:

* De `dataVersion` sleutel wordt ingesteld op een waarde van `2` .

* De `data.api` sleutel wordt ingesteld op de teken reeks `DeleteFile` .

* De `url` sleutel bevat het pad `dfs.core.windows.net` .

> [!NOTE]
> Als toepassingen de `DeleteBlob` bewerking gebruiken om een BLOB uit het account te verwijderen, bevatten de gegevens deze wijzigingen niet.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Micro soft. storage. BlobRenamed-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Micro soft. storage. DirectoryCreated-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Micro soft. storage. DirectoryRenamed-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Micro soft. storage. DirectoryDeleted-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
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
| gegevens | object | Gebeurtenis gegevens van Blob-opslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| api | tekenreeks | De bewerking die de gebeurtenis heeft geactiveerd. |
| clientRequestId | tekenreeks | een aanvraag-id van de client voor de bewerking van de opslag-API. Deze id kan worden gebruikt om te correleren Azure Storage Diagnostische logboeken met behulp van het veld ' client-request-id ' in de logboeken, en kan worden verschaft in client aanvragen via de header ' x-MS-Client-Request-id '. Zie de [logboek indeling](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | tekenreeks | Door de service gegenereerde aanvraag-id voor de bewerking van de opslag-API. Kan worden gebruikt om te correleren Azure Storage Diagnostische logboeken met behulp van het veld aanvraag-id-header in de logboeken en wordt geretourneerd van het initiëren van de API-aanroep in de header x-MS-Request-id. Zie de [logboek indeling](/rest/api/storageservices/storage-analytics-log-format). |
| eTag | tekenreeks | De waarde die u kunt gebruiken om bewerkingen voorwaardelijk uit te voeren. |
| Invoer | tekenreeks | Het opgegeven inhouds type voor de blob. |
| contentLength | geheel getal | De grootte van de BLOB in bytes. |
| blobType | tekenreeks | Het type blob. Geldige waarden zijn ' BlockBlob ' of ' PageBlob '. |
| contentOffset | getal | De offset in bytes van een schrijf bewerking die wordt uitgevoerd op het punt waar de toepassing die de gebeurtenis heeft geactiveerd, het schrijven naar het bestand heeft voltooid. <br>Wordt alleen weer gegeven voor gebeurtenissen die zijn geactiveerd op Blob Storage-accounts die een hiërarchische naam ruimte hebben.|
| destinationUrl |tekenreeks | De URL van het bestand dat bestaat nadat de bewerking is voltooid. Als bijvoorbeeld de naam van een bestand wordt gewijzigd, bevat de `destinationUrl` eigenschap de URL van de nieuwe bestands naam. <br>Wordt alleen weer gegeven voor gebeurtenissen die zijn geactiveerd op Blob Storage-accounts die een hiërarchische naam ruimte hebben.|
| sourceUrl |tekenreeks | De URL van het bestand dat voor de bewerking bestaat. Als bijvoorbeeld de naam van een bestand wordt gewijzigd, bevat de `sourceUrl` URL van de oorspronkelijke bestands naam vóór de bewerking naamswijziging. <br>Wordt alleen weer gegeven voor gebeurtenissen die zijn geactiveerd op Blob Storage-accounts die een hiërarchische naam ruimte hebben. |
| url | tekenreeks | Het pad naar de blob. <br>Als de client gebruikmaakt van een BLOB-REST API, heeft de URL deze structuur: *\<storage-account-name\> . \<container-name\> / \<file-name\> blob.core.Windows.net/*. <br>Als de client een Data Lake Storage REST API gebruikt, heeft de URL deze structuur: *\<storage-account-name\> . DFS.core.Windows.net/ \<file-system-name\> / \<file-name\>*. |
| recursieve | tekenreeks | `True` de bewerking uitvoeren op alle onderliggende directory's. anders `False` . <br>Wordt alleen weer gegeven voor gebeurtenissen die zijn geactiveerd op Blob Storage-accounts die een hiërarchische naam ruimte hebben. |
| sequencer | tekenreeks | Een ondoorzichtige teken reeks waarde voor de logische reeks gebeurtenissen voor een bepaalde blobnaam.  Gebruikers kunnen standaard teken reeks vergelijking gebruiken om inzicht te krijgen in de relatieve volg orde van twee gebeurtenissen op dezelfde blobnaam. |
| storageDiagnostics | object | Diagnostische gegevens die af en toe worden opgenomen door de Azure Storage service. Indien aanwezig, moet worden genegeerd door gebeurtenis verbruikers. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel  |Beschrijving  |
|---------|---------|
| [Quick Start: Blob Storage-gebeurtenissen naar een aangepast webeindpunt door sturen met Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u met Azure CLI Blob Storage-gebeurtenissen naar een webhook verzendt. |
| [Snelstartgids: Blob Storage-gebeurtenissen door sturen naar een aangepast webeindpunt met Power shell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u Azure PowerShell kunt gebruiken voor het verzenden van Blob Storage-gebeurtenissen naar een webhook. |
| [Snelstartgids: Blob Storage-gebeurtenissen maken en routeren met de Azure Portal](blob-event-quickstart-portal.md) | Laat zien hoe u de portal kunt gebruiken voor het verzenden van Blob Storage-gebeurtenissen naar een webhook. |
| [Azure CLI: abonneren op gebeurtenissen voor een Blob Storage-account](./scripts/event-grid-cli-blob.md) | Voorbeeld script dat zich abonneert op een gebeurtenis voor een Blob Storage-account. Hiermee wordt de gebeurtenis naar een webhook verzonden. |
| [Power shell: abonneren op gebeurtenissen voor een Blob Storage-account](./scripts/event-grid-powershell-blob.md) | Voorbeeld script dat zich abonneert op een gebeurtenis voor een Blob Storage-account. Hiermee wordt de gebeurtenis naar een webhook verzonden. |
| [Resource Manager-sjabloon: Blob-opslag en-abonnement maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Hiermee implementeert u een Azure Blob-opslagaccount en abonneert u zich op gebeurtenissen van die opslagaccount. Er worden gebeurtenissen naar een webhook verzonden. |
| [Overzicht: reageren op Blob Storage-gebeurtenissen](../storage/blobs/storage-blob-event-overview.md) | Overzicht van het integreren van Blob Storage met Event Grid. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](overview.md) voor een inleiding tot Azure Event Grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
* Zie voor een inleiding tot het werken met Blob Storage [-gebeurtenissen routeren van Blob-opslag-Azure cli](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
