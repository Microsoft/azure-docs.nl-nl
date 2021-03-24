---
title: Herstel na nood geval met Azure-toepassing consistent snap shot tool voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u herstel na nood gevallen uitvoert wanneer u het hulp programma voor consistente moment opnamen van Azure-toepassing gebruikt dat u met Azure NetApp Files kunt gebruiken.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 554fa394179e7cfc5b86a2b50eb754547d137a44
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870382"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Herstel na nood geval met Azure-toepassing consistent momentopname programma (preview-versie)

In dit artikel wordt uitgelegd hoe u herstel na nood gevallen uitvoert wanneer u het hulp programma Azure-toepassing consistente moment opname gebruikt dat u met Azure NetApp Files kunt gebruiken.

> [!IMPORTANT]
> Deze bewerking is alleen van toepassing op een **grote Azure-instantie** .

## <a name="introduction"></a>Introductie

Het platform voor grote exemplaren van Azure kan ook een nood herstel site hebben die is geconfigureerd voor het repliceren van moment opnamen van opslag volumes naar.  Als moment opnamen correct zijn geconfigureerd met een dergelijke installatie, is het mogelijk om op deze site een herstel na nood geval uit te voeren.  Dit document is bedoeld als richt lijn voor het uitvoeren van herstel na nood gevallen voor deze installatie.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Vereisten voor het instellen van nood herstel

Aan de volgende vereisten moet worden voldaan voordat u de failover voor herstel na nood gevallen plant.

