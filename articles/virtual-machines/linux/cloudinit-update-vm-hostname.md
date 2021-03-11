---
title: Cloud-init gebruiken om de hostnaam in te stellen voor een virtuele Linux-machine
description: Cloud-init gebruiken voor het aanpassen van een virtuele Linux-machine tijdens het maken met de Azure CLI
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 5c3195fd338d17e2dae3ff2aadfb90790519715b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559389"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Cloud-init gebruiken om de hostnaam in te stellen voor een virtuele Linux-machine in azure
Dit artikel laat u zien hoe u [Cloud-init](https://cloudinit.readthedocs.io) kunt gebruiken voor het configureren van een specifieke hostnaam op een virtuele machine (VM) of virtuele-machine schaal sets (VMSS) bij het inrichten van de tijd in Azure. Deze Cloud-init-scripts worden uitgevoerd bij de eerste keer opstarten zodra de resources zijn ingericht door Azure. Zie [Cloud-init Overview](using-cloud-init.md) (Engelstalig) voor meer informatie over hoe Cloud-init standaard werkt in Azure en de ondersteunde Linux-distributies

## <a name="set-the-hostname-with-cloud-init"></a>De hostnaam instellen met Cloud-init
De hostnaam is standaard hetzelfde als de VM-naam wanneer u een nieuwe virtuele machine in azure maakt.  Als u een Cloud-init-script wilt uitvoeren om deze standaard-hostnaam te wijzigen wanneer u een virtuele machine in azure maakt met [AZ VM Create](/cli/azure/vm), geeft u het bestand Cloud-init op met de `--custom-data` Switch.  

Als u het upgrade proces in actie wilt zien, maakt u een bestand in uw huidige shell met de naam *cloud_init_hostname.txt* en plakt u de volgende configuratie. In dit voor beeld maakt u het bestand in het Cloud Shell niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_hostname.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano** -editor te gebruiken. Zorg ervoor dat het hele Cloud-init-bestand correct wordt gekopieerd, met name de eerste regel.  

```yaml
#cloud-config
hostname: myhostname
```

Voordat u deze installatie kopie implementeert, moet u een resource groep maken met de opdracht [AZ Group Create](/cli/azure/group) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele machine met [AZ VM Create](/cli/azure/vm) en geef het bestand Cloud-init op met de `--custom-data cloud_init_hostname.txt` volgende opties:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Als de Azure CLI eenmaal is gemaakt, wordt de informatie over de virtuele machine weer gegeven. Gebruik de `publicIpAddress` om SSH te gebruiken voor uw VM. Voer als volgt uw eigen adres in:

```bash
ssh <publicIpAddress>
```

Als u de naam van de virtuele machine wilt zien, gebruikt u de `hostname` opdracht als volgt:

```bash
hostname
```

De virtuele machine moet de hostnaam rapporteren als die waarde die is ingesteld in het Cloud-init-bestand, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```bash
myhostname
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
