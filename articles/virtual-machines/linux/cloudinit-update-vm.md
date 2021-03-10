---
title: Cloud-init gebruiken in een virtuele Linux-machine in azure
description: Cloud-init gebruiken voor het bijwerken en installeren van pakketten in een Linux-VM tijdens het maken met de Azure CLI
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: bbd3c30cb00dae25afeea356cefb86a9c860cde5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559338"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Cloud-init gebruiken voor het bijwerken en installeren van pakketten in een virtuele Linux-machine in azure
Dit artikel laat u zien hoe u [Cloud-init](https://cloudinit.readthedocs.io) kunt gebruiken om pakketten op een virtuele Linux-machine (VM) of virtuele-machine schaal sets bij te werken in Azure. Deze Cloud-init-scripts worden uitgevoerd bij de eerste keer opstarten zodra de resources zijn ingericht door Azure. Zie [Cloud-init Overview](using-cloud-init.md) (Engelstalig) voor meer informatie over hoe Cloud-init standaard werkt in Azure en de ondersteunde Linux-distributies

## <a name="update-a-vm-with-cloud-init"></a>Een virtuele machine bijwerken met Cloud-init
Uit veiligheids overwegingen kunt u een virtuele machine configureren om de meest recente updates bij de eerste keer opstarten toe te passen. Als Cloud-init werkt in verschillende Linux-distributies, hoeft u niet op te geven `apt` of `yum` voor de package manager. In plaats daarvan definieert u `package_upgrade` en laat het proces Cloud-init bepalen welk mechanisme het meest geschikt is voor de distributie die in gebruik is. Met deze werk stroom kunt u dezelfde Cloud-init-scripts gebruiken voor distributies.

Als u het upgrade proces in actie wilt zien, maakt u een bestand in uw huidige shell met de naam *cloud_init_upgrade.txt* en plakt u de volgende configuratie. In dit voor beeld maakt u het bestand in het Cloud Shell niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_upgrade.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano** -editor te gebruiken. Zorg ervoor dat het hele Cloud-init-bestand correct wordt gekopieerd, met name de eerste regel.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Voordat u deze installatie kopie implementeert, moet u een resource groep maken met de opdracht [AZ Group Create](/cli/azure/group) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele machine met [AZ VM Create](/cli/azure/vm) en geef het bestand Cloud-init op met de `--custom-data cloud_init_upgrade.txt` volgende opties:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH naar het open bare IP-adres van uw virtuele machine, weer gegeven in de uitvoer van de voor gaande opdracht. Voer uw eigen **publicIpAddress** als volgt in:

```bash
ssh <publicIpAddress>
```

Voer het hulp programma pakket beheer uit en controleer op updates.

```bash
sudo yum update
```

Als Cloud-init is ingeschakeld voor en geïnstalleerde updates bij het opstarten, moeten er geen aanvullende updates worden toegepast.  U ziet het update proces, het aantal gewijzigde pakketten en de installatie van `httpd` door uit te voeren `yum history` en de uitvoer te controleren zoals hieronder.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
