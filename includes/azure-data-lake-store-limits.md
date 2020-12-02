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
ms.openlocfilehash: 7098f23e9b5b6f56fbbe761335afc65375aea680
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509124"
---
**Azure Data Lake Storage Gen2** is geen accounttype voor toegewezen service of opslag. Het behoort tot de nieuwste mogelijkheden voor de analyse van big data.  Deze mogelijkheden zijn beschikbaar in een opslagaccount voor algemeen gebruik v2 of in een BlockBlobStorage-opslagaccount, en u kunt deze mogelijkheden verkrijgen door de functie **Hiërarchische naamruimte** van het account in te schakelen. Raadpleeg deze artikelen voor schaaldoelen. 

- [Schaaldoelen voor Blob Storage](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage).
- [Schaaldoelen voor standaard opslagaccounts](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage Gen1** is een toegewezen service. Het is een ondernemingsbrede opslagplaats op hyperschaal voor analytische workloads van big data. U kunt Data Lake Storage Gen1 gebruiken om gegevens van elke grootte, elk type en elke opnamesnelheid op één enkele locatie vast te leggen voor operationele en experimentele analyses. Er geldt geen limiet voor de hoeveelheid gegevens die u in een Data Lake Storage Gen1-account kunt opslaan.

| **Resource** | **Limiet** | **Opmerkingen** |
| --- | --- | --- |
| Maximale aantal Data Lake Storage Gen1-accounts per abonnement per regio |10 | Contact opnemen met Ondersteuning om een verhoging van deze limiet aan te vragen. |
| Maximale aantal toegangs-ACL's per bestand of map |32 | Dit is een vaste limiet. Gebruik groepen om de toegang met minder invoeren te beheren. |
| Maximale aantal standaard-ACL's per bestand of map |32 | Dit is een vaste limiet. Gebruik groepen om de toegang met minder invoeren te beheren. |