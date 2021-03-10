---
title: Een Ubuntu Linux VHD maken en uploaden in azure
description: Meer informatie over het maken en uploaden van een virtuele harde schijf (VHD) van Azure die een Ubuntu Linux besturings systeem bevat.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: 92ceecd16a428593764fe5ab6478cc4ea7ab91d7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554612"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Een virtuele Ubuntu-machine voor Azure voorbereiden


Ubuntu publiceert nu officiële Azure-Vhd's voor down loads op [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Als u uw eigen gespecialiseerde Ubuntu-installatie kopie voor Azure moet bouwen, in plaats van de hand matige procedure hieronder te gebruiken, wordt u aangeraden te beginnen met deze bekende werk schijven en zo nodig aan te passen. U kunt de nieuwste afbeeldings releases altijd vinden op de volgende locaties:

* Ubuntu 16.04/Xenial: [Ubuntu-16,04-server-cloudimg-amd64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al een Ubuntu Linux besturings systeem hebt geïnstalleerd op een virtuele harde schijf. Er zijn meerdere hulpprogram ma's voor het maken van VHD-bestanden, zoals Hyper-V. Zie [de Hyper-V-functie installeren en een virtuele machine configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))voor instructies.

**Ubuntu-installatie notities**

* Zie ook [algemene Linux-installatie notities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* De VHDX-indeling wordt niet ondersteund in azure, alleen **vaste VHD**.  U kunt de schijf converteren naar VHD-indeling met behulp van Hyper-V-beheer of de `Convert-VHD` cmdlet.
* Bij de installatie van het Linux-systeem wordt aanbevolen om standaard partities te gebruiken in plaats van LVM (vaak de standaard instelling voor veel installaties). Hiermee wordt voor komen dat LVM naam strijdig is met gekloonde Vm's, met name als een besturingssysteem schijf ooit moet worden gekoppeld aan een andere virtuele machine voor het oplossen van problemen. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) of [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) kan worden gebruikt op gegevens schijven als dit de voor keur heeft.
* Configureer geen swap partitie of swapfile op de besturingssysteem schijf. De inrichtings agent voor Cloud-init kan worden geconfigureerd voor het maken van een wissel bestand of een swap partitie op de tijdelijke bron schijf. Meer informatie hierover vindt u in de volgende stappen.
* Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1MB is vóór de conversie. Zie [installatie notities voor Linux](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.

## <a name="manual-steps"></a>Hand matige stappen
> [!NOTE]
> Voordat u probeert uw eigen aangepaste Ubuntu-installatie kopie voor Azure te maken, kunt u in plaats daarvan de vooraf gemaakte en geteste installatie kopieën gebruiken [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) .
> 
> 

1. Selecteer de virtuele machine in het middelste deel venster van Hyper-V-beheer.

2. Klik op **verbinding maken** om het venster voor de virtuele machine te openen.

3. Vervang de huidige opslag plaatsen in de installatie kopie om de Azure-opslag plaats van Ubuntu te gebruiken.

    Voor `/etc/apt/sources.list` het bewerken wordt het aanbevolen een back-up te maken:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16,04 en Ubuntu 18,04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. De Ubuntu Azure-installatie kopieën maken nu gebruik van de kernel op basis van [Azure](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel). Werk het besturings systeem bij naar de laatste door Azure aangepaste kernel en installeer Azure Linux-hulpprogram ma's (inclusief Hyper-V-afhankelijkheden) door de volgende opdrachten uit te voeren:

    Ubuntu 16,04 en Ubuntu 18,04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Wijzig de kernel-opstart regel voor grub zodat er aanvullende kernel-para meters voor Azure worden toegevoegd. Als u dit wilt openen `/etc/default/grub` in een tekst editor, zoekt u de variabele met de naam `GRUB_CMDLINE_LINUX_DEFAULT` (of voegt u deze toe, indien nodig) en bewerkt u deze met de volgende para meters:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Sla het bestand op en sluit dit en voer het vervolgens uit `sudo update-grub` . Dit zorgt ervoor dat alle console berichten worden verzonden naar de eerste seriële poort, die ondersteuning biedt voor technische ondersteuning van Azure bij problemen met de fout opsporing.

6. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is doorgaans de standaard instelling.

7. Installeer de Cloud-init (de inrichtings agent) en de Azure Linux-agent (de handler voor gast uitbreidingen). Cloud-init gebruikt netplan voor het configureren van de systeem netwerk configuratie tijdens het inrichten en elke volgende keer opstarten.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  Het `walinuxagent` pakket kan de `NetworkManager` pakketten en verwijderen `NetworkManager-gnome` als deze zijn geïnstalleerd.

8. Verwijder Cloud-init-standaard configuraties en overgebleven netplan artefacten die mogelijk conflicteren met Cloud-init inrichten op Azure:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Cloud-init configureren om het systeem in te richten met behulp van de Azure-gegevens Bron:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Configureer de Azure Linux-agent om te vertrouwen op Cloud-init om het inrichten uit te voeren. Bekijk het [project WALinuxAgent](https://github.com/Azure/WALinuxAgent) voor meer informatie over deze opties.

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Schone Cloud-init en Azure Linux agent runtime-artefacten en-Logboeken:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Voer de volgende opdrachten uit om de inrichting van de virtuele machine ongedaan te maken en deze voor te bereiden voor de inrichting van Azure:

    > [!NOTE]
    > De `sudo waagent -force -deprovision+user` opdracht zal proberen het systeem op te schonen en het geschikt maken voor opnieuw inrichten. `+user`Met deze optie worden de laatste ingerichte gebruikers account en de bijbehorende gegevens verwijderd.

    > [!WARNING]
    > Het ongedaan maken van de inrichting met de bovenstaande opdracht garandeert niet dat de installatie kopie is gewist van alle gevoelige informatie en is geschikt voor herdistributie.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Klik op **actie-> afgesloten** in Hyper-V-beheer.

14. Azure accepteert alleen Vhd's met een vaste grootte. Als de besturingssysteem schijf van de virtuele machine geen VHD met vaste grootte is, gebruikt u de `Convert-VHD` Power shell-cmdlet en geeft u de `-VHDType Fixed` optie op. Bekijk hier de documenten die u hier kunt vinden `Convert-VHD` : [Convert-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>Volgende stappen
U kunt nu uw Ubuntu Linux virtuele harde schijf gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het VHD-bestand naar Azure uploadt.