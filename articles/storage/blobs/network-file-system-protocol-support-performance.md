---
title: Prestatie overwegingen voor NFS 3,0 in Azure Blob-opslag (preview) | Microsoft Docs
description: Optimaliseer de prestaties van uw NFS (Network File System) 3,0-opslag aanvragen met behulp van de aanbevelingen in dit artikel.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: de511fa30caa608c2dc87b6c0ba166ed56ff9499
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490179"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Prestatie overwegingen voor NFS (Network File System) 3,0 in Azure Blob-opslag (preview-versie)

Blob-opslag ondersteunt nu het NFS-protocol (Network File System) 3,0. Dit artikel bevat aanbevelingen waarmee u de prestaties van uw opslag aanvragen kunt optimaliseren. Zie [Network File System (NFS) 3,0-protocol ondersteuning in Azure Blob-opslag (preview)](network-file-system-protocol-support.md)voor meer informatie over de ondersteuning van NFS 3,0 in Azure Blob Storage.

> [!NOTE]
> Ondersteuning voor NFS 3,0-protocol in Azure Blob-opslag is in open bare preview. Het biedt ondersteuning voor GPV2-opslag accounts met de prestaties van de Standard-laag in de volgende regio's: Australië-oost, Korea-centraal, VS-Oost en Zuid-Centraal vs. De preview biedt ook ondersteuning voor blok-blobs met een Premium-prestatie niveau in alle open bare regio's.

## <a name="add-clients-to-increase-throughput"></a>Clients toevoegen om de door voer te verhogen 

Azure Blob Storage lineair geschaald totdat het maximum aantal uitgangs-en ingangs limiet van het opslag account is bereikt. Uw toepassingen kunnen daarom een hogere door Voer bezorgen door meer clients te gebruiken.  Zie [schaalbaarheids-en prestatie doelen voor standaard opslag accounts](../common/scalability-targets-standard-account.md)voor het weer geven van uituitgangs-en ingangs limieten voor opslag accounts.

In het volgende diagram ziet u hoe de band breedte toeneemt wanneer u meer clients toevoegt. In deze grafiek wordt een-client een virtuele machine (VM) en het account gebruikt de laag standaard prestatie. 

> [!div class="mx-imgBorder"]
> ![Standaard prestaties](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

In het volgende diagram ziet u hetzelfde effect wanneer dit wordt toegepast op een account dat gebruikmaakt van de Premium-prestatie-laag.

> [!div class="mx-imgBorder"]
> ![Premium-prestaties](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>De Premium-prestatie-laag gebruiken voor grootschalige toepassingen

Niet alle toepassingen kunnen worden geschaald door meer clients toe te voegen. Voor deze toepassingen biedt [Azure Premium Block Blob Storage-account](storage-blob-create-account-block-blob.md) consistente lage latentie en hoge transactie tarieven. De Premium-opslag account voor blok-blobs kan maximale band breedte bereiken met minder threads en clients. Met één client kan een Premium-opslag account voor blok-BLOB bijvoorbeeld **2,3 x** -band breedte krijgen, vergeleken met dezelfde configuratie die wordt gebruikt met een standaard prestatie-v2-opslag account voor algemeen gebruik. 

Elke balk in het volgende diagram toont het verschil in bereikte band breedte tussen Premium-en Standard-prestatie opslag accounts. Naarmate het aantal clients toeneemt, neemt dat verschil af.  

> [!div class="mx-imgBorder"]
> ![Relatieve prestaties](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Vermijd veelvuldige overschrijvingen voor gegevens

Het duurt langer voordat een nieuwe schrijf bewerking is voltooid. De reden hiervoor is dat een NFS-overschrijvings bewerking, met name een gedeeltelijke in-place bestands bewerking, een combi natie is van een aantal onderliggende BLOB-bewerkingen: een lees-, wijzigings-en schrijf bewerking. Daarom is een toepassing die veelvuldige bewerkingen in de locatie vereist, niet geschikt voor Blob Storage-accounts die zijn ingeschakeld voor NFS. 

## <a name="other-best-practice-recommendations"></a>Andere best practice aanbevelingen 

- Gebruik Vm's met voldoende netwerk bandbreedte.

- Gebruik meerdere koppel punten wanneer uw workloads dat doen.

- Gebruik zoveel mogelijk threads.

- Grote blok grootten gebruiken.

- Maak opslag aanvragen van een-client die zich in dezelfde regio bevindt als het opslag account. Dit kan netwerk latentie verbeteren.

## <a name="next-steps"></a>Volgende stappen

- Zie [Network File System (NFS) 3,0-protocol ondersteuning in Azure Blob-opslag (preview)](network-file-system-protocol-support.md)voor meer informatie over de ondersteuning van NFS 3,0 in Azure Blob Storage.

- Zie [Blob Storage koppelen met behulp van het NFS-protocol (Network File System) 3,0 (preview)](network-file-system-protocol-support-how-to.md)om aan de slag te gaan.
