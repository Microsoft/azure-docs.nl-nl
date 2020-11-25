---
title: GlusterFS op virtuele machines van Azure op RHEL voor SAP NetWeaver | Microsoft Docs
description: GlusterFS in Azure VM's in Red Hat Enterprise Linux voor SAP NetWeaver
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: radeltch
ms.openlocfilehash: 41699625b7062daa005e6908ce9e4249dd86f1fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010007"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>GlusterFS in Azure VM's in Red Hat Enterprise Linux voor SAP NetWeaver

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, hoe u de virtuele machines configureert en hoe u een GlusterFS-cluster installeert dat kan worden gebruikt om de gedeelde gegevens van een Maxi maal beschikbaar SAP-systeem op te slaan.
In deze hand leiding wordt beschreven hoe u GlusterFS instelt die worden gebruikt door twee SAP-systemen, NW1 en NW2. De namen van de bronnen (bijvoorbeeld virtuele machines, virtuele netwerken) in het voor beeld aannemen dat u de SAP- [Bestands server sjabloon][template-file-server] hebt gebruikt met resource voorvoegsel **glust**.

Lees eerst de volgende SAP-opmerkingen en-documenten

* SAP-opmerking [1928533], die:
  * Lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure

* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2002167] heeft aanbevolen instellingen voor het besturings systeem voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP Hana richt lijnen voor Red Hat Enterprise Linux
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Product documentatie voor Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Specifieke RHEL-documentatie voor Azure:
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Overzicht

Voor een hoge Beschik baarheid is voor SAP net-Weave gedeelde opslag vereist. GlusterFS wordt geconfigureerd in een afzonderlijk cluster en kan worden gebruikt door meerdere SAP-systemen.

