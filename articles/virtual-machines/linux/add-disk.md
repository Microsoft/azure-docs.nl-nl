---
title: Een gegevens schijf toevoegen aan een Linux-VM met behulp van Azure CLI
description: Meer informatie over het toevoegen van een permanente gegevens schijf aan uw virtuele Linux-machine met Azure CLI
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: a4e0cee7a46e3f61f95e87f7cba1fb3595ace3c5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556805"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Een schijf toevoegen aan een virtuele Linux-machine

In dit artikel wordt beschreven hoe u een permanente schijf aan uw virtuele machine koppelt, zodat u uw gegevens kunt bewaren, zelfs als uw virtuele machine opnieuw wordt ingericht door onderhoud of het wijzigen van de grootte.


## <a name="attach-a-new-disk-to-a-vm"></a>Een nieuwe schijf koppelen aan een VM

Als u een nieuwe, lege gegevens schijf wilt toevoegen aan uw VM, gebruikt u de opdracht [AZ VM Disk attach](/cli/azure/vm/disk) met de `--new` para meter. Als uw virtuele machine zich in een beschikbaarheids zone bevindt, wordt de schijf automatisch gemaakt in dezelfde zone als de virtuele machine. Zie [overzicht van Beschikbaarheidszones](../../availability-zones/az-overview.md)voor meer informatie. In het volgende voor beeld wordt een schijf gemaakt met de naam *myDataDisk* met een grootte van 50 GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen

Als u een bestaande schijf wilt koppelen, zoekt u de schijf-ID en geeft u de ID door aan de opdracht [AZ VM Disk attach](/cli/azure/vm/disk) . In het volgende voor beeld wordt een query uitgevoerd voor een schijf met de naam *myDataDisk* in *myResourceGroup*, waarna deze wordt gekoppeld aan de virtuele machine met de naam *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>De schijf Format teren en koppelen

Om uw nieuwe schijf te partitioneren, te Format teren en te koppelen, zodat uw virtuele Linux-machine deze kan gebruiken, SSH in uw VM. Zie voor meer informatie [SSH gebruiken met Linux op Azure](mac-create-ssh-keys.md). In het volgende voor beeld wordt verbinding gemaakt met een virtuele machine met het open bare IP-adres *10.123.123.25* met de gebruikers naam *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>De schijf vinden

Wanneer u verbinding hebt gemaakt met de virtuele machine, moet u de schijf vinden. In dit voor beeld gebruiken we `lsblk` om de schijven weer te geven. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Hier `sdc` is de schijf die we willen, omdat deze 50G is. Als u niet zeker weet op welke schijf het formaat is gebaseerd, gaat u naar de pagina VM in de portal, selecteert u **schijven** en controleert u het LUN-nummer voor de schijf onder **gegevens schijven**. 


### <a name="format-the-disk"></a>De schijf Format teren

Format teer de schijf met `parted` , als de schijf groter is dan 2 tebibytes (Tib) of groter dan, moet u GPT-partitionering gebruiken, als deze zich onder 2TiB bevindt, dan kunt u gebruikmaken van MBR-of GPT-partitionering. 

> [!NOTE]
> Het is raadzaam om de meest recente versie `parted` te gebruiken die voor uw distributie beschikbaar is.
> Als de schijf grootte 2 tebibytes (TiB) of groter is, moet u GPT-partitionering gebruiken. Als de schijf kleiner is dan 2 TiB, kunt u gebruikmaken van MBR-of GPT-partitionering.  


In het volgende voor beeld wordt `parted` op gebruikt `/dev/sdc` , waarbij de eerste gegevens schijf doorgaans op de meeste vm's is. Vervang door `sdc` de juiste optie voor uw schijf. We Format teren ook met behulp van het [xfs](https://xfs.wiki.kernel.org/) -bestands systeem.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Gebruik het [`partprobe`](https://linux.die.net/man/8/partprobe) hulp programma om te controleren of de kernel op de hoogte is van de nieuwe partitie en het bestands systeem. `partprobe`Het gebruik van een fout kan ertoe leiden dat de blkid-of lslbk-opdrachten de UUID voor het nieuwe bestands systeem niet direct retour neren.


### <a name="mount-the-disk"></a>De schijf koppelen

Maak nu een directory voor het koppelen van het bestands systeem met behulp van `mkdir` . In het volgende voor beeld wordt een directory gemaakt op `/datadrive` :

```bash
sudo mkdir /datadrive
```

