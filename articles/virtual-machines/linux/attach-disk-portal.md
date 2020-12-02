---
title: Een gegevens schijf koppelen aan een virtuele Linux-machine
description: Gebruik de portal om een nieuwe of bestaande gegevens schijf te koppelen aan een virtuele Linux-machine.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 8f60c83417e9c614ca30f140e6acbbf08e5643cf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500645"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Een gegevens schijf koppelen aan een virtuele Linux-machine met behulp van de portal 
Dit artikel laat u zien hoe u met de Azure Portal zowel nieuwe als bestaande schijven kunt koppelen aan een virtuele Linux-machine. U kunt ook [een gegevens schijf koppelen aan een virtuele Windows-machine in de Azure Portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Lees de volgende tips voordat u schijven aan uw virtuele machine koppelt:

* De grootte van de virtuele machine bepaalt hoeveel gegevens schijven u kunt bijvoegen. Zie [grootten voor virtuele machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.
* Schijven die zijn gekoppeld aan virtuele machines zijn eigenlijk VHD-bestanden die zijn opgeslagen in Azure. Zie onze [Inleiding tot Managed disks](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.
* Nadat u de schijf hebt gekoppeld, moet u [verbinding maken met de Linux-VM om de nieuwe schijf te koppelen](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>De virtuele machine zoeken
1. Ga naar het [Azure Portal](https://portal.azure.com/) om de virtuele machine te vinden. Zoek en selecteer **virtuele machines**.
2. Kies de virtuele machine in de lijst.
3. Kies op de pagina **virtuele machines** onder **instellingen** de optie **schijven**.


## <a name="attach-a-new-disk"></a>Een nieuwe schijf koppelen

1. Selecteer in het deel venster **schijven** onder **gegevens schijven** de optie **maken en voeg een nieuwe schijf toe**.

1. Voer een naam in voor uw beheerde schijf. Controleer de standaard instellingen en werk indien nodig het **opslag type**, de **grootte (GIB)**, de **versleuteling** en de **host-caching** bij.
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Controleer de schijf instellingen.":::


1. Wanneer u klaar bent, selecteert u **Opslaan** boven aan de pagina om de beheerde schijf te maken en de VM-configuratie bij te werken.


## <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen
1. Selecteer in het deel venster **schijven** onder **gegevens schijven** de optie  **bestaande schijven bijvoegen**.
1. Klik op de vervolg keuzelijst voor de **naam** van de schijf en selecteer een schijf in de lijst met beschik bare beheerde schijven. 

1. Klik op **Opslaan** om de bestaande beheerde schijf te koppelen en de VM-configuratie bij te werken:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Verbinding maken met de Linux-VM om de nieuwe schijf te koppelen
Om uw nieuwe schijf te partitioneren, te Format teren en te koppelen, zodat uw virtuele Linux-machine deze kan gebruiken, SSH in uw VM. Zie voor meer informatie [SSH gebruiken met Linux op Azure](mac-create-ssh-keys.md). In het volgende voor beeld wordt verbinding gemaakt met een virtuele machine met het open bare IP-adres *10.123.123.25* met de gebruikers naam *azureuser*: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>De schijf vinden

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
sdc     3:0:0:0       4G
```

In dit voor beeld is de schijf die u hebt toegevoegd `sdc` . Het is een LUN 0 en is 4 GB.

Voor een complexere voor beeld ziet u hier hoe meerdere gegevens schijven er in de portal uitzien:

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Controleer de schijf instellingen.":::

In de afbeelding ziet u dat er 3 gegevens schijven zijn: 4 GB op LUN 0, 16 MB bij LUN 1 en 32G bij LUN 2.

Dit kan er als volgt uitzien `lsblk` :

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

Vanuit de uitvoer van `lsblk` kunt u zien dat de schijf van 4 GB op LUN 0 is `sdc` , de 16 GB schijf bij LUN 1 `sdd` en de 32G-schijf op LUN 2 `sde` .

### <a name="partition-a-new-disk"></a>Een nieuwe schijf partitioneren

Als u een bestaande schijf gebruikt die gegevens bevat, gaat u door met het koppelen van de schijf. Als u een nieuwe schijf koppelt, moet u de schijf partitioneren.

Het `parted` hulp programma kan worden gebruikt voor het partitioneren en Format teren van een gegevens schijf.

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

Maak een map om het bestands systeem met te koppelen `mkdir` . In het volgende voor beeld wordt een directory gemaakt op `/datadrive` :

```bash
sudo mkdir /datadrive
```

Gebruik `mount` om het bestands systeem vervolgens te koppelen. In het volgende voor beeld wordt de partitie */dev/sdc1* gekoppeld aan het `/datadrive` koppel punt:

```bash
sudo mount /dev/sdc1 /datadrive
```

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

We hebben de nano editor gebruikt, dus wanneer u klaar bent met het bewerken van het bestand, gebruikt `Ctrl+O` u om het bestand te schrijven en `Ctrl+X` de editor af te sluiten.

> [!NOTE]
> Later het verwijderen van een gegevens schijf zonder het bewerken van fstab kan ertoe leiden dat de virtuele machine niet kan worden opgestart. De meeste distributies bieden de opties *nobootwait* *en/* of fstab. Met deze opties kan een systeem worden opgestart zelfs als de schijf niet kan worden gekoppeld tijdens het opstarten. Raadpleeg de documentatie van uw distributie voor meer informatie over deze para meters.
> 
> De *niet-werkende* optie zorgt ervoor dat de VM wordt gestart, zelfs als het bestands systeem beschadigd is of wanneer de schijf niet aanwezig is tijdens het opstarten. Zonder deze optie kunt u zich voordoen als beschreven in [kan niet SSH-naar-Linux-VM wegens FSTAB-fouten](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)


## <a name="verify-the-disk"></a>Controleer de schijf

U kunt nu `lsblk` opnieuw gebruiken om de schijf en de koppel punt weer te geven.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

De uitvoer ziet er ongeveer uit zoals in dit voorbeeld:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

U kunt zien dat `sdc` nu is gekoppeld op `/datadrive` .

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

## <a name="next-steps"></a>Volgende stappen
U kunt ook [een gegevens schijf koppelen](add-disk.md) met behulp van de Azure cli.