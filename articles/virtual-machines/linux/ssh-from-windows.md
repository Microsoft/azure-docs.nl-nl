---
title: SSH-sleutels gebruiken om verbinding te maken met virtuele Linux-machines
description: Meer informatie over het genereren en gebruiken van SSH-sleutels van een Windows-computer om verbinding te maken met een virtuele Linux-machine in Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: aeecdcddaeba385c6dc0773356bcd0e36231e1e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607463"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-sleutels gebruiken met Windows op Azure

Dit artikel is van toepassing op Windows-gebruikers die SSH-sleutels ( *Secure Shell* ) willen [maken](#create-an-ssh-key-pair) en gebruiken voor [verbinding](#connect-to-your-vm) met virtuele Linux-machines (vm's) in Azure. U kunt ook [SSH-sleutels genereren en opslaan in de Azure Portal](../ssh-keys-portal.md) die u kunt gebruiken bij het maken van virtuele machines in de portal.


Als u SSH-sleutels wilt gebruiken vanuit een Linux-of macOS-client, raadpleegt u de [snelle stappen](mac-create-ssh-keys.md). Zie voor een meer gedetailleerd overzicht van SSH [gedetailleerde stappen: SSH-sleutels voor verificatie voor een Linux-vm in azure maken en beheren](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Overzicht van SSH en sleutels

[SSH](https://www.ssh.com/ssh/) is een versleuteld verbindings protocol waarmee beveiligde aanmeldingen via niet-beveiligde verbindingen worden toegestaan. SSH is het standaard verbindings protocol voor Linux-Vm's die worden gehost in Azure. Hoewel SSH zelf een versleutelde verbinding biedt, heeft het gebruik van wacht woorden met SSH de virtuele machine nog steeds kwetsbaar voor aanvallen met een zwakke kracht. Het is raadzaam om via SSH verbinding te maken met een virtuele machine met behulp van een openbaar-persoonlijk sleutel paar, ook wel *SSH-sleutels* genoemd. 

Het paar open bare persoonlijke sleutels is net als de vergren deling van uw voor deur. De vergren deling wordt aan het **publiek** blootgesteld, iedereen met de juiste sleutel de deur kan openen. De sleutel is **privé** en alleen verleend aan personen die u vertrouwt omdat deze kunnen worden gebruikt om de deur te ontgrendelen. 

- Wanneer u de virtuele machine maakt, wordt de *open bare sleutel* op uw virtuele Linux-machine geplaatst. 

- De *persoonlijke sleutel* blijft op het lokale systeem. Houd deze privésleutel geheim. Deel deze niet.

Wanneer u verbinding maakt met uw virtuele Linux-machine, test de VM de SSH-client om te controleren of deze de juiste persoonlijke sleutel heeft. Als de client de persoonlijke sleutel heeft, wordt de toegang tot de virtuele machine verleend. 

Afhankelijk van het beveiligings beleid van uw organisatie, kunt u één sleutel paar gebruiken om toegang te krijgen tot meerdere Azure-Vm's en-services. U hebt geen afzonderlijk paar sleutels voor elke virtuele machine nodig. 

Uw open bare sleutel kan worden gedeeld met iedereen, maar alleen u (of uw lokale beveiligings infrastructuur) moet toegang hebben tot uw persoonlijke sleutel.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH-clients

Recente versies van Windows 10 bevatten [openssh-client opdrachten](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) voor het maken en gebruiken van SSH-sleutels en het maken van ssh-verbindingen vanuit Power shell of een opdracht prompt. Dit is de eenvoudigste manier om een SSH-verbinding met uw virtuele Linux-machine te maken vanaf een Windows-computer. 

U kunt bash ook gebruiken in de [Azure Cloud shell](../../cloud-shell/overview.md) om verbinding te maken met uw VM. U kunt Cloud Shell gebruiken in een [webbrowser](https://shell.azure.com/bash), vanuit de [Azure Portal](https://portal.azure.com)of als een terminal in Visual Studio code met behulp van de [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

U kunt ook het [Windows-subsysteem voor Linux](/windows/wsl/about) installeren om via SSH verbinding te maken met uw virtuele machine en andere systeem eigen Linux-hulpprogram ma's gebruiken in een bash-shell.

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Maak een SSH-sleutel paar met behulp van de `ssh-keygen` opdracht. Voer een bestands naam in of gebruik de standaard waarde tussen haakjes (bijvoorbeeld `C:\Users\username/.ssh/id_rsa` ).  Voer een wachtwoordzin in voor het bestand of laat de wachtwoordzin leeg als u geen wachtwoordzin wilt gebruiken. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Een virtuele machine maken met behulp van uw sleutel

Als u een virtuele Linux-machine wilt maken die gebruikmaakt van SSH-sleutels voor verificatie, geeft u uw open bare SSH-sleutel op bij het maken van de VM.

Met de Azure CLI geeft u het pad en de bestands naam op voor de open bare sleutel met behulp van `az vm create` en de `--ssh-key-value` para meter.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Met Power shell gebruikt `New-AzVM` en voegt u de SSH-sleutel toe aan de VM-configuratie met behulp van. Zie [Quick Start: een virtuele Linux-machine maken in azure met Power shell](quick-create-powershell.md)voor een voor beeld.

Als u een groot aantal implementaties hebt met behulp van de portal, kunt u uw open bare sleutel uploaden naar Azure, waar u deze eenvoudig kan selecteren bij het maken van een virtuele machine vanuit de portal. Zie [een SSH-sleutel uploaden](../ssh-keys-portal.md#upload-an-ssh-key)voor meer informatie.


## <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Met de open bare sleutel die is geïmplementeerd op uw Azure-VM en de persoonlijke sleutel op uw lokale systeem, SSH naar uw virtuele machine met het IP-adres of de DNS-naam van uw virtuele machine. Vervang *azureuser* en *10.111.12.123* in de volgende opdracht door de gebruikers naam van de beheerder, het IP-adres (of de Fully Qualified Domain Name) en het pad naar uw persoonlijke sleutel:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Als u tijdens het maken van uw sleutel paar een wachtwoordzin hebt geconfigureerd, voert u de wachtwoordzin in wanneer u hierom wordt gevraagd.

Als de virtuele machine gebruikmaakt van het just-in-time-toegangs beleid, moet u toegang aanvragen voordat u verbinding kunt maken met de virtuele machine. Zie [toegang tot virtuele machines beheren met de just-in-time-beleids regels](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.


## <a name="next-steps"></a>Volgende stappen

- Zie [SSH-sleutels in de Azure Portal genereren en opslaan](../ssh-keys-portal.md) om te gebruiken bij het maken van virtuele machines in de portal voor meer informatie over SSH-sleutels in de Azure Portal.

- Zie voor gedetailleerde stappen, opties en geavanceerde voor beelden van het werken met SSH-sleutels [gedetailleerde stappen voor het maken van SSH-sleutel paren](create-ssh-keys-detailed.md).

- U kunt Power shell ook in Azure Cloud Shell gebruiken om SSH-sleutels te genereren en SSH-verbindingen te maken met virtuele Linux-machines. Zie de [Power shell-Snelstartgids](../../cloud-shell/quickstart-powershell.md#ssh).

- Zie [problemen met ssh-verbindingen met een virtuele machine van Azure Linux oplossen](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)als u problemen hebt met het gebruik van SSH om verbinding te maken met uw virtuele Linux-machines.