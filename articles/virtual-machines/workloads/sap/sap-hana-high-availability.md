---
title: Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure op SLES | Microsoft Docs
description: Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure op SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 2783d9dc7151190857d870b5493465884b82ffcc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957262"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure op SUSE Linux Enterprise Server

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

Voor on-premises ontwikkeling kunt u gebruikmaken van de HANA-systeem replicatie of het gebruik van gedeelde opslag om hoge Beschik baarheid voor SAP HANA tot stand te brengen.
Op virtuele machines van Azure (Vm's) is HANA-systeem replicatie op Azure momenteel de enige ondersteunde functie voor hoge Beschik baarheid. SAP HANA replicatie bestaat uit één primair knoop punt en ten minste één secundair knoop punt. Wijzigingen in de gegevens op het primaire knoop punt worden synchroon of asynchroon gerepliceerd naar het secundaire knoop punt.

In dit artikel wordt beschreven hoe u de virtuele machines implementeert en configureert, het cluster raamwerk installeert en SAP HANA systeem replicatie installeert en configureert.
In de voorbeeld configuraties worden installatie opdrachten, instantie nummer **03** en Hana-systeem-id **HN1** gebruikt.

Lees eerst de volgende SAP-opmerkingen en-documenten:

* SAP-opmerking [1928533], die:
  * De lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software.
  * Belang rijke informatie over de capaciteit van Azure VM-grootten.
  * De ondersteunde SAP-software en besturings systemen (OS) en database combinaties.
  * De vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure.
* SAP Note [2015553] bevat de vereisten voor SAP-ondersteunde SAP-software-implementaties in Azure.
* SAP Note [2205917] heeft aanbevolen instellingen voor het besturings systeem voor SuSE Linux Enterprise Server voor SAP-toepassingen.
* SAP Note [1944799] heeft SAP Hana richt lijnen voor SuSE Linux Enterprise Server voor SAP-toepassingen.
* SAP Note [2178632] bevat gedetailleerde informatie over alle metrische bewakings gegevens die worden gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1984787] bevat algemene informatie over SuSE Linux Enterprise Server 12.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* SAP Note [401162] bevat informatie over het vermijden van ' adres al in gebruik ' bij het instellen van Hana-systeem replicatie.
* De [SAP Community-wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) bevat alle vereiste SAP-notities voor Linux.
* [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure virtual machines planning en implementatie van SAP op Linux][planning-guide] -hand leiding.
* [Azure virtual machines-implementatie voor SAP op Linux][deployment-guide] (dit artikel).
* [Azure virtual machines DBMS-implementatie voor SAP in Linux][dbms-guide] -hand leiding.
* [SUSE Linux Enterprise Server voor SAP-toepassingen 12 SP3 best practices-hand leidingen][sles-for-sap-bp]
  * Instellen van een SAP HANA SR-infra structuur voor geoptimaliseerde prestaties (SLES for SAP-toepassingen 12 SP1). De hand leiding bevat alle vereiste informatie voor het instellen van SAP HANA systeem replicatie voor on-premises ontwikkeling. Gebruik deze hand leiding als basis lijn.
  * Instellen van een SAP HANA SR-kosten infrastructuur (SLES for SAP-toepassingen 12 SP1)

## <a name="overview"></a>Overzicht

SAP HANA is geïnstalleerd op twee virtuele machines voor een hoge Beschik baarheid. De gegevens worden gerepliceerd met behulp van HANA-systeem replicatie.

![Overzicht van SAP HANA hoge Beschik baarheid](./media/sap-hana-high-availability/ha-suse-hana.png)

Bij de installatie van de SAP HANA-systeem replicatie worden een specifieke virtuele hostnaam en een virtueel IP-adres gebruikt. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. De volgende lijst bevat de configuratie van de load balancer:

* Front-end-configuratie: IP-adres 10.0.0.13 voor HN1-db
* Back-end-configuratie: verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeem replicatie
* Poort testen: poort 62503
* Taakverdelings regels: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implementeren voor Linux

