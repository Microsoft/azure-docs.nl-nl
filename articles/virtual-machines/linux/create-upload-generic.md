---
title: Een virtuele Linux-VHD maken en uploaden
description: Meer informatie over het maken en uploaden van een virtuele harde schijf (VHD) van Azure die een Linux-besturings systeem bevat.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: ef4175d24cfd02bb5cb6470b6334fea190b5bec2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500594"
---
# <a name="information-for-non-endorsed-distributions"></a>Informatie over niet-goedgekeurde distributies

De SLA van het Azure-platform is alleen van toepassing op virtuele machines waarop het Linux-besturings systeem wordt uitgevoerd wanneer een van de [goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) wordt gebruikt. Voor deze geplaatste distributies zijn vooraf geconfigureerde Linux-installatie kopieën opgenomen in de Azure Marketplace.

* [Linux op door Azure goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ondersteuning voor Linux-installatie kopieën in Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alle distributies die worden uitgevoerd op Azure, hebben een aantal vereisten. Dit artikel kan niet uitgebreid worden, aangezien elke distributie anders is. Zelfs als u voldoet aan alle onderstaande criteria, moet u mogelijk uw Linux-systeem ingrijpend aanpassen zodat het correct kan worden uitgevoerd.

U wordt aangeraden te beginnen met een van de [Linux in azure goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). In de volgende artikelen wordt uitgelegd hoe u de verschillende getekende Linux-distributies die worden ondersteund op Azure voorbereidt:

- [CentOS-distributies](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES en OpenSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

Dit artikel richt zich op algemene richt lijnen voor het uitvoeren van uw Linux-distributie op Azure.

## <a name="general-linux-installation-notes"></a>Algemene Linux-installatie notities
* De indeling van de virtuele harde schijf (VHDX) van Hyper-V wordt niet ondersteund in azure, alleen *vaste VHD*.  U kunt de schijf converteren naar VHD-indeling met behulp van Hyper-V-beheer of de cmdlet [Convert-VHD](/powershell/module/hyper-v/convert-vhd) . Als u VirtualBox gebruikt, selecteert u **vaste grootte** in plaats van de standaard waarde (dynamisch toegewezen) bij het maken van de schijf.
* Azure biedt ondersteuning voor gen1 (BIOS Boot) & Gen2 (UEFI boot) virtuele machines.
* De maximale grootte die is toegestaan voor de VHD is 1.023 GB.
* Bij de installatie van het Linux-systeem raden we u aan om standaard partities te gebruiken in plaats van Logical Volume Manager (LVM). Dit is de standaard instelling voor veel installaties. Het gebruik van standaard partities voor komt dat LVM naam strijdig is met gekloonde Vm's, met name als een besturingssysteem schijf ooit is gekoppeld aan een andere identieke virtuele machine voor het oplossen van problemen. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan worden gebruikt op gegevens schijven.
* Kernel-ondersteuning voor het koppelen van UDF-bestands systemen is nood zakelijk. Bij de eerste keer opstarten in azure wordt de inrichtings configuratie door gegeven aan de Linux-VM met behulp van UDF-geformatteerde media die aan de gast zijn gekoppeld. De Azure Linux-agent moet het UDF-bestands systeem koppelen om de configuratie te lezen en de virtuele machine in te richten.
* Linux-kernel-versies ouder dan 2.6.37 bieden geen ondersteuning voor NUMA op Hyper-V met grotere VM-grootten. Dit probleem heeft voornamelijk betrekking op oudere distributies met behulp van de upstream Red Hat 2.6.32 kernel en is opgelost in Red Hat Enterprise Linux (RHEL) 6,6 (kernel-2.6.32-504). Systemen met aangepaste kernels die ouder zijn dan 2.6.37 of op RHEL gebaseerde kernels die ouder zijn dan 2.6.32-504, moeten de opstart parameter instellen `numa=off` op de kernel-opdracht regel in grub. conf. Zie [Red Hat KB 436883](https://access.redhat.com/solutions/436883)voor meer informatie.
* Configureer geen swap partitie op de besturingssysteem schijf. De Linux-agent kan worden geconfigureerd voor het maken van een wissel bestand op de tijdelijke bron schijf, zoals wordt beschreven in de volgende stappen.
* Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1 MB vóór de conversie is, zoals wordt beschreven in de volgende stappen.

### <a name="installing-kernel-modules-without-hyper-v"></a>Kernel-modules installeren zonder Hyper-V
Azure wordt uitgevoerd op de Hyper-V-Hyper Visor, daarom vereist Linux bepaalde kernel-modules om uit te voeren in Azure. Als u een virtuele machine hebt die buiten Hyper-V is gemaakt, bevatten de Linux-installatie Programma's mogelijk niet de drivers voor Hyper-V in de oorspronkelijke ramdisk (initrd of initramfs), tenzij de virtuele machine detecteert dat deze wordt uitgevoerd op een Hyper-V-omgeving. Wanneer u een ander virtualisatie systeem (zoals VirtualBox, KVM, enzovoort) gebruikt om de Linux-installatie kopie voor te bereiden, moet u mogelijk de initrd opnieuw bouwen zodat ten minste de hv_vmbus en hv_storvsc kernel-modules beschikbaar zijn op de eerste ramdisk.  Dit bekende probleem doet zich voor op systemen die zijn gebaseerd op de upstream-implementatie van Red Hat en mogelijk andere.

Het mechanisme voor het opnieuw samen stellen van de initrd-of initramfs-installatie kopie kan variëren, afhankelijk van de verdeling. Raadpleeg de documentatie of ondersteuning van uw distributie voor de juiste procedure.  Hier volgt een voor beeld voor het opnieuw samen stellen van de initrd met het `mkinitrd` hulp programma:

1. Maak een back-up van de bestaande initrd-installatie kopie:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Bouw de initrd opnieuw met de hv_vmbus en hv_storvsc kernel-modules:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Grootte van Vhd's wijzigen
VHD-installatie kopieën in azure moeten een virtuele grootte hebben die is afgestemd op 1 MB.  Normaal gesp roken worden Vhd's die zijn gemaakt met Hyper-V correct uitgelijnd.  Als de VHD niet correct is uitgelijnd, wordt mogelijk een fout bericht van de volgende strekking weer gegeven wanneer u een installatie kopie probeert te maken op basis van uw VHD.

* De VHD http:// \<mystorageaccount> . blob.core.Windows.net/VHDs/MyLinuxVM.VHD heeft een niet-ondersteunde virtuele grootte van 21475270656 bytes. De grootte moet een geheel getal zijn (in MB).

In dit geval kunt u de grootte van de virtuele machine wijzigen met de Hyper-V-beheer console of met de Power shell [-cmdlet resize-VHD](/powershell/module/hyper-v/resize-vhd?view=win10-ps) .  Als u niet in een Windows-omgeving wordt uitgevoerd, raden we u `qemu-img` aan om (indien nodig) te converteren en de grootte van de VHD te wijzigen.

> [!NOTE]
> Er is een [bekende fout in qemu-img-](https://bugs.launchpad.net/qemu/+bug/1490611) versies >= 2.2.1 dat resulteert in een VHD met een onjuiste indeling. Het probleem is opgelost in QEMU 2,6. We raden u `qemu-img` aan om 2.2.0 of lager of 2,6 of hoger te gebruiken.
> 

1. Het formaat van de VHD rechtstreeks wijzigen met behulp van hulpprogram ma's zoals `qemu-img` of `vbox-manage` kan leiden tot een niet-opstartbaar VHD.  U wordt aangeraden eerst de VHD te converteren naar een onbewerkte schijf installatie kopie.  Als de VM-installatie kopie is gemaakt als een onbewerkte schijf installatie kopie (de standaard instelling voor sommige Hyper visors zoals KVM), kunt u deze stap overs Laan.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Bereken de vereiste grootte van de schijf installatie kopie zodat de virtuele grootte wordt uitgelijnd op 1 MB.  Het volgende bash-shell script gebruikt `qemu-img info` om de virtuele grootte van de schijf installatie kopie te bepalen, waarna de grootte wordt berekend op de volgende 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Wijzig de grootte van de onbewerkte schijf met de `$rounded_size` bovenstaande set.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Converteer nu de onbewerkte schijf terug naar een VHD met een vaste grootte.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Of, met qemu versie 2.6 +, ook de `force_size` optie.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Vereisten voor Linux-kernel

De LIS-Stuur programma's (Linux Integration Services) voor Hyper-V en Azure zijn rechtstreeks aan de upstream Linux-kernel bijgedragen. Veel distributies die een recente versie van de Linux-kernel bevatten (zoals 3. x), hebben deze Stuur Programma's al beschikbaar of bieden Backported-versies van deze Stuur Programma's aan hun kernels.  Deze Stuur Programma's worden voortdurend bijgewerkt in de upstream-kernel met nieuwe oplossingen en functies. als dat mogelijk is, wordt u aangeraden een [officiële distributie](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) uit te voeren die deze oplossingen en updates bevat.

Als u een variant van Red Hat Enterprise Linux versie 6,0 tot 6,3 uitvoert, moet u de [meest recente Lis-Stuur Programma's voor Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)installeren. Vanaf RHEL 6.4 + (en derivaten) zijn de LIS-Stuur Programma's al opgenomen in de kernel en zijn er geen aanvullende installatie pakketten nodig.

Als een aangepaste kernel vereist is, raden we u aan een recente kernel-versie (zoals 3.8 +) te vinden. Voor distributies of leveranciers die hun eigen kernel onderhouden, moet u regel matig de LIS-Stuur Programma's van de upstream-kernel naar uw aangepaste kernel backport.  Zelfs als u al een relatief recente kernel-versie uitvoert, raden wij u ten zeerste aan om eventuele upstream-oplossingen in de LIS-Stuur Programma's bij te houden en ze zo nodig te backport. De locaties van de bron bestanden van het LIS-stuur programma zijn opgegeven in het bestand [Maintainers](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) in de bron structuur van de Linux-kernel:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
De volgende patches moeten zijn opgenomen in de kernel. Deze lijst kan niet worden voltooid voor alle distributies.

* [ata_piix: schijven standaard uitstellen naar de Hyper-V-Stuur Programma's](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: account voor in-transit pakketten in het pad opnieuw instellen](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: Vermijd het gebruik van WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: WRITE zelfde uitschakelen voor RAID-en Virtual Host-adapter Stuur Programma's](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL-verwijzings oplossing voor pointer](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: storingen in ring buffers kunnen resulteren in I/O-blok kering](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: beveiligen tegen dubbele uitvoering van __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>De Azure Linux-agent
De [Azure Linux-agent](../extensions/agent-linux.md) `waagent` richt zich op een virtuele Linux-machine in Azure. U kunt de meest recente versie, bestands problemen of pull-aanvragen indienen via de [Linux-agent github opslag plaats](https://github.com/Azure/WALinuxAgent).

* De Linux-agent wordt uitgebracht onder de Apache 2,0-licentie. Veel distributies leveren al RPM-of deb-pakketten voor de agent, en deze pakketten kunnen eenvoudig worden geïnstalleerd en bijgewerkt.
* De Azure Linux-agent vereist python v 2.6 +.
* De agent vereist ook de module python-pyasn1. De meeste distributies bieden deze module als een afzonderlijk pakket dat moet worden geïnstalleerd.
* In sommige gevallen is de Azure Linux-agent mogelijk niet compatibel met NetworkManager. Veel van de RPM/deb-pakketten die worden verschaft door distributies, configureren NetworkManager als een conflict met het waagent-pakket. In dergelijke gevallen wordt NetworkManager verwijderd wanneer u het Linux-agent pakket installeert.
* De Azure Linux-agent moet op of hoger zijn dan de [Mini maal ondersteunde versie](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Algemene Linux-systeem vereisten

1. Wijzig de kernel-opstart regel in GRUB of GRUB2 zodat de volgende para meters worden toegevoegd, zodat alle console berichten naar de eerste seriële poort worden verzonden. Deze berichten kunnen ondersteuning voor Azure bieden bij het opsporen van problemen.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    We raden u ook aan de volgende para meters te *verwijderen* , indien aanwezig.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafisch en stil opstarten zijn niet nuttig in een cloud omgeving, waar we alle logboeken willen verzenden naar de seriële poort. De `crashkernel` optie kan indien nodig worden geconfigureerd, maar houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met ten minste 128 MB reduceert. Dit kan problemen opleveren voor kleinere VM-grootten.

1. Installeer de Azure Linux-agent.
  
    De Azure Linux-agent is vereist voor het inrichten van een Linux-installatie kopie in Azure.  Veel distributies bieden de agent als een RPM-of deb-pakket (het pakket heet doorgaans WALinuxAgent of WALinuxAgent).  U kunt de agent ook hand matig installeren door de stappen in de [Linux-agent gids](../extensions/agent-linux.md)te volgen.

1. Zorg ervoor dat de SSH-server is geïnstalleerd en is geconfigureerd om te starten tijdens het opstarten.  Deze configuratie is doorgaans de standaard instelling.

1. Maak geen wissel ruimte op de besturingssysteem schijf.
  
    De Azure Linux-agent kan tijdens het inrichten van Azure automatisch wissel ruimte configureren met de lokale bron schijf die aan de VM is gekoppeld. De lokale bron schijf is een *tijdelijke* schijf en kan worden leeg gemaakt wanneer de inrichting van de virtuele machine ongedaan is. Nadat u de Azure Linux-agent (stap 2 hierboven) hebt geïnstalleerd, wijzigt u de volgende para meters in/etc/waagent.conf naar behoefte.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Op VirtualBox ziet u mogelijk de volgende fout na het uitvoeren van de `waagent -force -deprovision` tekst `[Errno 5] Input/output error` . Dit fout bericht is niet kritiek en kan worden genegeerd.

* Sluit de virtuele machine af en upload de VHD naar Azure.