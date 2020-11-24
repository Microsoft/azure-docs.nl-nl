---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553387"
---
In de volgende tabel staan de standaardlimieten voor Azure algemeen v1, v2, Blob Storage en blok-blob-opslagaccounts. De limiet voor *inkomend verkeer* verwijst naar alle gegevens die naar een opslagaccount worden verzonden. De limiet voor *uitgaand verkeer* verwijst naar alle gegevens die vanaf een opslagaccount worden ontvangen.

> [!NOTE]
> U kunt hogere limieten voor capaciteit en binnenkomend verkeer aanvragen. Neem contact op met [Ondersteuning voor Azure](https://azure.microsoft.com/support/faq/) om een verhoging aan te vragen.

| Resource | Limiet |
| --- | --- |
| Het aantal opslagaccounts per regio per abonnement, inclusief Standard- en Premium-opslagaccounts.| 250 |
| Maximale capaciteit van opslagaccount | 5 PiB <sup>1</sup>|
| Maximum aantal blobcontainers, blobs, bestandsshares, tabellen, wachtrijen, entiteiten of berichten per opslagaccount | Geen limiet |
| Maximum aantal aanvragen<sup>1</sup> per opslagaccount | 20.000 aanvragen per seconde |
| Maximale hoeveelheid inkomend verkeer<sup>1</sup> per opslagaccount (regio's Verenigde Staten en Europa) | 10 Gbps |
| Maximale hoeveelheid inkomend verkeer<sup>1</sup> per opslagaccount (overige regio's) | 5 Gbps als RA-GRS/GRS is ingeschakeld, 10 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale hoeveelheid uitgaand verkeer voor algemeen v2- en Blob Storage-accounts (alle regio's) | 50 Gbps |
| Maximale hoeveelheid uitgaand verkeer voor algemeen v1-accounts (VS-regio's) | 20 Gbps als RA-GRS/GRS is ingeschakeld, 30 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale hoeveelheid uitgaand verkeer voor algemeen v1-accounts (niet-VS-regio's) | 10 Gbps als RA-GRS/GRS is ingeschakeld, 15 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximum aantal virtuele-netwerkregels per opslagaccount | 200 |
| Maximum aantal IP-adresregels per opslagaccount | 200 |

<sup>1</sup> Azure Storage-standaardaccounts ondersteunen hogere capaciteitslimieten en hogere limieten voor inkomend verkeer op aanvraag. Neem contact op met de [Azure-ondersteuning](https://azure.microsoft.com/support/faq/) om een hogere accountlimiet aan te vragen.

<sup>2</sup> Als voor uw opslagaccount leestoegang is ingeschakeld met geografisch redundante opslag (RA-GRS) of geo-zone-redundante opslag (RA-GZRS), zijn de doelen voor uitgaand verkeer voor de secundaire locatie identiek aan die van de primaire locatie. Zie [Azure Storage-replicatie](../articles/storage/common/storage-redundancy.md) voor meer informatie.

> [!NOTE]
> Microsoft raadt aan voor de meeste scenario's een algemeen v2-opslagaccount te gebruiken. U kunt een algemeen v1 of een Azure Blob Storage-account eenvoudig bijwerken naar een algemeen v2-account zonder dat u last hebt van uitval en zonder gegevens te hoeven kopiëren. Zie [Upgraden naar een algemeen v2-opslagaccount](../articles/storage/common/storage-account-upgrade.md) voor meer informatie.

Alle opslagaccounts worden uitgevoerd op een platte netwerktopologie, ongeacht wanneer ze zijn gemaakt. Voor meer informatie over de platte Azure Storage-netwerkarchitectuur en schaalbaarheid raadpleegt u [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) (Microsoft Azure Storage: een maximaal beschikbare cloudopslagservice met grote consistentie).