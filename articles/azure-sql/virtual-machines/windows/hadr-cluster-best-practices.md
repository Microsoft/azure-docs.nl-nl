---
title: Aanbevolen procedures voor cluster configuratie
description: Meer informatie over de ondersteunde cluster configuraties wanneer u hoge Beschik baarheid en herstel na nood gevallen (HADR) configureert voor SQL Server op Azure Virtual Machines, zoals ondersteunde quorums of routerings opties voor verbindingen.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 4ab4e40e1dd4bbaf9ae73ab545285f5ae6261e27
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201767"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Aanbevolen procedures voor clusterconfiguratie (SQL Server op virtuele machines van Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Een cluster wordt gebruikt voor hoge Beschik baarheid en herstel na nood gevallen (HADR) met SQL Server op Azure Virtual Machines (Vm's). 

In dit artikel vindt u aanbevolen procedures voor cluster configuratie voor [failoverclusterinstanties (failover cluster instances)](failover-cluster-instance-overview.md) en [beschikbaarheids groepen](availability-group-overview.md) wanneer u deze gebruikt met SQL Server op virtuele machines van Azure. 


## <a name="networking"></a>Netwerken

Gebruik één NIC per server (cluster knooppunt) en één subnet. Azure-netwerken hebben fysieke redundantie, waardoor extra Nic's en subnetten onnodig worden op een gast cluster van de virtuele machine van Azure. In het cluster validatie rapport wordt u gewaarschuwd dat de knoop punten alleen bereikbaar zijn op één netwerk. U kunt deze waarschuwing negeren op virtuele Azure-machine gast-failoverclusters.

### <a name="tuning-failover-cluster-network-thresholds"></a>Drempel waarden voor failover cluster-netwerken afstemmen

Bij het uitvoeren van Windows-Failoverclusterknooppunten in azure-Vm's met SQL Server AlwaysOn, wordt het wijzigen van de cluster instelling in een meer beperkte bewakings status aanbevolen.  Hierdoor is het cluster veel stabieler en betrouwbaarder.  Zie voor meer informatie [IaaS met SQL AlwaysOn-failover cluster network drempels](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="quorum"></a>Quorum

Hoewel een cluster met twee knoop punten werkt zonder [quorum bron](/windows-server/storage/storage-spaces/understand-quorum), zijn klanten strikt verplicht een quorum bron te gebruiken voor productie-ondersteuning. Cluster validatie geeft geen cluster zonder quorum resource door. 

Technisch gesp roken kan een cluster met drie knoop punten een verlies van één knoop punt belopen (tot twee nodes) zonder een quorum resource. Maar nadat het cluster is gedalen op twee knoop punten, is er een risico dat de geclusterde bronnen offline gaan in het geval van een verlies of communicatie fout om te voor komen dat een scenario voor Split wordt gespleten.

Als u een quorum bron configureert, kan het cluster online met slechts één knoop punt online gaan.

De volgende tabel bevat de beschik bare quorum opties in de volg orde die wordt aanbevolen voor gebruik met een virtuele Azure-machine, waarbij de schijfwitness de voorkeurs keuze is: 


