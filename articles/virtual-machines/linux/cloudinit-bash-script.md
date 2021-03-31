---
title: Cloud-init gebruiken om een bash-script uit te voeren in een virtuele Linux-machine in azure
description: Cloud-init gebruiken om een bash-script uit te voeren in een Linux-VM tijdens het maken met de Azure CLI
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1dabd7db85d4222ca2e9f8387b716af291ee6a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559423"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Cloud-init gebruiken om een bash-script uit te voeren in een virtuele Linux-machine in azure
Dit artikel laat u zien hoe u [Cloud-init](https://cloudinit.readthedocs.io) kunt gebruiken om een bestaand bash-script uit te voeren op een virtuele Linux-machine (VM) of virtuele-machine schaal sets (VMSS) bij het inrichten van de tijd in Azure. Deze Cloud-init-scripts worden uitgevoerd bij de eerste keer opstarten zodra de resources zijn ingericht door Azure. Zie [Cloud-init Overview](using-cloud-init.md) (Engelstalig) voor meer informatie over hoe Cloud-init standaard werkt in Azure en de ondersteunde Linux-distributies

## <a name="run-a-bash-script-with-cloud-init"></a>Een bash-script uitvoeren met Cloud-init
Met Cloud-init hoeft u uw bestaande scripts niet te converteren naar een Cloud-config. Cloud-init accepteert meerdere invoer typen, een van de bash-scripts.

Als u de Azure-extensie voor aangepast script voor Linux hebt gebruikt om uw scripts uit te voeren, kunt u deze migreren voor het gebruik van Cloud-init. Azure-extensies hebben echter geïntegreerde rapportage om waarschuwingen te ontvangen voor script fouten, een Cloud-init-installatie kopie kan niet worden geïmplementeerd als het script is mislukt.

Als u deze functionaliteit in actie wilt zien, maakt u een eenvoudig bash-script om te testen. Net als het Cloud-init- `#cloud-config` bestand moet dit script lokaal zijn waar u de AzureCLI-opdrachten uitvoert om uw virtuele machine in te richten.  In dit voor beeld maakt u het bestand in het Cloud Shell niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor simple_bash.sh` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano** -editor te gebruiken. Zorg ervoor dat het hele Cloud-init-bestand correct wordt gekopieerd, met name de eerste regel.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Voordat u deze installatie kopie implementeert, moet u een resource groep maken met de opdracht [AZ Group Create](/cli/azure/group) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele machine met [AZ VM Create](/cli/azure/vm) en geef het bash-script bestand op met de `--custom-data simple_bash.sh` volgende opties:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Controleren of het bash-script is uitgevoerd
SSH naar het open bare IP-adres van uw virtuele machine, weer gegeven in de uitvoer van de voor gaande opdracht. Voer uw eigen **publicIpAddress** als volgt in:

```bash
ssh <publicIpAddress>
```

Ga naar de map **map/tmp** en controleer of myScript.txt bestand bestaat en of de juiste tekst erin is opgeslagen.  Als dat niet het geval is, kunt u de **/var/log/Cloud-init.log** controleren voor meer informatie.  Zoek naar de volgende vermelding:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
