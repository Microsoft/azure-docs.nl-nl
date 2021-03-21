---
title: Een Azure NFS-bestands share koppelen-Azure Files
description: Meer informatie over het koppelen van een netwerk bestandssysteem share.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621294"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Een NFS-bestands share koppelen

[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestands shares kunnen worden gekoppeld in Linux-distributies met behulp van het Server Message Block protocol (SMB) of het NFS-protocol (Network File System). Dit artikel is gericht op het koppelen met NFS, Zie [Azure files gebruiken met Linux](storage-how-to-use-files-linux.md)voor meer informatie over het koppelen met SMB. Zie [Azure file share-protocollen](storage-files-compare-protocols.md)voor meer informatie over elk van de beschik bare protocollen.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Vereisten

- [Maak een NFS-share](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > NFS-shares kunnen alleen worden geopend vanuit vertrouwde netwerken. Verbindingen met uw NFS-share moeten afkomstig zijn van een van de volgende bronnen:

- Gebruik een van de volgende netwerk oplossingen:
    - [Maak een persoonlijk eind punt](storage-files-networking-endpoints.md#create-a-private-endpoint) (aanbevolen) of [Beperk de toegang tot uw open bare eind punt](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure files](storage-files-configure-p2s-vpn-linux.md).
    - [Configureer een site-naar-site-VPN voor gebruik met Azure files](storage-files-configure-s2s-vpn.md).
    - [ExpressRoute](../../expressroute/expressroute-introduction.md)configureren.

## <a name="disable-secure-transfer"></a>Beveiligde overdracht uitschakelen

1. Meld u aan bij de Azure Portal en open het opslag account met de NFS-share die u hebt gemaakt.
1. Selecteer **Configuratie**.
1. Selecteer **uitgeschakeld** voor **beveiligde overdracht vereist**.
1. Selecteer **Opslaan**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Scherm afbeelding van de configuratie van het opslag account met beveiligde overdracht is uitgeschakeld.":::

## <a name="mount-an-nfs-share"></a>Een NFS-share koppelen

1. Zodra de bestands share is gemaakt, selecteert u de share en selecteert u **verbinding maken vanuit Linux**.
1. Voer het koppelingspad in dat u wilt gebruiken en kopieer vervolgens het script.
1. Maak verbinding met uw client en gebruik het meegeleverde koppelings script.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Scherm afbeelding van de Blade verbinding met bestands share":::

U hebt nu de NFS-share gekoppeld.

### <a name="validate-connectivity"></a>Connectiviteit valideren

Als uw koppeling is mislukt, is het mogelijk dat uw persoonlijke eind punt niet juist is ingesteld of niet toegankelijk is. Zie de sectie [connectiviteit controleren](storage-files-networking-endpoints.md#verify-connectivity) van het artikel netwerken-eind punten voor meer informatie over het controleren van de verbinding.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Azure Files in ons artikel, [het plannen van een Azure files-implementatie](storage-files-planning.md).
- Zie [problemen met Azure NFS-bestands shares oplossen](storage-troubleshooting-files-nfs.md)als u problemen ondervindt.