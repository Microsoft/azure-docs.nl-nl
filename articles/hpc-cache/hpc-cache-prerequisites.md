---
title: Vereisten voor de Azure HPC-cache
description: Vereisten voor het gebruik van Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 5ac0f0677be6b641d496a941c5a8e1343fd017bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562555"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Vereisten voor de Azure HPC-cache

Voordat u de Azure Portal gebruikt om een nieuwe Azure HPC-cache te maken, moet u ervoor zorgen dat uw omgeving aan deze vereisten voldoet.

## <a name="video-overviews"></a>Video overzichten

Bekijk deze Video's voor een snel overzicht van de onderdelen van het systeem en wat ze nodig hebben om samen te werken.

(Klik op de video afbeelding of op de koppeling om te kijken.)

* [Hoe werkt het](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) ? in dit artikel wordt uitgelegd hoe Azure HPC cache communiceert met opslag en clients

  [![Video miniatuur afbeelding: Azure HPC cache: Hoe werkt het? (Klik hier om de video pagina te bezoeken)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Vereisten: hierin](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) worden de vereisten voor NAS-opslag, Azure Blob-opslag, netwerk toegang en client toegang beschreven

  [![afbeelding van video miniatuur: Azure HPC-cache: vereisten (Klik om de video-pagina te bezoeken)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Lees de rest van dit artikel voor specifieke aanbevelingen.

## <a name="azure-subscription"></a>Azure-abonnement

Een betaald abonnement wordt aanbevolen.

## <a name="network-infrastructure"></a>Netwerkinfrastructuur

Er moeten twee aan het netwerk gerelateerde vereisten worden ingesteld voordat u uw cache kunt gebruiken:

* Een toegewezen subnet voor het Azure HPC-cache-exemplaar
* DNS-ondersteuning zodat de cache toegang kan krijgen tot opslag en andere bronnen

### <a name="cache-subnet"></a>Cache-subnet

Voor de Azure HPC-cache is een toegewezen subnet met de volgende kwaliteiten vereist:

* Voor het subnet moeten ten minste 64 IP-adressen beschikbaar zijn.
* Het subnet kan geen andere Vm's hosten, zelfs voor gerelateerde services zoals client machines.
* Als u meerdere Azure HPC-cache-exemplaren gebruikt, heeft elk een eigen subnet nodig.

De best practice is het maken van een nieuw subnet voor elke cache. U kunt een nieuw virtueel netwerk en subnet maken als onderdeel van het maken van de cache.

### <a name="dns-access"></a>DNS-toegang

De cache heeft DNS nodig om toegang te krijgen tot bronnen buiten het virtuele netwerk. Afhankelijk van de bronnen die u gebruikt, moet u mogelijk een aangepaste DNS-server instellen en door sturen configureren tussen die server en Azure DNS servers:

* Als u toegang wilt krijgen tot Azure Blob Storage-eind punten en andere interne resources, hebt u de op Azure gebaseerde DNS-server nodig.
* Voor toegang tot on-premises opslag moet u een aangepaste DNS-server configureren die uw opslag hostnamen kan omzetten. U moet dit doen **voordat** u de cache maakt.

Als u alleen Blob-opslag gebruikt, kunt u de standaard-DNS-server van Azure gebruiken voor uw cache. Als u echter toegang nodig hebt tot opslag of andere bronnen buiten Azure, moet u een aangepaste DNS-server maken en deze configureren voor het door sturen van eventuele aanvragen voor specifieke oplossingen van Azure naar de Azure DNS-server.

Als u een aangepaste DNS-server wilt gebruiken, moet u deze installatie stappen uitvoeren voordat u de cache maakt:

* Maak het virtuele netwerk dat als host moet fungeren voor de Azure HPC-cache.
* Maak de DNS-server.
* Voeg de DNS-server toe aan het virtuele netwerk van de cache.

  Voer de volgende stappen uit om de DNS-server toe te voegen aan het virtuele netwerk in de Azure Portal:

  1. Open het virtuele netwerk in de Azure Portal.
  1. Kies **DNS-servers** in het menu **instellingen** in de zijbalk.
  1. **Aangepaste** selecteren
  1. Voer het IP-adres van de DNS-server in het veld in.

Een eenvoudige DNS-server kan ook worden gebruikt voor het verdelen van client verbindingen tussen alle beschik bare cache koppel punten.

Meer informatie over Azure Virtual Networks en DNS-server configuraties in [naam omzetting voor bronnen in azure Virtual Networks](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Machtigingen

Controleer deze aan de machtigingen gerelateerde vereisten voordat u begint met het maken van de cache.

* Het cache-exemplaar moet virtuele netwerk interfaces (Nic's) kunnen maken. De gebruiker die de cache maakt, moet voldoende bevoegdheden hebben in het abonnement om Nic's te maken.

* Bij gebruik van Blob-opslag moet de Azure HPC-cache worden gemachtigd om toegang te krijgen tot uw opslag account. Gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om de cache toegang te geven tot uw Blob-opslag. Er zijn twee rollen vereist: Inzender van het opslag account en de gegevensinzender voor opslag-blobs.

  Volg de instructies in [opslag doelen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de rollen toe te voegen.

## <a name="storage-infrastructure"></a>Opslag infrastructuur
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

De cache ondersteunt Azure Blob-containers, NFS-hardware-opslag en NFS-gekoppelde ADLS BLOB-containers (momenteel als preview-versie). Voeg opslag doelen toe nadat u de cache hebt gemaakt.

Elk opslag type heeft specifieke vereisten.

### <a name="blob-storage-requirements"></a>Vereisten voor Blob-opslag

Als u Azure Blob Storage wilt gebruiken met uw cache, hebt u een compatibel opslag account nodig en een lege BLOB-container of een container die is gevuld met gegevens in de Azure HPC-cache, zoals wordt beschreven in [gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md).

> [!NOTE]
> Andere vereisten zijn van toepassing op met NFS gekoppelde Blob-opslag. Lees de [ADLS-opslag vereisten voor NFS](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) voor meer informatie.

Maak het account voordat u probeert een opslag doel toe te voegen. U kunt een nieuwe container maken wanneer u het doel toevoegt.

Als u een compatibel opslag account wilt maken, gebruikt u deze instellingen:

* Prestaties: **standaard**
* Soort account: **StorageV2 (algemeen gebruik v2)**
* Replicatie: **lokaal redundante opslag (LRS)**
* Access-laag (standaard): **Hot**

Het is een goed idee om een opslag account te gebruiken dat zich op dezelfde locatie bevinden als de cache.

U moet ook de cache toepassing toegang geven tot uw Azure Storage-account, zoals vermeld in de [machtigingen](#permissions)hierboven. Volg de procedure in [opslag doelen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de vereiste toegangs rollen in de cache op te slaan. Als u niet de eigenaar van het opslag account bent, laat u de eigenaar deze stap uitvoeren.

### <a name="nfs-storage-requirements"></a>NFS-opslag vereisten
<!-- linked from configuration.md -->

Als u een NFS-opslag systeem (bijvoorbeeld een on-premises hardware NAS-systeem) gebruikt, moet u ervoor zorgen dat het voldoet aan deze vereisten. Mogelijk moet u samen werken met de netwerk beheerders of firewall beheerders voor uw opslag systeem (of Data Center) om deze instellingen te controleren.

> [!NOTE]
> Het maken van een opslag doel mislukt als de cache onvoldoende toegang tot het NFS-opslag systeem heeft.

Meer informatie vindt u in het oplossen van problemen [met NAS-configuratie en NFS-opslag doel](troubleshoot-nas.md).

* **Netwerk verbinding:** De Azure HPC-cache vereist netwerk toegang met hoge band breedte tussen het subnet van de cache en het Data Center van het NFS-systeem. [ExpressRoute](../expressroute/index.yml) of vergelijk bare toegang wordt aanbevolen. Als u een VPN gebruikt, moet u dit mogelijk configureren om TCP MSS op 1350 te zetten om ervoor te zorgen dat grote pakketten niet worden geblokkeerd. Lees de [beperkingen voor VPN-pakket grootte](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) voor meer hulp bij het oplossen van VPN-instellingen.

* **Poort toegang:** De cache moet toegang hebben tot specifieke TCP/UDP-poorten op uw opslag systeem. Verschillende typen opslag hebben verschillende poort vereisten.

  Volg deze procedure om de instellingen van uw opslag systeem te controleren.

  * Geef een `rpcinfo` opdracht voor uw opslag systeem op om de benodigde poorten te controleren. De onderstaande opdracht geeft een lijst van de poorten en formatteert de relevante resultaten in een tabel. (Gebruik het IP-adres van uw systeem in plaats van de *<storage_IP>* term.)

    U kunt deze opdracht geven vanuit elke Linux-client waarop de NFS-infra structuur is geïnstalleerd. Als u een-client in het cluster-subnet gebruikt, kunt u hiermee ook de connectiviteit tussen het subnet en het opslag systeem controleren.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Zorg ervoor dat alle poorten die door de query zijn geretourneerd, ``rpcinfo`` onbeperkt verkeer van het subnet van de Azure HPC-cache toestaan.

  * Als u de opdracht niet kunt gebruiken `rpcinfo` , zorg er dan voor dat deze veelgebruikte poorten binnenkomend en uitgaand verkeer toestaan:

    | Protocol | Poort  | Service  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | gekoppeld   |
    | TCP/UDP  | 4047  | status   |

    Sommige systemen gebruiken verschillende poort nummers voor deze services. Raadpleeg de documentatie van uw opslag systeem voor meer informatie.

  * Controleer de firewall instellingen om er zeker van te zijn dat verkeer op al deze vereiste poorten is toegestaan. Controleer of de firewalls in Azure en on-premises firewalls in uw Data Center worden gebruikt.

* **Directory toegang:** Schakel de `showmount` opdracht in het opslag systeem in. De Azure HPC-cache maakt gebruik van deze opdracht om te controleren of de configuratie van de opslag doel naar een geldige export wijst en om ervoor te zorgen dat meerdere koppels geen toegang hebben tot dezelfde submappen (een risico voor conflicterende bestanden).

  > [!NOTE]
  > Als uw NFS-opslag systeem gebruikmaakt van het ONTAP 9,2-besturings systeem van NetApp, **moet u niet inschakelen `showmount`**. [Neem contact op met micro soft-service en ondersteuning](hpc-cache-support-ticket.md) voor hulp.

  Meer informatie over toegang tot mapweergave vindt u in het artikel NFS-opslag doel [probleem oplossing](troubleshoot-nas.md#enable-export-listing).

* **Hoofd toegang** (lezen/schrijven): de cache maakt verbinding met het back-end-systeem als gebruiker-id 0. Controleer deze instellingen op uw opslag systeem:
  
  * Inschakelen `no_root_squash` . Deze optie zorgt ervoor dat de externe hoofd gebruiker toegang kan krijgen tot bestanden die eigendom zijn van de hoofdmap.

  * Controleer het export beleid om er zeker van te zijn dat er geen beperkingen zijn voor toegang tot de hoofdmap vanuit het subnet van de cache.

  * Als uw opslag export bevat die submappen zijn van een andere export, controleert u of de cache hoofd toegang heeft tot het laagste segment van het pad. Lees de hoofdmap van de [Directory-paden](troubleshoot-nas.md#allow-root-access-on-directory-paths) in het artikel over het oplossen van problemen met de NFS-opslag doel voor meer informatie.

* Opslag van NFS-back-end moet een compatibel hardware/software-platform zijn. Neem contact op met het team van HPC-cache voor meer informatie.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Opslag vereisten voor NFS-gekoppelde BLOB (ADLS-NFS) (PREVIEW)

Azure HPC cache kan ook een BLOB-container gebruiken die is gekoppeld met het NFS-protocol als een opslag doel.

> [!NOTE]
> Ondersteuning voor NFS 3,0-protocol voor Azure Blob Storage is in open bare preview. De beschik baarheid is beperkt en functies kunnen worden gewijzigd tussen nu en wanneer de functie algemeen beschikbaar wordt. Gebruik geen preview-technologie in productie systemen.
>
> Lees meer over deze preview-functie in de [NFS 3,0-protocol ondersteuning in Azure Blob-opslag](../storage/blobs/network-file-system-protocol-support.md).

De vereisten voor het opslag account zijn verschillend voor een ADLS-NFS-Blob-opslag doel en voor een standaard-Blob-opslag doel. Volg de instructies in [Blob Storage koppelen door het NFS-protocol (Network File System 3,0)](../storage/blobs/network-file-system-protocol-support-how-to.md) zorgvuldig te gebruiken om het opslag account voor NFS te maken en te configureren.

Dit is een algemeen overzicht van de stappen. Deze stappen kunnen veranderen, dus Raadpleeg altijd de [ADLS-NFS-instructies](../storage/blobs/network-file-system-protocol-support-how-to.md) voor actuele informatie.

1. Zorg ervoor dat de functies die u nodig hebt, beschikbaar zijn in de regio's waar u van plan bent te werken.

1. Schakel de NFS-protocol functie in voor uw abonnement. Doe dit *voordat* u het opslag account maakt.

1. Maak een beveiligd virtueel netwerk (VNet) voor het opslag account. Gebruik hetzelfde virtuele netwerk voor uw opslag account voor NFS en voor uw Azure HPC-cache. (Gebruik niet hetzelfde subnet als de cache.)

1. Maak het opslag account.

   * In plaats van de instellingen voor het opslag account te gebruiken voor een standaard Blob Storage-account, volgt u de instructies in het [document How-to](../storage/blobs/network-file-system-protocol-support-how-to.md). Het type opslag account dat wordt ondersteund, kan variëren per Azure-regio.

   * Kies in de sectie **netwerken** een persoonlijk eind punt in het beveiligde virtuele netwerk dat u hebt gemaakt (aanbevolen) of kies een openbaar eind punt met beperkte toegang vanaf het beveiligde VNet.

   * Vergeet niet om de sectie **Geavanceerd** te volt ooien, waar u NFS-toegang inschakelt.

   * Geef de cache toepassing toegang tot uw Azure Storage-account zoals vermeld in de [machtigingen](#permissions)hierboven. U kunt dit doen wanneer u voor het eerst een opslag doel maakt. Volg de procedure in [opslag doelen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de vereiste toegangs rollen in de cache op te slaan.

     Als u niet de eigenaar van het opslag account bent, laat u de eigenaar deze stap uitvoeren.

## <a name="set-up-azure-cli-access-optional"></a>Toegang tot Azure CLI instellen (optioneel)

Als u de Azure HPC-cache wilt maken of beheren vanuit de Azure-opdracht regel interface (Azure CLI), moet u de CLI-software en de HPC-cache-extensie installeren. Volg de instructies in [Azure cli instellen voor Azure HPC-cache](az-cli-prerequisites.md).

## <a name="next-steps"></a>Volgende stappen

* [Een Azure HPC-cache-exemplaar maken](hpc-cache-create.md) op basis van de Azure Portal
