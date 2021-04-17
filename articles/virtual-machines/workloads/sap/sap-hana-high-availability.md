---
title: Hoge beschikbaarheid van SAP HANA op azure-VM's op SLES-| Microsoft Docs
description: Hoge beschikbaarheid van SAP HANA in Azure-VM's op SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: ea1296fd4e31c2deaed79e980ab764c523a2bfd7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364359"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Hoge beschikbaarheid van SAP HANA in Azure-VM's op SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Voor on-premises ontwikkeling kunt u HANA-systeemreplicatie gebruiken of gedeelde opslag gebruiken om hoge beschikbaarheid voor SAP HANA.
Op virtuele Azure-machines (VM's) is HANA-systeemreplicatie in Azure momenteel de enige ondersteunde functie voor hoge beschikbaarheid. SAP HANA replicatie bestaat uit één primair knooppunt en ten minste één secundair knooppunt. Wijzigingen in de gegevens op het primaire knooppunt worden synchroon of asynchroon gerepliceerd naar het secundaire knooppunt.

In dit artikel wordt beschreven hoe u de virtuele machines implementeert en configureert, het cluster-framework installeert en SAP HANA configureert.
In de voorbeeldconfiguraties worden installatieopdrachten, **exemplaarnummer 03** en HANA-systeem-id **HN1** gebruikt.

Lees eerst de volgende SAP-opmerkingen en -documenten:

* SAP Note [1928533,]met:
  * De lijst met Azure VM-grootten die worden ondersteund voor de implementatie van SAP-software.
  * Belangrijke capaciteitsinformatie voor Azure VM-grootten.
  * De ondersteunde combinaties van SAP-software en besturingssysteem (OS) en database.
  * De vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure.
* SAP Note [2015553 bevat] de vereisten voor SAP-ondersteunde SAP-software-implementaties in Azure.
* SAP Note [2205917 heeft] aanbevolen besturingssysteeminstellingen voor SUSE Linux Enterprise Server voor SAP-toepassingen.
* SAP Note [1944799 heeft] SAP HANA Richtlijnen SUSE Linux Enterprise Server sap-toepassingen.
* SAP Note [2178632] heeft gedetailleerde informatie over alle metrische bewakingsgegevens die worden gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste sap-hostagentversie voor Linux in Azure.
* SAP Note [2243692 heeft] informatie over SAP-licenties op Linux in Azure.
* SAP Note [1984787 heeft] algemene informatie over SUSE Linux Enterprise Server 12.
* SAP Note [1999351 heeft] aanvullende informatie over probleemoplossing voor de Uitgebreide bewakingsextensie van Azure voor SAP.
* SAP Note [401162 heeft] informatie over het voorkomen van 'adres dat al in gebruik is' bij het instellen van HANA-systeemreplicatie.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) beschikt over alle vereiste SAP-notities voor Linux.
* [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure Virtual Machines planning en implementatie voor SAP on][planning-guide] Linux-handleiding.
* [Azure Virtual Machines-implementatie voor SAP on Linux][deployment-guide] (dit artikel).
* [Handleiding azure Virtual Machines DBMS-implementatie voor SAP on Linux.][dbms-guide]
* [SUSE Linux Enterprise Server voor best practices voor SAP Applications 12 SP3][sles-for-sap-bp]
  * Instellen van een SAP HANA SR Performance Optimized Infrastructure (SLES for SAP Applications 12 SP1). De handleiding bevat alle vereiste informatie voor het instellen van SAP HANA voor on-premises ontwikkeling. Gebruik deze handleiding als basislijn.
  * Een SR SAP HANA infrastructuur (SLES for SAP Applications 12 SP1) instellen

## <a name="overview"></a>Overzicht

Voor hoge beschikbaarheid wordt SAP HANA geïnstalleerd op twee virtuele machines. De gegevens worden gerepliceerd met behulp van HANA-systeemreplicatie.

