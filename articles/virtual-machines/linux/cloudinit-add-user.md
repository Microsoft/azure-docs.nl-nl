---
title: Cloud-init gebruiken om een gebruiker toe te voegen aan een virtuele Linux-machine in azure
description: Cloud-init gebruiken om een gebruiker toe te voegen aan een Linux-VM tijdens het maken met de Azure CLI
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2c459965f2eb29a469ac90fdeb42107d1dbcf86a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559406"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Cloud-init gebruiken om een gebruiker toe te voegen aan een virtuele Linux-machine in azure
Dit artikel laat u zien hoe u [Cloud-init](https://cloudinit.readthedocs.io) kunt gebruiken om een gebruiker toe te voegen op een virtuele machine (VM) of virtuele-machine schaal sets (VMSS) bij het inrichten van de tijd in Azure. Dit Cloud-init-script wordt uitgevoerd bij de eerste keer opstarten zodra de resources zijn ingericht door Azure. Zie [Cloud-init Overview](using-cloud-init.md)(Engelstalig) voor meer informatie over de manier waarop Cloud-init systeem eigen werkt in Azure en de ondersteunde Linux-distributies.

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Een gebruiker toevoegen aan een virtuele machine met Cloud-init
Een van de eerste taken op een nieuwe virtuele Linux-machine is het toevoegen van een extra gebruiker voor uzelf om het gebruik van de *hoofdmap* te voor komen. SSH-sleutels zijn best practice voor beveiliging en gebruiks gemak. Sleutels worden toegevoegd aan het bestand *~/.ssh/authorized_keys* met dit script voor Cloud-init.

Als u een gebruiker aan een virtuele Linux-machine wilt toevoegen, maakt u een bestand in uw huidige shell met de naam *cloud_init_add_user.txt* en plakt u de volgende configuratie. In dit voor beeld maakt u het bestand in het Cloud Shell niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_add_user.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano** -editor te gebruiken. Zorg ervoor dat het hele Cloud-init-bestand correct wordt gekopieerd, met name de eerste regel.  U moet uw eigen open bare sleutel (zoals de inhoud van *~/.ssh/id_rsa. pub*) opgeven voor de waarde `ssh-authorized-keys:` -deze is inge kort om het voor beeld te vereenvoudigen.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Het #cloud-configuratie bestand bevat de `- default` para meter die is opgenomen. Hiermee wordt de gebruiker toegevoegd aan de bestaande gebruiker die door de beheerder is gemaakt tijdens het inrichten. Als u een gebruiker maakt zonder de `- default` para meter-de automatisch gegenereerde gebruiker met beheerders rechten die is gemaakt door het Azure-platform, wordt overschreven. 

Voordat u deze installatie kopie implementeert, moet u een resource groep maken met de opdracht [AZ Group Create](/cli/azure/group) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele machine met [AZ VM Create](/cli/azure/vm) en geef het bestand Cloud-init op met de `--custom-data cloud_init_add_user.txt` volgende opties:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH naar het open bare IP-adres van uw virtuele machine, weer gegeven in de uitvoer van de voor gaande opdracht. Voer uw eigen **publicIpAddress** als volgt in:

```bash
ssh <publicIpAddress>
```

Als u wilt bevestigen dat de gebruiker is toegevoegd aan de virtuele machine en de opgegeven groepen, bekijkt u de inhoud van het */etc/group* -bestand als volgt:

```bash
cat /etc/group
```

In de volgende voorbeeld uitvoer ziet u dat de gebruiker uit het *cloud_init_add_user.txt* -bestand is toegevoegd aan de virtuele machine en de juiste groep:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
