---
title: Exemplaren van failoverclusters
description: Lees hier meer over exemplaren van failoverclusters (FCI's) met SQL Server op virtuele Azure-machines (VM's).
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 5845a3bdc4b86fbbe44c92779e5aae95044eb6b2
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556366"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Exemplaren van failoverclusters met SQL Server op virtuele Azure-machines (VM's).
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel worden de verschillen in functies beschreven wanneer u werkt met exemplaren van failoverclusters (FCI's) voor SQL Server op virtuele Azure-machines (VM's). 

## <a name="overview"></a>Overzicht

SQL Server op Azure-VM's maakt gebruik van de WSFC-functionaliteit (Windows Server Failover Clustering) om lokaal hoge beschikbaarheid te bieden via redundantie op het niveau van het serverexemplaar: een exemplaar van een failovercluster. Een FCI is één exemplaar van SQL Server dat is geïnstalleerd op WSFC-knooppunten (het cluster) en, mogelijk, in verschillende subnetten. In het netwerk wordt een FCI weergegeven als een exemplaar van SQL Server dat op één computer wordt uitgevoerd. Maar de FCI biedt failover van het ene WSFC-knooppunt naar een ander als het huidige knooppunt niet meer beschikbaar is.

De rest van het artikel richt zich op de verschillen voor exemplaren van failoverclusters wanneer deze worden gebruikt met SQL Server Azure-VM's. Lees deze artikelen voor meer informatie over de technologie achter failoverclustering: 

- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Exemplaren van failoverclusters met SQL Server op virtuele Azure-machines ondersteunen het gebruik van een schijf-witness, een cloud-witness of een bestandsshare-witness voor clusterquorum.