![SAP HANA overzicht van hoge beschikbaarheid](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA installatie van systeemreplicatie maakt gebruik van een toegewezen virtuele hostnaam en virtuele IP-adressen. In Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. In de volgende lijst ziet u de configuratie van de load balancer:

* Front-endconfiguratie: IP-adres 10.0.0.13 voor hn1-db
* Back-endconfiguratie: verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeemreplicatie
* Testpoort: poort 62503
* Taakverdelingsregels: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implementeren voor Linux

De resourceagent voor SAP HANA is opgenomen in SUSE Linux Enterprise Server voor SAP-toepassingen.
De Azure Marketplace bevat een SUSE Linux Enterprise Server voor SAP-toepassingen 12 die u kunt gebruiken om nieuwe virtuele machines te implementeren.

### <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U kunt een van de snelstartsjablonen op GitHub gebruiken om alle vereiste resources te implementeren. Met de sjabloon worden de virtuele machines, de load balancer, de beschikbaarheidsset, en meer geïmplementeerd.
Volg deze stappen om de sjabloon te implementeren:

1. Open de [databasesjabloon][template-multisid-db] [of de geconvergeerde sjabloon][template-converged] op Azure Portal. 
    De databasesjabloon maakt alleen de taakverdelingsregels voor een database. Met de geconvergeerde sjabloon worden ook de taakverdelingsregels voor een ASCS/SCS- en ERS-exemplaar (alleen Linux) gemaakt. Als u van plan bent een sap NetWeaver-systeem te installeren en u het ASCS/SCS-exemplaar op dezelfde computers wilt installeren, gebruikt u de [geconvergeerde sjabloon][template-converged].

1. Voer de volgende parameters in:
    - **SAP-systeem-id:** voer de SAP-systeem-id in van het SAP-systeem dat u wilt installeren. De id wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
    - **Stacktype:**(deze parameter is alleen van toepassing als u de geconvergeerde sjabloon gebruikt.) Selecteer het SAP NetWeaver-stacktype.
    - **Type besturingssysteem:** selecteer een van de Linux-distributies. Selecteer voor dit voorbeeld **SLES 12.**
    - **Db-type:** selecteer **HANA.**
    - **Sap-systeemgrootte:** voer het aantal SAPS in dat door het nieuwe systeem wordt verstrekt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraagt u uw SAP-technologiepartner of systeemintegrator.
    - **Systeembeschikbaarheid:** selecteer **HA.**
    - **Gebruikersnaam van beheerder en beheerderswachtwoord:** er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om u aan te melden bij de computer.
    - **Nieuw of bestaand subnet:** bepaalt of er een nieuw virtueel netwerk en subnet moeten worden gemaakt of een bestaand subnet moet worden gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **Bestaande**.
    - **Subnet-id:** als u de virtuele machine wilt implementeren in een bestaand VNet waar u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan de naam van dat specifieke subnet. De id ziet er meestal uit **als /subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Handmatige implementatie

> [!IMPORTANT]
> Zorg ervoor dat het besturingssysteem dat u selecteert SAP-gecertificeerd is SAP HANA de specifieke VM-typen die u gebruikt. De lijst met SAP HANA VM-typen en besturingssysteemreleases voor deze VM's kan worden op gezocht in [SAP HANA Gecertificeerde IaaS-platforms.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Zorg ervoor dat u op de details van het vermelde VM-type klikt om de volledige lijst met SAP HANA ondersteunde besturingssysteemreleases voor het specifieke VM-type op te halen
>  

1. Een resourcegroep maken.
1. Maak een virtueel netwerk.
1. Maak een beschikbaarheidsset.
   - Stel het maximale updatedomein in.
1. Maak een load balancer (intern). We raden [u aan load balancer.](../../../load-balancer/load-balancer-overview.md)
   - Selecteer het virtuele netwerk dat u in stap 2 hebt gemaakt.
1. Maak virtuele machine 1.
   - Gebruik een SLES4SAP-afbeelding in de Azure-galerie die wordt ondersteund voor SAP HANA op het VM-type dat u hebt geselecteerd.
   - Selecteer de beschikbaarheidsset die u in stap 3 hebt gemaakt.
1. Maak virtuele machine 2.
   - Gebruik een SLES4SAP-afbeelding in de Azure-galerie die wordt ondersteund voor SAP HANA op het VM-type dat u hebt geselecteerd.
   - Selecteer de beschikbaarheidsset die u in stap 3 hebt gemaakt. 
1. Voeg gegevensschijven toe.

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund op een secundaire IP-configuratie van een NIC in taakverdelingsscenario's. Zie Beperkingen voor [Azure Load Balancer voor meer informatie.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Als u een extra IP-adres nodig hebt voor de VM, implementeert u een tweede NIC.   

> [!Note]
> Wanneer VM's zonder openbare IP-adressen in de back-endpool van interne (geen openbaar IP-adres) Standard Azure load balancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij er aanvullende configuratie wordt uitgevoerd om routering naar openbare eindpunten toe te staan. Zie Public [endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md)(Openbare eindpuntconnectiviteit voor Virtual Machines met behulp van Azure Standard Load Balancer in SAP-scenario's met hoge beschikbaarheid) voor meer informatie over het bereiken van uitgaande connectiviteit.  

1. Als u standard load balancer gebruikt, volgt u deze configuratiestappen:
   1. Maak eerst een front-end-IP-adresgroep:
   
      1. Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
      1. Voer de naam in van de nieuwe front-end-IP-adresgroep (bijvoorbeeld **hana-frontend**).
      1. Stel toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13).**
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-adresgroep is gemaakt, noteer het IP-adres van de groep.
   
   1. Maak vervolgens een back-endpool:
   
      1. Open de load balancer, **selecteer back-endpools** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe back-endpool in (bijvoorbeeld **hana-backend**).
      1. Selecteer **Virtual Network**.
      1. Selecteer **Een virtuele machine toevoegen.**
      1. Selecteer ** Virtuele machine**.
      1. Selecteer de virtuele machines van het SAP HANA cluster en hun IP-adressen.
      1. Selecteer **Toevoegen**.
   
   1. Maak vervolgens een statustest:
   
      1. Open de load balancer, selecteer **statustests** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe statustest in (bijvoorbeeld **hana-hp**).
      1. Selecteer **TCP** als protocol en poort 625 **03.** Houd de **waarde Interval** ingesteld op 5 en de drempelwaarde **Voor** onjuiste status ingesteld op 2.
      1. Selecteer **OK**.
   
   1. Maak vervolgens de taakverdelingsregels:
   
      1. Open het load balancer, selecteer **Taakverdelingsregels** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe load balancer in (bijvoorbeeld **hana-lb**).
      1. Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend,** **hana-backend** en **hana-hp).**
      1. Selecteer **HA-poorten.**
      1. Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
      1. Selecteer **OK**.

