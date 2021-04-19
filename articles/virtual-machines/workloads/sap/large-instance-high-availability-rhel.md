---
title: Hoge beschikbaarheid van Azure Large Instances voor SAP op RHEL
description: Meer informatie over het automatiseren van SAP HANA database-failover met behulp van een Pacemaker-cluster in Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: dc27fd67a3801815464ecd37fea567c02dee6e49
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719039"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Hoge beschikbaarheid van Azure Large Instances voor SAP op RHEL

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *blacklist,* een term die Microsoft niet meer gebruikt. Wanneer deze term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

In dit artikel leert u hoe u het Pacemaker-cluster in RHEL 7.6 configureert om een failover van SAP HANA database te automatiseren. U moet een goed begrip hebben van Linux, SAP HANA en Pacemaker om de stappen in deze handleiding uit te voeren.

De volgende tabel bevat de hostnamen die in dit artikel worden gebruikt. De codeblokken in het artikel tonen de opdrachten die moeten worden uitgevoerd, evenals de uitvoer van deze opdrachten. Let goed op naar het knooppunt waarnaar wordt verwezen in elke opdracht.

| Type | Hostnaam | Knooppunt|
|-------|-------------|------|
|Primaire host|`sollabdsm35`|knooppunt 1|
|Secundaire host|`sollabdsm36`|knooppunt 2|

## <a name="configure-your-pacemaker-cluster"></a>Uw Pacemaker-cluster configureren


Voordat u het cluster kunt configureren, moet u SSH-sleuteluitwisseling instellen om een vertrouwensrelatie tussen knooppunten tot stand te brengen.

1. Gebruik de volgende opdrachten om identieke te `/etc/hosts` maken op beide knooppunten.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    0.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    0.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    0.20.251.150 sollabdsm36-st

    10.20.251.151 sollabdsm35-st

    

    10.20.252.151 sollabdsm36-back

    10.20.252.150 sollabdsm35-back

    

    10.20.253.151 sollabdsm36-node

    10.20.253.150 sollabdsm35-node

    ```

2.  De SSH-sleutels maken en uitwisselen.
    1. SSH-sleutels genereren.

       ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
       ```
    2. Kopieer sleutels naar de andere hosts voor SSH zonder wachtwoord.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Schakel op beide knooppunten uit.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Start de servers opnieuw op en gebruik vervolgens de volgende opdracht om de status van de computer te controleren.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Configureer NTP (Network Time Protocol). De tijd- en tijdzones voor beide clusterknooppunten moeten overeenkomen. Gebruik de volgende opdracht om de inhoud van het bestand `chrony.conf` te openen en te controleren.
    1. De volgende inhoud moet worden toegevoegd aan het configuratiebestand. Wijzig de werkelijke waarden volgens uw omgeving.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Schakel de chrony-service in. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Het systeem bijwerken
    1. Installeer eerst de nieuwste updates op het systeem voordat u begint met het installeren van het SBD-apparaat.
    1. Als u geen volledige update van het systeem wilt, zelfs als wordt aanbevolen, moet u minimaal de volgende pakketten bijwerken.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```
 

7. Installeer de SAP HANA- en RHEL-HA-opslagplaatsen.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Installeer de hulpprogramma's Pacemaker, SBD, OpenIPMI, ipmitools en fencing_sbd op alle knooppunten.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitools
    ```

  ## <a name="configure-watchdog"></a>Watchdog configureren

In deze sectie leert u hoe u Watchdog configureert. In deze sectie worden dezelfde twee hosts en gebruikt waarnaar aan het begin van `sollabdsm35` `sollabdsm36` dit artikel wordt verwezen.

