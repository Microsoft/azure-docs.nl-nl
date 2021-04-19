---
title: Azure HPC Cache vereisten
description: Vereisten voor het gebruik van Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/14/2021
ms.author: v-erkel
ms.openlocfilehash: 3cddbba3dca64561d7e2b7b27715152a26a8c9e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717581"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Vereisten voor Azure HPC Cache

Voordat u de Azure Portal om een nieuwe Azure HPC Cache maken, moet u ervoor zorgen dat uw omgeving aan deze vereisten voldoet.

## <a name="video-overviews"></a>Videooverzichten

Bekijk deze video's voor een kort overzicht van de onderdelen van het systeem en wat ze nodig hebben om samen te werken.

(Klik op de videoafbeelding of de koppeling om te bekijken.)

* [Hoe het werkt:](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) legt uit hoe Azure HPC Cache met opslag en clients communiceert

  [![afbeelding van videominiaturen: Azure HPC Cache: Hoe het werkt (klik om de videopagina te bezoeken)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Vereisten:](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) beschrijft de vereisten voor NAS-opslag, Azure Blob-opslag, netwerktoegang en clienttoegang

  [![miniatuurafbeelding van video: Azure HPC Cache: Vereisten (klik om naar de videopagina te gaan)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Lees de rest van dit artikel voor specifieke aanbevelingen.

## <a name="azure-subscription"></a>Azure-abonnement

Een betaald abonnement wordt aanbevolen.

## <a name="network-infrastructure"></a>Netwerkinfrastructuur

Er moeten twee netwerkgerelateerde vereisten worden ingesteld voordat u uw cache kunt gebruiken:

* Een toegewezen subnet voor het Azure HPC Cache exemplaar
* DNS-ondersteuning zodat de cache toegang heeft tot opslag en andere resources

### <a name="cache-subnet"></a>Cachesubnet

De Azure HPC Cache heeft een toegewezen subnet met de volgende eigenschappen nodig:

* Het subnet moet ten minste 64 IP-adressen beschikbaar hebben.
* Het subnet kan geen andere VM's hosten, zelfs niet voor gerelateerde services zoals clientmachines.
* Als u meerdere Azure HPC Cache gebruikt, heeft elk exemplaar een eigen subnet nodig.

De best practice is het maken van een nieuw subnet voor elke cache. U kunt een nieuw virtueel netwerk en subnet maken als onderdeel van het maken van de cache.

### <a name="dns-access"></a>DNS-toegang

De cache heeft DNS nodig voor toegang tot resources buiten het virtuele netwerk. Afhankelijk van de resources die u gebruikt, moet u mogelijk een aangepaste DNS-server instellen en doorsturen configureren tussen die server en de Azure DNS servers:

* Voor toegang tot Azure Blob Storage-eindpunten en andere interne resources hebt u de op Azure gebaseerde DNS-server nodig.
* Voor toegang tot on-premises opslag moet u een aangepaste DNS-server configureren die uw opslaghostnamen kan oplossen. U moet dit doen voordat u de cache maakt.

Als u alleen Blob Storage gebruikt, kunt u de standaard door Azure geleverde DNS-server gebruiken voor uw cache. Als u echter toegang nodig hebt tot opslag of andere resources buiten Azure, moet u een aangepaste DNS-server maken en deze configureren voor het doorsturen van Azure-specifieke oplossingsaanvragen naar de Azure DNS server.

Als u een aangepaste DNS-server wilt gebruiken, moet u deze installatiestappen volgen voordat u de cache maakt:

* Maak het virtuele netwerk dat als host voor de Azure HPC Cache.
* Maak de DNS-server.
* Voeg de DNS-server toe aan het virtuele netwerk van de cache.

  Volg deze stappen om de DNS-server toe te voegen aan het virtuele netwerk in Azure Portal:

  1. Open het virtuele netwerk in het Azure Portal.
  1. Kies DNS-servers in het menu Instellingen in de zijbalk.
  1. Selecteer Aangepast
  1. Voer het IP-adres van de DNS-server in het veld in.

Een eenvoudige DNS-server kan ook worden gebruikt voor het laden van clientverbindingen tussen alle beschikbare cache-koppelingspunten.

Meer informatie over virtuele Azure-netwerken en DNS-serverconfiguraties in [Naamresolutie voor resources in virtuele Azure-netwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Machtigingen

Controleer deze machtigingsgerelateerde vereisten voordat u begint met het maken van uw cache.

* Het cache-exemplaar moet virtuele netwerkinterfaces (NIC's) kunnen maken. De gebruiker die de cache maakt, moet voldoende bevoegdheden hebben in het abonnement om NIC's te maken.

* Als u Blob Storage gebruikt, Azure HPC Cache autorisatie nodig voor toegang tot uw opslagaccount. Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om de cache toegang te geven tot uw Blob-opslag. Er zijn twee rollen vereist: Inzender voor opslagaccounts en Inzender voor opslagblobgegevens.

  Volg de instructies in [Opslagdoelen toevoegen om](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) de rollen toe te voegen.

## <a name="storage-infrastructure"></a>Opslaginfrastructuur
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

De cache biedt ondersteuning voor Azure Blob-containers, export van NFS-hardwareopslag en aan NFS-adls-blobcontainers (momenteel in preview). Voeg opslagdoelen toe nadat u de cache hebt aanmaken.

Elk opslagtype heeft specifieke vereisten.

### <a name="blob-storage-requirements"></a>Vereisten voor Blob Storage

Als u Azure Blob Storage wilt gebruiken met uw cache, hebt u een compatibel opslagaccount nodig en een lege Blob-container of een container die is gevuld met Azure HPC Cache opgemaakte gegevens, zoals beschreven in Gegevens verplaatsen naar [Azure Blob Storage.](hpc-cache-ingest.md)

> [!NOTE]
> Er gelden verschillende vereisten voor aan NFS-mounted blobopslag. Lees [ADLS-NFS-opslagvereisten](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) voor meer informatie.

Maak het account voordat u een opslagdoel probeert toe te voegen. U kunt een nieuwe container maken wanneer u het doel toevoegt.

Gebruik een van de volgende combinaties om een compatibel opslagaccount te maken:

| Prestaties | Type | Replicatie | Toegangslaag |
|--|--|--|--|
| Standard | StorageV2 (algemeen v2)| Lokaal redundante opslag (LRS) of zone-redundante opslag (ZRS) | Dynamisch |
| Premium | Blok-blobs | Lokaal redundante opslag (LRS) | Dynamisch |

Het opslagaccount moet toegankelijk zijn vanuit het privésubnet van uw cache. Als uw account gebruikmaakt van een privé-eindpunt of een openbaar eindpunt dat is beperkt tot specifieke virtuele netwerken, moet u ervoor zorgen dat u toegang vanuit het subnet van de cache inschakelen. (Een open openbaar eindpunt wordt niet aanbevolen.)

Het is een goed idee om een opslagaccount te gebruiken in dezelfde Azure-regio als uw cache.

U moet de cachetoepassing ook toegang geven tot uw Azure-opslagaccount, zoals vermeld in [Machtigingen](#permissions)hierboven. Volg de procedure in [Opslagdoelen toevoegen om](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) de cache de vereiste toegangsrollen te geven. Als u niet de eigenaar van het opslagaccount bent, laat u de eigenaar deze stap doen.

### <a name="nfs-storage-requirements"></a>NFS-opslagvereisten
<!-- linked from configuration.md -->

Als u een NFS-opslagsysteem gebruikt (bijvoorbeeld een on-premises hardware-NAS-systeem), moet u ervoor zorgen dat het aan deze vereisten voldoet. Mogelijk moet u samenwerken met de netwerkbeheerders of firewallbeheerders voor uw opslagsysteem (of datacenter) om deze instellingen te controleren.

> [!NOTE]
> Het maken van het opslagdoel mislukt als de cache onvoldoende toegang heeft tot het NFS-opslagsysteem.

Meer informatie vindt u in [Problemen met NAS-configuratie en NFS-opslagdoel oplossen.](troubleshoot-nas.md)

* Netwerkconnectiviteit: de Azure HPC Cache netwerktoegang met hoge bandbreedte nodig tussen het cachesubnet en het datacenter van het NFS-systeem. [ExpressRoute](../expressroute/index.yml) of vergelijkbare toegang wordt aanbevolen. Als u een VPN gebruikt, moet u deze mogelijk zo configureren dat TCP MSS op 1350 wordt vastgemaakt om ervoor te zorgen dat grote pakketten niet worden geblokkeerd. Lees [Beperkingen voor de grootte van VPN-pakketten voor](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) meer informatie over het oplossen van problemen met VPN-instellingen.

* Poorttoegang: de cache moet toegang hebben tot specifieke TCP/UDP-poorten op uw opslagsysteem. Verschillende typen opslag hebben verschillende poortvereisten.

  Volg deze procedure om de instellingen van uw opslagsysteem te controleren.

  * Voer een `rpcinfo` opdracht uit aan uw opslagsysteem om de benodigde poorten te controleren. Met de onderstaande opdracht worden de poorten weergegeven en worden de relevante resultaten in een tabel opgemaakt. (Gebruik het IP-adres van uw systeem in plaats van de *<storage_IP>* term.)

    U kunt deze opdracht uitvoeren vanaf elke Linux-client met een NFS-infrastructuur geïnstalleerd. Als u een client in het clustersubnet gebruikt, kan dit ook helpen om de connectiviteit tussen het subnet en het opslagsysteem te controleren.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Zorg ervoor dat alle poorten die door de query worden geretourneerd onbeperkt verkeer van het ``rpcinfo`` Azure HPC Cache subnet van de query toestaan.

  * Als u de opdracht niet kunt gebruiken, moet u ervoor zorgen dat deze veelgebruikte `rpcinfo` poorten inkomende en uitgaande verkeer toestaan:

    | Protocol | Poort  | Service  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | is bevestigd   |
    | TCP/UDP  | 4047  | status   |

    Sommige systemen gebruiken verschillende poortnummers voor deze services. Raadpleeg de documentatie van uw opslagsysteem om er zeker van te zijn.

  * Controleer de firewallinstellingen om er zeker van te zijn dat deze verkeer op al deze vereiste poorten toestaan. Controleer de firewalls die worden gebruikt in Azure en on-premises firewalls in uw datacenter.

* Toegang tot de hoofdmap (lezen/schrijven): de cache maakt verbinding met het back-endsysteem als gebruikers-id 0. Controleer deze instellingen op uw opslagsysteem:
  
  * Schakel `no_root_squash` in. Deze optie zorgt ervoor dat de externe hoofdgebruiker toegang heeft tot bestanden die eigendom zijn van de hoofdmap.

  * Controleer het exportbeleid om ervoor te zorgen dat deze geen beperkingen bevatten voor basistoegang vanuit het subnet van de cache.

  * Als uw opslag exporten heeft die subdirectorieën van een andere export zijn, moet u ervoor zorgen dat de cache hoofdtoegang heeft tot het laagste segment van het pad. Lees [Hoofdtoegang voor mappaden](troubleshoot-nas.md#allow-root-access-on-directory-paths) in het artikel Probleemoplossing voor NFS-opslagdoel voor meer informatie.

* NFS-back-endopslag moet een compatibel hardware-/softwareplatform zijn. Neem contact op Azure HPC Cache het team voor meer informatie.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Opslagvereisten voor aan NFS-mounted blob (ADLS-NFS) (PREVIEW)

Azure HPC Cache kan ook een blobcontainer gebruiken die is bevestigd met het NFS-protocol als opslagdoel.

> [!NOTE]
> Ondersteuning voor het NFS 3.0-protocol voor Azure Blob Storage is in openbare preview. Beschikbaarheid is beperkt en functies kunnen veranderen tussen nu en wanneer de functie algemeen beschikbaar komt. Gebruik geen preview-technologie in productiesystemen.
>
> Lees meer over deze preview-functie in [NFS 3.0-protocolondersteuning in Azure Blob Storage](../storage/blobs/network-file-system-protocol-support.md).

De vereisten voor opslagaccounts verschillen voor een ADLS-NFS-blobopslagdoel en voor een standaard blob-opslagdoel. Volg de instructies in Blob-opslag mount [by using the Network File System (NFS) 3.0 protocol zorgvuldig](../storage/blobs/network-file-system-protocol-support-how-to.md) te maken en configureren van het NFS-opslagaccount.

Dit is een algemeen overzicht van de stappen. Deze stappen kunnen worden gewijzigd. Raadpleeg daarom altijd de [ADLS-NFS-instructies](../storage/blobs/network-file-system-protocol-support-how-to.md) voor de huidige details.

1. Zorg ervoor dat de functies die u nodig hebt, beschikbaar zijn in de regio's waar u wilt werken.

1. Schakel de NFS-protocolfunctie voor uw abonnement in. Doe dit *voordat u* het opslagaccount maakt.

1. Maak een beveiligd virtueel netwerk (VNet) voor het opslagaccount. Gebruik hetzelfde virtuele netwerk voor uw NFS-opslagaccount en voor uw Azure HPC Cache. (Gebruik niet hetzelfde subnet als de cache.)

1. Maak het opslagaccount.

   * In plaats van de opslagaccountinstellingen te gebruiken voor een standaard [blob-opslagaccount,](../storage/blobs/network-file-system-protocol-support-how-to.md)volgt u de instructies in het document . Het type opslagaccount dat wordt ondersteund, kan per Azure-regio verschillen.

   * Kies in de sectie Netwerken een privé-eindpunt in het beveiligde virtuele netwerk dat u hebt gemaakt (aanbevolen) of kies een openbaar eindpunt met beperkte toegang vanaf het beveiligde VNet.

   * Vergeet niet om de sectie Geavanceerd te voltooien, waarin u NFS-toegang inschakelen.

   * Geef de cachetoepassing toegang tot uw Azure-opslagaccount, zoals vermeld in [Machtigingen](#permissions)hierboven. U kunt dit doen wanneer u voor het eerst een opslagdoel maakt. Volg de procedure in [Opslagdoelen toevoegen om](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) de cache de vereiste toegangsrollen te geven.

     Als u niet de eigenaar van het opslagaccount bent, laat u de eigenaar deze stap doen.

Meer informatie over het gebruik van ADLS-NFS-opslagdoelen met Azure HPC Cache in [Use NFS-mounted blob storage with Azure HPC Cache](nfs-blob-considerations.md).

## <a name="set-up-azure-cli-access-optional"></a>Azure CLI-toegang instellen (optioneel)

Als u een Azure HPC Cache wilt maken of beheren vanuit de Azure-opdrachtregelinterface (Azure CLI), moet u de CLI-software en de hpc-cache-extensie installeren. Volg de instructies in [Azure CLI instellen voor Azure HPC Cache.](az-cli-prerequisites.md)

## <a name="next-steps"></a>Volgende stappen

* [Een Azure HPC Cache maken op](hpc-cache-create.md) de Azure Portal