1. Als uw scenario het gebruik van basisgegevens load balancer, volgt u deze configuratiestappen:
   1. Maak eerst een front-end-IP-adresgroep:
   
      1. Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe front-end-IP-adresgroep in (bijvoorbeeld **hana-frontend).**
      1. Stel toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13).**
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-adresgroep is gemaakt, noteer het IP-adres van de groep.
   
   1. Maak vervolgens een back-endpool:
   
      1. Open de load balancer, **selecteer back-endpools** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe back-endpool in (bijvoorbeeld **hana-backend**).
      1. Selecteer **Een virtuele machine toevoegen.**
      1. Selecteer de beschikbaarheidsset die u in stap 3 hebt gemaakt.
      1. Selecteer de virtuele machines van het SAP HANA cluster.
      1. Selecteer **OK**.
   
   1. Maak vervolgens een statustest:
   
      1. Open de load balancer, selecteer **statustests** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe statustest in (bijvoorbeeld **hana-hp**).
      1. Selecteer **TCP** als protocol en poort 625 **03.** Houd de **waarde Interval** ingesteld op 5 en de drempelwaarde **Voor** onjuiste status ingesteld op 2.
      1. Selecteer **OK**.
   
   1. Maak SAP HANA 1.0 de taakverdelingsregels:
   
      1. Open de load balancer, selecteer **taakverdelingsregels** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe load balancer in (bijvoorbeeld hana-lb-3 **03** 15).
      1. Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
      1. Houd protocol **ingesteld** op **TCP** en voer poort 3 **03** 15 in.
      1. **Verhoog de time-out voor** inactieve tijd tot 30 minuten.
      1. Zorg ervoor dat **Zwevend IP is ingeschakeld.**
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3 **03** 17.
   
   1. Maak SAP HANA 2.0 de taakverdelingsregels voor de systeemdatabase:
   
      1. Open de load balancer, selecteer **taakverdelingsregels** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe load balancer in (bijvoorbeeld hana-lb-3 **03** 13).
      1. Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
      1. Laat **Protocol** ingesteld op **TCP** en voer poort 3 **03** 13 in.
      1. **Verhoog de time-out voor inactief** naar 30 minuten.
      1. Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort **3 03** 14.
   
   1. Voor SAP HANA 2.0 maakt u eerst de taakverdelingsregels voor de tenantdatabase:
   
      1. Open het load balancer, selecteer **Taakverdelingsregels** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe load balancer in (bijvoorbeeld hana-lb-3 **03** 40).
      1. Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
      1. Laat **Protocol** ingesteld op **TCP** en voer poort 3 **03** 40 in.
      1. **Verhoog de time-out voor inactief** naar 30 minuten.
      1. Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poorten **3 03** 41 en 3 **03** 42.

   Lees het hoofdstuk Verbindingen met [tenantdatabases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) in de handleiding SAP HANA [Tenantdatabases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) of [SAP-notitie 2388694][2388694]voor meer informatie over de vereiste poorten voor SAP HANA.

