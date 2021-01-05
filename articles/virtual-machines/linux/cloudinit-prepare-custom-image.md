---
title: Azure VM-installatie kopie voorbereiden voor gebruik met Cloud-init
description: Een bestaande installatie kopie van een Azure-VM voorbereiden voor implementatie met Cloud-init
author: danis
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 3aed65b0319f9a80c5ebc45428ff0c380c33fc3d
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883262"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Een bestaande installatie kopie van een virtuele Linux Azure-machine voorbereiden voor gebruik met Cloud-init
In dit artikel wordt beschreven hoe u een bestaande virtuele machine van Azure maakt en deze bereidt om te worden geïmplementeerd en klaar is om Cloud-init te gebruiken. De resulterende afbeelding kan worden gebruikt om een nieuwe virtuele machine of virtuele-machine schaal sets te implementeren. deze kan vervolgens worden aangepast door Cloud-init tijdens de implementatie.  Deze Cloud-init-scripts worden uitgevoerd bij de eerste keer opstarten zodra de resources zijn ingericht door Azure. Zie [Cloud-init Overview](using-cloud-init.md) (Engelstalig) voor meer informatie over hoe Cloud-init standaard werkt in Azure en de ondersteunde Linux-distributies

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u al een actieve virtuele machine van Azure hebt waarop een ondersteunde versie van het Linux-besturings systeem wordt uitgevoerd. U hebt de machine al geconfigureerd voor uw behoeften, alle benodigde modules geïnstalleerd, alle vereiste updates verwerkt en deze getest om te controleren of deze voldoet aan uw vereisten. 

## <a name="preparing-rhel-76--centos-76"></a>RHEL 7,6/CentOS 7,6 voorbereiden
U moet SSH in uw virtuele Linux-machine en de volgende opdrachten uitvoeren om Cloud-init te kunnen installeren.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install -y cloud-init 
```

Werk de `cloud_init_modules` sectie in `/etc/cloud/cloud.cfg` om de volgende modules op te laten staan:

```bash
- disk_setup
- mounts
```

Hier volgt een voor beeld van hoe een sectie voor algemeen gebruik `cloud_init_modules` eruitziet.

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

Een aantal taken met betrekking tot het inrichten en het verwerken van tijdelijke schijven moeten worden bijgewerkt in `/etc/waagent.conf` . Voer de volgende opdrachten uit om de juiste instellingen bij te werken.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Alleen Azure toestaan als gegevens bron voor de Azure Linux-agent door een nieuw bestand te maken `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` met een editor van uw keuze met de volgende regel:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Als voor uw bestaande Azure-installatie kopie een wissel bestand is geconfigureerd en u de wissel bestands configuratie voor nieuwe installatie kopieën wilt wijzigen met Cloud-init, moet u het bestaande wissel bestand verwijderen.

Voor Red Hat-gebaseerde installatie kopieën: Volg de instructies in het volgende Red Hat-document waarin wordt uitgelegd hoe u [het wissel bestand verwijdert](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Voor CentOS-installatie kopieën waarvoor swapfile is ingeschakeld, kunt u de volgende opdracht uitvoeren om de swapfile uit te scha kelen:

```bash
sudo swapoff /mnt/resource/swapfile
```

Controleer of de swapfile-verwijzing is verwijderd uit `/etc/fstab` -deze moet er ongeveer uitzien als in de volgende uitvoer:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Als u ruimte wilt besparen en het wissel bestand wilt verwijderen, kunt u de volgende opdracht uitvoeren:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Extra stap voor voor bereide installatie kopie Cloud-init
> [!NOTE]
> Als uw installatie kopie eerder een **Cloud-init-** voor bereide en geconfigureerde installatie kopie was, moet u de volgende stappen uitvoeren.

De volgende drie opdrachten worden alleen gebruikt als de virtuele machine die u wilt aanpassen een nieuwe gespecialiseerde bron installatie kopie eerder is ingericht door Cloud-init.  U hoeft deze niet uit te voeren als uw installatie kopie is geconfigureerd met behulp van de Azure Linux-agent.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>De Linux-agent instelling volt ooien 
Voor alle installatie kopieën van het Azure-platform is de Azure Linux-agent geïnstalleerd, ongeacht of deze is geconfigureerd door Cloud-init of niet.  Voer de volgende opdracht uit om het ongedaan maken van de inrichting van de gebruiker van de Linux-computer te volt ooien. 

```bash
sudo waagent -deprovision+user -force
```

Raadpleeg de [Azure Linux-agent](../extensions/agent-linux.md) voor meer informatie over de Azure Linux-agent voor het ongedaan maken van de inrichting.

Sluit de SSH-sessie af en voer vanuit uw bash-shell de volgende AzureCLI-opdrachten uit om de toewijzing op te heffen, te generaliseren en een nieuwe Azure VM-installatie kopie te maken.  Vervang `myResourceGroup` en `sourceVmName` door de relevante informatie die overeenkomt met uw sourceVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
