---
title: Toegang tot een virtuele machine van Azure Linux opnieuw instellen
description: Gebruikers met beheerders rechten beheren en de toegang tot de virtuele Linux-machines opnieuw instellen met behulp van de VMAccess-extensie en de Azure CLI
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 05/10/2018
ms.openlocfilehash: e0e63762fc151d93cb34885c04dd4d03285c29f1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557043"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Beheer gebruikers, SSH-en herstel schijven op Linux-Vm's met de VMAccess-extensie met de Azure CLI
## <a name="overview"></a>Overzicht
Op de schijf op de virtuele Linux-machine worden fouten weer gegeven. U stelt het Hoofdwacht woord voor uw virtuele Linux-machine opnieuw in of u hebt per ongeluk uw persoonlijke SSH-sleutel verwijderd. Als dat is gebeurd in de dagen van het Data Center, moet u daar een station maken en vervolgens de KVM openen om op de server console te komen. U kunt de Azure VMAccess-extensie beschouwen als de KVM-switch waarmee u toegang hebt tot de console om de toegang tot Linux opnieuw in te stellen of door onderhoud op schijf niveau uit te voeren.

In dit artikel wordt beschreven hoe u de Azure VMAccess-extensie gebruikt om een schijf te controleren of te herstellen, gebruikers toegang opnieuw in te stellen, accounts voor gebruikers met beheerders rechten te beheren of de SSH-configuratie op Linux bij te werken wanneer deze als Azure Resource Manager virtuele machines worden uitgevoerd. Als u klassieke virtuele machines wilt beheren, kunt u de instructies volgen die in de [klassieke VM-documentatie](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic)worden gevonden. 
 
> [!NOTE]
> Als u de VMAccess-extensie gebruikt om het wacht woord van uw virtuele machine opnieuw in te stellen na de installatie van de AAD-aanmeldings extensie, moet u de AAD-aanmeldings extensie opnieuw uitvoeren om AAD-aanmelding voor uw computer opnieuw in te scha kelen.

## <a name="prerequisites"></a>Vereisten
### <a name="operating-system"></a>Besturingssysteem

De extensie voor VM-toegang kan worden uitgevoerd voor deze Linux-distributies:

| Distributie | Versie |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS en 12,04 LTS |
| Debian | Debian 7,9 +, 8.2 + |
| Red Hat | RHEL 6,7 +, 7.1 + |
| Oracle Linux | 6.4 +, 7.0 + |
| SuSE | 11 en 12 |
| OpenSuse | openSUSE Schrikkel 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0 + |

## <a name="ways-to-use-the-vmaccess-extension"></a>Manieren om de VMAccess-extensie te gebruiken
Er zijn twee manieren waarop u de VMAccess-extensie kunt gebruiken op uw virtuele Linux-machines:

* Gebruik de Azure CLI en de vereiste para meters.
* [Gebruik onbewerkte json-bestanden die de VMAccess-extensie verwerken](#use-json-files-and-the-vmaccess-extension) en klik vervolgens op.

De volgende voor beelden gebruiken [AZ VM User](/cli/azure/vm/user) commands. Als u deze stappen wilt uitvoeren, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>SSH-sleutel bijwerken
In het volgende voor beeld wordt de SSH-sleutel voor de gebruiker `azureuser` op de VM bijgewerkt met de naam `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Opmerking:** De `az vm user update` opdracht voegt de nieuwe open bare-sleutel tekst toe aan het `~/.ssh/authorized_keys` bestand voor de gebruiker met beheerders rechten op de VM. Hiermee worden geen bestaande SSH-sleutels vervangen of verwijderd. Hiermee verwijdert u geen eerdere sleutels die zijn ingesteld tijdens de implementatie tijd of volgende updates via de VMAccess-extensie.

## <a name="reset-password"></a>Wachtwoord opnieuw instellen
In het volgende voor beeld wordt het wacht woord voor de gebruiker opnieuw ingesteld `azureuser` op de VM met de naam `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH opnieuw starten
In het volgende voor beeld wordt de SSH-daemon opnieuw gestart en wordt de SSH-configuratie opnieuw ingesteld op de standaard waarden voor een virtuele machine met de naam `myVM` :

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Een gebruiker met beheerdersrechten/sudo-gebruiker maken
In het volgende voor beeld wordt een gebruiker gemaakt met de naam `myNewUser` **sudo** -machtigingen. Het account gebruikt een SSH-sleutel voor verificatie op de virtuele machine met de naam `myVM` . Deze methode is ontworpen om u te helpen weer toegang te krijgen tot een virtuele machine in het geval dat de huidige referenties verloren zijn gegaan of worden verg eten. Als best practice worden accounts met **sudo** -machtigingen beperkt.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Een gebruiker verwijderen
In het volgende voor beeld wordt een gebruiker `myNewUser` met de naam op de VM verwijderd met de naam `myVM` :

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON-bestanden en de VMAccess-extensie gebruiken
In de volgende voor beelden worden onbewerkte JSON-bestanden gebruikt. Gebruik [AZ VM extension set](/cli/azure/vm/extension) om de json-bestanden aan te roepen. Deze JSON-bestanden kunnen ook worden aangeroepen vanuit Azure-sjablonen. 

### <a name="reset-user-access"></a>Gebruikers toegang opnieuw instellen
Als u de toegang tot de hoofdmap op uw Linux-VM verloren hebt, kunt u een VMAccess-script starten om de SSH-sleutel of het wacht woord van een gebruiker bij te werken.

Als u de open bare SSH-sleutel van een gebruiker wilt bijwerken, maakt u een bestand met `update_ssh_key.json` de naam en voegt u de instellingen in de volgende indeling toe. Vervang uw eigen waarden voor de `username` `ssh_key` para meters en:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Als u een gebruikers wachtwoord opnieuw wilt instellen, maakt u een bestand met `reset_user_password.json` de naam en voegt u de instellingen in de volgende indeling toe. Vervang uw eigen waarden voor de `username` `password` para meters en:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>SSH opnieuw starten
Als u de SSH-daemon opnieuw wilt opstarten en de SSH-configuratie opnieuw wilt instellen op de standaard waarden, maakt u een bestand met de naam `reset_sshd.json` . Voeg de volgende inhoud toe:

```json
{
  "reset_ssh": true
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Gebruikers met beheerders rechten beheren

Als u een gebruiker wilt maken met **sudo** -machtigingen die een SSH-sleutel voor verificatie gebruiken, maakt u een bestand met `create_new_user.json` de naam en voegt u de instellingen in de volgende indeling toe. Vervang uw eigen waarden door de `username` `ssh_key` para meters en. Deze methode is ontworpen om u te helpen weer toegang te krijgen tot een virtuele machine in het geval dat de huidige referenties verloren zijn gegaan of worden verg eten. Als best practice worden accounts met **sudo** -machtigingen beperkt.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Als u een gebruiker wilt verwijderen, maakt u een bestand met `delete_user.json` de naam en voegt u de volgende inhoud toe. Vervang uw eigen waarde door de `remove_user` para meter:

```json
{
  "remove_user":"myNewUser"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>De schijf controleren of herstellen
Met VMAccess kunt u ook een schijf controleren en herstellen die u hebt toegevoegd aan de virtuele Linux-machine.

Als u de schijf wilt controleren en vervolgens wilt herstellen, maakt u een bestand met `disk_check_repair.json` de naam en voegt u de instellingen in de volgende indeling toe. Vervang uw eigen waarde door de volgende naam `repair_disk` :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de Azure CLI. Als u de implementatie status van uitbrei dingen voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