De resource agent voor SAP HANA is opgenomen in SUSE Linux Enterprise Server voor SAP-toepassingen.
De Azure Marketplace bevat een installatie kopie voor SUSE Linux Enterprise Server voor SAP-toepassingen 12 die u kunt gebruiken om nieuwe virtuele machines te implementeren.

### <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U kunt een van de Quick Start-sjablonen gebruiken die zich op GitHub bevinden voor het implementeren van alle vereiste resources. De sjabloon implementeert de virtuele machines, de load balancer, de beschikbaarheidsset, enzovoort.
Voer de volgende stappen uit om de sjabloon te implementeren:

1. Open de [Database sjabloon][template-multisid-db] of de [geconvergeerde sjabloon][template-converged] op de Azure Portal. 
    De database sjabloon maakt alleen de taakverdelings regels voor een Data Base. De geconvergeerde sjabloon maakt ook de regels voor taak verdeling voor een exemplaar van ASCS/SCS en ERS (alleen voor Linux). Als u van plan bent om een SAP NetWeaver-systeem te installeren en u het ASCS/SCS-exemplaar op dezelfde computers wilt installeren, gebruikt u de [geconvergeerde sjabloon][template-converged].

1. Voer de volgende parameters in:
    - **SAP-systeem-id**: Voer de SAP-systeem-id in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als een voor voegsel voor de resources die worden geïmplementeerd.
    - **Stack type**: (deze para meter is alleen van toepassing als u de geconvergeerde sjabloon gebruikt.) Selecteer het stack type SAP net-Weaver.
    - **Type besturings systeem**: Selecteer een van de Linux-distributies. Selecteer voor dit voor beeld **SLES 12**.
    - **Db-type**: Selecteer **Hana**.
    - **SAP-systeem grootte**: Voer het aantal sap's in dat het nieuwe systeem moet bieden. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
    - **Systeem beschikbaarheid**: Selecteer **ha**.
    - **Gebruikers naam en wacht woord** beheerder: er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om u aan te melden bij de computer.
    - **Nieuw of bestaand subnet**: Hiermee wordt bepaald of er een nieuw virtueel netwerk en subnet moet worden gemaakt of dat er een bestaand subnet wordt gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
    - **Subnet-id**: als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de id van het specifieke subnet benoemen. De ID is doorgaans hetzelfde als **/Subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Handmatige implementatie

> [!IMPORTANT]
> Zorg ervoor dat het besturings systeem dat u selecteert, SAP gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. De lijst met SAP HANA gecertificeerde VM-typen en versies van het besturings systeem voor die apparaten kunnen worden opgezocht in [SAP Hana gecertificeerde IaaS-platformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Zorg ervoor dat u in de details van het opgegeven VM-type klikt om de volledige lijst met SAP HANA ondersteunde versies van het besturings systeem te verkrijgen voor het specifieke VM-type
>  

1. Een resourcegroep maken.
1. Maak een virtueel netwerk.
1. Maak een beschikbaarheidsset.
   - Stel het maximale update domein in.
1. Maak een load balancer (intern). [Standaard Load Balancer](../../../load-balancer/load-balancer-overview.md)worden aanbevolen.
   - Selecteer het virtuele netwerk dat u in stap 2 hebt gemaakt.
1. Virtuele machine 1 maken.
   - Gebruik een SLES4SAP-installatie kopie in de Azure-galerie die wordt ondersteund voor SAP HANA op het VM-type dat u hebt geselecteerd.
   - Selecteer de beschikbaarheidsset die u hebt gemaakt in stap 3.
1. Maak de virtuele machine 2.
   - Gebruik een SLES4SAP-installatie kopie in de Azure-galerie die wordt ondersteund voor SAP HANA op het VM-type dat u hebt geselecteerd.
   - Selecteer de beschikbaarheidsset die u hebt gemaakt in stap 3. 
1. Voeg gegevens schijven toe.

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.   

> [!Note]
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  

1. Als u standaard load balancer gebruikt, volgt u deze configuratie stappen:
   1. Maak eerst een front-end-IP-adres groep:
   
      1. Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Hana-frontend**).
      1. Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13**).
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.
   
   1. Maak vervolgens een back-end-pool:
   
      1. Open de load balancer, selecteer **back-endservers** en selecteer **toevoegen**.
      1. Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Hana-back-end**).
      1. Selecteer **Virtual Network**.
      1. Selecteer **een virtuele machine toevoegen**.
      1. Selecteer * * virtuele machine * *.
      1. Selecteer de virtuele machines van het SAP HANA cluster en de bijbehorende IP-adressen.
      1. Selecteer **Toevoegen**.
   
   1. Maak vervolgens een status test:
   
      1. Open de load balancer, selecteer **status controles** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe status test (bijvoorbeeld **Hana-HP**).
      1. Selecteer **TCP** als protocol en poort 625 **03**. Laat de waarde voor **interval** ingesteld op 5 en de drempel waarde voor een **onjuiste status** ingesteld op 2.
      1. Selecteer **OK**.
   
   1. Maak vervolgens de regels voor taak verdeling:
   
      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **Hana-lb**).
      1. Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-frontend**, **Hana-back-end** en **Hana-HP**).
      1. Selecteer **ha-poorten**.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.

