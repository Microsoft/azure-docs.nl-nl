---
title: Een Debian Linux-VHD voorbereiden
description: Meer informatie over het maken van Debian-VHD-installatie kopieën voor implementaties van VM'S in Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d822aa30926c473d08bc5d785211c82d038ef652
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203181"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Een Debian-VHD voorbereiden voor Azure
## <a name="prerequisites"></a>Vereisten
In deze sectie wordt ervan uitgegaan dat u al een Debian Linux-besturings systeem hebt geïnstalleerd vanuit een ISO-bestand dat is gedownload van de [Debian-website](https://www.debian.org/distrib/) naar een virtuele harde schijf. Er zijn meerdere hulpprogram ma's voor het maken van VHD-bestanden. Hyper-V is maar een voor beeld. Zie [de hyper-v-functie installeren en een virtuele machine configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))voor instructies voor het gebruik van Hyper-v.

## <a name="installation-notes"></a>Installatie notities
* Zie ook [algemene Linux-installatie notities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* De nieuwere VHDX-indeling wordt niet ondersteund in Azure. U kunt de schijf converteren naar VHD-indeling met behulp van Hyper-V-beheer of de cmdlet **Convert-VHD** .
* Bij de installatie van het Linux-systeem wordt aanbevolen om standaard partities te gebruiken in plaats van LVM (vaak de standaard instelling voor veel installaties). Hiermee wordt voor komen dat LVM naam strijdig is met gekloonde Vm's, met name als een besturingssysteem schijf ooit moet worden gekoppeld aan een andere virtuele machine voor het oplossen van problemen. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) of [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) kan worden gebruikt op gegevens schijven als dit de voor keur heeft.
* Configureer geen swap partitie op de besturingssysteem schijf. De Azure Linux-agent kan worden geconfigureerd voor het maken van een wissel bestand op de tijdelijke bron schijf. Meer informatie vindt u in de volgende stappen.
* Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1MB is vóór de conversie. Zie [installatie notities voor Linux](create-upload-generic.md#general-linux-installation-notes)voor meer informatie.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage gebruiken om Debian Vhd's te maken
Er zijn hulpprogram ma's beschikbaar voor het genereren van Debian-Vhd's voor Azure, zoals de [Azure-beheer](https://github.com/credativ/azure-manage) scripts van [Credativ](https://www.credativ.com/). Dit is de aanbevolen benadering en een volledig nieuwe installatie kopie maken. Als u bijvoorbeeld een Debian 8-VHD wilt maken, voert u de volgende opdrachten uit om het `azure-manage` hulp programma (en de afhankelijkheden) te downloaden en het script uit te voeren `azure_build_image` :

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Een Debian-VHD hand matig voorbereiden
1. Selecteer de virtuele machine in Hyper-V-beheer.
2. Klik op **verbinding maken** om een console venster voor de virtuele machine te openen.
3. Als u het besturings systeem hebt geïnstalleerd met behulp van een ISO, kunt u een wille keurige regel met betrekking tot "" in een opmerking plaatsen `deb cdrom` `/etc/apt/source.list` .

4. Bewerk het `/etc/default/grub` bestand en wijzig de para meter **GRUB_CMDLINE_LINUX** als volgt, zodat u aanvullende kernel-para meters voor Azure kunt gebruiken.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Bouw de grub opnieuw op en voer de volgende handelingen uit:

    ```console
    # sudo update-grub
    ```

6. Voeg Azure-opslag plaatsen van Debian toe aan/etc/apt/sources.List voor een Debian 8, 9 of 10:

    **Debian 8. x "Jessie"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9. x "stretch"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```
    
    **Debian 10. x "Buster"**
    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian buster main
    deb-src http://debian-archive.trafficmanager.net/debian buster main
    deb http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb http://debian-archive.trafficmanager.net/debian buster-updates main
    deb-src http://debian-archive.trafficmanager.net/debian buster-updates main
    deb http://debian-archive.trafficmanager.net/debian buster-backports main
    deb-src http://debian-archive.trafficmanager.net/debian buster-backports main
    ```

7. De Azure Linux-agent installeren:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. Voor Debian 9 + is het raadzaam om de nieuwe Debian-Cloud kernel te gebruiken voor gebruik met virtuele machines in Azure. Als u deze nieuwe kernel wilt installeren, maakt u eerst een bestand met de naam/etc/apt/preferences.d/Linux.pref met de volgende inhoud:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Voer vervolgens ' sudo apt-down load linux-image-Cloud-amd64 ' uit om de nieuwe Debian Cloud kernel te installeren.

9. Maak de inrichting van de virtuele machine ongedaan en bereid deze voor op het inrichten van Azure en voer de volgende handelingen uit:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Klik op **actie** -> afgesloten in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.

## <a name="next-steps"></a>Volgende stappen
U kunt nu de virtuele harde schijf van Debian gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het VHD-bestand naar Azure uploadt.