---
title: Inleiding tot Blob-opslag (objectopslag)
titleSuffix: Azure Storage
description: Gebruik Azure Blob-opslag voor het opslaan van grote hoeveelheden ongestructureerde objectgegevens, zoals tekst of binaire gegevens. Azure Blob-opslag is zeer schaalbaar en beschikbaar.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 06/24/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b55ea814214397e12e50e7bc5047988394623940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703586"
---
# <a name="introduction-to-azure-blob-storage"></a>Inleiding tot Azure Blob-opslag

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Resources voor blobopslag

Er zijn drie typen resources voor blobopslag:

- Het **opslagaccount**
- Een **container** in het opslagaccount
- Een **blob** in een container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Een diagram dat de relatie toont tussen een opslagaccount, containers en blobs](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Opslagaccounts

Een opslagaccount biedt een unieke naamruimte in Azure voor uw gegevens. Elk object dat u in Azure Storage opslaat, heeft een adres dat uw unieke accountnaam bevat. De combinatie van de accountnaam en het blob-eindpunt voor Azure Storage vormen het basisadres voor de objecten in uw opslagaccount.

Als de naam van uw opslagaccount bijvoorbeeld *mystorageaccount* is, is het standaardeindpunt voor de blobopslag:

```
http://mystorageaccount.blob.core.windows.net
```

Raadpleeg [Een opslagaccount maken](../common/storage-account-create.md) als u een opslagaccount wilt maken. Zie [Overzicht voor Azure-opslagaccounts](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over opslagaccounts.

### <a name="containers"></a>Containers

Een container kan een of meer blobs bevatten, net zoals een map een of meer bestanden kan bevatten in een bestandssysteem. Een opslagaccount kan een onbeperkt aantal containers bevatten en een container kan een onbeperkt aantal blobs bevatten.

> [!NOTE]
> De naam van een container mag alleen kleine letters bevatten. Zie [Naamgeving voor en verwijzingen naar containers, blobs en metagegevens](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) voor meer informatie over de naamgeving voor containers.

### <a name="blobs"></a>Blobs

Azure Storage ondersteunt drie soorten blobs:

- **Blok-blobs** worden gebruikt voor het opslaan van tekst en binaire gegevens. Blok-blobs bestaan uit blokken met gegevens die afzonderlijk kunnen worden beheerd. Blok-blobs kunnen tot ongeveer 4,75 TiB gegevens opslagen. Grotere blok-blobs zijn beschikbaar in de preview-versie, ze kunnen tot ongeveer 190,7 TiB opslagen
- **Toevoeg-blobs** bestaan uit blokken zoals blok-blobs, maar zijn geoptimaliseerd voor toevoegbewerkingen. Toevoeg-blobs zijn ideaal voor scenario's zoals het registreren van logboekgegevens van virtuele machines.
- **Pagina-blobs** worden gebruikt voor het opslaan van bestanden voor willekeurige toegang tot maximaal 8 TiB in grootte. Met pagina-blobs worden bestanden voor de virtuele harde schijf (VHD) opgeslagen en kunnen ze dienen als schijven voor Azure-VM's. Zie [Overzicht van Azure-pagina-blobs](storage-blob-pageblob-overview.md) voor meer informatie over pagina-blobs

Zie [Blok-blobs, toevoeg-blobs en pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) voor meer informatie over de verschillende soorten blobs.

## <a name="move-data-to-blob-storage"></a>Gegevens verplaatsen naar de blobopslag

Er zijn een aantal oplossingen waarmee u bestaande gegevens naar de blobopslag kunt migreren:

- **AzCopy** is een gebruiksvriendelijk opdrachtregelprogramma voor Windows en Linux waarmee u in containers of opslagaccounts gegevens kunt kopiëren naar en vanuit de blobopslag. Zie [Gegevensoverdracht met AzCopy v10](../common/storage-use-azcopy-v10.md) voor meer informatie over AzCopy.
- De **bibliotheek voor gegevensverplaatsing van Azure Storage** is een .NET-bibliotheek voor het verplaatsen van gegevens tussen Azure Storage-services. Het hulpprogramma AzCopy is samengesteld uit de bibliotheek voor gegevensverplaatsing. Zie de [naslagdocumentatie](/dotnet/api/microsoft.azure.storage.datamovement) voor de bibliotheek voor gegevensverplaatsing voor meer informatie.
- **Azure Data Factory** ondersteunt het kopiëren van gegevens naar en van de blobopslag met behulp van de accountsleutel, Shared Access Signature, de service-principal of beheerde identiteiten voor Azure-resources. Zie [Gegevens naar en van Azure Blob Storage kopiëren met Azure Data Factory](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie.
- **Blobfuse** is een stuurprogramma voor het virtuele bestandssysteem voor Azure Blob Storage. U kunt Blobfuse gebruiken om via het Linux-bestandssysteem toegang te krijgen tot uw bestaande blok-blobgegevens in uw Azure Storage-account. Zie [Blob-opslag koppelen als een bestandssysteem met Blobfuse](storage-how-to-mount-container-linux.md) voor meer informatie.
- De **Azure Data Box**-service is beschikbaar voor het overzetten van on-premises gegevens naar de blobopslag, wanneer vanwege grote gegevenssets of netwerkbeperkingen de kabel geen realistische optie is voor het uploaden van gegevens. Afhankelijk van uw gegevensgrootte kunt u [Azure Data Box Disk](../../databox/data-box-disk-overview.md)-, [Azure Data Box](../../databox/data-box-overview.md)- of [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md)-apparaten van Microsoft aanvragen. U kunt vervolgens uw gegevens naar deze apparaten kopiëren en ze terugsturen naar Microsoft, waarna ze worden geüpload naar Blob Storage.
- De **Azure Import/Export-service** biedt een manier om grote hoeveelheden gegevens te importeren of exporteren naar en van uw opslagaccount met harde schijven die u voorziet. Zie [De Microsoft Azure Import/Export-service gebruiken om gegevens over te zetten naar de blobopslag](../../import-export/storage-import-export-service.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Schaalbaarheids- en prestatiedoelen voor Blob-opslag](scalability-targets.md)
