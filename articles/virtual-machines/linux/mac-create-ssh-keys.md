---
title: Een SSH-sleutelpaar maken en gebruiken voor linux-VM's in Azure
description: Een sleutelpaar met een openbare en persoonlijke SSH-sleutel maken en gebruiken voor virtuele Linux-VM's in Azure om de beveiliging van het verificatieproces te verbeteren.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c5e683e1f5af42a69fac45c20f52169834967649
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788129"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snelle stappen: Een sleutelpaar met een openbare en persoonlijke SSH-sleutel maken en gebruiken voor virtuele Linux-VM's in Azure

Met een SSH-sleutelpaar (Secure Shell) kunt u virtuele machines (VM's) in Azure maken die gebruikmaken van SSH-sleutels voor verificatie. In dit artikel wordt beschreven hoe u snel een bestandspaar met een openbare en persoonlijke SSH-sleutel kunt genereren en gebruiken voor Linux-VM's. U kunt deze stappen voltooien met de Azure Cloud Shell, een macOS- of Linux-host. 

> [!NOTE]
> VM's die zijn gemaakt met behulp van SSH-sleutels, worden standaard geconfigureerd met uitgeschakelde wachtwoorden, waardoor het moeilijker wordt om brute-force aanvallen te raden. 

Zie Gedetailleerde stappen voor het maken van [SSH-sleutelparen voor meer achtergrondinformatie en voorbeelden.](create-ssh-keys-detailed.md)

Zie SSH-sleutels gebruiken met Windows in Azure voor meer manieren om SSH-sleutels te genereren en te gebruiken [op een Windows-computer.](ssh-from-windows.md)

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Gebruik de opdracht `ssh-keygen` om openbare en privé-SSH-sleutelbestanden te genereren. Deze bestanden worden standaard gemaakt in de map ~/.ssh. U kunt een andere locatie en een optioneel wachtwoord *(wachtwoordzin)* opgeven voor toegang tot het bestand met de persoonlijke sleutel. Als er op de opgegeven locatie een SSH-sleutelpaar met dezelfde naam bestaat, worden deze bestanden overschreven.

Met de volgende opdracht maakt u een SSH-sleutelpaar met behulp van RSA-versleuteling en een bitlengte van 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Als u de [Azure CLI](/cli/azure) gebruikt om uw VM te maken met de opdracht [az vm create,](/cli/azure/vm#az_vm_create) kunt u eventueel openbare en persoonlijke SSH-sleutelbestanden genereren met behulp van de `--generate-ssh-keys` optie . De sleutelbestanden worden opgeslagen in de map ~/.ssh, tenzij anders aangegeven met de `--ssh-dest-key-path` optie . Als er al een ssh-sleutelpaar bestaat en de optie wordt gebruikt, wordt er geen nieuw sleutelpaar gegenereerd, maar wordt het bestaande  `--generate-ssh-keys` sleutelpaar gebruikt. Vervang in de volgende opdracht *VMname* en *RGname* door uw eigen waarden:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Geef een openbare SSH-sleutel op bij het implementeren van een VM

Als u een Linux-VM wilt maken die gebruikmaakt van SSH-sleutels voor verificatie, geeft u uw openbare SSH-sleutel op bij het maken van de VM met behulp van de Azure Portal, Azure CLI, Azure Resource Manager-sjablonen of andere methoden:

* [Een virtuele Linux-machine maken met Azure Portal](quick-create-portal.md)
* [Een virtuele Linux-machine maken met de Azure CLI](quick-create-cli.md)
* [Een virtuele Linux-machine maken met een Azure-sjabloon](create-ssh-secured-vm-from-template.md)

Als u niet bekend bent met de indeling van een openbare SSH-sleutel, kunt u uw openbare sleutel weergeven met de volgende opdracht, en zo nodig vervangen door het pad en de bestandsnaam van uw eigen `cat` `~/.ssh/id_rsa.pub` openbare-sleutelbestand:

```bash
cat ~/.ssh/id_rsa.pub
```

Een typische openbare-sleutelwaarde ziet eruit als in dit voorbeeld:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Als u de inhoud van het bestand met de openbare sleutel kopieert en plakt voor gebruik in de Azure Portal- of een Resource Manager-sjabloon, moet u ervoor zorgen dat u geen aaneengepaste witruimte kopieert. Als u een openbare sleutel in macOS wilt kopiëren, kunt u het bestand met de openbare sleutel doorseen naar `pbcopy` . Op dezelfde manier kunt u in Linux het bestand met de openbare sleutel doorseen naar programma's zoals `xclip` .

De openbare sleutel die u op uw Linux-VM in Azure opgeslagen, wordt standaard opgeslagen in ~/.ssh/id_rsa.pub, tenzij u een andere locatie hebt opgegeven bij het maken van het sleutelpaar. Als u de [Azure CLI 2.0](/cli/azure) wilt gebruiken om uw VM te maken met een bestaande openbare sleutel, geeft u de waarde en eventueel de locatie van deze openbare sleutel op met behulp van de [opdracht az vm create](/cli/azure/vm#az_vm_create) met de optie `--ssh-key-values` . Vervang in de volgende opdracht *myVM*, *myResourceGroup,* *UbuntuLTS,* *azureuser* en *mysshkey.pub* door uw eigen waarden:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Als u meerdere SSH-sleutels wilt gebruiken met uw VM, kunt u deze invoeren in een lijst met door spaties gescheiden sleutels, zoals deze `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` .


## <a name="ssh-into-your-vm"></a>SSH in uw virtuele machine

Nu de openbare sleutel is geïmplementeerd op uw Azure-VM en de persoonlijke sleutel op uw lokale systeem, gebruikt u het IP-adres of de DNS-naam van uw VM om via SSH in uw VM te gaan. Vervang in de volgende opdracht *azureuser* en *myvm.westus.cloudapp.azure.com* door de gebruikersnaam van de beheerder en de volledig gekwalificeerde domeinnaam (of het IP-adres):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u een wachtwoordzin hebt opgegeven tijdens het maken van het sleutelpaar, voert u die wachtwoordzin in wanneer u hier om wordt gevraagd tijdens het aanmeldingsproces. De VM wordt toegevoegd aan uw ~/.ssh/known_hosts-bestand en u wordt pas opnieuw gevraagd om verbinding te maken als de openbare sleutel op uw Azure-VM is gewijzigd of de servernaam is verwijderd uit ~/.ssh/known_hosts.

Als de VM gebruik maakt van het Just-In-Time-toegangsbeleid, moet u toegang aanvragen voordat u verbinding kunt maken met de VM. Zie Toegang tot virtuele machines beheren met het Just-In-Time-beleid voor meer informatie over [het Just-In-Time-beleid.](../../security-center/security-center-just-in-time.md)

## <a name="next-steps"></a>Volgende stappen

* Zie Gedetailleerde stappen voor het maken en beheren van SSH-sleutelparen voor meer informatie over het werken [met SSH-sleutelparen.](create-ssh-keys-detailed.md)

* Zie Troubleshoot SSH connections to an Azure Linux VM (Problemen met SSH-verbindingen met een virtuele Azure Linux-VM oplossen) als u problemen hebt met [SSH-verbindingen met virtuele Azure-VM's.](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection)