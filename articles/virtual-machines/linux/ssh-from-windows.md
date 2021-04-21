---
title: SSH-sleutels gebruiken om verbinding te maken met linux-VM's
description: Meer informatie over het genereren en gebruiken van SSH-sleutels van een Windows-computer om verbinding te maken met een virtuele Linux-machine in Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 0199a47b2306d7d461ba61057c7ab1015015df08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835556"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-sleutels gebruiken met Windows in Azure

Dit artikel is bedoeld voor [](#create-an-ssh-key-pair) Windows-gebruikers die SSH-sleutels [](#connect-to-your-vm) *(Secure Shell)* willen maken en gebruiken om verbinding te maken met virtuele Linux-machines (VM's) in Azure. U kunt ook [SSH-sleutels genereren en opslaan in](../ssh-keys-portal.md) de Azure Portal te gebruiken bij het maken van VM's in de portal.


Zie de snelle stappen als u SSH-sleutels wilt gebruiken vanuit een Linux- of [macOS-client.](mac-create-ssh-keys.md) Zie Gedetailleerde stappen: SSH-sleutels maken en beheren voor verificatie bij een [linux-VM in Azure](create-ssh-keys-detailed.md)voor een gedetailleerder overzicht van SSH.

## <a name="overview-of-ssh-and-keys"></a>Overzicht van SSH en sleutels

[SSH](https://www.ssh.com/ssh/) is een versleuteld verbindingsprotocol waarmee beveiligde aanmeldingen via niet-beveiligde verbindingen mogelijk zijn. SSH is het standaardverbindingsprotocol voor linux-VM's die worden gehost in Azure. Hoewel SSH zelf een versleutelde verbinding biedt, maakt het gebruik van wachtwoorden met SSH de VM nog steeds kwetsbaar voor brute-force-aanvallen. Het is raadzaam om via SSH verbinding te maken met een VM met behulp van een openbaar-persoonlijk sleutelpaar, ook wel bekend als *SSH-sleutels.* 

Het openbare-persoonlijke sleutelpaar is hetzelfde als de vergrendeling op uw front door. De vergrendeling wordt blootgesteld aan **het openbare**, iedereen met de juiste sleutel kan de deur openen. De sleutel is **persoonlijk** en wordt alleen gegeven aan personen die u vertrouwt, omdat deze kan worden gebruikt om de deur te ontgrendelen. 

- De *openbare sleutel wordt* op uw Linux-VM geplaatst wanneer u de VM maakt. 

- De *persoonlijke sleutel blijft* op uw lokale systeem. Houd deze privésleutel geheim. Deel deze niet.

Wanneer u verbinding maakt met uw Linux-VM, test de VM de SSH-client om er zeker van te zijn dat deze de juiste persoonlijke sleutel heeft. Als de client de persoonlijke sleutel heeft, krijgt deze toegang tot de VM. 

Afhankelijk van het beveiligingsbeleid van uw organisatie kunt u één sleutelpaar opnieuw gebruiken voor toegang tot meerdere virtuele Azure-VM's en -services. U hebt geen afzonderlijk paar sleutels nodig voor elke VM. 

Uw openbare sleutel kan met iedereen worden gedeeld, maar alleen u (of uw lokale beveiligingsinfrastructuur) moet toegang hebben tot uw persoonlijke sleutel.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH-clients

Recente versies van Windows 10 zijn [OpenSSH-clientopdrachten](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) voor het maken en gebruiken van SSH-sleutels en het maken van SSH-verbindingen vanuit PowerShell of een opdrachtprompt. Dit is de eenvoudigste manier om vanaf een Windows-computer een SSH-verbinding te maken met uw Linux-VM. 

U kunt bash ook gebruiken in de [Azure Cloud Shell](../../cloud-shell/overview.md) verbinding te maken met uw VM. U kunt deze Cloud Shell in een [webbrowser](https://shell.azure.com/bash), vanuit de [Azure Portal](https://portal.azure.com)of als een terminal in Visual Studio Code met behulp van de [Azure-accountextensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

U kunt ook de Windows-subsysteem voor Linux [verbinding](/windows/wsl/about) te maken met uw VM via SSH en andere systeemeigen Linux-hulpprogramma's in een Bash-shell te gebruiken.

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Maak een SSH-sleutelpaar met behulp van de `ssh-keygen` opdracht . Voer een bestandsnaam in of gebruik de standaardwaarde tussen haakjes (bijvoorbeeld `C:\Users\username/.ssh/id_rsa` ).  Voer een wachtwoordzin voor het bestand in of laat de wachtwoordzin leeg als u geen wachtwoordzin wilt gebruiken. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Een VM maken met behulp van uw sleutel

Als u een linux-VM wilt maken die gebruikmaakt van SSH-sleutels voor verificatie, geeft u uw openbare SSH-sleutel op bij het maken van de VM.

Met de Azure CLI geeft u het pad en de bestandsnaam voor de openbare sleutel op met en `az vm create` de `--ssh-key-value` parameter .

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Gebruik met PowerShell de `New-AzVM` SSH-sleutel en voeg deze toe aan de VM-configuratie met . Zie [Quickstart: Create a Linux virtual machine in Azure with PowerShell (Snelstart: Een virtuele Linux-machine maken in Azure met PowerShell) voor een voorbeeld.](quick-create-powershell.md)

Als u veel implementaties via de portal wilt uitvoeren, kunt u uw openbare sleutel uploaden naar Azure, waar deze eenvoudig kan worden geselecteerd bij het maken van een VM vanuit de portal. Zie Upload [an SSH key (Een SSH-sleutel uploaden) voor meer informatie.](../ssh-keys-portal.md#upload-an-ssh-key)


## <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Nu de openbare sleutel is geïmplementeerd op uw Azure-VM en de persoonlijke sleutel op uw lokale systeem, gebruikt u het IP-adres of de DNS-naam van uw VM om via SSH naar uw VM te gaan. Vervang *azureuser* en *10.111.12.123* in de volgende opdracht door de gebruikersnaam van de beheerder, het IP-adres (of de volledig gekwalificeerde domeinnaam) en het pad naar uw persoonlijke sleutel:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Als u een wachtwoordzin hebt geconfigureerd toen u uw sleutelpaar maakte, voert u de wachtwoordzin in wanneer u hier om wordt gevraagd.

Als de VM gebruik maakt van het Just-In-Time-toegangsbeleid, moet u toegang aanvragen voordat u verbinding kunt maken met de VM. Zie Toegang tot virtuele machines beheren met het Just-In-Time-beleid voor meer informatie over [het Just-In-Time-beleid.](../../security-center/security-center-just-in-time.md)


## <a name="next-steps"></a>Volgende stappen

- Zie SSH-sleutels genereren en opslaan in de Azure Portal voor gebruik bij het maken van VM's in de portal voor meer informatie over [SSH-sleutels](../ssh-keys-portal.md) in de Azure Portal.

- Zie Gedetailleerde stappen voor het maken van SSH-sleutelparen voor gedetailleerde stappen, opties en geavanceerde voorbeelden van het werken met [SSH-sleutels.](create-ssh-keys-detailed.md)

- U kunt PowerShell ook gebruiken in Azure Cloud Shell SSH-sleutels te genereren en SSH-verbindingen te maken met linux-VM's. Zie de [Snelstart voor PowerShell.](../../cloud-shell/quickstart-powershell.md#ssh)

- Als u problemen hebt met het gebruik van SSH om verbinding te maken met uw Linux-VM's, zie [Troubleshoot SSH connections to an Azure Linux VM (Problemen met SSH-verbindingen met een Virtuele Linux-azure-VM oplossen).](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
