---
title: Pacemaker instellen voor SLES in azure | Microsoft Docs
description: Pacemaker instellen voor SUSE Linux Enterprise Server in azure
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
ms.date: 08/04/2020
ms.author: radeltch
ms.openlocfilehash: 57c6caea2de9063b133d4d5d643629184e412dad
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957687"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Pacemaker instellen voor SUSE Linux Enterprise Server in azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Er zijn twee opties voor het instellen van een pacemaker-cluster in Azure. U kunt een afschermings agent gebruiken. Dit zorgt ervoor dat er een knoop punt kan worden gestart via de Azure-Api's of dat u een SBD-apparaat kunt gebruiken.

Het SBD-apparaat vereist ten minste één extra virtuele machine die fungeert als iSCSI-doel server en een SBD-apparaat biedt. Deze iSCSI-doel servers kunnen echter worden gedeeld met andere pacemaker-clusters. Het voor deel van het gebruik van een SBD-apparaat is dat als u al gebruikmaakt van SBD-apparaten on-premises geen wijzigingen nodig hebt in de manier waarop u het pacemaker-cluster gebruikt. U kunt Maxi maal drie SBD-apparaten voor een pacemaker-cluster gebruiken om ervoor te zorgen dat een SBD-apparaat niet meer beschikbaar is, bijvoorbeeld tijdens het bijwerken van het besturings systeem van de iSCSI-doel server. Als u meer dan één SBD-apparaat per pacemaker wilt gebruiken, moet u ervoor zorgen dat u meerdere iSCSI-doel servers implementeert en een SBD verbindt vanaf elke iSCSI-doel server. We raden u aan een SBD-apparaat of drie te gebruiken. Pacemaker kan een cluster knooppunt niet automatisch afomheiningen als u alleen twee SBD-apparaten configureert en een van de knoop punten niet beschikbaar is. Als u een omheining wilt kunnen opvangen wanneer een iSCSI-doel server niet beschikbaar is, moet u drie SBD-apparaten gebruiken en daarom drie iSCSI-doel servers. Dit is de meest flexibele configuratie wanneer u SBDs gebruikt.

De Azure Fence-agent vereist geen extra virtuele machine (s) implementeren.   

