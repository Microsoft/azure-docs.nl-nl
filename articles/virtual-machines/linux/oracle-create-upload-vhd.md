---
title: Een Oracle Linux VHD maken en uploaden
description: Meer informatie over het maken en uploaden van een virtuele harde schijf (VHD) van Azure die een Oracle Linux besturings systeem bevat.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 9984589b19f15ab00e895bca75c295a92a68d0fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557791"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Een Oracle Linux virtuele machine voorbereiden voor Azure

In dit artikel wordt ervan uitgegaan dat u al een Oracle Linux besturings systeem hebt geïnstalleerd op een virtuele harde schijf. Er zijn meerdere hulpprogram ma's voor het maken van VHD-bestanden, zoals Hyper-V. Zie [de Hyper-V-functie installeren en een virtuele machine configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))voor instructies.

## <a name="oracle-linux-installation-notes"></a>Installatie notities van Oracle Linux
* Zie ook [algemene Linux-installatie notities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* Ondersteuning voor Hyper-V en Azure Oracle Linux met de onbreekbare Enter prise kernel (UEK) of met de Red Hat compatibele kernel.
* Oracle-UEK2 wordt niet ondersteund op Hyper-V en Azure omdat het niet de vereiste Stuur Programma's bevat.
* De VHDX-indeling wordt niet ondersteund in azure, alleen **vaste VHD**.  U kunt de schijf converteren naar VHD-indeling met behulp van Hyper-V-beheer of de cmdlet Convert-VHD.
* **Kernel-ondersteuning voor het koppelen van UDF-bestands systemen is vereist.** Bij de eerste keer opstarten in azure wordt de inrichtings configuratie door gegeven aan de Linux-VM via met UDF geformatteerde media die aan de gast zijn gekoppeld. De Azure Linux-agent moet het UDF-bestands systeem kunnen koppelen om de configuratie te lezen en de virtuele machine in te richten.
* Bij de installatie van het Linux-systeem wordt aanbevolen om standaard partities te gebruiken in plaats van LVM (vaak de standaard instelling voor veel installaties). Hiermee wordt voor komen dat LVM naam strijdig is met gekloonde Vm's, met name als een besturingssysteem schijf ooit moet worden gekoppeld aan een andere virtuele machine voor het oplossen van problemen. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) of [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) kan worden gebruikt op gegevens schijven als dit de voor keur heeft.
* Linux-kernel-versies ouder dan 2.6.37 bieden geen ondersteuning voor NUMA op Hyper-V met grotere VM-grootten. Dit probleem heeft voornamelijk betrekking op oudere distributies met behulp van de upstream Red Hat 2.6.32 kernel en is opgelost in Oracle Linux 6,6 en hoger
* Configureer geen swap partitie op de besturingssysteem schijf. Meer informatie hierover vindt u in de volgende stappen.
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
4. Maak in de map  een bestand `/etc/sysconfig/` met de naam netwerk dat de volgende tekst bevat:

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
3. Maak in de map  een bestand `/etc/sysconfig/` met de naam netwerk dat de volgende tekst bevat:

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
12. De Azure Linux-agent en-afhankelijkheden installeren:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Cloud-init installeren voor het afhandelen van de inrichting

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. Wisselings configuratie maakt geen wissel ruimte op de schijf met het besturings systeem.

    Voorheen werd de Azure Linux-agent gebruikt om de wissel ruimte automatisch te configureren met behulp van de lokale bron schijf die is gekoppeld aan de virtuele machine nadat de virtuele machine is ingericht op Azure. Dit wordt nu echter verwerkt door Cloud-init, u **moet** de Linux-agent niet gebruiken om de bron schijf te Format teren Maak het wissel bestand, wijzig de volgende para meters op de `/etc/waagent.conf` juiste manier:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Als u koppelen, Format teren en wisselen wilt maken, kunt u het volgende doen:
    * Deze in als Cloud-init-configuratie door geven telkens wanneer u een virtuele machine maakt
    * Gebruik een Cloud-init-instructie geïntegreerde in de installatie kopie die dit doet wanneer de virtuele machine wordt gemaakt:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```


15. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

16. Klik op **actie-> afgesloten** in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.

## <a name="next-steps"></a>Volgende stappen
U kunt nu uw Oracle Linux. VHD gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het VHD-bestand naar Azure uploadt.