Zie [Cluster configuration best practices (SQL Server on Azure VMs)](hadr-cluster-best-practices.md#quorum) (Best practices voor clusterconfiguratie (SQL Server op Azure-VM's)) voor meer informatie. 


## <a name="storage"></a>Storage

In traditionele on-premises geclusterde omgevingen gebruikt een Windows-failovercluster een SAN (Storage Area Network) dat door beide knooppunten als gedeelde opslag toegankelijk is. SQL Server-bestanden worden gehost in de gedeelde opslag, en alleen het actieve knooppunt heeft op enig moment toegang tot de bestanden. 

SQL Server op Azure-VM's biedt verschillende opties als een oplossing voor gedeelde opslag voor een implementatie van exemplaren van failoverclusters met SQL Server: 

||[Gedeelde Azure-schijven](../../../virtual-machines/windows/disks-shared.md)|[Premiumbestandsshares](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[S2D (Storage Spaces Direct)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Minimale versie van het besturingssysteem**| Alles |Windows Server 2012|Windows Server 2016|
|**Minimale versie van SQL Server**|Alles|SQL Server 2012|SQL Server 2016|
|**Ondersteunde VM-beschikbaarheid** |Beschikbaarheidssets met nabijheidsplaatsingsgroepen |Beschikbaarheidssets en beschikbaarheidszones|Beschikbaarheidssets |
|**Ondersteunt FileStream**|Ja|Nee|Ja |
|**Azure blob-cache**|Nee|Nee|Ja|

In de rest van deze sectie vindt u een overzicht van de voordelen en beperkingen van elke opslagoptie die beschikbaar is voor SQL Server op Azure-VM's. 

### <a name="azure-shared-disks"></a>Gedeelde Azure-schijven

[Gedeelde Azure-schijven](../../../virtual-machines/windows/disks-shared.md) is een functie van [Azure Managed Disks](../../../virtual-machines/managed-disks-overview.md). Windows Server Failover Clustering ondersteunt het gebruik van gedeelde Azure-schijven met een exemplaar van een failovercluster. 

**Ondersteund besturingssysteem**: Alle   
**Ondersteunde SQL-versie**: Alle     

**Voordelen**: 
- Handig voor toepassingen die moeten worden gemigreerd naar Azure, met behoud van hun architectuur voor hoge beschikbaarheid en herstel na noodgevallen (HADR). 
- Geclusterde toepassingen kunnen ongewijzigd naar Azure worden gemigreerd door de ondersteuning voor SCSI PR (SCSI Persistent Reservations). 
- Biedt ondersteuning voor opslag met Azure Premium SSD en Azure Ultra Disk-opslag.
- Met behulp van één gedeelde schijf of meerdere gedeelde schijven met striping kan een gedeelde opslaggroep worden gemaakt. 
- Ondersteunt FileStream.


**Beperkingen**: 
- Virtuele machines moeten in dezelfde beschikbaarheidsset en dezelfde nabijheidsplaatsingsgroep worden geplaatst.
- Beschikbaarheidszones worden niet ondersteund.
- Caching op Premium SSD-schijven wordt niet ondersteund.
 
Zie [Create an FCI with Azure shared disks (SQL Server on Azure VMs)](failover-cluster-instance-azure-shared-disks-manually-configure.md) (Een FCI maken met gedeelde Azure-schijven (SQL Server op Azure-VM's)) om aan de slag te gaan. 

### <a name="storage-spaces-direct"></a>Opslagruimten direct

[Storage Spaces Direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) is een Windows Server-functie die wordt ondersteund met failoverclustering op virtuele Azure-machines. De functie biedt een virtueel SAN op basis van software.

**Ondersteund besturingssysteem**: Windows Server 2016 en hoger   
**Ondersteunde SQL-versie**: SQL Server 2016 en hoger   


**Voordelen:** 
- Voldoende netwerkbandbreedte zorgt voor een stabiele en goed presterende oplossing voor gedeelde opslag. 
- Biedt ondersteuning voor Azure blob-cache, zodat leesbewerkingen lokaal vanuit de cache kunnen worden verwerkt. (Updates worden gelijktijdig gerepliceerd naar beide knooppunten.) 
- Ondersteunt FileStream. 

**Beperkingen:**
- Alleen beschikbaar voor Windows Server 2016 en hoger. 
- Beschikbaarheidszones worden niet ondersteund.
- Vereist dat aan beide virtuele machines dezelfde schijfcapaciteit is gekoppeld. 
- Hoge netwerkbandbreedte is vereist voor hoge prestaties vanwege de continue replicatie van de schijf. 
- Vereist een grotere VM-grootte en dubbele betaling voor opslag, omdat opslag is gekoppeld aan elke VM. 

Zie [Create an FCI with Storage Spaces Direct (SQL Server on Azure VMs)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) (Een FCI maken met Storage Spaces Direct (SQL Server op Azure-VM's)) om aan de slag te gaan. 

### <a name="premium-file-share"></a>Premium-bestandsshare

[Premium-bestandsshares](../../../storage/files/storage-how-to-create-premium-fileshare.md) zijn een functie van [Azure Files](../../../storage/files/index.yml). Premium-bestandsshares worden opgeslagen op een SSD en hebben een consistent lage latentie. Ze worden volledig ondersteund voor gebruik met exemplaren van failoverclusters voor SQL Server 2012 of hoger met Windows Server 2012 of hoger. Premium-bestandsshares bieden meer flexibiliteit, omdat u een bestandsshare zonder downtime kunt vergroten of verkleinen en schalen.

**Ondersteund besturingssysteem**: Windows Server 2012 en hoger   
**Ondersteunde SQL-versie**: SQL Server 2012 en hoger   

**Voordelen:** 
- Oplossing voor uitsluitend gedeelde opslag voor virtuele machines verspreid over meerdere beschikbaarheidszones. 
- Volledig beheerd bestandssysteem met zeer lage latentie en goede I/O-prestaties met burstmogelijkheden. 

**Beperkingen:**
- Alleen beschikbaar voor Windows Server 2012 en hoger. 
- FileStream wordt niet ondersteund. 


Zie [Create an FCI with a premium file share (SQL Server on Azure VMs)](failover-cluster-instance-premium-file-share-manually-configure.md) (Een FCI maken met een Premium-bestandsshare (SQL Server op Azure-VM's)) om aan de slag te gaan. 

### <a name="partner"></a>Partner

Er zijn oplossingen voor clustering met ondersteunde partneropslag. 

**Ondersteund besturingssysteem**: Alle   
**Ondersteunde SQL-versie**: Alle   

Een voorbeeld is om SIOS DataKeeper als opslag te gebruiken. Zie voor meer informatie het blogbericht [Failover clustering and SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI en ExpressRoute

U kunt ook gedeelde blokopslag op een iSCSI-doel beschikbaar maken via Azure ExpressRoute. 

**Ondersteund besturingssysteem**: Alle   
**Ondersteunde SQL-versie**: Alle   

NetApp Private Storage (NPS) biedt bijvoorbeeld een iSCSI-doel aan voor Azure-VM's via ExpressRoute met Equinix.

In het geval van oplossingen van Microsoft-partners voor gedeelde opslag en gegevensreplicatie neemt u contact op met de leverancier als er problemen zijn met de toegang tot gegevens bij failover.

## <a name="connectivity"></a>Connectiviteit

Exemplaren van failoverclusters met SQL Server op virtuele Azure-machines gebruiken een [DNN (naam van gedistribueerd netwerk)](failover-cluster-instance-distributed-network-name-dnn-configure.md) of een [VNN (naam van een virtueel netwerk) met Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) om verkeer door te sturen naar het SQL Server exemplaar, ongeacht welk knooppunt momenteel eigenaar is van de geclusterde resources. Er zijn aanvullende overwegingen bij het gebruik van bepaalde functies en de DNN met een FCI met SQL Server. Zie [Feature interoperability with SQL Server FCI & DNN](failover-cluster-instance-dnn-interoperability.md) (Interoperabiliteit van functies met FCI met SQL Server en DNN) voor meer informatie. 

Raadpleeg [HADR-verbindingen routeren naar SQL Server in Azure-VM's](hadr-cluster-best-practices.md#connectivity) voor meer informatie over opties voor clusterconnectiviteit. 

## <a name="limitations"></a>Beperkingen

Houd rekening met de volgende beperkingen voor exemplaren van failoverclusters met SQL Server op virtuele Azure-machines. 

### <a name="lightweight-extension-support"></a>Ondersteuning voor Lichtgewicht-extensie   

Op dit moment worden exemplaren van failoverclusters met SQL Server op virtuele Azure-machines alleen ondersteund met de [beheermodus Lichtgewicht](sql-server-iaas-agent-extension-automate-management.md#management-modes) van de SQL Server IaaS Agent-extensie. Als u wilt overschakelen van de volledige extensiemodus naar lichtgewicht, verwijdert u de resource van de **virtuele SQL-machine** voor de bijbehorende VM's en registreert u deze vervolgens in de modus lichtgewicht bij de extensie SQL IaaS-agent. Wanneer u de resource van de **virtuele SQL-machine** verwijdert met behulp van Azure Portal, schakelt u het selectievakje naast de juiste virtuele machine uit om te voorkomen dat de VM wordt verwijderd. 

De volledige extensie ondersteunt functies zoals automatische back-ups en patches, en geavanceerd portalbeheer. Deze functies werken niet voor VM's met SQL Server die in de beheermodus Lichtgewicht zijn geregistreerd.

### <a name="msdtc"></a>MSDTC 

Virtuele Azure-machines bieden ondersteuning voor MSDTC (Microsoft Distributed Transaction Coordinator) in Windows Server 2019 met opslag op geclusterde gedeelde volumes (CSV) en [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) of op VM's met SQL Server die gebruikmaken van gedeelde Azure-schijven. 

MSDTC wordt vanwege deze redenen niet ondersteund op virtuele Azure-machines voor Windows Server 2016 of eerder met geclusterde gedeelde volumes:

- De geclusterde MSDTC-resource kan niet worden geconfigureerd voor het gebruik van gedeelde opslag. Als u in Windows Server 2016 een MSDTC-resource maakt, wordt er geen gedeelde opslag weergegeven die beschikbaar is voor gebruik, zelfs als dat wel het geval is. Dit probleem is opgelost in Windows Server 2019.
- De standaard load balancer biedt geen ondersteuning voor RPC-poorten.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [best practices voor clusterconfiguraties](hadr-cluster-best-practices.md), zodat u daarna [uw VM met SQL Server kunt voorbereiden voor FCI](failover-cluster-instance-prepare-vm.md). 

Zie voor meer informatie: 

- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)