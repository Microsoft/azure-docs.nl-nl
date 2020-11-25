---
title: Gedetailleerde stappen voor het maken van een SSH-sleutel paar
description: Meer informatie over het maken en beheren van een open bare en persoonlijke SSH-sleutel paar voor virtuele Linux-machines in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 34a84ed333172ea0931c529d2dbeee1b774ae8c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016359"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Gedetailleerde stappen: SSH-sleutels voor verificatie voor een virtuele Linux-machine maken en beheren in azure

Met een SSH-sleutel paar (Secure Shell) kunt u een virtuele Linux-machine maken die gebruikmaakt van SSH-sleutels voor verificatie. In dit artikel leest u hoe u een open bare sleutel paar van een SSH RSA-bestand voor SSH-client verbindingen maakt en gebruikt.

Zie [het maken van een open bare SSH-sleutel paar voor virtuele Linux-machines in azure](mac-create-ssh-keys.md)als u snelle opdrachten wilt.

Zie [SSH-sleutels gebruiken met Windows op Azure voor informatie over het](ssh-from-windows.md)maken van SSH-sleutels en het gebruiken om een verbinding te maken met een van een **Windows** -computer. U kunt ook de [Azure Portal](../ssh-keys-portal.md) gebruiken voor het maken en beheren van SSH-sleutels voor het maken van virtuele machines in de portal.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Gebruik en voordelen van SSH-sleutels

Wanneer u een virtuele Azure-machine maakt door de open bare sleutel op te geven, kopieert Azure de open bare sleutel (in de `.pub` indeling) naar de `~/.ssh/authorized_keys` map op de virtuele machine. SSH-sleutels in `~/.ssh/authorized_keys` worden gebruikt om de client te laten overeenkomen met de bijbehorende persoonlijke sleutel op een SSH-verbinding. In een Azure Linux-VM die gebruikmaakt van SSH-sleutels voor verificatie, configureert Azure de SSHD-server zodanig dat de aanmelding met een wacht woord niet is toegestaan, alleen SSH-sleutels. Door een virtuele Azure-machine met SSH-sleutels te maken, kunt u de VM-implementatie beveiligen en zelf de configuratie stap na de implementatie van wacht woorden uitschakelen in het `sshd_config` bestand.

Als u geen SSH-sleutels wilt gebruiken, kunt u uw virtuele Linux-machine instellen om wachtwoord verificatie te gebruiken. Als uw virtuele machine niet beschikbaar is op internet, kan het gebruik van wacht woorden voldoende zijn. U moet echter nog steeds uw wacht woorden beheren voor elke Linux-VM en goed functioneren van wachtwoord beleid en-procedures behouden, zoals minimale wachtwoord lengte en regel matige updates. 

## <a name="generate-keys-with-ssh-keygen"></a>Sleutels genereren met ssh-keygen

Voor het maken van de sleutels is een voorkeurs opdracht `ssh-keygen` , die beschikbaar is met OpenSSH-hulpprogram ma's in de Azure Cloud shell, een macOS-of Linux-host en Windows 10. `ssh-keygen` vraagt een reeks vragen en schrijft een persoonlijke sleutel en een overeenkomende open bare sleutel. 

SSH-sleutels worden standaard opgeslagen in de `~/.ssh`-directory.  Als u niet beschikt over de map `~/.ssh`, wordt deze door de opdracht `ssh-keygen` voor u gemaakt met de juiste machtigingen.

### <a name="basic-example"></a>Basis voorbeeld

Met de volgende opdracht worden de `ssh-keygen` open bare en persoonlijke sleutel bestanden van de SSH RSA standaard 4096 gegenereerd in de `~/.ssh` Directory. Als er een SSH-sleutel paar op de huidige locatie bestaat, worden deze bestanden overschreven.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Gedetailleerd voor beeld
In het volgende voor beeld ziet u aanvullende opdracht Opties voor het maken van een SSH RSA-sleutel paar. Als er een SSH-sleutel paar op de huidige locatie bestaat, worden deze bestanden overschreven. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Uitleg van de opdracht**

`ssh-keygen` = het programma dat wordt gebruikt voor het maken van de sleutels

`-m PEM` = de sleutel opmaken als PEM

`-t rsa` = type sleutel dat moet worden gemaakt, in dit geval in de RSA-indeling

`-b 4096` = het aantal bits in de sleutel, in dit geval 4096

`-C "azureuser@myserver"` = een opmerking die wordt toegevoegd aan het einde van het openbare sleutelbestand om het gemakkelijk te identificeren. Normaal gesp roken wordt een e-mail adres gebruikt als opmerking, maar u kunt ook het beste gebruikmaken van uw infra structuur.

