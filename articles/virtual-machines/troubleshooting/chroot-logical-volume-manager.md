---
title: Virtuele Linux-machines herstellen met behulp van chroot waarbij LVM (Logical Volume Manager) wordt gebruikt-virtuele machines van Azure
description: Herstel van virtuele Linux-machines met LVMs.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 390443874ea63a8661ef8baea627015fcf679719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002694"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Problemen met een virtuele Linux-machine oplossen wanneer er geen toegang is tot de Azure-seriële console en de schijf indeling gebruikmaakt van LVM (Logical Volume Manager)

Deze hand leiding voor probleem oplossing is van nut voor scenario's waarbij een Linux-VM niet wordt opgestart, SSH niet mogelijk is en de onderliggende bestandssysteem indeling is geconfigureerd met LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Moment opname van de failover van de VM maken

Maak een moment opname van de betrokken VM. 

De moment opname wordt vervolgens gekoppeld aan een virtuele machine voor **herstel** . Volg de instructies in [dit onderwerp](../linux/snapshot-copy-managed-disk.md#use-azure-portal) voor informatie over het maken van een **moment opname**.

## <a name="create-a-rescue-vm"></a>Een herstel-VM maken
Meestal wordt een herstel-VM van dezelfde versie van het besturings systeem aanbevolen. Dezelfde **regio** en **resource groep** van de betrokken VM gebruiken

## <a name="connect-to-the-rescue-vm"></a>Verbinding maken met de virtuele machine voor herstel
Verbinding maken met behulp van SSH in de virtuele machine voor **herstel** . Bevoegdheden verhogen en super gebruiker worden met

`sudo su -`

## <a name="attach-the-disk"></a>De schijf koppelen
Koppel een schijf aan de **herstel** -VM die u hebt gemaakt op basis van de moment opname die u eerder hebt gemaakt.

Azure Portal-> de **herstel** -VM selecteren-> **schijven** 

![Schijf maken](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Vul de velden in. Wijs een naam toe aan de nieuwe schijf, selecteer dezelfde resource groep als de moment opname, de betrokken VM en de virtuele machine voor herstel.

Het **bron type** is **moment opname** .
De **bron momentopname** is de naam van de **moment opname** die u eerder hebt gemaakt.

![schijf 2 maken](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Maak een koppel punt voor de gekoppelde schijf.

`mkdir /rescue`

Voer de opdracht **fdisk-l** uit om te controleren of de momentopname schijf is gekoppeld en geef alle apparaten en partities weer die beschikbaar zijn

`fdisk -l`

In de meeste gevallen wordt de gekoppelde schijf met moment opnamen gezien als **/dev/SDC** die twee partities weer geven **/dev/sdc1** en **/dev/SDC2**

![Uitvoert](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

De * *\** _ duidt op een opstart partitie, beide partities moeten worden gekoppeld.

Voer de opdracht _ *lsblk** uit om de LVMs van de betrokken VM te bekijken

`lsblk`

![Scherm opname van de uitvoer van de lsblk-opdracht.](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Controleer of de LVMs van de betrokken VM worden weer gegeven.
Als dat niet het geval is, gebruikt u de onderstaande opdrachten om deze in te scha kelen en voert u **lsblk** opnieuw uit
Zorg ervoor dat de LVMs van de gekoppelde schijf zichtbaar is voordat u doorgaat.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Zoek het pad om het logische volume met de/(root)-partitie te koppelen. Het bevat de configuratie bestanden zoals/etc/default/grub

In dit voor beeld is de uitvoer van de vorige **lsblk**  **-opdracht rootvg-rootlv** de juiste **hoofdmap** voor het koppelen en kan worden gebruikt in de volgende opdracht.

De uitvoer van de volgende opdracht geeft het pad weer naar de koppeling voor de **hoofd** -LV

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Ga door met het koppelen van dit apparaat aan de Directory/Rescue

`mount /dev/rootvg/rootlv /rescue`

Koppel de partitie waarop de **opstart vlag** is ingesteld op/Rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Controleer of de bestands systemen van de gekoppelde schijf nu correct zijn gekoppeld met behulp van de **lsblk** -opdracht

![Lsblk uitvoeren](./media/chroot-logical-volume-manager/lsblk-output-1.png)

of de **VG-th-** opdracht

![DF](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Toegang verkrijgen tot chroot

Krijg toegang tot **chroot** , waarmee u verschillende oplossingen kunt uitvoeren. er zijn kleine variaties voor elke Linux-distributie.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Als er een fout optreedt zoals:

**chroot: kan de opdracht '/bin/bash ' niet uitvoeren: dit bestand of deze map bestaat niet**

poging om het logische volume van de **usr** te koppelen

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Wanneer u opdrachten uitvoert in een **chroot** -omgeving, moet u er rekening mee houden dat ze worden uitgevoerd op de gekoppelde besturingssysteem schijf en niet op de lokale **hulpverlenings** -VM. 

Opdrachten kunnen worden gebruikt om software te installeren, te verwijderen en bij te werken. Problemen met Vm's oplossen om fouten op te lossen.


Voer de opdracht lsblk uit en de/Rescue is nu/en/Rescue/boot is/boot ![ scherm afbeelding toont een console venster met de opdracht l s BLK en de uitvoer structuur ervan.](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Oplossingen uitvoeren

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Voor beeld 1: de virtuele machine configureren om op te starten vanuit een andere kernel

Een veelvoorkomend scenario is om te voor komen dat een virtuele machine wordt opgestart vanaf een eerdere kernel als de huidige geïnstalleerde kernel beschadigd is geraakt of een upgrade niet correct is voltooid.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*begint*

De **grep** -opdracht geeft een lijst van de kernels waarvan **grub. cfg** op de hoogte is.
![Scherm afbeelding toont een console venster waarin het resultaat van een grep-Zoek opdracht voor kernels wordt weer gegeven.](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv-lijst** geeft weer welke kernel wordt geladen bij de volgende standaard opstart- ![ kernel](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2: set-default** wordt gebruikt om te wijzigen in een andere kernel-grub2 die is ![ ingesteld](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv-** lijst geeft weer welke kernel wordt geladen bij de volgende keer dat ![ de computer wordt opgestart](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** maakt grub. cfg opnieuw met behulp van de vereiste versies ![ grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Voor beeld 2-pakketten bijwerken

Een mislukte kernel-upgrade kan de virtuele machine niet-opstart bare weer geven.
Alle logische volumes koppelen zodat pakketten kunnen worden verwijderd of opnieuw worden geïnstalleerd

Voer de opdracht **LVS** uit om te controleren welke **LVS** beschikbaar zijn voor het koppelen, elke virtuele machine, die is gemigreerd of afkomstig is van een andere Cloud provider, afhankelijk is van de configuratie.

De **chroot** -omgeving sluiten de vereiste **LV** koppelen

![Scherm afbeelding toont een console venster met de opdracht l v s en koppelt vervolgens een L V.](./media/chroot-logical-volume-manager/advanced.png)

Nu opnieuw toegang tot de **chroot** -omgeving door uit te voeren

`chroot /rescue`

Alle LVs moeten worden weer gegeven als gekoppelde partities

![Scherm opname van de LVs weer gegeven als gekoppelde partities.](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

De geïnstalleerde **kernel** doorzoeken

![Scherm afbeelding die laat zien hoe u een query op de geïnstalleerde kernel uitvoert.](./media/chroot-logical-volume-manager/rpm-kernel.png)

Verwijder, indien nodig, de **kernel** 
 ![ geavanceerde kernel](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Voor beeld 3: seriële console inschakelen
Als toegang tot de seriële Azure-console niet mogelijk is, controleert u de GRUB-configuratie parameters voor uw virtuele Linux-machine en corrigeert u deze. Gedetailleerde informatie vindt u [in dit document](./serial-console-grub-proactive-configuration.md)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Voor beeld 4: kernel laden met problematisch LVM swap-volume

Een virtuele machine kan niet volledig worden opgestart en de **Dracut** -prompt wordt verbroken.
Meer informatie over de fout kunt u vinden in de Azure-seriële console of naar Azure Portal-> diagnostische gegevens over opstarten > serieel logboek


Er is een fout met de volgende strekking aanwezig:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Grub. cfg wordt in dit voor beeld geconfigureerd om een LV te laden met de naam **rd. lvm. lv = VG/SwapVol** en de virtuele machine kan dit niet vinden. Deze regel toont hoe de kernel wordt geladen, die verwijst naar de LV-SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Verwijder de foutieve LV uit de/etc/default/grub-configuratie en bouw grub2. cfg opnieuw in


## <a name="exit-chroot-and-swap-the-os-disk"></a>Chroot afsluiten en de besturingssysteem schijf wisselen

Nadat het probleem is opgelost, gaat u verder met het ontkoppelen van de schijf en ontkoppelen van de herstel-VM, zodat deze kan worden omgewisseld met de betreffende VM-besturingssysteem schijf.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Ontkoppel de schijf van de virtuele machine voor herstel en voer een schijf wisseling uit.

Selecteer de virtuele machine in de portal **schijven** en selecteer ontkoppelen schijf **ontkoppelen** 
 ![](./media/chroot-logical-volume-manager/detach-disk.png) 

Sla de wijzigingen op en ![ Sla de koppeling op](./media/chroot-logical-volume-manager/save-detach.png) 

De schijf wordt nu beschikbaar zodat deze kan worden omgewisseld met de oorspronkelijke besturingssysteem schijf van de betrokken VM.

Navigeer in het Azure Portal naar de mislukte VM en selecteer **schijven**  ->  **wisselen** schijf 
 ![ swap schijf](./media/chroot-logical-volume-manager/swap-disk.png) 

Vul de velden **op de schijf kiezen** is de momentopname schijf die u in de vorige stap hebt losgekoppeld. De VM-naam van de betrokken VM is ook vereist en vervolgens **OK** selecteren

![Nieuwe besturingssysteem schijf](./media/chroot-logical-volume-manager/new-osdisk.png) 

Als de virtuele machine wordt uitgevoerd, wordt de schijf swap afgesloten. Start de VM opnieuw op wanneer de schijf wisseling is voltooid.


## <a name="next-steps"></a>Volgende stappen
Meer informatie over

 [Azure-seriële console]( ./serial-console-linux.md)

[Modus voor één gebruiker](./serial-console-grub-single-user-mode.md)