> [!IMPORTANT]
> Schakel geen TCP-tijdstempels in op Virtuele Azure-VM's die achter de Azure Load Balancer. Als u TCP-tijdstempels inschakelen, mislukken de statustests. Stel parameter **net.ipv4.tcp_timestamps** in **op 0.** Zie [statustests Load Balancer voor meer informatie.](../../../load-balancer/load-balancer-custom-probe-overview.md)
> Zie ook SAP-opmerking [2382421.](https://launchpad.support.sap.com/#/notes/2382421) 

## <a name="create-a-pacemaker-cluster"></a>Een Pacemaker-cluster maken

Volg de stappen in [Pacemaker instellen op SUSE Linux Enterprise Server in Azure om](high-availability-guide-suse-pacemaker.md) een eenvoudige Pacemaker-cluster te maken voor deze HANA-server. U kunt hetzelfde Pacemaker-cluster gebruiken voor SAP HANA en SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>SAP HANA installeren

De stappen in deze sectie gebruiken de volgende voorvoegsels:
- **[A]**: De stap is van toepassing op alle knooppunten.
- **[1]**: De stap is alleen van toepassing op knooppunt 1.
- **[2]**: De stap is alleen van toepassing op knooppunt 2 van het Pacemaker-cluster.

1. **[A]** De schijfindeling instellen: **Logical Volume Manager (LVM).**

   U wordt aangeraden LVM te gebruiken voor volumes die gegevens en logboekbestanden opslaan. In het volgende voorbeeld wordt ervan uitgenomen dat aan de virtuele machines vier gegevensschijven zijn gekoppeld die worden gebruikt om twee volumes te maken.

   Vermeld alle beschikbare schijven:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Voorbeelduitvoer:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak fysieke volumes voor alle schijven die u wilt gebruiken:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak een volumegroep voor de gegevensbestanden. Gebruik één volumegroep voor de logboekbestanden en één voor de gedeelde map van SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak de logische volumes. Er wordt een lineair volume gemaakt wanneer u zonder `lvcreate` de `-i` switch gebruikt. We raden u aan een striped volume te maken voor betere I/O-prestaties en de stripe-grootten uit te lijnen met de waarden die worden beschreven in SAP HANA [VM-opslagconfiguraties.](./hana-vm-operations-storage.md) Het argument moet het aantal onderliggende fysieke volumes zijn en `-i` het argument is de `-I` stripegrootte. In dit document worden twee fysieke volumes gebruikt voor het gegevensvolume, zodat het `-i` schakelargument is ingesteld **op 2**. De stripegrootte voor het gegevensvolume is **256KiB.** Eén fysiek volume wordt gebruikt voor het logboekvolume, zodat er geen of switches expliciet worden gebruikt voor de `-i` `-I` logboekvolumeopdrachten.  

   > [!IMPORTANT]
   > Gebruik de switch en stel deze in op het aantal onderliggende fysieke volumes wanneer u meer dan één fysiek volume gebruikt voor elke `-i` gegevens-, logboek- of gedeelde volumes. Gebruik de `-I` schakelknop om de stripegrootte op te geven bij het maken van een striped volume.  
   > Zie [SAP HANA VM-opslagconfiguraties](./hana-vm-operations-storage.md) voor aanbevolen opslagconfiguraties, waaronder stripe-grootten en het aantal schijven.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Maak de mount-directories en kopieer de UUID van alle logische volumes:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Maak `fstab` vermeldingen voor de drie logische volumes:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Voeg de volgende regel in het `/etc/fstab` bestand in:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   De nieuwe volumes monteren:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** De schijfindeling instellen: **Gewone schijven.**

   Voor demosystemen kunt u uw HANA-gegevens en logboekbestanden op één schijf plaatsen. Maak een partitie op /dev/disk/azure/scsi1/lun0 en maak deze op met xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Voeg deze regel in het bestand /etc/fstab in:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Maak de doelmap en bevestig de schijf:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Hostnaamresolutie instellen voor alle hosts.

   U kunt een DNS-server gebruiken of het bestand /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in het bestand /etc/hosts in. Wijzig het IP-adres en de hostnaam in uw omgeving:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Installeer de SAP HANA met hoge beschikbaarheid:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Volg SAP HANA hoofdstuk 4 SAP HANA [SR Performance Optimized Scenario](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)om een systeemreplicatie te installeren.

1. **[A]** Voer het **hdblcm-programma** uit vanaf de HANA-dvd. Voer de volgende waarden in bij de prompt:
   * Installatie kiezen: Voer **1 in.**
   * Selecteer extra onderdelen voor installatie: Voer **1 in.**
   * Voer het installatiepad [/hana/shared]: Selecteer Enter.
   * Voer Lokale hostnaam [..]: Selecteer Enter.
   * Wilt u extra hosts toevoegen aan het systeem? (y/n) [n]: Selecteer Enter.
   * Voer SAP HANA systeem-id: voer de SID van HANA in, bijvoorbeeld: **HN1.**
   * Voer Exemplaarnummer [00]: voer het nummer van het HANA-exemplaar in. Voer **03 in** als u de Azure-sjabloon hebt gebruikt of de sectie handmatige implementatie van dit artikel hebt gevolgd.
   * Selecteer Databasemodus /Index invoeren [1]: Selecteer Enter.
   * Selecteer Systeemgebruik/Index invoeren [4]: Selecteer de waarde voor systeemgebruik.
   * Voer Locatie van gegevensvolumes [/hana/data/HN1]: Selecteer Enter.
   * Voer Locatie van logboekvolumes [/hana/log/HN1]: Selecteer Enter.
   * Maximale geheugentoewijzing beperken? [n]: Selecteer Enter.
   * Voer de hostnaam van het certificaat in voor host '...' [...]: Selecteer Enter.
   * Sap Host Agent User(sapadm) Password: Voer het gebruikerswachtwoord van de hostagent in.
   * Sap Host Agent-gebruikerswachtwoord (sapadm) bevestigen: voer ter bevestiging nogmaals het gebruikerswachtwoord van de hostagent in.
   * Wachtwoord van systeembeheerder (hdbadm) invoeren: voer het wachtwoord van de systeembeheerder in.
   * Wachtwoord van systeembeheerder (hdbadm) bevestigen: voer ter bevestiging nogmaals het wachtwoord van de systeembeheerder in.
   * Voer System Administrator Home Directory [/usr/sap/HN1/home]: Selecteer Enter.
   * Voer De aanmeldingsshell van de systeembeheerder [/bin/sh]: Selecteer Enter.
   * Voer de gebruikers-id van de systeembeheerder [1001]: Selecteer Enter.
   * Voer de id van de gebruikersgroep (sapsys) [79]: Selecteer Enter.
   * Wachtwoord van databasegebruiker (SYSTEM) invoeren: voer het wachtwoord van de databasegebruiker in.
   * Wachtwoord databasegebruiker (SYSTEM) bevestigen: voer ter bevestiging nogmaals het wachtwoord van de databasegebruiker in.
   * Start u het systeem opnieuw op na het opnieuw opstarten van de machine? [n]: Selecteer Enter.
   * Wilt u doorgaan? (y/n): Valideer de samenvatting. Voer **y** in om door te gaan.

1. **[A]** Upgrade de SAP-hostagent.

   Download het meest recente SAP Host Agent-archief van [het SAP Software Center][sap-swcenter] en voer de volgende opdracht uit om de agent bij te werken. Vervang het pad naar het archief om te wijzen naar het bestand dat u hebt gedownload:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Systeemreplicatie SAP HANA 2.0 configureren

In de stappen in deze sectie worden de volgende voorvoegsels gebruikt:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het Pacemaker-cluster.

1. **[1]** Maak de tenantdatabase.

   Als u een SAP HANA 2.0 of MDC gebruikt, maakt u een tenantdatabase voor uw SAP NetWeaver-systeem. Vervang **NW1 door** de SID van uw SAP-systeem.

   Voer de volgende opdracht uit als <\> adm :

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configureer systeemreplicatie op het eerste knooppunt:

   Back-up van de databases <\> adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopieer de PKI-bestanden van het systeem naar de secundaire site:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Maak de primaire site:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configureer systeemreplicatie op het tweede knooppunt:
    
   Registreer het tweede knooppunt om de systeemreplicatie te starten. Voer de volgende opdracht uit <\> adm :

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Een SAP HANA 1.0-systeemreplicatie configureren

De stappen in deze sectie gebruiken de volgende voorvoegsels:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het Pacemaker-cluster.

1. **[1]** Maak de vereiste gebruikers.

   Voer de volgende opdracht uit als root. Zorg ervoor dat u vetgedrukte tekenreeksen (HANA System ID **HN1** en exemplaarnummer **03**) vervangt door de waarden van uw SAP HANA installatie:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Maak de keystore-vermelding.

   Voer de volgende opdracht uit als root om een nieuwe keystore-vermelding te maken:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1] Een** back-up maken van de database.

   Back-up maken van de databases als hoofdmap:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Als u een installatie met meerdere tenants gebruikt, maakt u ook een back-up van de tenantdatabase:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configureer systeemreplicatie op het eerste knooppunt.

   Maak de primaire site als <\> adm :

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configureer systeemreplicatie op het secundaire knooppunt.

   Registreer de secundaire site als <\> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>De Python-systeemreplicatie hook SAPHanaSR implementeren

