---
title: Uw Linux-VM optimaliseren voor Azure
description: Meer informatie over de optimalisatie tips om ervoor te zorgen dat u uw virtuele Linux-machine hebt ingesteld voor optimale prestaties in azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 1e3551834e7664d5036fa8a5e0497e5a37f61c2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498503"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Uw Linux-VM optimaliseren voor Azure
Het maken van een virtuele Linux-machine (VM) is eenvoudig vanuit de opdracht regel of vanuit de portal. In deze zelf studie ziet u hoe u ervoor kunt zorgen dat u deze hebt ingesteld om de prestaties van het Microsoft Azure platform te optimaliseren. In dit onderwerp wordt een Ubuntu-Server-VM gebruikt, maar u kunt ook virtuele Linux-machine maken met behulp van [uw eigen installatie kopieën als sjablonen](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Vereisten
In dit onderwerp wordt ervan uitgegaan dat u al een werkend Azure-abonnement ([gratis proef versie](https://azure.microsoft.com/pricing/free-trial/)) hebt, en al een virtuele machine hebt ingericht in uw Azure-abonnement. Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en bent aangemeld bij uw Azure-abonnement met [AZ login](/cli/azure/reference-index) voordat u [een VM maakt](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure-besturingssysteem schijf
Wanneer u een virtuele Linux-machine in azure hebt gemaakt, zijn er twee schijven gekoppeld. **/dev/sda** is de schijf met het besturings systeem, **/dev/sdb** is de tijdelijke schijf.  Gebruik niet de hoofd besturingssysteem schijf (**/dev/sda**) voor iets behalve het besturings systeem als het is geoptimaliseerd voor de snelle opstart tijd van de virtuele machine en biedt geen goede prestaties voor uw workloads. U wilt een of meer schijven aan uw virtuele machine koppelen om permanente en geoptimaliseerde opslag voor uw gegevens te krijgen. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Schijven toevoegen voor grootte-en prestatie doelen
Op basis van de VM-grootte kunt u Maxi maal 16 extra schijven op a-Series, 32-schijven op een D-en 64-schijven op een G-serie machine aansluiten, elk tot 32 TB groot. U voegt extra schijven toe als dat nodig is voor uw ruimte en IOps-vereisten. Elke schijf heeft een prestatie doel van 500 IOps voor standaard opslag en Maxi maal 20.000 IOps per schijf voor Premium Storage.

Als u de hoogste IOps wilt benutten op Premium Storage schijven waarop de cache-instellingen zijn ingesteld op **alleen-lezen** of op **geen**, moet u **belemmeringen** uitschakelen bij het koppelen van het bestands systeem in Linux. U hebt geen obstakels nodig omdat de schrijf bewerkingen naar Premium Storage schijven duurzaam zijn voor deze cache-instellingen.

* Als u **reiserFS** gebruikt, schakelt u belemmeringen uit met behulp van de optie voor het koppelen `barrier=none` (voor het inschakelen van belemmeringen `barrier=flush` )
* Als u **ext3/ext4** gebruikt, schakelt u belemmeringen uit met de optie voor het koppelen `barrier=0` (voor het inschakelen van belemmeringen `barrier=1` )
* Als u **xfs** gebruikt, schakelt u belemmeringen uit met de optie voor het koppelen `nobarrier` (voor het inschakelen van barrières gebruikt u de optie `barrier` )

## <a name="unmanaged-storage-account-considerations"></a>Overwegingen voor onbeheerd opslag account
De standaard actie bij het maken van een virtuele machine met Azure CLI is het gebruik van Azure Managed Disks.  Deze schijven worden verwerkt door het Azure-platform en vereisen geen voor bereiding of locatie om ze op te slaan.  Voor niet-beheerde schijven is een opslag account vereist en er zijn een aantal extra prestatie overwegingen.  Zie voor meer informatie over beheerde schijven [overzicht Azure Managed Disks](../managed-disks-overview.md).  De volgende sectie geeft een overzicht van de prestatie overwegingen alleen wanneer u niet-beheerde schijven gebruikt.  De standaard en Aanbevolen opslag oplossing is om beheerde schijven te gebruiken.

Als u een virtuele machine met niet-beheerde schijven maakt, moet u ervoor zorgen dat u schijven koppelt van opslag accounts die zich in dezelfde regio bevinden als uw virtuele machine om te zorgen voor dicht nabijheid en netwerk latentie te minimaliseren.  Elk standaard opslag account heeft een maximum van 20.000 IOps en een grootte capaciteit van 500 TB.  Deze limiet werkt op ongeveer 40 intensief gebruikte schijven, inclusief de besturingssysteem schijf en alle gegevens schijven die u maakt. Voor Premium Storage accounts is er geen maximale IOps-limiet, maar er is een grootte limiet van 32 TB. 

Bij het verwerken van werk belastingen met hoge IOps en als u standaard opslag voor uw schijven hebt gekozen, moet u mogelijk de schijven in meerdere opslag accounts splitsen om ervoor te zorgen dat u de limiet van 20.000 IOps voor standaard opslag accounts niet bereikt. Uw virtuele machine kan een combi natie van schijven in verschillende opslag accounts en typen opslag account bevatten om uw optimale configuratie te verzorgen.
 

## <a name="your-vm-temporary-drive"></a>Het tijdelijke VM-station
Wanneer u een virtuele machine maakt, biedt Azure standaard een besturingssysteem schijf (**/dev/sda**) en een tijdelijke schijf (**/dev/sdb**).  Alle extra schijven die u toevoegt, worden weer gegeven als **/dev/SDC**, **/dev/sdd**, **/dev/SDE** , enzovoort. Alle gegevens op de tijdelijke schijf (**/dev/sdb**) zijn niet duurzaam en kunnen verloren gaan als voor specifieke gebeurtenissen, zoals het wijzigen van de grootte van de VM, opnieuw implementeren of onderhoud, het opnieuw opstarten van uw VM wordt afgedwongen.  De grootte en het type van de tijdelijke schijf zijn gerelateerd aan de VM-grootte die u tijdens de implementatie hebt gekozen. Alle virtuele machines van de Premium-grootte (DS, G en DS_V2 serie) het tijdelijke station worden ondersteund door een lokale SSD voor extra prestaties van Maxi maal 48k IOps. 

## <a name="linux-swap-partition"></a>Linux-wisselende partitie
Als uw Azure-VM afkomstig is van een Ubuntu-of CoreOS-installatie kopie, kunt u CustomData gebruiken om een Cloud-config naar Cloud-init te verzenden. Als u [een aangepaste Linux-installatie kopie hebt geüpload](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) die gebruikmaakt van Cloud-init, kunt u ook swap-partities configureren met Cloud-init.

U kunt het **/etc/waagent.conf** -bestand niet gebruiken om swap te beheren voor alle installatie kopieën die zijn ingericht en ondersteund door Cloud-init. Zie [Cloud-init gebruiken](using-cloud-init.md)voor een volledige lijst met installatie kopieën. 

De eenvoudigste manier om swap te beheren voor deze installatie kopieën is door de volgende stappen uit te voeren:

1. Maak in de map **/var/lib/Cloud/scripts/per-boot** een bestand met de naam **create_swapfile. sh**:

   **$ sudo touch/var/lib/Cloud/scripts/per-boot/create_swapfile. sh**

1. Voeg de volgende regels toe aan het bestand:

   **$ sudo vi/var/lib/Cloud/scripts/per-boot/create_swapfile. sh**

   ```
   #!/bin/sh
   if [ ! -f '/mnt/swapfile' ]; then
   fallocate --length 2GiB /mnt/swapfile
   chmod 600 /mnt/swapfile
   mkswap /mnt/swapfile
   swapon /mnt/swapfile
   swapon -a ; fi
   ```

   > [!NOTE]
   > U kunt de waarde wijzigen aan de hand van uw behoeften en op basis van de beschik bare ruimte op de bron schijf, die varieert op basis van de grootte van de virtuele machine die wordt gebruikt.

1. Het uitvoer bare bestand maken:

   **$ sudo chmod + x/var/lib/Cloud/scripts/per-boot/create_swapfile. sh**

1. Als u de swapfile wilt maken, moet u het script direct na de laatste stap uitvoeren:

   **$ sudo/var/lib/Cloud/scripts/per-boot/./create_swapfile. sh**

Voor installatie kopieën die geen ondersteuning voor Cloud-init hebben, hebben VM-installatie kopieën die zijn geïmplementeerd vanuit Azure Marketplace een Linux-VM-agent geïntegreerd met het besturings systeem. Met deze agent kan de virtuele machine communiceren met verschillende Azure-Services. Als u een standaard installatie kopie van Azure Marketplace hebt geïmplementeerd, moet u het volgende doen om de instellingen voor Linux-wissel bestand correct te configureren:

Zoek en wijzig twee vermeldingen in het **/etc/waagent.conf** -bestand. Ze bepalen het bestaan van een toegewezen wissel bestand en grootte van het wissel bestand. De para meters die u moet controleren, zijn `ResourceDisk.EnableSwap` en `ResourceDisk.SwapSizeMB` 

Zorg ervoor dat de para meters de volgende instellingen hebben om een geschikt schijf en een gekoppeld wissel bestand in te scha kelen:

* ResourceDisk. EnableSwap = Y
* ResourceDisk. SwapSizeMB = {grootte in MB om te voldoen aan uw behoeften} 

Nadat u de wijziging hebt aangebracht, moet u de waagent opnieuw opstarten of de Linux-VM opnieuw opstarten om deze wijzigingen weer te geven.  U weet dat de wijzigingen zijn geïmplementeerd en dat er een wissel bestand is gemaakt wanneer u de `free` opdracht gebruikt om de beschik bare ruimte weer te geven. In het volgende voor beeld wordt een wissel bestand van 512 MB gemaakt als gevolg van het wijzigen van het bestand **waagent. conf** :

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/O-plannings algoritme voor Premium Storage
Met de 2.6.18 Linux-kernel is het standaard plannings algoritme voor I/O gewijzigd ten opzichte van de deadline van CFQ (volledig billijke Queuing-algoritme). Voor wille keurige I/O-patronen voor toegang is er sprake van een ververwaarlozend verschil in de prestatie verschillen tussen CFQ en deadline.  Voor schijven op basis van SSD waarbij het I/O-patroon van de schijf voornamelijk is opeenvolgend. Als u terugschakelt naar het algoritme NOOPERATION of deadline, kunnen er betere I/O-prestaties worden gerealiseerd.

### <a name="view-the-current-io-scheduler"></a>De huidige I/O-Planner weer geven
Gebruik de volgende opdracht:  

```bash
cat /sys/block/sda/queue/scheduler
```

U ziet de volgende uitvoer, waarmee de huidige planner wordt aangeduid.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Het huidige apparaat (/dev/sda) van een I/O-plannings algoritme wijzigen
Gebruik de volgende opdrachten:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Het Toep assen van deze instelling voor **/dev/sda** is niet nuttig. Stel in op alle gegevens schijven waarbij sequentiële I/O's het I/O-patroon domineren.  

De volgende uitvoer wordt weer gegeven, waarmee wordt aangegeven dat **grub. cfg** is opnieuw opgebouwd en dat de standaard planner is bijgewerkt naar nooperation.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Voor de Red Hat-distributie familie hebt u alleen de volgende opdracht nodig:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

Ubuntu 18,04 met de door Azure afgestemde kernel maakt gebruik van I/O-planners met meerdere wacht rijen. In dat scenario `none` is de juiste selectie in plaats van `noop` . Zie [Ubuntu I/O-planners](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers)voor meer informatie.

## <a name="using-software-raid-to-achieve-higher-iops"></a>Software-RAID gebruiken om hogere I/ops te krijgen
Als uw werk belasting meer IOps vereist dan één schijf kan bieden, moet u een software matige RAID-configuratie van meerdere schijven gebruiken. Omdat Azure schijf tolerantie al uitvoert op de lokale Fabric-laag, bereikt u het hoogste prestatie niveau van een RAID-0-Stripe-configuratie.  U kunt schijven inrichten en maken in de Azure-omgeving en deze koppelen aan uw virtuele Linux-machine voordat u partitioneert, de schijven formatteert en koppelt.  Meer informatie over het configureren van een software-RAID-installatie op uw virtuele Linux-machine in azure vindt u in het document **[Software RAID in Linux configureren](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** .

Als alternatief voor een traditionele RAID-configuratie kunt u er ook voor kiezen om Logical Volume Manager (LVM) te installeren om een aantal fysieke schijven te configureren in één gestripte logische opslag volume. In deze configuratie worden lees-en schrijf bewerkingen gedistribueerd naar meerdere schijven in de volume groep (vergelijkbaar met RAID0). Uit het oogpunt van prestaties is het waarschijnlijk dat u uw logische volumes wilt verwijderen zodat Lees-en schrijf bewerkingen gebruikmaken van alle gekoppelde gegevens schijven.  Meer informatie over het configureren van een gesegmenteerd logisch volume op uw virtuele Linux-machine in azure vindt u in de **[LVM configureren op een virtuele Linux-machine in azure](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** document.

## <a name="next-steps"></a>Volgende stappen
Houd er rekening mee dat, net als bij alle optimalisatie-discussies, tests moeten worden uitgevoerd voor en na elke wijziging om de impact van de wijziging te meten.  Optimalisatie is een stapsgewijs proces dat verschillende resultaten heeft op verschillende computers in uw omgeving.  Wat werkt voor één configuratie, werkt mogelijk niet voor anderen.

Enkele handige koppelingen naar aanvullende bronnen:

* [Gebruikershandleiding voor Azure Linux Agent](../extensions/agent-linux.md)
* [Software-RAID op Linux configureren](/previous-versions/azure/virtual-machines/linux/configure-raid)