`-f ~/.ssh/mykeys/myprivatekey` = de bestands naam van het bestand met de persoonlijke sleutel als u ervoor kiest om de standaard naam niet te gebruiken. Een bijbehorende open bare-sleutel bestand dat `.pub` is toegevoegd aan, wordt gegenereerd in dezelfde map. De map moet bestaan.

`-N mypassphrase` = een extra wachtwoordzin die wordt gebruikt voor toegang tot het bestand met de persoonlijke sleutel. 

### <a name="example-of-ssh-keygen"></a>Voorbeeld van ssh-keygen

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Opgeslagen sleutel bestanden

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

De naam van het sleutelpaar voor dit artikel. Een sleutel paar met `id_rsa` de naam is de standaard waarde. sommige hulpprogram ma's verwachten mogelijk de `id_rsa` Bestands naam van de persoonlijke sleutel, zodat het een goed idee is. De map `~/.ssh/` is de standaardlocatie voor SSH-sleutelparen en het SSH-configuratiebestand. Als `ssh-keygen` niet wordt opgegeven met een volledig pad, worden de sleutels in de huidige werkmap gemaakt in plaats van in de standaardmap `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lijst van de `~/.ssh` map

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Sleutel wachtwoordzin

`Enter passphrase (empty for no passphrase):`

Het is *raadzaam* om een wachtwoordzin toe te voegen aan uw persoonlijke sleutel. Zonder een wachtwoordzin om het sleutel bestand te beveiligen, kan iedereen met het bestand deze gebruiken om zich aan te melden bij een server met de bijbehorende open bare sleutel. Het toevoegen van een wachtwoordzin biedt meer beveiliging in het geval iemand toegang kan krijgen tot uw persoonlijke sleutel bestand, zodat u tijd hebt om de sleutels te wijzigen.

## <a name="generate-keys-automatically-during-deployment"></a>Automatisch sleutels genereren tijdens implementatie

Als u de [Azure cli](/cli/azure) gebruikt om uw virtuele machine te maken, kunt u optioneel open bare en persoonlijke SSH-sleutel bestanden genereren door de opdracht [AZ VM Create](/cli/azure/vm) uit te voeren met de `--generate-ssh-keys` optie. De sleutels worden opgeslagen in de map ~/.ssh. Houd er rekening mee dat met deze opdracht optie geen sleutels worden overschreven als deze al op die locatie bestaan.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Open bare SSH-sleutel opgeven bij het implementeren van een virtuele machine

Als u een virtuele Linux-machine wilt maken die gebruikmaakt van SSH-sleutels voor verificatie, geeft u uw open bare SSH-sleutel op bij het maken van de virtuele machine met behulp van de Azure Portal, CLI, Resource Manager-sjablonen of andere methoden. Wanneer u de portal gebruikt, voert u de open bare sleutel zelf in. Als u de [Azure cli](/cli/azure) gebruikt om een virtuele machine te maken met een bestaande open bare sleutel, geeft u de waarde of locatie van deze open bare sleutel op door de opdracht [AZ VM Create](/cli/azure/vm) uit te voeren met de `--ssh-key-value` optie. 

Als u niet bekend bent met de indeling van een open bare SSH-sleutel, kunt u de open bare sleutel `cat` als volgt uitvoeren, vervangen door `~/.ssh/id_rsa.pub` de locatie van uw eigen open bare-sleutel bestand:

```bash
cat ~/.ssh/id_rsa.pub
```

