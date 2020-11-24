---
title: bestand opnemen
description: bestand opnemen
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 297641bbaccb44739d67fdd26f0c1f64062bba46
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560769"
---
**Azure Data Lake Storage Gen2** is geen accounttype voor toegewezen service of opslag. Het behoort tot de nieuwste mogelijkheden voor de analyse van big data.  Deze mogelijkheden zijn beschikbaar in een opslagaccount voor algemeen gebruik v2 of in een BlockBlobStorage-opslagaccount, en u kunt deze mogelijkheden verkrijgen door de functie **Hiërarchische naamruimte** van het account in te schakelen. Raadpleeg deze artikelen voor schaaldoelen. 

- [Schaaldoelen voor Blob Storage](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage).
- [Schaaldoelen voor standaard opslagaccounts](../articles/storage/common/scalability-targets-standard-account.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage Gen1** is een toegewezen service. Het is een ondernemingsbrede opslagplaats op hyperschaal voor analytische workloads van big data. U kunt Data Lake Storage Gen1 gebruiken om gegevens van elke grootte, elk type en elke opnamesnelheid op één enkele locatie vast te leggen voor operationele en experimentele analyses. Er geldt geen limiet voor de hoeveelheid gegevens die u in een Data Lake Storage Gen1-account kunt opslaan.

| **Resource** | **Limiet** | **Opmerkingen** |
| --- | --- | --- |
| Maximale aantal Data Lake Storage Gen1-accounts per abonnement per regio |10 | Contact opnemen met Ondersteuning om een verhoging van deze limiet aan te vragen. |
| Maximale aantal toegangs-ACL's per bestand of map |32 | Dit is een vaste limiet. Gebruik groepen om de toegang met minder invoeren te beheren. |
| Maximale aantal standaard-ACL's per bestand of map |32 | Dit is een vaste limiet. Gebruik groepen om de toegang met minder invoeren te beheren. |