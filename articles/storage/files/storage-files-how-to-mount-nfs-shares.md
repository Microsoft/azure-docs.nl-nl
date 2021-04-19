---
title: Een Azure NFS-bestands delen - Azure Files
description: Meer informatie over het toevoegen van een network file system-share.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4369619cd83dffe36cf156f523a951e1360438db
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717067"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Een NFS-bestands share toevoegen

[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestands shares kunnen worden aangesloten in Linux-distributies met behulp van het Server Message Block-protocol (SMB) of het NFS-protocol (Network File System). Dit artikel is gericht op het maken van een NFS-toepassing. Zie Use Azure Files with Linux (Een SMB gebruiken met [SMB) voor](storage-how-to-use-files-linux.md)meer informatie over het maken van een Azure Files met Linux. Zie Protocollen voor Azure-bestands delen voor meer informatie over [elk van de beschikbare protocollen.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Beperkingen

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Vereisten

- [Maak een NFS-share.](storage-files-how-to-create-nfs-shares.md)

    > [!IMPORTANT]
    > NFS-shares zijn alleen toegankelijk vanuit vertrouwde netwerken. Verbindingen met uw NFS-share moeten afkomstig zijn van een van de volgende bronnen:

- Gebruik een van de volgende netwerkoplossingen:
    - Maak [een privé-eindpunt (aanbevolen)](storage-files-networking-endpoints.md#create-a-private-endpoint) of [beperk de toegang tot uw openbare eindpunt.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Configureer een punt-naar-site-VPN (P2S) in Linux voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Configureer een site-naar-site-VPN voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md).
    - Configureer [ExpressRoute.](../../expressroute/expressroute-introduction.md)

## <a name="disable-secure-transfer"></a>Veilige overdracht uitschakelen

1. Meld u aan bij Azure Portal toegang tot het opslagaccount met de NFS-share die u hebt gemaakt.
1. Selecteer **Configuratie**.
1. Selecteer **Uitgeschakeld bij** Veilige overdracht **vereist.**
1. Selecteer **Opslaan**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Schermopname van het configuratiescherm van het opslagaccount met beveiligde overdracht uitgeschakeld.":::

## <a name="mount-an-nfs-share"></a>Een NFS-share monteren

1. Zodra de bestands share is gemaakt, selecteert u de share en selecteert **u Verbinding maken vanuit Linux.**
1. Voer het pad in dat u wilt gebruiken en kopieer het script.
1. Maak verbinding met uw client en gebruik het opgegeven koppelscript.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Schermopname van de blade Verbinding maken met bestands share.":::

U hebt nu uw NFS-share bevestigd.

### <a name="validate-connectivity"></a>Connectiviteit valideren

Als de bevestiging is mislukt, is het mogelijk dat uw privé-eindpunt niet juist is ingesteld of niet toegankelijk is. Zie de sectie Connectiviteit controleren van het artikel Netwerk eindpunten voor [meer](storage-files-networking-endpoints.md#verify-connectivity) informatie over het bevestigen van connectiviteit.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over Azure Files in ons artikel Planning for an Azure Files deployment (Planning voor een [Azure Files implementatie).](storage-files-planning.md)
- Zie Problemen met [Azure NFS-bestands](storage-troubleshooting-files-nfs.md)shares oplossen als u problemen ervaart.