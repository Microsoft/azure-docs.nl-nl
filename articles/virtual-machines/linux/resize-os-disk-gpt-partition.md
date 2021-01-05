---
title: Het formaat van een besturingssysteem schijf met een GPT-partitie wijzigen
description: In dit artikel vindt u instructies voor het wijzigen van de grootte van een besturingssysteem schijf met een GPT-partitie (GUID-partitie tabel) in Linux.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: ab83a3b11aebdc9fed450410aa1f9bee2d25c4bb
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900668"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Het formaat van een besturingssysteem schijf met een GPT-partitie wijzigen

> [!NOTE]
> Dit artikel is alleen van toepassing op besturingssysteem schijven met een GPT-partitie (GUID-partitie tabel).

In dit artikel wordt beschreven hoe u de grootte van een besturingssysteem schijf met een GPT-partitie in Linux kunt verg Roten. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Vaststellen of de besturingssysteem schijf een MBR-of GPT-partitie heeft

Gebruik de `parted` opdracht om te bepalen of de schijf partitie is gemaakt met een master boot record partitie (MBR) of een GPT-partitie.

### <a name="mbr-partition"></a>MBR-partitie

In de volgende uitvoer toont de **partitie tabel** de waarde van **MSDOS**. Met deze waarde wordt een MBR-partitie aangeduid.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT-partitie

In de volgende uitvoer toont de **partitie tabel** de waarde **GPT**. Met deze waarde wordt een GPT-partitie aangeduid.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Als uw virtuele machine (VM) een GPT-partitie op de besturingssysteem schijf heeft, verg root u de besturingssysteem schijf.

## <a name="increase-the-size-of-the-os-disk"></a>De grootte van de besturingssysteem schijf verg Roten

De volgende instructies zijn van toepassing op door Linux goedgekeurde distributies.

> [!NOTE]
> Voordat u doorgaat, maakt u een back-up van uw VM of neemt u een moment opname van de besturingssysteem schijf op.

### <a name="ubuntu"></a>Ubuntu

Verhoog de grootte van de besturingssysteem schijf in Ubuntu 16. *x* en 18. *x*:

1. Stop de virtuele machine.
1. Verhoog de grootte van de besturingssysteem schijf van de portal.
1. Start de virtuele machine opnieuw op en meld u aan bij de VM als een **hoofd** gebruiker.
1. Controleer of op de besturingssysteem schijf nu een groter formaat van het bestands systeem wordt weer gegeven.

In het volgende voor beeld is de grootte van de besturingssysteem schijf gewijzigd van de portal naar 100 GB. Op het **/dev/sda1** -bestands systeem dat is gekoppeld, **/** wordt 97 GB weer gegeven.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

De grootte van de besturingssysteem schijf in SUSE 12 SP4, SUSE SLES 12 voor SAP, SUSE SLES 15 en SUSE SLES 15 voor SAP verg Roten:

1. Stop de virtuele machine.
1. Verhoog de grootte van de besturingssysteem schijf van de portal.
1. Start de VM opnieuw op.

Wanneer de VM opnieuw is opgestart, voert u de volgende stappen uit:

1. Gebruik deze opdracht om toegang te krijgen tot uw VM als een **hoofd** gebruiker:

   ```
   # sudo -i
   ```

1. Gebruik de volgende opdracht om het **growpart** -pakket te installeren dat u gebruikt om de grootte van de partitie te wijzigen:

   ```
   # zypper install growpart
   ```

1. Gebruik de `lsblk` opdracht om de partitie te vinden die is gekoppeld aan de hoofdmap van het bestands systeem ( **/** ). In dit geval ziet u dat partitie 4 van Device **SDA** is gekoppeld aan **/** :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Wijzig de grootte van de vereiste partitie door gebruik te maken van de `growpart` opdracht en het partitie nummer dat u in de vorige stap hebt bepaald:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Voer de `lsblk` opdracht opnieuw uit om te controleren of de partitie is verhoogd.

   In de volgende uitvoer ziet u dat de grootte van de **/dev/sda4** -partitie is gewijzigd in 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Bepaal het type bestands systeem op de besturingssysteem schijf met behulp van de `lsblk` opdracht met de `-f` markering:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Gebruik op basis van het bestandssysteem type de juiste opdrachten om de grootte van het bestands systeem te wijzigen.
   
   Voor **xfs**, gebruikt u deze opdracht:
   
   ```
   #xfs_growfs /
   ```
   
   Voorbeelduitvoer:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Voor **ext4**, gebruikt u deze opdracht:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Controleer de verhoogde grootte van het bestands systeem voor **VG-th** met behulp van deze opdracht:
   
   ```
   #df -Thl
   ```
   
   Voorbeelduitvoer:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   In het vorige voor beeld ziet u dat de bestandssysteem grootte van de besturingssysteem schijf is verhoogd.

