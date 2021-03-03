---
title: IBM DB2 pureScale op Azure
description: In dit artikel wordt een architectuur weer gegeven voor het uitvoeren van een IBM DB2 pureScale-omgeving in Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 29150f229f1bd6adbbe6a335fdb91a44f3a2345b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675672"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale op Azure

De IBM DB2 pureScale-omgeving biedt een database cluster voor Azure met hoge Beschik baarheid en schaal baarheid in Linux-besturings systemen. Dit artikel bevat een architectuur voor het uitvoeren van DB2 pureScale in Azure.

## <a name="overview"></a>Overzicht

Ondernemingen hebben lange, traditionele RDBMS-platforms (Relational Database Management System) gebruikt voor de behoeften van hun online Trans Action processing (OLTP). De meeste dagen migreren hun op mainframe gebaseerde database omgevingen naar Azure als een manier om capaciteit uit te breiden, kosten te verlagen en een stabiele operationele kosten structuur te hand haven. Migratie is vaak de eerste stap bij het moderniseren van een verouderd platform. 

Onlangs heeft een zakelijke klant de IBM DB2-omgeving die op z/OS wordt uitgevoerd, opnieuw gehost op IBM DB2 pureScale op Azure. De pureScale-oplossing voor de Db2-database cluster biedt hoge Beschik baarheid en schaal baarheid in Linux-besturings systemen. De klant heeft Db2 als een zelfstandig, geschaald exemplaar op één virtuele machine (VM) uitgevoerd in een groot, uitbreidbaar systeem op Azure vóór de installatie van de Db2-pureScale. 

Hoewel de oorspronkelijke omgeving niet identiek is, levert IBM DB2 pureScale op Linux vergelijk bare functies met hoge Beschik baarheid en schaal baarheid als IBM DB2 voor z/OS dat wordt uitgevoerd in een parallelle Sysplex-configuratie op het mainframe. In dit scenario wordt het cluster via iSCSI verbonden met een gedeeld opslag cluster. We hebben het GlusterFS-bestands systeem gebruikt, een gratis, schaalbaar, open-source gedistribueerd bestands systeem dat specifiek is geoptimaliseerd voor Cloud opslag. IBM ondersteunt deze oplossing echter niet meer. Als u de ondersteuning van IBM wilt behouden, moet u een ondersteund bestands systeem dat compatibel is met iSCSI gebruiken. Micro soft biedt Opslagruimten Direct (S2D) als optie