Dit is een belangrijke stap om de integratie met het cluster te optimaliseren en de detectie te verbeteren wanneer een cluster-failover nodig is. Het wordt ten zeerste aanbevolen om de PYTHON-hook van SAPHanaSR te configureren.    

1. **[A]** Installeer de "systeemreplicatie hook" van HANA. De hook moet worden geïnstalleerd op beide HANA DB-knooppunten.           

   > [!TIP]
   > Controleer of pakket SAPHanaSR ten minste versie 0.153 is om de Python-hookfunctionaliteit van SAPHanaSR te kunnen gebruiken.       
   > De Python-hook kan alleen worden geïmplementeerd voor HANA 2.0.        

   1. Bereid de hook voor als `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Stop HANA op beide knooppunten. Voer uit <sid \> adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Pas `global.ini` deze aan op elk clusterknooppunt.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** Het cluster vereist configuratie van sudoers op elk clusterknooppunt voor <sid \> adm. In dit voorbeeld wordt dit bereikt door een nieuw bestand te maken. Voer de opdrachten uit als `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```
Zie [HANA HA/DR-providers](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-sr-guide-PerfOpt-12/index.html#_set_up_sap_hana_hadr_providers)instellen SAP HANA meer informatie over de implementatie van de systeemreplicatie hook.  

3. **[A]** Start SAP HANA beide knooppunten. Voer uit <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Controleer** de hookinstallatie. Voer uit <sid \> adm op de actieve HANA-systeemreplicatiesite.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-08 22:18:15.877583 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:18:46.531564 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:21:26.816573 ha_dr_SAPHanaSR SOK

    ```

## <a name="create-sap-hana-cluster-resources"></a>Clusterbronnen SAP HANA maken

Maak eerst de HANA-topologie. Voer de volgende opdrachten uit op een van de Pacemaker-clusterknooppunten:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Maak vervolgens de HANA-resources:

> [!IMPORTANT]
> Uit recente tests zijn situaties gebleken waarin netcat niet meer reageert op aanvragen vanwege achterstand en de beperking van het verwerken van slechts één verbinding. De netcat-resource luistert niet meer naar de Azure Load Balancer-aanvragen en het zwevende IP-adres is niet meer beschikbaar.  
> Voor bestaande Pacemaker-clusters is het in het verleden aanbevolen om netcat te vervangen door socat. Momenteel wordt u aangeraden azure-lb resource agent te gebruiken, die deel uitmaakt van pakketresource-agents, met de volgende pakketversievereisten:
> - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018.a7fb5035-3.30.1 zijn.  
> - Voor SLES 15/15 SP1 moet de versie ten minste resource-agents-4.3.0184.6ee15eb2-4.13.1 zijn.  
>
> Houd er rekening mee dat de wijziging korte downtime vereist.  
> Als voor bestaande Pacemaker-clusters de configuratie al is gewijzigd voor het gebruik van socat, zoals beschreven in [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), is het niet nodig om onmiddellijk over te schakelen naar de azure-lb-resourceagent.


> [!NOTE]
> Dit artikel bevat verwijzingen naar de termen *master* en *slave*, termen die Microsoft niet meer gebruikt. Wanneer deze termen uit de software worden verwijderd, worden deze uit dit artikel verwijderd.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Zorg ervoor dat de status van het cluster ok is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de resources worden uitgevoerd.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Actieve/leessysteemreplicatie van HANA configureren in pacemaker-cluster

