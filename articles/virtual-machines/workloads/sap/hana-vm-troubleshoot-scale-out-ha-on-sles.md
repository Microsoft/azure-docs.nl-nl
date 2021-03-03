---
title: SAP HANA scale-out HSR-Pacemaker met SLES op Azure Vm's Troubleshooting | Microsoft Docs
description: Gids voor het controleren en oplossen van problemen met een complexe SAP HANA scale-out configuratie met hoge Beschik baarheid op basis van SAP HANA System Replication (HSR) en pacemaker op SLES 12 SP3 dat wordt uitgevoerd op Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: f4c1de484ce2659a7e84a1546a7c49c1d77a7d56
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674487"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Controleren en problemen oplossen SAP HANA scale-out instellingen voor hoge Beschik baarheid in SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Dit artikel helpt u bij het controleren van de pacemaker-cluster configuratie voor SAP HANA scale-out die wordt uitgevoerd op Azure virtual machines (Vm's). De Cluster installatie is uitgevoerd in combi natie met SAP HANA System Replication (HSR) en de SUSE RPM package SAPHanaSR-Uitschalen. Alle tests zijn uitgevoerd op SUSE SLES 12 SP3. De secties van het artikel hebben betrekking op verschillende gebieden en bevatten voorbeeld opdrachten en fragmenten uit configuratie bestanden. Deze voor beelden worden aangeraden als methode om de hele cluster installatie te controleren en te controleren.



## <a name="important-notes"></a>Belangrijke opmerkingen

Alle tests voor SAP HANA scale-out in combi natie met SAP HANA systeem replicatie en pacemaker zijn alleen uitgevoerd met SAP HANA 2,0. De versie van het besturings systeem is SUSE Linux Enterprise Server 12 SP3 voor SAP-toepassingen. Het nieuwste RPM-pakket, SAPHanaSR-ScaleOut van SUSE, is gebruikt om het pacemaker-cluster in te stellen.
SUSE heeft een [gedetailleerde beschrijving gepubliceerd van deze instellingen die zijn geoptimaliseerd voor de prestaties][sles-hana-scale-out-ha-paper].

Voor virtuele-machine typen die worden ondersteund voor SAP HANA scale-out, controleert u de [SAP Hana Certified IaaS Directory][sap-hana-iaas-list].

> [!NOTE]
> Dit artikel bevat verwijzingen naar de termen *Master* en *Slave*, termen die micro soft niet meer gebruikt. Wanneer deze voor waarden worden verwijderd uit de software, worden ze uit dit artikel verwijderd.

Er is een technisch probleem met SAP HANA scale-out in combi natie met meerdere subnetten en Vnic's en het instellen van HSR. Het is verplicht om de nieuwste SAP HANA 2,0-patches te gebruiken waarbij dit probleem is opgelost. De volgende SAP HANA versies worden ondersteund: 

* Rev 2.00.024.04 of hoger 
* Rev 2.00.032 of hoger

Als u ondersteuning van SUSE nodig hebt, volgt u deze [hand leiding][suse-pacemaker-support-log-files]. Verzamel alle informatie over het cluster met SAP HANA hoge Beschik baarheid (HA) zoals beschreven in het artikel. SUSE-ondersteuning heeft deze informatie nodig voor verdere analyse.