||[Schijfwitness](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Cloudwitness](/windows-server/failover-clustering/deploy-cloud-witness)  |[Bestands share-Witness](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Ondersteund besturingssysteem**| Alles |Windows Server 2016 +| Alles|


### <a name="disk-witness"></a>Schijfwitness

Een schijfwitness is een kleine geclusterde schijf in het cluster beschik bare opslag groep. Deze schijf is Maxi maal beschikbaar en kan een failover tussen knoop punten uitvoeren. Het bevat een kopie van de cluster database, met een standaard grootte van meestal minder dan 1 GB. De schijfwitness is de voorkeurs quorum optie voor elk cluster dat gebruikmaakt van gedeelde Azure-schijven (of een oplossing voor gedeelde schijven, zoals gedeeld SCSI-, iSCSI-of Fibre Channel-SAN).  Een geclusterd gedeeld volume kan niet worden gebruikt als schijfwitness.

Een gedeelde Azure-schijf configureren als de schijfwitness. 

Zie [een schijfwitness configureren](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)om aan de slag te gaan.


**Ondersteund besturingssysteem**: Alle   


### <a name="cloud-witness"></a>Cloudwitness

Een cloudwitness is een type quorum-Witness van het failovercluster dat gebruikmaakt van Microsoft Azure om een stem op het cluster quorum te bieden. De standaard grootte is ongeveer 1 MB en bevat alleen de tijds tempel. Een cloudwitness is ideaal voor implementaties in meerdere sites, meerdere zones en meerdere regio's.

Zie [een Cloudwitness configureren](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)om aan de slag te gaan.


**Ondersteund besturingssysteem**: Windows Server 2016 en hoger   


### <a name="file-share-witness"></a>Bestandsshare-witness

Een bestandssharewitness is een SMB-bestands share die doorgaans is geconfigureerd op een bestands server met Windows Server. Het onderhoudt cluster informatie in een Witness. log-bestand, maar slaat geen kopie van de cluster database op. In azure kunt u een bestands share configureren op een afzonderlijke virtuele machine.

Zie [Configure a file share Witness](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)om aan de slag te gaan.


**Ondersteund besturingssysteem**: Windows Server 2012 en hoger   

## <a name="connectivity"></a>Connectiviteit

In een traditionele on-premises netwerk omgeving lijkt een SQL Server failover-cluster exemplaar één exemplaar van SQL Server uitgevoerd op één computer. Omdat het failover-cluster exemplaar van het knoop punt naar het knoop punt wordt gefailovert, biedt de virtuele netwerk naam (VNN) voor de instantie een uniform verbindings punt en kunnen toepassingen verbinding maken met het SQL Server exemplaar zonder te weten welk knoop punt momenteel actief is. Wanneer een failover optreedt, wordt de naam van het virtuele netwerk geregistreerd bij het nieuwe actieve knoop punt nadat het is gestart. Dit proces is transparant voor de client of toepassing die verbinding maakt met SQL Server, waardoor de uitval tijd van de client of toepassing tijdens een storing wordt geminimaliseerd. Op dezelfde manier gebruikt de listener van de beschikbaarheids groep een VNN om verkeer door te sturen naar de juiste replica. 

Gebruik een VNN met Azure Load Balancer of een gedistribueerde netwerk naam (DNN) om verkeer door te sturen naar de VNN van het failovercluster met SQL Server op virtuele machines van Azure of om de bestaande VNN-listener in een beschikbaarheids groep te vervangen. 


De volgende tabel vergelijkt de HADR-verbindings ondersteuning: 

| |**Virtual Network naam (VNN)**  |**Gedistribueerde netwerk naam (DNN)**  |
|---------|---------|---------|
|**Minimale versie van het besturingssysteem**| Alles | Windows Server 2016 |
|**Minimale versie van SQL Server** |Alles |SQL Server 2019 CU2 (voor FCI)<br/> SQL Server 2019 CU8 (voor AG)|
|**Ondersteunde HADR-oplossing** | Failover-clusterexemplaar <br/> Beschikbaarheidsgroep | Failover-clusterexemplaar <br/> Beschikbaarheidsgroep|


### <a name="virtual-network-name-vnn"></a>Naam van virtueel netwerk (VNN)

Omdat het virtuele IP-toegangs punt anders werkt in azure, moet u [Azure Load Balancer](../../../load-balancer/index.yml) zodanig configureren dat verkeer wordt gerouteerd naar het IP-adres van de FCI-knoop punten of de listener voor de beschikbaarheids groep. In virtuele machines van Azure bevat een load balancer het IP-adres voor de VNN waarop de geclusterde SQL Server resources zijn gebaseerd. De load balancer distribueert inkomende stromen die aan de front-end arriveren en routert dat verkeer vervolgens naar de instanties die zijn gedefinieerd door de back-end-pool. U configureert de verkeers stroom met behulp van regels voor taak verdeling en status controles. Met SQL Server FCI zijn de exemplaren van de back-end-pool de virtuele machines van Azure waarop SQL Server worden uitgevoerd. 

Er is een lichte vertraging bij de failover wanneer u de load balancer gebruikt, omdat de status test altijd om de 10 seconden wordt gecontroleerd. 

Meer informatie over het configureren van Azure Load Balancer voor het [failover-cluster exemplaar](failover-cluster-instance-vnn-azure-load-balancer-configure.md) of een [beschikbaarheids groep](availability-group-vnn-azure-load-balancer-configure.md)

**Ondersteund besturingssysteem**: Alle   
**Ondersteunde SQL-versie**: Alle   
**Ondersteunde HADR-oplossing**: failover-cluster exemplaar en beschikbaarheids groep   


### <a name="distributed-network-name-dnn"></a>Gedistribueerde netwerknaam (DNN)

De naam van een gedistribueerde netwerk is een nieuwe Azure-functie voor SQL Server 2019. De DNN biedt een andere manier om clients te SQL Server verbinding maken met het SQL Server failovercluster-exemplaar of de beschikbaarheids groep zonder een load balancer te gebruiken. 

Wanneer er een DNN-resource wordt gemaakt, wordt de DNS-naam met de IP-adressen van alle knoop punten in het cluster gebonden aan het cluster. De SQL-client probeert verbinding te maken met elk IP-adres in deze lijst om te zoeken naar de bron waarmee verbinding moet worden gemaakt.  U kunt dit proces versnellen door `MultiSubnetFailover=True` in de Connection String op te geven. Met deze instelling geeft u aan dat de provider alle IP-adressen parallel moet proberen, zodat de client direct verbinding kan maken met de FCI of listener. 

Als dat mogelijk is, wordt een gedistribueerde netwerk naam aanbevolen voor een load balancer omdat: 
- De end-to-end-oplossing is betrouwbaarder omdat u de load balancer resource niet meer hoeft te onderhouden. 
- Als u de load balancer Probe, wordt de duur van de failover geminimaliseerd. 
- De DNN vereenvoudigt het inrichten en beheren van het failovercluster of de beschikbaarheids groep-listener met SQL Server op Azure-Vm's. 

De meeste SQL Server functies werken op transparante wijze met FCI-en beschikbaarheids groepen wanneer u de DNN gebruikt, maar er zijn bepaalde functies waarvoor speciale aandacht vereist is. Zie [FCI-en DNN-interoperabiliteit](failover-cluster-instance-dnn-interoperability.md) en [AG-en DNN-interoperabiliteit](availability-group-dnn-interoperability.md) voor meer informatie. 

Als u aan de slag wilt gaan, leert u hoe u een gedistribueerde netwerk naam bron kunt configureren voor [een failovercluster](failover-cluster-instance-distributed-network-name-dnn-configure.md) of een [beschikbaarheids groep](availability-group-distributed-network-name-dnn-listener-configure.md)

**Ondersteund besturingssysteem**: Windows Server 2016 en hoger   
**Ondersteunde SQL-versie**: SQL Server 2019 Cu2 (FCI) en SQL Server 2019 CU8 (AG)   
**Ondersteunde HADR-oplossing**: failover-cluster exemplaar en beschikbaarheids groep   


## <a name="limitations"></a>Beperkingen

Houd rekening met de volgende beperkingen wanneer u werkt met FCI-of beschikbaarheids groepen en SQL Server op Azure Virtual Machines. 

### <a name="msdtc"></a>MSDTC 

Virtuele Azure-machines bieden ondersteuning voor MSDTC (Microsoft Distributed Transaction Coordinator) in Windows Server 2019 met opslag op geclusterde gedeelde volumes (CSV) en [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) of op VM's met SQL Server die gebruikmaken van gedeelde Azure-schijven. 

MSDTC wordt vanwege deze redenen niet ondersteund op virtuele Azure-machines voor Windows Server 2016 of eerder met geclusterde gedeelde volumes:

- De geclusterde MSDTC-resource kan niet worden geconfigureerd voor het gebruik van gedeelde opslag. Als u in Windows Server 2016 een MSDTC-resource maakt, wordt er geen gedeelde opslag weergegeven die beschikbaar is voor gebruik, zelfs als dat wel het geval is. Dit probleem is opgelost in Windows Server 2019.
- De standaard load balancer biedt geen ondersteuning voor RPC-poorten.


## <a name="next-steps"></a>Volgende stappen

Nadat u de juiste aanbevolen procedures voor uw oplossing hebt vastgesteld, kunt u aan de slag met het [voorbereiden van uw SQL Server VM voor FCI](failover-cluster-instance-prepare-vm.md) of door de beschikbaarheids groep te maken met behulp van de [Azure Portal](availability-group-azure-portal-configure.md), de [Azure cli/Power shell](./availability-group-az-commandline-configure.md)of de [Azure Quick](availability-group-quickstart-template-configure.md)start-sjablonen.
