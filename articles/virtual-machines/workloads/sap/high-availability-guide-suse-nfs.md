---
title: Hoge Beschik baarheid voor NFS op Azure Vm's op SLES | Microsoft Docs
description: Hoge Beschik baarheid voor NFS op Azure Vm's op SUSE Linux Enterprise Server
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 05bcb0aebd44dee60fa3f323e1f109e4c0761ec8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022010"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Hoge Beschik baarheid voor NFS op Azure Vm's op SUSE Linux Enterprise Server

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert, het cluster raamwerk installeert en een Maxi maal beschik bare NFS-server installeert die kan worden gebruikt om de gedeelde gegevens van een Maxi maal beschikbaar SAP-systeem op te slaan.
In deze hand leiding wordt beschreven hoe u een Maxi maal beschik bare NFS-server instelt die wordt gebruikt door twee SAP-systemen, NW1 en NW2. De namen van de resources (bijvoorbeeld virtuele machines, virtuele netwerken) in het voor beeld aannemen dat u de SAP- [Bestands server sjabloon][template-file-server] hebt gebruikt met resource voorvoegsel **Prod**.

Lees eerst de volgende SAP-opmerkingen en-documenten

* SAP-opmerking [1928533], die:
  * Lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure

* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2205917] heeft aanbevolen instellingen voor het besturings systeem voor SuSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [1944799] heeft SAP Hana richt lijnen voor SuSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1984787] bevat algemene informatie over SuSE Linux Enterprise Server 12.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Richt lijnen voor best practices voor de maximale Beschik baarheid van SUSE Linux Enter prise 12 SP3][sles-hae-guides]
  * Maxi maal beschik bare NFS-opslag met DRBD en pacemaker
