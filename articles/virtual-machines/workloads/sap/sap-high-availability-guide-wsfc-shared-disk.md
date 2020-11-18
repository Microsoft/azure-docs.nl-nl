---
title: Cluster SAP ASCS/SCS instance op WSFC met behulp van gedeelde schijf in azure | Microsoft Docs
description: Meer informatie over het clusteren van een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een gedeelde cluster schijf.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f9bfcaa1299f4aacbc11110308ba14093b09f7d5
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684301"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf in azure

> ![Windows OS][Logo_Windows] Windows
>

Windows Server Failover Clustering is de basis van een High-Availability SAP-ASCS/SCS-installatie en DBMS in Windows.

Een failovercluster is een groep van 1 + n onafhankelijke servers (knoop punten) die samen werken om de beschik baarheid van toepassingen en services te verg Roten. Als er een storing optreedt in een knoop punt, wordt in Windows Server failover clustering het aantal fouten berekend dat kan optreden en nog steeds een goed onderhanden cluster voor het leveren van toepassingen en services. U kunt kiezen uit verschillende quorum modi om failover clustering te bezorgen.

## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat met de taken in dit artikel, raadpleegt u het volgende artikel:

* [Azure Virtual Machines architectuur en scenario's met hoge Beschik baarheid voor SAP net-Weaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server Failover Clustering in azure

Voor Windows Server failover clustering met Azure Virtual Machines zijn aanvullende configuratie stappen vereist. Wanneer u een cluster bouwt, moet u verschillende IP-adressen en namen van virtuele hosts instellen voor het SAP-exemplaar ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Naam omzetting in Azure en de naam van de virtuele cluster-host

Het Azure-Cloud platform biedt geen optie voor het configureren van virtuele IP-adressen, zoals zwevende IP-adressen. U hebt een alternatieve oplossing nodig om een virtueel IP-adres in te stellen om de cluster bron in de cloud te bereiken. 

De Azure Load Balancer-service biedt een *interne Load Balancer* voor Azure. Met de interne load balancer bereiken clients het cluster via het virtuele IP-adres van het cluster. 

Implementeer de interne load balancer in de resource groep die de cluster knooppunten bevat. Configureer vervolgens alle benodigde regels voor het door sturen van poorten met behulp van de test poorten van de interne load balancer. Clients kunnen verbinding maken via de naam van de virtuele host. De DNS-server zet het IP-adres van het cluster op en de interne load balancer verwerkt poort door sturen naar het actieve knoop punt van het cluster.

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.  

![Afbeelding 1: configuratie van Windows Failover Clustering in azure zonder een gedeelde schijf][sap-ha-guide-figure-1001]

_Configuratie van Windows Server Failover Clustering in azure zonder een gedeelde schijf_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>SAP ASCS/SCS HA met gedeelde cluster schijven
In Windows bevat een SAP ASCS/SCS-instantie SAP Central Services, de SAP-berichten server, server processen in de wachtrij en SAP Global host-bestanden. Met SAP Global host files worden centrale bestanden opgeslagen voor het hele SAP-systeem.

Een SAP ASCS/SCS-exemplaar heeft de volgende onderdelen:

* SAP-Centrale Services:
    * Twee processen, een bericht en een server voor plaatsen, en een \<ASCS/SCS virtual host name> , die wordt gebruikt voor toegang tot deze twee processen.
    * Bestands structuur: S:\usr\sap \\ &lt; sid &gt; \ ASCS/SCS\<instance number\>


* SAP Global host files:
  * Bestands structuur: S:\usr\sap \\ &lt; sid &gt; \SYS \. ..
  * De sapmnt-bestands share, waarmee u toegang tot deze globale S:\usr\sap \\ &lt; sid &gt; \SYS \. .. files mogelijk maakt met behulp van het volgende UNC-pad:

    \\\\<ASCS/SCS-naam van virtuele host \> \Sapmnt \\ &lt; sid &gt; \SYS \. ..


![Afbeelding 2: processen, bestands structuur en globale host sapmnt-bestands share van een SAP ASCS/SCS-exemplaar][sap-ha-guide-figure-8001]

_Processen, bestands structuur en globale host sapmnt bestands share van een SAP ASCS/SCS-exemplaar_

In een instelling met hoge Beschik baarheid, cluster SAP ASCS/SCS instances. We gebruiken *geclusterde gedeelde schijven* (stations S, in ons voor beeld) om de ASCS/SCS en SAP Global host-bestanden te plaatsen.

![Afbeelding 3: SAP ASCS/SCS HA Architecture met gedeelde schijf][sap-ha-guide-figure-8002]

_SAP ASCS/SCS HA-architectuur met gedeelde schijf_


Met de architectuur Server replicatie 1 in wachtrij plaatsen:
* Hetzelfde \<ASCS/SCS virtual host name> wordt gebruikt voor toegang tot het SAP-bericht en server processen in de wachtrij en de SAP Global host-bestanden via de bestands share sapmnt.
* Dezelfde gedeelde cluster schijf stations worden onderling gedeeld.  

Met de architectuur Server replicatie 2 in wachtrij plaatsen: 
* Hetzelfde \<ASCS/SCS virtual host name> wordt gebruikt voor toegang tot het SAP Message Server-proces en de SAP Global host-bestanden via de bestands share sapmnt.
* Dezelfde gedeelde cluster schijf stations worden onderling gedeeld.
* Er is \<ERS virtual host name> een afzonderlijke toegang tot het Server proces voor plaatsen  

![Afbeelding 4: de architectuur van SAP ASCS/SCS HA met een gedeelde schijf][sap-ha-guide-figure-8003]

_SAP ASCS/SCS HA-architectuur met gedeelde schijf_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Gedeelde schijf en replicatie server in de wachtrij 

1. Gedeelde schijf wordt ondersteund met een architectuur voor Server replicatie 1 in de wachtrij, waarbij ERS-instantie (hosting Replication server):   

   - is niet geclusterd
   - gebruikt `localhost` naam
   - wordt geïmplementeerd op lokale schijven op elk van de cluster knooppunten

2. Gedeelde schijf wordt ook ondersteund met de architectuur Server replicatie 2 in wachtrij plaatsen, waarbij het ERS2-exemplaar (in de wachtrij voor het plaatsen van replicatie server):  

   - is geclusterd
   - de naam van de toegewezen virtuele/netwerkhost wordt gebruikt
   - het IP-adres van de virtuele ERS moet worden geconfigureerd op de interne Azure-Load Balancer, naast het IP-adres (A) SCS
   - wordt geïmplementeerd op **lokale schijven** op elk van de geclusterde knoop punten. er is daarom geen gedeelde schijf nodig

   > [!TIP]
   > U kunt hier meer informatie vinden over het in de wachtrij plaatsen van replicatie server 1 en 2 (ERS1 en ERS2):  
   > [Replicatie server in wachtrij plaatsen in een micro soft-failovercluster](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Nieuwe replicatie in de wachtrij plaatsen in een failover-cluster omgeving](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Opties voor gedeelde schijven in azure voor SAP-workloads

Er zijn twee opties voor gedeelde schijven in een Windows-failovercluster in Azure:

- [Gedeelde Azure-schijven](../../disks-shared.md) -functie, waarmee Azure Managed Disk gelijktijdig aan meerdere vm's kan worden gekoppeld. 
- Software van derden gebruiken [SIOS data keeper cluster Edition](https://us.sios.com/products/datakeeper-cluster) om een gespiegelde opslag te maken die gedeelde cluster opslag simuleert. 

Houd bij het selecteren van de technologie voor gedeelde schijf rekening met de volgende overwegingen:

**Gedeelde Azure-schijf voor SAP-workloads**
- Hiermee kunt u Azure Managed Disk aan meerdere Vm's tegelijk koppelen zonder dat extra software hoeft te worden onderhouden en gebruikt 
- U werkt met één gedeelde Azure-schijf op één opslag cluster. Dat heeft gevolgen voor de betrouw baarheid van uw SAP-oplossing.
- Momenteel is de enige ondersteunde implementatie met Azure gedeelde Premium-schijf in de Beschikbaarheidsset. Een gedeelde Azure-schijf wordt niet ondersteund in de zonegebonden-implementatie.     
- Zorg ervoor dat u een Azure Premium-schijf inricht met een minimale schijf grootte die is opgegeven in [Premium-SSD bereik](../../disks-shared.md#disk-sizes) om aan het vereiste aantal vm's tegelijk te kunnen koppelen (meestal 2 voor SAP ASCS Windows-failovercluster). 
- Een gedeelde Azure-schijf wordt niet ondersteund voor SAP-workloads, omdat deze geen ondersteuning biedt voor implementatie in een Beschikbaarheidsset of zonegebonden-implementatie.  
 
**SIOS**
- De SIOS-oplossing voorziet in realtime synchrone gegevens replicatie tussen twee schijven
- Met de SIOS-oplossing die u met twee Managed disks werkt, en als u beschikbaarheids sets of beschikbaarheids zones gebruikt, worden de beheerde schijven op verschillende opslag clusters gegrond. 
- Implementatie in beschikbaarheids zones wordt ondersteund
- Hiervoor moet u software van derden installeren en gebruiken, die u ook moet aanschaffen

### <a name="shared-disk-using-azure-shared-disk"></a>Gedeelde schijf met behulp van een gedeelde Azure-schijf

Micro soft biedt [gedeelde Azure-schijven](../../disks-shared.md), die kunnen worden gebruikt voor het implementeren van SAP ASCS/SCS hoge Beschik baarheid met een optie voor een gedeelde schijf.

#### <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

Momenteel kunt u Azure Premium-SSD-schijven gebruiken als een gedeelde Azure-schijf voor het SAP ASCS/SCS-exemplaar. De volgende beperkingen zijn momenteel aanwezig:

-  [Azure Ultra Disk](../../disks-types.md#ultra-disk) wordt niet ondersteund als een gedeelde Azure-schijf voor SAP-workloads. Het is momenteel niet mogelijk om virtuele Azure-machines te plaatsen met behulp van Azure Ultra disk in de Beschikbaarheidsset
-  Een [gedeelde Azure-schijf](../../disks-shared.md) met Premium-SSD schijven wordt alleen ondersteund met vm's in de beschikbaarheidsset. Het wordt niet ondersteund in Beschikbaarheidszones-implementatie. 
-  De waarde [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) voor de gedeelde Azure-schijf bepaalt hoeveel cluster knooppunten de gedeelde schijf kunnen gebruiken. Normaal gesp roken kunt u met het SAP ASCS/SCS-exemplaar twee knoop punten in een Windows-failovercluster configureren. Daarom moet de waarde voor `maxShares` zijn ingesteld op twee.
-  Alle SAP-ASCS/SCS-cluster-Vm's moeten worden geïmplementeerd in dezelfde [plaatsings groep voor Azure nabijheid](../../windows/proximity-placement-groups.md).   
   Hoewel u Windows-cluster-Vm's in een Beschikbaarheidsset met een gedeelde Azure-schijf zonder PPG implementeert, zorgt PPG ervoor dat de fysieke nabijheid van Azure gedeelde schijven en de cluster-Vm's worden gesloten, waardoor er minder latentie tussen de virtuele machines en de opslaglaag kan worden bereikt.    

Raadpleeg de sectie [beperkingen](../../disks-shared.md#limitations) van de documentatie van Azure Shared disk voor meer informatie over de beperkingen voor een gedeelde Azure-schijf.

> [!IMPORTANT]
> Wanneer u een Windows-failovercluster met SAP ASCS/SCS implementeert met een gedeelde Azure-schijf, moet u er rekening mee houden dat uw implementatie met één gedeelde schijf in één opslag cluster wordt uitgevoerd. Uw SAP ASCS/SCS-exemplaar wordt beïnvloed, in het geval van problemen met het opslag cluster, waar de gedeelde Azure-schijf wordt geïmplementeerd.    

> [!TIP]
> Bekijk de [SAP NetWeaver op de Azure-plannings handleiding](./planning-guide.md) en de [Azure Storage hand leiding voor SAP-workloads](./planning-guide-storage.md) voor belang rijke overwegingen bij het plannen van uw SAP-implementatie.

### <a name="supported-os-versions"></a>Ondersteunde versies van besturings systemen

Zowel Windows Server 2016 als 2019 worden ondersteund (gebruik de meest recente Data Center-installatie kopieën).

We raden u ten zeerste aan **Windows Server 2019 Data Center** te gebruiken als:
- Windows 2019 failover cluster-service is Azure-compatibel
- Er is een integratie en bewustzijn van Azure host-onderhoud toegevoegd en de ervaring is verbeterd door bewaking voor Azure Schedule-gebeurtenissen.
- U kunt de naam van een gedistribueerde netwerk gebruiken (dit is de standaard optie). Daarom is er geen specifiek IP-adres nodig voor de naam van het cluster netwerk. Bovendien hoeft u dit IP-adres niet te configureren op de interne Azure-Load Balancer. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Gedeelde schijven in azure met SIOS data keeper

Een andere optie voor gedeelde schijven is het gebruik van software van derden SIOS data keeper cluster Edition om een gespiegelde opslag te maken die gedeelde cluster opslag simuleert. De SIOS-oplossing biedt synchrone gegevens replicatie in realtime.

Een gedeelde schijf bron voor een cluster maken:

1. Koppel een extra schijf aan elk van de virtuele machines in een Windows-cluster configuratie.
2. Voer SIOS data keeper cluster Edition uit op beide VM-knoop punten.
3. Configureer de data keeper-cluster versie van SIOS zodat de inhoud van het extra schijf volume dat is gekoppeld aan de virtuele bron machine, wordt Spie gels op het extra schijf volume dat is gekoppeld aan de virtuele doel machine. SIOS data keeper maakt samen vatting van de bron-en doel volumes en geeft deze vervolgens weer als één gedeelde schijf aan Windows Server Failover Clustering.

Meer informatie over [SIOS data keeper](https://us.sios.com/products/datakeeper-cluster/).

![Afbeelding 5: configuratie van Windows Server Failover Clustering in azure met SIOS data keeper][sap-ha-guide-figure-1002]

_Configuratie van Windows Failover Clustering in azure met SIOS data keeper_

> [!NOTE]
> U hebt geen gedeelde schijven nodig voor hoge Beschik baarheid met enkele DBMS-producten, zoals SQL Server. SQL Server AlwaysOn repliceert DBMS-gegevens en logboek bestanden van de lokale schijf van een cluster knooppunt naar de lokale schijf van een ander cluster knooppunt. In dit geval heeft de configuratie van het Windows-cluster geen gedeelde schijf nodig.
>

## <a name="next-steps"></a>Volgende stappen

* [De Azure-infra structuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en een gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-infrastructure-wsfc-shared-disk]

* [SAP NetWeaver HA installeren op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Multi-SID-configuratie met hoge Beschik baarheid van SAP)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md