1. Als uw scenario gebruikmaakt van basis load balancer, volgt u deze configuratie stappen:
   1. Maak eerst een front-end-IP-adres groep:
   
      1. Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Hana-frontend**).
      1. Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13**).
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.
   
   1. Maak vervolgens een back-end-pool:
   
      1. Open de load balancer, selecteer **back-endservers** en selecteer **toevoegen**.
      1. Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Hana-back-end**).
      1. Selecteer **een virtuele machine toevoegen**.
      1. Selecteer de beschikbaarheidsset die u hebt gemaakt in stap 3.
      1. Selecteer de virtuele machines van het SAP HANA cluster.
      1. Selecteer **OK**.
   
   1. Maak vervolgens een status test:
   
      1. Open de load balancer, selecteer **status controles** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe status test (bijvoorbeeld **Hana-HP**).
      1. Selecteer **TCP** als protocol en poort 625 **03**. Laat de waarde voor **interval** ingesteld op 5 en de drempel waarde voor een **onjuiste status** ingesteld op 2.
      1. Selecteer **OK**.
   
   1. Maak voor SAP HANA 1,0 de regels voor taak verdeling:
   
      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 15).
      1. Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-** front-end).
      1. Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 15 in.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3 **03** 17.
   
   1. Voor SAP HANA 2,0 maakt u de regels voor taak verdeling voor de systeem database:
   
      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 13).
      1. Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-** front-end).
      1. Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 13 in.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3 **03** 14.
   
   1. Voor SAP HANA 2,0 maakt u eerst de regels voor taak verdeling voor de Tenant database:
   
      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 40).
      1. Selecteer het frontend-IP-adres, de back-endadresgroep en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-front-end**).
      1. Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 40 in.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor de poorten 3 **03** 41 en 3 **03** 42.

   Lees voor meer informatie over de vereiste poorten voor SAP HANA de hoofdstuk [verbindingen met Tenant databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) in de hand leiding voor [SAP Hana Tenant-data bases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) of [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.
> Zie ook SAP-opmerking [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Een Pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](high-availability-guide-suse-pacemaker.md) om een basis pacemaker-cluster voor deze Hana-server te maken. U kunt hetzelfde pacemaker-cluster gebruiken voor SAP HANA en SAP NetWeaver (A) SCS.

## <a name="install-sap-hana"></a>SAP HANA installeren

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:
- **[A]**: de stap is van toepassing op alle knoop punten.
- **[1]**: de stap is alleen van toepassing op knoop punt 1.
- **[2]**: de stap is alleen van toepassing op knoop punt 2 van het pacemaker-cluster.

1. **[A]** de schijf indeling instellen: **Logical Volume Manager (LVM)**.

   We raden u aan LVM te gebruiken voor volumes die gegevens en logboek bestanden opslaan. In het volgende voor beeld wordt ervan uitgegaan dat aan de virtuele machines vier gegevens schijven zijn gekoppeld die worden gebruikt om twee volumes te maken.

   Alle beschik bare schijven weer geven:

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

   Maak een volume groep voor de gegevens bestanden. Gebruik één volume groep voor de logboek bestanden en een voor de gedeelde map van SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak de logische volumes. Er wordt een lineair volume gemaakt wanneer u `lvcreate` zonder de `-i` Switch gebruikt. U wordt aangeraden een striped volume te maken voor betere I/O-prestaties en de Stripe-groottes af te stemmen op de waarden die worden beschreven in [SAP Hana VM-opslag configuraties](./hana-vm-operations-storage.md). Het `-i` argument moet het aantal onderliggende fysieke volumes zijn en het `-I` argument is de Stripe-grootte. In dit document worden twee fysieke volumes gebruikt voor het gegevens volume, dus is het `-i` argument switch ingesteld op **2**. De Stripe-grootte voor het gegevens volume is **256KiB**. Er wordt één fysiek volume gebruikt voor het logboek volume, dus nee `-i` of `-I` switches worden expliciet gebruikt voor de volume opdrachten van het logboek.  

   > [!IMPORTANT]
   > Gebruik de `-i` Switch en stel deze in op het nummer van het onderliggende fysieke volume wanneer u meer dan één fysiek volume gebruikt voor elke gegevens, elk logboek of gedeelde volumes. Gebruik de `-I` Schakel optie om de Stripe-grootte op te geven bij het maken van een striped volume.  
   > Zie [SAP Hana VM-opslag configuraties](./hana-vm-operations-storage.md) voor aanbevolen opslag configuraties, inclusief Stripe-grootte en aantal schijven.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Maak de koppelings directory's en kopieer de UUID van alle logische volumes:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   `fstab`Vermeldingen maken voor de drie logische volumes:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Voeg de volgende regel toe aan het `/etc/fstab` bestand:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   De nieuwe volumes koppelen:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** de schijf indeling instellen: **platte schijven**.

   Voor demo systemen kunt u uw HANA-gegevens en-logboek bestanden op één schijf plaatsen. Maak een partitie op/dev/disk/Azure/SCSI1/lun0 en Format teer deze met xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Voeg deze regel toe aan het bestand/etc/fstab-bestand:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Maak de doel directory en koppel de schijf:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A] het** omzetten van de hostnaam voor alle hosts instellen.

   U kunt een DNS-server gebruiken of het bestand/etc/hosts-bestand op alle knoop punten wijzigen. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels toe aan het bestand/etc/hosts-bestand. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** de SAP Hana Maxi maal beschik bare pakketten installeren:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Als u SAP HANA systeem replicatie wilt installeren, volgt u hoofd stuk 4 van de [hand leiding](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)voor het SAP Hana van SR-prestaties.

