---
title: Zelfstudie - Schijven voor schaalsets maken en gebruiken met Azure CLI
description: Leer hoe u met Azure CLI beheerde schijven kunt maken en gebruiken met schaalsets voor virtuele machines, waaronder het toevoegen, voorbereiden, opvragen en loskoppelen van schijven.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: adaa7d1c2cf4a78a680ef4fbbec06975ceda812b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433498"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Zelfstudie: Schijven maken en gebruiken met schaalset voor virtuele machines met Azure CLI
Schaalsets voor virtuele machines maken gebruik van schijven voor het opslaan van het besturingssysteem, toepassingen en gegevens van het VM-exemplaar. Bij het maken en beheren van een schaalset is het belangrijk dat u een schijfgrootte en configuratie kiest die geschikt zijn voor de verwachte werkbelasting. Deze zelfstudie bevat informatie over het maken en beheren van VM-schijven. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Gegevensschijven koppelen en voorbereiden

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.29 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="default-azure-disks"></a>Standaard Azure-schijven
Op het moment dat u een schaalset maakt, worden er automatisch twee schijven aan elk VM-exemplaar gekoppeld.

**Besturingssysteemschijf**: besturingssysteemschijven kunnen tot 2 TB groot zijn, en hosten het besturingssysteem van het VM-exemplaar. De besturingssysteemschijf is standaard gelabeld met */dev/sda*. De schijfcacheconfiguratie van de besturingssysteemschijf is geoptimaliseerd voor besturingssysteemprestaties. Vanwege deze configuratie kan de besturingssysteemschijf **beter geen** toepassingen of gegevens hosten. Gebruik voor toepassingen en gegevens gegevensschijven, die verderop in dit artikel worden beschreven.

**Tijdelijke schijf**: tijdelijke schijven gebruiken een SSD-schijf die zich op dezelfde Azure-host bevindt als het VM-exemplaar. Deze schijven leveren zeer goede prestaties en kunnen worden gebruikt voor bewerkingen zoals tijdelijke gegevensverwerking. Als het VM-exemplaar echter wordt verplaatst naar een nieuwe host, worden gegevens die zijn opgeslagen op een tijdelijke schijf verwijderd. De grootte van de tijdelijke schijf wordt bepaald door de grootte van het VM-exemplaar. Tijdelijke schijven zijn gelabeld als */dev/sdb* en hebben een koppelpunt van */mnt*.

### <a name="temporary-disk-sizes"></a>Groottes van tijdelijke schijven
| Type | Veelgebruikte grootten | Maximumgrootte van tijdelijke schijf (GiB) |
|----|----|----|
| [Algemeen doel](../virtual-machines/sizes-general.md) | A-, B- en D-serie | 1600 |
| [Geoptimaliseerde rekenkracht](../virtual-machines/sizes-compute.md) | F-serie | 576 |
| [Geoptimaliseerd geheugen](../virtual-machines/sizes-memory.md) | D-, E-, G- en M-serie | 6144 |
| [Geoptimaliseerde opslag](../virtual-machines/sizes-storage.md) | L-serie | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N-serie | 1440 |
| [Hoge prestaties](../virtual-machines/sizes-hpc.md) | A- en H-serie | 2000 |


## <a name="azure-data-disks"></a>Azure-gegevensschijven
Er kunnen extra gegevensschijven worden toegevoegd voor het installeren van toepassingen en het opslaan van gegevens. Gegevensschijven moeten worden gebruikt in situaties waarin duurzame en responsieve gegevensopslag gewenst is. Elke gegevensschijf heeft een maximale capaciteit van 4 TB. De grootte van het VM-exemplaar bepaalt hoeveel gegevensschijven er kunnen worden gekoppeld. Voor elke VM-vCPU kunnen twee gegevensschijven worden aangesloten op een absoluut maximum van 64 schijven per virtuele machine.

