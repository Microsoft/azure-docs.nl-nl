---
title: Hoge beschikbaarheid van SAP HANA in Azure-VM's op RHEL-| Microsoft Docs
description: Hoge beschikbaarheid van SAP HANA virtuele Azure-machines (VM's).
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
ms.openlocfilehash: 3d1b05560c02f3bf4de199a3d5cad48907ee16fb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365804"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hoge beschikbaarheid van SAP HANA in Azure-VM's op Red Hat Enterprise Linux

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Voor on-premises ontwikkeling kunt u HANA-systeemreplicatie gebruiken of gedeelde opslag gebruiken om hoge beschikbaarheid voor SAP HANA.
Op virtuele Azure-machines (VM's) is HANA-systeemreplicatie in Azure momenteel de enige ondersteunde functie voor hoge beschikbaarheid.
SAP HANA replicatie bestaat uit één primair knooppunt en ten minste één secundair knooppunt. Wijzigingen in de gegevens op het primaire knooppunt worden synchroon of asynchroon gerepliceerd naar het secundaire knooppunt.

In dit artikel wordt beschreven hoe u de virtuele machines implementeert en configureert, het clusterkader installeert en SAP HANA configureert.
In de voorbeeldconfiguraties worden installatieopdrachten, **exemplaarnummer 03** en HANA-systeem-id **HN1** gebruikt.

Lees eerst de volgende SAP-opmerkingen en -documenten:

* SAP Note [1928533,]met:
  * De lijst met Azure VM-grootten die worden ondersteund voor de implementatie van SAP-software.
  * Belangrijke capaciteitsinformatie voor Azure VM-grootten.
  * De ondersteunde combinaties van SAP-software en besturingssysteem (OS) en database.
  * De vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure.
* SAP Note [2015553 bevat] een lijst met vereisten voor SAP-ondersteunde SAP-software-implementaties in Azure.
* SAP Note [2002167 heeft] aanbevolen besturingssysteeminstellingen voor Red Hat Enterprise Linux
* SAP Note [2009879 heeft] SAP HANA Richtlijnen voor Red Hat Enterprise Linux
* SAP Note [2178632 heeft] gedetailleerde informatie over alle controlemetrieken die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste sap-hostagentversie voor Linux in Azure.
* SAP Note [2243692 heeft] informatie over SAP-licenties op Linux in Azure.
* SAP Note [1999351 heeft] aanvullende informatie over probleemoplossing voor de Uitgebreide bewakingsextensie van Azure voor SAP.
* [SAP Community WIKI heeft](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) alle vereiste SAP-notities voor Linux.
* [Azure Virtual Machines planning en implementatie voor SAP on Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP on Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SAP HANA systeemreplicatie in pacemaker-cluster](https://access.redhat.com/articles/3004101)
* Algemene RHEL-documentatie
  * [Overzicht van Add-On hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Beheer van Add-On hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Naslag voor Add-On beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-specifieke RHEL-documentatie:
  * [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - Microsoft Azure Virtual Machines clusterleden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een Red Hat Enterprise Linux 7.4 (en hoger) High-Availability cluster op Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Installeer SAP HANA op Red Hat Enterprise Linux voor gebruik in Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Overzicht

Voor hoge beschikbaarheid wordt SAP HANA geïnstalleerd op twee virtuele machines. De gegevens worden gerepliceerd met behulp van HANA-systeemreplicatie.

![SAP HANA overzicht van hoge beschikbaarheid](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA installatie van systeemreplicatie maakt gebruik van een toegewezen virtuele hostnaam en virtuele IP-adressen. In Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. In de volgende lijst ziet u de configuratie van de load balancer:

* Front-endconfiguratie: IP-adres 10.0.0.13 voor hn1-db
* Back-endconfiguratie: verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeemreplicatie
* Testpoort: poort 62503
* Taakverdelingsregels: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Implementeren voor Linux

De Azure Marketplace bevat een Red Hat Enterprise Linux 7.4 voor SAP HANA die u kunt gebruiken om nieuwe virtuele machines te implementeren.

### <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U kunt een van de snelstartsjablonen op GitHub gebruiken om alle vereiste resources te implementeren. Met de sjabloon worden de virtuele machines, de load balancer, de beschikbaarheidsset, en meer geïmplementeerd.
Volg deze stappen om de sjabloon te implementeren:

1. Open de [databasesjabloon][template-multisid-db] op Azure Portal.
1. Voer de volgende parameters in:
    * **SAP-systeem-id:** voer de SAP-systeem-id in van het SAP-systeem dat u wilt installeren. De id wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
    * **Type besturingssysteem:** selecteer een van de Linux-distributies. Selecteer voor dit voorbeeld **RHEL 7.**
    * **Db-type:** selecteer **HANA.**
    * **Sap-systeemgrootte:** voer het aantal SAPS in dat door het nieuwe systeem wordt verstrekt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraagt u uw SAP-technologiepartner of systeemintegrator.
    * **Systeembeschikbaarheid:** selecteer **HOGE BESCHIKBAARHEID.**
    * **Gebruikersnaam van beheerder, beheerderswachtwoord of SSH-sleutel:** er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om u aan te melden bij de computer.
    * **Subnet-id:** als u de virtuele machine wilt implementeren in een bestaand VNet waar u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan de naam van dat specifieke subnet. De id ziet er meestal uit **als /subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**. Laat leeg als u een nieuw virtueel netwerk wilt maken

### <a name="manual-deployment"></a>Handmatige implementatie

1. Een resourcegroep maken.
1. Maak een virtueel netwerk.
1. Maak een beschikbaarheidsset.  
   Stel het maximale updatedomein in.
1. Maak een load balancer (intern). We raden [u aan load balancer.](../../../load-balancer/load-balancer-overview.md)
   * Selecteer het virtuele netwerk dat u in stap 2 hebt gemaakt.
1. Maak virtuele machine 1.  
   Gebruik ten minste Red Hat Enterprise Linux 7.4 voor SAP HANA. In dit voorbeeld wordt de Red Hat Enterprise Linux 7.4 voor SAP HANA selecteer <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> de beschikbaarheidsset die u in stap 3 hebt gemaakt.
1. Maak virtuele machine 2.  
   Gebruik ten minste Red Hat Enterprise Linux 7.4 voor SAP HANA. In dit voorbeeld wordt de Red Hat Enterprise Linux 7.4 voor SAP HANA selecteer <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> de beschikbaarheidsset die u in stap 3 hebt gemaakt.
1. Voeg gegevensschijven toe.

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund op een secundaire IP-configuratie van een NIC in taakverdelingsscenario's. Zie Beperkingen [voor Azure Load Balancer voor meer informatie.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Als u een extra IP-adres voor de VM nodig hebt, implementeert u een tweede NIC.    

> [!Note]
> Wanneer VM's zonder openbare IP-adressen in de back-endpool van interne (geen openbaar IP-adres) Standard Azure load balancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij er aanvullende configuratie wordt uitgevoerd om routering naar openbare eindpunten toe te staan. Zie Public [endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md)(Openbare eindpuntconnectiviteit voor Virtual Machines met behulp van Azure Standard Load Balancer in SAP-scenario's met hoge beschikbaarheid) voor meer informatie over het bereiken van uitgaande connectiviteit.  

1. Als u standard load balancer gebruikt, volgt u deze configuratiestappen:
   1. Maak eerst een front-end-IP-adresgroep:

      1. Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe front-end-IP-adresgroep in (bijvoorbeeld **hana-frontend**).
      1. Stel Toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13).**
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-adresgroep is gemaakt, noteer het IP-adres van de groep.

   1. Maak vervolgens een back-endpool:

      1. Open de load balancer, **selecteer back-endpools** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe back-endpool in (bijvoorbeeld **hana-backend**).
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
   
      1. Open de load balancer, selecteer **taakverdelingsregels** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe load balancer in (bijvoorbeeld **hana-lb**).
      1. Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend,** **hana-backend** en **hana-hp).**
      1. Selecteer **HA-poorten.**
      1. **Verhoog de time-out voor** inactief naar 30 minuten.
      1. Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
      1. Selecteer **OK**.


1. Als uw scenario het gebruik van basisconfiguratieregels load balancer, volgt u deze configuratiestappen:
   1. Configureer de load balancer. Maak eerst een front-end-IP-adresgroep:

      1. Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe front-end-IP-adresgroep in (bijvoorbeeld **hana-frontend).**
      1. Stel toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13).**
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-adresgroep is gemaakt, noteer het IP-adres van de groep.

   1. Maak vervolgens een back-endpool:

      1. Open de load balancer, **selecteer back-endpools** en selecteer **Toevoegen.**
      1. Voer de naam van de nieuwe back-endpool in (bijvoorbeeld **hana-backend).**
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
      1. Houd protocol **ingesteld** op **TCP** en voer poort 3 **03** 13 in.
      1. **Verhoog de time-out voor** inactieve tijd tot 30 minuten.
      1. Zorg ervoor dat **Zwevend IP is ingeschakeld.**
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3 **03** 14.

   1. Maak SAP HANA 2.0 eerst de taakverdelingsregels voor de tenantdatabase:

      1. Open de load balancer, selecteer **taakverdelingsregels** en selecteer **Toevoegen.**
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

## <a name="install-sap-hana"></a>SAP HANA installeren

In de stappen in deze sectie worden de volgende voorvoegsels gebruikt:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het Pacemaker-cluster.

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

   Maak de logische volumes. Er wordt een lineair volume gemaakt wanneer u zonder `lvcreate` de `-i` switch gebruikt. We raden u aan een striped volume te maken voor betere I/O-prestaties en de stripe-grootten uit te lijnen met de waarden die worden beschreven in SAP HANA [VM-opslagconfiguraties.](./hana-vm-operations-storage.md) Het argument moet het aantal onderliggende fysieke volumes zijn en `-i` het argument is de `-I` stripegrootte. In dit document worden twee fysieke volumes gebruikt voor het gegevensvolume, dus het `-i` schakelargument is ingesteld **op 2**. De stripegrootte voor het gegevensvolume is **256KiB.** Eén fysiek volume wordt gebruikt voor het logboekvolume, zodat er geen of switches expliciet worden gebruikt voor de `-i` `-I` logboekvolumeopdrachten.  

   > [!IMPORTANT]
   > Gebruik de schakelknop en stel deze in op het aantal onderliggende fysieke volumes wanneer u meer dan één fysiek volume gebruikt voor elke gegevens, logboek `-i` of gedeelde volumes. Gebruik de `-I` schakelknop om de stripegrootte op te geven bij het maken van een striped volume.  
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

1. **[A]** RHEL voor HANA-configuratie

   Configureer RHEL zoals beschreven in <https://access.redhat.com/solutions/2447641> en in de volgende SAP-opmerkingen:  
   - [2292690 - SAP HANA DB: aanbevolen instellingen voor het besturingssysteem voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB: aanbevolen besturingssysteeminstellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** Installeer de SAP HANA

   Volg om SAP HANA-systeemreplicatie te <https://access.redhat.com/articles/3004101> installeren.

   * Voer het **hdblcm-programma** uit vanaf de HANA-dvd. Voer de volgende waarden in bij de prompt:
   * Installatie kiezen: Voer **1 in.**
   * Selecteer extra onderdelen voor installatie: Voer **1 in.**
   * Voer installatiepad [/hana/shared]: Selecteer Enter.
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
   * Sap Host Agent User-wachtwoord (sapadm) invoeren: voer het gebruikerswachtwoord van de hostagent in.
   * Sap Host Agent-gebruikerswachtwoord (sapadm) bevestigen: voer ter bevestiging nogmaals het gebruikerswachtwoord van de hostagent in.
   * Voer het wachtwoord van de systeembeheerder (hdbadm) in: voer het wachtwoord van de systeembeheerder in.
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

1. **[A]** Firewall configureren

   Maak de firewallregel voor de Azure load balancer-testpoort.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Systeemreplicatie SAP HANA 2.0 configureren

In de stappen in deze sectie worden de volgende voorvoegsels gebruikt:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het Pacemaker-cluster.

1. **[A]** Firewall configureren

   Maak firewallregels om HANA-systeemreplicatie en clientverkeer toe te staan. De vereiste poorten worden vermeld op [TCP/IP-poorten van alle SAP-producten.](https://help.sap.com/viewer/ports) De volgende opdrachten zijn slechts een voorbeeld om HANA 2.0-systeemreplicatie en clientverkeer naar database SYSTEMDB, HN1 en NW1 toe te staan.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Maak de tenantdatabase.

   Als u een SAP HANA 2.0 of MDC gebruikt, maakt u een tenantdatabase voor uw SAP NetWeaver-systeem. Vervang **NW1 door** de SID van uw SAP-systeem.

   Voer de <opdracht \> uit als <adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configureer systeemreplicatie op het eerste knooppunt:

   Back-up van de databases als <\> adm:

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
    
   Registreer het tweede knooppunt om de systeemreplicatie te starten. Voer de volgende opdracht uit <\> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1] Replicatiestatus** controleren

   Controleer de replicatiestatus en wacht tot alle databases zijn gesynchroniseerd. Als de status ONBEKEND blijft, controleert u de firewallinstellingen.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Een SAP HANA 1.0-systeemreplicatie configureren

De stappen in deze sectie gebruiken de volgende voorvoegsels:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het Pacemaker-cluster.

1. **[A]** Firewall configureren

   Maak firewallregels om HANA-systeemreplicatie en clientverkeer toe te staan. De vereiste poorten worden vermeld op [TCP/IP-poorten van alle SAP-producten.](https://help.sap.com/viewer/ports) De volgende opdrachten zijn slechts een voorbeeld van het toestaan van HANA 2.0-systeemreplicatie. Pas deze aan uw SAP HANA 1.0-installatie aan.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Maak de vereiste gebruikers.

   Voer de volgende opdracht uit als root. Zorg ervoor dat u vetgedrukte tekenreeksen (HANA-systeem-id **HN1** en exemplaarnummer **03**) vervangt door de waarden van uw SAP HANA installatie:

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

   Maak de primaire site als <\> adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configureer systeemreplicatie op het secundaire knooppunt.

   Registreer de secundaire site als <\> adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Een Pacemaker-cluster maken

Volg de stappen in [Pacemaker instellen op](high-availability-guide-rhel-pacemaker.md) Red Hat Enterprise Linux in Azure om een eenvoudig Pacemaker-cluster voor deze HANA-server te maken.

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>De Python-systeemreplicatie hook SAPHanaSR implementeren

Dit is een belangrijke stap om de integratie met het cluster te optimaliseren en de detectie te verbeteren wanneer een cluster-failover nodig is. Het wordt ten zeerste aanbevolen om de PYTHON-hook SAPHanaSR te configureren.    

1. **[A]** Installeer de 'systeemreplicatie hook' van HANA. De hook moet worden geïnstalleerd op beide HANA DB-knooppunten.           

   > [!TIP]
   > De Python-hook kan alleen worden geïmplementeerd voor HANA 2.0.        

   1. Bereid de hook voor als `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
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
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

Zie Enable the SAP HA/DR provider hook (De SAP HA/DR-provider hook inschakelen) voor meer informatie over de implementatie van [SAP HANA-systeemreplicatie hook.](https://access.redhat.com/articles/3004101#enable-srhook)  
 
## <a name="create-sap-hana-cluster-resources"></a>Clusterbronnen SAP HANA maken

Installeer de SAP HANA resourceagents op **alle knooppunten**. Zorg ervoor dat u een opslagplaats inschakelen die het pakket bevat. U hoeft geen aanvullende opslagplaatsen in teschakelen als u een RHEL 8.x HA-afbeelding gebruikt.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Maak vervolgens de HANA-topologie. Voer de volgende opdrachten uit op een van de Pacemaker-clusterknooppunten:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Maak vervolgens de HANA-resources.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *slave*, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

Als u een cluster bouwt op **RHEL 7.x,** gebruikt u de volgende opdrachten:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Als u een cluster bouwt op **RHEL 8.x,** gebruikt u de volgende opdrachten:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Zorg ervoor dat de status van het cluster ok is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de resources worden uitgevoerd.

> [!NOTE]
> De time-outs in de bovenstaande configuratie zijn slechts voorbeelden en moeten mogelijk worden aangepast aan de specifieke HANA-configuratie. U moet bijvoorbeeld de time-out voor het starten verhogen als het langer duurt om de database SAP HANA starten.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Actieve/lees ingeschakelde systeemreplicatie van HANA configureren in pacemaker-cluster

Vanaf SAP HANA 2.0 SPS 01 staat SAP actief/lezen ingeschakelde installatie toe voor SAP HANA-systeemreplicatie, waarbij de secundaire systemen van SAP HANA-systeemreplicatie actief kunnen worden gebruikt voor lees-intensieve werkbelastingen. Voor de ondersteuning van een dergelijke installatie in een cluster is een tweede virtueel IP-adres vereist waarmee clients toegang kunnen krijgen tot de secundaire database met SAP HANA lezen. Om ervoor te zorgen dat de secundaire replicatiesite nog steeds toegankelijk is nadat een overname is uitgevoerd, moet het cluster het virtuele IP-adres verplaatsen naar de secundaire van de SAPHana-resource.

In deze sectie worden de extra stappen beschreven die nodig zijn voor het beheren van HANA Active/Read ingeschakelde systeemreplicatie in een Red Hat-cluster met hoge beschikbaarheid met tweede virtuele IP.    

Voordat u verdergaat, moet u ervoor zorgen dat u een volledig geconfigureerd Red Hat High Availability Cluster hebt SAP HANA database, zoals beschreven in de bovenstaande segmenten van de documentatie.  

![SAP HANA hoge beschikbaarheid met secundaire lees ingeschakeld](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Aanvullende installatie in Azure load balancer voor actieve/lees-ingeschakelde installatie

Als u wilt doorgaan met aanvullende stappen voor het inrichten van het tweede virtuele IP-adres, moet u ervoor zorgen dat u Azure Load Balancer zoals beschreven in [de sectie Handmatige](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#manual-deployment) implementatie.

1. Voor **standaard** load balancer volgt u onderstaande aanvullende stappen op dezelfde load balancer u in een eerdere sectie hebt gemaakt.

   a. Maak een tweede front-end-IP-adresgroep: 

   - Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
   - Voer de naam in van de tweede front-end-IP-adresgroep (bijvoorbeeld **hana-secondaryIP).**
   - Stel Toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.14).**
   - Selecteer **OK**.
   - Nadat de nieuwe front-end-IP-adresgroep is gemaakt, noteer het IP-adres van de groep.

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
   - Zorg ervoor dat **Zwevend IP is ingeschakeld.**
   - Selecteer **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>HANA-systeemreplicatie configureren die actief/gelezen is ingeschakeld

De stappen voor het configureren van HANA-systeemreplicatie worden beschreven in [SAP HANA 2.0-systeemreplicatie configureren.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) Als u een secundair scenario met lees ingeschakeld implementeert tijdens het configureren van systeemreplicatie op het tweede knooppunt, voert u de volgende opdracht uit als **adm:**

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Een secundaire virtuele IP-adresresource toevoegen voor een actieve/lees-ingeschakelde installatie

Het tweede virtuele IP-adres en de juiste co-locatiebeperking kunnen worden geconfigureerd met de volgende opdrachten:

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
Zorg ervoor dat de status van het cluster ok is en dat alle resources zijn gestart. Het tweede virtuele IP-adres wordt uitgevoerd op de secundaire site, samen met de secundaire SAPHana-resource.

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

In de volgende sectie vindt u de typische set failovertests die u wilt uitvoeren.

Let op het tweede virtuele IP-gedrag tijdens het testen van een HANA-cluster dat is geconfigureerd met secundaire database met lees ingeschakeld:

1. Wanneer u **SAPHana_HN1_HDB03** clusterresource migreert naar **hn1-db-1,** wordt het tweede virtuele IP-adres verplaatst naar de andere server **hn1-db-0.** Als u AUTOMATED_REGISTER="false" hebt geconfigureerd en HANA-systeemreplicatie niet automatisch is geregistreerd, wordt het tweede virtuele IP-adres uitgevoerd op **hn1-db-0,** omdat de server beschikbaar is en de clusterservices online zijn.  

2. Bij het testen van de crash van de server worden de tweede virtuele IP-resources **(rsc_secip_HN1_HDB03)** en Azure load balancer-poortresource **(rsc_secnc_HN1_HDB03)** uitgevoerd op de primaire server naast de primaire virtuele IP-resources.  Terwijl de secundaire server niet werkt, maken de toepassingen die zijn verbonden met de HANA-database met leesmogelijkheden verbinding met de primaire HANA-database. Dit gedrag wordt verwacht omdat u niet wilt dat toepassingen die zijn verbonden met de HANA-database met leesmogelijkheden, ontoegankelijk zijn wanneer de secundaire server niet beschikbaar is.

3. Wanneer de secundaire server beschikbaar is en de clusterservices online zijn, worden de tweede virtuele IP- en poortresources automatisch verplaatst naar de secundaire server, ook al is HANA-systeemreplicatie mogelijk niet geregistreerd als secundair. U moet ervoor zorgen dat u de secundaire HANA-database registreert als gelezen ingeschakeld voordat u clusterservices op die server start. U kunt de clusterresource van het HANA-exemplaar configureren om de secundaire resource automatisch te registreren door parameter AUTOMATED_REGISTER=true in te stellen.
   
4. Tijdens failover en terugval kunnen de bestaande verbindingen voor toepassingen, die gebruikmaken van het tweede virtuele IP-adres om verbinding te maken met de HANA-database, worden onderbroken.  

## <a name="test-the-cluster-setup"></a>De installatie van het cluster testen

In deze sectie wordt beschreven hoe u uw installatie kunt testen. Voordat u een test start, moet u ervoor zorgen dat Pacemaker geen mislukte actie heeft (via de status pcs), er geen onverwachte locatiebeperkingen zijn (bijvoorbeeld leftovers van een migratietest) en dat HANA de synchronisatiestatus heeft, bijvoorbeeld met systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>De migratie testen

Resourcetoestand voordat u de test start:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U kunt het SAP HANA hoofd-knooppunt migreren door de volgende opdracht uit te voeren:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Als u in stelt, moet met deze opdracht het SAP HANA hoofd-knooppunt en de groep met het virtuele `AUTOMATED_REGISTER="false"` IP-adres naar hn1-db-1 worden gemigreerd.

Zodra de migratie is uitgevoerd, ziet de uitvoer 'sudo pcs status' er als deze uit

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

De SAP HANA resource op hn1-db-0 is gestopt. In dit geval configureert u het HANA-exemplaar als secundair door deze opdracht uit te voeren:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Met de migratie worden locatiebeperkingen gemaakt die opnieuw moeten worden verwijderd:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Controleer de status van de HANA-resource met behulp van 'pcs-status'. Zodra HANA is gestart op hn1-db-0, moet de uitvoer er als deze uitzien

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>De Azure Fencing-agent testen

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *slave*, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.  

Resourcetoestand voordat u de test start:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

U kunt de installatie van de Azure Fencing-agent testen door de netwerkinterface uit te stellen op het knooppunt waar SAP HANA wordt uitgevoerd als master.
Zie [Red Hat Knowledgebase-artikel 79523](https://access.redhat.com/solutions/79523) voor een beschrijving van het simuleren van een netwerkfout. In dit voorbeeld gebruiken we het net_breaker om alle toegang tot het netwerk te blokkeren.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

De virtuele machine moet nu opnieuw worden opgestart of gestopt, afhankelijk van uw clusterconfiguratie.
Als u de instelling instelt op uit, wordt de virtuele machine gestopt `stonith-action` en worden de resources gemigreerd naar de virtuele machine die wordt uitgevoerd.

Nadat u de virtuele machine opnieuw hebt SAP HANA, kan de resource niet worden SAP HANA als secundair als u in `AUTOMATED_REGISTER="false"` stelt. In dit geval configureert u het HANA-exemplaar als secundair door deze opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Resourcetoestand na de test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Een handmatige failover testen

Resourcetoestand voordat de test wordt uitgevoerd:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U kunt een handmatige failover testen door het cluster op het knooppunt hn1-db-0 te stoppen:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Na de failover kunt u het cluster opnieuw starten. Als u in stelt, kan SAP HANA resource op het `AUTOMATED_REGISTER="false"` knooppunt hn1-db-0 niet starten als secundair. In dit geval configureert u het HANA-exemplaar als secundair door deze opdracht uit te voeren:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Resourcetoestand na de test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Een handmatige failover testen

Resourcetoestand voordat de test wordt uitgevoerd:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U kunt een handmatige failover testen door het cluster op het knooppunt hn1-db-0 te stoppen:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [SAP HANA VM-opslagconfiguraties configureren](./hana-vm-operations-storage.md)