Vanaf SAP HANA 2.0 SPS 01 kan SAP actief/lezen-ingeschakeld instellen voor SAP HANA-systeemreplicatie, waarbij de secundaire systemen van SAP HANA-systeemreplicatie actief kunnen worden gebruikt voor lees-intensieve werkbelastingen. Voor de ondersteuning van een dergelijke installatie in een cluster is een tweede virtueel IP-adres vereist waarmee clients toegang kunnen krijgen tot de secundaire database met SAP HANA lezen. Om ervoor te zorgen dat de secundaire replicatiesite nog steeds toegankelijk is nadat een overname is uitgevoerd, moet het cluster het virtuele IP-adres verplaatsen naar de secundaire van de SAPHana-resource.

In deze sectie worden de extra stappen beschreven die nodig zijn voor het beheren van HANA Active/Read ingeschakelde systeemreplicatie in een SUSE-cluster met hoge beschikbaarheid met tweede virtuele IP.    
Voordat u verdergaat, moet u ervoor zorgen dat u een volledig geconfigureerd SUSE-cluster met hoge beschikbaarheid hebt SAP HANA database, zoals beschreven in de bovenstaande segmenten van de documentatie.  

![SAP HANA hoge beschikbaarheid met secundaire lees ingeschakeld](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Aanvullende installatie in Azure load balancer voor actieve/lees-ingeschakelde installatie

Als u wilt doorgaan met aanvullende stappen voor het inrichten van het tweede virtuele IP-adres, moet u ervoor zorgen dat u Azure Load Balancer zoals beschreven in [de sectie Handmatige](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment) implementatie.

1. Voor **standaard** load balancer volgt u de aanvullende stappen hieronder op dezelfde load balancer die u eerder hebt gemaakt.

   a. Maak een tweede front-end-IP-adresgroep: 

   - Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
   - Voer de naam in van de tweede front-end-IP-adresgroep (bijvoorbeeld **hana-secondaryIP).**
   - Stel toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.14).**
   - Selecteer **OK**.
   - Noteer het front-end-IP-adres nadat de nieuwe front-end-IP-adresgroep is gemaakt.

   b. Maak vervolgens een statustest:

   - Open de load balancer, selecteer **statustests** en selecteer **Toevoegen.**
   - Voer de naam van de nieuwe statustest in (bijvoorbeeld **hana-secondaryhp**).
   - Selecteer **TCP** als protocol en poort **62603.** Houd de **waarde Interval** ingesteld op 5 en de drempelwaarde **Voor** onjuiste status ingesteld op 2.
   - Selecteer **OK**.

   c. Maak vervolgens de taakverdelingsregels:

   - Open de load balancer, selecteer **taakverdelingsregels** en selecteer **Toevoegen.**
   - Voer de naam van de nieuwe load balancer in (bijvoorbeeld **hana-secondarylb**).
   - Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-secondaryIP,** **hana-backend** en **hana-secondaryhp).**
   - Selecteer **HA-poorten.**
   - **Verhoog de time-out voor** inactieve tijd tot 30 minuten.
   - Zorg ervoor dat **Zwevend IP is ingeschakeld.**
   - Selecteer **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>HANA-systeemreplicatie configureren die actief/gelezen is ingeschakeld

De stappen voor het configureren van HANA-systeemreplicatie worden beschreven in [SAP HANA 2.0-systeemreplicatie configureren.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication) Als u een secundair scenario met lees ingeschakeld implementeert tijdens het configureren van systeemreplicatie op het tweede knooppunt, voert u de volgende opdracht uit als **adm:**

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Een secundaire resource voor een virtueel IP-adres toevoegen voor een actieve/lees-instelling

Het tweede virtuele IP-adres en de juiste co-locatiebeperking kunnen worden geconfigureerd met de volgende opdrachten:

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
Zorg ervoor dat de clusterstatus ok is en dat alle resources zijn gestart. Het tweede virtuele IP-adres wordt uitgevoerd op de secundaire site, samen met de secundaire SAPHana-resource.

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

In de volgende sectie vindt u de typische set failovertests die u wilt uitvoeren.

Let op het tweede virtuele IP-gedrag tijdens het testen van een HANA-cluster dat is geconfigureerd met secundaire met lees ingeschakeld:

1. Wanneer u **SAPHana_HN1_HDB03** clusterresource migreert naar **hn1-db-1,** wordt het tweede virtuele IP-adres verplaatst naar de andere server **hn1-db-0.** Als u AUTOMATED_REGISTER="false" hebt geconfigureerd en HANA-systeemreplicatie niet automatisch is geregistreerd, wordt het tweede virtuele IP-adres uitgevoerd op **hn1-db-0,** omdat de server beschikbaar is en de clusterservices online zijn.  

2. Bij het testen van een servercrash worden de tweede virtuele IP-resources **(rsc_secip_HN1_HDB03)** en Azure load balancer-poortresource **(rsc_secnc_HN1_HDB03)** uitgevoerd op de primaire server naast de primaire virtuele IP-resources. Terwijl de secundaire server niet werkt, maken de toepassingen die zijn verbonden met de HANA-database met leesmogelijkheden verbinding met de primaire HANA-database. Dit gedrag wordt verwacht omdat u niet wilt dat toepassingen die zijn verbonden met de HANA-database met leesmogelijkheden ontoegankelijk zijn terwijl de secundaire server niet beschikbaar is.
  