Gebruik `mount` om het bestands systeem vervolgens te koppelen. In het volgende voor beeld wordt de `/dev/sdc1` partitie gekoppeld aan het `/datadrive` koppel punt:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>De koppeling persistent maken

Om ervoor te zorgen dat het station automatisch na het opnieuw opstarten opnieuw wordt gekoppeld, moet het worden toegevoegd aan het *bestand/etc/fstab* -bestand. Het wordt ook ten zeerste aanbevolen om de UUID (Universally Unique Identifier) in *bestand/etc/fstab* te gebruiken om te verwijzen naar het station in plaats van alleen de naam van het apparaat (zoals */dev/sdc1*). Als het besturingssysteem een schijffout ontdekt tijdens het opstarten, kunt u door de UUID te gebruiken voorkomen dat de verkeerde schijf wordt gekoppeld aan een bepaalde locatie. Resterende gegevensschijven worden dan toegewezen aan dezelfde apparaat-id's. Als u de UUID van het nieuwe station wilt zoeken, gebruikt u het hulpprogramma `blkid`:

```bash
sudo blkid
```

De uitvoer ziet er ongeveer uit als in het volgende voor beeld:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> Het onjuist bewerken van het bestand **/etc/fstab** kan leiden tot een systeem dat niet kan worden opgestart. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. U wordt ook aangeraden een back-up van het bestand /etc/fstab te maken voordat u het bewerkt.

Open vervolgens het *bestand/etc/fstab* -bestand in een tekst editor als volgt:

```bash
sudo nano /etc/fstab
```

In dit voor beeld gebruikt u de UUID-waarde voor het `/dev/sdc1` apparaat dat is gemaakt in de vorige stappen en de koppel punt van `/datadrive` . Voeg de volgende regel toe aan het einde van het `/etc/fstab` bestand:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

In dit voor beeld gebruiken we de nano editor, dus wanneer u klaar bent met het bewerken van het bestand, gebruikt `Ctrl+O` u om het bestand te schrijven en `Ctrl+X` de editor af te sluiten.

> [!NOTE]
> Later het verwijderen van een gegevens schijf zonder het bewerken van fstab kan ertoe leiden dat de virtuele machine niet kan worden opgestart. De meeste distributies bieden de opties *nobootwait* *en/* of fstab. Met deze opties kan een systeem worden opgestart zelfs als de schijf niet kan worden gekoppeld tijdens het opstarten. Raadpleeg de documentatie van uw distributie voor meer informatie over deze para meters.
>
> De *niet-werkende* optie zorgt ervoor dat de VM wordt gestart, zelfs als het bestands systeem beschadigd is of wanneer de schijf niet aanwezig is tijdens het opstarten. Zonder deze optie kunt u zich voordoen als beschreven in [kan niet SSH-naar-Linux-VM wegens FSTAB-fouten](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)
>
> De seriële Azure VM-console kan worden gebruikt voor toegang tot de console van uw virtuele machine als het wijzigen van fstab een opstart fout heeft veroorzaakt. Meer informatie vindt u in de [documentatie van de seriële console](../troubleshooting/serial-console-linux.md).

### <a name="trimunmap-support-for-linux-in-azure"></a>Ondersteuning voor Linux in azure bijsnijden/ontkoppelen
Sommige Linux-kernels ondersteunen bewerkingen voor het verwijderen/ontkoppelen van ongebruikte blokken op de schijf. Deze functie is vooral nuttig in de standaard opslag om Azure te informeren dat verwijderde pagina's niet meer geldig zijn en kunnen worden verwijderd en geld kunnen besparen als u grote bestanden maakt en deze vervolgens verwijdert.

Er zijn twee manieren om ondersteuning voor het verkleinen van de virtuele Linux-machine in te scha kelen. Zoals gebruikelijk, raadpleegt u de distributie voor de aanbevolen benadering:

* Gebruik de `discard` koppelings optie in *bestand/etc/fstab*, bijvoorbeeld:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* In sommige gevallen kan de `discard` optie invloed hebben op de prestaties. U kunt de `fstrim` opdracht ook hand matig uitvoeren vanaf de opdracht regel of deze toevoegen aan uw crontab om regel matig uit te voeren:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Volgende stappen

* Bekijk de aanbevelingen voor het [optimaliseren van uw Linux-machine](/previous-versions/azure/virtual-machines/linux/optimization) om ervoor te zorgen dat uw Linux-VM correct is geconfigureerd.
* Breid uw opslag capaciteit uit door extra schijven toe te voegen en [RAID te configureren](/previous-versions/azure/virtual-machines/linux/configure-raid) voor extra prestaties.