* [SUSE Linux Enterprise Server voor SAP-toepassingen 12 SP3 best practices-hand leidingen][sles-for-sap-bp]
* [Release opmerkingen voor de SUSE-extensie voor hoge Beschik baarheid van 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Overzicht

Voor een hoge Beschik baarheid is SAP NetWeaver een NFS-server nodig. De NFS-server is geconfigureerd in een afzonderlijk cluster en kan worden gebruikt door meerdere SAP-systemen.

![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-nfs/ha-suse-nfs.png)

De NFS-server gebruikt een specifieke virtuele hostnaam en virtuele IP-adressen voor elk SAP-systeem dat deze NFS-server gebruikt. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. De volgende lijst bevat de configuratie van de load balancer.        

* Front-end configuratie
  * IP-adres 10.0.0.4 voor NW1
  * IP-adres 10.0.0.5 voor NW2
* Back-end-configuratie
  * Verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van het NFS-cluster
* Test poort
  * Poort 61000 voor NW1
  * Poort 61001 voor NW2
* Taakverdelings regels (als u basis load balancer gebruikt)
  * 2049 TCP voor NW1
  * 2049 UDP voor NW1
  * 2049 TCP voor NW2
  * 2049 UDP voor NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Een Maxi maal beschik bare NFS-server instellen

U kunt een Azure-sjabloon van GitHub gebruiken om alle vereiste Azure-resources te implementeren, met inbegrip van de virtuele machines, beschikbaarheidsset en load balancer, of u kunt de resources hand matig implementeren.

### <a name="deploy-linux-via-azure-template"></a>Linux implementeren via Azure-sjabloon

De Azure Marketplace bevat een installatie kopie voor SUSE Linux Enterprise Server voor SAP-toepassingen 12 die u kunt gebruiken om nieuwe virtuele machines te implementeren.
U kunt een van de Quick Start-sjablonen op GitHub gebruiken voor het implementeren van alle vereiste resources. De sjabloon implementeert de virtuele machines, de load balancer, de beschik baarheid, enzovoort. Volg deze stappen om de sjabloon te implementeren:

1. Open de [sjabloon SAP-Bestands server][template-file-server] in de Azure Portal   
1. Voer de volgende para meters in
   1. Resource voorvoegsel  
      Geef het voor voegsel op dat u wilt gebruiken. De waarde wordt gebruikt als een voor voegsel voor de resources die worden geïmplementeerd.
   2. Aantal SAP-systemen  
      Voer het aantal SAP-systemen in dat deze bestands server gaat gebruiken. Hiermee implementeert u de vereiste hoeveelheid frontend-configuraties, regels voor taak verdeling, poort testen, schijven, enzovoort.
   3. Type besturings systeem  
      Selecteer een van de Linux-distributies. Selecteer voor dit voor beeld SLES 12
   4. Gebruikers naam en beheerders wachtwoord voor beheerder  
      Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om u aan te melden bij de computer.
   5. Subnet-ID  
      Als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de ID van het specifieke subnet benoemen. De ID ziet er meestal uit als/Subscriptions/**&lt; -abonnements &gt; -id**/resourceGroups/naam **&lt; &gt; van de resource groep**/providers/Microsoft.Network/virtualNetworks/naam **&lt; &gt; van het virtuele netwerk**/subnets/**&lt; subnet naam &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux hand matig implementeren via Azure Portal

U moet eerst de virtuele machines voor dit NFS-cluster maken. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de back-endservers.

1. Een resourcegroep maken
1. Een Virtual Network maken
1. Een beschikbaarheidsset maken  
   Maximum aantal update domeinen instellen
1. Virtuele machine 1 maken gebruik ten minste SLES4SAP 12 SP3, in dit voor beeld wordt de SLES4SAP 12 SP3 BYOS image SLES for SAP-toepassingen 12 SP3 (BYOS) gebruikt  
   Selecteer een Beschikbaarheidsset die u eerder hebt gemaakt  
1. Virtuele machine 2 maken gebruik ten minste SLES4SAP 12 SP3, in dit voor beeld de SLES4SAP 12 SP3 BYOS-installatie kopie  
   SLES for SAP-toepassingen 12 SP3 (BYOS) wordt gebruikt  
   Selecteer een Beschikbaarheidsset die u eerder hebt gemaakt  
1. Voeg één gegevens schijf voor elk SAP-systeem toe aan beide virtuele machines.
1. Maak een Load Balancer (intern). [Standaard Load Balancer](../../../load-balancer/load-balancer-overview.md)worden aanbevolen.  
   1. Volg deze instructies voor het maken van een standaard Load Balancer:
      1. De frontend-IP-adressen maken
         1. IP-adres 10.0.0.4 voor NW1
            1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
            1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld **NW1-** front-end)
            1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.4**)
            1. Klik op OK
         1. IP-adres 10.0.0.5 voor NW2
            * Herhaal de bovenstaande stappen voor NW2
      1. De back-end-Pools maken
         1. Verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van het NFS-cluster
            1. Open de load balancer, selecteer back-endservers en klik op toevoegen
            1. Voer de naam van de nieuwe back-end-groep in (bijvoorbeeld **NW-back-end**)
            1. Virtual Network selecteren
            1. Klik op een virtuele machine toevoegen
            1. Selecteer de virtuele machines van het NFS-cluster en de bijbehorende IP-adressen.
            1. Klik op Toevoegen.
      1. De status tests maken
         1. Poort 61000 voor NW1
            1. Open de load balancer, selecteer status controles en klik op toevoegen
            1. Voer de naam van de nieuwe status test in (bijvoorbeeld **NW1-HP**)
            1. TCP als protocol selecteren, poort 610 **00**, interval 5 en drempel waarde voor onjuiste status 2 gebruiken
            1. Klik op OK
         1. Poort 61001 voor NW2
            * Herhaal de bovenstaande stappen voor het maken van een status test voor NW2
      1. Taakverdelings regels
         1. Open de load balancer, selecteer taakverdelings regels en klik op toevoegen
         1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **NW1-lb**)
         1. Selecteer het frontend-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **NW1-frontend**. **NW-back-end** en **NW1-HP**)
         1. Selecteer **ha-poorten**.
         1. Time-out voor inactiviteit tot 30 minuten verhogen
         1. **Zorg ervoor dat zwevend IP-adressen zijn ingeschakeld**
         1. Klik op OK
         * Herhaal de bovenstaande stappen om de taakverdelings regel voor NW2 te maken
   1. Als uw scenario basis load balancer vereist, volgt u deze instructies:
      1. De frontend-IP-adressen maken
         1. IP-adres 10.0.0.4 voor NW1
            1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
            1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld **NW1-** front-end)
            1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.4**)
            1. Klik op OK
         1. IP-adres 10.0.0.5 voor NW2
            * Herhaal de bovenstaande stappen voor NW2
      1. De back-end-Pools maken
         1. Verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van het NFS-cluster
            1. Open de load balancer, selecteer back-endservers en klik op toevoegen
            1. Voer de naam van de nieuwe back-end-groep in (bijvoorbeeld **NW-back-end**)
            1. Klik op een virtuele machine toevoegen
            1. Selecteer de Beschikbaarheidsset die u eerder hebt gemaakt
            1. De virtuele machines van het NFS-cluster selecteren
            1. Klik op OK
      1. De status tests maken
         1. Poort 61000 voor NW1
            1. Open de load balancer, selecteer status controles en klik op toevoegen
            1. Voer de naam van de nieuwe status test in (bijvoorbeeld **NW1-HP**)
            1. TCP als protocol selecteren, poort 610 **00**, interval 5 en drempel waarde voor onjuiste status 2 gebruiken
            1. Klik op OK
         1. Poort 61001 voor NW2
            * Herhaal de bovenstaande stappen voor het maken van een status test voor NW2
      1. Taakverdelings regels
         1. 2049 TCP voor NW1
            1. Open de load balancer, selecteer taakverdelings regels en klik op toevoegen
            1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **NW1-lb-2049**)
            1. Selecteer het frontend-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **NW1-front-end**)
            1. Behoud protocol **TCP**, voer poort **2049** in
            1. Time-out voor inactiviteit tot 30 minuten verhogen
            1. **Zorg ervoor dat zwevend IP-adressen zijn ingeschakeld**
            1. Klik op OK
         1. 2049 UDP voor NW1
            * Herhaal de bovenstaande stappen voor poort 2049 en UDP voor NW1
         1. 2049 TCP voor NW2
            * Herhaal de bovenstaande stappen voor poort 2049 en TCP voor NW2
         1. 2049 UDP voor NW2
            * Herhaal de bovenstaande stappen voor poort 2049 en UDP voor NW2

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.  

> [!Note]
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  

> [!IMPORTANT]
> Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.

### <a name="create-pacemaker-cluster"></a>Een pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](high-availability-guide-suse-pacemaker.md) om een basis pacemaker-cluster voor deze NFS-server te maken.

### <a name="configure-nfs-server"></a>NFS-server configureren

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** NFS-server inschakelen

   De export vermelding voor het hoofd-NFS maken

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** drbd-onderdelen installeren

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** een partitie maken voor de drbd-apparaten

   Alle beschik bare gegevens schijven weer geven

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Voorbeeld uitvoer
   
   ```
   lun0  lun1
   ```

   Partities maken voor elke gegevens schijf

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** LVM-configuraties maken

   Alle beschik bare partities weer geven

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Voorbeeld uitvoer
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   LVM-volumes voor elke partitie maken

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** drbd configureren

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Zorg ervoor dat het bestand drbd. conf de volgende twee regels bevat

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   De configuratie van de globale drbd wijzigen

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Voeg de volgende items toe aan de handler en de sectie net.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** de NFS drbd-apparaten maken

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   De configuratie voor het nieuwe drbd-apparaat invoegen en afsluiten

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   De configuratie voor het nieuwe drbd-apparaat invoegen en afsluiten

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Het drbd-apparaat maken en starten

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** eerste synchronisatie overs Laan

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** het primaire knoop punt instellen

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** wacht totdat de nieuwe drbd-apparaten zijn gesynchroniseerd

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** bestands systemen op de drbd-apparaten maken

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** drbd detectie van Split-hersenen

   Wanneer u drbd gebruikt voor het synchroniseren van gegevens van de ene host naar een andere, kan een zogenaamde Splits hersenen worden uitgevoerd. Een splits-hersenen is een scenario waarbij beide cluster knooppunten het drbd-apparaat hebben gepromoveerd tot de primaire en niet-gesynchroniseerd. Het kan een zeldzame situatie zijn, maar u moet nog steeds een splits-hersenen zo snel mogelijk verwerken en oplossen. Het is daarom belang rijk om een melding te ontvangen wanneer een split-hersenen is opgetreden.

   Lees [de officiële drbd-documentatie](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) over het instellen van een melding voor gesplitste hersenen.

   Het is ook mogelijk om automatisch te herstellen vanuit een split-hersenen scenario. Lees voor meer informatie [automatisch splitsen voor gesplitste herstel beleid](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Cluster Framework configureren

1. **[1]** Voeg de NFS drbd-apparaten voor de SAP-systeem NW1 toe aan de cluster configuratie

   > [!IMPORTANT]
   > Recente tests hebben getoonde situaties, waarbij netcat niet meer reageert op aanvragen als gevolg van achterstand en de beperking van het verwerken van slechts één verbinding. De netcat-resource stopt met Luis teren naar de Azure Load Balancer-aanvragen en het zwevende IP-adres is niet meer beschikbaar.  
   > Voor bestaande pacemaker-clusters wordt geadviseerd in de laatste Vervang netcat met socat. Momenteel raden we u aan Azure-lb resource agent te gebruiken, die deel uitmaakt van pakket resource-agents, met de volgende pakket versie vereisten:
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018. a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resource-agents-4.3.0184.6 ee15eb2-4.13.1 zijn.  
   >
   > Houd er rekening mee dat voor de wijziging korte uitval tijd nodig is.  
   > Voor bestaande pacemaker-clusters geldt dat als de configuratie al is gewijzigd in het gebruik van socat, zoals beschreven in [Azure Load-Balancer-detectie beveiliging](https://www.suse.com/support/kb/doc/?id=7024128), u niet onmiddellijk over de resource-agent van Azure lb hoeft te scha kelen.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Voeg de NFS drbd-apparaten voor de SAP-systeem NW2 toe aan de cluster configuratie

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   De `crossmnt` optie in de `exportfs` cluster bronnen is aanwezig in onze documentatie voor achterwaartse compatibiliteit met oudere versies van SLES.  

1. **[1]** onderhouds modus uitschakelen
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [De SAP-ASCS en-data base installeren](high-availability-guide-suse.md)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
