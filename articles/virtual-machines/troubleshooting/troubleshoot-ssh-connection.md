---
title: Problemen met SSH-verbindingen met een Azure VM oplossen | Microsoft Docs
description: Het oplossen van problemen zoals ' SSH-verbinding is mislukt ' of ' SSH-verbinding geweigerd ' voor een Azure-VM met Linux.
keywords: SSH-verbinding geweigerd, SSH-fout, Azure SSH, SSH-verbinding is mislukt
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 63c1e388ecd53d9b827e45a1fa78bdb6feeaab21
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201940"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Het oplossen van problemen met SSH-verbindingen naar een virtuele Azure Linux-machine waarop zich fouten voordoen, die afsluit vanwege fouten of die wordt geweigerd
Dit artikel helpt u bij het vinden en corrigeren van de problemen die zich voordoen als gevolg van SSH-fouten (Secure Shell), SSH-verbindings fouten of SSH worden geweigerd wanneer u verbinding probeert te maken met een virtuele Linux-machine (VM). U kunt de Azure Portal-, Azure CLI-of VM-extensie voor toegang voor Linux gebruiken om verbindings problemen op te lossen


Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="quick-troubleshooting-steps"></a>Snelle probleemoplossings stappen
Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met de virtuele machine.

1. [Stel de SSH-configuratie opnieuw](#reset-the-ssh-configuration)in.
2. [De referenties](#reset-ssh-credentials-for-a-user) voor de gebruiker opnieuw instellen.
3. Controleer de regels voor de [netwerk beveiligings groep](../../virtual-network/network-security-groups-overview.md) om SSH-verkeer toe te staan.
   * Zorg ervoor dat er een regel voor de [netwerk beveiligings groep](#check-security-rules) bestaat om SSH-verkeer toe te staan (standaard TCP-poort 22).
   * U kunt geen poort omleiding/toewijzing gebruiken zonder een Azure-load balancer te gebruiken.
4. Controleer de [status](../../service-health/resource-health-overview.md)van de VM-resource.
   * Zorg ervoor dat de VM-rapporten in orde zijn.
   * Als u [Diagnostische gegevens over opstarten hebt ingeschakeld](boot-diagnostics.md), controleert u of de virtuele machine geen opstart fouten in de logboeken meldt.
5. [Start de VM opnieuw](#restart-a-vm)op.
6. [Implementeer de virtuele machine](#redeploy-a-vm)opnieuw.

Blijf lezen voor meer gedetailleerde stappen voor probleem oplossing en uitleg.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Beschik bare methoden voor het oplossen van problemen met SSH-verbindingen
U kunt de referenties of de SSH-configuratie opnieuw instellen met een van de volgende methoden:

* [Azure Portal](#use-the-azure-portal) -geweldig als u de SSH-configuratie of SSH-sleutel snel opnieuw moet instellen en u de Azure-hulpprogram ma's niet hebt geïnstalleerd.
* [Seriële console van Azure VM](./serial-console-linux.md) : de seriële VM-console werkt onafhankelijk van de SSH-configuratie en geeft u een interactieve console voor uw VM. In het geval van ' niet SSH '-situaties is het specifiek dat de seriële console is ontworpen om te helpen oplossen. Zie hieronder voor meer informatie.
* [Azure cli](#use-the-azure-cli) : als u zich al op de opdracht regel bevindt, stelt u de SSH-configuratie of-referenties snel opnieuw in. Als u werkt met een klassieke virtuele machine, kunt u de [klassieke Azure-cli](#use-the-azure-classic-cli)gebruiken.
* [Azure VMAccessForLinux-extensie](#use-the-vmaccess-extension) : Maak en hergebruik JSON-definitie bestanden om de SSH-configuratie of gebruikers referenties opnieuw in te stellen.

Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw VM. Als u nog steeds geen verbinding kunt maken, voert u de volgende stap uit.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken
De Azure Portal biedt een snelle manier om de SSH-configuratie of gebruikers referenties opnieuw in te stellen zonder dat er hulpprogram ma's op de lokale computer hoeven te worden geïnstalleerd.

Selecteer uw virtuele machine in de Azure Portal om te beginnen. Schuif omlaag naar de sectie **ondersteuning en probleem oplossing** en selecteer **wacht woord opnieuw instellen** , zoals in het volgende voor beeld:

![De SSH-configuratie of referenties opnieuw instellen in de Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>De SSH-configuratie opnieuw instellen
Als u de SSH-configuratie opnieuw wilt instellen, selecteert u `Reset configuration only` in de sectie **modus** zoals in de vorige scherm afbeelding en selecteert u vervolgens **bijwerken**. Zodra deze actie is voltooid, probeert u opnieuw toegang te krijgen tot uw VM.

### <a name="reset-ssh-credentials-for-a-user"></a>SSH-referenties voor een gebruiker opnieuw instellen
Als u de referenties van een bestaande gebruiker opnieuw wilt instellen, selecteert u `Reset SSH public key` of `Reset password` in de sectie **modus** , zoals in de vorige scherm afbeelding. Geef de gebruikers naam en een SSH-sleutel of nieuw wacht woord op en selecteer vervolgens  **bijwerken**.

Vanuit dit menu kunt u ook een gebruiker met sudo-bevoegdheden op de VM maken. Geef een nieuwe gebruikers naam en een bijbehorend wacht woord of SSH-sleutel op en selecteer vervolgens **bijwerken**.

### <a name="check-security-rules"></a>Beveiligings regels controleren

Gebruik [IP-stroom controleren](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) om te controleren of het verkeer van of naar een virtuele machine wordt geblokkeerd door een regel in een netwerk beveiligings groep. U kunt ook de juiste regels voor beveiligings groepen controleren om ervoor te zorgen dat inkomende NSG regel bestaat en de prioriteit van de SSH-poort (standaard 22) wordt weer gegeven. Zie [using effectief security rules to Troubleshooting VM Traffic Flow](../../virtual-network/diagnose-network-traffic-filter-problem.md)voor meer informatie.

### <a name="check-routing"></a>Route ring controleren

Gebruik de [volgende hop](../../network-watcher/diagnose-vm-network-routing-problem.md) -mogelijkheid van Network Watcher om te controleren of een route niet voor komt dat verkeer wordt gerouteerd naar of van een virtuele machine. U kunt ook efficiënte routes bekijken om alle efficiënte routes voor een netwerk interface weer te geven. Zie [het gebruik van efficiënte routes voor het oplossen van problemen met de VM-verkeers stroom](../../virtual-network/diagnose-network-routing-problem.md)voor meer informatie.

## <a name="use-the-azure-vm-serial-console"></a>De seriële Azure VM-console gebruiken
De [seriële console van Azure VM](./serial-console-linux.md) biedt toegang tot een op tekst gebaseerde console voor virtuele Linux-machines. U kunt de-console gebruiken om uw SSH-verbinding in een interactieve shell op te lossen. Zorg ervoor dat u voldoet aan de [vereisten](./serial-console-linux.md#prerequisites) voor het gebruik van seriële console en voer de onderstaande opdrachten uit om uw SSH-connectiviteit verder te verhelpen.

### <a name="check-that-ssh-is-running"></a>Controleren of SSH wordt uitgevoerd
U kunt de volgende opdracht gebruiken om te controleren of SSH op uw virtuele machine wordt uitgevoerd:

```console
ps -aux | grep ssh
```

Als er een uitvoer is, is SSH actief.

### <a name="check-which-port-ssh-is-running-on"></a>Controleren op welke poort-SSH wordt uitgevoerd

U kunt de volgende opdracht gebruiken om te controleren op welke poort SSH wordt uitgevoerd:

```console
sudo grep Port /etc/ssh/sshd_config
```

De uitvoer ziet er ongeveer als volgt uit:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken
Als u dat nog niet hebt gedaan, installeert u de nieuwste [Azure cli](/cli/azure/install-az-cli2) en meldt u zich aan bij een Azure-account met behulp van [AZ login](/cli/azure/reference-index).

Als u een aangepaste Linux-schijf kopie hebt gemaakt en geüpload, controleert u of de [Microsoft Azure Linux-agent](../extensions/agent-linux.md) versie 2.0.5 of hoger is geïnstalleerd. Voor Vm's die zijn gemaakt met behulp van Galerie kopieën, is deze toegangs extensie al geïnstalleerd en geconfigureerd voor u.

### <a name="reset-ssh-configuration"></a>SSH-configuratie opnieuw instellen
U kunt in eerste instantie de SSH-configuratie opnieuw instellen op de standaard waarden en de SSH-server opnieuw opstarten op de VM. Hiermee worden de naam, het wacht woord of de SSH-sleutels van het gebruikers account niet gewijzigd.
In het volgende voor beeld wordt [AZ VM User reset-SSH](/cli/azure/vm/user) gebruikt om de SSH-configuratie opnieuw in te stellen op de virtuele machine met de naam `myVM` in `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>SSH-referenties voor een gebruiker opnieuw instellen
In het volgende voor beeld wordt [AZ VM user update](/cli/azure/vm/user) gebruikt om de referenties opnieuw in te stellen op de `myUsername` waarde die is opgegeven in `myPassword` , op de virtuele machine met de naam `myVM` `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Als u SSH-sleutel verificatie gebruikt, kunt u de SSH-sleutel voor een bepaalde gebruiker opnieuw instellen. In het volgende voor beeld wordt **AZ VM Access set-Linux-User** gebruikt voor het bijwerken van de SSH-sleutel die is opgeslagen in `~/.ssh/id_rsa.pub` voor de gebruiker met de naam `myUsername` , op de VM met de naam `myVM` in `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>De VMAccess-extensie gebruiken
De VM-toegangs uitbreiding voor Linux leest in een JSON-bestand waarin de acties worden gedefinieerd die moeten worden uitgevoerd. Deze acties omvatten het opnieuw instellen van SSHD, het opnieuw instellen van een SSH-sleutel of het toevoegen van een gebruiker. U gebruikt de Azure CLI nog steeds om de VMAccess-extensie aan te roepen, maar u kunt desgewenst de json-bestanden op meerdere Vm's opnieuw gebruiken. Met deze aanpak kunt u een opslag plaats maken van json-bestanden die vervolgens kunnen worden aangeroepen voor bepaalde scenario's.

### <a name="reset-sshd"></a>SSHD opnieuw instellen
Maak een bestand `settings.json` met de naam met de volgende inhoud:

```json
{
    "reset_ssh":True
}
```

Met de Azure CLI kunt u de `VMAccessForLinux` uitbrei ding aanroepen om de SSHD-verbinding opnieuw in te stellen door het JSON-bestand op te geven. In het volgende voor beeld wordt [AZ VM extension set ingesteld](/cli/azure/vm/extension) op het opnieuw instellen van SSHD op de virtuele machine met de naam `myVM` in `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>SSH-referenties voor een gebruiker opnieuw instellen
Als SSHD lijkt goed te werken, kunt u de referenties voor een gebruiker van de gebruikers teller opnieuw instellen. Als u het wacht woord voor een gebruiker opnieuw wilt instellen, maakt u een bestand met de naam `settings.json` . In het volgende voor beeld worden de referenties opnieuw ingesteld `myUsername` op de waarde die is opgegeven in `myPassword` . Voer de volgende regels in het `settings.json` bestand in met uw eigen waarden:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Of om de SSH-sleutel voor een gebruiker opnieuw in te stellen, maakt u eerst een bestand met de naam `settings.json` . In het volgende voor beeld worden de referenties opnieuw ingesteld op `myUsername` de waarde die is opgegeven in `myPassword` , op de virtuele machine met de naam `myVM` in `myResourceGroup` . Voer de volgende regels in het `settings.json` bestand in met uw eigen waarden:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Nadat u het JSON-bestand hebt gemaakt, gebruikt u de Azure CLI om de `VMAccessForLinux` extensie aan te roepen om uw SSH-gebruikers referenties opnieuw in te stellen door het JSON-bestand op te geven. In het volgende voor beeld worden de referenties opnieuw ingesteld op de virtuele machine met de naam `myVM` in `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>De klassieke Azure-CLI gebruiken
Als u dat nog niet hebt [gedaan, installeert u de klassieke Azure-CLI en maakt u verbinding met uw Azure-abonnement](/cli/azure/install-classic-cli). Zorg ervoor dat u de Resource Manager-modus als volgt gebruikt:

```azurecli
azure config mode arm
```

Als u een aangepaste Linux-schijf kopie hebt gemaakt en geüpload, controleert u of de [Microsoft Azure Linux-agent](../extensions/agent-linux.md) versie 2.0.5 of hoger is geïnstalleerd. Voor Vm's die zijn gemaakt met behulp van Galerie kopieën, is deze toegangs extensie al geïnstalleerd en geconfigureerd voor u.

### <a name="reset-ssh-configuration"></a>SSH-configuratie opnieuw instellen
De SSHD-configuratie zelf is mogelijk onjuist geconfigureerd of er is een fout opgetreden in de service. U kunt SSHD opnieuw instellen om er zeker van te zijn dat de SSH-configuratie zelf geldig is. Het opnieuw instellen van SSHD moet de eerste stap voor het oplossen van problemen zijn.

In het volgende voor beeld wordt SSHD opnieuw ingesteld op een virtuele machine `myVM` met de naam in de resource groep met de naam `myResourceGroup` . Gebruik uw eigen naam voor de VM en de resource groep als volgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>SSH-referenties voor een gebruiker opnieuw instellen
Als SSHD lijkt te werken, kunt u het wacht woord opnieuw instellen voor een gebruiker van de gebruikers naam. In het volgende voor beeld worden de referenties opnieuw ingesteld op `myUsername` de waarde die is opgegeven in `myPassword` , op de virtuele machine met de naam `myVM` in `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Als u SSH-sleutel verificatie gebruikt, kunt u de SSH-sleutel voor een bepaalde gebruiker opnieuw instellen. In het volgende voor beeld wordt de SSH-sleutel die is opgeslagen in `~/.ssh/id_rsa.pub` voor de gebruiker met de naam `myUsername` , bijgewerkt op de virtuele machine met de naam `myVM` in `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a>Een VM opnieuw opstarten
Als u de SSH-configuratie en gebruikers referenties opnieuw hebt ingesteld, of als er een fout is opgetreden, kunt u proberen de virtuele machine opnieuw te starten om onderliggende computer problemen op te lossen.

### <a name="azure-portal"></a>Azure Portal
Als u een virtuele machine opnieuw wilt opstarten met behulp van de Azure Portal, selecteert u uw virtuele machine en selecteert u **opnieuw opstarten** , zoals in het volgende voor beeld:

![Een virtuele machine opnieuw opstarten in de Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
In het volgende voor beeld wordt [AZ VM restart opnieuw](/cli/azure/vm) gestart voor het opnieuw opstarten van de VM met `myVM` de naam in de resource groep met de naam `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

In het volgende voor beeld wordt de virtuele machine `myVM` met de naam in de resource groep met de naam opnieuw gestart `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a>Een virtuele machine opnieuw implementeren
U kunt een virtuele machine opnieuw implementeren naar een ander knoop punt in azure, waardoor onderliggende netwerk problemen kunnen worden verholpen. Zie voor meer informatie over het opnieuw implementeren van een VM de [virtuele machine opnieuw implementeren naar het nieuwe Azure-knoop punt](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

> [!NOTE]
> Nadat deze bewerking is voltooid, gaan tijdelijke schijf gegevens verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele machine, bijgewerkt.
>
>

### <a name="azure-portal"></a>Azure Portal
Als u een virtuele machine opnieuw wilt implementeren met behulp van de Azure Portal, selecteert u uw virtuele machine en schuift u omlaag naar de sectie **ondersteuning en probleem oplossing** . Selecteer opnieuw **implementeren** , zoals in het volgende voor beeld:

![Een virtuele machine opnieuw implementeren in de Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
In het volgende voor beeld wordt [AZ VM redeploy](/cli/azure/vm) gebruikt voor het opnieuw implementeren van de VM met `myVM` de naam in de resource groep met de naam `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

In het volgende voor beeld wordt de virtuele machine `myVM` met de naam in de resource groep met de naam opnieuw geïmplementeerd `myResourceGroup` . Gebruik uw eigen waarden als volgt:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Vm's die zijn gemaakt met behulp van het klassieke implementatie model

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Voer de volgende stappen uit om de meest voorkomende SSH-verbindings fouten op te lossen voor virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model. Probeer na elke stap opnieuw verbinding te maken met de virtuele machine.

* Externe toegang opnieuw instellen via de [Azure Portal](https://portal.azure.com). Selecteer de virtuele machine op de Azure Portal en selecteer vervolgens **extern opnieuw instellen...**.
* Start de VM opnieuw op. Selecteer de virtuele machine op de [Azure Portal](https://portal.azure.com)en selecteer **opnieuw opstarten**.

* Implementeer de virtuele machine opnieuw in een nieuw Azure-knoop punt. Zie voor meer informatie over het opnieuw implementeren van een VM de [virtuele machine opnieuw implementeren naar het nieuwe Azure-knoop punt](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

    Nadat deze bewerking is voltooid, gaan tijdelijke schijf gegevens verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele machine, bijgewerkt.
* Volg de instructies in het [opnieuw instellen van een wacht woord of SSH voor op Linux gebaseerde virtuele machines](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) naar:

  * Stel het wacht woord of de SSH-sleutel opnieuw in.
  * Maak een *sudo* -gebruikers account.
  * Stel de SSH-configuratie opnieuw in.
* Controleer de resource status van de VM voor problemen met het platform.<br>
     Selecteer de VM en blader door de **instellingen**  >  **status controleren**.

## <a name="additional-resources"></a>Aanvullende resources
* Als u nog steeds niet kunt overstappen op uw virtuele machine na de volgende stappen, raadpleegt u [meer gedetailleerde stappen voor probleem oplossing](detailed-troubleshoot-ssh-connection.md) om uw probleem op te lossen.
* Zie [problemen oplossen met toegang tot een toepassing die wordt uitgevoerd op een virtuele machine van Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json) voor meer informatie over het oplossen van toepassings toegang
* Voor meer informatie over het oplossen van problemen met virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model raadpleegt [u een wacht woord of ssh opnieuw instellen voor op Linux gebaseerde virtuele machines](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic).