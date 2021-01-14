---
title: Extern bureaublad gebruiken voor een virtuele Linux-machine in azure
description: Meer informatie over het installeren en configureren van Extern bureaublad (xrdp) om verbinding te maken met een virtuele Linux-machine in azure met grafische hulpprogram ma's
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: bea7e38c35ceddafb64937d6e1a6f69d7c727f44
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196381"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in azure
Virtuele Linux-machines (Vm's) in Azure worden meestal beheerd vanaf de opdracht regel met behulp van een SSH-verbinding (Secure Shell). Wanneer u geen ervaring hebt met Linux of voor snelle probleemoplossings scenario's, is het gebruik van extern bureau blad mogelijk eenvoudiger. In dit artikel wordt beschreven hoe u een desktop-omgeving ([xfce](https://www.xfce.org)) en extern bureau blad ([xrdp](http://xrdp.org)) installeert en configureert voor uw virtuele Linux-machine met behulp van het Resource Manager-implementatie model.


## <a name="prerequisites"></a>Vereisten
Voor dit artikel is een bestaande Ubuntu 18,04 LTS-VM vereist in Azure. Als u een virtuele machine wilt maken, gebruikt u een van de volgende methoden:

- De [Azure cli](quick-create-cli.md)
- De [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Een desktop omgeving installeren op uw virtuele Linux-machine
Voor de meeste Linux-Vm's in Azure is geen bureaublad omgeving standaard geïnstalleerd. Virtuele Linux-machines worden vaak beheerd met SSH-verbindingen in plaats van een desktop omgeving. Er zijn verschillende desktop omgevingen in Linux die u kunt kiezen. Afhankelijk van uw keuze van de bureaublad omgeving, kan het één tot 2 GB aan schijf ruimte verbruiken en vijf tot tien minuten duren om alle vereiste pakketten te installeren en te configureren.

In het volgende voor beeld wordt de licht gewicht [xfce4](https://www.xfce.org/) Desktop Environment geïnstalleerd op een Ubuntu 18,04 LTS-VM. Opdrachten voor andere distributies variëren enigszins per `yum` Red Hat Enterprise Linux, configureren van de juiste `selinux` regels of gebruiken `zypper` om te installeren op Suse, bijvoorbeeld).

Eerst SSH naar uw VM. In het volgende voor beeld wordt verbinding gemaakt met de virtuele machine met de naam *myvm.westus.cloudapp.Azure.com* met de gebruikers naam van *azureuser*. Gebruik uw eigen waarden:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Zie [SSH-sleutels gebruiken met Windows](ssh-from-windows.md)als u Windows gebruikt en meer informatie wilt over het gebruik van SSH.

Installeer vervolgens xfce met behulp van de volgende `apt` opties:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Een extern bureau blad-server installeren en configureren
Nu u een bureaublad omgeving hebt geïnstalleerd, configureert u een extern bureau blad-service om te Luis teren naar binnenkomende verbindingen. [xrdp](http://xrdp.org) is een open source Remote Desktop Protocol (RDP)-server die beschikbaar is in de meeste Linux-distributies en goed werkt met xfce. Installeer xrdp op uw Ubuntu-VM als volgt:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Vertel xrdp welke bureaublad omgeving moet worden gebruikt wanneer u uw sessie start. Configureer xrdp als volgt om xfce als uw bureaublad omgeving te gebruiken:

```bash
echo xfce4-session >~/.xsession
```

Start de xrdp-service opnieuw om de wijzigingen van kracht te laten worden door de volgende:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Een wacht woord voor een lokale gebruikers account instellen
Als u een wacht woord hebt gemaakt voor uw gebruikers account tijdens het maken van de VM, slaat u deze stap over. Als u alleen SSH-sleutel verificatie gebruikt en geen wacht woord voor een lokale account hebt ingesteld, geeft u een wacht woord op voordat u xrdp gebruikt om u aan te melden bij uw VM. xrdp kan geen SSH-sleutels voor verificatie accepteren. In het volgende voor beeld wordt een wacht woord voor het gebruikers account *azureuser* opgegeven:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Als u een wacht woord opgeeft, wordt uw SSHD-configuratie niet bijgewerkt om wachtwoord aanmeldingen mogelijk te maken als dat niet het geval is. Vanuit het oogpunt van beveiliging kunt u verbinding maken met uw virtuele machine via een SSH-tunnel met behulp van verificatie op basis van een sleutel en vervolgens verbinding maken met xrdp. Als dit het geval is, slaat u de volgende stap over voor het maken van een netwerk beveiligings groep regel om extern bureau blad-verkeer toe te staan.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Een regel voor de netwerk beveiligings groep voor Extern bureaublad verkeer maken
Als u wilt toestaan dat Extern bureaublad verkeer uw virtuele Linux-machine bereikt, moet er een regel voor de netwerk beveiligings groep worden gemaakt waarmee TCP op poort 3389 uw virtuele machine kan bereiken. Zie [Wat is een netwerk beveiligings groep?](../../virtual-network/network-security-groups-overview.md) voor meer informatie over regels voor netwerk beveiligings groepen. U kunt ook [de Azure Portal gebruiken om een regel voor een netwerk beveiligings groep te maken](../windows/nsg-quickstart-portal.md).

In het volgende voor beeld wordt een regel voor een netwerk beveiligings groep gemaakt met [AZ VM Open-Port](/cli/azure/vm#az-vm-open-port) op poort *3389*. Open vanuit de Azure CLI, niet de SSH-sessie naar uw virtuele machine, de volgende regel voor de netwerk beveiligings groep:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Uw virtuele Linux-machine verbinden met een Extern bureaublad-client
Open uw lokale extern bureau blad-client en maak verbinding met het IP-adres of de DNS-naam van uw virtuele Linux-machine. Voer de gebruikers naam en het wacht woord voor het gebruikers account op uw virtuele machine als volgt in:

![Verbinding maken met xrdp met behulp van uw Extern bureaublad-client](./media/use-remote-desktop/remote-desktop-client.png)

Nadat de verificatie is gelukt, wordt de xfce-desktop omgeving geladen en ziet deze er ongeveer uit zoals in het volgende voor beeld:

![xfce desktop environment via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Als uw lokale RDP-client gebruikmaakt van verificatie op netwerk niveau (NLA), moet u deze verbindings instelling mogelijk uitschakelen. XRDP biedt momenteel geen ondersteuning voor NLA. U kunt ook zoeken naar alternatieve RDP-oplossingen die NLA ondersteunen, zoals [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Problemen oplossen
Als u geen verbinding kunt maken met uw virtuele Linux-machine met behulp van een Extern bureaublad-client, gebruikt u `netstat` de virtuele Linux-machine om te controleren of uw virtuele machine op de volgende wijze naar RDP-verbindingen luistert:

```bash
sudo netstat -plnt | grep rdp
```

In het volgende voor beeld ziet u dat de VM naar behoren luistert op TCP-poort 3389:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Als de *xrdp-sesman-* service niet luistert, start u op een Ubuntu-VM de service als volgt opnieuw op:

```bash
sudo service xrdp restart
```

Bekijk de logboeken in */var/log* op uw Ubuntu-VM voor aanwijzingen over waarom de service mogelijk niet reageert. U kunt ook de syslog controleren tijdens een verbinding met een extern bureau blad om fouten te bekijken:

```bash
tail -f /var/log/syslog
```

Andere Linux-distributies zoals Red Hat Enterprise Linux en SUSE kunnen verschillende manieren hebben om services en andere locaties van het logboek bestand om te controleren.

Als er geen antwoord wordt ontvangen in uw extern bureau blad-client en er geen gebeurtenissen in het systeem logboek worden weer gegeven, betekent dit gedrag dat extern bureau blad-verkeer de virtuele machine niet kan bereiken. Controleer de regels voor de netwerk beveiligings groep om ervoor te zorgen dat u een regel hebt voor het toestaan van TCP op poort 3389. Zie problemen [met toepassings connectiviteit oplossen](../troubleshooting/troubleshoot-app-connection.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen
Zie [SSH-sleutels voor Linux-Vm's maken in azure](mac-create-ssh-keys.md)voor meer informatie over het maken en gebruiken van SSH-sleutels met virtuele Linux-machines.

Zie [SSH-sleutels gebruiken met Windows](ssh-from-windows.md)voor meer informatie over het gebruik van SSH vanuit Windows.