---
title: Een Red Hat Enterprise Linux VHD maken en uploaden voor gebruik in azure
description: Meer informatie over het maken en uploaden van een virtuele harde schijf van Azure (VHD) met een Red Hat Linux-besturings systeem.
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 28f82b641216dcd77b650b066c72bef4c4470dd2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098959"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Een op Red Hat gebaseerde virtuele machine voor Azure voorbereiden

In dit artikel wordt beschreven hoe u een virtuele Red Hat Enterprise Linux-machine (RHEL) voorbereidt voor gebruik in Azure. De versies van RHEL die in dit artikel worden behandeld, zijn 6,7 + en 7.1 +. De Hyper visors voor voor bereidingen die in dit artikel worden behandeld, zijn Hyper-V, op kernel gebaseerde virtuele machine (KVM) en VMware. Voor meer informatie over de geschiktheids vereisten voor deelname aan het Cloud Access-programma van Red Hat raadpleegt u [de Cloud Access-website van Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) en [voert u RHEL uit op Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Zie [Azure Image Builder](../image-builder-overview.md)voor manieren om het bouwen van RHEL-installatie kopieën te automatiseren.

## <a name="hyper-v-manager"></a>Hyper-V-beheer

In deze sectie wordt beschreven hoe u een virtuele machine met [RHEL 6](#rhel-6-using-hyper-v-manager), [RHEL 7](#rhel-7-using-hyper-v-manager)of [RHEL 8](#rhel-8-using-hyper-v-manager) voorbereidt met behulp van Hyper-V-beheer.

### <a name="prerequisites"></a>Vereisten
In deze sectie wordt ervan uitgegaan dat u al een ISO-bestand hebt verkregen van de website Red Hat en de RHEL-installatie kopie hebt geïnstalleerd op een virtuele harde schijf (VHD). Zie [hyper-v-functie installeren en een virtuele machine configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))voor meer informatie over het gebruik van Hyper-v-beheer om een installatie kopie van een besturings systeem te installeren.

**RHEL-installatie notities**

* Azure biedt geen ondersteuning voor de VHDX-indeling. Azure ondersteunt alleen vaste VHD. U kunt Hyper-V-beheer gebruiken om de schijf te converteren naar VHD-indeling, of u kunt de cmdlet Convert-VHD gebruiken. Als u VirtualBox gebruikt, selecteert u **vaste grootte** in plaats van de standaard optie voor dynamisch toegewezen wanneer u de schijf maakt.
* Azure biedt ondersteuning voor gen1 (BIOS Boot) & Gen2 (UEFI boot) virtuele machines.
* De maximale grootte die is toegestaan voor de VHD is 1.023 GB.
* Logical Volume Manager (LVM) wordt ondersteund en kan worden gebruikt op de besturingssysteem schijf of gegevens schijven in virtuele machines van Azure. Over het algemeen is het echter raadzaam om standaard partities op de besturingssysteem schijf te gebruiken in plaats van LVM. Met deze procedure wordt voor komen dat LVM naam strijdig is met gekloonde virtuele machines, met name als u ooit een besturingssysteem schijf moet koppelen aan een andere identieke virtuele machine voor het oplossen van problemen. Zie ook  [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) en [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) -documentatie.
* **Kernel-ondersteuning voor het koppelen van UDF-bestands systemen (Universal Disk Format) is vereist**. Bij de eerste keer opstarten op Azure wordt de inrichtings configuratie door de UDF-indelings media die aan de gast is gekoppeld, door gegeven aan de virtuele Linux-machine. De Azure Linux-agent moet het UDF-bestands systeem kunnen koppelen om de configuratie te lezen en de virtuele machine zo in te richten, mislukt het inrichten.
* Configureer geen swap partitie op de schijf met het besturings systeem. Meer informatie hierover vindt u in de volgende stappen.

* Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1MB is vóór de conversie. Meer informatie vindt u in de volgende stappen. Zie ook [Linux-installatie notities](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.

### <a name="rhel-6-using-hyper-v-manager"></a>RHEL 6 met Hyper-V-beheer

1. Selecteer de virtuele machine in Hyper-V-beheer.

1. Klik op **verbinding maken** om een console venster voor de virtuele machine te openen.

1. In RHEL 6 kan NetworkManager conflicteren met de Azure Linux-agent. Verwijder dit pakket door de volgende opdracht uit te voeren:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Verplaats (of verwijder) de udev-regels om te voor komen dat statische regels voor de Ethernet-interface worden gegenereerd. Deze regels veroorzaken problemen bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Zorg ervoor dat de netwerk service wordt gestart op het moment van opstarten door de volgende opdracht uit te voeren:

    ```console
    # sudo chkconfig network on
    ```

1. Registreer uw Red Hat-abonnement om de installatie van pakketten vanuit de RHEL-opslag plaats mogelijk te maken door de volgende opdracht uit te voeren:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Het WALinuxAgent-pakket, `WALinuxAgent-<version>` , is gepusht naar de Red Hat extras-opslag plaats. Schakel de extra's-opslag plaats in door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u deze wijziging wilt door voeren, opent u `/boot/grub/menu.lst` in een tekst editor en zorgt u ervoor dat de standaard-kernel de volgende para meters bevat:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Dit zorgt er ook voor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning voor Azure kan helpen bij het oplossen van problemen.
    
    Daarnaast wordt u aangeraden de volgende para meters te verwijderen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort.  U kunt de `crashkernel` optie zo nodig laten configureren. Houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer reduceert. Deze configuratie kan problemen opleveren bij kleinere grootten van virtuele machines.


1. Zorg ervoor dat de SSH-server (Secure Shell) is geïnstalleerd en geconfigureerd om te starten bij het opstarten. Dit is meestal de standaard instelling. Wijzig/etc/ssh/sshd_config zodat deze de volgende regel bevat:

    ```config
    ClientAliveInterval 180
    ```

1. Installeer de Azure Linux-agent door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    Als u het WALinuxAgent-pakket installeert, worden de NetworkManager-en NetworkManager-gnome-pakketten verwijderd als deze nog niet zijn verwijderd in stap 3.

1. Maak geen wissel ruimte op de schijf met het besturings systeem.

    De Azure Linux-agent kan automatisch wissel ruimte configureren door gebruik te maken van de lokale bron schijf die is gekoppeld aan de virtuele machine nadat de virtuele machine is ingericht op Azure. De lokale bron schijf is een tijdelijke schijf en kan worden leeg gemaakt als de inrichting van de virtuele machine ongedaan is. Nadat u de Azure Linux-agent in de vorige stap hebt geïnstalleerd, wijzigt u de volgende para meters in/etc/waagent.conf op de juiste manier:

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Hef de registratie van het abonnement op (indien nodig) door de volgende opdracht uit te voeren:

    ```console
    # sudo subscription-manager unregister
    ```

1. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Klik op **actie**  >  **Afsluiten** in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.


### <a name="rhel-7-using-hyper-v-manager"></a>RHEL 7 met Hyper-V-beheer

1. Selecteer de virtuele machine in Hyper-V-beheer.

1. Klik op **verbinding maken** om een console venster voor de virtuele machine te openen.

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Zorg ervoor dat de netwerk service wordt gestart op het moment van opstarten door de volgende opdracht uit te voeren:

    ```console
    # sudo systemctl enable network
    ```

1. Registreer uw Red Hat-abonnement om de installatie van pakketten vanuit de RHEL-opslag plaats mogelijk te maken door de volgende opdracht uit te voeren:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u deze wijziging wilt door voeren, opent u `/etc/default/grub` in een tekst editor en bewerkt u de `GRUB_CMDLINE_LINUX` para meter. Bijvoorbeeld:

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    Dit zorgt er ook voor dat alle console berichten worden verzonden naar de eerste seriële poort en interactie met de seriële console inschakelen, die ondersteuning voor Azure kan helpen bij het oplossen van problemen. Deze configuratie schakelt ook de nieuwe RHEL 7-naamgevings conventies voor Nic's uit.

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort. U kunt de `crashkernel` optie zo nodig laten configureren. Houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer reduceert. Dit kan problemen opleveren bij kleinere VM-grootten.

1. Wanneer u klaar bent `/etc/default/grub` met bewerken, voert u de volgende opdracht uit om de configuratie van grub opnieuw samen te stellen:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > Als u een met UEFI ingeschakelde VM uploadt, is de opdracht voor het bijwerken van grub `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` .

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten. Dit is meestal de standaard instelling. Wijzig `/etc/ssh/sshd_config` de volgende regel in:

    ```config
    ClientAliveInterval 180
    ```

1. Het WALinuxAgent-pakket, `WALinuxAgent-<version>` , is gepusht naar de Red Hat extras-opslag plaats. Schakel de extra's-opslag plaats in door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installeer de Azure Linux-agent, Cloud-init en andere nood zakelijke hulpprogram ma's door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Cloud-init configureren voor het afhandelen van de inrichting:

    1. Waagent configureren voor Cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Als u een specifieke virtuele machine migreert en geen gegeneraliseerde installatie kopie wilt maken, stelt u `Provisioning.Agent=disabled` de `/etc/waagent.conf` configuratie in.
    
    1. Koppels configureren:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Azure data source configureren:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. Als deze is geconfigureerd, verwijdert u de bestaande swapfile:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Cloud-init-logboek registratie configureren:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Wisselings configuratie maakt geen wissel ruimte op de schijf met het besturings systeem.

    Voorheen werd de Azure Linux-agent gebruikt om de wissel ruimte automatisch te configureren met behulp van de lokale bron schijf die is gekoppeld aan de virtuele machine nadat de virtuele machine is ingericht op Azure. Dit wordt nu echter verwerkt door Cloud-init, u **moet** de Linux-agent niet gebruiken om de bron schijf te Format teren Maak het wissel bestand, wijzig de volgende para meters op de `/etc/waagent.conf` juiste manier:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Als u de registratie van het abonnement wilt opheffen, voert u de volgende opdracht uit:

    ```console
    # sudo subscription-manager unregister
    ```

1. Inrichting

    Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

    > [!CAUTION]
    > Als u een specifieke virtuele machine migreert en geen gegeneraliseerde installatie kopie wilt maken, slaat u de stap voor het opheffen van de inrichting over. Als u de opdracht uitvoert `waagent -force -deprovision` , wordt de bron machine onbruikbaar. deze stap is alleen bedoeld voor het maken van een gegeneraliseerde installatie kopie.
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. Klik op **actie**  >  **Afsluiten** in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.

### <a name="rhel-8-using-hyper-v-manager"></a>RHEL 8 met Hyper-V-beheer

1. Selecteer de virtuele machine in Hyper-V-beheer.

1. Klik op **verbinding maken** om een console venster voor de virtuele machine te openen.

1. Zorg ervoor dat de netwerk beheer-service wordt gestart op het moment van opstarten door de volgende opdracht uit te voeren:

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. Configureer de netwerk interface zodanig dat deze automatisch wordt gestart bij het opstarten en DHCP gebruikt:

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. Registreer uw Red Hat-abonnement om de installatie van pakketten vanuit de RHEL-opslag plaats mogelijk te maken door de volgende opdracht uit te voeren:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Wijzig de kernel-opstart regel in uw grub-configuratie zodat u aanvullende kernel-para meters voor Azure kunt gebruiken en de seriële console kunt inschakelen. 

    1. Huidige GRUB-para meters verwijderen:
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. Bewerk `/etc/default/grub` in een tekst editor en voeg de volgende para meters toe:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   Dit zorgt er ook voor dat alle console berichten worden verzonden naar de eerste seriële poort en interactie met de seriële console inschakelen, die ondersteuning voor Azure kan helpen bij het oplossen van problemen. Deze configuratie schakelt ook de nieuwe naam conventies voor Nic's uit.
   
   1. Daarnaast wordt u aangeraden de volgende para meters te verwijderen:

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort. U kunt de `crashkernel` optie zo nodig laten configureren. Houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer reduceert. Dit kan problemen opleveren bij kleinere VM-grootten.

1. Wanneer u klaar bent `/etc/default/grub` met bewerken, voert u de volgende opdracht uit om de configuratie van grub opnieuw samen te stellen:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    Voer de volgende opdracht uit voor een virtuele-machine met UEFI:

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten. Dit is meestal de standaard instelling. Wijzig `/etc/ssh/sshd_config` de volgende regel in:

    ```config
    ClientAliveInterval 180
    ```

1. Installeer de Azure Linux-agent, Cloud-init en andere nood zakelijke hulpprogram ma's door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Cloud-init configureren voor het afhandelen van de inrichting:

    1. Waagent configureren voor Cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Als u een specifieke virtuele machine migreert en geen gegeneraliseerde installatie kopie wilt maken, stelt u `Provisioning.Agent=disabled` de `/etc/waagent.conf` configuratie in.
    
    1. Koppels configureren:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Azure data source configureren:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. Als deze is geconfigureerd, verwijdert u de bestaande swapfile:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Cloud-init-logboek registratie configureren:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Wisselings configuratie maakt geen wissel ruimte op de schijf met het besturings systeem.

    Voorheen werd de Azure Linux-agent gebruikt om de wissel ruimte automatisch te configureren met behulp van de lokale bron schijf die is gekoppeld aan de virtuele machine nadat de virtuele machine is ingericht op Azure. Dit wordt nu echter verwerkt door Cloud-init, u **moet** de Linux-agent niet gebruiken om de bron schijf te Format teren Maak het wissel bestand, wijzig de volgende para meters op de `/etc/waagent.conf` juiste manier:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Als u de registratie van het abonnement wilt opheffen, voert u de volgende opdracht uit:

    ```console
    # sudo subscription-manager unregister
    ```

1. Inrichting

    Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > Als u een specifieke virtuele machine migreert en geen gegeneraliseerde installatie kopie wilt maken, slaat u de stap voor het opheffen van de inrichting over. Als u de opdracht uitvoert `waagent -force -deprovision` , wordt de bron machine onbruikbaar. deze stap is alleen bedoeld voor het maken van een gegeneraliseerde installatie kopie.


1. Klik op **actie**  >  **Afsluiten** in Hyper-V-beheer. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.


## <a name="kvm"></a>KVM

In deze sectie wordt beschreven hoe u met KVM een [RHEL 6](#rhel-6-using-kvm) -of [RHEL 7](#rhel-7-using-kvm) -distributie voorbereidt om te uploaden naar Azure. 

### <a name="rhel-6-using-kvm"></a>RHEL 6 met KVM

1. Down load de KVM-installatie kopie van RHEL 6 van de website Red Hat.

1. Stel een Hoofdwacht woord in.

    Genereer een versleuteld wacht woord en kopieer de uitvoer van de opdracht:

    ```console
    # openssl passwd -1 changeme
    ```

    Stel een hoofd wachtwoord in met guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Wijzig het tweede veld van de hoofd gebruiker van "!!" naar het versleutelde wacht woord.

1. Maak een virtuele machine in KVM vanuit de qcow2-installatie kopie. Stel het schijf type in op **qcow2** en stel het apparaat model van de virtuele netwerk interface in op **virtio**. Vervolgens start u de virtuele machine en meldt u zich aan als root.

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Verplaats (of verwijder) de udev-regels om te voor komen dat statische regels voor de Ethernet-interface worden gegenereerd. Deze regels veroorzaken problemen bij het klonen van een virtuele machine in azure of Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Zorg ervoor dat de netwerk service wordt gestart op het moment van opstarten door de volgende opdracht uit te voeren:

    ```console
    # chkconfig network on
    ```

1. Registreer uw Red Hat-abonnement om de installatie van pakketten vanuit de RHEL-opslag plaats mogelijk te maken door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u deze configuratie wilt uitvoeren, opent u `/boot/grub/menu.lst` in een tekst editor en zorgt u ervoor dat de standaard-kernel de volgende para meters bevat:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Dit zorgt er ook voor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning voor Azure kan helpen bij het oplossen van problemen.
    
    Daarnaast wordt u aangeraden de volgende para meters te verwijderen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort. U kunt de `crashkernel` optie zo nodig laten configureren. Houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer reduceert. Dit kan problemen opleveren bij kleinere VM-grootten.


1. Hyper-V-modules toevoegen aan initramfs:  

    Bewerk `/etc/dracut.conf` en voeg de volgende inhoud toe:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs opnieuw maken:

    ```config-conf
    # dracut -f -v
    ```

1. Cloud-init verwijderen:

    ```console
    # yum remove cloud-init
    ```

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten van het systeem:

    ```console
    # chkconfig sshd on
    ```

    Wijzig/etc/ssh/sshd_config zodat de volgende regels worden toegevoegd:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Het WALinuxAgent-pakket, `WALinuxAgent-<version>` , is gepusht naar de Red Hat extras-opslag plaats. Schakel de extra's-opslag plaats in door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Installeer de Azure Linux-agent door de volgende opdracht uit te voeren:

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. De Azure Linux-agent kan automatisch wissel ruimte configureren door gebruik te maken van de lokale bron schijf die is gekoppeld aan de virtuele machine nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale bron schijf een tijdelijke schijf is en kan worden leeg gemaakt als de inrichting van de virtuele machine wordt opheffen. Nadat u de Azure Linux-agent in de vorige stap hebt geïnstalleerd, wijzigt u de volgende para meters in **/etc/waagent.conf** op de juiste manier:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Hef de registratie van het abonnement op (indien nodig) door de volgende opdracht uit te voeren:

    ```console-conf
    # subscription-manager unregister
    ```

1. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

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

1. Sluit de virtuele machine in KVM af.

1. Converteer de qcow2-afbeelding naar de VHD-indeling.

    > [!NOTE]
    > Er is een bekende fout in qemu-img-versies >= 2.2.1 dat resulteert in een VHD met een onjuiste indeling. Het probleem is opgelost in QEMU 2,6. U kunt het beste een qemu-img 2.2.0 of lager gebruiken of bijwerken naar 2,6 of hoger. Naslaginformatie: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Converteer de afbeelding eerst naar de RAW-indeling:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    Zorg ervoor dat de grootte van de onbewerkte afbeelding is uitgelijnd op 1 MB. Als dat niet het geval is, wordt de grootte afronden op 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    De onbewerkte schijf converteren naar een VHD met een vaste grootte:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Of, met qemu versie **2.6 +** bevat de `force_size` optie:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>RHEL 7 met KVM

1. Down load de KVM-installatie kopie van RHEL 7 van de website Red Hat. Deze procedure maakt gebruik van RHEL 7 als voor beeld.

1. Stel een Hoofdwacht woord in.

    Genereer een versleuteld wacht woord en kopieer de uitvoer van de opdracht:

    ```console
    # openssl passwd -1 changeme
    ```

    Stel een hoofd wachtwoord in met guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Het tweede veld van de hoofd gebruiker wijzigen van "!!" naar het versleutelde wacht woord.

1. Maak een virtuele machine in KVM vanuit de qcow2-installatie kopie. Stel het schijf type in op **qcow2** en stel het apparaat model van de virtuele netwerk interface in op **virtio**. Vervolgens start u de virtuele machine en meldt u zich aan als root.

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Zorg ervoor dat de netwerk service wordt gestart op het moment van opstarten door de volgende opdracht uit te voeren:

    ```console
    # sudo systemctl enable network
    ```

1. Registreer uw Red Hat-abonnement om de installatie van pakketten vanuit de RHEL-opslag plaats in te scha kelen door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u deze configuratie wilt uitvoeren, opent u `/etc/default/grub` in een tekst editor en bewerkt u de `GRUB_CMDLINE_LINUX` para meter. Bijvoorbeeld:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Met deze opdracht zorgt u er ook voor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning voor Azure kan helpen bij het oplossen van problemen. Met deze opdracht worden ook de nieuwe RHEL 7-naamgevings conventies voor Nic's uitgeschakeld. Daarnaast wordt u aangeraden de volgende para meters te verwijderen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort. U kunt de `crashkernel` optie zo nodig laten configureren. Houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer reduceert. Dit kan problemen opleveren bij kleinere VM-grootten.

1. Wanneer u klaar bent `/etc/default/grub` met bewerken, voert u de volgende opdracht uit om de configuratie van grub opnieuw samen te stellen:

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Voeg Hyper-V-modules toe aan initramfs.

    `/etc/dracut.conf`Inhoud bewerken en toevoegen:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs opnieuw maken:

    ```config-conf
    # dracut -f -v
    ```

1. Cloud-init verwijderen:

    ```console
    # yum remove cloud-init
    ```

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten van het systeem:

    ```console
    # systemctl enable sshd
    ```

    Wijzig/etc/ssh/sshd_config zodat de volgende regels worden toegevoegd:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Het WALinuxAgent-pakket, `WALinuxAgent-<version>` , is gepusht naar de Red Hat extras-opslag plaats. Schakel de extra's-opslag plaats in door de volgende opdracht uit te voeren:

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installeer de Azure Linux-agent door de volgende opdracht uit te voeren:

    ```console
    # yum install WALinuxAgent
    ```

    De waagent-service inschakelen:

    ```console
    # systemctl enable waagent.service
    ```

1. Cloud-init installeren volg de stappen in ' een RHEL 7-virtuele machine voorbereiden vanuit Hyper-V-beheer ', stap 12, ' Cloud-init installeren voor het afhandelen van de inrichting '.

1. Configuratie wisselen 

    Maak geen wissel ruimte op de schijf met het besturings systeem.
    Volg de stappen in ' een virtuele machine van RHEL 7 voorbereiden vanuit Hyper-V-beheer ', stap 13, ' swap configuratie '


1. Hef de registratie van het abonnement op (indien nodig) door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager unregister
    ```


1. Inrichting

    Volg de stappen in ' een RHEL 7-virtuele machine voorbereiden vanuit Hyper-V-beheer ', stap 15, ' inrichting opheffen '

1. Sluit de virtuele machine in KVM af.

1. Converteer de qcow2-afbeelding naar de VHD-indeling.

    > [!NOTE]
    > Er is een bekende fout in qemu-img-versies >= 2.2.1 dat resulteert in een VHD met een onjuiste indeling. Het probleem is opgelost in QEMU 2,6. U kunt het beste een qemu-img 2.2.0 of lager gebruiken of bijwerken naar 2,6 of hoger. Naslaginformatie: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Converteer de afbeelding eerst naar de RAW-indeling:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Zorg ervoor dat de grootte van de onbewerkte afbeelding is uitgelijnd op 1 MB. Als dat niet het geval is, wordt de grootte afronden op 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    De onbewerkte schijf converteren naar een VHD met een vaste grootte:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Of, met qemu versie **2.6 +** bevat de `force_size` optie:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

In deze sectie wordt beschreven hoe u een [RHEL 6](#rhel-6-using-vmware) -of [RHEL 7](#rhel-6-using-vmware)  -distributie van VMware voorbereidt.

### <a name="prerequisites"></a>Vereisten
In deze sectie wordt ervan uitgegaan dat u al een RHEL virtuele machine hebt geïnstalleerd in VMware. Zie voor meer informatie over het installeren van een besturings systeem in VMware de [installatie handleiding voor VMware Guest Operating System](https://partnerweb.vmware.com/GOSIG/home.html).

* Wanneer u het Linux-besturings systeem installeert, raden we u aan om standaard partities te gebruiken in plaats van LVM, wat vaak de standaard waarde is voor veel installaties. Hiermee wordt voor komen dat LVM naam strijdig is met de gekloonde virtuele machine, met name als een besturingssysteem schijf ooit moet worden gekoppeld aan een andere virtuele machine voor het oplossen van problemen. LVM of RAID kan worden gebruikt op gegevens schijven als dit de voor keur heeft.
* Configureer geen swap partitie op de schijf met het besturings systeem. U kunt de Linux-agent configureren voor het maken van een wissel bestand op de tijdelijke bron schijf. In de volgende stappen vindt u meer informatie hierover.
* Wanneer u de virtuele harde schijf maakt, selecteert u **virtuele schijf opslaan als één bestand**.

### <a name="rhel-6-using-vmware"></a>RHEL 6 met VMware
1. In RHEL 6 kan NetworkManager conflicteren met de Azure Linux-agent. Verwijder dit pakket door de volgende opdracht uit te voeren:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Maak in de map/etc/sysconfig/een bestand met de naam **netwerk** dat de volgende tekst bevat:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Verplaats (of verwijder) de udev-regels om te voor komen dat statische regels voor de Ethernet-interface worden gegenereerd. Deze regels veroorzaken problemen bij het klonen van een virtuele machine in azure of Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Zorg ervoor dat de netwerk service wordt gestart op het moment van opstarten door de volgende opdracht uit te voeren:

    ```console
    # sudo chkconfig network on
    ```

1. Registreer uw Red Hat-abonnement om de installatie van pakketten vanuit de RHEL-opslag plaats mogelijk te maken door de volgende opdracht uit te voeren:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Het WALinuxAgent-pakket, `WALinuxAgent-<version>` , is gepusht naar de Red Hat extras-opslag plaats. Schakel de extra's-opslag plaats in door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u dit wilt doen, opent u `/etc/default/grub` in een tekst editor en bewerkt u de `GRUB_CMDLINE_LINUX` para meter. Bijvoorbeeld:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   Dit zorgt er ook voor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning voor Azure kan helpen bij het oplossen van problemen. Daarnaast wordt u aangeraden de volgende para meters te verwijderen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort. U kunt de `crashkernel` optie zo nodig laten configureren. Houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer reduceert. Dit kan problemen opleveren bij kleinere VM-grootten.

1. Hyper-V-modules toevoegen aan initramfs:

    Bewerk `/etc/dracut.conf` en voeg de volgende inhoud toe:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs opnieuw maken:

    ```config-cong
    # dracut -f -v
    ```

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten. Dit is meestal de standaard instelling. Wijzig `/etc/ssh/sshd_config` de volgende regel in:

    ```config
    ClientAliveInterval 180
    ```

1. Installeer de Azure Linux-agent door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. Maak geen wissel ruimte op de schijf met het besturings systeem.

    De Azure Linux-agent kan automatisch wissel ruimte configureren door gebruik te maken van de lokale bron schijf die is gekoppeld aan de virtuele machine nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale bron schijf een tijdelijke schijf is en kan worden leeg gemaakt als de inrichting van de virtuele machine wordt opheffen. Nadat u de Azure Linux-agent in de vorige stap hebt geïnstalleerd, wijzigt u de volgende para meters op de `/etc/waagent.conf` juiste manier:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Hef de registratie van het abonnement op (indien nodig) door de volgende opdracht uit te voeren:

    ```console
    # sudo subscription-manager unregister
    ```

1. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

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

1. Sluit de virtuele machine af en converteer het VMDK-bestand naar een VHD-bestand.

    > [!NOTE]
    > Er is een bekende fout in qemu-img-versies >= 2.2.1 dat resulteert in een VHD met een onjuiste indeling. Het probleem is opgelost in QEMU 2,6. U kunt het beste een qemu-img 2.2.0 of lager gebruiken of bijwerken naar 2,6 of hoger. Naslaginformatie: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Converteer de afbeelding eerst naar de RAW-indeling:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    Zorg ervoor dat de grootte van de onbewerkte afbeelding is uitgelijnd op 1 MB. Als dat niet het geval is, wordt de grootte afronden op 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    De onbewerkte schijf converteren naar een VHD met een vaste grootte:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Of, met qemu versie **2.6 +** bevat de `force_size` optie:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>RHEL 7 met VMware
1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Zorg ervoor dat de netwerk service wordt gestart op het moment van opstarten door de volgende opdracht uit te voeren:

    ```console
    # sudo systemctl enable network
    ```

1. Registreer uw Red Hat-abonnement om de installatie van pakketten vanuit de RHEL-opslag plaats mogelijk te maken door de volgende opdracht uit te voeren:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Wijzig de kernel-opstart regel in de grub-configuratie zodat er aanvullende kernel-para meters voor Azure zijn. Als u deze wijziging wilt door voeren, opent u `/etc/default/grub` in een tekst editor en bewerkt u de `GRUB_CMDLINE_LINUX` para meter. Bijvoorbeeld:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Deze configuratie zorgt er ook voor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning voor Azure kan helpen bij het oplossen van problemen. De nieuwe RHEL 7-naamgevings conventies voor Nic's worden ook uitgeschakeld. Daarnaast wordt u aangeraden de volgende para meters te verwijderen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Grafisch en stil opstarten zijn niet handig in een cloud omgeving waar alle logboeken moeten worden verzonden naar de seriële poort. U kunt de `crashkernel` optie zo nodig laten configureren. Houd er rekening mee dat deze para meter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer reduceert. Dit kan problemen opleveren bij kleinere VM-grootten.

1. Wanneer u klaar bent `/etc/default/grub` met bewerken, voert u de volgende opdracht uit om de configuratie van grub opnieuw samen te stellen:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Voeg Hyper-V-modules toe aan initramfs.

    Bewerken `/etc/dracut.conf` , inhoud toevoegen:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs opnieuw maken:

    ```console
    # dracut -f -v
    ```

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten. Dit is doorgaans de standaard instelling. Wijzig `/etc/ssh/sshd_config` de volgende regel in:

    ```config
    ClientAliveInterval 180
    ```

1. Het WALinuxAgent-pakket, `WALinuxAgent-<version>` , is gepusht naar de Red Hat extras-opslag plaats. Schakel de extra's-opslag plaats in door de volgende opdracht uit te voeren:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installeer de Azure Linux-agent door de volgende opdracht uit te voeren:

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. Cloud-init installeren

    Volg de stappen in ' een virtuele machine van RHEL 7 voorbereiden vanuit Hyper-V-beheer ', stap 12, ' Cloud-init installeren voor het afhandelen van de inrichting '.

1. Configuratie wisselen

    Maak geen wissel ruimte op de schijf met het besturings systeem.
    Volg de stappen in ' een virtuele machine van RHEL 7 voorbereiden vanuit Hyper-V-beheer ', stap 13, ' swap configuratie '

1. Als u de registratie van het abonnement wilt opheffen, voert u de volgende opdracht uit:

    ```console
    # sudo subscription-manager unregister
    ```

1. Inrichting

    Volg de stappen in ' een RHEL 7-virtuele machine voorbereiden vanuit Hyper-V-beheer ', stap 15, ' inrichting opheffen '


1. Sluit de virtuele machine af en converteer het VMDK-bestand naar de VHD-indeling.

    > [!NOTE]
    > Er is een bekende fout in qemu-img-versies >= 2.2.1 dat resulteert in een VHD met een onjuiste indeling. Het probleem is opgelost in QEMU 2,6. U kunt het beste een qemu-img 2.2.0 of lager gebruiken of bijwerken naar 2,6 of hoger. Naslaginformatie: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Converteer de afbeelding eerst naar de RAW-indeling:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    Zorg ervoor dat de grootte van de onbewerkte afbeelding is uitgelijnd op 1 MB. Als dat niet het geval is, wordt de grootte afronden op 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    De onbewerkte schijf converteren naar een VHD met een vaste grootte:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Of, met qemu versie **2.6 +** bevat de `force_size` optie:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>Kickstart-bestand

In deze sectie wordt beschreven hoe u een RHEL 7 distributie van een ISO voorbereidt met behulp van een kickstart-bestand.

### <a name="rhel-7-from-a-kickstart-file"></a>RHEL 7 van een kickstart-bestand

1.  Maak een kickstart-bestand dat de volgende inhoud bevat en sla het bestand op. Zie de [installatie handleiding voor kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)voor meer informatie over de installatie van KickStart.

    ```text
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
      auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
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

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. Plaats het kickstart-bestand waar het installatie systeem toegang toe heeft.

1. Maak een nieuwe virtuele machine in Hyper-V-beheer. Selecteer op de pagina **virtuele harde schijf aansluiten** de optie **later een virtuele harde schijf koppelen** en voltooi de wizard Nieuwe virtuele machine.

1. Open de instellingen van de virtuele machine:

    a.  Koppel een nieuwe virtuele harde schijf aan de virtuele machine. Zorg ervoor dat u de **VHD-indeling** en de **vaste grootte** selecteert.

    b.  Koppel de ISO-installatie aan het DVD-station.

    c.  Stel het BIOS in op opstarten vanaf CD.

1. Start de virtuele machine. Wanneer de installatie handleiding wordt weer gegeven, drukt u op **Tab** om de opstart opties te configureren.

1. Geef `inst.ks=<the location of the kickstart file>` aan het einde van de opstart opties op en druk op **Enter**.

1. Wacht totdat de installatie is voltooid. Wanneer de virtuele machine is voltooid, wordt deze automatisch afgesloten. Uw Linux-VHD is nu gereed om te worden geüpload naar Azure.

## <a name="known-issues"></a>Bekende problemen
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Het Hyper-V-stuur programma kan niet worden opgenomen in de eerste RAM-schijf wanneer een niet-Hyper-V-Hyper Visor wordt gebruikt

In sommige gevallen worden de Stuur Programma's voor Hyper-V in de eerste RAM-schijf (initrd of initramfs) mogelijk niet in de installatie van Linux vermeld, tenzij Linux detecteert dat deze wordt uitgevoerd in een Hyper-V-omgeving.

Wanneer u een ander virtualisatie systeem gebruikt (dat wil zeggen, VirtualBox, xen, enzovoort) om uw Linux-installatie kopie voor te bereiden, moet u initrd mogelijk opnieuw samen stellen om ervoor te zorgen dat ten minste de hv_vmbus en hv_storvsc kernel-modules beschikbaar zijn op de eerste RAM-schijf. Dit is een bekend probleem ten minste op systemen die zijn gebaseerd op de upstream-Red Hat-distributie.

U kunt dit probleem oplossen door Hyper-V-modules toe te voegen aan initramfs en deze opnieuw te bouwen:

Bewerk `/etc/dracut.conf` en voeg de volgende inhoud toe:

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

Initramfs opnieuw maken:

```console
# dracut -f -v
```

Zie de informatie over het opnieuw opbouwen van [initramfs](https://access.redhat.com/solutions/1958)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* U kunt nu uw Red Hat Enterprise Linux virtuele harde schijf gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het VHD-bestand naar Azure uploadt.
* Zie [de website Red Hat](https://access.redhat.com/certified-hypervisors)voor meer informatie over de Hyper visors die zijn gecertificeerd voor het uitvoeren van Red Hat Enterprise Linux.
* Ga naar de documentatie pagina voor [BYOS](../workloads/redhat/byos.md)voor meer informatie over het gebruik van voor bereide RHEL BYOS-installatie kopieën.