![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>GlusterFS instellen

U kunt een Azure-sjabloon van github gebruiken om alle vereiste Azure-resources te implementeren, met inbegrip van de virtuele machines, beschikbaarheidsset en netwerk interfaces, of u kunt de resources hand matig implementeren.

### <a name="deploy-linux-via-azure-template"></a>Linux implementeren via Azure-sjabloon

De Azure Marketplace bevat een installatie kopie voor Red Hat Enterprise Linux die u kunt gebruiken om nieuwe virtuele machines te implementeren.
U kunt een van de Quick Start-sjablonen op github gebruiken voor het implementeren van alle vereiste resources. De sjabloon implementeert de virtuele machines, de beschik baarheid, enzovoort. Volg deze stappen om de sjabloon te implementeren:

1. Open de [sjabloon SAP-Bestands server][template-file-server] in de Azure Portal
1. Voer de volgende para meters in
   1. Resource voorvoegsel  
      Geef het voor voegsel op dat u wilt gebruiken. De waarde wordt gebruikt als een voor voegsel voor de resources die worden geïmplementeerd.
   2. Aantal SAP-systeem Voer het aantal SAP-systemen in dat deze bestands server gaat gebruiken. Hiermee wordt het vereiste aantal schijven, enzovoort, geïmplementeerd.
   3. Type besturings systeem  
      Selecteer een van de Linux-distributies. Selecteer voor dit voor beeld RHEL 7
   4. Beheerders naam, beheerders wachtwoord of SSH-sleutel  
      Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om u aan te melden bij de computer.
   5. Subnet-ID  
      Als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de ID van het specifieke subnet benoemen. De ID ziet er meestal uit als/Subscriptions/**&lt; -abonnements &gt; -id**/resourceGroups/naam **&lt; &gt; van de resource groep**/providers/Microsoft.Network/virtualNetworks/naam **&lt; &gt; van het virtuele netwerk**/subnets/**&lt; subnet naam &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux hand matig implementeren via Azure Portal

U moet eerst de virtuele machines voor dit cluster maken. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de back-endservers. [Standaard Load Balancer](../../../load-balancer/load-balancer-overview.md)worden aanbevolen.  

1. Een resourcegroep maken
1. Een Virtual Network maken
1. Een beschikbaarheidsset maken  
   Maximum aantal update domeinen instellen
1. Virtuele machine 1 maken  
   Gebruik ten minste RHEL 7, in dit voor beeld de installatie kopie van Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecteer een Beschikbaarheidsset die u eerder hebt gemaakt  
1. Virtuele machine 2 maken  
   Gebruik ten minste RHEL 7, in dit voor beeld de installatie kopie van Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecteer een Beschikbaarheidsset die u eerder hebt gemaakt  
1. Voeg één gegevens schijf voor elk SAP-systeem toe aan beide virtuele machines.

### <a name="configure-glusterfs"></a>GlusterFS configureren

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1, **[2]** -alleen van toepassing op knoop punt 2, **[3]** -alleen van toepassing op knoop punt 3.

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]** registreren

   Registreer uw virtuele machines en koppel deze aan een groep die opslag plaatsen voor RHEL 7 en GlusterFS bevat

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]** glusterfs opslag plaatsen inschakelen

   Schakel de volgende opslag plaatsen in om de vereiste pakketten te installeren.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]** glusterfs-pakketten installeren

   Deze pakketten installeren op alle GlusterFS-knoop punten

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   Start de knoop punten na de installatie opnieuw op.

1. **[A] de** Firewall wijzigen

   Voeg firewall regels toe om client verkeer naar de GlusterFS-knoop punten toe te staan.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]** glusterfs-service inschakelen en starten

   Start de GlusterFS-service op alle knoop punten.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]** GluserFS maken

   Voer de volgende opdrachten uit om het GlusterFS-cluster te maken

   <pre><code>sudo gluster peer probe glust-1
   sudo gluster peer probe glust-2
   
   # Check gluster peer status
   sudo gluster peer status
   
   # Number of Peers: 2
   # 
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Accepted peer request (Connected)
   # 
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Accepted peer request (Connected)
   </code></pre>

1. **[2]** status van test peer

   De status van de peer op het tweede knoop punt testen

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[3]** status van test peer

   De status van de peer op het derde knoop punt testen

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[A]** LVM maken

   In dit voor beeld wordt de GlusterFS gebruikt voor twee SAP-systemen, NW1 en NW2. Gebruik de volgende opdrachten om LVM-configuraties voor deze SAP-systemen te maken.

   Gebruik deze opdrachten voor NW1

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun0
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW1</b> /dev/disk/azure/scsi1/lun0
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW1</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW1</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW1</b>/aers
   sudo lvscan
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/aers
   
   sudo mkdir -p /rhs/<b>NW1</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW1</b>/trans
   sudo mkdir -p /rhs/<b>NW1</b>/sys
   sudo mkdir -p /rhs/<b>NW1</b>/ascs
   sudo mkdir -p /rhs/<b>NW1</b>/aers
   
   sudo chattr +i /rhs/<b>NW1</b>/sapmnt
   sudo chattr +i /rhs/<b>NW1</b>/trans
   sudo chattr +i /rhs/<b>NW1</b>/sys
   sudo chattr +i /rhs/<b>NW1</b>/ascs
   sudo chattr +i /rhs/<b>NW1</b>/aers

   echo -e "/dev/rhgs-<b>NW1</b>/sapmnt\t/rhs/<b>NW1</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/trans\t/rhs/<b>NW1</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/sys\t/rhs/<b>NW1</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/ascs\t/rhs/<b>NW1</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/aers\t/rhs/<b>NW1</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

   Gebruik deze opdrachten voor NW2

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun1
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW2</b> /dev/disk/azure/scsi1/lun1
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW2</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW2</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW2</b>/aers
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/aers
   
   sudo mkdir -p /rhs/<b>NW2</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW2</b>/trans
   sudo mkdir -p /rhs/<b>NW2</b>/sys
   sudo mkdir -p /rhs/<b>NW2</b>/ascs
   sudo mkdir -p /rhs/<b>NW2</b>/aers
   
   sudo chattr +i /rhs/<b>NW2</b>/sapmnt
   sudo chattr +i /rhs/<b>NW2</b>/trans
   sudo chattr +i /rhs/<b>NW2</b>/sys
   sudo chattr +i /rhs/<b>NW2</b>/ascs
   sudo chattr +i /rhs/<b>NW2</b>/aers
   sudo lvscan
   
   echo -e "/dev/rhgs-<b>NW2</b>/sapmnt\t/rhs/<b>NW2</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/trans\t/rhs/<b>NW2</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/sys\t/rhs/<b>NW2</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/ascs\t/rhs/<b>NW2</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/aers\t/rhs/<b>NW2</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

1. **[1]** het gedistribueerde volume maken

   Gebruik de volgende opdrachten om het GlusterFS-volume voor NW1 te maken en het te starten.

   <pre><code>sudo gluster vol create <b>NW1</b>-sapmnt replica 3 glust-0:/rhs/<b>NW1</b>/sapmnt glust-1:/rhs/<b>NW1</b>/sapmnt glust-2:/rhs/<b>NW1</b>/sapmnt force
   sudo gluster vol create <b>NW1</b>-trans replica 3 glust-0:/rhs/<b>NW1</b>/trans glust-1:/rhs/<b>NW1</b>/trans glust-2:/rhs/<b>NW1</b>/trans force
   sudo gluster vol create <b>NW1</b>-sys replica 3 glust-0:/rhs/<b>NW1</b>/sys glust-1:/rhs/<b>NW1</b>/sys glust-2:/rhs/<b>NW1</b>/sys force
   sudo gluster vol create <b>NW1</b>-ascs replica 3 glust-0:/rhs/<b>NW1</b>/ascs glust-1:/rhs/<b>NW1</b>/ascs glust-2:/rhs/<b>NW1</b>/ascs force
   sudo gluster vol create <b>NW1</b>-aers replica 3 glust-0:/rhs/<b>NW1</b>/aers glust-1:/rhs/<b>NW1</b>/aers glust-2:/rhs/<b>NW1</b>/aers force
   
   sudo gluster volume start <b>NW1</b>-sapmnt
   sudo gluster volume start <b>NW1</b>-trans
   sudo gluster volume start <b>NW1</b>-sys
   sudo gluster volume start <b>NW1</b>-ascs
   sudo gluster volume start <b>NW1</b>-aers
   </code></pre>

   Gebruik de volgende opdrachten om het GlusterFS-volume voor NW2 te maken en het te starten.

   <pre><code>sudo gluster vol create <b>NW2</b>-sapmnt replica 3 glust-0:/rhs/<b>NW2</b>/sapmnt glust-1:/rhs/<b>NW2</b>/sapmnt glust-2:/rhs/<b>NW2</b>/sapmnt force
   sudo gluster vol create <b>NW2</b>-trans replica 3 glust-0:/rhs/<b>NW2</b>/trans glust-1:/rhs/<b>NW2</b>/trans glust-2:/rhs/<b>NW2</b>/trans force
   sudo gluster vol create <b>NW2</b>-sys replica 3 glust-0:/rhs/<b>NW2</b>/sys glust-1:/rhs/<b>NW2</b>/sys glust-2:/rhs/<b>NW2</b>/sys force
   sudo gluster vol create <b>NW2</b>-ascs replica 3 glust-0:/rhs/<b>NW2</b>/ascs glust-1:/rhs/<b>NW2</b>/ascs glust-2:/rhs/<b>NW2</b>/ascs force
   sudo gluster vol create <b>NW2</b>-aers replica 3 glust-0:/rhs/<b>NW2</b>/aers glust-1:/rhs/<b>NW2</b>/aers glust-2:/rhs/<b>NW2</b>/aers force
   
   sudo gluster volume start <b>NW2</b>-sapmnt
   sudo gluster volume start <b>NW2</b>-trans
   sudo gluster volume start <b>NW2</b>-sys
   sudo gluster volume start <b>NW2</b>-ascs
   sudo gluster volume start <b>NW2</b>-aers
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [De SAP-ASCS en-data base installeren](high-availability-guide-rhel.md)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