1. **[A]** Voer het **hdblcm** -programma uit vanaf de Hana-DVD. Voer de volgende waarden in bij de prompt:
   * Installatie kiezen: Voer **1** in.
   * Selecteer extra onderdelen voor installatie: Voer **1** in.
   * Voer het installatiepad [/Hana/Shared] in: Selecteer ENTER.
   * Voer de naam van de lokale host [..] in: Selecteer ENTER.
   * Wilt u extra hosts toevoegen aan het systeem? (j/n) [n]: Selecteer ENTER.
   * Voer SAP HANA systeem-ID in: Voer de SID van HANA in, bijvoorbeeld: **HN1**.
   * Voer het exemplaar nummer [00] in: Voer het HANA-exemplaar nummer in. Voer **03** in als u de Azure-sjabloon hebt gebruikt of als u de sectie hand matige implementatie in dit artikel hebt gevolgd.
   * Database modus selecteren/index invoeren [1]: Selecteer ENTER.
   * Systeem gebruik selecteren/index invoeren [4]: Selecteer de waarde voor het systeem gebruik.
   * Voer de locatie van de gegevens volumes [/hana/data/HN1] in: Selecteer ENTER.
   * Locatie van logboek volumes invoeren [/hana/log/HN1]: Selecteer ENTER.
   * Maximale geheugen toewijzing beperken? [n]: Selecteer ENTER.
   * Voer de hostnaam van het certificaat in voor de host... [...]: Selecteer ENTER.
   * Voer het wacht woord voor de gebruiker van de SAP host agent (sapadm) in: Voer het wacht woord van de Hosta Gent in.
   * Bevestig het wacht woord van de gebruiker van de SAP-Hosta Gent (sapadm): Voer het wacht woord van de Hosta Gent in om het te bevestigen
   * Voer het wacht woord voor de systeem beheerder (hdbadm) in: Voer het beheerders wachtwoord voor het systeem in.
   * Wacht woord van systeem beheerder (hdbadm) bevestigen: Voer het wacht woord van de systeem beheerder nogmaals in om dit te bevestigen.
   * Voer de basismap van de systeem beheerder in [/usr/sap/HN1/home]: Selecteer ENTER.
   * Voer aanmeldings shell van systeem beheerder in [/bin/sh]: Selecteer ENTER.
   * Voer de gebruikers-ID van de systeem beheerder in [1001]: Selecteer ENTER.
   * ID van gebruikers groep opgeven (sapsys) [79]: Selecteer ENTER.
   * Voer het wacht woord voor de database gebruiker (systeem) in: Voer het wacht woord voor de database gebruiker in.
   * Wacht woord van database gebruiker (systeem) bevestigen: Voer het wacht woord voor de database gebruiker opnieuw in om dit te bevestigen.
   * Systeem opnieuw opstarten nadat de computer opnieuw is opgestart? [n]: Selecteer ENTER.
   * Wilt u doorgaan? (j/n): Valideer de samen vatting. Voer **y** in om door te gaan.