### <a name="rhel-with-lvm"></a>RHEL met LVM

1. Gebruik deze opdracht om toegang te krijgen tot uw VM als een **hoofd** gebruiker:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Gebruik de `lsblk` opdracht om te bepalen welk logisch volume (LV) is gekoppeld aan de hoofdmap van het bestands systeem ( **/** ). In dit geval ziet u dat **rootvg-rootlv** is gekoppeld aan **/** . Als u een ander bestands systeem wilt, vervangt u de LV en het koppel punt in dit artikel.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Controleer of er vrije ruimte is in de LVM-volume groep (VG) die de hoofd partitie bevat. Als er vrije ruimte is, gaat u verder met stap 12.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   In dit voor beeld ziet u in de **vrije PE/grootte** van de regel dat er 38,02 GB gratis is in de volume groep. U hoeft het formaat van de schijf niet te wijzigen voordat u ruimte aan de volume groep toevoegt.

1. Verhoog de grootte van de besturingssysteem schijf in RHEL 7. *x* met LVM:

   1. Stop de virtuele machine.
   1. Verhoog de grootte van de besturingssysteem schijf van de portal.
   1. Start de virtuele machine.

1. Wanneer de VM opnieuw is opgestart, voert u de volgende stap uit:

   - Installeer het pakket **Cloud-hulppr-growpart** om de **growpart** -opdracht op te geven. Dit is vereist om de besturingssysteem schijf en de GDISK-handler voor GPT-schijf indelingen te verg Roten. Deze pakketten zijn vooraf geïnstalleerd op de meeste installatie kopieën van Marketplace.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Bepaal op welke schijf en partitie LVM fysieke volume of volumes (HW) in de volume groep met de naam **rootvg** met behulp van de `pvscan` opdracht. Let op de grootte en vrije ruimte tussen de vier Kante haken (**[** en **]**).

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Controleer de grootte van de partitie met behulp van `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Vouw de partitie die deze HW bevat uit met `growpart` de apparaatnaam en het partitie nummer. Hierdoor wordt de opgegeven partitie uitgebreid voor het gebruik van alle vrije aaneengesloten ruimte op het apparaat.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Controleer of de grootte van de partitie is gewijzigd in de verwachte grootte met behulp van de `lsblk` opdracht opnieuw. U ziet dat in het voor beeld **sda4** is gewijzigd van 63 gb in 95 GB.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Vouw de PV uit om de rest van de zojuist uitgebreide partitie te gebruiken:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Controleer of de nieuwe grootte van de HW de verwachte grootte heeft, en vergelijk deze met de oorspronkelijke waarden voor **[grootte/vrij]** :

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Vouw het gewenste logische volume (LV) uit op basis van het gewenste bedrag. De hoeveelheid hoeft niet alle vrije ruimte in de volume groep te zijn. In het volgende voor beeld wordt de grootte van **/dev/mapper/rootvg-rootlv** gewijzigd van 2 GB in 12 GB (een toename van 10 GB). Met deze opdracht wordt ook de grootte van het bestands systeem gewijzigd.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Voorbeelduitvoer:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. De `lvresize` opdracht roept de juiste formaat wijziging automatisch aan voor het bestands systeem in de LV. Controleer of **/dev/mapper/rootvg-rootlv**, dat is gekoppeld aan **/** , een verhoogde bestandssysteem grootte heeft met behulp van de volgende opdracht:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Voorbeelduitvoer:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Als u dezelfde procedure wilt gebruiken om de grootte van een ander logisch volume te wijzigen, wijzigt u de LV-naam in stap 12.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE] 
>Maak altijd een moment opname van de virtuele machine voordat u de schijf grootte van het besturings systeem verg root.

De besturingssysteem schijf in een RHEL RAW-partitie verg Roten:

1. Stop de virtuele machine.
1. Verhoog de grootte van de besturingssysteem schijf van de portal.
1. Start de virtuele machine.

Wanneer de VM opnieuw is opgestart, voert u de volgende stappen uit:

1. Gebruik de volgende opdracht om toegang te krijgen tot uw VM als een **hoofd** gebruiker:
 
   ```
   sudo su
   ```

1. Installeer het **gptfdisk** -pakket, dat vereist is voor het verg Roten van de besturingssysteem schijf.

   ```
   yum install gdisk -y
   ```

1.  Als u alle beschik bare sectoren op de schijf wilt zien, voert u de volgende opdracht uit:
    ```
    gdisk -l /dev/sda
    ```

1. De details van het partitie type worden weer geven. Zorg ervoor dat het GPT is. De basis partitie identificeren. Wijzig of verwijder de opstart partitie (BIOS-opstart partitie) en de systeem partitie (EFI-systeem partitie) niet.

1. Gebruik de volgende opdracht om het partitioneren voor de eerste keer te starten. 
    ```
    gdisk /dev/sda
    ```

1. U ziet nu een bericht waarin u wordt gevraagd om de volgende opdracht (' opdracht:? voor hulp '). 

   ```
   w
   ```

1. Er wordt een waarschuwing weer gegeven met de melding ' waarschuwing! De secundaire header is te vroeg op de schijf geplaatst. Wilt u dit probleem corrigeren? (J/N): ". U moet op Y drukken

   ```
   Y
   ```

1. Er wordt een bericht weer gegeven waarin wordt gemeld dat de definitieve controles zijn voltooid en om bevestiging wordt gevraagd. Druk op Y

   ```
   Y
   ```

1. Controleren of alles goed is gebeurd met behulp van de partprobe-opdracht

   ```
   partprobe
   ```

1. De bovenstaande stappen hebben ervoor gezorgd dat de secundaire GPT-header aan het einde wordt geplaatst. De volgende stap is het proces van het wijzigen van de grootte te starten met het hulp programma gdisk opnieuw. Gebruik de volgende opdracht.

   ```
   gdisk /dev/sda
   ```
1. Druk in het menu opdracht op ' p ' om een lijst met partities weer te geven. Identificeren van de hoofd partitie (in de stappen wordt sda2 beschouwd als de basis partitie) en de opstart partitie (in de stappen wordt sda3 beschouwd als de opstart partitie) 

   ```
   p
   ```
    ![Hoofd partitie en opstart partitie](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Druk op ' om de partitie te verwijderen en selecteer het partitie nummer dat is toegewezen aan opstarten (in dit voor beeld is ' 3 ')
   ```
   d
   3
   ```
1. Druk op ' om de partitie te verwijderen en selecteer het partitie nummer dat is toegewezen aan het opstarten (in dit voor beeld is ' 2 ')
   ```
   d
   2
   ```
    ![De hoofd partitie en opstart partitie verwijderen](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Als u de basis partitie opnieuw wilt maken met een verhoogde grootte, drukt u op ' n ', voert u het partitie nummer in dat u eerder voor root (' 2 ' voor dit voor beeld) hebt verwijderd en kiest u de eerste sector als ' standaard waarde ', laatste sector als ' waarde van de laatste sector-opstart grootte ' (' 4096 in dit geval ' overeenkomt met het 8300 2-
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Als u de opstart partitie opnieuw wilt maken, drukt u op ' n ', voert u het partitie nummer in dat u eerder voor het opstarten hebt verwijderd (' 3 ' voor dit voor beeld) en kiest u de eerste sector als standaard waarde, laatste sector als standaard waarde en hexadecimale code als ' EF02 '
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Schrijf de wijzigingen met de opdracht ' w ' en druk op ' Y ' om te bevestigen
   ```
   w
   Y
   ```
1. Voer de opdracht ' partprobe ' uit om te controleren op schijf stabiliteit
   ```
   partprobe
   ```
1. De virtuele machine opnieuw opstarten en de grootte van de hoofd partitie zijn verhoogd
   ```
   reboot
   ```

   ![Nieuwe hoofd partitie en opstart partitie](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Voer de xfs_growfs opdracht uit op de partitie om de grootte ervan te wijzigen
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS-groeiende FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)


1. Controleer of de nieuwe grootte wordt weer gegeven met behulp van de **DF** -opdracht:

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