3. Wanneer de secundaire server beschikbaar is en de clusterservices online zijn, worden de tweede virtuele IP- en poortresources automatisch verplaatst naar de secundaire server, ook al is HANA-systeemreplicatie mogelijk niet geregistreerd als secundair. U moet ervoor zorgen dat u de secundaire HANA-database registreert als gelezen ingeschakeld voordat u clusterservices op die server start. U kunt de clusterresource van het HANA-exemplaar configureren om de secundaire automatisch te registreren door parameter AUTOMATED_REGISTER=true.       

4. Tijdens failover en terugval kunnen de bestaande verbindingen voor toepassingen, die het tweede virtuele IP-adres gebruiken om verbinding te maken met de HANA-database, worden onderbroken.  

## <a name="test-the-cluster-setup"></a>De clusterinstallatie testen

In deze sectie wordt beschreven hoe u uw installatie kunt testen. Bij elke test wordt ervan uitgenomen dat u de hoofdmap bent en SAP HANA master wordt uitgevoerd op de virtuele machine **hn1-db-0.**

### <a name="test-the-migration"></a>De migratie testen

Voordat u de test start, moet u ervoor zorgen dat Pacemaker geen mislukte actie heeft (via crm_mon -r), er geen onverwachte locatiebeperkingen zijn (bijvoorbeeld leftovers van een migratietest) en dat HANA de synchronisatietoestand heeft, bijvoorbeeld met SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr
Sites    srHook
----------------
SITE2    SOK

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

U kunt het SAP HANA hoofd-knooppunt migreren door de volgende opdracht uit te voeren:

<pre><code>crm resource move msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b> force
</code></pre>

Als u in stelt, moet met deze reeks opdrachten het SAP HANA hoofd-knooppunt en de groep die het virtuele IP-adres bevat, worden gemigreerd naar `AUTOMATED_REGISTER="false"` hn1-db-1.

Zodra de migratie is uitgevoerd, ziet crm_mon -r-uitvoer er als deze uit

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

De SAP HANA resource op hn1-db-0 kan niet worden begonnen als secundair. In dit geval configureert u het HANA-exemplaar als secundair door deze opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Tijdens de migratie worden locatiebeperkingen gemaakt die opnieuw moeten worden verwijderd:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource clear msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

U moet ook de status van de secundaire knooppuntresource ops schonen:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Controleer de status van de HANA-resource met behulp crm_mon -r. Zodra HANA is gestart op hn1-db-0, moet de uitvoer er als deze uitzien

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>De Azure Fencing-agent testen (niet SBD)

U kunt de installatie van de Azure Fencing-agent testen door de netwerkinterface uit te stellen op het knooppunt hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

De virtuele machine moet nu opnieuw worden opgestart of gestopt, afhankelijk van uw clusterconfiguratie.
Als u de instelling instelt op uit, wordt de virtuele machine gestopt `stonith-action` en worden de resources gemigreerd naar de virtuele machine die wordt uitgevoerd.

Nadat u de virtuele machine opnieuw hebt SAP HANA, kan de resource niet als secundair worden starten als u in `AUTOMATED_REGISTER="false"` stelt. In dit geval configureert u het HANA-exemplaar als secundair door deze opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>SBD-fencing testen

U kunt de installatie van SBD testen door het inquisitorproces te stoppen.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Clusterknooppunt hn1-db-0 moet opnieuw worden opgestart. De Pacemaker-service kan later niet meer worden gestart. Zorg ervoor dat u het opnieuw start.

### <a name="test-a-manual-failover"></a>Een handmatige failover testen

U kunt een handmatige failover testen door de service op het `pacemaker` knooppunt hn1-db-0 te stoppen:

<pre><code>service pacemaker stop
</code></pre>

Na de failover kunt u de service opnieuw starten. Als u in stelt, kan SAP HANA resource op het `AUTOMATED_REGISTER="false"` knooppunt hn1-db-0 niet starten als secundair. In dit geval configureert u het HANA-exemplaar als secundair door deze opdracht uit te voeren:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-tests

> [!IMPORTANT]
> Zorg ervoor dat het besturingssysteem dat u selecteert SAP-gecertificeerd is SAP HANA de specifieke VM-typen die u gebruikt. De lijst met SAP HANA VM-typen en besturingssysteemreleases voor deze VM's kan worden op gezocht in [SAP HANA Gecertificeerde IaaS-platforms.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Zorg ervoor dat u op de details van het vermelde VM-type klikt om de volledige lijst met SAP HANA ondersteunde besturingssysteemreleases voor het specifieke VM-type op te halen

Voer alle testcases uit die worden vermeld in de handleiding SAP HANA SR Performance Optimized Scenario of SAP HANA SR Cost Optimized Scenario, afhankelijk van uw use-case. U vindt de handleidingen op de [pagina met best practices voor SLES for SAP.][sles-for-sap-bp]

De volgende tests zijn een kopie van de testbeschrijvingen van de handleiding SAP HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server for SAP Applications 12 SP1. Lees de handleiding zelf altijd voor een bijgewerkte versie. Zorg er altijd voor dat HANA synchroon is voordat u de test start en zorg er ook voor dat de Pacemaker-configuratie juist is.