De uitvoer ziet er ongeveer als volgt uit (dit is een redactie):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Als u de inhoud van het bestand met de open bare sleutel kopieert en plakt in het Azure Portal of een resource manager-sjabloon, moet u ervoor zorgen dat u geen extra witruimte kopieert of extra regel einden toevoegt. Als u bijvoorbeeld macOS gebruikt, kunt u het bestand met de open bare sleutel (standaard) door sluizen `~/.ssh/id_rsa.pub` naar **pbcopy** om de inhoud te kopiëren (er zijn andere Linux-Program ma's die hetzelfde doen, zoals `xclip` ).

Als u liever een open bare sleutel gebruikt die zich in een indeling met meerdere regels bevindt, kunt u een RFC4716-geformatteerde sleutel genereren in een PEM-container van de open bare sleutel die u eerder hebt gemaakt.

Een RFC4716-sleutel maken van een bestaande openbare SSH-sleutel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH naar uw virtuele machine met een SSH-client
Met de open bare sleutel die is geïmplementeerd op uw Azure-VM en de persoonlijke sleutel op uw lokale systeem, SSH naar uw virtuele machine met het IP-adres of de DNS-naam van uw virtuele machine. Vervang *azureuser* en *myvm.westus.cloudapp.Azure.com* in de volgende opdracht door de gebruikers naam van de beheerder en de Fully Qualified Domain Name (of het IP-adres):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Als u tijdens het maken van uw sleutel paar een wachtwoordzin hebt opgegeven, voert u de wachtwoordzin in wanneer u hierom wordt gevraagd tijdens het aanmeldings proces. (De server is toegevoegd aan uw map `~/.ssh/known_hosts` en u wordt pas weer gevraagd om verbinding te maken nadat de openbare sleutel op uw virtuele Azure-machine is gewijzigd of de naam van de server is verwijderd uit `~/.ssh/known_hosts`.)

Als de virtuele machine gebruikmaakt van het just-in-time-toegangs beleid, moet u toegang aanvragen voordat u verbinding kunt maken met de virtuele machine. Zie [toegang tot virtuele machines beheren met de just-in-time-beleids regels](../../security-center/security-center-just-in-time.md)voor meer informatie over het just-in-time-beleid.

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>SSH-agent gebruiken om de wachtwoordzin van uw persoonlijke sleutel op te slaan

Als u wilt voor komen dat uw persoonlijke sleutel wachtwoord wachtwoordzin met elke SSH-aanmelding, kunt u gebruiken `ssh-agent` om de wachtwoordzin van uw persoonlijke sleutel bestand in de cache op te slaan. Als u een Mac gebruikt, wordt de wachtwoordzin van de persoonlijke sleutel veilig opgeslagen door de macOS sleutel keten wanneer u aanroept `ssh-agent` .

Controleer en gebruik `ssh-agent` en `ssh-add` om het SSH-systeem te informeren over de sleutel bestanden, zodat u de wachtwoordzin niet interactief hoeft te gebruiken.

```bash
eval "$(ssh-agent -s)"
```

Voeg nu de persoonlijke sleutel toe aan `ssh-agent` met de opdracht `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

De wachtwoordzin van de persoonlijke sleutel wordt nu opgeslagen in `ssh-agent` .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Ssh-copy-id gebruiken om de sleutel te kopiëren naar een bestaande virtuele machine

Als u al een virtuele machine hebt gemaakt, kunt u een nieuwe open bare SSH-sleutel toevoegen aan uw virtuele Linux-machine met behulp van `ssh-copy-id` .

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Een SSH-configuratiebestand maken en configureren

U kunt een SSH-configuratie bestand maken en configureren ( `~/.ssh/config` ) om de aanmeldingen te versnellen en het gedrag van uw SSH-client te optimaliseren. 

In het volgende voor beeld ziet u een eenvoudige configuratie die u kunt gebruiken om snel aan te melden als een gebruiker voor een specifieke VM met behulp van de standaard persoonlijke SSH-sleutel. 

Maak het bestand.

```bash
touch ~/.ssh/config
```

Bewerk het bestand om de nieuwe SSH-configuratie toe te voegen

```bash
vim ~/.ssh/config
```

Voeg de vereiste configuratie-instellingen voor uw host-VM toe. In dit voor beeld is de naam van de virtuele machine *myvm* en is de account naam *azureuser*.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

U kunt configuraties voor extra hosts toevoegen om elk gebruik te kunnen maken van een eigen toegewezen sleutel paar. Zie [SSH-configuratie bestand](https://www.ssh.com/ssh/config/) voor meer geavanceerde configuratie opties.

Nu u een SSH-sleutel paar en een geconfigureerd SSH-configuratie bestand hebt, kunt u zich snel en veilig aanmelden bij uw virtuele Linux-machine. Wanneer u de volgende opdracht uitvoert, zoekt en laadt SSH alle instellingen van het `Host myvm` blok in het SSH-configuratie bestand.

```bash
ssh myvm
```

De eerste keer dat u zich aanmeldt bij een server met behulp van een SSH-sleutel, wordt u gevraagd om de wachtwoordzin voor dat sleutel bestand.

## <a name="next-steps"></a>Volgende stappen

De volgende stap bestaat uit het maken van virtuele Linux-machines in Azure met de nieuwe openbare SSH-sleutel. Virtuele Azure-machines die zijn gemaakt met een open bare SSH-sleutel, zijn beter beveiligd dan Vm's die zijn gemaakt met de standaard aanmeldings methode, wacht woorden.

* [Een virtuele Linux-machine maken met Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een virtuele Linux-machine maken met de Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Een virtuele Linux-machine maken met een Azure-sjabloon](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
