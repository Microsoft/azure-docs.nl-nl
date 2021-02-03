---
title: Inleiding tot FreeBSD op Azure
description: Meer informatie over het gebruik van virtuele FreeBSD-machines in azure
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: ced29c3222b7f4f5d7cf5f9e472c420c47341786
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493240"
---
# <a name="introduction-to-freebsd-on-azure"></a>Inleiding tot FreeBSD op Azure
Dit artikel bevat een overzicht van het uitvoeren van een FreeBSD-virtuele machine in Azure.

## <a name="overview"></a>Overzicht
FreeBSD voor Microsoft Azure is een geavanceerd computer besturingssysteem dat wordt gebruikt voor het inschakelen van moderne servers, Desk tops en Inge sloten platforms.

Micro soft Corporation maakt installatie kopieën van FreeBSD beschikbaar in azure met de vooraf geconfigureerde [Azure VM-gast agent](https://github.com/Azure/WALinuxAgent/) . Momenteel worden de volgende FreeBSD-versies aangeboden als installatie kopieën van micro soft:

- FreeBSD 10,4 op Azure Marketplace
- FreeBSD 11,2 op Azure Marketplace
- FreeBSD 12,0 op Azure Marketplace

De agent is verantwoordelijk voor de communicatie tussen de FreeBSD-VM en de Azure-infra structuur voor bewerkingen, zoals het inrichten van de virtuele machine bij het eerste gebruik (gebruikers naam, wacht woord of SSH-sleutel, hostnaam enzovoort) en het inschakelen van functionaliteit voor selectieve VM-extensies.

Net als voor toekomstige versies van FreeBSD is de strategie actueel en zijn de meest recente releases beschikbaar zodra ze zijn gepubliceerd door het team van de FreeBSD-versie techniek.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Een FreeBSD-VM maken via Azure CLI op FreeBSD
Eerst moet u [Azure cli](/cli/azure/get-started-with-azure-cli) installeren, met de volgende opdracht op een FreeBSD-machine.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Als bash niet is geïnstalleerd op uw FreeBSD-computer, voert u de volgende opdracht uit vóór de installatie. 

```bash
sudo pkg install bash
```

Als python niet is geïnstalleerd op uw FreeBSD-computer, voert u de volgende opdrachten uit vóór de installatie. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Tijdens de installatie wordt u gevraagd `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` . Als u antwoordt `y` en `/etc/rc.conf` invoert `a path to an rc file to update` , kunt u aan het probleem voldoen `ERROR: [Errno 13] Permission denied` . U kunt dit probleem oplossen door het schrijf recht toe te kennen aan de huidige gebruiker op het bestand `etc/rc.conf` .

Nu kunt u zich aanmelden bij Azure en uw FreeBSD-VM maken. Hieronder ziet u een voor beeld van het maken van een FreeBSD 11,0-VM. U kunt ook de para meter toevoegen `--public-ip-address-dns-name` met een wereld wijd unieke DNS-naam voor een nieuw, gemaakt openbaar IP-adres. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vervolgens kunt u zich aanmelden bij uw FreeBSD-VM via het IP-adres dat is afgedrukt in de uitvoer van de bovenstaande implementatie. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-extensies voor FreeBSD
Hieronder vindt u ondersteunde VM-extensies in FreeBSD.

### <a name="vmaccess"></a>VMAccess
De [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) -extensie kan:

* Stel het wacht woord van de oorspronkelijke sudo-gebruiker opnieuw in.
* Maak een nieuwe sudo-gebruiker met het opgegeven wacht woord.
* Stel de sleutel van de open bare host in met de opgegeven sleutel.
* De open bare host-sleutel die tijdens de inrichting van de virtuele machine is verstrekt, opnieuw instellen als de host-sleutel niet is verstrekt.
* Open de SSH-poort (22) en herstel de sshd_config als reset_ssh is ingesteld op True.
* Verwijder de bestaande gebruiker.
* Schijven controleren.
* Een toegevoegde schijf herstellen.

### <a name="customscript"></a>CustomScript
De [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) -extensie kan:

* Down load indien aanwezig de aangepaste scripts van Azure Storage of externe open bare opslag (bijvoorbeeld GitHub).
* Voer het script voor het toegangs punt uit.
* Inline-opdrachten ondersteunen.
* Windows-stijl nieuwe regel in shell-en python-scripts automatisch converteren.
* Verwijder automatisch een stuk lijst in shell-en python-scripts.
* Beveilig gevoelige gegevens in CommandToExecute.

> [!NOTE]
> FreeBSD VM ondersteunt nu alleen CustomScript versie 1. x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Verificatie: gebruikers namen, wacht woorden en SSH-sleutels
Wanneer u een virtuele FreeBSD-machine maakt met behulp van de Azure Portal, moet u een gebruikers naam, wacht woord of open bare SSH-sleutel opgeven.
Gebruikers namen voor het implementeren van een virtuele FreeBSD-machine in azure mogen niet overeenkomen met de namen van systeem accounts (UID <100) die al aanwezig zijn in de virtuele machine (bijvoorbeeld ' root ').
Op dit moment wordt alleen de RSA SSH-sleutel ondersteund. Een SSH-sleutel met meerdere regels moet beginnen met `---- BEGIN SSH2 PUBLIC KEY ----` en eindigen op `---- END SSH2 PUBLIC KEY ----` .

## <a name="obtaining-superuser-privileges"></a>Bevoegdheden van super gebruiker verkrijgen
Het gebruikers account dat is opgegeven tijdens de implementatie van de virtuele machine-instantie op Azure is een bevoegd account. Het pakket met sudo is geïnstalleerd in de gepubliceerde FreeBSD-installatie kopie.
Nadat u bent aangemeld via dit gebruikers account, kunt u opdrachten uitvoeren als root met behulp van de opdracht syntaxis.

```
$ sudo <COMMAND>
```

U kunt eventueel een basis shell verkrijgen met behulp van `sudo -s` .

## <a name="known-issues"></a>Bekende problemen
De [Azure VM Guest agent](https://github.com/Azure/WALinuxAgent/) versie 2.2.2 heeft een [bekend probleem](https://github.com/Azure/WALinuxAgent/pull/517) dat ertoe leidt dat de inrichtings fout voor FreeBSD vm in azure wordt veroorzaakt. De oplossing is vastgelegd door de [Azure VM Guest agent](https://github.com/Azure/WALinuxAgent/) versie 2.2.3 en latere versies. 

## <a name="next-steps"></a>Volgende stappen
* Ga naar [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/thefreebsdfoundation.freebsd-12_2?tab=Overview) om een FreeBSD-VM te maken.