1. Zorg ervoor dat de watchdog-daemon niet wordt uitgevoerd op systemen.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. De standaard-Linux-watchdog, die tijdens de installatie wordt geïnstalleerd, is de iTCO-watchdog die niet wordt ondersteund door UCS- en HPE SDFlex-systemen. Daarom moet deze watchdog worden uitgeschakeld.
    1. De verkeerde watchdog is geïnstalleerd en geladen op het systeem:
       ```
   
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Los het verkeerde stuurprogramma uit de omgeving:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Om ervoor te zorgen dat het stuurprogramma niet wordt geladen tijdens de volgende keer opstarten van het systeem, moet het stuurprogramma worden geblokkeerd. Als u de iTCO-modules wilt blokkeren, voegt u het volgende toe aan het einde van het `50-blacklist.conf` bestand:
       ```
   
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Kopieer het bestand naar de secundaire host.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Test of de ipmi-service is gestart. Het is belangrijk dat de IPMI-timer niet actief is. Het timerbeheer wordt uitgevoerd vanuit de SBD Pacemaker-service.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Het vereiste apparaat is standaard /dev/watchdog wordt niet gemaakt.

    ```
    No watchdog device was created

    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Configureer de IPMI-watchdog.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Kopieer het watchdog-configuratiebestand naar de secundaire.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Schakel de ipmi-service in en start deze.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Nu is de IPMI-service gestart en wordt het apparaat /dev/watchdog gemaakt– Maar de timer is nog steeds gestopt. Later beheert de SBD het opnieuw instellen van de watchdog en schakelt de IPMI-timer in.
7.  Controleer of /dev/watchdog bestaat, maar niet in gebruik is.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD-configuratie
In deze sectie leert u hoe u SBD configureert. In deze sectie worden dezelfde twee hosts gebruikt, en , waarnaar aan het begin van `sollabdsm35` `sollabdsm36` dit artikel wordt verwezen.

1.  Zorg ervoor dat de iSCSI- of FC-schijf zichtbaar is op beide knooppunten. In dit voorbeeld wordt een op FC gebaseerd SBD-apparaat gebruikt. Zie de referentiedocumentatie voor meer informatie [](http://www.linux-ha.org/wiki/SBD_Fencing)over SBD-fencing.
2.  De LUN-ID moet identiek zijn op alle knooppunten.
  
3.  Controleer de status van meerdere paden voor het SBD-apparaat.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Het maken van de SBD-schijven en het instellen van de primitieve fencing van het cluster. Deze stap moet worden uitgevoerd op het eerste knooppunt.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Kopieer de SBD-configuratie naar knooppunt2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Controleer of de SBD-schijf zichtbaar is op beide knooppunten.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Voeg het SBD-apparaat toe aan het SBD-configuratiebestand.

    ```
    \# SBD_DEVICE specifies the devices to use for exchanging sbd messages

    \# and to monitor. If specifying more than one path, use ";" as

    \# separator.

    \#

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    \## Type: yesno
     Default: yes
     \# Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Initialisatie van cluster
In deze sectie initialiseert u het cluster. In deze sectie worden dezelfde twee hosts en gebruikt waarnaar aan het begin van `sollabdsm35` `sollabdsm36` dit artikel wordt verwezen.

1.  Stel het wachtwoord van de clustergebruiker in (alle knooppunten).
    ```
    passwd hacluster
    ```
2.  Start PCS op alle systemen.
    ```
    systemctl enable pcsd
    ```
  

3.  Stop de firewall en schakel deze uit (alle knooppunten).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Start de pcsd-service.
    ```
    systemctl start pcsd
    ```
  
  

5.  Voer de clusterverificatie alleen uit vanaf knooppunt1.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36



        Username: hacluster

            Password:

            sollabdsm35.localdomain: Authorized

            sollabdsm36.localdomain: Authorized

     ``` 

6.  Het cluster maken.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Controleer de clusterstatus.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Als één knooppunt niet lid wordt van het cluster, controleert u of de firewall nog steeds wordt uitgevoerd.

  

9. Het SBD-apparaat maken en inschakelen
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  

10. Stop het cluster om de clusterservices opnieuw op te starten (op alle knooppunten).

    ```
    pcs cluster stop --all
    ```


11. Start de clusterservices opnieuw (op alle knooppunten).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync moet de SBD-service starten.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Start het cluster opnieuw op (indien niet automatisch gestart vanaf pcsd).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Schakel Stonith-instellingen in.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Controleer de status van het nieuwe cluster met nu één resource.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Nu moet de IPMI-timer worden uitgevoerd en moet het apparaat /dev/watchdog worden geopend door sbd.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm351 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Controleer de SBD-status.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Test de SBD-fencing door de kernel te crashen.

    * Activeer de kernel-crash.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * De tweede test die moet worden uitgevoerd, is om een knooppunt af te schermen met pcs-opdrachten.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. Voor de rest van de SAP HANA kunt u STONITH uitschakelen door het volgende in te stellen:

   * pcs-eigenschappenset `stonith-enabled=false`
   * Deze parameter moet worden ingesteld op true voor productief gebruik. Als deze parameter niet is ingesteld op true, wordt het cluster niet ondersteund.
   * pcs-eigenschappenset `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>HANA-integratie in het cluster

In deze sectie integreert u HANA in het cluster. In deze sectie worden dezelfde twee hosts, `sollabdsm35` en , gebruikt waarnaar aan het begin van dit artikel wordt `sollabdsm36` verwezen.

