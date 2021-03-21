---
title: 'Zelfstudie: Azure-schijven beheren met de Azure CLI'
description: In deze zelfstudie leert u hoe u Freeman Azure CLI gebruikt voor het maken en beheren van Azure-schijven voor virtuele machines
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 202125dfa1cd2760695672fb948fb47bfc3ca0c9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564642"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Zelfstudie: Azure-schijven beheren met de Azure CLI

Virtuele machines (VM's) in Azure gebruiken schijven om het besturingssysteem, toepassingen en gegevens op te slaan. Wanneer u een VM maakt, is het van belang dat u een schijfgrootte en configuratie kiest die geschikt zijn voor de verwachte werkbelasting. In deze zelfstudie ziet u hoe u VM-schijven implementeert en beheert. U krijgt informatie over:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Het koppelen en voorbereiden van gegevensschijven
> * Momentopnamen van schijven


## <a name="default-azure-disks"></a>Standaard Azure-schijven

Wanneer een virtuele Azure-machine wordt gemaakt, worden automatisch twee schijven aan de virtuele machine gekoppeld.

**Besturingssysteemschijf**: besturingssysteemschijven kunnen tot 2 TB groot zijn, en huisvesten het besturingssysteem van de VM’s. De besturingssysteemschijf is standaard gelabeld met */dev/sda*. De schijfcacheconfiguratie van de besturingssysteemschijf is geoptimaliseerd voor besturingssysteemprestaties. Vanwege deze configuratie kan de besturingssysteemschijf **beter niet** worden gebruikt voor toepassingen of gegevens. Gebruik voor toepassingen en gegevens gegevensschijven, die verderop in deze zelfstudie worden beschreven.

**Tijdelijke schijf**: tijdelijke schijven gebruiken een SSD-schijf die zich op dezelfde Azure-host bevindt als de virtuele machine. Tijdelijke schijven leveren zeer goede prestaties en kunnen worden gebruikt voor bewerkingen als tijdelijke gegevensverwerking. Als de virtuele machine wordt verplaatst naar een nieuwe host, worden gegevens die zijn opgeslagen op een tijdelijke schijf echter verwijderd. De grootte van de tijdelijke schijf wordt bepaald door de VM-grootte. Tijdelijke schijven zijn gelabeld als */dev/sdb* en hebben een koppelpunt van */mnt*.

## <a name="azure-data-disks"></a>Azure-gegevensschijven

Voor de installatie van toepassingen en de opslag van gegevens kunnen extra gegevensschijven worden toegevoegd. Gegevensschijven moeten worden gebruikt in situaties waarin duurzame en responsieve gegevensopslag gewenst is. De grootte van de virtuele machine bepaalt hoeveel gegevensschijven aan een virtuele machine kunnen worden gekoppeld.

## <a name="vm-disk-types"></a>Typen VM-schijven

Azure biedt twee typen schijven.

**Standard-schijven** - ondersteund door HDD's en bieden voordelige en hoogwaardige opslag. Standard-schijven zijn ideaal voor een kostenefficiënte werkbelasting voor ontwikkelen en testen.

**Premium-schijven** - ondersteund door hoogwaardige schijven met een lage latentie op basis van SSD. Ideaal voor virtuele machines met een productiewerkbelasting. VM-grootten met een  **S** in de [groottenaam](../vm-naming-conventions.md), bieden doorgaans ondersteuning voor Premium Storage. VM's uit de DS-serie, DSv2-serie, GS-serie en FS-serie ondersteunen premium opslag. Wanneer u een schijfgrootte selecteert, wordt de waarde omhoog afgerond naar het volgende type. Als de schijfgrootte bijvoorbeeld groter is dan 64 GB maar kleiner dan 128 GB, is het schijftype P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Wanneer u een Premium Storage-schijf inricht, bent u, in tegenstelling tot standaard opslag, verzekerd van de capaciteit, IOPS en doorvoer van die schijf. Als u bijvoorbeeld een P50-schijf maakt, richt Azure een schijf in met een opslagcapaciteit van 4.095 GB, 7.500 IOPS en een doorvoer van 250 MB/s. Uw toepassing kan alle of een deel van de capaciteit en prestaties gebruiken. Premium SSD-schijven zijn zodanig ontworpen dat er in de voorgaande tabel 99,9% van de tijd minder dan één milliseconde latentie en doel-IOPS en -doorvoer wordt beschreven.

In de bovenstaande tabel wordt het max. IOP's per schijf aangegeven, maar er kan een hoger prestatieniveau worden bereikt door striping van meerdere gegevensschijven. Er kunnen bijvoorbeeld 64 gegevensschijven worden gekoppeld aan Standard_GS5 VM. Als voor elk van deze schijven een P30-grootte wordt gebruikt, kan een maximum van 80.000 IOP's worden behaald. Zie [VM-typen en -grootten](../sizes.md) voor gedetailleerde informatie over het maximum aantal IOP's per VM.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-and-attach-disks"></a>Schijven maken en koppelen

Gegevensschijven kunnen worden gemaakt en aan een VM worden gekoppeld tijdens het maken of aan een bestaande virtuele machine worden gekoppeld.

### <a name="attach-disk-at-vm-creation"></a>Schijf koppelen tijdens het maken van de virtuele machine

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Maak een VM met de opdracht [az vm create](/cli/azure/vm#az-vm-create). In het volgende voorbeeld wordt de VM *myVM* gemaakt, wordt het gebruikersaccount *azureuser* toegevoegd en worden SSH-sleutels gemaakt als deze nog niet bestaan. Het argument `--datadisk-sizes-gb` wordt gebruikt om op te geven dat een extra schijf moet worden gemaakt en gekoppeld aan de virtuele machine. Als u meer dan één schijf wilt maken en koppelen, gebruikt u een door spaties gescheiden lijst met waarden voor schijfgroottes. In het volgende voorbeeld wordt een virtuele machine gemaakt met twee gegevensschijven, beide van 128 GB. Omdat de schijfgrootte 128 GB is, zijn deze schijven beide geconfigureerd als P10, dat maximaal 500 IOP's per schijf biedt.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Een schijf koppelen aan een bestaande virtuele machine

Als u een nieuwe schijf wilt maken en koppelen aan een bestaande virtuele machine wilt maken, gebruikt u de opdracht [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). In het volgende voorbeeld wordt een Premium-schijf gemaakt met een grootte van 128 GB en gekoppeld aan de virtuele machine die u in de vorige stap hebt gemaakt.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Gegevensschijven voorbereiden

Wanneer een schijf is gekoppeld aan de virtuele machine, moet het besturingssysteem worden geconfigureerd voor gebruik van de schijf. Het volgende voorbeeld laat zien hoe u een schijf handmatig configureert. Dit proces kan ook worden geautomatiseerd met behulp van cloud-init, wat wordt beschreven in een [latere zelfstudie](./tutorial-automate-vm-deployment.md).


Maak een SSH-verbinding met de virtuele machine. Vervang het voorbeeld van een IP-adres door het openbare IP-adres van de virtuele machine.

```console
ssh 10.101.10.10
```

Partitioneer de schijf met `parted`.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Schrijf een bestandssysteem naar de partitie met behulp van de opdracht `mkfs`. Gebruik `partprobe` om het besturingssysteem bekend te maken met de wijziging.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Koppel de nieuwe schijf, zodat deze toegankelijk is in het besturingssysteem.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

De schijf kan nu worden geopend via het `/datadrive`-koppelpunt. Dit kan worden gecontroleerd door de opdracht `df -h` uit te voeren.

```bash
df -h | grep -i "sd"
```

De uitvoer geeft het nieuwe station weer dat is gekoppeld aan `/datadrive`.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Om ervoor te zorgen dat het station na het opnieuw opstarten opnieuw wordt gekoppeld, moet het worden toegevoegd aan het bestand */etc/fstab*. Haal hiervoor de UUID van de schijf op met het hulpprogramma `blkid`.

```bash
sudo -i blkid
```

De uitvoer geeft de UUID van het station weer, in dit geval `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> Het onjuist bewerken van het bestand **/etc/fstab** kan leiden tot een systeem dat niet kan worden opgestart. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. U wordt ook aangeraden een back-up van het bestand /etc/fstab te maken voordat u het bewerkt.

Open het bestand `/etc/fstab` als volgt in een teksteditor:

```bash
sudo nano /etc/fstab
```

Voeg een regel die lijkt op de volgende aan het bestand */etc/fstab* toe en vervang de UUID door uw eigen waarde.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Wanneer u klaar bent met het bewerken van het bestand, gebruikt u `Ctrl+O` om het bestand te schrijven en `Ctrl+X` om de editor af te sluiten.

Nu de schijf is geconfigureerd, sluit u de SSH-sessie.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Een momentopname van een gegevensschijf maken

Wanneer u een momentopname van de schijf maakt, maakt Azure een alleen-lezen en tijdgebonden kopie van de schijf. Azure VM-momentopnamen zijn handig om snel de status van een virtuele machine op te slaan voordat u configuratiewijzigingen aanbrengt. Bij een probleem of fout kan de virtuele machine worden hersteld met behulp van een momentopname. Wanneer een virtuele machine meer dan één schijf heeft, wordt van elke schijf een momentopname gemaakt, onafhankelijk van de andere schijven. Overweeg de virtuele machine te stoppen voordat u momentopnamen van de schijf maakt, zodat u toepassingsconsistente back-ups kunt maken. U kunt ook de [Azure Backup-service](../../backup/index.yml) gebruiken, waarmee u automatische back-ups kunt maken terwijl de virtuele machine wordt uitgevoerd.

### <a name="create-snapshot"></a>Momentopname maken

Voordat u een momentopname maakt, hebt u de id of naam van de schijf nodig. Gebruik [az vm show](/cli/azure/vm#az-vm-show) om de schijf-id aan te geven. In dit voorbeeld wordt de schijf-id opgeslagen in een variabele, zodat deze in een later stadium kan worden gebruikt.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Nu u de id hebt, gebruikt u [az snapshot create](/cli/azure/snapshot#az-snapshot-create) om een momentopname van de schijf te maken.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Schijf maken op basis van een momentopname

Deze momentopname kan vervolgens met [az disk create](/cli/azure/disk#az-disk-create) worden geconverteerd naar een schijf die kan worden gebruikt om de virtuele machine opnieuw te maken.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Virtuele machine herstellen op basis van een momentopname

Voor het herstellen van de virtuele machine dient u de bestaande virtuele machine te verwijderen met [az vm delete](/cli/azure/vm#az-vm-delete).

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Maak een nieuwe virtuele machine op basis van de momentopname van de schijf.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Gegevensschijf opnieuw koppelen

Alle gegevensschijven moeten opnieuw worden gekoppeld aan de virtuele machine.

Zoek de naam van de gegevensschijf met de opdracht [az disk list](/cli/azure/disk#az-disk-list). In dit voorbeeld wordt de naam van de schijf in een variabele met de naam `datadisk` geplaatst, die in de volgende stap wordt gebruikt.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Gebruik de opdracht [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) om de schijf te koppelen.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meer geleerd over onderwerpen over VM-schijven, zoals:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Het koppelen en voorbereiden van gegevensschijven
> * Momentopnamen van schijven

In de volgende zelfstudie leert u hoe u de configuratie van virtuele machines automatiseert.

> [!div class="nextstepaction"]
> [VM-configuratie automatiseren](./tutorial-automate-vm-deployment.md)