1. **[A]** de SAP Hosta Gent bijwerken.

   Down load het meest recente SAP host agent-archief vanuit het [SAP Software Center][sap-swcenter] en voer de volgende opdracht uit om de agent bij te werken. Vervang het pad naar het archief zodat dit verwijst naar het bestand dat u hebt gedownload:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0-systeem replicatie configureren

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:

* **[A]**: de stap is van toepassing op alle knoop punten.
* **[1]**: de stap is alleen van toepassing op knoop punt 1.
* **[2]**: de stap is alleen van toepassing op knoop punt 2 van het pacemaker-cluster.

1. **[1]** de Tenant database maken.

   Als u SAP HANA 2,0 of MDC gebruikt, maakt u een Tenant database voor uw SAP net-Weaver-systeem. Vervang **NW1** door de sid van uw SAP-systeem.

   Voer de volgende opdracht uit als <hanasid \> adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** systeem replicatie op het eerste knoop punt configureren:

   Maak een back-up van de data bases als <hanasid \> adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopieer de PKI-bestanden van het systeem naar de secundaire site:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   De primaire site maken:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** systeem replicatie op het tweede knoop punt configureren:
    
   Registreer het tweede knoop punt om de systeem replicatie te starten. Voer de volgende opdracht uit als <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1,0-systeem replicatie configureren

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:

* **[A]**: de stap is van toepassing op alle knoop punten.
* **[1]**: de stap is alleen van toepassing op knoop punt 1.
* **[2]**: de stap is alleen van toepassing op knoop punt 2 van het pacemaker-cluster.