In dit artikel wordt de architectuur beschreven die wordt gebruikt voor deze Azure-migratie. De klant heeft Red Hat Linux 7,4 gebruikt voor het testen van de configuratie. Deze versie is beschikbaar op de Azure Marketplace. Voordat u een Linux-distributie kiest, moet u ervoor zorgen dat u de momenteel ondersteunde versies verifieert. Zie de documentatie voor [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) en [glusterfs](https://docs.gluster.org/en/latest/)voor meer informatie.

Dit artikel is een uitgangs punt voor uw DB2-implementatie plan. Uw bedrijfs vereisten verschillen, maar hetzelfde basis patroon is van toepassing. U kunt dit architecturale patroon ook gebruiken voor Online Analytical Processing (OLAP)-toepassingen in Azure.

Dit artikel heeft geen betrekking op verschillen en mogelijke migratie taken voor het verplaatsen van een IBM DB2 voor z/OS-Data Base naar IBM DB2 pureScale dat wordt uitgevoerd op Linux. En het biedt geen aanpassings schattingen voor de werk belasting voor het overstappen van DB2 z/OS naar de DB2-pureScale. 

Om u te helpen beslissen over de beste architectuur voor de DB2-pureScale voor uw omgeving, wordt u aangeraden de grootte volledig te ramen en een hypo these te maken. Zorg er in het bron systeem voor dat u de Sysplex van de DB2-z/O'S-parallelle DDF met de architectuur voor gegevens deling, de configuratie van de koppelings faciliteit en-gebruiks statistieken (Distributed Data Facility) kunt overwegen.

> [!NOTE]
> In dit artikel wordt één benadering van de DB2-migratie beschreven, maar er zijn er andere. DB2 pureScale kan bijvoorbeeld ook worden uitgevoerd in gevirtualiseerde on-premises omgevingen. IBM ondersteunt DB2 op Microsoft Hyper-V in verschillende configuraties. Zie voor meer informatie de [architectuur van DB2 pureScale-virtualisatie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) in het IBM-kennis centrum.

## <a name="architecture"></a>Architectuur

Ter ondersteuning van hoge Beschik baarheid en schaal baarheid in azure, kunt u gebruikmaken van een scale-out, gedeelde gegevens architectuur voor DB2-pureScale. De klant migratie gebruikte de volgende voorbeeld architectuur.

![DB2-pureScale op virtuele machines van Azure met opslag en netwerken](media/pureScaleArchitecture.png "DB2-pureScale op virtuele machines van Azure met opslag en netwerken")


Het diagram toont de logische lagen die nodig zijn voor een DB2 pureScale-cluster. Dit zijn onder andere virtuele machines voor een-client, voor beheer, voor caching, voor de data base-engine en voor gedeelde opslag. 

Naast de knoop punten van de data base-engine bevat het diagram twee knoop punten die worden gebruikt voor het opslaan van clusters (CFs). Er worden mini maal twee knoop punten gebruikt voor de data base-engine zelf. Een DB2-Server die deel uitmaakt van een pureScale-cluster wordt een lid genoemd. 

Het cluster is verbonden via iSCSI met een gedeeld opslag cluster met drie knoop punten om scale-out opslag en hoge Beschik baarheid te bieden. DB2 pureScale wordt geïnstalleerd op virtuele machines van Azure waarop Linux wordt uitgevoerd.

Deze benadering is een sjabloon die u kunt aanpassen voor de grootte en schaal van uw organisatie. Het is gebaseerd op het volgende:

-   Twee of meer database leden worden gecombineerd met ten minste twee CF-knoop punten. De knoop punten beheren een globale buffer groep (GBP) voor gedeeld geheugen en GLM-Services (Global Lock Manager) om gedeelde toegang te beheren en de inhoud van actieve leden te vergren delen. Eén CF-knoop punt fungeert als de primaire en de andere als het secundaire failover-knoop punt van het failovercluster. Om te voor komen dat een Single Point of Failure in de omgeving, vereist een DB2 pureScale-cluster ten minste vier knoop punten.

-   Gedeelde opslag met hoge prestaties (weer gegeven in P30-grootte in het diagram). Elk knoop punt gebruikt deze opslag.

-   Netwerken met hoge prestaties voor de gegevens leden en gedeelde opslag.

### <a name="compute-considerations"></a>Reken overwegingen

Deze architectuur voert de toepassings-, opslag-en gegevens lagen uit op virtuele machines van Azure. Met de [installatie scripts](https://aka.ms/db2onazure) voor de implementatie maakt u het volgende:

-   Een DB2 pureScale-cluster. Het type reken resources dat u nodig hebt in azure, is afhankelijk van uw installatie. In het algemeen kunt u twee benaderingen gebruiken:

    -   Gebruik een HPC-netwerk (High-Performance Computing) met meerdere knoop punten waarbij kleine tot middel grote instanties toegang krijgen tot gedeelde opslag. Voor dit HPC-type configuratie bieden de door Azure-geheugen geoptimaliseerde E-Series of door opslag geoptimaliseerde [virtuele machines](../../../sizes.md) van de L-serie de benodigde reken kracht.

    -   Gebruik minder grote instanties van virtuele machines voor de data-engines. Voor grote instanties zijn de grootste, door het geheugen geoptimaliseerde [M-Series](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuele machines ideaal voor zware werk belastingen in het geheugen. U hebt mogelijk een toegewezen exemplaar nodig, afhankelijk van de grootte van de logische partitie (LPAR) die wordt gebruikt om DB2 uit te voeren.

-   De DB2-CF maakt gebruik van virtuele machines die zijn geoptimaliseerd voor geheugen, zoals E-Series of L-serie.

-   Een gedeeld opslag cluster dat gebruikmaakt van Standard \_ DS4 \_ v2 virtuele machines waarop Linux wordt uitgevoerd.

-   De Management JumpBox is een standaard \_ DS2 \_ v2-virtuele machine met Linux.  Een alternatief is Azure Bastion, een service die een veilige RDP/SSH-ervaring biedt voor alle virtuele machines in uw virtuele netwerk.

-   De client is een standaard \_ DS3 \_ v2 virtuele machine met Windows (gebruikt voor het testen).

-   *Optioneel*. Een Witness-server. Dit is alleen nodig voor bepaalde eerdere versies van Db2 pureScale. In dit voor beeld wordt een standaard \_ DS3 \_ v2-virtuele machine met Linux gebruikt (gebruikt voor de DB2-pureScale).

> [!NOTE]
> Een DB2 pureScale-cluster vereist ten minste twee DB2-exemplaren. Er is ook een cache-exemplaar en een exemplaar van een vergrendelings Manager vereist.

### <a name="storage-considerations"></a>Opslagoverwegingen

Net als Oracle RAC is DB2 pureScale een krachtige blok-I/O-en scale-out-data base. We raden u aan om de grootste optie voor [Azure Premium SSD](../../../disks-types.md) te gebruiken die aansluit bij uw behoeften. Kleinere opslag opties zijn mogelijk geschikt voor ontwikkel-en test omgevingen, terwijl productie omgevingen vaak meer opslag capaciteit nodig hebben. De voorbeeld architectuur maakt gebruik van [P30](https://azure.microsoft.com/pricing/details/managed-disks/) vanwege de verhouding van IOPS tot grootte en prijs. Ongeacht de grootte gebruikt u Premium Storage voor de beste prestaties.

DB2 pureScale maakt gebruik van een gedeelde-alles-architectuur, waarbij alle gegevens toegankelijk zijn vanaf alle cluster knooppunten. Premium-opslag moet worden gedeeld door meerdere exemplaren, hetzij op aanvraag of op toegewezen exemplaren.

Een groot DB2 pureScale-cluster kan 200 terabytes (TB) of meer extra Premium-opslag vereisen met IOPS van 100.000. DB2 pureScale ondersteunt een iSCSI-blok interface die u kunt gebruiken in Azure. De iSCSI-interface vereist een gedeeld opslag cluster dat u kunt implementeren met S2D of een ander hulp programma. Met dit type oplossing maakt u een virtueel Storage Area Network (vSAN)-apparaat in Azure. DB2 pureScale maakt gebruik van de vSAN voor het installeren van het geclusterde bestands systeem dat wordt gebruikt voor het delen van gegevens tussen virtuele machines.

### <a name="networking-considerations"></a>Aandachtspunten voor netwerken

IBM beveelt InfiniBand-netwerken aan voor alle leden in een DB2 pureScale-cluster. DB2 pureScale maakt ook gebruik van RDMA (Remote Direct Memory Access), waar beschikbaar, voor de CFs.

Tijdens de installatie maakt u een Azure- [resource groep](../../../../azure-resource-manager/management/overview.md) die alle virtuele machines bevat. Over het algemeen kunt u resources groeperen op basis van hun levens duur en wie ze beheert. Voor de virtuele machines in deze architectuur is [versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)vereist. Het is een Azure-functie die consistente, uiterst lage netwerk latentie biedt via I/O-virtualisatie met één hoofdmap (SR-IOV) naar een virtuele machine.

Elke virtuele machine van Azure wordt geïmplementeerd in een virtueel netwerk dat subnetten heeft: Main, Gluster FS front end (gfsfe), Gluster FS back end (bfsbe), DB2 pureScale (db2be) en DB2 pureScale front end (db2fe). Het installatie script maakt ook de primaire [nic's](../../../windows/multiple-nics.md) op de virtuele machines in het hoofd subnet.

Gebruik [netwerk beveiligings groepen](../../../../virtual-network/virtual-network-vnet-plan-design-arm.md) om netwerk verkeer binnen het virtuele netwerk te beperken en de subnetten te isoleren.

In azure moet DB2 pureScale TCP/IP gebruiken als de netwerk verbinding voor opslag.

## <a name="next-steps"></a>Volgende stappen

-   [Deze architectuur implementeren op Azure](deploy-ibm-db2-purescale-azure.md)