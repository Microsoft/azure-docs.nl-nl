---
title: Een SSH-sleutel paar maken en gebruiken voor virtuele Linux-machines in azure
description: Het maken en gebruiken van een open bare SSH-sleutel paar voor virtuele Linux-machines in azure om de beveiliging van het verificatie proces te verbeteren.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 7a6971bce2ba4cb3e18455aad34e2d10b73dc066
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203419"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snelle stappen: maken en gebruiken van een openbaar persoonlijk sleutel paar met SSH voor Linux-Vm's in azure

Met een SSH-sleutel paar (Secure Shell) kunt u virtuele machines (Vm's) in azure maken die gebruikmaken van SSH-sleutels voor verificatie. In dit artikel wordt beschreven hoe u snel een bestand met open bare-sleutel paar voor virtuele machines met SSH-code kunt genereren en gebruiken voor Linux-Vm's. U kunt deze stappen volt ooien met de Azure Cloud Shell, een macOS-of Linux-host. 

> [!NOTE]
> Vm's die zijn gemaakt met SSH-sleutels zijn standaard geconfigureerd met uitgeschakelde wacht woorden, waardoor het lastiger wordt om aanvallen te voor komen. 

Zie voor meer achtergrond informatie en voor beelden [gedetailleerde stappen voor het maken van SSH-sleutel paren](create-ssh-keys-detailed.md).

Zie [SSH-sleutels gebruiken met Windows op Azure](ssh-from-windows.md)voor meer manieren om SSH-sleutels te genereren en gebruiken op een Windows-computer.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Gebruik de opdracht `ssh-keygen` om openbare en privé-SSH-sleutelbestanden te genereren. Deze bestanden worden standaard gemaakt in de map ~/.ssh. U kunt een andere locatie opgeven en een optioneel wacht woord (*wachtwoordzin*) voor toegang tot het bestand met de persoonlijke sleutel. Als er op de opgegeven locatie een SSH-sleutelpaar met dezelfde naam bestaat, worden deze bestanden overschreven.

Met de volgende opdracht wordt een SSH-sleutel paar gemaakt met behulp van RSA-versleuteling en een bitlengte van 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Als u de [Azure cli](/cli/azure) gebruikt om uw virtuele machine te maken met de opdracht [AZ VM Create](/cli/azure/vm#az-vm-create) , kunt u optioneel open bare en persoonlijke SSH-sleutel bestanden genereren met behulp van de `--generate-ssh-keys` optie. De sleutel bestanden worden opgeslagen in de map ~/.SSH, tenzij anders is aangegeven met de `--ssh-dest-key-path` optie. Als er al een SSH-sleutel paar bestaat en de  `--generate-ssh-keys` optie wordt gebruikt, wordt er geen nieuw sleutel paar gegenereerd maar wordt in plaats daarvan het bestaande sleutel paar gebruikt. Vervang *VMname* en *RGname* door uw eigen waarden in de volgende opdracht:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Een open bare SSH-sleutel opgeven bij het implementeren van een virtuele machine

Als u een virtuele Linux-machine wilt maken die gebruikmaakt van SSH-sleutels voor verificatie, geeft u uw open bare SSH-sleutel op bij het maken van de VM met behulp van de Azure Portal, Azure CLI, Azure Resource Manager sjablonen of andere methoden:

* [Een virtuele Linux-machine maken met Azure Portal](quick-create-portal.md)
* [Een virtuele Linux-machine maken met de Azure CLI](quick-create-cli.md)
* [Een virtuele Linux-machine maken met een Azure-sjabloon](create-ssh-secured-vm-from-template.md)

Als u niet bekend bent met de indeling van een open bare SSH-sleutel, kunt u uw open bare sleutel weer geven met de volgende `cat` opdracht, `~/.ssh/id_rsa.pub` indien nodig vervangen door het pad en de bestands naam van uw eigen open bare-sleutel bestand:

```bash
cat ~/.ssh/id_rsa.pub
```

Een typische waarde voor een open bare sleutel ziet er als volgt uit:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Als u de inhoud van het open bare-sleutel bestand kopieert en plakt voor gebruik in de Azure Portal of een resource manager-sjabloon, moet u ervoor zorgen dat u geen afsluitende spaties kopieert. Als u een open bare sleutel in macOS wilt kopiëren, kunt u het open bare-sleutel bestand door sluizen naar `pbcopy` . Op dezelfde manier kunt u in Linux het open bare-sleutel bestand door sluizen naar Program ma's zoals `xclip` .

De open bare sleutel die u op uw virtuele Linux-machine in azure plaatst, wordt standaard opgeslagen in ~/.ssh/id_rsa. pub, tenzij u een andere locatie hebt opgegeven tijdens het maken van het sleutel paar. Als u de [Azure CLI 2,0](/cli/azure) wilt gebruiken om uw virtuele machine te maken met een bestaande open bare sleutel, geeft u de waarde en optioneel de locatie van deze open bare sleutel op met behulp van de opdracht [AZ VM Create](/cli/azure/vm#az-vm-create) met de `--ssh-key-values` optie. Vervang in de volgende opdracht *myVM*, *myResourceGroup*, *UbuntuLTS*, *azureuser* en *mysshkey. pub* door uw eigen waarden:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Als u meerdere SSH-sleutels met uw virtuele machine wilt gebruiken, kunt u ze in een door spaties gescheiden lijst invoeren `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` .


## <a name="ssh-into-your-vm"></a>SSH in uw virtuele machine

Met de open bare sleutel op uw virtuele Azure-machine en de persoonlijke sleutel op uw lokale systeem, SSH in uw virtuele machine met behulp van het IP-adres of de DNS-naam van uw virtuele machine. Vervang in de volgende opdracht *azureuser* en *myvm.westus.cloudapp.Azure.com* door de gebruikers naam van de beheerder en de Fully Qualified Domain Name (of het IP-adres):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u tijdens het maken van uw sleutel paar een wachtwoordzin hebt opgegeven, voert u deze wachtwoordzin in wanneer u hierom wordt gevraagd tijdens het aanmeldings proces. De virtuele machine wordt toegevoegd aan het bestand ~/.ssh/known_hosts en u wordt niet gevraagd om opnieuw verbinding te maken totdat de open bare sleutel op uw virtuele Azure-machine wordt gewijzigd of de server naam is verwijderd uit ~/.ssh/known_hosts.

Als de virtuele machine gebruikmaakt van het just-in-time-toegangs beleid, moet u toegang aanvragen voordat u verbinding kunt maken met de virtuele machine. Zie [toegang tot virtuele machines beheren met de just-in-time-beleids regels](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over het werken met SSH-sleutel paren de [gedetailleerde stappen voor het maken en beheren van SSH-sleutel paren](create-ssh-keys-detailed.md).

* Zie [problemen met ssh-verbindingen met een virtuele machine van Azure Linux oplossen](../troubleshooting/troubleshoot-ssh-connection.md)als u problemen ondervindt met ssh-verbindingen met virtuele Azure-machines.