## <a name="vm-disk-types"></a>Typen VM-schijven
Azure biedt twee typen schijven.

### <a name="standard-disk"></a>Standard-schijf
Standard Storage wordt ondersteund door HDD's en biedt voordelige en hoogwaardige opslag. Standard-schijven zijn ideaal voor een kostenefficiënte werkbelasting voor ontwikkelen en testen.

### <a name="premium-disk"></a>Premium-schijf
Premium-schijven worden ondersteund door hoogwaardige schijven met een lage latentie op basis van SSD. Deze schijven worden aanbevolen voor VM's waarop productieworkloads worden uitgevoerd. Premium Storage ondersteunt virtuele machines uit de DS-serie, DSv2-serie GS-serie en FS-serie. Wanneer u een schijfgrootte selecteert, wordt de waarde omhoog afgerond naar het volgende type. Als de schijfgrootte bijvoorbeeld kleiner dan 128 GB is, is het schijftype P10. Als de schijfgrootte tussen 129 en 512 GB is, is het type een P20. Alles groter dan 512 GB is een P30.

### <a name="premium-disk-performance"></a>Prestaties Premium-schijf
|Schijftype voor Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Schijfgrootte (afronden) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024 GB (1 TB) | 2\.048 GB (2 TB) | 4\.095 GB (4 TB) |
| Max. aantal IOP's per schijf | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Doorvoer per schijf | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

In de bovenstaande tabel wordt het max. IOP's per schijf aangegeven, maar er kan een hoger prestatieniveau worden bereikt door striping van meerdere gegevensschijven. Een virtuele machine van het type Standard_GS5 kan bijvoorbeeld maximaal 80.000 IOPS bereiken. Zie [Linux VM-grootten](../virtual-machines/sizes.md) voor gedetailleerde informatie over het maximum aantal IOP's per VM.


## <a name="create-and-attach-disks"></a>Schijven maken en koppelen
U kunt schijven maken en koppelen wanneer u een schaalset maakt, maar ook voor een bestaande schaalset.