In de volgende testbeschrijvingen wordt ervan uit PREFER_SITE_TAKEOVER="true" en AUTOMATED_REGISTER="false".
OPMERKING: De volgende tests zijn ontworpen om opeenvolgend te worden uitgevoerd en zijn afhankelijk van de exit-status van de voorgaande tests.

1. TEST 1: PRIMAIRE DATABASE STOPPEN OP KNOOPPUNT 1

   Resourcetoestand voordat u de test start:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <\> adm op knooppunt hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker moet het gestopte HANA-exemplaar en de failover naar het andere knooppunt detecteren. Zodra de failover is uitgevoerd, wordt het HANA-exemplaar op knooppunt hn1-db-0 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als secundaire HANA.

   Voer de volgende opdrachten uit om knooppunt hn1-db-0 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resourcetoestand na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: PRIMAIRE DATABASE STOPPEN OP KNOOPPUNT 2

   Resourcetoestand voordat u de test start:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende opdrachten uit als <\> adm op knooppunt hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker moet het gestopte HANA-exemplaar en de failover naar het andere knooppunt detecteren. Zodra de failover is uitgevoerd, wordt het HANA-exemplaar op knooppunt hn1-db-1 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als secundaire HANA.

   Voer de volgende opdrachten uit om knooppunt hn1-db-1 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcetoestand na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: PRIMAIRE DATABASE CRASHEN OP KNOOPPUNT

   Resourcetoestand voordat u de test start:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <\> adm op knooppunt hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker moet het uitvallen van het HANA-exemplaar en de failover naar het andere knooppunt detecteren. Zodra de failover is uitgevoerd, wordt het HANA-exemplaar op knooppunt hn1-db-0 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als secundaire HANA.

   Voer de volgende opdrachten uit om knooppunt hn1-db-0 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resourcetoestand na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: PRIMAIRE DATABASE CRASHEN OP KNOOPPUNT 2

   Resourcetoestand voordat u de test start:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende opdrachten uit <\> adm op knooppunt hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker moet het omgebrachte HANA-exemplaar detecteren en een failover naar het andere knooppunt maken. Zodra de failover is uitgevoerd, wordt het HANA-exemplaar op knooppunt hn1-db-1 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als secundaire HANA.

   Voer de volgende opdrachten uit om knooppunt hn1-db-1 als secundaire te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcetoestand na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: CRASH PRIMARY SITE NODE (NODE 1)

   Resourcetoestand voordat de test wordt uitgevoerd:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als root op knooppunt hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet het afgesloten clusterknooppunt detecteren en het knooppunt omkeren. Zodra het knooppunt is omheind, activeert Pacemaker een overname van het HANA-exemplaar. Wanneer het omheind knooppunt opnieuw wordt opgestart, wordt Pacemaker niet automatisch opgestart.

   Voer de volgende opdrachten uit om Pacemaker te starten, de SBD-berichten voor knooppunt hn1-db-0 op te schonen, knooppunt hn1-db-0 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resourcetoestand na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: CRASH SECONDARY SITE NODE (NODE 2)

   Resourcetoestand voordat de test wordt uitgevoerd:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende opdrachten uit als root op knooppunt hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet het afgesloten clusterknooppunt detecteren en het knooppunt omkeren. Zodra het knooppunt is omheind, activeert Pacemaker een overname van het HANA-exemplaar. Wanneer het omheind knooppunt opnieuw wordt opgestart, wordt Pacemaker niet automatisch opgestart.

   Voer de volgende opdrachten uit om Pacemaker te starten, de SBD-berichten voor knooppunt hn1-db-1 op te schonen, knooppunt hn1-db-1 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resource status na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: DE SECUNDAIRE DATABASE STOPPEN OP KNOOPPUNT 2

   Resourcetoestand voordat u de test start:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <\> adm op knooppunt hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker detecteert het gestopte HANA-exemplaar en markeer de resource als mislukt op knooppunt hn1-db-1. Pacemaker moet het HANA-exemplaar automatisch opnieuw opstarten. Voer de volgende opdracht uit om de mislukte status op te schonen.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resource status na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: DE SECUNDAIRE DATABASE CRASHEN OP KNOOPPUNT 2

   Resourcetoestand voordat u de test start:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <\> adm op knooppunt hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker detecteert het uitgevallen HANA-exemplaar en markeer de resource als mislukt op knooppunt hn1-db-1. Voer de volgende opdracht uit om de status Mislukt op te schonen. Pacemaker moet het HANA-exemplaar vervolgens automatisch opnieuw opstarten.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcetoestand na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: SECUNDAIR SITE-KNOOPPUNT CRASHEN (NODE 2) MET SECUNDAIRE HANA-DATABASE

   Resourcetoestand voordat u de test start:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als root op knooppunt hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet het afgesloten clusterknooppunt detecteren en het knooppunt omkeren. Wanneer het omheind knooppunt opnieuw wordt opgestart, wordt Pacemaker niet automatisch opgestart.

   Voer de volgende opdrachten uit om Pacemaker te starten, de SBD-berichten voor knooppunt hn1-db-1 op te schonen en de mislukte resource op te schonen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcetoestand na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
