---
title: Een Oracle Linux VHD maken en uploaden
description: Meer informatie over het maken en uploaden van een virtuele harde schijf (VHD) van Azure die een Oracle Linux besturings systeem bevat.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 4e9f87ec4a0df28b14bca8f5a44dfe9388e8ff03
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500526"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Een Oracle Linux virtuele machine voorbereiden voor Azure

In dit artikel wordt ervan uitgegaan dat u al een Oracle Linux besturings systeem hebt geïnstalleerd op een virtuele harde schijf. Er zijn meerdere hulpprogram ma's voor het maken van VHD-bestanden, zoals Hyper-V. Zie [de Hyper-V-functie installeren en een virtuele machine configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))voor instructies.

## <a name="oracle-linux-installation-notes"></a>Installatie notities van Oracle Linux
* Zie ook [algemene Linux-installatie notities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* Ondersteuning voor Hyper-V en Azure Oracle Linux met de onbreekbare Enter prise kernel (UEK) of met de Red Hat compatibele kernel.
* Oracle-UEK2 wordt niet ondersteund op Hyper-V en Azure omdat het niet de vereiste Stuur Programma's bevat.
* De VHDX-indeling wordt niet ondersteund in azure, alleen **vaste VHD**.  U kunt de schijf converteren naar VHD-indeling met behulp van Hyper-V-beheer of de cmdlet Convert-VHD.
* Bij de installatie van het Linux-systeem wordt aanbevolen om standaard partities te gebruiken in plaats van LVM (vaak de standaard instelling voor veel installaties). Hiermee wordt voor komen dat LVM naam strijdig is met gekloonde Vm's, met name als een besturingssysteem schijf ooit moet worden gekoppeld aan een andere virtuele machine voor het oplossen van problemen. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan worden gebruikt op gegevens schijven als dit de voor keur heeft.
* Linux-kernel-versies ouder dan 2.6.37 bieden geen ondersteuning voor NUMA op Hyper-V met grotere VM-grootten. Dit probleem heeft voornamelijk betrekking op oudere distributies met behulp van de upstream Red Hat 2.6.32 kernel en is opgelost in Oracle Linux 6,6 en hoger
* Configureer geen swap partitie op de besturingssysteem schijf. De Linux-agent kan worden geconfigureerd voor het maken van een wissel bestand op de tijdelijke bron schijf.  Meer informatie hierover vindt u in de volgende stappen.
* Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1MB is vóór de conversie. Zie [installatie notities voor Linux](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.
* Zorg ervoor dat de `Addons` opslag plaats is ingeschakeld. Bewerk het bestand `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) of `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7) en wijzig de regel `enabled=0` naar `enabled=1` onder **[ol6_addons]** of **[ol7_addons]** in dit bestand.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 en hoger
U moet specifieke configuratie stappen in het besturings systeem uitvoeren om de virtuele machine in azure uit te voeren.

1. Selecteer de virtuele machine in het middelste deel venster van Hyper-V-beheer.
2. Klik op **verbinding maken** om het venster voor de virtuele machine te openen.
3. Verwijder NetworkManager door de volgende opdracht uit te voeren:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Opmerking:** Als het pakket nog niet is geïnstalleerd, mislukt deze opdracht met een fout bericht. Dit is normaal.
4. Maak in de map **network** een bestand `/etc/sysconfig/` met de naam netwerk dat de volgende tekst bevat:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Maak een bestand met de naam **ifcfg-eth0** in de `/etc/sysconfig/network-scripts/` map met de volgende tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Wijzig de udev-regels om te voor komen dat er statische regels voor de Ethernet-interface (s) worden gegenereerd. Deze regels kunnen problemen veroorzaken bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Zorg ervoor dat de netwerk service wordt gestart op het moment dat de computer wordt opgestart door de volgende opdracht uit te voeren:

    ```console
    # chkconfig network on
    ```

8. Installeer python-pyasn1 door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u dit wilt doen, opent u '/boot/grub/menu.lst ' in een tekst editor en zorgt u ervoor dat de kernel de volgende para meters bevat:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Dit zorgt ervoor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning voor Azure kan helpen bij het oplossen van problemen.
   
   Naast het bovenstaande wordt het aanbevolen om de volgende para meters te *verwijderen* :

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort.
   
   De `crashkernel` optie kan desgewenst worden geconfigureerd, maar houd er rekening mee dat met deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine 128 MB of meer wordt verminderd, wat kan leiden tot problemen met de kleinere VM-grootten.
10. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is doorgaans de standaard instelling.
11. Installeer de Azure Linux-agent door de volgende opdracht uit te voeren. De meest recente versie is 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Houd er rekening mee dat als u het WALinuxAgent-pakket installeert, de NetworkManager-en NetworkManager-gnome-pakketten worden verwijderd als deze nog niet zijn verwijderd, zoals beschreven in stap 2.
12. Maak geen wissel ruimte op de besturingssysteem schijf.
    
    De Azure Linux-agent kan tijdens het inrichten van Azure automatisch wissel ruimte configureren met de lokale bron schijf die aan de VM is gekoppeld. Houd er rekening mee dat de lokale bron schijf een *tijdelijke* schijf is en kan worden leeg gemaakt wanneer de inrichting van de virtuele machine wordt opheffen. Nadat u de Azure Linux-agent hebt geïnstalleerd (Zie de vorige stap), wijzigt u de volgende para meters in/etc/waagent.conf op de juiste manier:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Klik op **actie-> afgesloten** in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 en hoger
**Wijzigingen in Oracle Linux 7**

Het voorbereiden van een virtuele machine voor Oracle Linux 7 voor Azure lijkt veel op Oracle Linux 6, maar er zijn echter enkele belang rijke verschillen:

* Azure ondersteunt Oracle Linux met een onherstelbare Enter prise kernel (UEK) of met de Red Hat compatibele kernel. Oracle Linux met UEK wordt aanbevolen.
* Het NetworkManager-pakket is niet meer strijdig met de Azure Linux-agent. Dit pakket wordt standaard geïnstalleerd en wordt aangeraden dat het niet wordt verwijderd.
* GRUB2 wordt nu gebruikt als de standaard-bootloader, dus de procedure voor het bewerken van kernel-para meters is gewijzigd (zie hieronder).
* XFS is nu het standaard bestandssysteem. Het ext4-bestands systeem kan nog steeds worden gebruikt, indien gewenst.

**Configuratiestappen**

1. Selecteer de virtuele machine in Hyper-V-beheer.
2. Klik op **verbinding maken** om een console venster voor de virtuele machine te openen.
3. Maak in de map **network** een bestand `/etc/sysconfig/` met de naam netwerk dat de volgende tekst bevat:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Maak een bestand met de naam **ifcfg-eth0** in de `/etc/sysconfig/network-scripts/` map met de volgende tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Wijzig de udev-regels om te voor komen dat er statische regels voor de Ethernet-interface (s) worden gegenereerd. Deze regels kunnen problemen veroorzaken bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Zorg ervoor dat de netwerk service wordt gestart op het moment dat de computer wordt opgestart door de volgende opdracht uit te voeren:

    ```console
    # sudo chkconfig network on
    ```

7. Installeer het python-pyasn1-pakket door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Voer de volgende opdracht uit om de huidige yum-meta gegevens te wissen en updates te installeren:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u dit wilt doen, opent u '/etc/default/grub ' in een tekst editor en bewerkt u de `GRUB_CMDLINE_LINUX` para meter, bijvoorbeeld:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Dit zorgt ervoor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning voor Azure kan helpen bij het oplossen van problemen. Het schakelt ook de naamgevings conventies voor Nic's in Oracle Linux 7 uit met de onbreekbare bedrijfs-kernel. Naast het bovenstaande wordt het aanbevolen om de volgende para meters te *verwijderen* :

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort.
   
   De `crashkernel` optie kan desgewenst worden geconfigureerd, maar houd er rekening mee dat met deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine 128 MB of meer wordt verminderd, wat kan leiden tot problemen met de kleinere VM-grootten.
10. Zodra u klaar bent met het bewerken van '/etc/default/grub ', voert u de volgende opdracht uit om de grub-configuratie opnieuw samen te stellen:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is doorgaans de standaard instelling.
12. Installeer de Azure Linux-agent door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent
    ```

13. Maak geen wissel ruimte op de besturingssysteem schijf.
    
    De Azure Linux-agent kan tijdens het inrichten van Azure automatisch wissel ruimte configureren met de lokale bron schijf die aan de VM is gekoppeld. Houd er rekening mee dat de lokale bron schijf een *tijdelijke* schijf is en kan worden leeg gemaakt wanneer de inrichting van de virtuele machine wordt opheffen. Nadat u de Azure Linux-agent hebt geïnstalleerd (Zie de vorige stap), wijzigt u de volgende para meters in/etc/waagent.conf op de juiste manier:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

14. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:
    
    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

15. Klik op **actie-> afgesloten** in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.

## <a name="next-steps"></a>Volgende stappen
U kunt nu uw Oracle Linux. VHD gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het VHD-bestand naar Azure uploadt.