Vanaf API-versie `2019-07-01` kunt u de grootte van de besturingssysteemschijf instellen in een schaalset voor virtuele machines met de eigenschap [storageProfile.osDisk.diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk). Na het inrichten moet u de schijf wellicht uitbreiden of partitioneren om gebruik te maken van de volledige ruimte. Meer informatie over [de schijf uitbreiden vindt u hier](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Schijven koppelen bij het maken van een schaalset
Maak eerst een resourcegroep met de opdracht [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak een schaalset voor virtuele machines met de opdracht [az vmss create](/cli/azure/vmss). In het volgende voorbeeld wordt een schaalset gemaakt met de naam *myScaleSet*, en worden SSH-sleutels gegenereerd als deze nog niet bestaan. Er worden twee schijven gemaakt met de parameter `--data-disk-sizes-gb`. De eerste schijf is *64* GB en de tweede schijf *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

Het duurt enkele minuten om alle schaalsetresources en VM-exemplaren te maken en te configureren.

### <a name="attach-a-disk-to-existing-scale-set"></a>Een schijf koppelen aan een bestaande schaalset
U kunt ook schijven koppelen aan een bestaande schaalset. Gebruik de schaalset die in de vorige stap is gemaakt om een andere schijf toe te voegen met [az vmss disk attach](/cli/azure/vmss/disk). In het volgende voorbeeld wordt een extra schijf van *128* GB gekoppeld:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>De gegevensschijven voorbereiden
De schijven die worden gemaakt en die worden gekoppeld aan de VM-exemplaren in uw schaalset zijn RAW-schijven. U moet dit type schijven voorbereiden voordat u ze kunt gebruiken met uw gegevens en toepassingen. Dit doet u door een partitie en een bestandssysteem te maken en de schijven vervolgens te koppelen.

Als u dit proces wilt automatiseren voor meerdere VM-exemplaren in een schaalset, kunt u de aangepaste scriptextensie van Azure gebruiken. Met deze extensie kunt u scripts lokaal uitvoeren op elk VM-exemplaar, bijvoorbeeld om gekoppelde gegevensschijven voor te bereiden. Zie voor meer informatie het [overzicht van de aangepaste scriptextensie](../virtual-machines/extensions/custom-script-linux.md).

In het volgende voorbeeld wordt met [az vmss extension set](/cli/azure/vmss/extension) op elk VM-exemplaar een voorbeeldscript uit een GitHub-repository uitgevoerd waarmee alle gekoppelde RAW-gegevensschijven worden voorbereid:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Als u wilt controleren of de schijven goed zijn voorbereid, gaat u met SSH naar een van de VM-exemplaren. Gebruik [az vmss list-instance-connection-info](/cli/azure/vmss) om de verbindingsinformatie voor uw schaalsets op te vragen:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Gebruik uw eigen openbare IP-adres en poortnummer om verbinding te maken met het eerste VM-exemplaar, zoals wordt weergegeven in het volgende voorbeeld:

```console
ssh azureuser@52.226.67.166 -p 50001
```

Controleer de partities in het VM-exemplaar als volgt:

```bash
sudo fdisk -l
```

In de volgende voorbeelduitvoer ziet u dat er drie gegevensschijven zijn gekoppeld aan het VM-exemplaar - */dev/sdc*, */dev/sdd* en */dev/sde*. Deze schijven hebben allemaal één partitie die alle beschikbare ruimte benut:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Onderzoek de bestandssystemen en koppelpunten op het VM-exemplaar als volgt:

```bash
sudo df -h
```

De volgende voorbeelduitvoer laat zien dat de bestandssystemen van de drie schijven correct zijn gekoppeld onder */datadisks*:

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

De schijven in elk VM-exemplaar in uw schaalset worden automatisch op dezelfde manier voorbereid. Als de schaalset wordt opgeschaald, worden de vereiste gegevensschijven gekoppeld aan de nieuwe VM-exemplaren. De aangepaste scriptextensie wordt ook uitgevoerd om de schijven automatisch voor te bereiden.

Verbreek de SSH-verbinding met uw VM-exemplaar:

```bash
exit
```


## <a name="list-attached-disks"></a>Gekoppelde schijven opvragen
Als u informatie wilt opvragen over schijven die zijn gekoppeld aan een schaalset, gebruikt u [az vmss show](/cli/azure/vmss) en voert u een query uit op *virtualMachineProfile.storageProfile.dataDisks*:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

U ziet vervolgens gegevens van de grootte van de schijf, de opslaglaag en het LUN (Logical Unit Number). In de volgende voorbeelduitvoer ziet u gegevens van de drie gegevensschijven die aan het VM-exemplaar zijn gekoppeld:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Een schijf loskoppelen
Wanneer u een bepaalde schijf niet meer nodig hebt, kunt u deze loskoppelen van de schaalset. De schijf wordt dan verwijderd uit alle VM-exemplaren in de schaalset. Als u een schijf wilt loskoppelen van een schaalset, gebruikt u [az vmss disk detach](/cli/azure/vmss/disk) en geeft u het LUN van de schijf op. De LUN's worden weergegeven in de uitvoer van [az vmss show](/cli/azure/vmss) in de vorige sectie. In het volgende voorbeeld wordt het LUN *2* losgekoppeld van de schaalset:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en schijven wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [az group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u schijven maakt en gebruikt met schaalsets met Azure CLI:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Gegevensschijven koppelen en voorbereiden

Ga naar de volgende zelfstudie voor informatie over het gebruik van een aangepaste installatiekopie voor de VM-exemplaren in uw schaalset.

> [!div class="nextstepaction"]
> [Een aangepaste installatiekopie gebruiken voor VM-exemplaren in een schaalset](tutorial-use-custom-image-cli.md)