Er zijn twee opties voor het integreren van HANA. De eerste optie is een oplossing die is geoptimaliseerd voor kosten, waarbij u het secundaire systeem kunt gebruiken om het QAS-systeem uit te voeren. We raden deze methode niet aan omdat er geen systeem meer is om updates te testen op de clustersoftware, het besturingssysteem of HANA, en configuratie-updates kunnen leiden tot ongeplande downtime van het PRD-systeem. Als het PRD-systeem moet worden geactiveerd op het secundaire systeem, moet de QAS bovendien worden afgesloten op het secundaire knooppunt. De tweede optie is om het QAS-systeem op één cluster te installeren en een tweede cluster voor de PRD te gebruiken. Met deze optie kunt u ook alle onderdelen testen voordat ze in productie worden genomen. In dit artikel wordt beschreven hoe u de tweede optie configureert.


* Dit proces is een build van de RHEL-beschrijving op de pagina:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Stappen die u moet volgen om HSR te configureren

1.  Dit zijn de acties die moeten worden uitgevoerd op knooppunt1 (primair).
    1. Zorg ervoor dat de databaselogboekmodus is ingesteld op normaal.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p SAPhana10 -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA systeemreplicatie werkt alleen nadat de eerste back-up is uitgevoerd. Met de volgende opdracht maakt u een eerste back-up in de `/tmp/` map . Selecteer een correct back-upbestandssysteem voor de database. 
       ```
       * hdbsql -i 00 -u system -p SAPhana10 "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```
    

    3. Back-up maken van alle databasecontainers van deze database.
       ```
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"
   
       
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Schakel het HSR-proces in op het bronsysteem.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Controleer de status van het primaire systeem.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Dit zijn de acties die moeten worden uitgevoerd op knooppunt2 (secundair).
     1. Stop de database.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Alleen voor SAP HANA2.0 kopieert u het SAP HANA en bestanden van het `PKI SSFS_HR2.KEY` `SSFS_HR2.DAT` primaire knooppunt naar het secundaire knooppunt.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Schakel secundair in als de replicatiesite.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Start de database.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Controleer de status van de database.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Het is ook mogelijk om meer informatie over de replicatiestatus op te halen:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Beschrijving van de logboekreplicatiemodus

Zie de officiële SAP-documentatie voor meer informatie over de modus voor [logboekreplicatie.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/c039a1a5b8824ecfa754b55e0caffc01.html)
  

#### <a name="network-setup-for-hana-system-replication"></a>Netwerkinstallatie voor HANA-systeemreplicatie


Om ervoor te zorgen dat het replicatieverkeer het juiste VLAN gebruikt voor de replicatie, moet het correct worden geconfigureerd in `global.ini` de . Als u deze stap overslaat, gebruikt HANA het toegangs-VLAN voor de replicatie, wat mogelijk ongewenst is.


In de volgende voorbeelden wordt de configuratie van de hostnaam resolutie voor systeemreplicatie naar een secundaire site. Er kunnen drie afzonderlijke netwerken worden geïdentificeerd:

* Openbaar netwerk met adressen in het bereik van 10.0.1.*

* Netwerk voor interne SAP HANA communicatie tussen hosts op elke site: 192.168.1.*

* Toegewezen netwerk voor systeemreplicatie: 10.5.1.*

In het eerste voorbeeld is de parameter ingesteld op en worden alleen de hosts van de aangrenzende `[system_replication_communication]listeninterface` `.global` replicerende site opgegeven.

In het volgende voorbeeld is `[system_replication_communication]listeninterface` de parameter ingesteld op en worden alle hosts van beide sites `.internal` opgegeven.

  

### <a name="source-sap-ag-sap-hana-hrs-networking"></a>Bron-SAP AG SAP HANA HRS-netwerken

  

Voor systeemreplicatie is het niet nodig om het bestand te bewerken. Interne hostnamen ('virtueel') moeten worden toegewezen aan IP-adressen in het bestand om een toegewezen netwerk te maken voor `/etc/hosts` systeemreplicatie. `global.ini` De syntaxis hiervoor is als volgt:

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<internal-host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Een SAP HANA configureren in een Pacemaker-cluster
In deze sectie leert u hoe u een SAP HANA configureert in een Pacemaker-cluster. In deze sectie worden dezelfde twee hosts en gebruikt waarnaar aan het begin van `sollabdsm35` `sollabdsm36` dit artikel wordt verwezen.

Zorg ervoor dat u aan de volgende vereisten hebt voldaan:  

* Pacemaker-cluster is geconfigureerd volgens de documentatie en heeft de juiste en werkende fencing

* SAP HANA opstarten bij opstarten is uitgeschakeld op alle clusterknooppunten omdat het starten en stoppen wordt beheerd door het cluster

* SAP HANA replicatie en overname van het systeem met behulp van hulpprogramma's van SAP werken goed tussen clusterknooppunten

* SAP HANA bevat een bewakingsaccount dat door het cluster kan worden gebruikt vanuit beide clusterknooppunten

* Beide knooppunten zijn geabonneerd op de kanalen 'Hoge beschikbaarheid' en 'RHEL for SAP HANA' (RHEL 6,RHEL 7)

  

* Over het algemeen moet u alle pcs-opdrachten alleen uitvoeren vanaf een knooppunt, omdat de CIB automatisch wordt bijgewerkt vanuit de pcs-shell.

* [Meer informatie over quorumbeleid](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Stappen om te configureren 
1. Pc's configureren.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Corosync configureren.
    ```
    https://access.redhat.com/solutions/1293523 --> quorum information RHEL7

    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