1. **[1]** de vereiste gebruikers maken.

   Voer de volgende opdracht uit als basis. Zorg ervoor dat u vetgedrukte teken reeksen (HANA-systeem-ID **HN1** en instantie nummer **03**) vervangt door de waarden van uw SAP Hana-installatie:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** de vermelding voor het opslag item maken.

   Voer de volgende opdracht uit als basis voor het maken van een nieuwe vermelding in de opslag locatie:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Maak een back-up van de data base.

   Back-up van de data bases maken als hoofdmap:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Als u een installatie met meerdere tenants gebruikt, moet u ook een back-up maken van de Tenant database:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configureer de systeem replicatie op het eerste knoop punt.

   Maak de primaire site als <hanasid \> adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configureer de systeem replicatie op het secundaire knoop punt.

   Registreer de secundaire site als <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA cluster resources maken

Maak eerst de HANA-topologie. Voer de volgende opdrachten uit op een van de pacemaker-cluster knooppunten:

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
> Recente tests hebben getoonde situaties, waarbij netcat niet meer reageert op aanvragen als gevolg van achterstand en de beperking van het verwerken van slechts één verbinding. De netcat-resource stopt met Luis teren naar de Azure Load Balancer-aanvragen en het zwevende IP-adres is niet meer beschikbaar.  
> Voor bestaande pacemaker-clusters wordt geadviseerd in de laatste Vervang netcat met socat. Momenteel raden we u aan Azure-lb resource agent te gebruiken, die deel uitmaakt van pakket resource-agents, met de volgende pakket versie vereisten:
> - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018. a7fb5035-3.30.1 zijn.  
> - Voor SLES 15/15 SP1 moet de versie ten minste resource-agents-4.3.0184.6 ee15eb2-4.13.1 zijn.  
>
> Houd er rekening mee dat voor de wijziging korte uitval tijd nodig is.  
> Voor bestaande pacemaker-clusters geldt dat als de configuratie al is gewijzigd in het gebruik van socat, zoals beschreven in [Azure Load-Balancer-detectie beveiliging](https://www.suse.com/support/kb/doc/?id=7024128), u niet onmiddellijk over de resource-agent van Azure lb hoeft te scha kelen.

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

Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welke knoop punten de resources worden uitgevoerd.

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

## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

In deze sectie wordt beschreven hoe u de installatie kunt testen. Bij elke test wordt ervan uitgegaan dat u root bent en de SAP HANA-Master wordt uitgevoerd op de virtuele machine **HN1-DB-0** .

### <a name="test-the-migration"></a>De migratie testen

Voordat u met de test begint, moet u ervoor zorgen dat pacemaker geen mislukte actie heeft (via crm_mon-r), er zijn geen onverwachte locatie beperkingen (bijvoorbeeld rests van een migratie test) en de HANA-synchronisatie status, bijvoorbeeld met SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

U kunt het knoop punt SAP HANA Master migreren door de volgende opdracht uit te voeren:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Als u `AUTOMATED_REGISTER="false"` deze reeks opdrachten instelt, moet de SAP Hana hoofd knooppunt en de groep met het virtuele IP-adres naar HN1-db-1 worden gemigreerd.

Nadat de migratie is voltooid, ziet de crm_mon-r-uitvoer er als volgt uit:

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

De SAP HANA resource op HN1-DB-0 kan niet worden gestart als secundair. In dit geval configureert u het HANA-exemplaar als secundair door de volgende opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

De migratie maakt locatie beperkingen die opnieuw moeten worden verwijderd:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

U moet ook de status van de secundaire knooppunt resource opschonen:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Bewaak de status van de HANA-bron met behulp van crm_mon-r. Zodra HANA is gestart op HN1-DB-0, ziet de uitvoer er als volgt uit:

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

### <a name="test-the-azure-fencing-agent-not-sbd"></a>De Azure omheinings agent testen (niet SBD)

U kunt de installatie van de Azure omheinings agent testen door de netwerk interface op het knoop punt HN1-DB-0 uit te scha kelen:

<pre><code>sudo ifdown eth0
</code></pre>

Afhankelijk van de cluster configuratie moet de virtuele machine nu opnieuw worden opgestart of gestopt.
Als u de `stonith-action` instelling instelt op uit, wordt de virtuele machine gestopt en worden de resources gemigreerd naar de actieve virtuele machine.

Nadat u de virtuele machine opnieuw hebt gestart, kan de SAP HANA resource niet worden gestart als secundaire als u deze instelt `AUTOMATED_REGISTER="false"` . In dit geval configureert u het HANA-exemplaar als secundair door de volgende opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>SBD-omheining testen

U kunt de installatie van SBD testen door het Inquisitor-proces te doden.

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

Cluster knooppunt HN1-DB-0 moet opnieuw worden opgestart. De pacemaker-service wordt later mogelijk niet gestart. Zorg ervoor dat u het opnieuw start.

### <a name="test-a-manual-failover"></a>Een hand matige failover testen

U kunt een hand matige failover testen door de `pacemaker` service op het knoop punt HN1-DB-0 te stoppen:

<pre><code>service pacemaker stop
</code></pre>

Na de failover kunt u de service opnieuw starten. Als u instelt `AUTOMATED_REGISTER="false"` , kan de SAP Hana resource op het knoop punt HN1-DB-0 niet worden gestart als secundair. In dit geval configureert u het HANA-exemplaar als secundair door de volgende opdracht uit te voeren:

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
> Zorg ervoor dat het besturings systeem dat u selecteert, SAP gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. De lijst met SAP HANA gecertificeerde VM-typen en versies van het besturings systeem voor die apparaten kunnen worden opgezocht in [SAP Hana gecertificeerde IaaS-platformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Zorg ervoor dat u in de details van het opgegeven VM-type klikt om de volledige lijst met SAP HANA ondersteunde versies van het besturings systeem te verkrijgen voor het specifieke VM-type

Voer alle test cases uit die worden vermeld in het scenario SAP HANA SR-geoptimaliseerde prestaties of de SAP HANA SR cost Optimization scenario, afhankelijk van uw use-case. U kunt de hand leidingen vinden op de [pagina SLES for SAP Best practices][sles-for-sap-bp].

De volgende tests zijn een kopie van de test beschrijvingen van het SAP HANA SR-prestatie geoptimaliseerde scenario SUSE Linux Enterprise Server voor de hand leiding voor SAP-toepassingen 12 SP1. Lees voor een actuele versie altijd ook de hand leiding zelf. Zorg er altijd voor dat HANA synchroon is voordat u de test start en Controleer ook of de pacemaker-configuratie juist is.

In de volgende test beschrijvingen wordt aangenomen PREFER_SITE_TAKEOVER = "True" en AUTOMATED_REGISTER = "false".
Opmerking: de volgende tests zijn ontworpen om sequentieel te worden uitgevoerd en zijn afhankelijk van de afsluit status van de voor gaande tests.

1. TEST 1: DE PRIMAIRE DATA BASE OP HET KNOOP PUNT 1 STOPPEN

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <hanasid \> adm op het knoop punt HN1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker moet het gestopte HANA-exemplaar en de failover naar het andere knoop punt detecteren. Zodra de failover is uitgevoerd, wordt de HANA-instantie op het knoop punt HN1-DB-0 gestopt omdat het knoop punt door pacemaker niet automatisch als HANA secundair wordt geregistreerd.

   Voer de volgende opdrachten uit om het knoop punt HN1-DB-0 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resource status na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: DE PRIMAIRE DATA BASE OP KNOOP PUNT 2 STOPPEN

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende opdrachten uit als <hanasid \> adm op het knoop punt HN1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker moet het gestopte HANA-exemplaar en de failover naar het andere knoop punt detecteren. Zodra de failover is uitgevoerd, wordt de HANA-instantie op het knoop punt HN1-db-1 gestopt omdat het knoop punt door pacemaker niet automatisch als HANA secundair wordt geregistreerd.

   Voer de volgende opdrachten uit om het knoop punt HN1-db-1 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
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

1. TEST 3: CRASH PRIMARY DATA BASE ON NODE

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <hanasid \> adm op het knoop punt HN1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker moet het gedode HANA-exemplaar en de failover naar het andere knoop punt detecteren. Zodra de failover is uitgevoerd, wordt de HANA-instantie op het knoop punt HN1-DB-0 gestopt omdat het knoop punt door pacemaker niet automatisch als HANA secundair wordt geregistreerd.

   Voer de volgende opdrachten uit om het knoop punt HN1-DB-0 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resource status na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: CRASH PRIMARY DATA BASE ON NODE 2

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende opdrachten uit als <hanasid \> adm op het knoop punt HN1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker moet het gedode HANA-exemplaar en de failover naar het andere knoop punt detecteren. Zodra de failover is uitgevoerd, wordt de HANA-instantie op het knoop punt HN1-db-1 gestopt omdat het knoop punt door pacemaker niet automatisch als HANA secundair wordt geregistreerd.

   Voer de volgende opdrachten uit om het knoop punt HN1-db-1 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
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

1. TEST 5: VASTLOPEND PRIMAIR SITE KNOOPPUNT (KNOOP PUNT 1)

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als hoofd knooppunt op het knoop punt HN1-DB-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet het gegedoode cluster knooppunt detecteren en het knoop punt deomheining. Zodra het knoop punt is geomheiningd, wordt door pacemaker een overname geactiveerd van het HANA-exemplaar. Wanneer het geomheiningde knoop punt opnieuw wordt opgestart, wordt pacemaker niet automatisch gestart.

   Voer de volgende opdrachten uit om pacemaker te starten, de SBD-berichten te reinigen voor het knoop punt HN1-DB-0, het knoop punt HN1-DB-0 als secundair te registreren en de mislukte resource op te schonen.

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

   Resource status na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: ONDERLIGGEND SECUNDAIR SITE KNOOPPUNT (KNOOP PUNT 2)

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende opdrachten uit als hoofd knooppunt op het knoop punt HN1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet het gegedoode cluster knooppunt detecteren en het knoop punt deomheining. Zodra het knoop punt is geomheiningd, wordt door pacemaker een overname geactiveerd van het HANA-exemplaar. Wanneer het geomheiningde knoop punt opnieuw wordt opgestart, wordt pacemaker niet automatisch gestart.

   Voer de volgende opdrachten uit om pacemaker te starten, de SBD-berichten voor het knoop punt HN1-db-1 te reinigen, het knoop punt HN1-db-1 als secundair te registreren en de mislukte resource op te schonen.

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

1. TEST 7: DE SECUNDAIRE DATA BASE OP KNOOP PUNT 2 STOPPEN

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <hanasid \> adm op het knoop punt HN1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker detecteert het gestopte HANA-exemplaar en markeert de resource als mislukt op het knoop punt HN1-db-1. Pacemaker moet het HANA-exemplaar automatisch opnieuw starten. Voer de volgende opdracht uit om de mislukte status op te schonen.

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

1. TEST 8: DE SECUNDAIRE DATA BASE OP KNOOP PUNT 2 VASTLOPEN

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als <hanasid \> adm op het knoop punt HN1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker detecteert de gedodene HANA-instantie en markeert de resource als mislukt op het knoop punt HN1-db-1. Voer de volgende opdracht uit om de mislukte status op te schonen. Pacemaker moet het HANA-exemplaar vervolgens automatisch opnieuw starten.

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

1. TEST 9: CRASH SECUNDAIR SITE-KNOOP PUNT (KNOOP PUNT 2) MET SECUNDAIRE HANA-DATA BASE

   Resource status voordat u begint met testen:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als hoofd knooppunt op het knoop punt HN1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet het gegedoode cluster knooppunt detecteren en het knoop punt deomheining. Wanneer het geomheiningde knoop punt opnieuw wordt opgestart, wordt pacemaker niet automatisch gestart.

   Voer de volgende opdrachten uit om pacemaker te starten, de SBD-berichten voor het knoop punt HN1-db-1 op te schonen en de mislukte resource op te schonen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
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

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
