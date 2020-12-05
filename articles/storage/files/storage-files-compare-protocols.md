---
title: 'Beschik bare Azure Files protocollen: NFS en SMB'
description: Meer informatie over de beschik bare protocollen voordat u een Azure-bestands share maakt, waaronder Server Message Block (SMB) en Network File System (NFS).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 288d1740c1c4c67dd8756de5fc0652966ae89488
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620971"
---
# <a name="azure-file-share-protocols"></a>Protocollen voor Azure-bestands shares

Azure Files biedt twee protocollen voor het koppelen en koppelen van uw Azure-bestands shares. [SMB-protocol (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) en [NFS-protocol (Network File System](https://en.wikipedia.org/wiki/Network_File_System) ) (preview). Azure Files biedt momenteel geen ondersteuning voor multi-protocol toegang, zodat een share alleen een NFS-share of een SMB-share kan zijn. Daarom is het raadzaam om te bepalen welk protocol het beste aansluit bij uw behoeften voordat u Azure-bestands shares maakt.

## <a name="differences-at-a-glance"></a>Verschillen in een oogopslag

|Functie  |NFS (preview-versie)  |SMB  |
|---------|---------|---------|
|Toegangs protocollen     |NFS 4,1         |SMB 2,1, SMB 3,0         |
|Ondersteund besturings systeem     |Linux-kernel versie 4.3 +         |Windows 2008 R2 +, Linux-kernel versie 4.11 +         |
|[Beschikbare lagen](storage-files-planning.md#storage-tiers)     |Premium Storage         |Premium-opslag, geoptimaliseerde trans actie, hot, cool         |
|[Redundantie](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Verificatie     |Alleen voor de host gebaseerde verificatie        |Verificatie op basis van identiteit, verificatie op basis van een gebruiker         |
|Machtigingen     |UNIX-stijl machtigingen         |NTFS-machtigingen         |
|Bestandssysteem semantiek     |POSIX-compatibel         |Niet POSIX-compatibel         |
|Hoofdletter gevoeligheid     |Hoofdlettergevoelig         |Niet hoofdletter gevoelig         |
|Ondersteuning voor vaste koppelingen     |Ondersteund         |Niet ondersteund         |
|Ondersteuning voor symbolische koppelingen     |Ondersteund         |Niet ondersteund         |
|Geopende bestanden verwijderen of wijzigen     |Ondersteund         |Niet ondersteund         |
|Vergrendelen     |Bereik voor advies netwerk vergrendeling op byte gebied         |Ondersteund         |
|Veilige vermelding van openbaar IP-adres | Niet ondersteund | Ondersteund|
|Protocol-Interop| Niet ondersteund | FileREST|

## <a name="nfs-shares-preview"></a>NFS-shares (preview-versie)

Het koppelen van Azure-bestands shares met NFS 4,1 is momenteel beschikbaar als preview-versie. Het biedt een nauwere integratie met Linux. Dit is een volledig POSIX-compatibel aanbod dat een standaard is voor verschillende varianten van UNIX en andere besturings systemen op basis van * nix. Deze service voor bestands opslag op ondernemings niveau wordt geschaald om aan uw opslag behoeften te voldoen en kan gelijktijdig worden geopend door duizenden reken instanties.

### <a name="limitations"></a>Beperkingen

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Best passend

NFS met Azure Files is ideaal voor:

- Werk belastingen waarvoor POSIX-compatibele bestands shares, hoofdletter gevoeligheid of UNIX-stijl machtigingen (UID/GID) zijn vereist.
- Linux-werk belastingen waarvoor geen Windows-toegang is vereist.

### <a name="security"></a>Beveiliging

Alle Azure Files gegevens worden op rest versleuteld. Voor versleuteling in transit biedt Azure een laag code ring voor alle gegevens die onderweg zijn tussen Azure-data centers met behulp van [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). Hierdoor bestaat er versleuteling wanneer gegevens worden overgebracht tussen Azure-data centers. In tegens telling tot Azure Files het SMB-protocol gebruikt, bieden bestands shares met het NFS-protocol geen verificatie op basis van een gebruiker. Verificatie voor NFS-shares is gebaseerd op de geconfigureerde netwerk beveiligings regels. Daarom moet u service-eind punten of particuliere eind punten gebruiken om ervoor te zorgen dat er alleen beveiligde verbindingen tot stand worden gebracht met de NFS-share. Als u toegang wilt krijgen tot shares van on-premises, moet u naast een persoonlijk eind punt een VPN-of ExpressRoute instellen. Aanvragen die niet afkomstig zijn uit de volgende bronnen, worden geweigerd:

- [Een persoonlijk eind punt](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [Een punt-naar-site-VPN (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [Site-to-Site](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Een beperkt openbaar eind punt](storage-files-networking-overview.md#storage-account-firewall-settings)

Zie [Azure files Networking-overwegingen](storage-files-networking-overview.md)voor meer informatie over de beschik bare netwerk opties.

## <a name="smb-shares"></a>SMB-shares

Azure-bestands shares die zijn gekoppeld aan SMB bieden meer Azure Files functies en hebben geen beperkingen voor Azure Files functies, omdat deze algemeen beschikbaar is.

### <a name="features"></a>Functies

- Azure-bestands synchronisatie
- Verificatie op basis van identiteit
- Ondersteuning voor Azure Backup
- Momentopnamen
- Voorlopig verwijderen
- Versleuteling-in-transit en versleuteling-at-rest

### <a name="best-suited"></a>Best passend

SMB met Azure Files is ideaal voor:

- Productie omgevingen
- Klanten die behoefte hebben aan een van de functies die worden vermeld in- [functies](#features)

## <a name="next-steps"></a>Volgende stappen

- [Een NFS-bestands share maken](storage-files-how-to-create-nfs-shares.md)
- [Een SMB-bestandsshare maken](storage-how-to-create-file-share.md)
