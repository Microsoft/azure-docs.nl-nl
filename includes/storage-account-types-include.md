---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b8a72640ebe8daffedb196456df7d40bc41b7b1b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557963"
---
Azure Storage biedt verschillende soorten opslagaccounts. Elk type ondersteunt verschillende functies en heeft een eigen prijsmodel. Neem deze verschillen in overweging voordat u een opslagaccount maakt om het type account te kiezen dat het best bij uw toepassingen past. De typen opslagaccounts zijn:

- **Algemeen v2-accounts**: Eenvoudig opslagaccounttype voor blobs, bestanden, wachtrijen en tabellen. Aanbevolen voor de meeste scenario's die gebruikmaken van Azure Storage.
- **Algemeen v1-accounts**: Verouderd accounttype voor blobs, bestanden, wachtrijen en tabellen. Gebruik, indien mogelijk, algemeen v2-accounts.
- **BlockBlobStorage-accounts**: Opslagaccounts met Premium-prestatiekenmerken voor blok-blobs en toevoeg-blobs. Aanbevolen voor scenario's met hoge transactiesnelheden of scenario's die kleinere objecten gebruiken of een consistente lage opslaglatentie vereisen.
- **FileStorage-accounts**: Opslagaccounts voor alleen bestanden met Premium-prestatie kenmerken. Aanbevolen voor toepassingen voor ondernemingen of high performance-prestaties.
- **BlobStorage-accounts**: Verouderde opslagaccounts voor alleen blobs. Gebruik, indien mogelijk, algemeen v2-accounts.

In de volgende tabel worden de typen opslagaccounts en de mogelijkheden ervan beschreven:

| Type opslagaccount | Ondersteunde services                       | Ondersteunde prestatielagen      | Ondersteunde toegangslagen         | Replicatieopties               | Implementatiemodel<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Versleuteling<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Algemeen v2   | Blob, bestand, wachtrij, tabel, schijf en Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Dynamisch, statisch en archiefopslag<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (preview), RA-GZRS (preview)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Versleuteld              |
| Algemeen v1   | Blob, bestand, wachtrij, tabel en schijf       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N.v.t.                            | LRS, GRS, RA-GRS                  | Resource Manager, klassiek    | Versleuteld              |
| BlockBlobStorage   | Blob (alleen blok-blobs en toevoeg-blobs) | Premium                       | N.v.t.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Versleuteld              |
| FileStorage   | Alleen bestanden | Premium                       | N.v.t.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Versleuteld              |
| BlobStorage         | Blob (alleen blok-blobs en toevoeg-blobs) | Standard                      | Dynamisch, statisch en archiefopslag<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Versleuteld              |

<div id="deployment-model"><sup>1</sup>Het wordt aanbevolen het Azure Resource Manager-implementatiemodel te gebruiken. Opslagaccounts die gebruikmaken van het klassieke implementatiemodel, kunnen in sommige locaties nog steeds worden gemaakt; bestaande klassieke accounts blijven ondersteund worden. Zie voor meer informatie <a href="/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager versus klassieke implementatie: inzicht in implementatiemodellen en de status van uw resources</a>.</div><br/>

<div id="encryption"><sup>2</sup>Alle opslagaccounts worden versleuteld met behulp van SSE (Storage Service Encryption) voor data-at-rest. Zie <a href="/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption voor data-at-rest</a>.</div><br/>

<div id="archive"><sup>3</sup>Archiefopslag en laaginstelling op blobniveau ondersteunen alleen blok-blobs. De archieflaag is alleen beschikbaar op het niveau van een afzonderlijke blob, niet op opslagaccountniveau. Zie voor meer informatie <a href="/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: Dynamische, statische en archiefopslaglagen</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>Zone-redundante opslag (ZRS) en geo-zone-redundante opslag (GZRS/RA-GZRS) (preview) zijn alleen beschikbaar voor Standard algemeen v2-, BlockBlobStorage- en FileStorage-accounts in bepaalde regio's. Zie <a href="/azure/storage/common/storage-redundancy">Azure Storage-redundantie</a> voor meer informatie over opties voor Azure Storage-redundantie.</div><br/>

<div id="premium-performance"><sup>5</sup>Premium-prestaties voor algemeen v2- en algemeen v1-accounts is alleen beschikbaar voor schijf- en pagina-blobs. Premium-prestaties voor blok- en toevoeg-blobs zijn alleen beschikbaar voor BlockBlobStorage-accounts. Premium-prestaties voor bestanden zijn alleen beschikbaar voor FileStorage-accounts.</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure Data Lake Storage Gen2 is een reeks mogelijkheden die is toegewezen aan analyse van big data, gebouwd op Azure Blob Storage. Data Lake Storage Gen2 wordt alleen ondersteund voor algemeen v2-opslagaccounts waarvoor hiërarchische naamruimte is ingeschakeld. Zie <a href="/azure/storage/blobs/data-lake-storage-introduction">Inleiding tot Azure Data Lake Storage Gen2</a> voor meer informatie over Data Lake Storage Gen2.</div>