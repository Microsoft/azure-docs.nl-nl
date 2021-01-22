---
title: De inrichtings agent uitschakelen of verwijderen
description: Meer informatie over het uitschakelen of verwijderen van de inrichtings agent in Linux Vm's en installatie kopieën.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 0fea82c376a178de0be8ede6c0393e1de21de614
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675801"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>De Linux-agent uit Vm's en installatie kopieën uitschakelen of verwijderen

Voordat u de Linux-agent verwijdert, moet u weten welke VM niet kan worden uitgevoerd nadat de Linux-agent is verwijderd.

Azure virtual machine (VM)- [uitbrei dingen](../extensions/overview.md) zijn kleine toepassingen die configuratie en automatiserings taken na de implementatie bieden op Azure-vm's, uitbrei dingen worden geïnstalleerd en beheerd door het Azure-besturings vlak. Het is de taak van de [Azure Linux-agent](../extensions/agent-linux.md) om de platform extensie opdrachten te verwerken en te zorgen voor de juiste status van de uitbrei ding in de virtuele machine.

Het Azure-platform fungeert als host voor veel uitbrei dingen die variëren van VM-configuratie, bewaking, beveiliging en hulpprogram ma's. Er is een grote keuze aan uitbrei dingen van de eerste en derden, voor beelden van belang rijke scenario's waarin extensies worden gebruikt voor:
* Ondersteuning voor Azure-Services van de eerste partij, zoals Azure Backup, bewaking, schijf versleuteling, beveiliging, site replicatie en andere.
* SSH/wacht woord opnieuw instellen
* VM-configuratie: aangepaste scripts uitvoeren, chef, puppet-agents enzovoort installeren.
* Producten van derden, zoals AV-producten, VM-hulpprogram ma's voor beveiligings problemen, VM-en app-bewakings programma.
* Uitbrei dingen kunnen worden gebundeld met een nieuwe VM-implementatie. Ze kunnen bijvoorbeeld deel uitmaken van een grotere implementatie, het configureren van toepassingen op de VM-inrichting of het uitvoeren van een ondersteunde uitbrei ding die wordt gebruikt voor de implementatie van systemen.

## <a name="disabling-extension-processing"></a>Uitbrei ding verwerken uitschakelen

Er zijn verschillende manieren om de verwerking van uitbrei dingen uit te scha kelen, afhankelijk van uw behoeften, maar voordat u doorgaat, **moet** u alle uitbrei dingen verwijderen die zijn geïmplementeerd op de VM, bijvoorbeeld met behulp van Azure CLI, u kunt een [lijst](/cli/azure/vm/extension#az-vm-extension-list) maken en [verwijderen](/cli/azure/vm/extension#az-vm-extension-delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Als u dit niet doet, probeert het platform de extensie configuratie en time-out na 40min te verzenden.

### <a name="disable-at-the-control-plane"></a>Uitschakelen op het besturings vlak
Als u niet zeker weet of u uitbrei dingen in de toekomst nodig hebt, kunt u de Linux-agent op de virtuele machine installeren en vervolgens de extensie verwerkings mogelijkheden van het platform uitschakelen. Deze optie is beschikbaar in `Microsoft.Compute` `2018-06-01` de API-versie of hoger en heeft geen afhankelijkheid van de geïnstalleerde versie van de Linux-agent.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
U kunt de verwerkings uitbreiding eenvoudig opnieuw inschakelen vanaf het platform, met de bovenstaande opdracht, maar instellen op ' True '.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>De Linux-agent verwijderen van een actieve VM

Zorg ervoor dat u alle bestaande uitbrei dingen van de virtuele machine eerder hebt **verwijderd** , zoals hierboven is beschreven.

### <a name="step-1-remove-the-azure-linux-agent"></a>Stap 1: de Azure Linux-agent verwijderen

Als u de Linux-agent alleen verwijdert en niet de bijbehorende configuratie artefacten, kunt u de installatie op een later tijdstip opnieuw installeren. Voer een van de volgende handelingen uit als root om de Azure Linux-agent te verwijderen:

#### <a name="for-ubuntu-1804"></a>Voor Ubuntu >= 18,04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Voor redhat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Voor SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Stap 2: (optioneel) de Azure Linux-agent artefacten verwijderen
> [!IMPORTANT] 
>
> U kunt alle gekoppelde artefacten van de Linux-agent verwijderen, maar dit betekent dat u deze niet op een later tijdstip opnieuw moet installeren. Daarom is het raadzaam om de Linux-agent eerst uit te scha kelen, waarbij u de Linux-agent alleen verwijdert met behulp van de bovenstaande. 

Als u weet dat u de Linux-agent niet meer opnieuw moet installeren, kunt u de volgende handelingen uitvoeren:

#### <a name="for-ubuntu-1804"></a>Voor Ubuntu >= 18,04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Voor redhat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Voor SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Een installatie kopie voorbereiden zonder Linux-agent
Als u een installatie kopie hebt die al Cloud-init bevat en u de Linux-agent wilt verwijderen, maar nog steeds wilt inrichten met behulp van Cloud-init, voert u de stappen in stap 2 (en eventueel stap 3) uit als basis voor het verwijderen van de Azure Linux-agent. vervolgens worden de Cloud-init-configuratie en de gegevens in de cache verwijderd en wordt de virtuele machine

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Inrichting opheffen en een installatie kopie maken
De Linux-agent biedt de mogelijkheid om enkele van de bestaande meta gegevens van de installatie kopie op te schonen, met de stap ' waagent-deprovisioning + gebruiker ', maar nadat deze is verwijderd, moet u de volgende acties uitvoeren, zoals hieronder, en eventuele andere gevoelige gegevens verwijderen.

- Alle bestaande SSH-host-sleutels verwijderen

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Het beheerders account verwijderen

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Het hoofd wachtwoord verwijderen

   ```bash
   passwd -d root
   ```

Zodra u het bovenstaande hebt voltooid, kunt u de aangepaste installatie kopie maken met behulp van de Azure CLI.


**Een normale beheerde installatie kopie maken**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Een installatie kopie versie maken in een galerie met gedeelde afbeeldingen**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Een virtuele machine maken op basis van een installatie kopie die geen Linux-agent bevat
Wanneer u de virtuele machine maakt op basis van de installatie kopie zonder Linux-agent, moet u ervoor zorgen dat de VM-implementatie configuratie aangeeft dat extensies niet worden ondersteund op deze VM.

> [!NOTE] 
> 
> Als u dit niet doet, probeert het platform de extensie configuratie en time-out na 40min te verzenden.

Als u de VM wilt implementeren met uitbrei dingen die zijn uitgeschakeld, kunt u de Azure CLI gebruiken met [--Enable-agent](/cli/azure/vm#az-vm-create).

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

U kunt dit ook doen met behulp van de sjablonen voor Azure Resource Manager (ARM) door in te stellen `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Volgende stappen

Zie [Linux inrichten](provisioning.md)voor meer informatie.