- U hebt een DR-knoop punt ingericht op de DR-site. Er zijn twee opties voor DR. Het ene is de normale DR en andere is multifunctionele DR.
- U werkt met opslag replicatie. Het micro soft Operations-team voert de configuratie van de opslag replicatie uit op het moment dat de DR-inrichting automatisch wordt ingericht. U kunt de opslag replicatie bewaken met behulp van de opdracht `azacsnap -c details --details replication` op de Dr-site.
- U hebt opslag momentopnamen ingesteld en geconfigureerd op de primaire locatie.
- Er is een HANA-exemplaar geïnstalleerd op de DR-site voor de primaire met dezelfde SID als het primaire exemplaar.
- U leest en begrijpt de procedure voor DR-failover beschreven in [SAP Hana large instances hoge Beschik baarheid en herstel na nood gevallen op Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- U hebt opslag momentopnamen ingesteld en geconfigureerd op de DR-locatie.
- Er is een configuratie bestand (bijvoorbeeld `DR.json` ) gemaakt met de Dr-opslag volumes en de bijbehorende informatie op de Dr-server.
- U hebt de stappen op de DR-site voltooid voor het volgende:
  - Communicatie met opslag inschakelen.
  - Schakel communicatie met SAP HANA in.

## <a name="set-up-disaster-recovery"></a>Herstel na noodgeval instellen

Micro soft ondersteunt replicatie op opslag niveau voor herstel na nood gevallen. Er zijn twee manieren om DR in te stellen.

Een van de **normale** en andere is voor meerdere **doel einden**. In de **normale** Dr hebt u een toegewezen exemplaar op de Dr-locatie voor failover. In het scenario voor meerdere **doel einden** van Dr, hebt u een ander exemplaar van QA of ontwikkeling Hana dat wordt uitgevoerd op de Hana-eenheid voor grote instanties op de Dr-site. Maar u hebt ook een vooraf geïnstalleerd HANA-exemplaar geïnstalleerd dat niet in de actieve versie is en heeft dezelfde SID als de HANA-instantie waarvoor u een failover wilt uitvoeren naar de HANA-grote instantie-eenheid. Micro soft Operations heeft de omgeving ingesteld voor u, inclusief de opslag replicatie op basis van de invoer die is opgegeven in het formulier service aanvraag (SRF) op het moment van de onboarding.

> [!IMPORTANT]
> Zorg ervoor dat aan alle vereisten wordt voldaan voor de installatie van DR.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Gegevens replicatie van primaire naar DR-site bewaken

Het micro soft Operations-team beheert en bewaakt de DR-koppeling van de primaire site naar de DR-site.
U kunt de gegevens replicatie van de primaire server naar een DR-server bewaken met behulp van de opdracht snap shot `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Een failover uitvoeren naar een DR-site

Voer de opdracht failover uit op de DR-site ( `azacsnap -c restore --restore revertvolume` ).

> [!IMPORTANT]
> De `azacsnap -c restore --restore revertvolume` opdracht verbreekt de opslag replicatie van de productie site naar de Dr-site. U moet de micro soft-bewerkingen bereiken om de replicatie opnieuw in te stellen. Zodra de replicatie opnieuw is ingeschakeld, worden alle gegevens op de DR-opslag voor deze SID geïnitialiseerd. De opdracht die de failover uitvoert, maakt de meest recent gerepliceerde opslag momentopname beschikbaar. Als u terug wilt herstellen naar een oudere moment opname, opent u een ondersteunings aanvraag zodat de bewerkingen een eerdere moment opname kunnen maken op de DR-site.

Op hoog niveau zijn dit de stappen die u moet volgen voor de failover van DR:

- U moet het HANA-exemplaar op de **primaire** site afsluiten. Deze actie is alleen nodig als u de failover naar een DR-site wilt uitvoeren, zodat er geen inconsistenties in de gegevens zijn.
- Sluit het HANA-exemplaar op het DR-knoop punt voor de productie-SID.
- Voer de opdracht `azacsnap -c restore --restore revertvolume` uit op het Dr-knoop punt met de sid die moet worden hersteld
  - De opdracht verbreekt de koppeling van de opslag replicatie van de primaire naar de DR-site
  - De opdracht herstelt alleen het/data-en/logbackups-volume, het/Shared-volume is niet hersteld, maar in plaats daarvan wordt de bestaande/Shared voor SID op de DR-locatie gebruikt.
  - Het/data-en/logbackups-volume koppelen: Zorg ervoor dat u het toevoegt aan het fstab-bestand
- Herstel de HANA SYSTEMDB-moment opname. HANA Studio toont alleen de laatste HANA-moment opname die beschikbaar is onder de moment opname van de opslag die is hersteld als onderdeel van de uitvoering van de opdracht `azacsnap -c restore --restore revertvolume` .
- Herstel de Tenant database.
- Start het HANA-exemplaar op de DR-site voor de productie-SID (bijvoorbeeld: h80 in dit geval).
- Testen uitvoeren.

### <a name="example-performing-disaster-recovery"></a>Voor beeld van herstel na nood gevallen

In deze subsectie worden de gedetailleerde stappen beschreven voor een failover naar de site voor nood herstel.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Stap 1: de volume gegevens van het DR-knoop punt ophalen

Voer de opdracht uit `df –h` om de bestands systemen en gekoppelde volumes weer te geven waarnaar moet worden verwezen na de failover.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Stap 2: de HANA op de primaire site uitschakelen

Als u een volledige failover van productie werkbelastingen uitvoert, kunt u verbinding maken met de primaire productie site en de SAP HANA-exemplaren waarvoor failover wordt uitgevoerd, afsluiten naar DR.

Als bijvoorbeeld is aangemeld als root, wordt in het volgende voor beeld weer gegeven hoe SAP HANA kan worden afgesloten.  Vervang door <sid> uw SAP Hana sid.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Stap 3: de HANA op de DR-site uitschakelen

Het is belang rijk dat u SAP HANA op de DR-site afsluit voordat u de volumes herstelt.

Als bijvoorbeeld is aangemeld als root, wordt in het volgende voor beeld weer gegeven hoe SAP HANA kan worden afgesloten.  Vervang door <sid> uw SAP Hana sid.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Zorg ervoor dat de HANA-instanties op de DR-site off line zijn voordat u de volumes herstelt.

#### <a name="step-4-restore-the-volumes"></a>Stap 4: de volumes herstellen

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_Uitvoer van de opdracht Dr-failover_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> De stappen aan het einde van de console moeten worden weer gegeven om de voor bereiding van de opslag voor een DR-failover te volt ooien.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Stap 5: overbodige bestandssysteem ontkoppelen

Voer de opdracht uit `umount` om de bestands systemen/volumes die niet nodig zijn, te ontkoppelen.

```bash
umount <Mount point>
```

Ontkoppelen van de gegevens-en logboek back-mountpoints. Het scale-out-scenario bevat mogelijk meerdere gegevens koppel punt.

#### <a name="step-6-configure-the-mount-points"></a>Stap 6: de koppel punten configureren

Wijzig het bestand `/etc/fstab` om de gegevens en logboeken van back-ups voor de primaire sid te controleren (in dit voor beeld sid = h80) en voeg de nieuwe koppel punt vermeldingen toe die zijn gemaakt op basis van de primaire site Dr-volumes. De nieuwe koppelings punt vermeldingen worden vermeld in de uitvoer van de opdracht.

- Voer een opmerking uit van de bestaande koppel punten die worden uitgevoerd op de DR-site met het `#` teken:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Voeg de volgende regels toe aan `/etc/fstab`
  > Dit moet dezelfde uitvoer van de opdracht zijn

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Stap 7: de herstel volumes koppelen

Voer de opdracht uit `mount –a` om alle koppel punten te koppelen.

```bash
mount -a
```

Als u nu uitvoert, `df –h` ziet u dat de `*_dp` volumes zijn gekoppeld.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Stap 8: de SYSTEMDB herstellen

Klik vanuit de HANA Studio met de rechter muisknop op SYSTEMDB instance en kies back-up en herstel en vervolgens herstel systeem database

Zie de hand leiding voor het herstellen van een Data Base uit een moment opname, met name de SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Stap 9: de Tenant database herstellen

Klik vanuit de HANA Studio met de rechter muisknop op SYSTEMDB instance en kies back-up en herstel en vervolgens ' Tenant-data base herstellen '.

Zie de hand leiding voor het herstellen van een Data Base uit een moment opname, met name de TENANT-data base (s).

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Uitvoeren `azacsnap -c backup` op de Dr-site

Als u back-ups op basis van moment opnamen uitvoert op de DR-site, moet de naam van de HANA-server die is geconfigureerd in het `azacsnap` configuratie bestand op de Dr-site hetzelfde zijn als de naam van de productie server.

> [!IMPORTANT]
> Het uitvoeren `azacsnap -c backup` van de kan opslag momentopnamen maken op de Dr-site, maar deze worden niet automatisch gerepliceerd naar een andere site.  Werk samen met micro soft-bewerkingen om meer inzicht te krijgen in bestanden of gegevens terug naar de oorspronkelijke productie site.

## <a name="next-steps"></a>Volgende stappen

- [Details van de moment opname ophalen](azacsnap-cmd-ref-details.md)
- [Maak een back-up](azacsnap-cmd-ref-backup.md)