Tijdens interne tests wordt de installatie van het cluster tijdens het Azure Portal met een normale, gewende VM afgesloten. Daarom raden we u aan een failover van een cluster op andere manieren te testen. Gebruik methoden zoals het forceren van een kernel of het uitschakelen van de netwerken of het migreren van de **MSL** -resource. Zie de details in de volgende secties. De veronderstelling is dat een standaard afsluiting plaatsvindt met de bedoeling. Het beste voor beeld van een opzettelijk afsluiten is voor onderhoud. Zie de details in [gepland onderhoud](#planned-maintenance).

Tijdens interne tests is de installatie van het cluster tijdens de onderhouds modus geverwarrend na een hand matige SAP HANA overname. U kunt het beste hand matig overschakelen voordat u de onderhouds modus van het cluster beëindigt. Een andere optie is om een failover te activeren voordat u het cluster in de onderhouds modus plaatst. Zie [gepland onderhoud](#planned-maintenance)voor meer informatie. De documentatie van SUSE beschrijft hoe u het cluster op deze manier opnieuw kunt instellen met behulp van de **CRM** -opdracht. Maar de eerder genoemde benadering was tijdens interne tests krachtig en nooit onverwachte neven effecten weer gegeven.

Wanneer u de opdracht **CRM migreren** gebruikt, moet u ervoor zorgen dat u de cluster configuratie opschoont. Er worden locatie beperkingen toegevoegd waarvan u mogelijk niet op de hoogte bent. Deze beperkingen zijn van invloed op het gedrag van het cluster. Meer informatie vindt u in [gepland onderhoud](#planned-maintenance).



## <a name="test-system-description"></a>Beschrijving van test systeem

 Voor SAP HANA scale-out HA-verificatie en-certificering werd een installatie gebruikt. Het bestaat uit twee systemen met drie SAP HANA knoop punten: één master en twee werk rollen. De volgende tabel bevat de namen van virtuele machines en interne IP-adressen. Alle verificatie voorbeelden die volgen, zijn uitgevoerd op deze Vm's. Door gebruik te maken van deze VM-namen en IP-adressen in de voor beelden van de opdracht, kunt u beter inzicht krijgen in de opdrachten en de bijbehorende uitvoer:


| Knooppunt type | VM-naam | IP-adres |
| --- | --- | --- |
| Hoofd knooppunt op site 1 | HSO-Hana-VM-S1-0 | 10.0.0.30 |
| Worker-knoop punt 1 op site 1 | HSO-Hana-VM-S1-1 | 10.0.0.31 |
| Worker-knoop punt 2 op site 1 | HSO-Hana-VM-S1-2 | 10.0.0.32 |
| | | |
| Hoofd knooppunt op site 2 | HSO-Hana-VM-S2-0 | 10.0.0.40 |
| Worker-knoop punt 1 op site 2 | HSO-Hana-VM-S2-1 | 10.0.0.41 |
| Worker-knoop punt 2 op site 2 | HSO-Hana-VM-S2-2  | 10.0.0.42 |
| | | |
| Hoofd knooppunt van Maker | HSO-Hana-DM | 10.0.0.13 |
| SBD-apparaatinterface | HSO-Hana-SBD | 10.0.0.19 |
| | | |
| NFS-server 1 | HSO-NFS-VM-0 | 10.0.0.15 |
| NFS-server 2 | HSO-NFS-VM-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Meerdere subnetten en Vnic's

De volgende SAP HANA netwerk aanbevelingen zijn drie subnetten gemaakt binnen één virtueel Azure-netwerk. SAP HANA scale-out op Azure moet worden geïnstalleerd in de niet-gedeelde modus. Dit betekent dat elk knoop punt lokale schijf volumes gebruikt voor **/Hana/data** en **/Hana/log**. Omdat de knoop punten alleen lokale schijf volumes gebruiken, is het niet nodig om een afzonderlijk subnet voor opslag te definiëren:

- 10.0.2.0/24 voor de communicatie tussen SAP HANA tussen knoop punten
- 10.0.1.0/24 voor SAP HANA-systeem replicatie (HSR)
- 10.0.0.0/24 voor alle andere zaken

Zie [SAP HANA global.ini](#sap-hana-globalini)voor meer informatie over SAP Hana configuratie met betrekking tot het gebruik van meerdere netwerken.

Elke VM in het cluster heeft drie Vnic's die overeenkomen met het aantal subnetten. [Het maken van een virtuele Linux-machine in azure met meerdere netwerk interface kaarten][azure-linux-multiple-nics] beschrijft een mogelijke routerings probleem in azure bij het implementeren van een virtuele Linux-machine. Dit specifieke routerings artikel is alleen van toepassing op het gebruik van meerdere Vnic's. Het probleem wordt door SUSE per standaard in SLES 12 SP3 opgelost. Zie voor meer informatie [multi-NIC met Cloud-netconfig in EC2 en Azure][suse-cloud-netconfig].


Voer de volgende opdrachten uit om te controleren of SAP HANA correct is geconfigureerd voor het gebruik van meerdere netwerken. Controleer eerst op het niveau van het besturings systeem dat alle drie de interne IP-adressen voor alle drie de subnetten actief zijn. Als u de subnetten met verschillende IP-adresbereiken hebt gedefinieerd, moet u de opdrachten aanpassen:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

De volgende voorbeeld uitvoer is afkomstig uit het tweede worker-knoop punt op site 2. U kunt drie verschillende interne IP-adressen zien van ETH0, eth1 en eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Controleer vervolgens de SAP HANA poorten voor de naam server en de HSR. SAP HANA moet worden geluisterd naar de bijbehorende subnetten. Afhankelijk van het SAP HANA-exemplaar nummer, moet u de opdrachten aanpassen. Het exemplaar nummer voor het test systeem is **00**. Er zijn verschillende manieren om erachter te komen welke poorten er worden gebruikt. 

De volgende SQL-instructie retourneert de exemplaar-ID, het exemplaar nummer en andere informatie:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Als u de juiste poort nummers zoekt, kunt u bijvoorbeeld in HANA studio onder **configuratie** of via een SQL-instructie zoeken:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Als u wilt zoeken naar elke poort die wordt gebruikt in de SAP-software stack, inclusief SAP HANA, zoekt u naar [TCP/IP-poorten van alle SAP-producten][sap-list-port-numbers].

Gezien het exemplaar nummer **00** in het test systeem van SAP Hana 2,0 is het poort nummer voor de naam server **30001**. Het poort nummer voor HSR-meta gegevens communicatie is **40002**. U kunt zich ook aanmelden bij een worker-knoop punt en vervolgens de master node-Services controleren. Voor dit artikel hebben we gekeken dat worker Node 2 op site 2 probeert verbinding te maken met het hoofd knooppunt op site 2.

Controleer de naam server poort:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Als u wilt bewijzen dat de communicatie tussen knoop punten gebruikmaakt van subnet **10.0.2.0/24**, zou het resultaat eruitzien als in de volgende voorbeeld uitvoer.
Alleen de verbinding via het subnet **10.0.2.0/24** moet slagen:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Controleer nu op HSR-poort **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Als u wilt bewijzen dat de HSR-communicatie gebruikmaakt van subnet **10.0.1.0/24**, zou het resultaat eruitzien als in de volgende voorbeeld uitvoer.
Alleen de verbinding via het subnet **10.0.1.0/24** moet slagen:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Het **corosync** -configuratie bestand moet correct zijn op elk knoop punt in het cluster, met inbegrip van het hoofd knooppunt van de maker. Als de cluster koppeling van een knoop punt niet werkt zoals verwacht, maakt of kopieert u **/etc/corosync/corosync.conf** hand matig op alle knoop punten en start u de service opnieuw. 

De inhoud van **corosync. conf** van het test systeem is een voor beeld.

De eerste sectie is **totem**, zoals beschreven in [cluster installatie](./high-availability-guide-suse-pacemaker.md#cluster-installation), stap 11. U kunt de waarde voor **mcastaddr** negeren. U hoeft alleen maar de bestaande vermelding te gebruiken. De vermeldingen voor **token** en **consensus** moeten worden ingesteld volgens [Microsoft Azure SAP Hana documentatie][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

De tweede sectie, logboek registratie, is niet gewijzigd ten **opzichte** van de opgegeven standaard waarden:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

In het derde gedeelte wordt de **nodelist** weer gegeven. Alle knoop punten van het cluster moeten worden weer gegeven met hun **nodeId**:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

In de laatste sectie **quorum** is het belang rijk om de waarde voor **expected_votes** correct in te stellen. Dit moet het aantal knoop punten zijn met inbegrip van het hoofd knooppunt van de maker. En de waarde voor **two_node** moet **0** zijn. Verwijder de vermelding niet volledig. Stel de waarde in op **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Start de service opnieuw via **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD-apparaat

Het instellen van een SBD-apparaat op een virtuele Azure-machine wordt beschreven in [SBD omheining](./high-availability-guide-suse-pacemaker.md#sbd-fencing).

Controleer eerst op de SBD Server-VM of er ACL-vermeldingen zijn voor elk knoop punt in het cluster. Voer de volgende opdracht uit op de SBD Server-VM:


<pre><code>
targetcli ls
</code></pre>


Op het test systeem ziet de uitvoer van de opdracht eruit als in het volgende voor beeld. ACL-namen, zoals **IQN. 2006-04. HSO-DB-0. local: HSO-DB-0** , moeten worden ingevoerd als de bijbehorende initiator namen op de vm's. Elke VM moet een ander abonnement hebben.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Controleer vervolgens of de namen van de initia tors op alle Vm's verschillend zijn en overeenkomen met de eerder vermelde vermeldingen. Dit voor beeld is van worker Node 1 op site 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

De uitvoer ziet eruit als in het volgende voor beeld:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Controleer vervolgens of de **detectie** goed werkt. Voer de volgende opdracht uit op elk cluster knooppunt met behulp van het IP-adres van de SBD-Server-VM:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

De uitvoer moet er ongeveer uitzien als in het volgende voor beeld:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Het volgende bewijs punt is om te controleren of het knoop punt het SDB-apparaat ziet. Controleer de service op elk knoop punt, met inbegrip van het hoofd knooppunt van de maker:

<pre><code>
lsscsi | grep dbhso
</code></pre>

De uitvoer moet er ongeveer uitzien als in het volgende voor beeld. De namen kunnen echter verschillen. De naam van het apparaat kan ook worden gewijzigd nadat de virtuele machine opnieuw is opgestart:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Afhankelijk van de status van het systeem helpt het soms de iSCSI-services opnieuw te starten om problemen op te lossen. Voer vervolgens de volgende opdrachten uit:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


U kunt vanuit een wille keurig knoop punt controleren of alle knoop punten **duidelijk** zijn. Zorg ervoor dat u de juiste naam voor het apparaat gebruikt op een specifiek knoop punt:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

De uitvoer moet **duidelijk** weer geven voor elk knoop punt in het cluster:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Een andere SBD-controle is de **dump** optie van de **SBD** -opdracht. In dit voor beeld van een opdracht en uitvoer van het hoofd knooppunt van de maker is de naam van het apparaat **SDD**, niet **SDM**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

De uitvoer, naast de naam van het apparaat, moet er op alle knoop punten hetzelfde uitzien:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Een meer controle voor SBD is de mogelijkheid om een bericht naar een ander knoop punt te verzenden. Als u een bericht wilt verzenden naar worker-knoop punt 2 op site 2, voert u de volgende opdracht uit op worker-knoop punt 1 op site 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Op de doel-VM zijde, **HSO-Hana-VM-S2-2** in dit voor beeld, kunt u de volgende vermelding vinden in **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Controleer of de vermeldingen in **/etc/sysconfig/SBD** overeenkomen met de beschrijving bij het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](./high-availability-guide-suse-pacemaker.md#sbd-fencing). Controleer of de opstart instelling in **/etc/iscsi/iscsid.conf** is ingesteld op automatisch.

De volgende vermeldingen zijn belang rijk in **/etc/sysconfig/SBD**. Pas de **id-** waarde indien nodig aan:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Controleer de opstart instelling in **/etc/iscsi/iscsid.conf**. De vereiste instelling zou moeten zijn gebeurd met de volgende **iscsiadm** -opdracht, zoals beschreven in de documentatie. Controleer en pas deze hand matig aan met **VI** als het verschilt.

Met deze opdracht wordt het opstart gedrag ingesteld:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Dit item in **/etc/iscsi/iscsid.conf** maken:

<pre><code>
node.startup = automatic
</code></pre>

Tijdens het testen en verifiëren na het opnieuw opstarten van een virtuele machine is het SBD-apparaat in sommige gevallen niet meer zichtbaar. Er is een discrepantie tussen de opstart instelling en de weer gegeven YaST2. Voer de volgende stappen uit om de instellingen te controleren:

1. YaST2 starten.
2. Selecteer **Network Services** aan de linkerkant.
3. Schuif omlaag naar **iSCSI-initiator** en selecteer deze.
4. Op het volgende scherm onder het tabblad **service** ziet u de unieke naam van de initiator voor het knoop punt.
5. Controleer boven de naam van de initiator of de start waarde van de **service** is ingesteld op **tijdens het opstarten**.
6. Als dat niet het geval is, stelt u deze in op wanneer u in plaats van **hand matig** **opstart** .
7. Vervolgens schakelt u het bovenste tabblad naar **verbonden doelen**.
8. Op het scherm **verbonden doelen** ziet u een vermelding voor het SBD-apparaat, zoals in dit voor beeld: **10.0.0.19:3260 IQN. 2006-04. dbhso. local: dbhso**.
9. Controleer **of de opstart** waarde is ingesteld **op tijdens het opstarten**.
10. Als dat niet het geval is, kiest u **bewerken** en wijzigt u deze.
11. Sla de wijzigingen op en sluit YaST2 af.



## <a name="pacemaker"></a>Pacemaker

Nadat alles correct is ingesteld, kunt u de volgende opdracht uitvoeren op elk knoop punt om de status van de pacemaker-service te controleren:

<pre><code>
systemctl status pacemaker
</code></pre>

De bovenkant van de uitvoer moet er ongeveer uitzien als in het volgende voor beeld. Het is belang rijk dat de status na **actief** wordt weer gegeven als **geladen** en **actief (wordt uitgevoerd)**. De status na **laden** moet worden weer gegeven als **ingeschakeld**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Als de instelling nog steeds is **uitgeschakeld**, voert u de volgende opdracht uit:

<pre><code>
systemctl enable pacemaker
</code></pre>

Voer de volgende opdracht uit om alle geconfigureerde resources in pacemaker weer te geven:

<pre><code>
crm status
</code></pre>

De uitvoer moet er ongeveer uitzien als in het volgende voor beeld. Het is prima dat de **cln** -en **MSL** -bronnen worden weer gegeven als gestopt op de meerderheid van de VM van de maker, **HSO-Hana-DM**. Er is geen SAP HANA installatie op het hoofd knooppunt van de maker. De **cln** -en **MSL** -bronnen worden dus als gestopt weer gegeven. Het is belang rijk dat het juiste totale aantal Vm's, **7**, wordt weer gegeven. Alle virtuele machines die deel uitmaken van het cluster, moeten worden weer gegeven met de status **online**. Het huidige primaire hoofd knooppunt moet correct worden herkend. In dit voor beeld is het **HSO-Hana-VM-S1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Een belang rijk onderdeel van pacemaker is onderhouds modus. In deze modus kunt u wijzigingen aanbrengen zonder dat u een onmiddellijke cluster actie hoeft te maken. Een voor beeld is het opnieuw opstarten van de VM. Een typische use-case zou gepland zijn voor het besturings systeem of het onderhoud van Azure-infra structuur. Zie [gepland onderhoud](#planned-maintenance). Gebruik de volgende opdracht om pacemaker in te stellen in de onderhouds modus:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Wanneer u de **status van CRM** controleert, ziet u in de uitvoer dat alle resources zijn gemarkeerd als **onbeheerd**. In deze status reageert het cluster niet op wijzigingen zoals het starten of stoppen van SAP HANA.
In het volgende voor beeld ziet u de uitvoer van de opdracht **CRM-status** terwijl het cluster zich in de onderhouds modus bevindt:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Dit opdracht voorbeeld laat zien hoe u de onderhouds modus van het cluster kunt beëindigen:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Een andere **CRM** -opdracht haalt de volledige cluster configuratie op in een editor, zodat u deze kunt bewerken. Nadat de wijzigingen zijn opgeslagen, start het cluster de juiste acties:

<pre><code>
crm configure edit
</code></pre>

Als u de volledige configuratie van het cluster wilt bekijken, gebruikt u de optie **CRM-weer gave** :

<pre><code>
crm configure show
</code></pre>



Na storingen in cluster bronnen wordt met de opdracht **CRM-status** een lijst met **mislukte acties** weer gegeven. Zie het volgende voor beeld van deze uitvoer:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

U moet na storingen een cluster opschonen. Gebruik de **CRM** -opdracht opnieuw en gebruik de opdracht optie **opruimen** om deze mislukte actie vermeldingen op te halen. Geef de overeenkomstige cluster bron de volgende naam:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

De opdracht moet uitvoer retour neren zoals in het volgende voor beeld:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Failover of overname

Zoals beschreven in [belang rijke opmerkingen](#important-notes), mag u geen standaard zonder problemen gebruiken om de failover van het cluster of SAP Hana HSR-overname te testen. In plaats daarvan raden we u aan een kernel in te scha kelen, een bron migratie af te dwingen of om alle netwerken op het niveau van het besturings systeem van een virtuele machine af te sluiten. Een andere methode is de CRM-opdracht **\<node\> stand** . Zie het [SuSE-document][sles-12-ha-paper]. 

De volgende drie voorbeeld opdrachten kunnen een clusterfailover geforceerd:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Zoals beschreven in [gepland onderhoud](#planned-maintenance), is een goede manier om de cluster activiteiten te controleren door **SAPHanaSR-showAttr** uit te voeren met de opdracht **Watch** :

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Het helpt ook de SAP HANA landschaps status te bekijken die afkomstig is van een SAP python-script. De Cluster installatie zoekt naar deze status waarde. Het wordt duidelijk wanneer u denkt dat er een fout is opgetreden in het worker-knoop punt. Als een worker-knoop punt uitvalt, wordt SAP HANA niet onmiddellijk een fout geretourneerd voor de status van het hele scale-out systeem. 

Er zijn enkele nieuwe pogingen om onnodige failovers te voor komen. Het cluster reageert alleen als de status wordt gewijzigd van **OK**, retour waarde **4**, naar **fout**, retour waarde **1**. Het is dus correct als de uitvoer van **SAPHanaSR-showAttr** een virtuele machine met de status **offline** weergeeft. Maar er is nog geen activiteit om de primaire en secundaire te scha kelen. Er wordt geen cluster activiteit geactiveerd, zolang SAP HANA geen fout retourneert.

U kunt de status van de SAP HANA landschap controleren als gebruiker **\<HANA SID\> adm** door het SAP python-script als volgt aan te roepen. Mogelijk moet u het pad aanpassen:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

De uitvoer van deze opdracht moet eruitzien als in het volgende voor beeld. De kolom Status van de **host** en de **algehele status** van de host zijn beide belang rijk. De werkelijke uitvoer is breder, met aanvullende kolommen.
Als u de uitvoer tabel beter leesbaar wilt maken in dit document, zijn de meeste kolommen aan de rechter kant verwijderd:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Er is een andere opdracht voor het controleren van de huidige cluster activiteiten. Bekijk de volgende opdracht en de uitvoer staart nadat het hoofd knooppunt van de primaire site is afgebroken. U kunt de lijst met overgangs acties zien, zoals het **promo veren** van het voormalige secundaire hoofd knooppunt, **HSO-Hana-VM-S2-0**, als de nieuwe primaire master. Als alles goed is en alle activiteiten zijn voltooid, moet deze lijst met **overgangs overzichten** leeg zijn.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Gepland onderhoud 

Er zijn verschillende use-cases voor gepland onderhoud. Eén vraag is of het nu gewoon onderhoud van de infra structuur is, zoals wijzigingen op het niveau van het besturings systeem en de schijf configuratie of een HANA-upgrade.
U kunt aanvullende informatie vinden in documenten van SUSE, zoals een [downtime van nul][sles-zero-downtime-paper] of [SAP Hana scenario met SR-prestaties geoptimaliseerd][sles-12-for-sap]. Deze documenten bevatten ook voor beelden die laten zien hoe u hand matig een primaire migreert.

Er zijn intensieve interne tests uitgevoerd om het gebruik van de infra structuur te controleren. Om te voor komen dat er problemen zijn die te maken hebben met de migratie van de primaire, hebben we besloten om altijd een primaire te migreren voordat u een cluster in de onderhouds modus plaatst. Op deze manier is het niet nodig om het cluster te verg eten over de voormalige situatie: de zijde is primair en de secundaire.

Er zijn twee verschillende situaties in dit opzicht:

- **Gepland onderhoud op de huidige secundaire**. In dit geval kunt u het cluster gewoon in de onderhouds modus plaatsen en het op het secundaire werk doen zonder dat dit van invloed is op het cluster.

- **Gepland onderhoud op de huidige primaire**. Zodat gebruikers tijdens onderhoud kunnen blijven werken, moet u een failover afdwingen. Met deze methode moet u de failover van het cluster activeren op pacemaker en niet alleen op het niveau van de SAP HANA HSR. De pacemaker-configuratie activeert automatisch de SAP HANA overname. U moet ook de failover uitvoeren voordat u het cluster in de onderhouds modus plaatst.

De procedure voor onderhoud op de huidige secundaire site is als volgt:

1. Plaats het cluster in de onderhouds modus.
2. Het werk volt ooien op de secundaire site. 
3. De onderhouds modus van het cluster beëindigen.

De procedure voor onderhoud op de huidige primaire site is complexer:

1. Activeer een failover of SAP HANA overname hand matig via een pacemaker-resource migratie. Bekijk de details die volgen.
2. SAP HANA op de voormalige primaire site wordt afgesloten door de Cluster installatie.
3. Plaats het cluster in de onderhouds modus.
4. Nadat het onderhouds werk is uitgevoerd, registreert u de eerste primaire locatie als de nieuwe secundaire site.
5. De cluster configuratie opschonen. Bekijk de details die volgen.
6. De onderhouds modus van het cluster beëindigen.


Bij het migreren van een resource wordt een vermelding toegevoegd aan de cluster configuratie. Een voor beeld is een failover afgedwongen. U moet deze vermeldingen opschonen voordat u de onderhouds modus beëindigt. Zie het volgende voor beeld.

Eerst moet u een clusterfailover afdwingen door de **MSL** -resource te migreren naar het huidige secundaire hoofd knooppunt. Met deze opdracht geeft u een waarschuwing dat een **verplaatsings beperking** is gemaakt:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Controleer het failoverproces via de opdracht **SAPHanaSR-showAttr**. Als u de cluster status wilt controleren, opent u een speciaal shell venster en start u de opdracht met **Watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

In de uitvoer moet de hand matige failover worden weer gegeven. Het voormalige secundaire hoofd knooppunt is **gepromoveerd**, in dit voor beeld **HSO-Hana-VM-S2-0**. De voormalige primaire site is gestopt, **LSS** waarde **1** voor het voormalige primaire hoofd knooppunt **HSO-Hana-VM-S1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Na de failover van het cluster en SAP HANA overname, plaatst u het cluster in de onderhouds modus zoals beschreven in [pacemaker](#pacemaker).

De opdrachten **SAPHanaSR-showAttr** en **CRM-status** geven niets over de beperkingen die zijn gemaakt door de resource migratie. Een optie om deze beperkingen zichtbaar te maken, is het weer geven van de volledige cluster bron configuratie met de volgende opdracht:

<pre><code>
crm configure show
</code></pre>

Binnen de cluster configuratie vindt u een nieuwe locatie beperking die is veroorzaakt door de voormalige hand matige resource migratie. Dit voor beeld begint met **locatie-cli**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Helaas kunnen dergelijke beperkingen van invloed zijn op het algehele gedrag van het cluster. Daarom is het verplicht om ze opnieuw te verwijderen voordat u het hele systeem back-up brengt. Met de opdracht **unmigrate** kunt u de locatie beperkingen opschonen die eerder zijn gemaakt. De naam kan enigszins verwarrend zijn. De resource wordt niet opnieuw gemigreerd naar de oorspronkelijke VM van waaruit deze is gemigreerd. Alleen de locatie beperkingen worden verwijderd en ook de bijbehorende informatie wordt geretourneerd wanneer u de opdracht uitvoert:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Aan het einde van het onderhouds werk stopt u de onderhouds modus van het cluster zoals wordt weer gegeven in [pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report voor het verzamelen van logboek bestanden

Voor het analyseren van problemen met pacemaker-clusters is het handig en wordt u gevraagd door SUSE-ondersteuning om het **hb_report** -hulp programma uit te voeren. Alle belang rijke logboek bestanden die u nodig hebt om te analyseren wat er is gebeurd, worden verzameld. In deze voorbeeld aanroep wordt gebruikgemaakt van een begin-en eind tijd waarop een specifiek incident heeft plaatsgevonden. Zie ook [belang rijke opmerkingen](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

De opdracht geeft aan waar de gecomprimeerde logboek bestanden worden geplaatst:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

U kunt de afzonderlijke bestanden vervolgens extra heren met behulp van de standaard **tar** -opdracht:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Wanneer u de uitgepakte bestanden bekijkt, vindt u alle logboek bestanden. De meeste hiervan zijn in afzonderlijke directory's geplaatst voor elk knoop punt in het cluster:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Binnen het opgegeven tijds bereik is het huidige hoofd knooppunt **HSO-Hana-VM-S1-0** afgebroken. In het **logboek. log** vindt u vermeldingen die betrekking hebben op deze gebeurtenis:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Een ander voor beeld is het pacemaker-logboek bestand op de secundaire master, die de nieuwe primaire master geworden. Dit fragment toont aan dat de status van het afgebroken primaire hoofd knooppunt is ingesteld op **offline**:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


De volgende fragmenten zijn afkomstig uit het SAP HANA **global.ini** -bestand op cluster site 2. In dit voor beeld ziet u de hostnamen-omzettings vermeldingen voor het gebruik van verschillende netwerken voor SAP HANA interknooppunt communicatie en HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

De cluster-oplossing biedt een browser interface die een GUI biedt voor gebruikers die de voor keur geven aan menu's en afbeeldingen om alle opdrachten op het shell niveau te krijgen.
Als u de browser interface wilt gebruiken, vervangt u door **\<node\>** een werkelijk SAP Hana knoop punt in de volgende URL. Voer vervolgens de referenties in van het cluster (gebruikers **cluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Deze scherm afbeelding toont het cluster Dashboard:


![Hawk-cluster dashboard](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


In dit voor beeld worden de locatie beperkingen weer gegeven die zijn veroorzaakt door een migratie van een cluster bron, zoals wordt uitgelegd in [gepland onderhoud](#planned-maintenance):


![Hawk-lijst beperkingen](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


U kunt ook de **hb_report** uitvoer uploaden in Hawk onder **geschiedenis**, zoals hieronder wordt weer gegeven. Zie hb_report voor het verzamelen van logboek bestanden: 

![Hawk-upload hb_report uitvoer](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Met de **geschiedenis Verkenner** kunt u vervolgens alle cluster overgangen door lopen die zijn opgenomen in de **hb_report** uitvoer:

![Hawk overgangen in de hb_report uitvoer](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Deze laatste scherm afbeelding toont de sectie **Details** van één overgang. Het cluster is gereageerd op een primair hoofd knooppunt crash, knoop punt **HSO-Hana-VM-S1-0**. Nu wordt het secundaire knoop punt bevorderd als de nieuwe Master, **HSO-Hana-VM-S2-0**:

![Hawk enkele overgang](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Volgende stappen

In deze hand leiding wordt een hoge Beschik baarheid voor SAP HANA in een scale-out configuratie beschreven. Naast de-data base is een ander belang rijk onderdeel in een SAP-landschap de SAP net-Weaver-stack. Meer informatie over [hoge Beschik baarheid voor SAP NetWeaver op Azure virtual machines die gebruikmaken van de SuSE Enter prise Linux-server][sap-nw-ha-guide-sles].
