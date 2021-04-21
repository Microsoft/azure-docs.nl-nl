---
title: xrdp gebruiken met Linux
description: Informatie over het installeren en configureren van Extern bureaublad (xrdp) om verbinding te maken met een Linux-VM in Azure met behulp van grafische hulpprogramma's
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 309b106d2141c8257c5163efe7ff45a7bae5d5c3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759647"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Xrdp installeren en configureren voor het gebruik van Extern bureaublad met Ubuntu

Virtuele Linux-machines (VM's) in Azure worden meestal beheerd vanaf de opdrachtregel met behulp van een SSH-verbinding (Secure Shell). Wanneer Linux nieuw is of voor snelle probleemoplossingsscenario's, kan het gebruik van extern bureaublad eenvoudiger zijn. In dit artikel wordt beschreven hoe u een bureaubladomgeving[(xfce)](https://www.xfce.org)en extern bureaublad[(xrdp)](http://xrdp.org)installeert en configureert voor uw Linux-VM waarop Ubuntu wordt uitgevoerd.

Het artikel is geschreven en getest met behulp van een Ubuntu 18.04-VM. 

## <a name="prerequisites"></a>Vereisten

Voor dit artikel is een bestaande Ubuntu 18.04 LTS-VM in Azure vereist. Als u een VM moet maken, gebruikt u een van de volgende methoden:

- De [Azure CLI](quick-create-cli.md)
- De [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Een bureaubladomgeving installeren op uw Linux-VM

Voor de meeste linux-VM's in Azure is standaard geen desktopomgeving geïnstalleerd. Linux-VM's worden meestal beheerd met behulp van SSH-verbindingen in plaats van een desktopomgeving. Er zijn verschillende bureaubladomgevingen in Linux die u kunt kiezen. Afhankelijk van uw keuze van de desktopomgeving kan het één tot 2 GB aan schijfruimte verbruiken en duurt het 5 tot 10 minuten om alle vereiste pakketten te installeren en te configureren.

In het volgende voorbeeld wordt de lichtgewicht [xfce4-desktopomgeving](https://www.xfce.org/) geïnstalleerd op een Ubuntu 18.04 LTS-VM. Opdrachten voor andere distributies variëren enigszins (gebruik om te installeren op Red Hat Enterprise Linux en de juiste regels te configureren, of te gebruiken om bijvoorbeeld te installeren in `yum` `selinux` `zypper` SUSE).

Eerst SSH naar uw VM. In het volgende voorbeeld wordt verbinding met de virtuele myvm.westus.cloudapp.azure.com *met* de gebruikersnaam *azureuser*. Gebruik uw eigen waarden:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u Windows gebruikt en meer informatie nodig hebt over het gebruik van SSH, zie [SSH-sleutels gebruiken met Windows.](ssh-from-windows.md)

Installeer vervolgens xfce met `apt` behulp van als volgt:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Een extern bureaublad-server installeren en configureren
Nu u een bureaubladomgeving hebt geïnstalleerd, configureert u een extern bureaublad-service om te luisteren naar binnenkomende verbindingen. [xrdp](http://xrdp.org) is een open source Remote Desktop Protocol (RDP)-server die beschikbaar is in de meeste Linux-distributies en goed werkt met xfce. Installeer xrdp als volgt op uw Ubuntu-VM:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Vertel xrdp welke bureaubladomgeving u moet gebruiken wanneer u de sessie start. Configureer xrdp als volgt om xfce te gebruiken als uw bureaubladomgeving:

```bash
echo xfce4-session >~/.xsession
```

Start de xrdp-service opnieuw op om de wijzigingen als volgt van kracht te laten worden:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Een wachtwoord voor een lokaal gebruikersaccount instellen
Als u een wachtwoord voor uw gebruikersaccount hebt gemaakt tijdens het maken van de VM, slaat u deze stap over. Als u alleen SSH-sleutelverificatie gebruikt en geen wachtwoord voor een lokaal account hebt ingesteld, geeft u een wachtwoord op voordat u xrdp gebruikt om u aan te melden bij uw VM. xrdp kan geen SSH-sleutels accepteren voor verificatie. In het volgende voorbeeld wordt een wachtwoord opgegeven voor het gebruikersaccount *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Als u een wachtwoord opgeeft, wordt uw SSHD-configuratie niet bijgewerkt om wachtwoordmelding toe te staan als dit momenteel niet het mogelijk is. Vanuit het oogpunt van beveiliging wilt u mogelijk verbinding maken met uw VM via een SSH-tunnel met behulp van verificatie op basis van sleutels en vervolgens verbinding maken met xrdp. Als dit het volgende is, slaat u de volgende stap over bij het maken van een netwerkbeveiligingsgroepsregel om extern bureaublad-verkeer toe te staan.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Een netwerkbeveiligingsgroepsregel maken voor Extern bureaublad verkeer
Als u Extern bureaublad linux-VM wilt laten bereiken, moet er een netwerkbeveiligingsgroepsregel worden gemaakt waarmee TCP op poort 3389 uw VM kan bereiken. Zie Wat is een netwerkbeveiligingsgroep? voor meer informatie over regels [voor netwerkbeveiligingsgroep.](../../virtual-network/network-security-groups-overview.md) U kunt ook [de Azure Portal om een netwerkbeveiligingsgroepsregel te maken.](../windows/nsg-quickstart-portal.md)

In het volgende voorbeeld wordt een netwerkbeveiligingsgroepsregel [gemaakt met az vm open-port](/cli/azure/vm#az_vm_open_port) op poort *3389.* Open vanuit de Azure CLI, niet de SSH-sessie naar uw VM, de volgende netwerkbeveiligingsgroepsregel:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Uw Linux-VM verbinden met een Extern bureaublad client

Open uw lokale extern bureaublad-client en maak verbinding met het IP-adres of de DNS-naam van uw Linux-VM. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Schermopname van de extern bureaublad-client.":::

Voer als volgt de gebruikersnaam en het wachtwoord voor het gebruikersaccount op uw VM in:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Schermopname van het xrdp-aanmeldscherm.":::

Na de authenticatie wordt de xfce-desktopomgeving geladen en ziet deze er ongeveer uit als in het volgende voorbeeld:

![xfce-desktopomgeving via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Als uw lokale RDP-client gebruikmaakt van NLA (Network Level Authentication), moet u die verbindingsinstelling mogelijk uitschakelen. XRDP biedt momenteel geen ondersteuning voor NLA. U kunt ook alternatieve RDP-oplossingen bekijken die ondersteuning bieden voor NLA, zoals [FreeRDP.](https://www.freerdp.com)


## <a name="troubleshoot"></a>Problemen oplossen
Als u geen verbinding kunt maken met uw Linux-VM met behulp van een Extern bureaublad-client, gebruikt u op uw Linux-VM om te controleren of uw VM als volgt naar `netstat` RDP-verbindingen luistert:

```bash
sudo netstat -plnt | grep rdp
```

In het volgende voorbeeld ziet u dat de VM luistert op TCP-poort 3389 zoals verwacht:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Als de *xrdp-sesman-service* niet luistert, start u de service op een Ubuntu-VM als volgt opnieuw op:

```bash
sudo service xrdp restart
```

Bekijk de logboeken in */var/log* op uw Ubuntu-VM voor informatie over waarom de service mogelijk niet reageert. U kunt de syslog ook controleren tijdens een poging om verbinding te maken met een extern bureaublad om eventuele fouten weer te geven:

```bash
tail -f /var/log/syslog
```

Andere Linux-distributies, zoals Red Hat Enterprise Linux en SUSE, kunnen verschillende manieren hebben om services en alternatieve logboekbestandslocaties opnieuw op te starten om te controleren.

Als u geen reactie ontvangt in uw extern bureaublad-client en geen gebeurtenissen in het systeemlogboek ziet, geeft dit gedrag aan dat extern bureaublad-verkeer de VM niet kan bereiken. Controleer de regels van uw netwerkbeveiligingsgroep om ervoor te zorgen dat u een regel hebt om TCP toe te staan op poort 3389. Zie Problemen met toepassingsconnectiviteit [oplossen voor meer informatie.](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection)


## <a name="next-steps"></a>Volgende stappen
Zie SSH-sleutels maken voor linux-VM's in Azure voor meer informatie over het maken en gebruiken van [SSH-sleutels met Virtuele Linux-VM's.](mac-create-ssh-keys.md)

Zie SSH-sleutels gebruiken met Windows voor meer informatie over het gebruik [van SSH vanuit Windows.](ssh-from-windows.md)