![Overzicht van pacemaker op SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Wanneer u Linux pacemaker geclusterde knoop punten en SBD-apparaten plant en implementeert, is het essentieel voor de algehele betrouw baarheid van de volledige cluster configuratie, waardoor de route ring tussen de betrokken Vm's en de virtuele machine (s) die de SBD-apparaten hosten, niet via andere apparaten, zoals [nva's](https://azure.microsoft.com/solutions/network-appliances/), wordt door gegeven. Anders kunnen problemen en onderhouds gebeurtenissen met de NVA een negatieve invloed hebben op de stabiliteit en betrouw baarheid van de algehele cluster configuratie. Om dergelijke obstakels te voor komen, definieert u geen routerings regels van Nva's of door de [gebruiker gedefinieerde routerings regels](../../../virtual-network/virtual-networks-udr-overview.md) die verkeer routeren tussen geclusterde knoop punten en SBD-apparaten via nva's en vergelijk bare apparaten bij het plannen en implementeren van Linux pacemaker geclusterde knoop punten en SBD-apparaten. 
>

## <a name="sbd-fencing"></a>SBD-omheining

Volg deze stappen als u een SBD-apparaat voor omheining wilt gebruiken.

### <a name="set-up-iscsi-target-servers"></a>ISCSI-doel servers instellen

U moet eerst de virtuele iSCSI-doel machines maken. iSCSI-doel servers kunnen worden gedeeld met meerdere pacemaker-clusters.

1. Implementeer nieuwe SLES 12 SP1 of hogere virtuele machines en maak er verbinding mee via SSH. De machines hoeven niet groot te zijn. De grootte van een virtuele machine, zoals Standard_E2s_v3 of Standard_D2s_v3, is voldoende. Zorg ervoor dat u Premium Storage gebruikt op de besturingssysteem schijf.

Voer de volgende opdrachten uit op alle **virtuele iSCSI-doel machines**.

1. SLES bijwerken

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Mogelijk moet u het besturings systeem opnieuw opstarten na het bijwerken of bijwerken van het besturings systeem. 

1. Pakketten verwijderen

   Als u een bekend probleem met targetcli en SLES 12 SP3 wilt voor komen, verwijdert u de volgende pakketten. U kunt fouten negeren over pakketten die niet kunnen worden gevonden

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. ISCSI-doel pakketten installeren

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. De iSCSI-doel service inschakelen

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>ISCSI-apparaat maken op iSCSI-doel server

Voer de volgende opdrachten uit op alle **virtuele iSCSI-doel machines** om de iSCSI-schijven te maken voor de clusters die door uw SAP-systemen worden gebruikt. In het volgende voor beeld worden SBD-apparaten voor meerdere clusters gemaakt. U ziet hoe u een iSCSI-doel server gebruikt voor meerdere clusters. De SBD-apparaten worden geplaatst op de besturingssysteem schijf. Zorg ervoor dat u voldoende ruimte hebt.

**`nfs`** wordt gebruikt voor het identificeren van het NFS-cluster, **ascsnw1** wordt gebruikt om de ASCS-cluster van **NW1** te identificeren, **dbnw1** wordt gebruikt voor het identificeren van het database cluster van **NW1**, **NFS-0** en **NFS-1** zijn de hostnamen van de NFS-cluster knooppunten, **NW1-xscs-0** en **NW1-xscs-1** zijn de hostnamen van de **NW1** ASCS-cluster knooppunten, en **NW1-DB-0** en **NW1-db-1** zijn de hostnamen van de database cluster knooppunten. Vervang deze door de hostnamen van uw cluster knooppunten en de SID van uw SAP-systeem.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

U kunt controleren of alles juist is ingesteld met

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>SBD-apparaat instellen

Maak verbinding met het iSCSI-apparaat dat in de laatste stap van het cluster is gemaakt.
Voer de volgende opdrachten uit op de knoop punten van het nieuwe cluster dat u wilt maken.
De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** verbinding maken met de iSCSI-apparaten

   Schakel eerst de iSCSI-en SBD-Services in.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Wijzig de naam van de initiator op het eerste knoop punt

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Wijzig de inhoud van het bestand zodat dit overeenkomt met de Acl's die u hebt gebruikt bij het maken van het iSCSI-apparaat op de iSCSI-doel server, bijvoorbeeld voor de NFS-server.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Wijzig de naam van de initiator op het tweede knoop punt

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Wijzig de inhoud van het bestand zodat deze overeenkomt met de Acl's die u hebt gebruikt bij het maken van het iSCSI-apparaat op de iSCSI-doel server

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** de iSCSI-service opnieuw starten

   Start de iSCSI-service nu opnieuw op om de wijziging toe te passen

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   De iSCSI-apparaten verbinden. In het onderstaande voor beeld is 10.0.0.17 het IP-adres van de iSCSI-doel server en is 3260 de standaard poort. <b>IQN. 2006-04. NFS. local: NFS</b> is een van de doel namen die worden weer gegeven wanneer u de eerste opdracht uitvoert (iscsiadm-m-detectie).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Zorg ervoor dat de iSCSI-apparaten beschikbaar zijn en noteer de naam van het apparaat (in het volgende voor beeld/dev/SDE)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Haal nu de Id's van de iSCSI-apparaten op.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   De opdracht lijst drie apparaat-Id's voor elk SBD-apparaat. We raden u aan de ID te gebruiken die begint met SCSI-3, in bovenstaand voor beeld.

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** het SBD-apparaat maken

   Gebruik de apparaat-ID van de iSCSI-apparaten om de nieuwe SBD-apparaten op het eerste cluster knooppunt te maken.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** de SBD-configuratie aanpassen

   Open het SBD-configuratie bestand

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Wijzig de eigenschap van het SBD-apparaat, schakel de pacemaker-integratie in en wijzig de start modus van SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Het `softdog` configuratie bestand maken

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Laad nu de module

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Cluster installatie

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** SLES bijwerken

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** installatie onderdeel, vereist voor cluster bronnen

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Azure-lb-onderdeel installeren dat nodig is voor cluster bronnen

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Controleer de versie van de pakket bron-agents en zorg ervoor dat aan de minimale versie vereisten wordt voldaan:  
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018. a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resource-agents-4.3.0184.6 ee15eb2-4.13.1 zijn.  

1. **[A]** het besturings systeem configureren

   In sommige gevallen maakt pacemaker veel processen, waardoor het toegestane aantal processen wordt uitgeput. In dat geval kan een heartbeat tussen de cluster knooppunten mislukken en kan de failover van uw resources worden uitgevoerd. Het is raadzaam om de Maxi maal toegestane processen te verhogen door de volgende para meter in te stellen.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Verklein de omvang van de gewijzigde cache. Zie [lage schrijf prestaties op SLES 11/12-servers met grote hoeveel heden RAM-geheugen](https://www.suse.com/support/kb/doc/?id=7010287)voor meer informatie.

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Cloud-netconfig-Azure voor HA-cluster configureren

   >[!NOTE]
   > Controleer de geïnstalleerde versie van Package **Cloud-netconfig-Azure** door **Zypper info Cloud-netconfig-Azure** uit te voeren. Als de versie in uw omgeving 1,3 of hoger is, is het niet meer nodig om het beheer van netwerk interfaces door de invoeg toepassing voor het Cloud netwerk te onderdrukken. Als de versie lager is dan 1,3, raden we aan om het pakket **Cloud-netconfig-Azure** bij te werken naar de meest recente beschik bare versie.  

   Wijzig het configuratie bestand voor de netwerk interface zoals hieronder wordt weer gegeven om te voor komen dat de invoeg toepassing van het Cloud netwerk het virtuele IP-adres verwijdert (pacemaker moet de VIP-toewijzing beheren). Zie [SuSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)voor meer informatie. 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** SSH-toegang inschakelen

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** SSH-toegang inschakelen

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** SSH-toegang inschakelen

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A] het** pakket fence-agents installeren, als u een STONITH-apparaat gebruikt, op basis van de Azure Fence-agent.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > De geïnstalleerde versie van pakket **Fence-agents** moet ten minste **4.4.0**  zijn om te profiteren van de snellere failover-tijden met de Azure Fence-agent, als een cluster knooppunt moet worden geomheiningd. Het is raadzaam om het pakket bij te werken als er een lagere versie wordt uitgevoerd.  


1. **[A]** Azure python SDK installeren 
   - Op SLES 12 SP4 of SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - Op SLES 15 en hoger 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >Afhankelijk van uw versie en type installatie kopie moet u mogelijk de open bare Cloud extensie voor de besturingssysteem versie activeren voordat u Azure python SDK kunt installeren.
   >U kunt de uitbrei ding controleren door SUSEConnect---List-Extensions uit te voeren.  
   >De snellere failover-tijden met Azure Fence agent:
   > - op SLES 12 SP4 of SLES 12 SP5 Installeer versie **4.6.2** of hoger van pakket python-Azure-verwerkings-compute  
   > - op SLES 15 installeren-versie **4.6.2** of hoger van pakket python **3**-Azure--compute 

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten.

   >[!IMPORTANT]
   > Als u hostnamen in de cluster configuratie gebruikt, is het essentieel dat u een betrouw bare naam omzetting voor hostnamen hebt. De cluster communicatie mislukt als de namen niet beschikbaar zijn en die kunnen leiden tot vertragingen in de failover van het cluster.
   > Het voor deel van het gebruik van bestand/etc/hosts is dat uw cluster onafhankelijk van DNS wordt. Dit kan ook een enkel storings punt zijn.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** cluster installeren
- Als u gebruikmaakt van SBD-apparaten voor omheining
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- Als *u geen gebruik maakt* van SBD-apparaten voor omheining
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** knoop punt toevoegen aan cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** hacluster wacht woord wijzigen in hetzelfde wacht woord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** corosync-instellingen aanpassen.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Voeg de volgende vetgedrukte inhoud toe aan het bestand als er geen of andere waarden zijn. Zorg ervoor dat u het token wijzigt in 30000 om het onderhoud van geheugen behoud toe te staan. Zie [dit artikel voor Linux][virtual-machines-linux-maintenance] of [Windows][virtual-machines-windows-maintenance]voor meer informatie.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Start de corosync-service vervolgens opnieuw

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Standaard pacemaker-configuratie voor SBD

De configuratie in deze sectie is alleen van toepassing als u gebruikmaakt van SBD STONITH.  

1. **[1]** het gebruik van een STONITH-apparaat inschakelen en de Fence-vertraging instellen

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Een STONITH-apparaat van Azure Fence agent maken

Deze sectie van de documentatie is alleen van toepassing als STONITH wordt gebruikt op basis van de Azure Fence-agent.
Het STONITH-apparaat gebruikt een Service-Principal om te autoriseren bij Microsoft Azure. Volg deze stappen om een service-principal te maken.

1. Ga naar <https://portal.azure.com>
1. Open de Blade Azure Active Directory  
   Ga naar Eigenschappen en noteer de map-id. Dit is de **Tenant-id**.
1. Klik op App-registraties
1. Klik op nieuwe registratie
1. Voer een naam in, selecteer alleen accounts in deze organisatie Directory 
2. Selecteer het toepassings type ' Web ', voer een aanmeldings-URL in (bijvoorbeeld http://localhost) en klik op toevoegen.  
   De aanmeldings-URL wordt niet gebruikt en kan een geldige URL zijn
1. Selecteer certificaten en geheimen en klik vervolgens op nieuw client geheim
1. Voer een beschrijving in voor een nieuwe sleutel, selecteer nooit verloopt en klik op toevoegen
1. Schrijf de waarde op. Dit wordt gebruikt als het **wacht woord** voor de Service-Principal
1. Selecteer Overzicht. Noteer de toepassings-id. Deze wordt gebruikt als de gebruikers naam (**aanmeldings-id** in de onderstaande stappen) van de Service-Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** een aangepaste rol maken voor de Fence-agent

De service-principal heeft standaard geen machtigingen voor toegang tot uw Azure-resources. U moet de Service-Principal machtigingen geven om alle virtuele machines van het cluster te starten en te stoppen (toewijzing ongedaan te maken). Als u de aangepaste rol nog niet hebt gemaakt, kunt u deze maken met behulp van [Power shell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) of [Azure cli](../../../role-based-access-control/custom-roles-cli.md)

Gebruik de volgende inhoud voor het invoer bestand. U moet de inhoud aanpassen aan uw abonnementen, door c276fc76-9cd4-44c9-99a7-4fd71546436e en e91d47c4-76f3-4271-a796-21b4ecfe3624 te vervangen door de Id's van uw abonnement. Als u slechts één abonnement hebt, verwijdert u de tweede vermelding in AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** de aangepaste rol toewijzen aan de Service-Principal

Wijs de aangepaste rol Linux Fence-agent rol toe die in het laatste hoofd stuk is gemaakt voor de Service-Principal. Gebruik de rol owner niet meer.

1. Ga naar [https://portal.azure.com](https://portal.azure.com)
1. Open de blade Alle resources
1. De virtuele machine van het eerste clusterknooppunt selecteren
1. Klik op Toegangsbeheer (IAM)
1. Klik op roltoewijzing toevoegen
1. Selecteer de rol ' Linux Fence-agent functie '
1. Voer de naam in van de toepassing die u hierboven hebt gemaakt
1. Klik op Opslaan

Herhaal de bovenstaande stappen voor het tweede cluster knooppunt.

### <a name="1-create-the-stonith-devices"></a>**[1]** de STONITH-apparaten maken

Nadat u de machtigingen voor de virtuele machines hebt bewerkt, kunt u de STONITH-apparaten in het cluster configureren.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 \ 
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> De bewakings-en omheinings bewerkingen worden gedeserialiseerd. Als er een langere bewakings bewerking en gelijktijdige Afleidings gebeurtenis optreedt, is er dus geen vertraging voor de failover van het cluster vanwege de bewakings bewerking die al wordt uitgevoerd.

> [!TIP]
>De Azure Fence-agent vereist uitgaande connectiviteit met open bare eind punten zoals gedocumenteerd, samen met mogelijke oplossingen, in [open bare-eindpunt connectiviteit voor vm's met behulp van standaard ILB](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemaker-configuratie voor geplande Azure-evenementen

Azure biedt [geplande gebeurtenissen](../../linux/scheduled-events.md). Geplande gebeurtenissen worden via de meta gegevens service verschaft en bieden de tijd om de toepassing voor te bereiden op gebeurtenissen zoals het afsluiten van de VM, het opnieuw implementeren van VM'S, enzovoort. **[Azure-gebeurtenissen](https://github.com/ClusterLabs/resource-agents/pull/1161)** van de resource agent voor geplande Azure-gebeurtenissen. Als er gebeurtenissen worden gedetecteerd, probeert de agent alle resources op de betrokken VM te stoppen en deze naar een ander knoop punt in het cluster te verplaatsen. Om te voor komen dat er extra pacemaker-resources moeten worden geconfigureerd. 

1. **[A]** Controleer of het pakket voor de **Azure-gebeurtenis** agent al is geïnstalleerd en up-to-date is. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Configureer de resources in pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Nadat u de pacemaker-resources voor Azure-Events agent hebt geconfigureerd en u het cluster in of uit de onderhouds modus plaatst, worden er mogelijk waarschuwings berichten weer gegeven als:  
     Waarschuwing: CIB-Boots trap: options: onbekend kenmerk ' hostName_ <strong> hostName</strong>'  
     Waarschuwing: CIB-Boots trap: opties: onbekend kenmerk ' Azure-events_globalPullState '  
     Waarschuwing: CIB-Boots trap: options: onbekend kenmerk ' hostName_ <strong>hostName</strong>'  
   > Deze waarschuwings berichten kunnen worden genegeerd.

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [Hoge Beschik baarheid voor NFS op Azure Vm's op SUSE Linux Enterprise Server][sles-nfs-guide]
* [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server voor SAP-toepassingen][sles-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