1.  Maak een gekloonde SAPHanaTopology-resource.
    ```
    pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1 interleave=true
    SAPHanaTopology resource is gathering status and configuration of SAP
    HANA System Replication on each node. SAPHanaTopology requires
    following attributes to be configured.



        Attribute Name Description

        SID SAP System Identifier (SID) of SAP HANA installation. Must be
    same for all nodes.

    InstanceNumber 2-digit SAP Instance identifier.
    pcs resource show SAPHanaTopology_HR2_00-clone

    Clone: SAPHanaTopology_HR2_00-clone

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true

        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
    type=SAPHanaTopology)

        Attributes: InstanceNumber=00 SID=HR2

        Operations: monitor interval=60 timeout=60
    (SAPHanaTopology_HR2_00-monitor-interval-60)

        start interval=0s timeout=180
    (SAPHanaTopology_HR2_00-start-interval-0s)

        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)

    ```

3.  Maak een primaire/secundaire SAPHana-resource.

    ```
    SAPHana resource is responsible for starting, stopping and relocating the SAP HANA database. This resource must be run as a Primary/    Secondary cluster resource. The resource has the following attributes.

    

    Attribute Name Required? Default value Description

    SID Yes None SAP System Identifier (SID) of SAP HANA installation. Must be same for all nodes.

    InstanceNumber Yes none 2-digit SAP Instance identifier.

    PREFER_SITE_TAKEOVER

    no yes Should cluster prefer to switchover to secondary instance instead of restarting primary locally? ("no": Do prefer restart locally;   "yes": Do prefer takeover to remote site)

    AUTOMATED_REGISTER no false Should the former SAP HANA primary be registered as secondary after takeover and DUPLICATE_PRIMARY_TIMEOUT?     ("false": no, manual intervention will be needed; "true": yes, the former primary will be registered by resource agent as secondary)

    DUPLICATE_PRIMARY_TIMEOUT no 7200 Time difference (in seconds) needed between primary time stamps, if a dual-primary situation occurs. If   the time difference is less than the time gap, then the cluster holds one or both instances in a "WAITING" status. This is to give an   admin a chance to react on a failover. A failed former primary will be registered after the time difference is passed. After this   registration to the new primary all data will be overwritten by the system replication.
    ```
  

5.  Maak de HANA-resource.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary



    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Maak een resource voor een virtueel IP-adres.

    ```
    Cluster will contain Virtual IP address in order to reach the Primary instance of SAP HANA. Below is example command to create IPaddr2  resource with IP 10.7.0.84/24

    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Beperkingen maken.

    ```
    For correct operation we need to ensure that SAPHanaTopology resources are started before starting the SAPHana resources and also that  the virtual IP address is present on the node where the Primary resource of SAPHana is running. To achieve this, the following 2    constraints need to be created.

    pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
    pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
    ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Het handmatig verplaatsen van een SAPHana-resource naar een ander knooppunt testen

#### <a name="sap-hana-takeover-by-cluster"></a>(SAP Hana overnemen per cluster)


Als u de overstap van de SAPHana-resource van het ene knooppunt naar het andere wilt testen, gebruikt u de onderstaande opdracht. Houd er rekening mee dat de optie niet moet worden gebruikt bij het uitvoeren van de volgende opdracht, omdat de `--primary` SAPHana-resource intern werkt.
```pcs resource move SAPHana_HR2_00-primary```

Nadat elke pcs-resource verplaatsen opdracht aanroepen, maakt het cluster locatiebeperkingen voor het verplaatsen van de resource te bereiken. Deze beperkingen moeten worden verwijderd om automatische failover in de toekomst toe te staan.
Als u ze wilt verwijderen, kunt u de volgende opdracht gebruiken.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Meld u aan bij HANA als verificatie.

  * gedegradeerde host:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Gepromoveerde host:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Met de optie `AUTOMATED_REGISTER=false` kunt u niet heen en weer schakelen.

Als deze optie is ingesteld op false, moet u het knooppunt opnieuw registreren:

  
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```
  

Knooppunt2, dat de primaire host was, fungeert nu als de secundaire host.

Overweeg deze optie in te stellen op true om de registratie van de gedegradeerde host te automatiseren.

  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true

pcs cluster node clear node1
```
