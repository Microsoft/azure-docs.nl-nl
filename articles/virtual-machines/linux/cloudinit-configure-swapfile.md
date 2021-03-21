---
title: Cloud-init gebruiken voor het configureren van een swap-partitie op een Linux-VM
description: Cloud-init gebruiken voor het configureren van een swap-partitie in een Linux-VM tijdens het maken met de Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: b9f4adc4e1e980db2af4fcc20b3a4492309c89f3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559372"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Cloud-init gebruiken voor het configureren van een swap-partitie op een Linux-VM
Dit artikel laat u zien hoe u [Cloud-init](https://cloudinit.readthedocs.io) kunt gebruiken om de swap-partitie te configureren voor diverse Linux-distributies. De swap-partitie is traditioneel geconfigureerd door de Linux-agent (WALA) op basis van welke distributies een vereiste is.  Dit document bevat een overzicht van het proces voor het bouwen van de wissel partitie op aanvraag tijdens de inrichtings tijd met behulp van Cloud-init.  Zie [Cloud-init Overview](using-cloud-init.md) (Engelstalig) voor meer informatie over hoe Cloud-init standaard werkt in Azure en de ondersteunde Linux-distributies

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Swap Partition maken voor op Ubuntu gebaseerde installatie kopieën
Ubuntu galerie-afbeeldingen maken standaard geen swap-partities. Voor het inschakelen van de configuratie van de wisseling tijdens het inrichten van de VM met behulp van Cloud-init-raadpleegt u het [AzureSwapPartitions-document](https://wiki.ubuntu.com/AzureSwapPartitions) op de Ubuntu-wiki.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Swap Partition maken voor installatie kopieën op basis van Red Hat en CentOS

Maak in uw huidige shell een bestand met de naam *cloud_init_swappart.txt* en plak de volgende configuratie. In dit voor beeld maakt u het bestand in het Cloud Shell niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_swappart.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano** -editor te gebruiken. Zorg ervoor dat het hele Cloud-init-bestand correct wordt gekopieerd, met name de eerste regel.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Voordat u deze installatie kopie implementeert, moet u een resource groep maken met de opdracht [AZ Group Create](/cli/azure/group) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele machine met [AZ VM Create](/cli/azure/vm) en geef het bestand Cloud-init op met de `--custom-data cloud_init_swappart.txt` volgende opties:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Controleer of swap Partition is gemaakt
SSH naar het open bare IP-adres van uw virtuele machine, weer gegeven in de uitvoer van de voor gaande opdracht. Voer uw eigen **publicIpAddress** als volgt in:

```bash
ssh <publicIpAddress>
```

Wanneer u de virtuele machine hebt SSH'ed, controleert u of de wissel partitie is gemaakt

```bash
swapon -s
```

De uitvoer van deze opdracht moet er als volgt uitzien:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Als u een bestaande Azure-installatie kopie hebt waarvoor een swap partitie is geconfigureerd en u de wissel partitie configuratie voor nieuwe installatie kopieën wilt wijzigen, moet u de bestaande swap partitie verwijderen. Zie het document ' Customize images to Provisioning by Cloud-init ' voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
